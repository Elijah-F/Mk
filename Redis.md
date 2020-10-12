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
127.0.0.1:6379> move name 1       ## 删除
(integer) 1
127.0.0.1:6379> EXISTS name       ## 判断是否存在
(integer) 0
127.0.0.1:6379> EXPIRE name 5     ## 设置数据的过期时间
(integer) 1
127.0.0.1:6379> ttl name          ## 查看剩余的过期时间
127.0.0.1:6379> TYPE name         ## 查看数据类型
string
```

> Redis 是单线程的!

Redis是很快的，官方表示，Redis是基于内存的操作，CPU并不是Redis的性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了！

Redis 是 C 语言写的，官方提供的数据为 100000+ QPS，完全不比同样是使用key-value的Memecache差！

> Redis 为什么单线程还这么快？

核心: Redis 是将所有的数据全部放在内存中的，所以说使用单线程效率就是最高的，多线程会存在CPU上下文切换，反而耗时。对于内存系统来说，如果没有上下文切换，那么效率就是最高的！

# 五大数据类型
## String（字符串）

```shell
###################################################################################
127.0.0.1:6379> SET key1 v1
OK
127.0.0.1:6379> GET key1
"v1"
127.0.0.1:6379> APPEND key1 "hello"           ## 追加,如果不存在就相当于set key
(integer) 7
127.0.0.1:6379> GET key1
"v1hello"
127.0.0.1:6379> STRLEN key1                   ## 获取长度
(integer) 7
###################################################################################
127.0.0.1:6379> SET views 1
OK
127.0.0.1:6379> GET views
"1"
127.0.0.1:6379> INCR views                ## views++
(integer) 2
127.0.0.1:6379> INCR views
(integer) 3
127.0.0.1:6379> INCRBY views 5            ## views+=5
(integer) 8
127.0.0.1:6379> DECR views                ## views--
(integer) 7
127.0.0.1:6379> DECRBY views 3            ## views-=3
(integer) 4
127.0.0.1:6379> GET views
"4"
###################################################################################
127.0.0.1:6379> SET key1 "what the fuck!"
OK
127.0.0.1:6379> GET key1
"what the fuck!"
127.0.0.1:6379> GETRANGE key1 0 3         ## 截取 [0,3]
"what"
127.0.0.1:6379> GETRANGE key1 0 -1        ## 截取全部字符串
"what the fuck!"
127.0.0.1:6379> SETRANGE key1 9 shit      ## 从第9个字符开始替换
(integer) 14
127.0.0.1:6379> GET key1
"what the shit!"
###################################################################################
# setex(set with expire)   # 设置过期时间
# setnx(set if not exist)  # 不存在则设置
127.0.0.1:6379> setex key3 30 "hello"
OK
127.0.0.1:6379> TTL key3
(integer) 25
127.0.0.1:6379> GET key3
"hello"
127.0.0.1:6379> KEYS *
1) "key3"
2) "mykey"
3) "key1"
127.0.0.1:6379> TTL key3
(integer) -2
127.0.0.1:6379> KEYS *
1) "mykey"
2) "key1"
127.0.0.1:6379> SETNX mykey "MongoDB"    ## mykey已经存在, 0:表示设置失败
(integer) 0
127.0.0.1:6379> GET MYKEY
"redis"
###################################################################################
127.0.0.1:6379> MSET k1 v1 k2 v2           ## 批量设置多个值
OK
127.0.0.1:6379> MGET k1 k2                 ## 批量获取多个值
1) "v1"
2) "v2"
127.0.0.1:6379> MSETNX k1 v1 k3 v3
(integer) 0
127.0.0.1:6379> GET k3                     ## MSETNX是原子操作
(nil)

# 这里的key是一个巧妙的设计:  user:{id}:{age}
127.0.0.1:6379> mset user:1:name fuck user:1:age 20
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "fuck"
2) "20"
###################################################################################
# getset 先 get 在 set
127.0.0.1:6379> GETSET db redis          ## 如果不存在就返回nil，并设置新的值
(nil)
127.0.0.1:6379> GET db
"redis"
127.0.0.1:6379> GETSET db mongodb        ## 如果存在，就返回原来的值，并设置新的值
"redis"
127.0.0.1:6379> GET db
"mongodb"
###################################################################################
```

## List
> 本质上是一个链表

```shell
###################################################################################
127.0.0.1:6379> LPUSH list one          ## 头插
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> RPUSH list right        ## 尾插
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
127.0.0.1:6379> LPOP list               ## 头删
"three"
127.0.0.1:6379> RPOP list               ## 尾删
"right"
###################################################################################
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> LINDEX list 0           ## 获取对应下标的值
"two"
###################################################################################
127.0.0.1:6379> LLEN list               ## 获取长度
(integer) 2
###################################################################################
127.0.0.1:6379> LPUSH list one
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1
1) "one"
2) "two"
3) "one"
127.0.0.1:6379> LREM list 2 one             ## 删除两个 "one", 根据value来删除
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
###################################################################################
127.0.0.1:6379> RPUSH list 1
(integer) 2
127.0.0.1:6379> RPUSH list 2
(integer) 3
127.0.0.1:6379> RPUSH list 3
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "1"
3) "2"
4) "3"
127.0.0.1:6379> LTRIM list 1 2              ## 截断 [1,2]
OK
127.0.0.1:6379> LRANGE list 0 -1
1) "1"
2) "2"
###################################################################################
127.0.0.1:6379> LRANGE list 0 -1
1) "1"
2) "2"
127.0.0.1:6379> RPOPLPUSH list list2        ## 从list的右侧删除，添加到list2的左侧
"2"
127.0.0.1:6379> LRANGE list 0 -1
1) "1"
127.0.0.1:6379> LRANGE list2 0 -1
1) "2"
###################################################################################
127.0.0.1:6379> LSET list 0 10000           ## 更改指定下表对应的值
OK
127.0.0.1:6379> lrange list 0 0
1) "10000"
127.0.0.1:6379> lset list 1 10000           ## 超出范围，报错
(error) ERR index out of range
###################################################################################
127.0.0.1:6379> LRANGE list 0 -1
1) "10000"
127.0.0.1:6379> LINSERT list BEFORE 10000 hello    ## 在指定value前插入
(integer) 2
127.0.0.1:6379> LINSERT list AFTER 10000 world     ## 在指定value后插入
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "hello"
2) "10000"
3) "world"
###################################################################################
```

## Set
> Set 中的值不能重复

```shell
###################################################################################
127.0.0.1:6379> SADD myset "hello"                ## 添加
(integer) 1
127.0.0.1:6379> SADD myset "world"
(integer) 1
127.0.0.1:6379> SADD myset "the"
(integer) 1
127.0.0.1:6379> SMEMBERS myset                    ## 查看所有
1) "hello"
2) "world"
3) "the"
127.0.0.1:6379> SISMEMBER myset hello             ## 判断是否存在
(integer) 1
127.0.0.1:6379> SISMEMBER myset fuck
(integer) 0
###################################################################################
127.0.0.1:6379> SCARD myset                       ## 查看元素个数
(integer) 3
127.0.0.1:6379> SREM myset the                    ## 移除指定元素
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "hello"
2) "world"
###################################################################################
127.0.0.1:6379> SMEMBERS myset
1) "hello"
2) "world"
127.0.0.1:6379> SRANDMEMBER myset                 ## 无序不重复集合,抽随机(1个)
"world"
127.0.0.1:6379> SRANDMEMBER myset
"world"
127.0.0.1:6379> SRANDMEMBER myset
"hello"
127.0.0.1:6379> SRANDMEMBER myset 2               ## 2个
"hello"
###################################################################################
127.0.0.1:6379> spop myset                        ## 随机移除
"world"
127.0.0.1:6379> SMEMBERS myset
1) "hello"
###################################################################################
127.0.0.1:6379> sadd myset hello
(integer) 1
127.0.0.1:6379> sadd myset world
(integer) 1
127.0.0.1:6379> sadd myset the
(integer) 1
127.0.0.1:6379> sadd myset2 set2
(integer) 1
127.0.0.1:6379> smove myset myset2 the           ## 移动制定的元素
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "hello"
2) "world"
127.0.0.1:6379> SMEMBERS myset2
1) "set2"
2) "the"
###################################################################################
127.0.0.1:6379> SADD myset a b c
(integer) 0
127.0.0.1:6379> SADD myset2 c d e
(integer) 3
127.0.0.1:6379> SDIFF myset myset2               ## 差集
1) "b"
2) "a"
127.0.0.1:6379> SDIFF myset2 myset
1) "d"
2) "e"
127.0.0.1:6379> SINTER myset myset2              ## 交集
1) "c"
127.0.0.1:6379> SUNION myset myset2              ## 并集
1) "a"
2) "c"
3) "e"
4) "d"
5) "b"
###################################################################################
```

## Hash
> Hash 更适合于对象的存储，String 更适合字符串的存储！

```shell
###################################################################################
127.0.0.1:6379> hset myhash field1 fuck                  ## 设置值
(integer) 1
127.0.0.1:6379> hget myhash field2                       ## 获取值
(nil)
127.0.0.1:6379> hget myhash field1
"fuck"
127.0.0.1:6379> hmset myhash field1 hello field2 world   ## 批量设置
OK
127.0.0.1:6379> hmget myhash field1 field2               ## 批量获取
1) "hello"
2) "world"
127.0.0.1:6379> hgetall myhash                           ## 获取所有值
1) "field1"
2) "hello"
3) "field2"
4) "world"
127.0.0.1:6379> hdel myhash field1                       ## 删除
(integer) 1
###################################################################################
127.0.0.1:6379> hlen myhash                              ## 获取长度
(integer) 1
127.0.0.1:6379> HEXISTS myhash field2                    ## 判断key是否存在
(integer) 1
###################################################################################
127.0.0.1:6379> hkeys myhash                             ## 获取所有的key
1) "field2"
127.0.0.1:6379> hvals myhash                             ## 获取所有的value
1) "world"
###################################################################################
```
