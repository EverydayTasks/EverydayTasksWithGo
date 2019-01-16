# 样章：文件I/O

## 1.1 话题介绍

**TODO** 需要一个更能够体现编程哲学的精彩开头

文件I/O是几乎所有编程语言书籍都绕不开的入门话题之一。介绍标准库的时候，基本上第一个章节都是文件I/O。实际上，Hello World也是I/O例程，和文件I/O的差别只是Hello World写到了stdin标准输入，而没写到文件而已。

在我们的日常开发中，内存、文件、数据库是最常见的操作对象。而内存中的操作，比如数值运算、数组、指针等，大部分入门书籍都会浓墨重彩地描述，因此本书先介绍文件I/O相关的话题。

文件I/O这个话题，其实内容非常丰富。本章因为篇幅限制，只选取了最常见的几个场景，逐步深入描述，最后详细分析几个文件I/O的日常应用。

## 1.2 Go语言内置的文件I/O基础

Go语言内置集成了文件I/O的功能。介绍。

最基础的文件读取。

Go语言提供了一个方便函数，`ioutil.ReadFile()`，可以直接读取文件内容，得到的是一个`[]byte`数组：

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
	defer f.Close()
	
	// 新建缓冲
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

我们可以看到，这里使用了一个缓冲数组`buf`，用来存放读到的字符。`Read()`函数每次调用，会读到这个缓冲填满为止；如果文件已经没有多少剩余，则buf不会被完全填满。因此`Read()`的提供了返回值`n`，用来表示实际读到的字节数。我们可以通过判断`n`和`len(buf)`是否相等来判断文件是否已经读取到结尾了。

上面的这个例子，实际上只调用了一次`Read()`，因此最多只读到了10个字节，要读完整个文件，需要多次读取。

下面是使用`for`循环，多次读取文件，直到读取到全部内容的代码：

```go
// TODO
```


看完了文件读取，我们来看看最基础的文件写入。

```go
package main

import "io/ioutil"

func main() {
	data := []byte("Hello World!\n")
	ioutil.WriteFile("E:/hello_out.txt", data, 0644)
}
```

这里，`ioutil.WriteFile()`的第三个参数是`FileMode`，使用UNIX文件权限编码。这里`0644`开头的0表示八进制数字。

如果要用缓冲数组写入，可以这么做：

```go
// TODO: write with a buffer
```

如果不想覆盖已有文件，而是在其后添加内容，则可以使用APPEND模式打开文件，再写入：

```go
package main

import "os"

func main() {
	f, err := os.OpenFile("E:/append.txt", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
	if err != nil {
		panic(err)
	}
	if _, err := f.WriteString("a new line\n"); err != nil {
		panic(err)
	}
	if err := f.Close(); err != nil {
		panic(err)
	}
}

```

注意，这里不但有`os.O_APPEND`，还使用了`os.O_CREATE`，表示如果文件不存在，则新建该文件并写入内容。如果文件存在，则使用`APPEND`模式往后添加新内容。


OK，到此为止我们看到了Go语言文件基本读写的功能。我相信大家在阅读本书之前一定都看过类似的入门介绍，因此本节并没有详细讲述，而只是列出来供大家参考而已。本书的重点，在于本章后面会讲到的，更多的读写相关的细节。

## 1.3 文件I/O细节

我们在日常开发的过程中，使用文件读写时，实际的需求往往不同于上节讲到的整个文件一次读入这种简单场景。例如：100G的大文件，如何实现读取而不撑爆内存？或者我们需要按行来读入文件，而不是按固定长度来读取？作者在本节中尝试列举几种常见的文件读写场景，并寻找合适的解决方案。

### 场景1：按行读取


Go语言在更早的版本还提供了，但他们各有各的问题

- `reader.ReadString('\n')`:
- `reader.ReadLine()`：

TODO: 详细描述这两个函数的问题

因此，Go1.1以后，官方推荐的按行读取的方式是使用`bufio.Scanner`。
基本用法如下：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	file, err := os.Open("E:/b.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)
	// Scan() until end of file
	for scanner.Scan() {
		// One line of text
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```

另外需要注意的是，`Scanner`是有缓冲区的，其默认大小为`bufio.MaxScanTokenSize`，现在的版本这个值是65536字节，即64KB。如果你读取的文件单行大小超出了这个限度，就会报错`bufio.Scanner: token too long`。如果你的文件单行太大了，可以在调用`scanner.Scan()`之前，通过`Scanner.Buffer()`函数来指定一个足够大的缓冲区，这样就不会遇到缓冲区超长的问题了。

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	// Open file
	file, err := os.Open("E:/a.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	// make a buffer with enough capacity
	bufCap := 128 * 1024
	buf := make([]byte, bufCap, bufCap)

	// make a scanner with the buffer
	scanner := bufio.NewScanner(file)
	scanner.Buffer(buf, bufCap)

	// Scan() until end of file
	for scanner.Scan() {
		// One line of text
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}
```

### 场景2：读取文件的部分

很多时候，我们并不需要读取整个文件，而是只需要查看其中的一部分即可。这里有几个常见的需求场景：

1. 读取文件开头的一部分
2. 读取文件中间的一部分
3. 读取文件结尾的一部分

类似的，对于文本文件，如果我们的场景是按行读取的话，那么这几个需求就变为：

1. 读取文件开头的N行
2. 读取文件从第M行开始到第N行结束的内容
3. 读取文件最后N行的内容

有了`bufio.Scanner`，前两个功能可以很容易实现。要读取文件开头N行，只要`Scan()`到第N行之后退出读取循环即可；而要读取第M行到第N行，则在读取前M行时，跳过去忽略掉其内容即可。

读取文件开头N行的示例如下：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	// read first N lines
	N := 3
	file, err := os.Open("E:/a.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)

	n := 0
	// Scan() until end of file
	for scanner.Scan() {
		n++
		// if current line number is greater than N, quit
		if n > N {
			break
		}
		// One line of text
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```

读取第M行到第N行的示例如下：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	// read first N lines
	N := 3
	file, err := os.Open("E:/a.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)

	n := 0
	// Scan() until end of file
	for scanner.Scan() {
		n++
		// if current line number is greater than N, quit
		if n > N {
			break
		}
		// One line of text
		fmt.Println(scanner.Text())
	}

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```

而读取最后N行的内容则更复杂一点：再读完整个文件之前，无法知道总共有多少行，因此也无法知道应当从第几行算起。如果不考虑效率，可以利用一个大小为N的FIFO对列，把独到的每一行都加入对列。当超过N行时，把最早的内容从队列中取出来抛弃掉。这样读完整个文件时，对列里剩下的N行内容实际上就是文件的最后N行。示例如下：

```go
```

但这种简单的办法显然效率不高，我们应当模仿UNIX系统的`tail`命令来实现这个功能，即先利用`Seek()`跳转到文件结尾，然后用缓冲区从结尾往前读取，每次读取一个缓冲区，并分析其内容，如果发现换行符，则得到了一行文本。这样反复往前读，直到读到了N行文本为止。这里代码需要处理缓冲区到文本行的转换，实际上是要自己实现`bufio`的内部细节内容。代码如下：

```go
// TODO: implement backward reading N lines with Seek() and a buffer
```

最后，关于`tail`，有一个开源的第三方库<https://github.com/hpcloud/tail>实现了完整的tail功能，而且还实现了类似`tail -f`的实时跟踪文件新写入内容的功能。我们会在最后一节“扩展话题”里专门研究这个第三方库的设计和实现。

### Go语言标准库提供的其他方便功能

bufio

ioutil

## 1.4 文件I/O进阶

文件编码与unicode

Go语言默认的文件编码是UTF-8，如果需要打开其他编码的文件，比如GBK，需要使用encoding模块。Go语言官方提供的编码模块在<golang.org/x/text/encoding>，这个模块将来可能会加入到标准库中。

我们先看看如何读取GBK格式的文件：

```go
package main

import (
	"bufio"
	"fmt"
	"os"

	"golang.org/x/text/encoding/simplifiedchinese"
	"golang.org/x/text/transform"
)

func main() {
	readGBK("E:/gbk_sample.txt")
}

// Read UTF-8 from a GBK encoded file.
func readGBK(filename string) {
	f, err := os.Open(filename)
	if err != nil {
		panic(err)
	}
	defer f.Close()

	// Use GBK encoding
	enc := simplifiedchinese.GBK
	// make a transform reader with GBK decoder
	r := transform.NewReader(f, enc.NewDecoder())

	// Read converted UTF-8 from `r` as needed.
	// As an example we'll read line-by-line showing what was read:
	sc := bufio.NewScanner(r)
	for sc.Scan() {
		fmt.Printf("Read line: %s\n", sc.Bytes())
	}
	if err = sc.Err(); err != nil {
		panic(err)
	}

}
```

然后，不同编码格式的文件可以在内存中进行转换：

```go
package main

import (
	"bytes"
	"fmt"
	"io/ioutil"

	"golang.org/x/text/encoding/simplifiedchinese"
	"golang.org/x/text/transform"
)

// 读取GBK编码的数据，转化成标准编码UTF8
func decodeGBK(s []byte) ([]byte, error) {
	reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewDecoder())
	d, e := ioutil.ReadAll(reader)
	if e != nil {
		return nil, e
	}
	return d, nil
}

// 把标准编码UTF8的数据，转化成GBK编码
func encodeGbk(s []byte) ([]byte, error) {
	reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewEncoder())
	d, e := ioutil.ReadAll(reader)
	if e != nil {
		return nil, e
	}
	return d, nil
}

func main() {

	s := "你好，明天！"
	gbk, err := decodeGBK([]byte(s))
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(string(gbk))
	}

	utf8, err := encodeGbk(gbk)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(string(utf8))
	}
}
```

这里只是内存中转换，要进行文件转换，下面我们展示如下的转换过程：

开头，hello.GBK.txt文件里存放的是GBK编码的“你好，明天！”，我们读入该文件，用GBK解码成为标准编码UTF8的格式，写入到hello.UTF8.txt中。然后我们再读取hello.UTF8.txt，在内存中转换为GBK格式，再写入到 hello.GBK.new.txt中。

```go

```



内存映射

参考第三方开源工程[mmap-go](https://github.com/edsrzf/mmap-go)。


## 1.5 并发读写

因为Go语言的主要设计要素就是并发，在此我们着重探讨一下并发读写的课题。在这个过程中，可以看到goroutine、channel等Go语言的并发工具，如何更简洁高效地实现并发读写的功能。

并发读

并发写

多读一写

一读多写

多读多写

## 1.6 文件I/O的应用

文件分割

按内容分割

文件合并（多路归并）

CSV读写。

Go标准库提供了"encoding/csv"模组，参考https://golang.org/pkg/encoding/csv/


配置文件读写。

压缩文件读写。

扫描计算。

文件模板。

## 1.7 扩展话题：相关第三方库赏析

tail库：<https://github.com/hpcloud/tail>

