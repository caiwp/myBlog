---
title: "Mac制作linux启动盘"
date: 2019-11-26T17:35:32+08:00
draft: false
author: "WenpiCai"
tags: ["Linux"]
showFullContent: false
---

```bash
# 列出并找出盘
diskutil list

# 取消挂载
diskutil umountDisk disk4

# 写入
sudo dd if=ubuntu-17.10-desktop-amd64.iso of=/dev/disk4 bs=1m
```

