---
title: "Go比较float"
date: 2019-10-31T16:11:19+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

* 直接比较是会有坑，需要进行减去取绝对值的有效范围如

```go
func almostEqual(a, b float64) bool {
	return math.Abs(a-b) <= 0.001
}
```

