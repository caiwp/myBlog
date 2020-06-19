---
title: "Golang指定时区"
date: 2020-04-07T16:54:04+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

参考链接 https://stackoverflow.com/questions/27991671/how-to-get-the-current-timestamp-in-other-timezones-in-golang 

```golang
//init the loc
loc, _ := time.LoadLocation("Asia/Shanghai")

//set timezone,  
now := time.Now().In(loc)
```

