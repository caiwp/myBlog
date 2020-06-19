---
title: "Goroutine请求TCP返回读取问题"
date: 2020-04-17T15:16:48+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

场景：

```
0、本服务是一个 TCP 服务监听接收请求
1、需要与某个远程服务建立 TCP 连接。
2、建立唯一的客户端C并保持连接。
3、多个请求与本服务建立 TCP 连接产生多个 goroutine。
4、当 goroutine 需要涉及到远程服务调用客户端C进行 Write 和 Read 操作。每个应当是独立不干扰的。
5、出现 Read 操作时数据并不按预期返回，随着 goroutine 数量增多出现概率变大。
```

定位问题：应该是多goroutine并发读取socket缓冲区数据导致。

解决：

```
A、设想通过 channel 来避免并发。复杂度增加，需要对应将结果与请求对应，并且个别地方是阻塞请求。
B、加锁控制。简单粗暴，加好超时控制避免死锁情况。
```

