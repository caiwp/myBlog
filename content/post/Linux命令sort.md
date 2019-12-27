---
title: "Linux命令sort"
date: 2019-11-05T15:45:00+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Linux"]
showFullContent: false
---

参考链接 https://www.cnblogs.com/coderchuanyu/p/4194211.html

忽略相同行使用-u选项或者uniq

```bash
　　
　　只显示单一行：
　　uniq -u file.txt 　
　　sort file.txt | uniq -u

　　统计各行在文件中出现的次数：
　　sort file.txt | uniq -c

　　在文件中找出重复的行：
　　sort file.txt | uniq -d
```

