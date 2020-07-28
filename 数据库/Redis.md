## NoSQL概述

大数据时代

> 单机时代

存在的问题

1. 数据量太大，一个机器放不下
2. 数据的索引（B+ Tree），一个机器内存放不下
3. 访问量（读写混合），一个服务器承受不了

> Memcached（缓存）+ MySQL+垂直拆分

网站80%情况都是在读，每次去查询十分麻烦！减轻压力，使用缓存。

> 分库分表 + 水平拆分 + MySQL集群

早些年MyISAM：表锁

InnoDB：行锁

> 如今最近的年代

热榜实时更新，数据量大，变化快

MySQL关系型数据库不够用

## 什么是NoSQL

> Not Only SQL

泛指非关系型数据库

使用键值对来控制

> NoSQL特点

1. 方便扩展（数据之间没有关系，很好扩展）
2. 大数据量高性能（Redis 1秒写8万次，读11万，缓存记录性能高）
3. 数据类型多样（不需要设计数据库，随取随用）
4. 传统RDBMS和NoSQL

```
传统的 RDBMS
- 结构化组织
- SQL
- 数据和关系都存在单独的表中
- 操作操作，数据定义语言
- 严格的一致性
- 基础的事务
……
```

```
NoSQL
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库
- 最终一致性
- CAP定理和BASE
- 高性能，高可用，高可扩
…
```

> 大数据的3V和3高 

## NoSQL的四大分类

KV键值对：

- Redis

文档型数据库（bson格式和json）：

- MongoDB
  - 分布式文件存储的数据库，c++编写，处理大量文档
  - 介于关系型与非关系型数据库之间

列存储数据库：

图关系数据库：存关系

## Redis入门

> Remote Dictionary Server

能干啥？

1. 内存存储，持久化（rdb、aof）
2. 效率高，可以用于高速缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（浏览量）
6. ……

特性：

1. 多样数据类型
2. 持久化
3. 集群
4. 事务

### Windows安装

https://github.com/rgl/redis/downloads

### Linux安装

6.x以上版本需要更新gcc

```
tar -xzvf
make 
make install
```

修改配置文件后台启动

```
daemonize yes
```

### 测试性能

```shell
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```



### 基本命令

清空当前数据库`flushdb`

清空全部数据库`flushall`



基础知识

> Redis是单线程的

6.0.0以后是多线程

16个数据库，默认使用第0个





命令

```
set    #set key
get    #get key
keys * #查看所有key
exists #判断当前key是否存在
move   #移除当前key
expire #设置key时间过期
ttl    #查看当前key的剩余时间
type   #查看key类型
```



## 五大数据类型

### Redis-Key



### String

```shell
append [key] [string]	#追加字符串
strlen [key]   			#获取字符串长度
incr					#加一
decr					#减一
incrby [key] 10 		#一次加10（步长）
decrby					
getrange [key] 0 3		#截取字符串[0,3]（0 -1所有同get key）
setrange [key] 

setex					#set with expire
setnx					#set if not exist

mset
mget

127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) "v3"
127.0.0.1:6379> msetnx k1 v1 k4 v4   #原子性操作
(integer) 0
127.0.0.1:6379> get k4
(nil)

#对象
set user:{id}:{filed}


127.0.0.1:6379> getset db redis   	#如果不存在，相当于set
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongodb	#如果存在，先get再set
"redis"
127.0.0.1:6379> get db
"mongodb"

```



### List

```shell
127.0.0.1:6379> LPUSH list one
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1
1) "three"
2) "two"
127.0.0.1:6379> rpush list right
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "right"

127.0.0.1:6379> LPOP list			#移除list第一个
"three"	
127.0.0.1:6379> RPOP list			#移除list最后一个
"right"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 0		#通过下标获取值
"two"
127.0.0.1:6379> lindex list 1
"one"
127.0.0.1:6379> LLEN list			#获取长度
(integer) 2

lrem #移除list中指定个数的值

ltrim #通过下标截取指定长度，list只剩下截取的元素

rpoplpush #移除列表的最后一个元素到新列表

lset #将列表指定下标的值替换为另一个值，即更新操作

linsert #可指定前面后面位置插入
```



### Set

> 不重复

```
sadd myset 
smembers myset
sismember
srem	#移除指定元素
scard  	#set中元素个数
srandmember myset 	#随机抽出指定元素

spop	#随机删除
sdiff  	#差集
sinter	#交集
sunion	#并集
```



### Hash

> Map集合，key-map 本质和String没有什么区别，还是一个简单的key-value

```
hset myhash field value
hget
hmset
hmget
hgetall
hdel 
hlen	
hexists myhash field
hincrby
hsetnx
```

对象用hash存储方便

### Zset（有序集合）

```bash
zadd
zrange
zrangebyscore key min max
zcard	#获取有序集合中的个数
zcount	#获取指定区间的成员数量
```

## 三种特殊类型

### geospatial地理位置

Redis的Geo

```
GEOADD
GEODIST
GEOHASH
GEOPOSH
GEOADIUS

```

### Hyperloglog

> 什么是基数？（不重复的元素）

用来做基数统计的算法

```
PFADD mykey a b c d e f g h i j
PFCOUNT mykey
PFADD mykey2 i j k l m n o
PFMERGE mykey3 mykey2 mykey
```



### Bitmaps

> 位存储

两个状态0 1

```
setbit
getbit
bitcount
```

## 事务

Redis事务本质：一组命令的集合！一个事物中所有命令都会序列化，在事务执行过程中，会按照顺序执行！

一次性、顺序性、排他性！执行一些列的命令

==Redis单条命令保证原子性，但是事务不保证原子性==

Redis事务没有隔离级别的概念

Redis的事务：

- 开启事务（multi）
- 命令入队（……）
- 执行事务（exec）

> 正常执行事务

> 放弃事务

discard

> 编译异常  全部命令不执行



> 运行异常 其他命令正常执行



> 监控

### **悲观锁**：

- 认为什么时候都会出问题，无论做什么都加锁

### **乐观锁**：

- 认为什么时候都不会出现问题，所有不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据
- 获取version
- 更新的时候比较version

> Redis测监视测试



## Jedis





## Redis配置文件

1. 配置文件 unit单位 对大小写不敏感





## Redis持久化



### RDB（snapshotting）（Redis默认持久化）

==保存的文件是dump.rdb== 都是在配置文件快照中进行配置

> 触发机制

1. save的规则满足的情况下，会自动触发rdb规则
2. 执行flushall命令，也会触发rdb规则
3. 退出redis，也会产生rdb文件

备份就自动生成一个dump.rdb

> 恢复rdb文件

在redis启动目录中放rdb文件

```bash
save 900 1 	#在900秒（15分钟）之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照
save 300 10 #在300秒（5分钟）之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照
save 60 10000#在60秒（1分钟）之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照
```



### AOF（Append Only File）

==保存的文件是appendonly.aof==

**将我们所有命令记录下来，恢复的时候重新执行**

默认不开启，将appendonly no改为yes即可生效

```bash
appendfsync always  #每次有数据修改发生时都会写入AOF文件，这样会严重降低Redis的速度
appendfsync	everysec #每秒钟同步一次，显示地将多个命令同步到硬盘
appendfsync no		#让操作系统决定何时进行同步
```

重启，redis就可以生效了

aof文件有问题，无法启动redis

redis提供了一个工具redis-check-aof

## Redis发布订阅

```bash
redis 127.0.0.1:6379> SUBSCRIBE ylldgzh
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "ylldgzh"
3) (integer) 1
1) "message"
2) "ylldgzh"
3) "hello,yll"
1) "message"
2) "ylldgzh"
3) "hello,redis"
#等待读取信息
#发送端
redis 127.0.0.1:6379> PUBLISH ylldgzh "hello,yll"
(integer) 1
redis 127.0.0.1:6379> PUBLISH ylldgzh "hello,redis"
(integer) 1
redis 127.0.0.1:6379>
```



## Redis主从复制

是指将一台Redis服务器的内容，复制到其他Redis服务器，前者称为主节点，后者称为从节点

数据的复制是单向的。

主从复制，读写分离！

`info replication`

配置三台Redis服务器

### 一主二从

默认都是主节点，一般只需要配置从机

```bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379	#认主机
OK
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:down
master_last_io_seconds_ago:-1
master_sync_in_progress:0
slave_repl_offset:1
master_link_down_since_seconds:1592916819
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:94c953e3da093a084e25e06eb232fb7eb12e5d14
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
127.0.0.1:6380>
```

真实的配置是 在配置文件中配置，这样是永久的 用命令，是暂时的



主机可以写，从机不能写只能读！主机中所有信息和数据，都会复制到从机



测试:主机断开连接，从机依旧连接到主机，但是没有写操作，这个时候，主机如果回来了，从机依旧可以直接获取到主机写的信息！



主机断开，从机可使用命令	`slaveof no one`让自己变成主机下

## 哨兵模式

（自动选老大）

> 测试

1. 配置哨兵配置文件sentinel.conf

```
sentinel monitor myredis 127.0.0.1 6379 1
```

后面的数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的成为主机

```
redis-sentinel sentinel.conf  #开启哨兵
```

主机宕掉后再回来只会成为新主机的从机 



## Redis缓存穿透和雪崩

> 服务的高可用问题

### 缓存穿透

> 差不多

用户想要查询数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多时。缓存都没有命中，于是都去请求持久层数据库。这给持久层数据库造成很大的压力，这时候相当于出现了缓存穿透



> 解决方案

#### 布隆过滤器

是一种数据结构



#### 缓存空对象



### 缓存击穿

> 查询量太大，缓存过期



### 缓存雪崩

> 缓存集中过期失效，Redis宕机