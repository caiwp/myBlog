---
title: "Linux命令pkill"
date: 2019-11-01T15:47:45+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Linux"]
showFullContent: false
---

https://man.linuxde.net/pkill

**pkill命令**可以按照进程名杀死进程。会全部杀掉。

```bash
pkill (选项) (参数)
-o：仅向找到的最小（起始）进程号发送信号；
-n：仅向找到的最大（结束）进程号发送信号；
-P：指定父进程号发送信号；
-g：指定进程组；
-t：指定开启进程的终端。
```

