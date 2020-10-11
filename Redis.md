# NoSQL 概述

## NoSQL 特点

1. 方便扩展（数据之间没有关系！）
2. 大数据量高性能（Redis 一秒写8万次，读取11万次，NoSQL的缓存记录级，是一种细粒度的缓存，性能会比较高！）
3. 数据类型是多样型的！（不需要实现设计数据库！随去随用！）
4. 传统的 RDBMS 和 NoSQL

```shell
传统的 RDBMS:
- 结构化组织
- SQL
- 数据和关系都存在单独的表中
- 严格的一致性
- 基础的事务
...
```

```shell
NoSQL:
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库（社交关系）
- CAP定理 和 BASE
- 高性能，高可用，高可扩
...
```

## NoSQL 四大分类

**KV键值对：**

- Redis

**文档型数据库（bson格式 和json一样）:**

- MongoDB
  - MongoDB 是一个基于分布式文件存储的数据库，C++编写，主要用来处理大量的文档！
  - MongoDB 是一个介于关系型数据库和非关系型数据库中间的产品！MongoDB 是非关系型数据库中功能最丰富，最像关系型数据库的！
- ConchDB

**列存储数据库:**

- HBase
- 分布式文件系统

**图关系数据库:**

- 它放的不是图形，放的是关系，比如：朋友圈社交网络，广告推荐等！
- Neo4j，InfoGrid

# Redis 入门

## 概述

> Redis 是什么？

Redis（Remote Dictionary Server），即远程字典服务

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

> Redis 能干嘛？

1、内存存储，持久化。

2、效率高，可以用于高速缓存。

3、发布订阅系统。

4、滴入信息分析。

5、定时器，计数器（浏览量！）

6、......

> 特性

1、多样的数据类型

2、持久化

3、集群

4、事务

5、......

## Linux 下安装

redis 默认不是后台启动的，修改配置文件将 daemonize no 改为 yes

```shell
λ redis-server redis.conf     # 使用自己的配置文件
λ redis-cli -p 7379           # 连接:6379 默认端口号
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set name fuck
OK
127.0.0.1:6379> get name
"fuck"
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> shutdown
not connected> exit
```

## redis-benchmark
redis-benchmark 是一个压力测试工具！
```shell
# 测试: 100个并发连接，10万个请求
λ redis-benchmark -h localhost -p 6379 -c 100 -n 100000
...
100.00% <= 2 milliseconds
78247.26 requests per second

====== SET ======
  100000 requests completed in 1.24 seconds    ## 对10万个请求进行写入测试
  100 parallel clients                         ## 100个并发连接
  3 bytes payload                              ## 每次写入3个字节
  keep alive: 1                         ## 只有一台服务器来处理这些请求,单机性能
  host configuration "save": 900 1 300 10 60 10000
  host configuration "appendonly": no
  multi-thread: no

97.93% <= 1 milliseconds
99.94% <= 2 milliseconds
100.00% <= 3 milliseconds
80971.66 requests per second                   ## 每秒处理的请求数量

====== GET ======
  100000 requests completed in 1.27 seconds
  100 parallel clients
  3 bytes payload
  ...
```

## 基础知识
> Redis 有16个数据库, 默认使用第0个。

```shell
127.0.0.1:6379> select 3          ## 切换数据库
OK
127.0.0.1:6379[3]> DBSIZE         ## 查看数据库大小
(integer) 0
127.0.0.1:6379[3]> SET name fuck
OK
127.0.0.1:6379[3]> KEYS *         ## 查看所有的key
1) "name"
127.0.0.1:6379[3]> FLUSHDB        ## 清空当前数据库
OK
127.0.0.1:6379[3]> KEYS *
(empty array)
127.0.0.1:6379[3]> FLUSHALL       ## 清空全部数据库
OK
127.0.0.1:6379> move name 1
(integer) 1
127.0.0.1:6379> EXISTS name       ## 判断是否存在
(integer) 0
127.0.0.1:6379> EXPIRE name 5     ## 设置数据的过期时间
(integer) 1
127.0.0.1:6379> ttl name          ## 查看剩余的过期时间
```

> Redis 是单线程的!

Redis是很快的，官方表示，Redis是基于内存的操作，CPU并不是Redis的性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了！

Redis 是 C 语言写的，官方提供的数据为 100000+ QPS，完全不比同样是使用key-value的Memecache差！

> Redis 为什么单线程还这么快？

核心: Redis 是将所有的数据全部放在内存中的，所以说使用单线程效率就是最高的，多线程会存在CPU上下文切换，反而耗时。对于内存系统来说，如果没有上下文切换，那么效率就是最高的！

# 五大数据类型
## Redis-Key

