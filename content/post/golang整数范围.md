---
title: "Golang整数范围"
date: 2019-11-12T10:25:55+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

### 参考链接 

https://stackoverflow.com/questions/6878590/the-maximum-value-for-an-int-type-in-go

https://groups.google.com/group/golang-nuts/msg/71c307e4d73024ce?pli=1

The germane part:

> Since integer types use two's complement arithmetic, you can infer the min/max constant values for `int` and `uint`. For example,
>
> ```golang
> const MaxUint = ^uint(0) 
> const MinUint = 0 
> const MaxInt = int(MaxUint >> 1) 
> const MinInt = -MaxInt - 1
> ```

As per @CarelZA's comment:

```golang
uint8  : 0 to 255 
uint16 : 0 to 65535 
uint32 : 0 to 4294967295 
uint64 : 0 to 18446744073709551615 
int8   : -128 to 127 
int16  : -32768 to 32767 
int32  : -2147483648 to 2147483647 
int64  : -9223372036854775808 to 9223372036854775807
```