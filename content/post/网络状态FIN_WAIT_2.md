---
title: "网络状态FIN_WAIT_2"
date: 2019-11-05T11:13:13+08:00
draft: true
author: "WenpiCai"
tags: ["笔记"]
showFullContent: false
---

参考链接：

http://www.linuxtcpipstack.com/537.html

https://www.iteye.com/blog/kld208-1626701

在服务端进行 kill 进程释放端口时，netstat 查看会有一个网络状态 FIN_WAIT_2，所以找了一下这个释放的过程相关。

CLOSED->SYN_SENT->ESTABLISHED->FIN_WAIT_1->FIN_WAIT_2->TIME_WAIT->CLOSED