# Go语言日常 | Everyday Tasks With Go

Go语言进阶书籍，讲述日常开发中遇到的各种任务和解决方案。

A mid level Golang book for everyday programming tasks.

# 样章 | Sample Chapter

- [样章：文件](file.cn.md)
- [Sample Chapter](file.cn.md)


# 目录 | Contents

整体进度：[5%]

- [**100%**] [引言](intro.cn.md) - 概述本书的定位和内容
- [ 1%] [并发](concurrency.cn.md) - Go语言最强大的工具
- [**80%**] [文件](file.cn.md) - 关于文件操作的大百科全书
- [ 0%] [数据](data.cn.md) - struct, csv, json, xml, protobuf, flatbuffer等
- [ 0%] [日志](log.cn.md) - beyond stdout
- [ 1%] [WEB](web.cn.md) - Go的新土地
- [ 0%] [数据库](database.cn.md) - 互联网的脊梁，以及，我选PostgreSQL
- [ 0%] [安全](security.cn.md) - Crypto, SQL Injection and more
- [ 0%] [配置](config.cn.md) - 从goflags到etcd
- [ 0%] [时间/日期](time.cn.md) - 从纳秒到宇宙尺度
- [ 0%] [数据结构](datastructure.cn.md) - 从[]数组到红黑树
- [ 0%] [常用算法](algorithms.cn.md) - **常用**算法
- [ 0%] [网络](network.cn.md) - Go最多的应用场景
- [ 0%] [通知](notification.cn.md) - Email, SMS and others
- [ 0%] [分布式](distributed.cn.md) - 并发的扩展，以及consensus
- [ 0%] [React](react.cn.md) - React
- [ 0%] [Debug](debug.cn.md) - 不想见到你
- [ 0%] [运维](devops.cn.md) - 可靠性的关键
- [ 0%] [科学计算](scientific.cn.md) - 抢滩Python的领土
- [ 0%] [大数据](bigdata.cn.md) - small language, big data
- [ 0%] [云端](cloud.cn.md) - 开源工程云端生存指南
- [ 0%] [图像处理](image.cn.md) - 自制photoshop
- [ 0%] [多媒体](multimedia.cn.md) - 语音、视频与VR
- [ 0%] [游戏](game.cn.md) - Games
- [ 0%] [机器学习与AI](ai.cn.md) - 初步介绍，现在Go语言在AI领域还没有发力
- [ 0%] [嵌入式](embedded.cn.md) - IoT

# 介绍 | Introduction

### 起因

程序员使用某个语言编程，常常经历几个发展阶段：

1. 初学乍练的新手
1. 初窥门径的初级程序员
1. 登堂入室的中级程序员
1. 驾轻就熟的老手
1. 融会贯通的高手
1. 登峰造极的大师

每个阶段的成长，都离不开大量的学习、编程实践、交流，这个过程中离不开编程书籍的辅助。

我观察市面上的编程书籍，往往有如下几个系列：

- 《The X Programming Language》。程序设计语言系列。一般都是语言创造者写的权威书籍，重点介绍语言的设计特性，其特色是权威、参考性。
- 《X in Action》。实战系列。其特色是注重实用性，循序渐进。不像《X程序设计语言》那样只介绍语言本身，还会介绍标准库、编程环境等实践环节。
- 针对某个框架、某个库的进阶书籍。如spring框架的书籍、nodejs框架的书籍、数据库相关的书籍、hadoop等大数据书籍、深度学习框架的书籍等。
- 《Effective X》。高效系列。进阶书籍，重点关注进阶程序员需要了解的性能、设计等问题。
- 《Design Patterns in X》。设计模式系列。起源于《设计模式》一书，用对应语言来实现常见的设计模式。

可以看到，阶段一的初学者，手边可选的书是最多的，程序设计语言系列、实战系列以及其他几个类似的系列，都是针对零基础初学者，然后以后期参考作用。 然后，第四个阶段的老手，可以阅读高效编程系列或设计模式系列等书籍，以期求豁然开朗，成为真正的高手。 但广大的二三阶段的初中级程序员，往往没有语言关联的书籍，而只能参考针对某些框架、库的书籍，在日常开发中遇到的问题，往往要通过网络搜索、问答网站、问同事、以及自己啃代码来解决。

有没有一本书，针对某个语言，总结初中级程序员日常开发中常见的问题，并搜集丰富的解决方案，以便开发过程中随时查阅呢？这就是《Go语言日常》这本书的初衷。 
这本书的定位，就是上述阶段二三的初中及程序员。在语言特性有基本了解后，对如何用来解决实际问题还有所迷茫的人们，这本书就是最好的辅助。

我个人也是从新手一路成长过来的，所以根据自己的开发经验，初步总结了如下的需求（任务Task），作为写作的主题：（未来随着写作的进展会根据反馈进行更改）

	* 文件读写 （文本、二进制、分布式等）
	* 数据流读写（比如JSON）
	* 邮件收发（报警、通知用）
	* 短信收发
	* 简单的服务器 （HTTPS等）
	* 压缩解压缩
	* 图像处理
	* 图表绘制 (visualization)
	* 表格处理（类似Datagram）
	* 定时任务（类似Crontab）
	* 消息队列（跨进程、跨机器）
	* 数据库读写
	* 文件同步 （跨机器，类似Rsync）
	* 目录文件操作（新建、删除等）
	* 服务器信息读取（类似top, free, ps等）
	* 进程监控、文件监控
	* 报警
	* 日志处理
	* 常见数据结构
	* 常见算法

另外，根据需要，还会涉及某些常用的库或框架的介绍，比如大数据、机器学习的库的实践主题。


### 本书的潜在优势：

第一，一本好书，细节的打磨尤其重要。日常的任务，根据具体问题的环境，不可能有普适的最佳解。所以针对每个任务，应当详细分析各种具体的场景，或者根据不同的需求，比如是性能要求高还是维护性要求高，来寻找不同的答案。同样是读取文件，可能追求打开的速度最大化，也可能追求内存的占用最合理，本书需要找到各种需求，提供相应的较佳的解决方案，并分析其利弊。在未来撰写博文的过程中，我也会按照这个要求来与读者互动，寻求更丰富的细节。也就是说，本书不但只给出答案，还能把答案给出花样来。这是本书优势其一。

第二，因为关注的重点是日常任务的实用性，那本书一定要配合好足够实用的代码。最好的情况是，读者在遇到问题之后，根据具体的场景在书中找到对应的解答，直接就能用上。这方面Go语言本身的特性倒是给了我们很大的便利。Go语言的惯例就是把所有库都收集在同一个工作空间中管理，通过工程和版本来区分引用。因此我只要针对问题和场景实现好不同版本的代码，读者就可以直接引用我的库来解决问题。这里为未来的代码做一个规范：某个问题，如果能用标准库解决，就用标准库；如果不行，则寻找最成熟、常用的库来解决，并做好版本管理与更新，博文和书籍未来都要根据引用库的情况来更新；如果实在没有第三方库，就通过我自己写的代码来实现，这样本书配套的代码，本身就能做成一个日常开库，为以后的读者所用。有了这个库，才能称为实用。

第三，由于不是入门书籍，本书并不会用单独的章节来逐个讲解语言特性，而是根据任务的解决方案，在对解决方案的评价环节，穿插讲解对应的语言特性，以及其优势劣势、需要注意的点。也就是说，我会在写好一段代码之后，再来评价这里为什么用这个语言特性来实现，换一种又会有什么影响，以及如果是其他语言，会有什么区别（以体现Go语言该特性的优势或劣势）。这种穿插讲解的方式，也许可以帮助已经学习过某些语言特性的程序员，更好地理解其实用方法。

第四，书籍各个章节的组织。因为本书的起源是我创业的读书应用的开发，所以我打算把它也利用上。具体方案就是，未来写的各种日常任务都能够在读书应用的程序中体现出来（即，读书应用严格依赖于日常库开发），而整书的脉络以读书应用的开发历程来有机组织。最终呈献给读者的图景就是：通过由浅入深、由易至难的一个个任务的解决，最终做出来一个商业化应用的复杂软件（即未来的读书应用），并且这个软件是开源的，随时可以参考。一本书，有一个真实软件作为支撑，会有更大的说服力。

### 为什么选择Go语言：

我计划个人开发一款阅读软件，经过技术调研，后端决定用Go语言，前端用Kotlin/Android来实现。因此接下来很长一段时间我的开发语言将以Go和Kotlin为主。而不先选择Kotlin，则是由于我是第一次开发移动App，所以对日常开发的需求还不够了解，打算在写完Go语言日常之后再考虑写Kotlin日常，并且那本书会更多得注重移动开发。我以往工作中使用过Java\C++\Lua\D进行过后台服务的开发，所以对后台开发的各种日常需求还是比较了解的，因此选择Go语言来成书。

### 写作计划：

总的来说，现在这本书的实现路径就是：

	1. 学好Go语言（已初步完成）
	2. 开发阅读软件的后台过程中总结需求，形成任务
	3. 针对每个任务，找到最好的解决方案，写好代码。
	4. 研究任务相关的各种不同情况，以及各种方案的优劣。
	5. 总结成一篇博文。
	6. 在网上发布，并征集反馈，对这个任务的需求和解决都进行优化 
	7. 将博文改成章节加入到正书中，并写好与其他章节的前后衔接
	8. 总集成书。

因此可以看出，这本书的写作应该是伴随着阅读软件的开发同步进行的。我预计阅读软件初版需要1年左右时间，因此本书预计再总结3个月，加上其他时间盈余，估计一年半成形。

### 短期计划：

2019年1月份（即年前）写好一篇样章，初步选定的任务是“文件读写”，我会把文件读写相关的各种场景总结出来，并找到相对好的解决方案，用Go语言实现，并写成章节。这个过程中可以通过探索而确立：

	* 单个主题的深度和广度
	* 任务的发现、定位、思考、讨论、解决、评价的过程，即初步确立每个章节的行文结构和套路
	* 确立配套日常库的代码风格
	* 收集反馈