---
title: "Linux命令awk"
date: 2019-11-05T15:44:55+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Linux"]
showFullContent: false
---

参考链接 https://www.runoob.com/linux/linux-comm-awk.html

```bash
每行按空格或TAB分割，输出文本中的1、4项
awk '{print $1,$4}' log.txt

使用","分割
awk -F, '{print $1,$2}' log.txt

if 条件
awk '{if ($1==1) print "A"; else if ($1==2) print "B"; else print "C"}'
```

