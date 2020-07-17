---
title: "Redis源码翻阅记录"
date: 2020-07-16T11:01:18+08:00
draft: false
author: "WenpiCai"
tags: ["源码", "redis"]
showFullContent: false
---

## 准备工作

### 源码

```bash
仓库 https://github.com/antirez/redis.git
分支 git:remotes/origin/6.0
git clone -b 6.0 https://github.com/redis/redis.git
```

### 工具

```
Vim 优点：阅读内容一目了然，只键盘操作快捷。劣势：跳转较弱，会占用终端，用tmux来解决阅读节点，但感觉容易混淆。（刚开始选择）
VS Code 优点：插件较自动化，跳转轻松。劣势：outline无法右边显示，触摸板翻页感觉容易失焦点。（最终选择）
```

## 阶段一

### 粗读

* 代码目录结构整理。目标：清楚整体项目，便于后面阅读定位。

* 重点 src 目录下所有文件草读。目标：清楚文件定位，内容大致情况，得出文件重要程度，便于后面阅读重点。

### 结论

```
大体应该有以下几块：
事件
配置
数据类型：对外支持的，底层实现的。（大量代码）
网络
集群
哨兵
数据持久化
日记

服务端入口 src/server.c (突破口)
客户端入口 src/redis-cli.c
```

## 阶段二

### src/server.c

```
initServerConfig
serverCron 定时任务
beforeSleep 
afterSleep
restartServer 做好收尾工作，另执行启动，退出
listenToPort 监听TCP服务
initServer
sigShutdownHandler 监听信号关闭操作
adjustOpenFilesLimit 
InitServerLast 启动多线程，每个客户端独自线程监听连接，获取请求，找到命令，调用命令。
call 执行客户端的具体命令
processCommand 分发客户端命令
```

## 阶段三

发现新版里面用上了多线程，查了一下是4.0版本后引入，决定拉回3.2版本代码进行比较。

git clone -b 3.2 https://github.com/redis/redis.git

### src/server.c

```
initServer
```

###src/networking.c

```
acceptTcpHandler
acceptCommonHandler 调用创建客户端
createClient 创建客户端并启用事件监听 3.2和6.0区别大 3.2会将读取交到文件事件里面，6.0直接交到conn也就是当前线程。
readQueryFromClient 读取数据给客户端
processInputBuffer 分发客户端命令前的验证
```

###src/connection.c (3.2没有) 将每个客户端使用独立的线程连接，多线程

```
connSocketSetReadHandler 连接读取操作
```

### src/ae.c

```
aeProcessEvents 事件处理 https://draveness.me/redis-eventloop/
```

附 Redis 和 I/O 多路复用 https://draveness.me/redis-io-multiplexing/

### 结论

```
通过对比 3.2 与 6.0分析其中单线程与多线程有区别点：单线程主要依赖文件事件来实现，配合I/O多路复用来读取客户端的请求数据。多线程则每个客户端有独立的连接独立线程，只要读取其连接请求数据即可。
```

### 疑问：线程安全？

