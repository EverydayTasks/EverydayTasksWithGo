# 样章：文件I/O


## 1.1 话题介绍

**TODO** 需要一个更能够体现编程哲学的精彩开头

文件I/O是几乎所有编程语言书籍都绕不开的入门话题之一。介绍标准库的时候，基本上第一个章节都是文件I/O。实际上，Hello World也是I/O例程，和文件I/O的差别只是Hello World写到了stdin标准输入，而没写到文件而已。

在我们的日常开发中，内存、文件、数据库是最常见的操作对象。而内存中的操作，比如数值运算、数组、指针等，大部分入门书籍都会浓墨重彩地描述，因此本书先介绍文件I/O相关的话题。

文件I/O这个话题，其实内容非常丰富。本章因为篇幅限制，只选取了最常见的几个场景，逐步深入描述，最后详细分析几个文件I/O的日常应用。

## 1.2 Go语言内置的文件I/O基础

Go语言内置集成了文件I/O的功能。介绍。

最基础的文件读取。

Go语言提供了一个方便函数，ioutil.ReadFile()，可以直接读取文件内容，得到的是一个[]byte数组：

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {

	// 读取文件内容
	buf, err := ioutil.ReadFile("hello.txt")

	// 错误处理
	if err != nil {
		panic(err)
 	}

	// 使用内置的string()函数将buf数组转换成字符串打印
	fmt.Println(string(buf))
}
```

这个函数很方便，直接调用就可以读取整个文件内容。但是这种使用方式只适合小文件，文件较大之后，就需要分段读取了。

Go语言提供的标准的读取方法是这样的：

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {

	// 打开文件
	f, err := os.Open("hello.txt")
	// 错误处理
	if err != nil {
		panic(err)
 	}
	
	buf := make([]byte, 10)
	// 读取文件内容到缓冲buf里
	n, err := f.Read(buf)

	// 错误处理
	if err != nil {
		panic(err)
 	}

	// 使用内置的string()函数将buf数组转换成字符串打印
	fmt.Println(string(buf))
}
```

最基础的文件写入。

## 1.3 文件I/O细节

利用buffer。

根据索引读取

读写整个文件的方便函数

## 1.4 文件I/O进阶

内存映射

二进制文件读写

文件编码、unicode

## 1.5 并发读写

因为Go语言的主要设计要素就是并发，在此我们着重探讨一下并发读写的课题。在这个过程中，可以看到goroutine、channel等Go语言的并发工具，如何更简洁高效地实现并发读写的功能。

并发读

并发写

多读一写

一读多写

多读多写

## 1.6 文件I/O的应用

head | tail | slice

文件分割

按内容分割

文件合并（多路归并）

## 1.7 扩展话题

CSV读写。

配置文件读写。

压缩文件读写。

扫描计算。

文件模板。
