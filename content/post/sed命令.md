---
title: "Sed命令"
date: 2020-05-08T15:20:54+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Linux"]
showFullContent: false
---

参考链接 https://www.runoob.com/linux/linux-comm-sed.html

```bash
直接替换文件内容
sed -i 's/108/581/g' *.json

在每行的头添加字符，比如"HEAD"，命令如下：
sed 's/^/HEAD&/g' test.file

在每行的行尾添加字符，比如“TAIL”，命令如下：
sed 's/$/&TAIL/g' test.file

替换第2个值
sed -i 's/108/581/2' test.sh
```

