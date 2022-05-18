# Redis

## NoSQL概述

为什么使用Nosql？

因为现在的时代是大数据时代

### 什么是NoSQL

NoSQL= No Only SQL

泛指非关系型数据库，随着web2.0互联网的诞生！传统的关系型数据库很难对付web2.0时代，特别是高大规模的高并发的社区

随着时代的进步，软件创新等，用户的个人信息，社交网络，地理位置，用户自己产生的数据，用户日志等爆发式的增长

我们需要使用Nosql处理以上的情况

而且很多的数据类型的存储不需要一个固定的格式。

### NoSQL特点

解耦：

1.方便拓展（数据之间没有挂你先，很好拓展！）

2.大数据量高性能（Redis一秒写八万次，读取十一万 ）

3.数据类型是多样性的！（不需要事先设计数据库！随取随用）

4.传统的RDBMS和NoSQL

~~~xml
传统的RDBMS
- 结构化组织
- SQL
- 数据和关系都存在单独的表中
- 数据定义语言
- 严格的一致性
- 基础的事务
- ...
~~~

~~~xml
Nosql
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库
- 最终一致性
- CAP定理和BASE（异地多活）
- 高性能，高可用，高可扩
- ...
~~~

> 了解：3V+3高

大数据时代的3V：主要是描述问题的

海量（Volume）
多样（Variety）
实时（Velocity）

大数据时代的3高：主要是对程序的要求

高并发
高可扩
高性能

### NoSQL的四大分类

- KV键值对
- 文档型数据库：

1. MongoDB
2. ConthDB

- 列存储数据库：

1. HBase
2. 分布式文件系统

- 图形数据库：

1. Neo4J
2. InfoGrid

## Redis入门

在usr/bin下

### 概述

> Redis（Remote Dictionary Server）

即远程字典服务！

是一个开源的使用ANSI C语言编写、支持网络、可基于内存也可持久化的日志型、Key-Value数据库，并且提供多种语言的API

被人们称为结构化数据库！

免费且开源

> Redis能干嘛

1、内存存储、持久化，内存中是断电即失、所以说持久化很重要（rdb、aof）

2.效率高、 可用用于高速缓存

3.发布订阅系统

4.地图信息分析

5.计时器、计数器

6........

> 特性

1. 多样化的数据类型
2. 持久化
3. 集群
4. 事务
5. ...

### 测试性能

redis-benchmark是一个压力测试工具

官方自带的性能测试工具

[root@KeKe bin]# redis-benchmark -h localhost -p 6379 -c 100 -n 100000

在本机测试，一百次并发每次十万个请求

### 基础知识

可用使用select进行切换数据库

```bash
127.0.0.1:6379> select 1#切换数据库
OK
127.0.0.1:6379[1]> 
```

~~~bash
127.0.0.1:6379[3]> dbsize#查看DB大小
(integer) 0
127.0.0.1:6379[3]> set name kk
OK
127.0.0.1:6379[3]> get name
"kk"
127.0.0.1:6379[3]> select 7
OK
127.0.0.1:6379[7]> dbsize
(integer) 0
127.0.0.1:6379[7]> get name
(nil)
~~~

~~~bash
127.0.0.1:6379[3]> keys *
1) "name"
#查看所有的key
~~~

~~~bash
127.0.0.1:6379[3]> flushdb#清除当前数据库
OK
127.0.0.1:6379[3]> keys *
(empty array)
~~~

清除全部数据库内容FLUSHALL

> Redis是单线程的

官方表示，Redis是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是根据及其的内存和网络带宽。

误区1：高性能的服务器不一定是多线程的

误区2：多线程效率不一定比单线程效率高

核心：redis是将所有的数据全部放在内存中的，所以说使用单线程去操作效率就是最高的，多线程（CPU上下文会切换：这是一个耗时的操作），对于内存系统来说，如果没有上下文切换效率是最高的，多次读写都是在一个CPU上的，在内存情况下，这个就是最佳的方案

## 五大数据类型

EXPIRE设置过期时间

### Redis-Key

~~~bash
127.0.0.1:6379> keys *
1) "age"
2) "name"
127.0.0.1:6379> EXISTS name#判断当前的key是否存在
(integer) 1
127.0.0.1:6379> EXISTS name1
(integer) 0
127.0.0.1:6379> move name 1#移除当前的key
(integer) 1
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> set name kk
OK
127.0.0.1:6379> get name
"kk"
127.0.0.1:6379> expire name 10
(integer) 1
127.0.0.1:6379> type age#查看key 的类型
string
~~~

### String

~~~bash
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> exists key1
(integer) 1
127.0.0.1:6379> APPEND key1 "hello"#在v1后面动态的追加字符
(integer) 7
127.0.0.1:6379> get key1
"v1hello"
~~~

~~~bash
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> incr views#加一
(integer) 1
127.0.0.1:6379> incr views
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views#减一
(integer) 1
127.0.0.1:6379> incrby views 2#设置步长，增量
(integer) 3
127.0.0.1:6379> incrby views 2
(integer) 5
127.0.0.1:6379> decrby views 2#设置步长，减量
(integer) 3
127.0.0.1:6379> decrby views 2
(integer) 1
127.0.0.1:6379> get key1
"hello,kk,rediss"
127.0.0.1:6379> getrange key1 5 7#获得一个区间的字符
",kk"
127.0.0.1:6379> setrange key1 8 xxxxxx#替换指定位置字符
(integer) 15
127.0.0.1:6379> get key1
"hello,kkxxxxxxs"
#setex（set with expire）#设置过期时间
#setnx（set if not exist）#不存在设置
127.0.0.1:6379> setex key3 30 v3
OK
127.0.0.1:6379> ttl key3#查看还有多少秒国企
(integer) 20
127.0.0.1:6379> setnx mykey "redis"
(integer) 1
127.0.0.1:6379> keys *
1) "mykey"
2) "key1"
127.0.0.1:6379> ttl key3
(integer) -2#已经过期
127.0.0.1:6379> setnx mykey "MongoDB"#因为mykey存在
(integer) 0#未执行成功
127.0.0.1:6379> get mykey
"redis"
~~~

~~~bash
127.0.0.1:6379> mset key1 v1 key1 v2 key3 v3#使用mset和mget可以同时设置多个值，但是要是kv键值对重复，后面的会覆盖前面的。且mset具有原子性，要么同时成功要么同时失败
OK
127.0.0.1:6379> get key1
"v2"
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 2
OK#设置一个对象user
127.0.0.1:6379> mget user:1:name user:1:age#获取对象user的name和age
1) "zhangsan"
2) "2"
#getset命令，先get再set
127.0.0.1:6379> GETSET k1 v1
(nil)
127.0.0.1:6379> GETSET k1 v2
"v1"
127.0.0.1:6379> get k1
"v2"
~~~

String类似的使用场景：

- 计数器
- 统计多单位的数量
- 粉丝量
- 对象缓存存储

### List

有序可重复

~~~bash
#使用LPUSH list value 进行插入
127.0.0.1:6379> LRANGE list 0 -1#输出，后进先出，类似一个盒子
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1
1) "three"
2) "two"
127.0.0.1:6379> Rpush list right#放到List最右边同理得Lpush
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"
#同理，以push得到pop
127.0.0.1:6379> RPOP list
"right"
127.0.0.1:6379> LPOP list
"left"
#通过下标查找
127.0.0.1:6379> lindex list 2
"one"
#获取长度
127.0.0.1:6379> LLEN list
(integer) 3
#移除掉指定个数的值
127.0.0.1:6379> lpush list three
(integer) 4
127.0.0.1:6379> lrem list 1 one
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 2 three
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
127.0.0.1:6379> lpush list three
(integer) 2
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> rPush list one
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LPUSH list four
(integer) 4
127.0.0.1:6379> LPUSH list five
(integer) 5
#通过下标截取指定的长度，而且list会被改变
127.0.0.1:6379> LTRIM list 2 3
OK
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
#rpoplpush移除列表的最后一个元素，将他移动到新的列表中
127.0.0.1:6379> RPOPLPUSH list list1
"two"
127.0.0.1:6379> lrange list1 0 -1
1) "two"
127.0.0.1:6379> lrange list 0 -1
1) "three"
#lset将list指定下标的值替换为另一个值，如果不存在或报错
127.0.0.1:6379> lset list 0 one
OK
127.0.0.1:6379> lrange list 0 -1
1) "one"
127.0.0.1:6379> lset list 1 two
(error) ERR index out of range
#可以使用linsert进行插入
127.0.0.1:6379> lrange list 0 -1
1) "one"
127.0.0.1:6379> LINSERT list after one zero
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "zero"
~~~

> 小结

list实际上是一个链表，before after left right都可以插入值

如果key不存在，创建新的链表

如果key存在，新增内容

如果移除了所有的值，空链表也代表不存在

在两边插入或者改动值效率最高，中间元素相对来说效率较低一点

### Set

无序不重复

~~~bash
#进行添加，s add
127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "kk"
(integer) 1
127.0.0.1:6379> sadd myset "lovekk"
(integer) 1
#s menbers查找set里面的所有成员
127.0.0.1:6379> SMEMBERS myset
1) "lovekk"
2) "kk"
3) "hello"
127.0.0.1:6379> sadd myset "lovekk"
(integer) 0
#由此可见不能插入重复的值
#获取set里由多少元素
127.0.0.1:6379> scard myset
(integer) 3
#移除
127.0.0.1:6379> srem myset hello
(integer) 1
#随机抽选一个元素
127.0.0.1:6379> SRANDMEMBER myset
"kk"
127.0.0.1:6379> SRANDMEMBER myset
"lovekk"
#spop随机删除一个元素
127.0.0.1:6379> SADD myset 1
(integer) 1
127.0.0.1:6379> SADD myset 2
(integer) 1
127.0.0.1:6379> SADD myset 3
(integer) 1
127.0.0.1:6379> SADD myset 4
(integer) 1
127.0.0.1:6379> SADD myset 5
(integer) 1
127.0.0.1:6379> spop myset
"1"
127.0.0.1:6379> spop myset
"4"
127.0.0.1:6379> spop myset
"2"
127.0.0.1:6379> spop myset
"5"
#用smove进行移动指定元素
127.0.0.1:6379> smove myset myset2 3
(integer) 1
127.0.0.1:6379> SMEMBERS myset2
1) "3"
#SDIFF取差集
127.0.0.1:6379> SDIFF myset myset1
1) "b"
2) "a"
#SINTER取交集
127.0.0.1:6379> SINTER myset myset1
1) "c"
#SUNION取并集
127.0.0.1:6379> sunion myset1 myset
1) "d"
2) "c"
3) "b"
4) "e"
5) "aha"
6) "a"
~~~

### Hash

Key-Map合集

~~~bash
#hash的set、get
127.0.0.1:6379> hset myhash field1 kk
(integer) 1
127.0.0.1:6379> hget myhash field1
"kk"
#获取全部
127.0.0.1:6379> hgetall myhash
1) "field1"
2) "hello"
3) "field2"
4) "world"
#删除某个field
127.0.0.1:6379> HDEL myhash field1
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "field2"
2) "world"
#获取长度
127.0.0.1:6379> HLEN myhash
(integer) 1
#判断field是否存在
127.0.0.1:6379> HEXISTS myhash field1
(integer) 0
127.0.0.1:6379> HEXISTS myhash field2
(integer) 1
#只获得所有的key
#只获得所有的values
127.0.0.1:6379> HKEYS myhash
1) "field2"
127.0.0.1:6379> HVALS myhash
1) "world"
#如果不存在则可以设置
127.0.0.1:6379> hsetnx myhash field4 4
(integer) 1
#如果存在则不可以设置
127.0.0.1:6379> hsetnx myhash field4 4
(integer) 0
#类似用户信息保存
127.0.0.1:6379> hset user:1 name kk
(integer) 1
127.0.0.1:6379> hget user:1 name
"kk"
~~~

### Zset

有序集合

~~~bash
#添加一个和多个
127.0.0.1:6379> zadd myset 1 one
(integer) 1
127.0.0.1:6379> zadd myset 2 two
(integer) 1
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
127.0.0.1:6379> zadd myset 3 three 4 four
(integer) 2
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"
4) "four"
#依照salary的大小，从小到大获取
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf
1) "yy"
2) "pp"
3) "kk"
#依照salary的大小，从大到小获取
127.0.0.1:6379> ZREVRANGE  salary 0 -1
1) "pp"
2) "yy"
#从小到大排序并且附带成绩
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores
1) "yy"
2) "2500"
3) "pp"
4) "5000"
5) "kk"
6) "50000"
#小于10000的从小到大排序
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 10000 withscores
1) "yy"
2) "2500"
3) "pp"
4) "5000"
#移除
127.0.0.1:6379> zrem salary kk
(integer) 1
#获取集合中的个数
127.0.0.1:6379> zcard salary
(integer) 2
#获取集合区间内有几个成员
127.0.0.1:6379> zcount myset 1 3
(integer) 3
127.0.0.1:6379> zcount myset 1 4
(integer) 4
~~~

应用场景：set 排序，存储班级成绩表，工资表

普通消息，重要消息，带权重进行判断

排行榜应用实现
