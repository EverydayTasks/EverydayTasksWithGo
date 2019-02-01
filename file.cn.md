# 文件操作

- [文件操作](#文件操作)
	- [1.1 话题介绍](#11-话题介绍)
		- [什么是文件](#什么是文件)
		- [文件在开发中扮演什么角色](#文件在开发中扮演什么角色)
		- [文件有哪些操作](#文件有哪些操作)
		- [文件读写操作](#文件读写操作)
	- [1.2 基础需求](#12-基础需求)
		- [Task N： 快速写文件](#task-n-快速写文件)
		- [Task N：多次写入](#task-n多次写入)
		- [Task N：缓冲写入](#task-n缓冲写入)
		- [Task N：快速文件读取](#task-n快速文件读取)
		- [Task N：分段文件读取](#task-n分段文件读取)
	- [1.3 文件I/O细节](#13-文件io细节)
		- [Task N：按行读取文本文件](#task-n按行读取文本文件)
		- [读取文件的部分](#读取文件的部分)
			- [Task N：读取前N个字节](#task-n读取前n个字节)
			- [Task N：读取第M字节到第N字节](#task-n读取第m字节到第n字节)
			- [Task N：读取最后N个字节](#task-n读取最后n个字节)
		- [文本文件](#文本文件)
			- [Task N：读取前N行](#task-n读取前n行)
			- [Task N：读取第M行到第N行](#task-n读取第m行到第n行)
			- [Task N：读取最后N行](#task-n读取最后n行)
		- [Task N：添加文件内容](#task-n添加文件内容)
	- [1.4 文件读写进阶](#14-文件读写进阶)
		- [文件编码与unicode](#文件编码与unicode)
			- [Task N：读取GBK编码文件](#task-n读取gbk编码文件)
			- [Task N：写入GBK编码文件](#task-n写入gbk编码文件)
		- [表格化数据格式：CSV](#表格化数据格式csv)
			- [Task N：读取CSV格式文件](#task-n读取csv格式文件)
		- [结构化数据文件格式：JSON](#结构化数据文件格式json)
			- [Task N：读取JSON文件](#task-n读取json文件)
		- [图像数据文件：Image](#图像数据文件image)
			- [Task N：写入png文件](#task-n写入png文件)
			- [Task N：读取png文件](#task-n读取png文件)
		- [压缩文件读写](#压缩文件读写)
			- [Task N：读取gzip文件](#task-n读取gzip文件)
			- [Task N：写入gzip文件](#task-n写入gzip文件)
		- [配置文件](#配置文件)
	- [1.5 其他文件操作](#15-其他文件操作)
		- [Task N：文件信息查询](#task-n文件信息查询)
		- [Task N：新建文件](#task-n新建文件)
			- [新建文件](#新建文件)
			- [新建文件夹](#新建文件夹)
			- [综合起来](#综合起来)
		- [Task N：删除文件](#task-n删除文件)
		- [Task N：重命名和移动](#task-n重命名和移动)
		- [Task N：复制文件](#task-n复制文件)
		- [Task N：修改文件状态](#task-n修改文件状态)
		- [Task N：搜索](#task-n搜索)
		- [Task N：同步](#task-n同步)
		- [Task N：文件备份](#task-n文件备份)
	- [1.6 文件I/O的应用实例](#16-文件io的应用实例)
		- [Task N：古腾堡书籍的预处理](#task-n古腾堡书籍的预处理)
			- [第1步，下载书籍](#第1步下载书籍)
			- [第2步，观察书籍内容](#第2步观察书籍内容)
			- [第3步，确定任务内容](#第3步确定任务内容)
			- [第4步，扫描文件，过滤掉开头和结尾](#第4步扫描文件过滤掉开头和结尾)
	- [1.7 扩展话题：相关第三方库赏析](#17-扩展话题相关第三方库赏析)

## 1.1 话题介绍

上一章讲了Go语言最核心的话题：并发。了解了并发以后，我们可以正式进入开发流程了。
我们的第一站话题是文件。

也许你会问，我们不是要做个app吗，为什么不先讲WEB开发，或者数据库之类的操作，而是文件呢？答案其实很无奈，和任何数据科学一样，我们要先准备数据。作为一个阅读应用，书籍本身是其最重要的内容。我们的后台数据库刚开头空空如也，嗷嗷待哺，希望获取大量的数据。但书籍不可能从天而降，我只能到网上去找。而我找来的书籍数据，都是文本格式的文件。比如：

- [Project Guttenburg](https://www.gutenberg.org/) - 世界上最大的免费电子书库，提供了超过58,000本免费电子书，其中大部分都是公版的。
- [青空文庫](https://www.aozora.gr.jp/) - 最大的日文免费电子书库，它的称号是“インターネットの電子図書館、青空文庫”（英特网的电子图书馆）。它收录了15,176本电子书，其中公版书14875。
- [最全中华古诗词数据库](https://github.com/chinese-poetry/chinese-poetry) - 最大的中华古诗词库5.5万首唐诗，26万宋诗，21050首词

这几个网站上的数据都是文件，古腾堡和青空文库是txt文本，古诗词数据库是JSON格式的数据文件。要做成app可用的数据，我得先把他们下载过来，再用程序处理成统一的格式，然后导入到PostgreSQL数据库中，然后才可以考虑用Go语言去读取数据库，喂给前端APP。

处理文件的这一步就是数据预处理。在大部分的数据研究和工程中，数据预处理都是最最最最令人头疼的。因此为了避免长期头疼，我就得先准备好对付它的技术。毕竟工欲善其事必先利其器嘛。所以正式开做APP之前，先要用Go语言把文本处理好，而这个过程遇到的问题和需求，就记录在本章了。

当然，为了读者们阅读流畅，我还是把本章的主题按照难易度循序渐进地排列，分理成不同的任务（Task），分别讨论。当然，在任务讨论之间，我会举例它在NeoReads里是怎么应用的，并指向实际代码。

### 什么是文件

我们每天都在处理文件。办公室里，每天工作的内容是打开office，阅读word文档，或者编辑Excel，或者修饰ppt，或者发送Email。当然，最后要记得保存。这些都是文件。Word文档和Email是格式化的文本文件，Excel是表格化的数据文件，PPT是包含图片、视频的多媒体文件。而程序员们的日常则往往是打开某个IDE，编写代码，阅读文档，运行程序，发现Bug，修改Bug。代码是纯文本文件，在线文档常常是格式化的HTML文件，可执行的程序是二进制文件，而调Bug，往往要靠看输出日志文件来查找问题，并用gdb等debug工具来分析coredump。

甚至可以说，人们工作的大半内容都在处理文件。计算机作为人的双手的延伸，处理的最多的也是文件。代码、程序本身即是文件，操作系统的命令几乎都是处理文件的，日志是文件，图片、视频是文件，游戏数据都是存在二进制文件中的，如FlatBuffers。很多计算分析用的数据，也是文件存储形式，比如金融数据等。就连数据库和大数据，其实也是间接地操作文件。基本上，不管你是哪一行的程序员，每天都躲不开文件操作。

### 文件在开发中扮演什么角色

因此文件操作是几乎所有编程语言书籍都绕不开的入门话题之一。入门书在介绍标准库的时候，基本上第一个章节，就是文件I/O。实际上，Hello World也是I/O例程，和文件I/O的差别只是把文字写到了stdout标准输入，而没写到本地文件而已。在操作系统看来，stdout也是一个文件句柄。

### 文件有哪些操作 

在我们的日常开发中，文件、内存、数据库是最常见的操作对象。而内存中的操作，比如数值运算、数组、指针等，大部分入门书籍都会浓墨重彩地描述，而文件操作，则往往浅尝辄止。本书出于定位和实际应用的考虑，把文件处理的内容着重细化，尽量覆盖到应用的方方面面，做成开篇的重点章节之一。

文件处理这个话题，其实内容非常丰富。如果把文件当做一个整体，可以进行的操作有增、删、查、改，另外还可以复制、粘贴、重命名等。而且文件夹也是一种特殊的文件，因此对文件夹，以及因此组合起来的文件树，我们还可以进行递归操作，以及文件夹同步等复杂操作。

### 文件读写操作

这些操作里，最常用，也最丰富的内容是文件的读写。我们常常读写的都是哪些文件？纯文本、二进制数据、CSV日志、JSON、XML、HTML、压缩文件、图像文件、protobuf，等等等等。我们不可能在本书中逐一介绍全部的文件格式，否则这就成了文件格式大全，而不是文件操作的章节了。但大致观察出几个类别，对其相关的文件读写做一番介绍，剩下的，大可以举一反三。比如了解了JSON文件的读写，再去处理XML时，只要查找一下XML格式的读写库文档，就不会太难。

从处理方式来区分，文件大致可以分为如下几类：

1. 纯二进制文件：读写的单位是`[]byte`字节数组，了解最基本的文件读写例程。
2. 纯文本文件：读写的单位一般是行，了解如何处理换行符。
3. 格式化二进制文件：如protobuf、图像文件等，需要按照文件二进制的格式去读写。
4. 表格式文本文件：如CSV
5. 格式化文本文件：如JSON、XML、HTML等
6. 其他辅助文件格式：如压缩文件

这些就是我们本章要介绍的各类对象。我们会逐一观察它们的需求，并选取其中一些典型的案例，探讨和实现其解决方案。通过这些了解，再遇到类似的文件，大概就可以触类旁通了。

本章一开始先回顾一下文件读写的基本操作，这里涉及到的话题基本都是Go语言标准库处理到的。

之后，对于上述6种文件类型的详细需求，我们会结合日常工作的实际情况举例分析。如CSV文件常用在科学计算或者金融数据的处理上，我们就找一些这方面的示例。

其中有的复杂功能，用到了第三方库，比如protobuf等，我们会在最后一节的扩展话题里单独进行赏析和讨论。

## 1.2 基础需求

首先我们先回顾一下Go语言提供的基础文件读写功能。虽然一般入门书籍基本上都已经介绍过这个话题，但作为后面文复杂件I/O应用的基石，我们还是先过一遍。顺便也准备好后面用得到的示例文件。

### Task N： 快速写文件

我们先用最简单的办法来写入文件：

```go
package main

import "io/ioutil"

func main() {
	// 准备文本
	text := `关雎 
关关雎鸠，在河之洲。
窈窕淑女，君子好逑。
参差荇菜，左右流之。
窈窕淑女，寤寐求之。
求之不得，寤寐思服。
悠哉悠哉，辗转反侧。
参差荇菜，左右采之。
窈窕淑女，琴瑟友之。
参差荇菜，左右芼之。
窈窕淑女，钟鼓乐之。`

	// 将文本转为二进制数据
	data := []byte(text)

	// 使用方便函数ioutil.WriteFile直接写入文件
	ioutil.WriteFile("D:/guanju.utf8.txt", data, 0644)
}

```

这里使用了`ioutil.WriteFile`，这是Go语言标准库提供的方便函数，一般只在文件内容很短的时候一次性使用。比如我们现在的示例，就符合这个场景。但是任何更复杂的场景，都**不应当**使用这个函数。

`ioutil.WriteFile()`的第三个参数是`FileMode`，使用UNIX文件权限编码。这里`0644`开头的0表示八进制数字。

如果要写入更大的文件，显然一次写入会占用大量内存，那样就需要多次写入了。

### Task N：多次写入

```go
package main

import "os"

func main() {
	// 准备文本
	text := []string{
		"Sonnet 18 by William Shakespeare\n",
		"Shall I compare thee to a summer's day?\n",
		"Thou art more lovely and more temperate:\n",
		"Rough winds do shake the darling buds of May,\n",
		"And summer's lease hath all too short a date:\n",
		"Sometime too hot the eye of heaven shines,\n",
		"And often is his gold complexion dimm'd;\n",
		"And every fair from fair sometime declines,\n",
		"By chance or nature's changing course untrimm'd;\n",
		"But thy eternal summer shall not fade\n",
		"Nor lose possession of that fair thou owest;\n",
		"Nor shall Death brag thou wander'st in his shade,\n",
		"When in eternal lines to time thou growest:\n",
		"So long as men can breathe or eyes can see,\n",
		"So long lives this and this gives life to thee.",
	}

	// 创建文件
	file, err := os.Create("D:/sonnet18.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	// 逐行写入
	for _, line := range text {
		file.WriteString(line)
		// 或者
		// file.Write(string(line))
	}
}

```

这里使用的文本是莎士比亚154首十四行诗中的第十八首，讲的是莎翁将这首诗献给爱人，“只要这首诗还在流传，你的美就会永存”。可以参考维基百科<https://en.wikipedia.org/wiki/Sonnet_18>或百度百科<https://baike.baidu.com/item/sonnet%2018>的介绍。

这里我们不再一次性写入整个文本，而是把文本分解成一行行，再分别写入。当然，作为示例，我们一次性准备好了全部数据，然后再遍历写入。再很多实际场景中，数据是从其他来源分步取过来的（例如数据库、HTTP请求，或者分布式消息队列），这时候就不用都存到内存中，而是来一个写一个，这样就大大节省了内存占用。

这里其实还有一个问题，就是直接使用`file.WriteString()`，或者`file.Write(b []byte)`的话，每次写入都会调用一次磁盘I/O操作，所以如果每次写入的数据特别小（例如我们这里给出的例子中一行诗歌就太短了），写入次数特别多的话，会浪费大量的I/O资源，性能较差。实际上，如果是这种情况，应当使用缓冲区写入`bufio`。

### Task N：缓冲写入

```go
package main

import (
	"bufio"
	"os"
)

func main() {
	// 准备文本
	text := []string{
		"Sonnet 18 by William Shakespeare\n",
		"Shall I compare thee to a summer's day?\n",
		"Thou art more lovely and more temperate:\n",
		"Rough winds do shake the darling buds of May,\n",
		"And summer's lease hath all too short a date:\n",
		"Sometime too hot the eye of heaven shines,\n",
		"And often is his gold complexion dimm'd;\n",
		"And every fair from fair sometime declines,\n",
		"By chance or nature's changing course untrimm'd;\n",
		"But thy eternal summer shall not fade\n",
		"Nor lose possession of that fair thou owest;\n",
		"Nor shall Death brag thou wander'st in his shade,\n",
		"When in eternal lines to time thou growest:\n",
		"So long as men can breathe or eyes can see,\n",
		"So long lives this and this gives life to thee.",
	}

	// 创建文件
	file, err := os.Create("D:/sonnet18.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	// 新建一个带缓冲区的bufio.Writer
	w := bufio.NewWriter(file)
	// 逐行写入
	for _, line := range text {
		w.WriteString(line)
	}

	// 强制flush缓冲区
	w.Flush()
}
```

可以看到，使用缓冲区写的话，其实就是在`os.Writer`之后再套一层`bufio.NewWriter`，其他的操作基本相同。需要注意的是，`bufio.Writer`写入数据时并不是直接写到文件，而是先写到一个内存缓冲区，等缓冲区满了之后再刷到硬盘上。我们示例中数据量较小，所以必须在最后加一句`w.Flush()`，否则数据没有刷到硬盘上程序就结束了，会发现文件是空的。

至此我们准备好两个文件：sonnet18.txt，guanju.utf8.txt。下面看看怎么读取他们。

### Task N：快速文件读取

Go语言提供了一个方便函数，`ioutil.ReadFile()`，可以直接读取文件内容，得到的是一个`[]byte`数组：

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {

	// 读取文件内容
	buf, err := ioutil.ReadFile("D:/sonnet18.txt")

	// 错误处理
	if err != nil {
		panic(err)
 	}

	// 使用内置的string()函数将buf数组转换成字符串打印
	fmt.Println(string(buf))
}
```

这个函数很方便，直接调用就可以读取整个文件内容。但是这种使用方式只适合小文件，文件较大之后，考虑到内存占用问题，如果我们读取文件只是需要遍历查询，而不需要整个文件内容都读出来，就可以进行分段读取。

### Task N：分段文件读取

Go语言提供的标准的文件读取方式是这样的：

```go
package main

import (
    "fmt"
    "io/ioutil"
)

func main() {

	// 打开文件
	f, err := os.Open("D:/sonnet18.txt")
	// 错误处理
	if err != nil {
		panic(err)
 	}
	defer f.Close()
	
	// 新建一个缓冲buf
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
	f, err := os.Open("D:/sonnet18.txt")
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

在实际使用中，这样用缓冲数组读取文件内容的方法还是太过底层了。不论我们的文件是格式化的二进制，还是文本文件，都需要进一步处理。格式化的二进制文件进一步如何处理，和其文件具体格式直接关联，因此我们不在这里作通论，而是在后面讨论具体的格式时再详谈，如protobuf格式等。而文本文件，则不管是什么格式，一般都要处理分隔符（最常见的是换行符`'\n'`），因此我们会在后面详细讨论文本按行读取这个最常见的需求。

## 1.3 文件I/O细节

我们在日常开发的过程中，使用文件读写时，实际的需求往往不同于上节讲到的整个文件一次读入这种简单场景。例如：100G的大文件，如何实现读取而不撑爆内存？或者我们需要按行来读入文件，而不是按固定长度来读取？作者在本节中尝试列举几种常见的文件读写场景，并寻找合适的解决方案。

### Task N：按行读取文本文件

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

### 读取文件的部分


很多时候，我们并不需要读取整个文件，而是只需要查看其中的一部分即可。这里有几个常见的需求场景：

1. 读取文件开头的一部分
2. 读取文件中间的一部分
3. 读取文件结尾的一部分

#### Task N：读取前N个字节

首先，假设我们需要读取长度为N个字节的数据。

那么对于需求一，如果N并不大，可以用一个`[]byte`数组存放，则非常简单，我们只需要新建一个大小为N的`[]byte`数组，然后直接使用`f.Read()`读取到数组满为止即可。

```go
package main

import (
	"fmt"
	"os"
)

func check(e error) {
	if e != nil {
		panic(e)
	}
}

func main() {
	f, err := os.Open("D:/sonnet18.txt")
	check(err)

	N := 10
	b := make([]byte, N)
	n, err := f.Read(b)
	check(err)
	fmt.Printf("read %d bytes: [%s]\n", n, string(b))
}

```

上述代码打印出来的结果是：

```
read 10 bytes: [Sonnet 18 ]
```

如果N比较大，则需要多次读取，一直读到足够N字节即可：

```go
package main

import (
	"fmt"
	"os"
)

func check(e error) {
	if e != nil {
		panic(e)
	}
}

func main() {
	f, err := os.Open("D:/sonnet18.txt")
	check(err)

	N := 100

	b := make([]byte, 10)
	nRead := 0
	for {
		n, err := f.Read(b)
		if n == 0 {
			break;
		}
		check(err)
		// 记录总共读了多少字节
		nRead += n
		// 如果还没足够N，处理掉当前读到的数据，并继续
		if nRead < N {
			// 处理当前的一段
			fmt.Printf("read %d bytes: [%s]\n", n, string(b))
		} else { // 足够N了，处理掉最后读到的一小段数据（直到N为止），然后跳出
			// 处理最后读到的一小段
			nTail := n - (nRead - N)
			fmt.Printf("read %d bytes: [%s]\n", nTail, string(b[:nTail]))
			break
		}
	}
}
```

这里我们假设的N是100，缓冲数组`b`大小设为10，这样，`for`循环里一共读取了10次，最后一次时判断`nRead < N`失效，退出循环。

#### Task N：读取第M字节到第N字节

对于需求二，读取中间部分的数据，假设我们需要读取文件从第M字节到第N字节的数据，则可以使用`Seek()`函数跳转到文件第M个字节，再连续读到第N个字节即可。这里示例是N-M很大，需要多次读取的情况：

```go
package main

import (
	"fmt"
	"io"
	"os"
)

func check(e error) {
	if e != nil {
		panic(e)
	}
}

func main() {
	f, err := os.Open("D:/sonnet18.txt")
	check(err)

	N := 100
	M := 200

	// 跳转到第N个字节
	f.Seek(int64(N), io.SeekStart)

	// L: 需要读取的长度
	L := M - N

	b := make([]byte, 10)
	nRead := 0
	for {
		n, err := f.Read(b)
		if n == 0 {
			break
		}
		check(err)
		// 记录总共读了多少字节
		nRead += n
		// 如果还没足够N，处理掉当前读到的数据，并继续
		if nRead < L {
			// 处理当前的一段
			fmt.Printf("read %d bytes: [%s]\n", n, string(b))
		} else { // 足够N了，处理掉最后读到的一小段数据（直到N为止），然后跳出
			// 处理最后读到的一小段
			nTail := n - (nRead - L)
			fmt.Printf("read %d bytes: [%s]\n", nTail, string(b[:nTail]))
			break
		}
	}
}
```

这里我们从第100个字节开始读取，读到第200个字节，即`N:=100;M:=200`，首先使用`f.Seek()`跳转到第N个字节，然后和上个例子一样，再读取`L:=M-N`个字节即可

#### Task N：读取最后N个字节

对于需求三，我们则可以先使用`Seek()`从文件末尾往前跳跃N个字节，再从那里一直读到文件末尾：

```go
package main

import (
	"fmt"
	"io"
	"os"
)

func check(e error) {
	if e != nil {
		panic(e)
	}
}

func main() {
	f, err := os.Open("D:/sonnet18.txt")
	check(err)

	N := 100

	// 跳转到倒数第N个字节
	f.Seek(-int64(N), io.SeekEnd)

	b := make([]byte, 10)
	nRead := 0
	for {
		n, err := f.Read(b)
		if n == 0 {
			break
		}
		check(err)
		// 记录总共读了多少字节
		nRead += n
		// 如果还没足够N，处理掉当前读到的数据，并继续
		if nRead < N {
			// 处理当前的一段
			fmt.Printf("read %d bytes: [%s]\n", n, string(b))
		} else { // 足够N了，处理掉最后读到的一小段数据（直到N为止），然后跳出
			// 处理最后读到的一小段
			nTail := n - (nRead - N)
			fmt.Printf("read %d bytes: [%s]\n", nTail, string(b[:nTail]))
			break
		}
	}
}

```

我们这里使用了`f.Seek(-int64(N), io.SeekEnd)`来从文件末尾`io.SeekEnd`，跳转`-N`个字节，即倒数第N个字节，然后一路读到末尾。

至此读取文件的一部分，三种场景我们都覆盖到了。

除了`Seek()`，Go还提供了`Reset()`和`Peek()`等文件I/O操作，这样组合起来可以解决更复杂的场景。如：TODO

### 文本文件

类似的，对于文本文件，如果我们的场景是按行读取的话，那么这几个需求就变为：

1. 读取文件开头的N行
2. 读取文件从第M行开始到第N行结束的内容
3. 读取文件最后N行的内容

有了`bufio.Scanner`，前两个功能可以很容易实现。要读取文件开头N行，只要`Scan()`到第N行之后退出读取循环即可；而要读取第M行到第N行，则在读取前M行时，跳过去忽略掉其内容即可。

#### Task N：读取前N行

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

#### Task N：读取第M行到第N行

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

#### Task N：读取最后N行

而读取最后N行的内容则更复杂一点：在读完整个文件之前，无法知道总共有多少行，因此也无法知道应当从第几行算起。如果不考虑效率，可以利用一个大小为N的FIFO对列，把读到的每一行都加入对列。当超过N行时，把最早的内容从队列中取出来抛弃掉。这样读完整个文件时，对列里剩下的N行内容实际上就是文件的最后N行。示例如下：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {
	// 读取最后5行
	N := 5

	// 先建立一个大小为0的对列
	queue := make([]string, N)

	file, err := os.Open("D:/sonnet18.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	scanner := bufio.NewScanner(file)

	// 逐行扫描全文
	for scanner.Scan() {
		// 将当前行加入到对列
		queue = append(queue, scanner.Text())
		// 如果对列长度超过N，则踢出最早进入对列的一行
		if len(queue) > N {
			queue = queue[1:]
		}
	}

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}

	// 全文扫描结束，从queue中读取最后N行进行处理
	for _, line := range queue {
		fmt.Println(line)
	}
}

```

但这种简单的办法显然效率不高。一方面，全文都要读一遍，如果遇到大文件，则前面的读取扫描过程都浪费了；另一方面，这个示例使用简单的`slice`来模拟FIFO对列。由于Go标准库里没有对列数据结构，老版的`container/vector`也已经删除，所以为了简单起见，这里使用模拟的对列。但这个方法实际效率很低，不管是内存的使用还是GC的消耗都很高。所以会更进一步降低性能。

在实际应用中，要高效地读取最后N行，我们应当模仿UNIX系统的`tail`命令来实现这个功能，即先利用`Seek()`跳转到文件结尾，然后用缓冲区从结尾往前读取，每次读取一个缓冲区，并分析其内容，如果发现换行符，则得到了一行文本。这样反复往前读，直到读到了N行文本为止。这里代码需要处理缓冲区到文本行的转换，实际上是要自己实现`bufio`的内部细节内容。

因此我么可以参考第三方开源库`tail`（<https://github.com/hpcloud/tail>），它实现了完整的tail功能，而且还实现了类似`tail -f`的实时跟踪文件新写入内容的功能。我们会在最后一节“扩展话题”里专门研究这个第三方库的设计和实现。

### Task N：添加文件内容

在向文件写入时，如果不想覆盖已有文件，而是在其后添加内容，则可以使用`O_APPEND`模式打开文件，再写入文件内容：

```go
package main

import "os"

func main() {
	f, err := os.OpenFile("D:/append.txt", os.O_CREATE|os.O_APPEND|os.O_WRONLY, 0644)
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

这个程序如果多运行几次，就会发现`D:/append.txt`的内容不断增加了。

OK，到此为止我们看到了Go语言文件基本读写的功能。下一节我们会详细讨论文件读写的各种进阶应用场景。这些场景，是我们日常开发中常常遇到的。

## 1.4 文件读写进阶

到现在我们已经很熟悉Go语言文件读写的基本操作了。那么对于文件读写，还有哪些平时常用的需求呢？除了基本的二进制和纯文本读写之外，还会遇到几种实践情况：

1. 非unicode编码。前面我们处理的都是标准编码的文件，但是现实中接触到很多文件都是非标准编码的，比如GBK等，这需要单独的处理。
2. 超大文件。
3. 压缩文件。现实中，很多大文件数据，例如日志、金融数据等，既需要日常积累和计算，又需要长期存储，那么直接读写压缩文件就是很常见的需求了。幸好Go语言的I/O有非常方便的类管道机制，我们只要在正常的读写之上再加上一层压缩功能既可。
4. 内存映射。有更高I/O性能需求的时候，内存映射是一个好方法。另外，内存映射还能实现进程间通讯。所以了解Go语言如何处理内存映射，也是必要的。
5. 并发读写。这个话题因为涉及到Go语言最擅长的并发功能，情况也比较复杂，我们单独在下一节讲述。

下面分别描述上述的问题。

### 文件编码与unicode

Go语言默认的文件编码是`UTF-8`，如果需要打开其他编码的文件，比如GBK，需要使用`encoding`模块。Go语言官方提供的编码模块在<golang.org/x/text/encoding>，这个模块将来可能会加入到标准库中。

#### Task N：读取GBK编码文件

我们先看看如何读取`GBK`编码的文本文件：

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
	// 打开文件
	f, err := os.Open("D:/guanju.gbk.txt")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	// 使用GBK编码
	enc := simplifiedchinese.GBK
	// 建立一个GBK编码的转换器Reader
	r := transform.NewReader(f, enc.NewDecoder())

	// 经过转换之后，就可以当做普通UTF-8文本来进行按行读取了
	sc := bufio.NewScanner(r)
	for sc.Scan() {
		fmt.Printf("Read line: %s\n", sc.Bytes())
	}
	if err = sc.Err(); err != nil {
		panic(err)
	}
}

```

#### Task N：写入GBK编码文件

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

// 把标准编码UTF8的数据，转化成GBK编码
func encodeGBK(s []byte) ([]byte, error) {
	reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewEncoder())
	d, e := ioutil.ReadAll(reader)
	if e != nil {
		return nil, e
	}
	return d, nil
}

// 读取GBK编码的数据，转化成标准编码UTF8
func decodeGBK(s []byte) ([]byte, error) {
	reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewDecoder())
	d, e := ioutil.ReadAll(reader)
	if e != nil {
		return nil, e
	}
	return d, nil
}

func main() {

	s := "你好，明天！"
	// 先转化为GBK编码
	gbk, err := encodeGBK([]byte(s))
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(string(gbk))
	}

	// 再转回成GBK
	utf8, err := decodeGBK(gbk)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(string(utf8))
	}
}
```

这里只是内存中转换，下面是一个文件转码的示例，我们将guanju.utf8.txt转码为GBK编码，存入到guanju.gbk.txt中：


```go
package main

import (
	"bufio"
	"bytes"
	"fmt"
	"io/ioutil"
	"log"
	"os"

	"golang.org/x/text/encoding/simplifiedchinese"
	"golang.org/x/text/transform"
)

// 把标准编码UTF8的数据，转化成GBK编码
func encodeGBK(s []byte) ([]byte, error) {
	reader := transform.NewReader(bytes.NewReader(s), simplifiedchinese.GBK.NewEncoder())
	d, e := ioutil.ReadAll(reader)
	if e != nil {
		return nil, e
	}
	return d, nil
}

func main() {

	// 打开UTF-8文件
	f, err := os.Open("D:/guanju.utf8.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer f.Close()

	// 新建GBK文件
	fout, err := os.Create("D:/guanju.gbk.txt")
	if err != nil {
		panic(err)
	}
	defer fout.Close()
	// 新建输出bufio.Writer
	bw := bufio.NewWriter(fout)
	defer bw.Flush()

	scanner := bufio.NewScanner(f)
	// 逐行读取
	for scanner.Scan() {
		line := scanner.Text()
		// 将当前行转换为GBK编码
		gbkBytes, err := encodeGBK([]byte(line))
		// 把GBK编码的数据写出到GBK文件中去
		if err != nil {
			fmt.Println(err)
		} else {
			bw.Write(gbkBytes)
			bw.WriteString("\n")
		}
	}

	if err := scanner.Err(); err != nil {
		panic(err)
	}

}

```

### 表格化数据格式：CSV

Go标准库提供了"encoding/csv"模组，参考https://golang.org/pkg/encoding/csv/


#### Task N：读取CSV格式文件

最简单的CSV读取，只需要在普通文本读取之上，套上一个`csv.Reader`就可以了：

```go
package main

import (
	"bufio"
	"encoding/csv"
	"fmt"
	"io"
	"os"
)

func main() {
	// 打开CSV文件
	f, err := os.Open("D:/test.csv")
	if err != nil {
		panic(err)
	}

	// 建立csv.Reader
	r := csv.NewReader(bufio.NewReader(f))
	for {
		record, err := r.Read()
		// 遇到EOF跳出
		if err == io.EOF {
			break
		}
		// 打印record，它是一个[]string数组
		fmt.Println(record)
		// 遍历打印其中每一个元素
		for value := range record {
			fmt.Printf("  %v\n", record[value])
		}
	}
}
```

这里每行读出来的`record`是一个字符串数组`[]string`，其中每一项是一列值。这样虽然简单，效率也高，但是遇到稍微复杂一点的情形，就会感觉不好用。

因为每一行是一个数组，只能通过下标来获取对应列的值，如`record[1]`，如果遇到CSV文件中某一列值有缺失，会导致直接错误。

而且使用下标的话，未来如果CSV格式发生了改变，现有的下标很可能需要跟着改变，比如插入一列的情形。这样的话，很不利于代码维护。

因此我们需要能把每一行数据`record`映射成一个`struct`的功能。我找到两个第三方库提供了如下功能：

- [csvutil](https://github.com/jszwec/csvutil)
- [go-csv-tag](https://github.com/artonge/go-csv-tag)

他们提供的核心服务都是利用`tag`来标记`struct`数据与CSV列之间的对应关系，然后就可以轻松地实现读写。

这两个库中，`csvutil`功能较多，结构也复杂一些。`go-csv-tag`则只用了一两百行代码，方便我们去理解。因此我这里举例用的是`go-csv-tag`，但是实际使用中，大家可以根据需求选用`csvutil`库.

假设CSV数据如下：

```csv
name, ID, number
name1, 1, 1.2
name2, 2, 2.3
name3, 3, 3.4
```

则映射和读取的代码如下：

```go
type Demo struct {                         // 带标记的struct，每个字段指定对应列
	Name string  `csv:"name"`
	ID   int     `csv:"ID"`
	Num  float64 `csv:"number"`
}

tab := []Demo{}                             // 新建内存中的对象数组
err  := csvtag.Load(csvtag.Config{          // 读取CSV文件
  Path: "file.csv",                         // 文件名
  Dest: &tab,                               // 目标对象数组
  Separator: ',',                           // 可选：如果分隔符不是','可以用这个设置
  Header: []string{"name", "ID", "number"}, // 可选：如果有文件头，则在这里指定
})
```

可以看到，通过库的封装，读取csv文件，并映射成`struct`对象，变成了很简单的事情。

### 结构化数据文件格式：JSON

JSON和XML这两种数据格式的处理，我们在上一章中已经有详细描述。作为通用数据交换格式，他们的功能很强大，表达力极强，因此也经常用做配置文件、数据文件等。这类文件实质上是文本文件，但是一般来说不按行处理，通常有两种处理方式：

* DOM: Document Object Model
* SAX: Simple API for XML

DOM是指把整个文档看做一个结构化的对象（一般是一个树状结构），读取文件的过程中逐步构建这个对象，添加其子节点。因此整个文件读完之后，内存中的对象会包含文件的全部信息。它的优点是内存中的DOM和文件中数据的结构一一对应，使用简单；并且DOM建立好之后，可以多次访问DOM的任意子节点。但缺点也很明显，内存消耗巨大，如果数据文件本身很大，则不能使用DOM模式。

SAX原先是为了XML而设计的一个处理方式，JSON由于实际上结构和XML类似，所以也可以采用这种模式来读取分析。SAX把整个文件看做一个信息流，在读取文件的过程中，遇到每一个节点，会回调相应的节点事件，处理代码事先注册好回调的逻辑，即可以指定遇到哪一种节点的时候做什么事情。SAX的优点是速度快（只遍历一遍文档，在关键节点获取信息进行操作，而不需要构造DOM），内存消耗少（只存储当前节点的环境信息），但是缺点是一遍过，而且回调事件零散，失去了数据的结构信息，需要在处理代码里自己想办法维护。

#### Task N：读取JSON文件

Go语言标准库`json`包和`xml`包都提供了`Decoder`和`Encoder`的实现，我们先举一个简单的例子：

```go
package main

import (
	"encoding/json"
	"io"
	"os"
)

type book struct {
	ID     int64
	Title  string
	Author string
	Intro  string
}

func main() {
	f, err := os.Open("D:/books.json")
	if err != nil {
		panic(err)
	}
	defer f.Close()
	dec := json.NewDecoder(f)
	enc := json.NewEncoder(os.Stdout)
	var books []book
	if err := dec.Decode(&books); err != nil {
		if err == io.EOF {
			return
		}
		panic(err)
	}
	for _, v := range books {
		if err := enc.Encode(&v); err != nil {
			panic(err)
		}
	}
}

```

这里使用的文件`books.json`格式如下：

```json
[
	{
		"id":1,
		"title":"On Writing Well",
		"author":"William Zinsser",
		"intro":"A book on how to become a good professional writer."
	},
	{
		"id":2,
		"title":"Elements of Style",
		"author":"William Strunk Jr./E.B. White",
		"intro":"A definitive guide to writing styles"
	}
]
```

XML的解析和JSON基本一致，这里就不赘述了。

可以看出，Go语言标准库的json解析包，虽然支持文件流，但实际上还是DOM模式，读完文件之后，全部的信息都存放到`books`对象中去了。因此如果`books.json`非常大，内存消耗会很严重。

`encoding/json`还提供了一套`streaming API`，即流模式API，可以让我们手动解析一个个Token，并在当前位置进行解析。如果使用这个API来解析`books.json`，每遇到一个`book`对象处理一次，可以这么做：

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type book struct {
	ID     int64
	Title  string
	Author string
	Intro  string
}

func main() {
	f, err := os.Open("D:/books.json")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	d := json.NewDecoder(f)

	// 跳过"{"
	_, err = d.Token()
	if err != nil {
		panic(err)
	}

	// 一个个解析book
	var b book
	for d.More() {
		err := d.Decode(&b)
		if err != nil {
			panic(err)
		}
		fmt.Println(b.Title)
	}

	// 跳过"}""
	_, err = d.Token()
	if err != nil {
		panic(err)
	}

}

```

这样做的好处是内存中不需要存储全部的`books`了，因此如果`books.json`内容数量很多时，对内存没有多余的消耗。这种模式在进行遍历统计时比DOM模式好用。但是这样仍然不如SAX模式方便，因为我们不但必须明确知道JSON的结构，并手动解析到需要到达的目标，并且，如果JSON文件数据结构层次比较多，就很难写出可以维护的代码了。这时候我们更需要SAX的事件驱动模式。

但经过作者的初步调查，JSON中并没有实现SAX模式，只能用流模式API的`Token()`来模拟。 假设我们的需求是遍历`books.json`统计每位作者有多少书，那么在SAX回调中，我们只要关注`author`字段的内容即可：

```go
package main

import (
	"encoding/json"
	"fmt"
	"io"
	"os"
)

type book struct {
	ID     int64
	Title  string
	Author string
	Intro  string
}

func main() {
	f, err := os.Open("D:/books.json")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	d := json.NewDecoder(f)
	level := 0
	var prev string
	for {
		t, tokenErr := d.Token()
		if tokenErr != nil {
			if tokenErr == io.EOF {
				break
			}
		}

		switch t.(type) {
		case json.Delim:
			ts := t.(json.Delim).String()
			// 判断层级
			if ts == "{" || ts == "[" {
				level++
			}
			if ts == "}" || ts == "]" {
				level--
			}
		default:
			// 我们的需求：获取级别2的"author"对应的值
			if prev == "author" && level == 2 {
				fmt.Println(t)
			}
		}

		// 记录键，注意这里凡是字符串类型都记录了，所以必须配合级别来避免错误
		switch t.(type) {
		case string:
			prev = t.(string)
		}
	}
}
```

可以看到，这里只是简单的模拟了一个搜索的过程，当我们读到某个字段，它的层级是`level==2`，且它前一个`Token`是`"author"`时，进行处理。

这样写显然容易出错，而且很冗长，但是效率确实是最高的（因为避免了绝大多数的`Decode()`过程。以后在正式章节中我会重写一个streamparser的API，来封装这个功能，实现类似上一章中介绍过的[jsonparser](https://github.com/buger/jsonparser)的效果。

第三方库[jstream](https://github.com/bcicen/jstream)，提供了另外一种形式的流模式读取：分层读取。比如我们的`books.json`，是分为两层，第一层是`[]`数组，第二层则是各个`book`对象。如果`book`对象的某个属性值仍然是数组或对象，那么那个值之内的数据就是第三层，依次类推。这在某些统计需求时会比较好用，可以按需使用。


最后，值得一提的是，标准库的`encoding/json`效率并不很高。第三方库[json-iterator](https://github.com/json-iterator/go)提供了与标准库100%兼容的功能，但是性能高很多。实际工作中，在对性能要求很高的场景，可以考虑使用这个库来替换标准库。

### 图像数据文件：Image

关于图像的处理，我们会在后面图像处理的章节专门详细描述。这里先举一个写入和读取图片文件的例子，作为文件操作的示例。

#### Task N：写入png文件

先看看写入png文件：

```go
package main

import (
	"image"
	"image/color"
	"image/draw"
	"image/png"
	"os"
)

func main() {
	// 画一个大小为400x400的正方形
	size := 400
	step := size / 10
	imgRect := image.Rect(0, 0, size, size)
	img := image.NewNRGBA(imgRect)

	// 画出边框
	draw.Draw(img, img.Bounds(), &image.Uniform{color.White}, image.ZP, draw.Src)

	// 填充黑色
	for x := 0; x < size; x += step {
		// 奇数块涂上阿根廷国旗的蓝色
		colorIceberg := color.NRGBA{117, 170, 219, 100}
		fill := &image.Uniform{colorIceberg}
		// 偶数块涂上白色
		if (x/step)%2 == 0 {
			fill = &image.Uniform{color.White}
		}
		draw.Draw(img, image.Rect(x, 0, x+step, size), fill, image.ZP, draw.Src)
	}
	// 打开输出文件
	outputFile, err := os.Create("D:/rect.png")
	if err != nil {
		panic(err)
	}
	defer outputFile.Close()

	// 调用png.Encode()来输出图片。参数是文件名和上面生成的Image对象
	png.Encode(outputFile, img)

}
```

这个程序画出一个正方形，里面涂上类似阿根廷足球队服的蓝白条纹。结果如下图：

![argentina.png](argentina.png)


#### Task N：读取png文件

然后我们再来看看如何读取png图片。这例子里，我们把这件阿根廷队服图片读取到内存，再把它替换成红黑相间的AC米兰队服图片，并写入到`acmilan.png`中。

```go
package main

import (
	"image"
	"image/color"
	"image/png"
	"os"
)

func changeColor(img image.Image) *image.RGBA {
	bounds := img.Bounds()
	dx := bounds.Dx()
	dy := bounds.Dy()
	res := image.NewRGBA(bounds)
	white := color.RGBA{255, 255, 255, 255}
	for i := 0; i < dx; i++ {
		for j := 0; j < dy; j++ {
			c := img.At(i, j)
			if c == white {
				// 如果是白色，设置成黑色
				res.SetRGBA(i, j, color.RGBA{0, 0, 0, 255})
			} else {
				// 如果不是白色，设置成红色
				res.SetRGBA(i, j, color.RGBA{255, 0, 0, 255})
			}
		}
	}
	return res
}

func main() {
	// 打开PNG文件
	f, err := os.Open("D:/argentina.png")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	img, err := png.Decode(f)
	if err != nil {
		panic(err)
	}
	newImg := changeColor(img)

	// 打开目标文件
	fout, err := os.Create("D:/acmilan.png")
	if err != nil {
		panic(err)
	}
	defer fout.Close()

	png.Encode(fout, newImg)
}
```

可以看到，读取图像文件时，只需要在文件上套上`png.Decode`就可以了。

图形处理是个非常丰富的大话题，所以更多的图像处理功能，我们会在本书后面关于图像处理的章节中详细描述。

### 压缩文件读写

Go语言标准库`compress`模块提供了几种常见格式的压缩和解压：

- bzip2
- flate
- gzip
- lzw
- zlib

这里我们概要介绍一下最常用的gzip格式，其他格式用法基本都一样。

#### Task N：读取gzip文件

读取gzip格式压缩文件，假设解压缩后是文本文件，我们需要按行读取：

```go
package main

import (
	"bufio"
	"compress/gzip"
	"fmt"
	"os"
)

func main() {
	// 打开压缩问价
	f, err := os.Open("D:/sonnet18.txt.gz")
	if err != nil {
		panic(err)
	}
	defer f.Close()

	// 打开gzip的Reader，套在文件Reader之上
	gr, err := gzip.NewReader(f)
	if err != nil {
		panic(err)
	}
	defer gr.Close()

	// 新建一个Scanner，读取gzip.Reader
	scanner := bufio.NewScanner(gr)
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}
}
```

这里可以看到，添加gzip解压功能非常直观。 由于Go语言`io.Reader`接口的通用性，和普通格式文件相比，读取gzip格式的文件，只需在打开文件后，解析文本前，多套一层`gzip.NewReader`就可以了。

#### Task N：写入gzip文件

而写入gzip压缩文件也同样只需要多套一层`gzip.NewWriter`：

```go
package main

import (
	"bufio"
	"compress/gzip"
	"os"
)

func main() {

	// 建立压缩文件
	f, err := os.OpenFile("D:/guanju.utf8.txt.gz", os.O_WRONLY|os.O_CREATE, 0644)
	if err != nil {
		panic(err)
	}
	defer f.Close()

	// 建立gzip.Writer
	gw := gzip.NewWriter(f)
	defer gw.Close()

	// 建立bufio.Writer
	bw := bufio.NewWriter(gw)

	// 文本
	text := `关雎 
关关雎鸠，在河之洲。
窈窕淑女，君子好逑。
参差荇菜，左右流之。
窈窕淑女，寤寐求之。
求之不得，寤寐思服。
悠哉悠哉，辗转反侧。
参差荇菜，左右采之。
窈窕淑女，琴瑟友之。
参差荇菜，左右芼之。
窈窕淑女，钟鼓乐之。`

	// 写入文本并Flush()
	bw.WriteString(text)
	bw.Flush()
}
```

可以看到，这里实际上也是在文件`f`和`bw bufio.Writer`之间套了一层`gzip.Writer`。
注意，在建立`gw := gzip.NewWriter(f)`之后，要加上`defer gw.Close()`，否则如果gzip的Writer没有正确关闭的话，压缩文件结尾会错误。

再进一步，利用Go语言Reader接口的组合特性，将gzip和csv配合起来，可以实现读写gzip格式的csv数据文件。

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

同样的方法，也可以用于其他格式，如压缩的JSON/XML等。

### 配置文件

另外，我们除了数据文件，还需要处理配置文件，因此这里也描述一下常用配置文件的读写。因为配置

Go语言并没有规定统一的配置语言，也没有官方推荐。所以需要根据你的需求来寻找合适的配置语言。

一般来说，一个工程的配置可以有多种来源：

1. 代码内写死的默认值
2. 程序命令行参数。在Go语言里这个称为Flags。标准库有`flags`模块专门处理命令行参数的读取。
3. 环境变量。
4. 配置文件。格式可以是JSON、XML等，或者专业配置语言INI、YAML、TOML等。
5. 分布式配置系统。如etcd、Consul等。

Go语言的标准工程结构并不需要配置文件，而是通过源码依赖与import路径来解决工程依赖关系。因此Go语言标准库里并没有提供对某种配置语言的支持。但作为通用数据格式，JSON和XML这两种格式在标准库里有较好的支持，所以不少人用JSON/XML作为配置语言。

但实际上JSON做配置语言还是没有那些专业户好用，毕竟它有两个大缺点：

1. 不支持注释。没有注释说明，配置文件的可读性大大降低，也增加了多人维护的沟通成本。而且无法用注释快速切换配置项，实际使用时也不方便。
2. 配置变量名必须用双引号。写起来比其他配置语言还是更加冗长。

而XML作为传统配置语言，功能倒是足够，但是它是所有常用配置语言里最冗长的。

有不少第三方库支持其他专门的配置语言格式，我们选取几个比较流行的坐下介绍：

go-ini: <https://github.com/go-ini/ini>。支持ini配置文件。

TOML: <https://github.com/BurntSushi/toml>。支持TOML配置语言。TOML是Rust官方推荐的配置语言（用于其代码工程配置），可读性、简洁性、和丰富度都不错。

YAML: <https://github.com/go-yaml/yaml>。比较传统的专业配置语言，可以说是简洁性提高很多的XML。

IniFlag: <https://github.com/vharitonsky/iniflags>。支持命令行参数Flag和ini文件格式的混合配置。

GoConfig: <https://github.com/micro/go-config>。支持多种配置源，如环境变量、命令行参数Flag、多种格式配置文件、etcd、k8s configmaps等。并且他有个特色可以实现动态加载，即这个库可以自动监测多个配置源，遇到更新时会自动重新加载。

Viper: <https://github.com/spf13/viper>。最强大的配置框架。支持多种配置源：程序设置、命令行参数Flag、环境变量、 多种配置文件（JSON, TOML, YAML, HCL, 以及Java Properties文件）、分布式配置系统（etcd、Consul）、缓冲区等。而且也支持动态加载。另外，它还符合`12-Factor apps`规范，利于云部署。所以现在viper是最流行的配置框架。

上面介绍的这几个第三方库，基本是按照从简单到复杂的顺序排列的，所以读者可以参考自己的需求，选择适合的库来使用。总的来说，如果你的工程很简单，只需要几个配置项，那么通常直接用Flags或者环境变量就可以了。稍微复杂一点的，可以使用json/xml/ini等配置文件。如果需要结构化比较丰富的配置，则可以考虑TOML和YAML。如果是需要动态加载等复杂功能，或者要部署到云端，需要etcd等远程配置系统的话，可以考虑GoConfig或者Viper。

我们会在最后的“第三方库赏析”里，专门对Viper库做一下分析，寻找到其中处理文件的代码逻辑，择其优秀者赏析之。

## 1.5 其他文件操作

除了读写，我们还还可以对文件进行查询、新建、删除、移动（重命名）、复制、搜索等操作。而且文件还可以组成文件夹。因为操作文件时往往涉及到文件夹，因此一起介绍了。

本节介绍这些操作的基本用法，以及几个进阶的应用，如文件树扫描、文件夹同步（类Rsync）、文件备份等。

### Task N：文件信息查询

Go语言标准库里，文件信息的查询都集成在`os.Stat()`函数里，这和操作系统的设计是一致的，操作系统中，文件的所有信息都存在一个字段里。示例如下：

```go
package main

import (
	"fmt"
	"os"
)

func main() {

	stat, err := os.Stat("D:/sonnet18.txt")
	if err != nil {
		panic(err)
	}

	// 文件名
	fmt.Printf("fileName: %v", stat.Name())
	// 是否文件夹
	fmt.Printf("isDir: %v\n", stat.Mode().IsDir())
	// 上面同样功能的便捷函数
	fmt.Printf("isDir: %v\n", stat.IsDir())
	// 是否普通文件
	fmt.Printf("isRegular: %v\n", stat.Mode().IsRegular())
	// 文件权限
	fmt.Printf("permission: %v\n", stat.Mode().Perm())
	// 修改时间
	fmt.Printf("lastModTime: %v\n", stat.ModTime())
	// 文件大小
	fmt.Printf("size: %v\n", stat.Size())

}
```

但是由于操作系统不同时，其他几个信息，如文件创建时间(ctime)、上次访问时间（atime）等，在标准库的代码里没有直接提供。要想访问这几个时间，只能使用操作系统相关的代码了，比如我的测试机是Windows的，是这么用：

```go
package main

import (
	"fmt"
	"os"
	"syscall"
	"time"
)

func main() {

	stat, err := os.Stat("D:/sonnet18.txt")
	if err != nil {
		panic(err)
	}

	wstat := stat.Sys().(*syscall.Win32FileAttributeData)
	fmt.Printf("atime: %v\n", time.Unix(0, wstat.LastAccessTime.Nanoseconds()))
	fmt.Printf("mtime: %v\n", time.Unix(0, wstat.LastWriteTime.Nanoseconds()))
	fmt.Printf("ctime: %v\n", time.Unix(0, wstat.CreationTime.Nanoseconds()))
}
```

不过一般不推荐直接这么用，否则你的代码就失去了跨平台的能力，并且用起来很麻烦。

第三方库[times](https://github.com/djherbis/times)封装了一套接口，可以让你比较跨平台（这么说，是因为有的平台还是不完全支持这几个时间）的查询文件时间。具体对某个操作系统的支持，请移步去它的主页查看。这里是一个简单的例子：

```go
package main

import (
	"log"

	times "gopkg.in/djherbis/times.v1"
)

func main() {
	t, err := times.Stat("D:/sonnet18.txt")
	if err != nil {
		log.Fatal(err.Error())
	}

	log.Println(t.AccessTime())
	log.Println(t.ModTime())

	if t.HasChangeTime() {
		log.Println(t.ChangeTime())
	}

	if t.HasBirthTime() {
		log.Println(t.BirthTime())
	}
}
```

显然，经过封装，这套代码比上面我使用的Windows操作系统的函数更可读，而且也通用。
当然，要使用这个包，首先需要运行`go get gopkg.in/djherbis/times.v1`

但和Python等其他语言提供的标准函数比起来，这样使用并不方便，所以我们也模拟Python实现几个方便函数。

```go
package main

import (
	"fmt"
	"os"
)

// IsDir check whether path is a directory
// 检查路径是否问文件夹
func IsDir(path string) (bool, error) {
	stat, err := os.Stat(path)
	if err != nil {
		return false, err
	}
	return stat.IsDir(), nil
}

// IsFile check whether path is a regular file
// 检查路径是否为普通文件
func IsFile(path string) (bool, error) {
	stat, err := os.Stat(path)
	if err != nil {
		return false, err
	}
	return stat.Mode().IsRegular(), nil
}

// IsExist check whether path exists
// 检查路径是否存在
func IsExist(path string) (bool, error) {
	_, err := os.Stat(path)
	if err == nil {
		return true, nil
	}
	if os.IsNotExist(err) {
		return false, err
	}
	return true, err
}

func main() {
	path := "D:/sonnet18.txt"

	// 是否文件夹
	isDir, _ := IsDir(path)
	fmt.Printf("isDir: %v\n", isDir)
	// 是否普通文件
	isFile, _ := IsFile(path)
	fmt.Printf("isFile: %v\n", isFile)
	// 是否存在
	isExist, _ := IsExist(path)
	fmt.Printf("exists: %v\n", isExist)
}

```

了解了文件信息查询之后，我们看看怎么新建文件。

TODO: 文件信息修改

### Task N：新建文件

#### 新建文件

我们先看看新建最简单的文件。这个操作相当于UNIX系统中的`touch`命令：

```go
package main

import "os"

func main() {
	file, err := os.Create("D:/etgo/new_file.txt")
	if err != nil {
		panic(err)
	}
	file.Close()
}
```
参见示例代码：`/chapter_file/file_manipulation/create_file/create_file.go`

注意，这里必须保证`etgo/`这个文件夹已经事先建立好了，否则会报错。等会儿讨论过文件夹的操作之后我们再详细处理这个情况。

我们可以查看`os.Create()`函数的代码，发现里面就是直接调用了`os.Open()`：

```go
func Create(name string) (*File, error) {
	return OpenFile(name, O_RDWR|O_CREATE|O_TRUNC, 0666)
}
```
因此，`os.Create()`只是一个方便函数而已。

文件建立好以后，就可以直接对它进行写入操作了。具体做法参见本章开头的文件写入章节。

#### 新建文件夹

如果想新建文件夹，可以调用`os.MkDir()`

```go
package main

import (
	"os"
)

func main() {
	if err := os.Mkdir("D:/tmp", 0644); err != nil {
		panic(err)
	}
}
```

如果文件夹已经存在，则会报错：

```
panic: mkdir D:/tmp: Cannot create a file when that file already exists.
```

如果你想要新建的文件夹是好几层嵌套的，希望一个函数把整个路径上每个文件夹都建立起来，可以使用`os.MkdirAll()`：

```go
package main

import (
	"os"
)

func main() {
	if err := os.MkdirAll("D:/tmp/sub", 0644); err != nil {
		panic(err)
	}
}
```

这里，如果文件夹已经存在，就不会报错了。因为它的功能时尝试路径中每一个文件夹是否存在，不存在就建立，存在则跳过。这个函数也会遇到错误，比如路径中的文件夹已经存在，但是并不是文件夹，而是不同文件；或者文件夹读写权限有问题。

比如，如果sub是一个文件，则会报如下错误：

```
panic: mkdir D:/tmp/sub: The system cannot find the path specified.
```

#### 综合起来

将上述两个功能综合起来，我们可以设计一个新建文件的函数，它会自动检查其路径上目录是否存在，并帮忙建立好。

```go
package main

import (
	"os"
	"path/filepath"
)

func main() {
	path := "D:/etgo1/new_file.txt"
	createWithDir(path)
}

func createWithDir(path string) {
	dir := filepath.Dir(path)
	if fi, err := os.Stat(dir); err == nil {
		// 文件夹路径存在，但该路径不是文件夹
		if !fi.IsDir() {
			panic(dir + " is not a directory")
		}
	} else if os.IsNotExist(err) { // 文件夹不存在，尝试新建
		os.MkdirAll(dir, 0644)
	} else { // 其他情况，比如没有查看权限
		panic(err)
	}
	// 文件夹已经建好，在其中新建文件
	file, err := os.Create(path)
	if err != nil {
		panic(err)
	}
	file.Close()
}
```

### Task N：删除文件

删除文件也是非常常见的需求。我们先看看最简单的删除操作：

```go
package main

import (
	"os"
)

func main() {
	err := os.Remove("D:/test.txt")
	if err != nil { // 删除失败
		panic(err)
	}
}
```

`os.Remove`删除单个文件，效果和UNIX系统的`rm`差不多。如果需要删除多个文件，我们就需要获取全部文件，分别调用了。

那么有没有按照通配符删除文件的功能呢？类似于`rm test*.txt`。可惜，标准库没有直接提供这个功能，不过还好它提供了使用通配符查找文件的功能`filepath.Glob()`，我们可以使用这个函数来找到所有要删除的文件，再逐一删除即可：

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func main() {
	// 找到符合通配符的文件
	files, err := filepath.Glob("D:/tmp/test*.txt")
	if err != nil {
		panic(err)
	}
	// 逐一删除每个文件
	n := 0
	for _, f := range files {
		err := os.Remove(f)
		if err != nil {
			panic(err)
		}
		n++
	}
	fmt.Printf("%v files removed.\n", n)
}
```
参见`/chapter_file/file_manipulations/remove_wildcard/remove_wildcard.go`

如果想要删除文件夹，也可以直接调用`os.Remove()`，但是如果文件夹内容为空，则无法删除。这时候我们可以调用`os.RemoveAll()`来删除该文件夹中的所有内容，并且删除该文件夹。

```go
package main

import "os"

func main() {
	err := os.RemoveAll("D:/tmp")
	if err != nil { // 删除失败
		panic(err)
	}
}
```
参见`/chapter_file/file_manipulations/remove_dir/remove_dir.go`

那如果我们想清空文件夹，但是不删除该文件夹本身呢？一种办法是利用`filepath.Glob()`找到文件夹中的所有子节点，然后再逐个调用`os.RemoveAll()`将它们分别完全删除。示例如下：

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func main() {
	// 找到文件夹之下的所有子节点
	files, err := filepath.Glob("D:/tmp/*")
	if err != nil {
		panic(err)
	}
	// 逐一删除每个文件或文件夹
	n := 0
	for _, f := range files {
		err := os.RemoveAll(f)
		if err != nil {
			panic(err)
		}
		n++
	}
	fmt.Printf("%v nodes removed.\n", n)
}
```
参见`/chapter_file/file_manipulations/clear_dir/clear_dir.go`


### Task N：重命名和移动

Go标准库直接支持了重命名，`os.Rename()`：

```go
```

### Task N：复制文件

### Task N：修改文件状态

### Task N：搜索

### Task N：同步

### Task N：文件备份

## 1.6 文件I/O的应用实例

这一节我们看一个文件操作的综合应用实例。 这个实例的需求来自NeoReads应用。

### Task N：古腾堡书籍的预处理

我们考虑把古腾堡的一本书，做预处理的过程复现一遍。举例就用简·奥斯丁的《傲慢与偏见》（Pride and Prejudice）这本书吧。

#### 第1步，下载书籍

首先从古腾堡下载这本书的文本。在本章，我们先手动下载。大规模的自动下载要放在后面章节的HTTP客户端内容讲解。

我们在古腾堡的页面<https://www.gutenberg.org/>上搜索"Pride and Prejudice"，找到Jane Austen的这本书，选择"Plain Text UTF-8"格式，得到的链接是<https://www.gutenberg.org/files/1342/1342-0.txt>

手动下载该文件（古腾堡禁止自动下载书籍链接，而书籍的压缩合集另有地方下载，参见后面关于HTTP下载的章节），另存为pride_and_prejudice.txt

#### 第2步，观察书籍内容

接下来我们怎么做呢？先观察一下这个文本，开头是一些meta信息，包括书名、作者、上传日期等，以及古腾堡的授权信息。然后有一个分隔行：

```
*** START OF THIS PROJECT GUTENBERG EBOOK PRIDE AND PREJUDICE ***
```

后面的内容分别是提供者、标题和作者：

```
Produced by Anonymous Volunteers

PRIDE AND PREJUDICE

By Jane Austen

Chapter 1
```

这里`Chapter 1`之后就是第一章的正式内容了。

然后看文本结尾，

```
End of the Project Gutenberg EBook of Pride and Prejudice, by Jane Austen

*** END OF THIS PROJECT GUTENBERG EBOOK PRIDE AND PREJUDICE ***
```

显然这个是正文结尾，以及分隔行。之后一大段是古腾堡的版权说明。

现在我们大概弄清了本书的结构。

#### 第3步，确定任务内容

我们确定预处理的步骤如下：

1. 删除开头分隔行之前的内容
2. 删除结尾分隔行之后的内容
3. 在开头获取标题、作者
4. 搜索"Chapter 1"这样的字符串，找到每一章的开头
5. 拆分各个章节，存放到不同的文件中
6. 将得到的标题等信息，以及各个章节和内容信息写入到数据库

我们的阅读应用，是以章节为基本阅读单元的，所以原始文本必须拆分到章节。数据库中存储的是章节信息，以及对应的压缩文本文件地址。

#### 第4步，扫描文件，过滤掉开头和结尾

首先我们要按行读取文件，然后找到开头和结尾的分隔行的位置。遇到开头分隔行的时候，就开始记录之后读到的每一行，写入到一个新文件里；遇到结尾分隔行时，直接退出扫描。代码如下：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
	"strings"
)

// 判断正文开始的分隔行
func isStartDelim(line string) bool {
	return strings.HasPrefix(line, "*** START OF")
}

// 判断正文结尾的分隔行
func isEndDelim(line string) bool {
	return strings.HasPrefix(line, "*** END OF")
}

// 判断是否为章节标题行
func isChapterTitle(line string) bool {
	return strings.HasPrefix(line, "Chapter")
}

// 提取章节编号
func extractChapterTitle(line string) string {
	return strings.TrimSpace(line[len("Chapter"):])
}

// 用来收集一个章节的数据结构
type chapter struct {
	title    string
	contents []string
}

// 打印章节的统计信息
func statsChapter(ch *chapter) {
	fmt.Printf("Chapter %v has %v lines\n", ch.title, len(ch.contents))
	fmt.Println("Last line:", ch.contents[len(ch.contents)-1])
}

func main() {
	// 打开文件
	file, err := os.Open("E:/books/gutenberg/pride_and_prejudice.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	// 使用scanner按行读取
	scanner := bufio.NewScanner(file)
	isContent := false
	var curChapter chapter
	for scanner.Scan() {
		// 当前行
		line := strings.TrimSpace(scanner.Text())

		// 如果遇到正文开始分隔行，则进入正文
		if isStartDelim(line) {
			isContent = true
			continue
		}

		// 如果遇到正文结束分隔行，则退出循环
		if isEndDelim(line) {
			break
		}

		// 如果遇到新章节的标题
		if isChapterTitle(line) {
			// 统计上一章节
			statsChapter(&curChapter)
			// 创建新章节
			chapterNum := extractChapterTitle(line)
			curChapter = chapter{chapterNum, []string{}}
			continue
		}

		// 如果是正文，则当前行加入当前章节数据中
		if isContent && len(line) > 0 {
			curChapter.contents = append(curChapter.contents, line)
		}

	}
	statsChapter(&curChapter)

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```
示例代码：`/chapter_file/guten1/guten1.go`

这里，我们每遇到一个新章节，就把上一章节收集到数据（存放在一个`chapter`对象中）做一下统计打印出来。实际项目中，则应该是将这个章节的数据写入到章节文件与数据库中去。这部分内容我们在后面的数据库章节再详细探讨。

现在运行程序，输出结果是这样的（由于输出太多，只显示后面几行）：

```
Chapter 55 has 209 lines
Last line: they had been generally proved to be marked out for misfortune.
Chapter 56 has 250 lines
Last line: acknowledge the substance of their conversation was impossible.
Chapter 57 has 146 lines
Last line: his seeing too little, she might have fancied too much.
Chapter 58 has 217 lines
Last line: parted.
Chapter 59 has 219 lines
Last line: as Jane's.”
Chapter 60 has 138 lines
Last line: Pemberley.
Chapter 61 has 108 lines
Last line: End of the Project Gutenberg EBook of Pride and Prejudice, by Jane Austen
```

观察输出，我们发现两个问题：

1. 最后一章的结尾还多了一句正文之外的话。
2. 每章最后一句都不是完整的句子。

这句话`End of the Project Gutenberg EBook of Pride and Prejudice, by Jane Austen`和结束分隔行的内容是冗余的，我也不知道古腾堡为什么要加上。但是显然得多加一句逻辑来处理。

而第2个问题，仔细看看文本，才发现每行都不是完整的句子。古腾堡的文章是按照宽度换行的，真正的段落是使用多个空行分开来表示的。比如第一章开头：

```
It is a truth universally acknowledged, that a single man in possession
of a good fortune, must be in want of a wife.

However little known the feelings or views of such a man may be on his
first entering a neighbourhood, this truth is so well fixed in the minds
of the surrounding families, that he is considered the rightful property
of some one or other of their daughters.

“My dear Mr. Bennet,” said his lady to him one day, “have you heard that
Netherfield Park is let at last?”

Mr. Bennet replied that he had not.
```

这里有四段话，每段之间有一个空白行表示分隔，而不是我们平时直接换行。而每段之内，句子到达一定宽度就直接换行了。比如第一行到了`in possesion`，就直接另起一行再接着写`of a good fortune`了。

这显然不是我们想要的格式。因为最终我们的文本要显示在手机、平板、电脑等不同尺寸的屏幕上，需要实时自适应屏幕换行，而不是事先按照固定宽度换掉。所以我们需要把这些换行再合并回去。做到每段话是一行文字。

另外再看看输出的开头：

```
Chapter  has 3 lines
Last line: By Jane Austen
Chapter 1 has 78 lines
Last line: daughters married; its solace was visiting and news.
```

第一章之前，标题、作者那几句话，没有处理对。也要单独加上处理函数，来记录书本的meta信息。

这样，经过改进，我们得到了第二版代码：

```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
	"strings"
)

// 判断正文开始的分隔行
func isStartDelim(line string) bool {
	return strings.HasPrefix(line, "*** START OF")
}

// 判断正文结尾的分隔行
func isEndDelim(line string) bool {
	return strings.HasPrefix(line, "*** END OF")
}

// 判断是否为章节标题行
func isChapterTitle(line string) bool {
	return strings.HasPrefix(line, "Chapter")
}

// 提取章节编号
func extractChapterTitle(line string) string {
	return strings.TrimSpace(line[len("Chapter"):])
}

type bookmeta struct {
	title    string
	author   string
	language string
}

// 用来收集一个章节的数据结构
type chapter struct {
	title    string
	contents []string
}

// 打印章节的统计信息
func statsChapter(ch *chapter) {
	if ch.title != "" { // 忽略掉第一章之前那段无效文字
		fmt.Printf("Chapter %v has %v paragraphs.\n", ch.title, len(ch.contents))
		fmt.Println("Last Paragraph:", ch.contents[len(ch.contents)-1])
		fmt.Println()
	}
}

// 检查书籍信息
func checkMeta(meta *bookmeta, line string) {
	titlePrefix := "Title: "
	authorPrefix := "Author: "
	langPrefix := "Language: "
	if strings.HasPrefix(line, titlePrefix) {
		meta.title = line[len(titlePrefix):]
	} else if strings.HasPrefix(line, authorPrefix) {
		meta.author = line[len(authorPrefix):]
	} else if strings.HasPrefix(line, langPrefix) {
		meta.language = line[len(langPrefix):]
	}
}

func main() {
	// 打开文件
	file, err := os.Open("E:/books/gutenberg/pride_and_prejudice.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	// 使用scanner按行读取
	scanner := bufio.NewScanner(file)
	isContent := false
	var curChapter chapter
	var meta bookmeta
	// 需要一个slice来存放当前段落的每一行，遇到新空白行的时候，将之前收集到的多行合并为一行。
	var curPara []string
	for scanner.Scan() {
		// 当前行
		line := strings.TrimSpace(scanner.Text())

		// 如果遇到正文开始分隔行，则进入正文
		if isStartDelim(line) {
			isContent = true
			continue
		}

		// 如果遇到正文结束分隔行，则退出循环
		if isEndDelim(line) {
			// 处理正文结束分隔行前一句多余的内容
			lastPos := len(curChapter.contents) - 1
			lastPara := curChapter.contents[lastPos]
			if strings.HasPrefix(lastPara, "End of the Project Gutenberg EBook") {
				curChapter.contents = curChapter.contents[:lastPos]
			}
			break
		}

		// 读取meta信息
		if !isContent {
			checkMeta(&meta, line)
		}

		// 如果遇到新章节的标题
		if isChapterTitle(line) {
			// 处理上一章节
			statsChapter(&curChapter)
			// 创建新章节
			chapterNum := extractChapterTitle(line)
			curChapter = chapter{chapterNum, []string{}}
			continue
		}

		// 如果是正文，则当前行加入当前章节数据中
		if isContent {
			if len(line) > 0 { // 文本
				curPara = append(curPara, line)
			} else { // 空行
				if len(curPara) > 0 {
					curChapter.contents = append(curChapter.contents, strings.Join(curPara, " "))
					curPara = []string{}
				}
			}
		}

	}
	// 处理最后一章
	statsChapter(&curChapter)

	// 打印书籍信息
	fmt.Printf("Book Meta: %#v", meta)

	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```
参见示例代码：`/chapter_file/guten2/guten2.go`

这里我们增加了`bookmeta`数据结构，然后在正文开始之前对它进行了分析。
另外，在处理正文时，也区分了当前行是否为空行，如果是空行，则表示上一个段落结束了。我们增加了一个对列用来存放当前段落的每一行，并在段落结束时，将他们`Join()`在一起，这样就恢复了文本本来的段落关系。

最后，打印出来的结果如下（只显示最后几行）：

```
Chapter 60 has 29 paragraphs.
Last Paragraph: Mrs. Phillips's vulgarity was another, and perhaps a greater, tax on his forbearance; and though Mrs. Phillips, as well as her sister, stood in too much awe of him to speak with the familiarity which Bingley's good humour encouraged, yet, whenever she _did_ speak, she must be vulgar. Nor was her respect for him, though it made her more quiet, at all likely to make her more elegant. Elizabeth did all she could to shield him from the frequent notice of either, and was ever anxious to keep him to herself, and to those of her family with whom he might converse without mortification; and though the uncomfortable feelings arising from all this took from the season of courtship much of its pleasure, it added to the hope of the future; and she looked forward with delight to the time when they should be removed from society so little pleasing to either, to all the comfort and elegance of their family party at Pemberley.

Chapter 61 has 15 paragraphs.
Last Paragraph: With the Gardiners, they were always on the most intimate terms. Darcy, as well as Elizabeth, really loved them; and they were both ever sensible of the warmest gratitude towards the persons who, by bringing her into Derbyshire, had been the means of uniting them.

Book Meta: main.bookmeta{title:"Pride and Prejudice", author:"Jane Austen", language:"English"}
```

可以看到现在每个章节里是一个个段落，而不是被固定宽度打断的一行行文字了。另外，最后一行也正确的输出了在正文前分析出来的书籍信息。

有了书籍信息、每个章节的数据，我们就可以在下一章“数据库操作”的综合应用里，把他们写入到数据库中了。



## 1.7 扩展话题：相关第三方库赏析

tail库：<https://github.com/hpcloud/tail>

viper库：<https://github.com/spf13/viper>

protobuf库: <https://github.com/golang/protobuf>