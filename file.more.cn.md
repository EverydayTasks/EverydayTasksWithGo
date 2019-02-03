### Go语言标准库提供的其他方便功能

bufio

TODO: 阅读bufio的代码和文档，补充其他没讲到的有用功能

ioutil

TODO: 阅读ioutil的代码和文档，补充其他没讲到的有用功能

在实际应用中，要高效地读取最后N行，我们应当模仿UNIX系统的`tail`命令来实现这个功能，即先利用`Seek()`跳转到文件结尾，然后用缓冲区从结尾往前读取，每次读取一个缓冲区，并分析其内容，如果发现换行符，则得到了一行文本。这样反复往前读，直到读到了N行文本为止。这里代码需要处理缓冲区到文本行的转换，实际上是要自己实现`bufio`的内部细节内容。代码如下：

```go
// TODO: implement backward reading N lines with Seek() and a buffer
```

### 内存映射

内存映射有很多好处，不过我们平时似乎很少用到。

实际上，GNU grep就用到了内存映射来提高性能：

```
Finally, when I was last the maintainer of GNU grep (15+ years ago...),
GNU grep also tried very hard to set things up so that the *kernel*
could ALSO avoid handling every byte of the input, by using mmap()
instead of read() for file input.  At the time, using read() caused
most Unix versions to do extra copying.  Since GNU grep passed out
of my hands, it appears that use of mmap became non-default, but you
can still get it via --mmap.  And at least in cases where the data
is already file system buffer caches, mmap is still faster:
```
参见[why GNU grep is fast](https://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html)

参考第三方开源工程[mmap-go](https://github.com/edsrzf/mmap-go)。

TODO: 描述mmap-go对内存映射的基本实现


## 1.5 并发读写

因为Go语言的主要设计要素就是并发，在此我们着重探讨一下并发读写的课题。在这个过程中，可以看到goroutine、channel等Go语言的并发工具，如何更简洁高效地实现并发读写的功能。

并发读

并发写

多读一写

一读多写

多读多写

### 文件备份

### inotify

### 结构化二进制格式：Protocol Buffers

我们在上一章“数据”章节中已经介绍过Go语言如何处理protobuf数据，不过主要集中在内存中读写的场景。本节介绍一下读取protobuf文件时需要注意的事项。
### 结构化二进制格式：FlatBuffers

本章介绍一些文件I/O的综合应用，日常开发中常常需要对文件进行这些操作：

1. 文件过滤
2. 文件分割
3. 文件合并 
4. 文件扫描计算

我们先看看Go语言如何处理命令行参数Flags和环境变量，再深入讨论一下配置文件的读写。

TODO: Go Flags

TODO：Go Env Vars

# 1.7 综合应用实例

### Task N: grep

### Task N: split

### Task N: merge

### Task N: awk

### Task N: tail -f

### Task N：CSV金融数据分析

这里我们综合一下前面的话题，实现一个常用的文件I/O需求：读取压缩的csv日志文件，根据其中某一列筛选需要的数据，分析并计算某个字段。在UNIX中，我们常用awk来完成这个需求。

这类计算需求在数据型日志非常常见，比如金融数据（K线、Book/Trade数据等）、监控日志、统计日志等。由于日志量往往非常大，一般以压缩格式存取。利用Go语言的接口+管道特性，可以很方便地组合出各种类型的数据的读写函数。

下面我们举得例子就是一个简化的股票交易日志，CSV每行的格式假定如下：

```csv
交易时间,股票代码,买卖方向,价格,交易量
09:30:31.256,SH601398,B,5.24,1000
```

假设日志记录的是每小时一个gz压缩文件，文件名格式是TRADE_20190116_09.log.gz。

```go
// TODO: implement this code
```
