# Redis

可以使用ps -ef|grep redis查看redis是否开启了

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

## 三种特殊数据类型

### Geospatial

> geoadd

~~~bash
#geoadd添加地理位置
#规则：两极无法直接添加，我们一般会下载城市数据，直接通过java程序导入
#参数 key   值（经度 纬度 名称）
127.0.0.1:6379> GEOADD china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> GEOADD china:city 121.47 31.32 shanghai
(integer) 1
127.0.0.1:6379> GEOADD china:city 106.50 29.53 chongqing
(integer) 1
127.0.0.1:6379> GEOADD china:city 114.05 22.55 shenzhen
(integer) 1
127.0.0.1:6379> GEOADD china:city 120.16 30.24 hangzhou
(integer) 1
127.0.0.1:6379> GEOADD china:city 108.95 34.26 xian
(integer) 1
~~~

> geopos

~~~bash
#获取成员数据
127.0.0.1:6379> GEOPOS china:city hangzhou
1) 1) "120.1600000262260437"
   2) "30.2400003229490224"
~~~

> #### geodist

两人之间的距离

单位：

- m表示单位为米
- km表示单位为千米
- mi表示单位为英里
- ft表示单位为英尺

```BASH
#自动计算距离
127.0.0.1:6379> geodist china:city beijing shanghai km
"1058.2413"
```

> georadius

~~~bash
#以某个经纬度为中心，一定距离为半径，在这个⚪内的城市有哪些
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km
1) "chongqing"
2) "xian"
#可以使用withdist等自动计算距离等
~~~

> georadiusbymember

~~~bash
#以某个城市为中心，一定距离为半径在这个⚪内的城市
127.0.0.1:6379> GEORADIUSBYMEMBER china:city quanzhou 800 km withdist
1) 1) "shenzhen"
   2) "534.3930"
2) 1) "quanzhou"
   2) "0.0000"
3) 1) "hangzhou"
   2) "614.7000"
4) 1) "wuxi"
   2) "761.6426"
5) 1) "shanghai"
   2) "768.1322"
~~~

> geohash

该命令将返回11个字符的geohash字符串

~~~bash
#将经纬度转换为字符串，如果两个字符串越接近，那么距离越近
127.0.0.1:6379> GEOHASH china:city quanzhou
1) "wskme9r0820"
~~~

> GEO底层的实现原理其实就是Zset

所以我们可以使用zset的命令来使用geo

~~~bash
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "quanzhou"
5) "hangzhou"
6) "wuxi"
7) "shanghai"
8) "beijing"
~~~

### Hyperloglog

> 什么是基数

一个集合内不重复的元素的个数

> 简介

Redis Hyperloglog 基数统计的算法

优点：占用的内存是固定的，2^64不同的元素的技术，只需要花费12KB内存

网页的UV（unique Visitor，一个人访问一个网站多次，但是还是算作一个人）

传统的方式，set保存用户的id，然后就可以统计set中的元素数量作为判断标准

这个方式如果保存大量的用户id，就会比较麻烦，我们的目的是为了技术，而不是保存用户id；

有0.81％的错误率

> PFADD

~~~bash
#使用PFADD进行添加
127.0.0.1:6379> PFADD mykey2 i j z x c v b n m 
(integer) 1
~~~

> PFCOUNT

~~~bash
#使用PFCOUNT进行统计
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9
~~~

> PFMERGE

~~~bash
#使用PFMERGE进行合并（交集）
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 15
~~~

如果允许容错，使用Hyperloglog

如果不允许容错就使用set等

### Bitmaps

> 位存储

统计用户活跃不活跃，登录未登录，打卡未打卡这种两个状态的都可以使用Bitmaps

Bitmaps位图，数据结构，都是操作二进制来记录，只有0和1两个状态

365天=365bit 1字节=8bit  46个字节左右

用bitmap来记录周一到周日的打卡

周一：0  周二：1  周三：0  周四：1  周五：1  周六：0  周日：1

~~~bash
#通过setbit来存储
127.0.0.1:6379> setbit sign 0 0
(integer) 0
127.0.0.1:6379> setbit sign 1 1
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 1
(integer) 0
#通过getbit来获取
127.0.0.1:6379> getbit sign 2
(integer) 0
127.0.0.1:6379> GETBIT sign 6
(integer) 1
#通过bitcount获取签到了几天？
127.0.0.1:6379> BITCOUNT sign 0 -1
(integer) 4
~~~

## 事务

Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行的过程中会按照顺序执行

一次性、顺序性、排他性

~~~bash
-----队列 set set set 执行 -----
~~~

Redis事务没有隔离级别的概念

隔离性：一个事务内部的操作及使用的数据对其他事务是隔离的，并发执行的各个事务之间不能相互干扰。

所以Redis不会出现重复读，脏读、幻读

**一次性：指的是Redis中的事务只执行一次，在该事务执行结束之后，这条事务的生命周期就结束了，再次执行事务时需要重新开启事务。** 

**顺序性：指Redis的事务中的所有命令，都是按照先后顺序依次执行的。** 

**排他性：指事务在对某一个数据进行操作的同时，其他事务或其他命令不能对这个数据进行操作。**

**Redis单条命令保证原子性，但是事务不保证原子性**

Redis里所有的命令在事务中，并没有直接被执行，只有发起执行命令的时候才会执行。Exec

Redis的事务：

- 开启事务（Multi）
- 命令入队（其他命令）
- 执行事务（ecec）

> 正常执行事务

~~~bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) "v2"
4) OK
#需要取消事务通过DISCARD进行取消
~~~

> 编译行异常（代码有错误）事务中所有的命令都不会被执行

~~~bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> sea k1 v1
(error) ERR unknown command `sea`, with args beginning with: `k1`, `v1`, 
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
~~~

> 运行时异常（例1/0），如果事务队列中存在语法性问题，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常

~~~bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 "v1"
QUEUED
127.0.0.1:6379> incr k1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) ERR value is not an integer or out of range
3) OK
4) "v2"
~~~

## 乐观锁（例：秒杀）

悲观锁：担心任何时候都会出问题，无论做什么都会加锁

乐观锁：认为什么时候都不会出问题，所以不会山高！更新数据的时候去判断一下在此期间是否有人修改过这个数据

- 获取version
- 更新的时候比较version

> Redis监控测试

~~~bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money#监视money对象
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
------------------------------------------------
#而什么时候加锁会生效呢
比如说我们还有另外一个事务，在这个事务还未exec执行的时候，我们修改了money的数值，乐观锁就会让他监控的事务不执行
~~~

可以使用UNWATCH进行取消所有监控

**不管事务是否执行成功Redis都会取消WATCH监控**

## Jedis

> 什么是Jedis

Jedis是Redis官方推荐的Java连接开发工具，使用Java操作Redis中间件

> 测试

导入对应的依赖

~~~bash
<dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
    </dependencies>
~~~

2.编码测试：

- 连接数据库
- 操作命令
- 结束测试

~~~bash
    public static void main(String[] args) {
        //1.new Jedis
        Jedis jedis = new Jedis("127.0.0.1",6379);
        //jedis所有的命令就是之前学习的指令
        System.out.println(jedis.ping());
    }
~~~

剩下的要对redis进行操作的命令和redis没有区别，学习使用即可

[![OLhIjx.md.png](https://s1.ax1x.com/2022/05/20/OLhIjx.md.png)](https://imgtu.com/i/OLhIjx)

## SpringBoot整合

在SpringBoot2.x之后，原来使用的jedis被替换为了lettuce

Jedis：采用的直连，多个线程操作的话是不安全的

lettuce：采用netty，实例可以在多个线程中进行共享

1. 导入依赖
2. 配置连接

~~~properties
#SpringBoot所有的配置类都有一个自动配置类
#自动配置类都会绑定一个properties配置文件
#配置redis
spring.redis.host=localhost
spring.redis.port=6379
#现在只能使用lettuce的连接池不能使用jedis的连接池，因为在源码中jedis很多方法已经不生效了
~~~

3.测试

~~~java
class RedisSpringbootApplicationTests {
    @Autowired
    private RedisTemplate redisTemplate;
    @Test
    void contextLoads() {
        //opsForValue 操作字符串类似String
        //opsForList 操作List 类似List
        //...
        redisTemplate.opsForValue().set("name","kk");
        System.out.println(redisTemplate.opsForValue().get("name"));
        //获取redis的连接对象
        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        connection.flushDb();

    }

}
~~~

真实开发中一般使用json来传递对象

所以我们先新建一个pojo来实现存入参数

再使用 String jsonUser = new ObjectMapper.writeValueAsString（user）；

redisTemplate.opsForValue（）.set（“user”，jsonUser）

### 自定义RedisTemplate

因为源码里RedisAutoConfiguration里写的RedisTemplate有一个注释@ConditionalOnMissingBean(name = "redisTemplate")

告诉我们我们可以自己写一个redisTemplate来替换这个源码

~~~java
//为了开发方便使用所以我们改成  <String, Object>  
RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
    template.setConnectionFactory(factory);
// 序列化配置
    Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
    ObjectMapper om = new ObjectMapper();
    om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
    jackson2JsonRedisSerializer.setObjectMapper(om);
    StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
    // key采用String的序列化方式
    template.setKeySerializer(stringRedisSerializer);
    template.setHashKeySerializer(stringRedisSerializer);
    template.setValueSerializer(jackson2JsonRedisSerializer);
    template.setHashValueSerializer(jackson2JsonRedisSerializer);
    template.afterPropertiesSet();
    return template;
~~~

## Redis.conf详解

> 单位

[![OO8SqU.png](https://s1.ax1x.com/2022/05/20/OO8SqU.png)](https://imgtu.com/i/OO8SqU)

可以自己设置且我们可以看出redis对大小写不敏感 

> 包含

[![OOGJh9.png](https://s1.ax1x.com/2022/05/20/OOGJh9.png)](https://imgtu.com/i/OOGJh9)

可以包含其他配置文件，可以将多个redis.conf组合起来

> 网络

~~~bash
bind 127.0.0.1 # 绑定的ip
protected-mode yes # 保护模式
port 6379 # 绑定的端口号
~~~

>通用GENERAL

~~~bash 
daemonize yes #以守护进程的方式运行，默认是no，我们需要自己开启为yes
pidfile /var/run/redis_6379.pid #如果以后台的方式运行，我们就需要指定一个pid文件

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)
# warning (only very important / critical messages are logged)
loglevel debug#日志级别，可以自己设定

logfile /var/log/redis/redis.log#日志生成的位置、文件名

databases 16 #数据库的数量，默认是16个数据库

always-show-logo yes #是否显示logo
~~~

> 快照

持久化，在规定的时间内执行了多少操作，则会持久化到文件 .rdb

.aof

redis是内u你数据库，没有持久化的数据断电即失

~~~bash
#如果900s内，至少有1key进行了修改，我们就进行持久化操作
save 900 1
#如果300s内，至少有10key进行了修改，我们就进行持久化操作
save 300 10
#如果60s内，至少有10000key进行了修改，我们就进行持久化操作
save 60 10000
stop-writes-on-bgsave-error yes#持久化如果出错是否要继续进行工作
rdbcompression yes#是否压缩rdb文件，需要消耗一些cpu资源
rdbchecksum yes#压缩过程进行检查校验

dir /var/lib/redis#rdb文件保存的目录
~~~

> REPLICATION复制

> SECURITY

设置密码

~~~bash
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) ""
127.0.0.1:6379> config set requirepass 123456
OK
127.0.0.1:6379> auth 123456
OK
~~~

> 限制CLIENTS

~~~bash
maxclients 10000#设置能连接上redis的最大客户端的数量
maxmemory<bytes>#redis配置最大的内存容量
maxmemory-policy noeviction#内存到达上限之后的策略
#移除一些过期的key
#报错
#...
1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 

2、allkeys-lru ： 删除lru算法的key   

3、volatile-random：随机删除即将过期key   

4、allkeys-random：随机删除   

5、volatile-ttl ： 删除即将过期的   

6、noeviction ： 永不过期，返回错误
~~~

> APPEND ONLY模式AOF配置

~~~bash
appendonly no#默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分情况下RDB够用了
appendfilename "appendonly.aof" #持久化的文件的名字
# appendfsync always #每秒修改都会sync。消耗性能
appendfsync everysec #每秒执行一次sync，可能会丢失这1s的数据
# appendfsync no     #不执行sync，这个时候操作系统自己同步数据，速度最快
~~~

## Redis持久化

### RDB(Redis DataBase)

> 什么是RDB

[![OOahBq.png](https://s1.ax1x.com/2022/05/20/OOahBq.png)](https://imgtu.com/i/OOahBq)

在指定的时间间隔内将内存中的数据集快照写入磁盘，它回复时是将快照文件直接读取到内存里

> 怎么理解RDB？

RDB持久化是指在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程就是有一个fork子进程，先将数据集写入到临时文件中，写入成功后，再替换之前的文件，用二进制压缩存储。

将数据保存到临时RDB文件，全部保存完成后再替换，防止数据丢失？

**rdb保存的文件时dump.rdb**

保存路径 dir /var/lib/redis

> 触发机制

1. save的规则满足的情况
2. 执行flushall命令
3. 退出redis

备份就自动生成一个dump.rdb

> 如何恢复rdb文件

只需要将rdb文件放在redis启动目录就可以，redis启动的时候会自动检查dump.rdb，会自动恢复

优点：

1. 适合大规模的数据恢复！dump.rdb
2. 对数据的完整性要求不高

缺点：

1. 需要一定的时间进行操作，如果redis意外宕机了，最后一次的修改数据就没有了
2. fork进程的时候会占用一定的内容空间

### AOF(Append Only File)

将我们的所有命令都记录下来，恢复的时候就把文件再全部进行一次

[![OOwCd0.md.png](https://s1.ax1x.com/2022/05/20/OOwCd0.md.png)](https://imgtu.com/i/OOwCd0)

AOF持久化以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录。

AOF保存的是appendonly.aof文件

> 重写规则说明

AOF文件大于64MB会fork一个新的进程来将我们的文件进行重写

~~~bash
appendonly yes

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"
~~~

默认是不开启的我们需要手动进行配置

修改完需要重新启动redis

如果aof文件有错误，我们需要如何修复这个aof文件？

redis提供了一个工具类：redis-check-aof

如果文件正常会自动恢复

> APPEND ONLY模式AOF配置

~~~bash
appendonly no#默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分情况下RDB够用了
appendfilename "appendonly.aof" #持久化的文件的名字
# appendfsync always #每秒修改都会sync。消耗性能
appendfsync everysec #每秒执行一次sync，可能会丢失这1s的数据
# appendfsync no     #不执行sync，这个时候操作系统自己同步数据，速度最快
~~~

优点：

1. 每一次修改都同步，文件完整度更高
2. 每秒同步一次，最多丢失一秒的数据

缺点：

1. 相对于数据文件来说，aof内存远远大于rdb的内存，修复速度也更慢
2. AOF是一个读写操作，所以运行效率也要更慢，所以我们redis的默认设置是RDB

拓展：

如果同时开启两种持久化操作：

- redis重启的时候会有现在如AOF文件来恢复数据
- RDB的数据不实的时候，同时使用两者的服务器也只会找AOF文件。

性能建议：

- RDB文件只作后备用途，建议只再Slave（从机）上持久化RDB文件，而且只要十五分钟备份一次就行，只保留save 900 1这条规则
- 如果开启了AOF，代价是带来了持续的IO，好处是不会丢失超过两秒的数据，但是rewrite过程中的阻塞是不可避免的。只要硬盘允许，应该尽量减少AOF rewrite的频率，并且将AOF重写的基础适当调大64MB太小了，方便我们使用
- 如果不适用AOF可以剩下一大笔IO，代价是Master/Slave同时宕机会丢失十几分钟的数据，启动脚本也要比较两个之间的RDB文件，载入比较新的那一个，微博就是这种架构

## Redis主从复制

### 概念

主从复制，是指将一台Redis服务器的数据复制到其他的redis服务器，前者被称为主节点(master/leader)，后者被称为从节点(slave/follower);数据的复制是单向的，只能由主节点到从节点。Master以写为主，Slave以读为主

**默认情况下，每台Redis服务器都是主节点**

主从复制的作用包括：

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式
2. 故障恢复：当主节点出现问题的时候，可以由从节点提供服务实现快速的故障恢复；实际上是一种服务的荣誉
3. 负载均衡：在主从复制的基础上配合读写分离，可以很好的分担服务器负载，尤其在写少读多的场景下可以大大提高Redis服务器的并发量
4. 高可用（集群）基石：主从复制是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础



一般来说，在工程项目中不能只用一台Redis（宕机）：

1. 从结构上，单个Redis服务器会发生单点故障，并且一台服务器要处理所有的请求，压力较大
2. 从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内存为256G，也不能将所有内存用作存储内存，一般来说，单台Redis最大使用内存不应该超过20G

### 环境配置

只配置从库，不用配置主库

~~~bash
#查看当前库的信息
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:0#没有从机
master_replid:85658e2b8884cd5df19909dfe73b0e903cf87238
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
~~~

复制三个配置文件，修改对应的信息

端口号、pid、日志名字、rdb文件名字

搭建成功后如下图所示：

![image-20220521113847166](C:\Users\86150\AppData\Roaming\Typora\typora-user-images\image-20220521113847166.png)

#### 一主二从

主（79），从（80，81）

~~~bash
#主机查看从机信息
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6380,state=online,offset=126,lag=1
slave1:ip=127.0.0.1,port=6381,state=online,offset=126,lag=0
master_replid:df4d6a1029a34891865fe1dd282ea012a3f266cc
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:126
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:126
--------------------------------------------
#从机查看主机信息
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:2
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:df4d6a1029a34891865fe1dd282ea012a3f266cc
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
~~~

这样的命令是暂时的，要永久修改需要修改配置文件

> 主从复制

replicaof <masterip><masterport>

进行修改即可

> 细节

主机可以写，从机只能读

在从机宕机以后，重启之后只要从主机变回从机立马可以读取得到主机在宕机时候写入的数据。

### 原理

Slave在启动成功连接到master后会发送一个sync命令

Master接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，并完成一次完全同步

全量复制：Slave服务在接收到数据库文件数据后，将其存盘并且加载到内存中

增量复制：Master继续将心的所有收集到的修改命令依次传给slave，完成同步

但是只要是Slave重新连接到master，一次完全同步（全量复制）将被自动执行

从机怎么转变自己成主机

~~~bash
SLAVEOF no one
~~~

## 哨兵模式

（自动选取主机的模式）

> 概述

主从切换的技术是：当主服务器宕机需要手动把一台服务器切换为主服务器，这样需要人工干预，费时费力

在Redis2.8版本之后开始正是提供 Sentinel（哨兵）架构来解决问题

哨兵能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例**

[![OjZbX4.md.png](https://s1.ax1x.com/2022/05/21/OjZbX4.md.png)](https://imgtu.com/i/OjZbX4)

这里的哨兵有两个作用：

- 通过发送命令，监控Redis服务器的运行状态
- 当哨兵检测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服务器，修改配置文件让他们切换主机

然而一个哨兵进程对Redis服务器进行监控还是不够保险，为此我们使用多哨兵模式

[![Oje0u4.png](https://s1.ax1x.com/2022/05/21/Oje0u4.png)](https://imgtu.com/i/Oje0u4)

多哨兵模式：

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，因为这样看来仅仅是哨兵1主观认为主服务器不可用，这个现象我们称之为**主观下线**

当后面的哨兵也检测到主服务器宕机并且数量达到一定值的时候，哨兵之间就会进行一次投票，投票结果由一个哨兵发起，进行failover【故障转移】操作，切换成功后，就会发布订阅模式，各个哨兵把之间监控的从服务器实现切换主机，这个过程称为**客观下线**

> 测试

1.配置哨兵文件

~~~bash
#sentinel.conf
sentinel monitor myredis 127.0.0.1 6379 1#这个1是选举谁
~~~

2.启动哨兵

[![OjnRne.png](https://s1.ax1x.com/2022/05/21/OjnRne.png)](https://imgtu.com/i/OjnRne)

当我们关闭6379端口的服务器后

~~~bash
8642:X 21 May 2022 13:25:45.384 # +new-epoch 1
8642:X 21 May 2022 13:25:45.384 # +try-failover master myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.386 # +vote-for-leader ed03142e6c239cba65902989fb9a9b2e9434cdc5 1
8642:X 21 May 2022 13:25:45.386 # +elected-leader master myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.386 # +failover-state-select-slave master myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.444 # +selected-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.444 * +failover-state-send-slaveof-noone slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.544 * +failover-state-wait-promotion slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.679 # +promoted-slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.679 # +failover-state-reconf-slaves master myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:45.735 * +slave-reconf-sent slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:46.703 * +slave-reconf-inprog slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:46.703 * +slave-reconf-done slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:46.758 # +failover-end master myredis 127.0.0.1 6379
8642:X 21 May 2022 13:25:46.758 # +switch-master myredis 127.0.0.1 6379 127.0.0.1 6381
8642:X 21 May 2022 13:25:46.759 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6381
8642:X 21 May 2022 13:25:46.759 * +slave slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6381
8642:X 21 May 2022 13:26:16.762 # +sdown slave 127.0.0.1:6379 127.0.0.1 6379 @ myredis 127.0.0.1 6381
~~~

优点：

1. 哨兵集群，基于主从复制模式，所有主从复制的优点他都具备
2. 主从可以切换，故障可以转移，系统的可用性就会更好
3. 哨兵模式就是主从模式的升级，手动到自动，更加健壮

缺点：

1. Redis不好在线扩容，集群容量一旦到达上限，在线扩容就十分麻烦
2. 实现哨兵模式的配置其实是很麻烦的，里面由很多种选择

哨兵模式的全部配置

~~~bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
~~~

## 缓存穿透和雪崩

### 缓存穿透

#### 概念

进行读请求的时候先向缓存中进行查询，如果缓存中没有，这个时候就回去数据库中查询。

而当用户很多的时候，缓存都没有命中，于是都向持久层数据库进行请求，这对持久层数据库躁症很大的压力。这个时候就相当于出现了缓存穿透

#### 解决方案

[![OjKEM8.png](https://s1.ax1x.com/2022/05/21/OjKEM8.png)](https://imgtu.com/i/OjKEM8)

[![OjK1zV.png](https://s1.ax1x.com/2022/05/21/OjK1zV.png)](https://imgtu.com/i/OjK1zV)

缓存空对象会存在两个问题：

1. 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多的空值的键
2. 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会由影响

### 缓存击穿

例子：微博服务器缓存设置60s过期，而在60.1s的时候恢复，但是在这0.1s内还是接收到很多数据，这持续的大并发就击穿缓存直接请求数据库。

#### 概述

这里需要注意和缓存穿透的区别，缓存击穿是指一个非常热点的数据，用户们不停的大并发集中对这一个点进行访问，在这个key失效的过程，持续的大并发击穿缓存直接请求数据库。

当某个key在过期的瞬间，会有大量的请求并发访问，由于缓存过期，会同时访问数据库来查询最新的数据，并且回写缓存，导致数据库瞬间压力过大

#### 解决方案

##### 设置热点数据永不过期

##### 加互斥锁

分布式锁：使用分布式锁，保证每个key同时只有一个线程去查询后端服务，其他线程没有获得分布式锁的权限，只需要等待，这种方式把高并发的压力转移到了分布式锁，对分布式锁的考验很大

### 缓存雪崩

#### 概念

是指在某一个时间段，缓存集中过期失效，Redis宕机

[![OjMvAH.png](https://s1.ax1x.com/2022/05/21/OjMvAH.png)](https://imgtu.com/i/OjMvAH)

#### 解决方案

##### Redis高可用

增设几台redis，也就是搭建集群（异地多活）

##### 限流降级

在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待

##### 数据预热

数据加热的含义就是在正式部署前，把可能的数据都预先访问一次，把大量访问的数据加载到缓存中，设置不同的过期时间，让缓存失效的时间点尽量均匀
