# 样章：文件I/O

- [样章：文件I/O](#%E6%A0%B7%E7%AB%A0%E6%96%87%E4%BB%B6io)
	- [1.1 话题介绍](#11-%E8%AF%9D%E9%A2%98%E4%BB%8B%E7%BB%8D)
	- [1.2 Go语言内置的文件I/O基础](#12-go%E8%AF%AD%E8%A8%80%E5%86%85%E7%BD%AE%E7%9A%84%E6%96%87%E4%BB%B6io%E5%9F%BA%E7%A1%80)
	- [1.3 文件I/O细节](#13-%E6%96%87%E4%BB%B6io%E7%BB%86%E8%8A%82)
		- [场景1：按行读取](#%E5%9C%BA%E6%99%AF1%E6%8C%89%E8%A1%8C%E8%AF%BB%E5%8F%96)
		- [场景2：读取文件的部分](#%E5%9C%BA%E6%99%AF2%E8%AF%BB%E5%8F%96%E6%96%87%E4%BB%B6%E7%9A%84%E9%83%A8%E5%88%86)
			- [二进制文件](#%E4%BA%8C%E8%BF%9B%E5%88%B6%E6%96%87%E4%BB%B6)
			- [文本文件](#%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6)
		- [Go语言标准库提供的其他方便功能](#go%E8%AF%AD%E8%A8%80%E6%A0%87%E5%87%86%E5%BA%93%E6%8F%90%E4%BE%9B%E7%9A%84%E5%85%B6%E4%BB%96%E6%96%B9%E4%BE%BF%E5%8A%9F%E8%83%BD)
	- [1.4 文件I/O进阶](#14-%E6%96%87%E4%BB%B6io%E8%BF%9B%E9%98%B6)
		- [文件编码与unicode](#%E6%96%87%E4%BB%B6%E7%BC%96%E7%A0%81%E4%B8%8Eunicode)
		- [表格化数据格式：CSV](#%E8%A1%A8%E6%A0%BC%E5%8C%96%E6%95%B0%E6%8D%AE%E6%A0%BC%E5%BC%8Fcsv)
		- [结构化数据文件格式：JSON和XML](#%E7%BB%93%E6%9E%84%E5%8C%96%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8Fjson%E5%92%8Cxml)
		- [结构化二进制格式：Protobuf](#%E7%BB%93%E6%9E%84%E5%8C%96%E4%BA%8C%E8%BF%9B%E5%88%B6%E6%A0%BC%E5%BC%8Fprotobuf)
		- [图像数据文件：Image](#%E5%9B%BE%E5%83%8F%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6image)
		- [压缩文件读写。](#%E5%8E%8B%E7%BC%A9%E6%96%87%E4%BB%B6%E8%AF%BB%E5%86%99)
		- [内存映射](#%E5%86%85%E5%AD%98%E6%98%A0%E5%B0%84)
	- [1.5 并发读写](#15-%E5%B9%B6%E5%8F%91%E8%AF%BB%E5%86%99)
	- [1.6 文件I/O的应用](#16-%E6%96%87%E4%BB%B6io%E7%9A%84%E5%BA%94%E7%94%A8)
		- [配置文件](#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)
	- [1.7 扩展话题：相关第三方库赏析](#17-%E6%89%A9%E5%B1%95%E8%AF%9D%E9%A2%98%E7%9B%B8%E5%85%B3%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93%E8%B5%8F%E6%9E%90)

## 1.1 话题介绍

**TODO** 需要一个更能够体现编程哲学的精彩开头

文件I/O是几乎所有编程语言书籍都绕不开的入门话题之一。介绍标准库的时候，基本上第一个章节都是文件I/O。实际上，Hello World也是I/O例程，和文件I/O的差别只是Hello World写到了stdin标准输入，而没写到文件而已。

在我们的日常开发中，内存、文件、数据库是最常见的操作对象。而内存中的操作，比如数值运算、数组、指针等，大部分入门书籍都会浓墨重彩地描述，因此本书先介绍文件I/O相关的话题。

文件I/O这个话题，其实内容非常丰富。本章因为篇幅限制，只选取了最常见的几个场景，逐步深入描述，最后详细分析几个文件I/O的日常应用。

那么我们日常都对文件进行哪些操作呢？不外乎增删查改。

我们常常读写的都是哪些文件？纯文本、二进制数据、CSV日志、JSON、XML、HTML、压缩文件、图像文件、protobuf，等等等等。我们不可能在本书中逐一介绍全部的文件格式，否则这就成了文件格式大全，而不是文件I/O的章节了。但大致观察出几个类别，对其相关的文件I/O做一番介绍，读者就可以举一反三了。比如了解了JSON文件的读写，再去处理XML时，只要查找一下XML格式的读写库文档，就不会太难。

从处理方式来看，大致可以分为几种：

1. 纯二进制文件：读写的单位是`[]byte`数组，了解最基本的文件读写例程。
2. 纯文本文件：读写的单位一般是行，了解如何处理换行符。
3. 格式化二进制文件：如protobuf、图像文件等，需要按照文件二进制的格式去读写。
4. 列式纯文本文件：如CSV
5. 格式化纯文本文件：如JSON、XML、HTML等
6. 其他辅助文件格式：如压缩文件

这些就是我们本章要介绍的各类文件对象。我们会逐一观察它们的需求，并选取其中一些典型的案例，探讨和实现其解决方案。通过这些了解，同一类型的其他文件，大概就可以触类旁通了。

本章一开始先回顾一下文件I/O的基本操作，这里涉及到的话题基本都是Go语言标准库处理到的。因为很多入门书籍都会涉及到这些话题，所以不深入讨论。

之后，对于上述6种文件类型的详细需求，我们会结合日常工作的实际情况举例分析。如CSV文件常用在科学计算或者金融数据的处理上，我们就找一些这方面的示例。

其中有的复杂功能，用到了第三方库，比如protobuf等，我们会在最后一节单独进行扩展话题：第三方库赏析来介绍。

## 1.2 Go语言内置的文件I/O基础

Go语言内置集成了文件I/O的功能。介绍。

TODO: 先准备两个文件，一个英文的，可以使用莎士比亚之类的英文诗；另一个中文的，可以使用诗经的一首诗。还得考虑一个典型的二进制文件示例。

TODO: 先介绍基础写文件，并由此准备好上述三个示例文件，供下文的读文件章节用于作为示例。

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
	buf, err := ioutil.ReadFile("E:/a.txt")

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
	f, err := os.Open("E:/a.txt")
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
	fmt.Print("Read", n, "Bytes:", string(buf))
}
```

我们可以看到，这里使用了一个缓冲数组`buf`，用来存放读到的字符。`Read()`函数每次调用，会读到这个缓冲填满为止；如果文件已经没有多少剩余，则buf不会被完全填满。因此`Read()`的提供了返回值`n`，用来表示实际读到的字节数。我们可以通过判断`n`和`len(buf)`是否相等来判断文件是否已经读取到结尾了。

上面的这个例子，实际上只调用了一次`Read()`，因此最多只读到了10个字节，要读完整个文件，需要多次读取。

下面是使用`for`循环，多次读取文件，直到读取到全部内容的代码：

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// 打开文件
	f, err := os.Open("E:/a.txt")
	// 错误处理
	if err != nil {
		panic(err)
	}
	defer f.Close()

	// 新建缓冲
	buf := make([]byte, 10)
	// 循环读取文件内容到缓冲buf里
	for {
		n, err := f.Read(buf)
		// 如果本次读到的数据长度为0，表示遇到了EOF文件末尾，可以跳出循环
		if n == 0 {
			break
		}
		// 注意打印的时候不要打印全部buf，真正有效的是buf[:n]前n个字节
		fmt.Print(string(buf[:n]))
		// 错误处理
		if err != nil {
			panic(err)
		}
	}
}
```

在实际使用中，这样用缓冲数组读取文件内容的方法还是太过底层了。不论我们的文件是格式化的二进制，还是文本文件，都需要进一步处理。格式化的二进制文件进一步如何处理，和其文件具体格式直接关联，因此我们不在这里作通论，而是在后面讨论具体的格式时再详谈，如protobuf格式等。而文本文件，则不管是什么格式，一般都要处理分隔符（最常见的是换行符`'\n'`），因此我们会在下一节讨论文本按行读取这个最常见的需求。

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

上面这个函数是一次性写入整个文件的内容，实际操作中只有处理小文件时为了方便才会使用。一般情况下，程序要写入文件的数据都会比较大，而且不是一次性准备好的。比如记录日志，或者从网络上传的流数据。因此我们可以使用多次写入的`Write()`函数，往文件中直接写入`[]byte`数组数据。


```go
// TODO: write with a buffer
```

如果是文本文件，还可以使用`WriteString()`函数来写入：

```go
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

上述这几个写入操作都还有一个性能问题：每次写入时，是直接向硬盘写入的，如果单次内容很少，写入次数很多，会浪费大量的I/O操作资源。因此，Go标准库还提供了`bufio`的写入模块，这里的写入操作，都会先写入到一个缓冲区中，待到缓冲区满了，再自动写入到文件中去。这样可以大大减少I/O操作，明显提高性能。

// TODO: bufio的写文件示例

OK，到此为止我们看到了Go语言文件基本读写的功能。我相信大家在阅读本书之前一定都看过类似的入门介绍，因此本节并没有详细讲述，而只是列出来供大家参考而已。本书的重点，在于本章后面会讲到的，更多的读写相关的细节。

## 1.3 文件I/O细节

我们在日常开发的过程中，使用文件读写时，实际的需求往往不同于上节讲到的整个文件一次读入这种简单场景。例如：100G的大文件，如何实现读取而不撑爆内存？或者我们需要按行来读入文件，而不是按固定长度来读取？作者在本节中尝试列举几种常见的文件读写场景，并寻找合适的解决方案。

### 场景1：按行读取


Go语言提供了一次读取直到指定分隔符的函数`ReadString(sep)`，基本用法如下：

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"
)

func main() {
	// 打开文件
	f, err := os.Open("E:/a.txt")
	// 错误处理
	if err != nil {
		panic(err)
	}
	defer f.Close()

	r := bufio.NewReader(f)

	for {
		s, err := r.ReadString('\n')
		// 注意读到的'\n'也保留在s中了，所以这里用fmt.Print而不是fmt.Println
		fmt.Print(s)
		if err == io.EOF {
			break
		}
	}
}

```

当`ReadString`遇到文件结束，或者最后一个`'\n'`时，`err`会变成`io.EOF`。因此如果文件结尾不是`'\n'`的话，需要先使用`s`再判断`err == io.EOF`，否则会漏掉一行。这是使用`ReadString`常见的一个需要注意的事项。 另外`ReadString`还有一个问题，就是每一次读取都新建了一个缓冲区区存放当前行的全部内容，因此对内存的消耗很大。最后，`ReadString`返回的一行文本，会包含最后的`'\n'`，这在很多文本处理场景中都显得不太方便。

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

那么，即想拥有类似于`Scanner`的方便，又想要一个自适应长度的缓冲区，应当怎么做呢？我们可以考虑这样来实现：

TODO：实现可变长缓冲区的`Scanner`，即背后不用`[]byte`数组，而是用一个可变长的`vector`来存放缓冲数据。

### 场景2：读取文件的部分


很多时候，我们并不需要读取整个文件，而是只需要查看其中的一部分即可。这里有几个常见的需求场景：

1. 读取文件开头的一部分
2. 读取文件中间的一部分
3. 读取文件结尾的一部分

#### 二进制文件
首先，对于二进制文件，假设我们需要读取长度为N个字节的数据。

那么对于需求一，如果N并不大，可以用一个`[]byte`数组存放，则非常简单：

```go

```

如果N比较大，则需要多次读取，一直读到足够N字节即可：

```go
```

而对于需求二，读取中间部分的数据，假设我们需要读取文件从第M字节到第N字节的数据，则可以使用`Seek()`函数跳转到文件第M个字节，再连续读到第N个字节即可。这里示例是N-M很大，需要多次读取的情况：

```go
```

对于需求三，我们则可以先使用`Seek()`从文件末尾往前跳跃N个字节，再从那里一直读到文件末尾即可：

```go
```

除了`Seek()`，Go还提供了`Reset()`和`Peek()`等文件I/O操作，这样组合起来可以解决更复杂的场景。如：TODO

#### 文本文件

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

TODO: 阅读bufio的代码和文档，补充其他没讲到的有用功能

ioutil

TODO: 阅读ioutil的代码和文档，补充其他没讲到的有用功能

## 1.4 文件I/O进阶

到现在我们已经很熟悉Go语言文件读写的基本操作了。那么对于文件读写，还有哪些平时常用的需求呢？除了基本的二进制和纯文本读写之外，还会遇到几种实践情况：

1. 非unicode编码。前面我们处理的都是标准编码的文件，但是现实中接触到很多文件都是非标准编码的，比如GBK等，这需要单独的处理。
2. 超大文件。
3. 压缩文件。现实中，很多大文件数据，例如日志、金融数据等，既需要日常积累和计算，又需要长期存储，那么直接读写压缩文件就是很常见的需求了。幸好Go语言的I/O有非常方便的类管道机制，我们只要在正常的读写之上再加上一层压缩功能既可。
4. 内存映射。有更高I/O性能需求的时候，内存映射是一个好方法。另外，内存映射还能实现进程间通讯。所以了解Go语言如何处理内存映射，也是必要的。
5. 并发读写。这个话题因为涉及到Go语言最擅长的并发功能，情况也比较复杂，我们单独在下一节讲述。

下面分别描述上述的问题。

### 文件编码与unicode

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

### 表格化数据格式：CSV

Go标准库提供了"encoding/csv"模组，参考https://golang.org/pkg/encoding/csv/

### 结构化数据文件格式：JSON和XML

### 结构化二进制格式：Protobuf

### 图像数据文件：Image

### 压缩文件读写。

Go语言标准库`compress`模块提供了几种常见格式的压缩和解压：

- bzip2
- flate
- gzip
- lzw
- zlib

这里我们概要介绍一下最常用的gzip格式，其他格式用法基本都一样。

读取gzip格式压缩文件：

写入gzip压缩文件：

文件头信息

利用Go语言Reader接口的组合特性，将gzip和csv配合起来，可以实现读写gzip格式的csv数据文件。

```go
package main

import (
    "compress/gzip"
    "encoding/csv"
    "fmt"
    "log"
    "os"
)

func main() {
    f, err := os.Open("data.csv.gz")
    if err != nil {
        log.Fatal(err)
    }
    defer f.Close()
    gr, err := gzip.NewReader(f)
    if err != nil {
        log.Fatal(err)
    }
    defer gr.Close()

    cr := csv.NewReader(gr)
    rec, err := cr.Read()
    if err != nil {
        log.Fatal(err)
    }
    for _, v := range rec {
        fmt.Println(v)
    }
}
```

同样的方法，也可以用于其他格式，如压缩的JSON等。


### 内存映射

参考第三方开源工程[mmap-go](https://github.com/edsrzf/mmap-go)。

TODO: 描述mmap-go对内存映射的基本实现


## 1.5 并发读写

因为Go语言的主要设计要素就是并发，在此我们着重探讨一下并发读写的课题。在这个过程中，可以看到goroutine、channel等Go语言的并发工具，如何更简洁高效地实现并发读写的功能。

并发读

并发写

多读一写

一读多写

多读多写

## 1.6 文件I/O的应用

本章介绍一些文件I/O的综合应用，日常开发中常常需要对文件进行这些操作：

1. 文件过滤
2. 文件分割
3. 文件合并 
4. 文件扫描计算

这里我们综合一下前面的话题，实现一个常用的文件I/O需求：读取压缩的csv日志文件，根据其中某一列筛选需要的数据，分析并计算某个字段。在UNIX中，我们常用awk来完成这个需求。

这类计算需求在数据型日志非常常见，比如金融数据（K线、Book/Trade数据等）、监控日志、统计日志等。由于日志量往往非常大，一般以压缩格式存取。利用Go语言的接口+管道特性，可以很方便地组合出各种类型的数据的读写函数。

下面我们举得例子就是一个简化的股票交易日志，CSV每行的格式假定如下：

```csv
交易时间,股票代码,买卖方向,价格,交易量
09:30:31.256,SH601398,B,5.24,1000
```

假设日志记录的是每小时一个gz压缩文件，文件名格式是TRADE_20190116_09.log.gz。

```go
```


### 配置文件

另外，我们除了数据文件，还需要处理配置文件，因此这里也描述一下常用配置文件的读写。因为配置

Go语言并没有规定统一的配置语言，也没有官方推荐。所以需要根据你的需求来寻找合适的配置语言。

一般来说，一个工程的配置可以有多种来源：

1. 代码内写死的默认值
2. 程序命令行参数。在Go语言里这个称为Flags。标准库有`flags`模块专门处理命令行参数的读取。
3. 环境变量。
4. 配置文件。格式可以是JSON、XML等，或者专业配置语言INI、YAML、TOML等。
5. 分布式配置系统。如etcd、Consul等。

我们先看看Go语言如何处理命令行参数Flags和环境变量，再深入讨论一下配置文件的读写。

TODO: Go Flags

TODO：Go Env Vars

Go语言的标准工程结构并不需要配置文件，而是通过源码依赖与import路径来解决工程依赖关系。因此Go语言标准库里并没有提供对某种配置语言的支持。但作为通用数据格式，JSON和XML这两种格式在标准库里有较好的支持，所以不少人用JSON/XML作为配置语言。

直接使用JSON作为配置文件，可以参考这样的示例：

```go
// TODO: read JSON config

```

但实际上JSON做配置语言还是没有那些专业户好用，毕竟它有两个大缺点：

1. 不支持注释。没有注释说明，配置文件的可读性大大降低，也增加了多人维护的沟通成本。而且无法用注释快速切换配置项，实际使用时也不方便。
2. 配置变量名必须用双引号。写起来比其他配置语言还是更加冗长。

而XML作为传统配置语言，功能倒是足够，但是它是所有常用配置语言里最冗长的。我们也举一个示例：

```go
// TODO: read XML config
```

有不少第三方库支持其他专门的配置语言格式，我们选取几个比较流行的坐下介绍：

go-ini: <https://github.com/go-ini/ini>。支持ini配置文件。

TOML: <https://github.com/BurntSushi/toml>。支持TOML配置语言。TOML是Rust官方推荐的配置语言（用于其代码工程配置），可读性、简洁性、和丰富度都不错。

YAML: <https://github.com/go-yaml/yaml>。比较传统的专业配置语言，可以说是简洁性提高很多的XML。

IniFlag: <https://github.com/vharitonsky/iniflags>。支持命令行参数Flag和ini文件格式的混合配置。

GoConfig: <https://github.com/micro/go-config>。支持多种配置源，如环境变量、命令行参数Flag、多种格式配置文件、etcd、k8s configmaps等。并且他有个特色可以实现动态加载，即这个库可以自动监测多个配置源，遇到更新时会自动重新加载。

Viper: <https://github.com/spf13/viper>。最强大的配置框架。支持多种配置源：程序设置、命令行参数Flag、环境变量、 多种配置文件（JSON, TOML, YAML, HCL, 以及Java Properties文件）、分布式配置系统（etcd、Consul）、缓冲区等。而且也支持动态加载。另外，它还符合`12-Factor apps`规范，利于云部署。所以现在viper是最流行的配置框架。

上面介绍的这几个第三方库，基本是按照从简单到复杂的顺序排列的，所以读者可以参考自己的需求，选择适合的库来使用。总的来说，如果你的工程很简单，只需要几个配置项，那么通常直接用Flags或者环境变量就可以了。稍微复杂一点的，可以使用json/xml/ini等配置文件。如果需要结构化比较丰富的配置，则可以考虑TOML和YAML。如果是需要动态加载等复杂功能，或者要部署到云端，需要etcd等远程配置系统的话，可以考虑GoConfig或者Viper。

我们会在最后的“第三方库赏析”里，专门对Viper库做一下分析，寻找到其中处理文件的代码逻辑，择其优秀者赏析之。

## 1.7 扩展话题：相关第三方库赏析

tail库：<https://github.com/hpcloud/tail>

viper库：<https://github.com/spf13/viper>