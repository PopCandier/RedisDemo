### Redis（Remote Dictionary Service）

[Redis 设计与实现](<http://redisbook.com/>)

redis 主要是为了减缓数据库的压力而开发出来的运行于内存的数据库，并且他也有自己的持久化技术，集群和分布式解决方案。



#### Redis 与 SQL 与 NoSQL

> 关系型数据库的特点

* 表格的形式，行来存储数据，是一个二维形式
* 存储的是结构化的数据，数据结构有固定的模式，数据需要适应表结构
* 表与表之间存在关系( Relationship)
* 大部分关系型数据库支持SQL(结构化查询语句)的操作，支持复杂的关联查询。

* 通过支持事务(ACID酸，原子性，一致性，隔离，持久)来保证数据的一致性

> 关系型数据库的缺点

* 扩容麻烦，即当mysql的存储达到磁盘最大容量，必须通过堆积硬件的方法实现扩容，也就是所谓的垂直扩容，并且不支持动态扩容。水平扩容需要复杂的实现，例如分库分表
* 表结构修改的困难，因此存储的数据格式也受到了限制
* 高并发和高数据量的情况下，我们的关系型数据库会读写到磁盘，这样的磁盘压力会比较大



由此非关系数据库孕育而生，non-relational 活着 Not Only SQL，NoSQL 最开始是不提供 SQL 的数 

据库的意思，但是后来意思慢慢地发生了变化。

> 非关系型数据库的特点

* 存储非结构化的数据，比如文本，图片，音频，视频，比较自由

* 表与表之间没有关联，可扩展性强

* 保证数据的最终一致性。遵循BASE(碱)理论，。 Basically Available（基本 

  可用）； Soft-state（软状态）； Eventually Consistent（最终一致性）。

* 支持海量数据存储和高并发的读写。

* 支持分布式，能够对数据进行分片存储，扩缩容简单



更多非关系型数据库，请查阅 <http://nosql-database.org/>



#### Redis 的特性

之前也说了，redis说到底也就是用c写的一个缓存服务，使用也缓存也是为了取数据的的时候补用走一遍大流程，而是直接从缓存那边取就可以了。

Redis 的特性： 

* 更丰富的数据类型 

* 进程内与跨进程；单机与分布式 

* 功能丰富：持久化机制、过期策略 

* 支持多种编程语言 

* 高可用，集群



#### Redis 的安装

> linux上的安装 Redis 单实例  

// todo

> window 上安装 Redis  

```
https://github.com/MicrosoftArchive/redis/tags
```

下载完成后，直接启动。

```cmd
redis-server.exe redis.windows.conf
```

在客户端运行

```
shutdown
```

将会终止redis服务端

redis 的参数可以通过三种方式配置，一种是 redis.conf，一种是启动时--携带的参数，一种是 config set。

redis可视化工具

```
链接: https://pan.baidu.com/s/1GOkRg1JSbJYTP_6JPhAYtQ 提取码: ge7m 
```



#### 基本操作

redis中默认有16个库，这一点可以在配置文件中修改，默认使用第一个库，也就是db0

```properties
# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```

这里我们使用一个可视化的工具

![1572858604662](./img/1572858604662.png)

因为没有完全隔离，不像数据库的 database，不适合把不同的库分配给不同的业务使用。

```
select 0
select 15
select 8
```

![1572858733964](./img/1572858733964.png)

清空当前数据库

```
flushdb 
```

清空所有的数据库

```
flushall
```

redis更为详细的操作指南

http://redisdoc.com/index.html

存值

```
set pop 123
```

取值

```
get pop
```

查看所有的键

```
keys *
```

获取键总数

```
dbsize
```

查看键是否存在

```
exists pop
```

![1572859175303](./img/1572859175303.png)

返回 `1`表示存在，返回`0`表示不存在

删除键，可以删除多个，因为参数提示是个数组

```
del pop pipi
```

重命名 前面是旧值，后面是新值

```
rename pop pop1
```



#### Redis 的基本数据类型

redis有几种数据类型，这边说得是数据类型而不是数据结构

数据类型可以理解成每个语言原生的类型，例如int，long等，而数据结构更多可能包含逻辑和更加复杂的结构，例如java中的对象

redis的数据类型的介绍<https://redis.io/topics/data-types-intro>

```
String、Hash、Set、List、Zset、Hyperloglog、Geo、Streams
```

#### Redis 基本数据类型

> 存储类型

用来 存储字符串、整数、浮点数

```
mset pop2 123 pop3 123
```

设置值，如果key存在，则不成功

```
setnx pop 123
```

![1572860545342](./img/1572860545342.png)

其实这个地方可以有一个分布式锁的实现。

首先 `setnx lock 1`设置，如果成功就表示获得了锁，然后 `del lock`

释放锁。但是如果释放锁的操作失败了，导致其他节点获取不到锁就有问题了，所以还需要加入超时的时间。

```
expire pop 5
```

表示这个键`5s`后过期，不过如果这个时间段里，事情完成了一般就释放了，就无法保证原子性。

我们可以使用多参数的set

```
EX seconds ： 将键的过期时间设置为 seconds 秒。 执行 SET key value EX seconds 的效果等同于执行 SETEX key seconds value 。

PX milliseconds ： 将键的过期时间设置为 milliseconds 毫秒。 执行 SET key value PX milliseconds 的效果等同于执行 PSETEX key milliseconds value 。

NX ： 只在键不存在时， 才对键进行设置操作。 执行 SET key value NX 的效果等同于执行 SETNX key value 。

XX ： 只在键已经存在时， 才对键进行设置操作
```

```
set key value [expiration EX seconds|PX milliseconds][NX|XX]
```

例如，你可以设置一个key的，只有他不存在的时候才可以设置，并且十秒后过期

```
set lock 1 EX 10 NX
```

