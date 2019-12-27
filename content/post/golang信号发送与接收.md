---
title: "Golang信号发送与接收"
date: 2019-12-27T15:37:56+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

参考链接 https://stackoverflow.com/questions/40498371/how-to-send-an-interrupt-signal-in-golang

在需要关闭程序时发送信号

```go
syscall.Kill(syscall.Getpid(), syscall.SIGINT)
```

接收信号

```go
var stopChan = make(chan os.Signal, 2)
signal.Notify(stopChan, os.Interrupt, syscall.SIGTERM, syscall.SIGINT)

<-stopChan // wait for SIGINT
```

