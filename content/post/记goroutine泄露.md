---
title: "记goroutine泄露"
date: 2019-12-23T14:47:34+08:00
draft: false
author: "WenpiCai"
tags: ["开发", "Go"]
showFullContent: false
---

### 程序说明

监听 TCP 与 UDP 服务，TCP 服务保持客户端长连接，UDP 服务由业务端调用发消息到客户端。

### 发现问题

通过监控系统发现 goroutine 数量居高不下，正常情况下是一个用户一个 goroutine 才是，明显出现了异常情况。

### 定位问题

这种情况应该是次 goroutine 泄露现象，需要知道哪个位置导致的。

初步猜测 TCP 连接断开重连导致，翻看日志，看到存在很多高频率重连，只是不符合导致暴涨现象。

回头直接看 goroutine 占用情况，方式 https://caiwp.github.io/post/golang利用pprof定位问题/ 

原因也就一目了然，是 UDP 服务发全服消息时调用的 goroutine 无法执行结束。

上代码

```go
// Handle 执行
func (t *UdpTransport) Handle(ctx context.Context, addr net.Addr, reader *Reader) {
	ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
	defer cancel()

	ch := make(chan struct{}, 0)
	go func() {
		t.run(ctx, addr, reader)
		ch <- struct{}{} // ERROR 会堵在这
	}()

	select {
	case <-ch:
	case <-ctx.Done():
		logger.Warn(fmt.Sprintf("done with error %s", ctx.Err().Error())) // 大量日志出现
		return
	}
}
```

在发全服消息时，t.run() 会找出所有客户端然后推送消息，耗时是不可控的，而这里面预先设定了5秒明显是不足够的。所以就会执行到 select 中的 ctx.Done()，Handle 就结束了，但 t.run() 在后续结束后就会 ch <- struct{}{} 就阻塞了。

### 解决问题

参考了一下 https://stackoverflow.com/questions/24096026/in-go-what-happens-if-you-write-to-closed-channel-can-i-treat-channels-as-deter  

```
If you write to a closed channel, your program will panic (see http://play.golang.org/p/KU7MLrFQSx for example). You could potentially catch this error with recover, but being in a situation where you don't know whether the channel you are writing to is open is usually a sign of a bug in the program. The send side of the channel is responsible for closing it, so it should know the current state. If you have multiple goroutines sending on the channel, then they should coordinate in closing the channel (e.g. by using a sync.WaitGroup).

In your Time.DoAfter hypothetical, it would depend on whether the channel was buffered. If it was an unbuffered channel, then the goroutine writing to the timer channel would block until someone read from the channel. If that never happened, then the goroutine would remain blocked until the program completed. If the channel was buffered, the send would complete immediately. The channel could be garbage collected before anyone read from it.

The standard library time.After behaves this way, returning a channel with a one slot buffer.
```

代码改进成

```go
// Handle 执行
func (t *UdpTransport) Handle(ctx context.Context, addr net.Addr, reader *Reader) {
	ctx, cancel := context.WithTimeout(ctx, 30*time.Second) // 30 秒超时
	defer cancel()

	ch := make(chan struct{}, 1) // 留一个 buffer
	go func() {
		t.run(ctx, addr, reader) // 优化逻辑
		ch <- struct{}{}
	}()

	select {
	case <-ch:
	case <-ctx.Done():
		logger.Warn(fmt.Sprintf("done with error %s", ctx.Err().Error()))
		return
	}
}
```

### 总结

**注意 chan buffer 作用**

**项目初期监控以及 pprof 必要性**