# 数据库


几乎所有的商业应用都会用到某种形式的数据库。本章介绍下列日常接触比较多的数据库：
在基础读写之上，还需要设计ORM之类的通用数据访问层，以隔离底层物理数据库，我们也会对常用的ORM库做一下介绍。

在NeoReads里，数据库处于核心的地位。所有的书籍信息，所有的评论注释，所有的用户信息，都存在数据库中。因此读写数据库是本app后台的最主要的逻辑，代码行数估计也是最多的。

本章选取NeoReads中的书籍信息，展示这部分数据的表单设计、数据填充、数据读取、更新等过程。NeoReads使用的是PostgreSQL，所以会重点介绍。其他类型的数据库，只做概述，提点一下与PostgreSQL用法的不同之处，而详细的使用示例，则需要读者自己去做了。

## 我为什么用PostgreSQL

我们上一章描述了如何获取在线书籍，并读写文本文件，将书籍处理成一章一章的片段。本章就继续下一步工作：将读取到的书籍信息和书籍片段，存储到数据库中。

首先，我们得选一个数据库。

通过初步调查，我发现常用的数据库有如下几种：

- 关系数据库
  - Oracle
  - SQLite
  - PostgreSQL
  - MySQL
  - SQLServer
- 非关系数据库
  - MongoDB
- Key/Value存储
  - redis

那么应该用哪一个来实现NeoReads后台呢？

首先排除Oracle，这个要收费。然后排除SQLServer，这个是Windows平台的。然后排除SQLite，这个性能不够，估计只能用在前端。然后排除redis，它的数据结构太简单，估计满足不了应用需求。

这样，剩下的是PostgreSQL，MySQL和MongoDB

这三个该怎么选择呢？

这三个都有开源版本。但是经过调查，MongoDB已经于2018年修改了协议，不再允许其开源版本用于服务端商务应用。参见<https://www.mongodb.com/licensing/server-side-public-license>所以这个也得排除。

其次是MySQL。MySQL采用GPL协议，倒是只要求用它开源就可以。但是MySQL已经被Oracle收购，考虑到Oracle的一贯作风，我觉得还是远离它比较好。

最后就只剩下了PostgreSQL。据我所查，它的协议是类BSD的，参见<https://www.postgresql.org/about/licence/>，因此可以完全免费试用，对商用也没有特殊要求。

而且PostgreSQL的功能和MySQL，Oracle是一个量级的，完全够NeoReads的应用。它还内置支持JSON和文本检索，支持文本类应用更加方便。

因此，PostgreSQL是我的首选数据库应用。

接下来本章书的重点内容，都以PostgreSQL为示例介绍。最后再用单独的章节来分别介绍一下Go语言处理其他数据库时需要注意的问题，方便大家参考。

总的来说，Go语言把数据库读写做成了通用的接口，因此，具体使用哪一种数据库，在接口层面区别不大。

## Go语言数据库接口

Go语言标准库里支持数据库读写的接口放在`database/sql`包中。要使用Go语言读写数据库，只要引用这个包，再引用对应数据库的实现包即可。

要访问PostgreSQL，我们需要引入`pq`包，其地址是`https://github.com/lib/pq`

我们先看一个最简单的示例：

```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/lib/pq"
)

type student struct {
	id   int64
	name string
}

func main() {
	connStr := "user=postgres dbname=hello sslmode=disable password=123456"
	db, err := sql.Open("postgres", connStr)
	if err != nil {
		log.Fatal(err)
	}

	rows, err := db.Query("SELECT * FROM student")
	if err != nil {
		panic(err)
	}

	for rows.Next() {
		var s student
		err = rows.Scan(&s.id, &s.name)
		if err != nil {
			panic(err)
		}
		fmt.Printf("[%v]:%v\n", s.id, s.name)
	}
}

```

上面的示例中，假设数据表`student`有如下结构：

```sql
CREATE TABLE student
(
    id bigint,
    name character varying(20)
)
```

可以看到，典型的数据库读取操作有三个步骤：

- 使用`sql.Open()`函数建立数据库连接
- 调用`db.Query()`进行SQL查询，返回的是一行行数据`rows`
- 使用`rows.Next()`和`rows.Scan()`的组合来访问每一行数据的值

基本上掌握了这个流程，就可以进行绝大多数的数据库查询操作了。

我们再看看最简单的数据库插入操作：

```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/lib/pq"
)

func main() {
	connStr := "user=postgres dbname=hello sslmode=disable password=123456"
	db, err := sql.Open("postgres", connStr)
	if err != nil {
		log.Fatal(err)
	}

	res, err := db.Exec("INSERT INTO student VALUES(4, '赵云')")
	if err != nil {
		panic(err)
	}

	rowCount, err := res.RowsAffected()
	if err != nil {
		panic(err)
	}
	fmt.Printf("rows affected = %d\n", rowCount)
}

```

和查询操作类似，要执行一个插入语句，也需要三步：

- 使用`sql.Open()`函数建立数据库连接
- 调用`db.Exce()`执行SQL插入语句，返回的是一个result对象。
- 使用`res.RowsAffected()`方法查询该操作影响的行数。


## 第三方库

[sqlx](https://github.com/jmoiron/sqlx) - 标准库`database/sql`的扩展


