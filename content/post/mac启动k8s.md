---
title: "Mac启动k8s"
date: 2020-07-03T16:06:33+08:00
draft: false
author: "WenpiCai"
tags: ["k8s", "docker"]
description: ""
showFullContent: false
---

参考链接 https://juejin.im/post/5d87980f5188253f74438bb6

### 启动

docker mac desktop 已经自带 k8s，设置成启动就可。

### web 查看dashboard

https://github.com/kubernetes/dashboard

```bash
# 查看 token 用于登录
kubectl -n kube-system describe secret default| awk '$1=="token:"{print $2}'
```

