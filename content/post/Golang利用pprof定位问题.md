---
title: "Golang 利用 pprof 定位问题"
date: 2019-10-30T15:40:45+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

在代码中引入  _ "net/http/pprof" 并监听 http 服务后

```sh
通过终端查看
go tool pprof http://127.0.0.1:9191/debug/pprof/heap

通过浏览器查看
http://127.0.0.1:9191/debug/pprof/
```

