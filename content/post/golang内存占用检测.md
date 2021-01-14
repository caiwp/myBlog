---
title: "Golang内存占用检测"
date: 2021-01-14T10:13:40+08:00
draft: false
author: "WenpiCai"
tags: ["笔记", "Go"]
showFullContent: false
---

# Golang 内存占用检测

参考链接 https://lrita.github.io/2017/05/26/golang-memory-pprof/

涉及命令  go tool pprof http://{host}/debug/pprof/heap

### 缘由

同样的服务在上了多组服务器后监控显示内存 process_resident_memory_bytes 区别很大，多组服务会根据实际连接和用户情况增减，但有一组数值仍然居高不下，数据对比就是20+M和50+M。以下便找出两台作为对比定位原因。{host1}为需要检测的服务。

### 命令使用

通过 go tool pprof http://{host}/debug/pprof/heap 进入查看命令行，直接输入 top 后

```bash
[WenpiCai@LOCAL-192-168-97-55 ~]$ go tool pprof http://{host1}/debug/pprof/heap
Fetching profile over HTTP from http://{host1}/debug/pprof/heap
Saved profile in /home/WenpiCai/pprof/pprof.MoziGateA.alloc_objects.alloc_space.inuse_objects.inuse_space.006.pb.gz
File: MoziGateA
Type: inuse_space
Time: Jan 14, 2021 at 10:26am (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top
Showing nodes accounting for 5524.47kB, 100% of 5524.47kB total
Showing top 10 nodes out of 41
      flat  flat%   sum%        cum   cum%
 1542.01kB 27.91% 27.91%  1542.01kB 27.91%  bufio.NewReaderSize (inline)
 1025.38kB 18.56% 46.47%  1025.38kB 18.56%  net/textproto.(*Reader).ReadMIMEHeader
  902.59kB 16.34% 62.81%   902.59kB 16.34%  compress/flate.NewWriter
  516.01kB  9.34% 72.15%   516.01kB  9.34%  MoziGate/internal/server/websocket.(*Client).recv
  514.38kB  9.31% 81.46%   514.38kB  9.31%  google.golang.org/protobuf/internal/strs.(*Builder).AppendFullName
  512.10kB  9.27% 90.73%   512.10kB  9.27%  github.com/googollee/go-socket%2eio.(*conn).connect
  512.02kB  9.27%   100%   512.02kB  9.27%  github.com/googollee/go-socket.io/engineio/transport/websocket.newConn
         0     0%   100%   902.59kB 16.34%  bufio.(*Writer).Flush
         0     0%   100%  1542.01kB 27.91%  bufio.NewReader
         0     0%   100%   902.59kB 16.34%  compress/gzip.(*Writer).Write
(pprof) quit
[WenpiCai@LOCAL-192-168-97-55 ~]$ go tool pprof http://{host2}/debug/pprof/heap
Fetching profile over HTTP from http://{host2}/debug/pprof/heap
Saved profile in /home/WenpiCai/pprof/pprof.MoziGateB.alloc_objects.alloc_space.inuse_objects.inuse_space.006.pb.gz
File: MoziGateB
Type: inuse_space
Time: Jan 14, 2021 at 10:27am (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top
Showing nodes accounting for 5527.99kB, 100% of 5527.99kB total
Showing top 10 nodes out of 58
      flat  flat%   sum%        cum   cum%
 1548.03kB 28.00% 28.00%  1548.03kB 28.00%  MoziGate/internal/server/websocket.(*Client).recv
    1028kB 18.60% 46.60%     1028kB 18.60%  bufio.NewReaderSize
  902.59kB 16.33% 62.93%   902.59kB 16.33%  compress/flate.NewWriter
     513kB  9.28% 72.21%      513kB  9.28%  sync.(*Pool).pinSlow
  512.19kB  9.27% 81.47%   512.19kB  9.27%  runtime.malg
  512.12kB  9.26% 90.74%   512.12kB  9.26%  github.com/googollee/go-socket.io/engineio/transport/websocket.newConn
  512.06kB  9.26%   100%   512.06kB  9.26%  net.newFD (inline)
         0     0%   100%   512.06kB  9.26%  MoziGate/internal/server/websocket.Run.func3
         0     0%   100%   512.06kB  9.26%  MoziGate/internal/server/websocket.newClient
         0     0%   100%   902.59kB 16.33%  bufio.(*Writer).Flush
(pprof) quit
```

上面结果对比上明显有差异，然后从函数调用途径入手，利用 top20 -cum，操作如下

```bash
[WenpiCai@LOCAL-192-168-97-55 ~]$ go tool pprof http://{host1}/debug/pprof/heap
Fetching profile over HTTP from http://{host1}/debug/pprof/heap
Saved profile in /home/WenpiCai/pprof/pprof.MoziGateA.alloc_objects.alloc_space.inuse_objects.inuse_space.005.pb.gz
File: MoziGateA
Type: inuse_space
Time: Jan 14, 2021 at 10:12am (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top20 -cum
Showing nodes accounting for 3476.61kB, 52.76% of 6590.04kB total
Showing top 20 nodes out of 45
      flat  flat%   sum%        cum   cum%
         0     0%     0%  2992.12kB 45.40%  net/http.(*conn).serve
         0     0%     0%  1542.01kB 23.40%  bufio.NewReader (inline)
 1542.01kB 23.40% 23.40%  1542.01kB 23.40%  bufio.NewReaderSize (inline)
         0     0% 23.40%  1451.43kB 22.02%  bufio.(*Writer).Flush
  902.59kB 13.70% 37.10%  1451.43kB 22.02%  compress/flate.NewWriter
         0     0% 37.10%  1451.43kB 22.02%  compress/gzip.(*Writer).Write
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/client_golang/prometheus/promhttp.HandlerFor.func1
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/client_golang/prometheus/promhttp.InstrumentHandlerCounter.func1
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/client_golang/prometheus/promhttp.InstrumentHandlerInFlight.func1
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/common/expfmt.MetricFamilyToText
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/common/expfmt.MetricFamilyToText.func1
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/common/expfmt.NewEncoder.func7
         0     0% 37.10%  1451.43kB 22.02%  github.com/prometheus/common/expfmt.encoderCloser.Encode
         0     0% 37.10%  1451.43kB 22.02%  net/http.(*ServeMux).ServeHTTP
         0     0% 37.10%  1451.43kB 22.02%  net/http.HandlerFunc.ServeHTTP
         0     0% 37.10%  1451.43kB 22.02%  net/http.serverHandler.ServeHTTP
 1032.02kB 15.66% 52.76%  1032.02kB 15.66%  MoziGate/internal/server/websocket.(*Client).recv
         0     0% 52.76%     1028kB 15.60%  net/http.newBufioReader
         0     0% 52.76%  1027.32kB 15.59%  github.com/googollee/go-socket%2eio.(*conn).serveRead
         0     0% 52.76%  1024.20kB 15.54%  github.com/googollee/go-socket%2eio.(*Server).serveConn
(pprof) quit
[WenpiCai@LOCAL-192-168-97-55 ~]$ go tool pprof http://{host2}/debug/pprof/heap
Fetching profile over HTTP from http://{host2}/debug/pprof/heap
Saved profile in /home/WenpiCai/pprof/pprof.MoziGateB.alloc_objects.alloc_space.inuse_objects.inuse_space.003.pb.gz
File: MoziGateB
Type: inuse_space
Time: Jan 14, 2021 at 10:12am (CST)
Entering interactive mode (type "help" for commands, "o" for options)
(pprof) top20 -cum
Showing nodes accounting for 2447.48kB, 54.41% of 4497.85kB total
Showing top 20 nodes out of 55
      flat  flat%   sum%        cum   cum%
         0     0%     0%  2056.96kB 45.73%  github.com/googollee/go-socket%2eio.(*conn).serveRead
         0     0%     0%  1928.71kB 42.88%  net/http.(*conn).serve
         0     0%     0%  1414.70kB 31.45%  net/http.(*ServeMux).ServeHTTP
         0     0%     0%  1414.70kB 31.45%  net/http.serverHandler.ServeHTTP
         0     0%     0%  1032.02kB 22.94%  github.com/googollee/go-socket.io/engineio/transport/websocket.rcWrapper.Close
         0     0%     0%  1032.02kB 22.94%  github.com/googollee/go-socket.io/parser.(*Decoder).DecodeArgs
         0     0%     0%  1032.02kB 22.94%  github.com/googollee/go-socket.io/parser.(*Decoder).DiscardLast (inline)
         0     0%     0%  1032.02kB 22.94%  io.Copy (inline)
         0     0%     0%  1032.02kB 22.94%  io.copyBuffer
         0     0%     0%  1032.02kB 22.94%  io/ioutil.devNull.ReadFrom
 1032.02kB 22.94% 22.94%  1032.02kB 22.94%  io/ioutil.glob..func1
         0     0% 22.94%  1032.02kB 22.94%  sync.(*Pool).Get
         0     0% 22.94%  1024.94kB 22.79%  MoziGate/internal/server/websocket.Run.func3
  512.88kB 11.40% 34.35%  1024.94kB 22.79%  MoziGate/internal/server/websocket.newClient
         0     0% 34.35%  1024.94kB 22.79%  github.com/googollee/go-socket%2eio.(*funcHandler).Call
         0     0% 34.35%  1024.94kB 22.79%  github.com/googollee/go-socket%2eio.(*namespaceHandler).dispatch
         0     0% 34.35%  1024.94kB 22.79%  reflect.Value.Call
         0     0% 34.35%  1024.94kB 22.79%  reflect.Value.call
         0     0% 34.35%   902.59kB 20.07%  bufio.(*Writer).Flush
  902.59kB 20.07% 54.41%   902.59kB 20.07%  compress/flate.NewWriter
```

上面结果可知，占用内存异常问题原因应该在 github.com/prometheus/common/expfmt.encoderCloser.Encode 下一步就进源码看看。

然后 google 了一下 compress/flate.newwriter memory leak 发现挺多讨论。

### 上服务器上查

发现真实占用内存确实比其他的高，看下执行日志有一条 Did you forget to Close() the ReadCloser from NextReader? 

难道存在没 Close 导致的？

先用上重启大法，重启后监控数据正常，还会不会出现类似情况再关注。