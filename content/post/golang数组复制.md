---
title: "Golang数组复制"
date: 2019-11-07T10:44:54+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

参考链接 https://www.geeksforgeeks.org/how-to-copy-an-array-into-another-array-in-golang/

```go
// creating a copy of an array by value
arr := arr1

// Creating a copy of an array by reference
arr := &arr1
```

两种方式结果不一样，第一种原值不变，第二种会导致原值也跟着改变