---
title: redis入门
date: 2022-02-02 17:15:12
tags:
---
## redis测试

```shell
# 测试： 100个并发连接  100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

## 五大数据类型

> **redis**是**单线程**的，可以用作**数据库**，**缓存**和**消息中间件MQ**

官方表示：redis是基于内存操作的，CPU不是redis性能瓶颈，redis的瓶颈是根据机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了！

为什么默认端口是6379：粉丝效应

redis默认有16个数据库，默认使用的是第0个数据库

可以使用select进行切换

[官方文档](https://redis.io/commands)

### Redis-key

```bash
root[/usr/local/bin]: redis-cli -p 6379
127.0.0.1:6379> select 3
OK
127.0.0.1:6379[3]> DBSIZE #查看DB大小
(integer) 0
127.0.0.1:6379[3]> keys * #查看所有的key
```

```bash
127.0.0.1:6379[3]>flushdb #清除当前数据库
```

```bash 
127.0.0.1:6379[3]>flushall #清空所有数据库
```

```bash
127.0.0.1:6379[3]>EXISTS age #判断age键是否存在，有为1，否则为0
(integer) 1
127.0.0.1:6379> exists age1
(integer) 0
```

```bash
127.0.0.1:6379> move age 1 #移除age , 1代表当前数据库
```

```bash
127.0.0.1:6379>expire age 10 # 10秒后过期
127.0.0.1:6379>ttl age #还剩多少时间过期
```

```bash
127.0.0.1:6379>type age #查看当前数据类型
string
```

### String（字符串）

```bash
127.0.0.1:6379> set key1 v1
OK
127.0.0.1:6379> append key1 hell  # 存在则追加，不存在则新建
(integer) 6
127.0.0.1:6379> get key1
"v1hell"
127.0.0.1:6379> strlen key1 # 获取字符串的长度
(integer) 6
```

```bash
127.0.0.1:6379> set views 0
OK
127.0.0.1:6379> incr views  # 自动加1
(integer) 1
127.0.0.1:6379> decr views # 自动减1
```

```bash
127.0.0.1:6379> incrby views 10 # 增加指定数值 同理decrby
(integer) 11
```

```bash
# 字符串范围 range
127.0.0.1:6379> set key1 "hello,kuangshen"
OK
127.0.0.1:6379> GETRANGE key1 0 3  # 截取[0,3]
"hell"
127.0.0.1:6379> GETRANGE key1 0 -1  # 取所有
"hello,kuangshen"
# 替换
127.0.0.1:6379> SETRANGE key2 1 xx # 从1开始替换字符串
(integer) 7
127.0.0.1:6379> GETRANGE key2 0 -1
"axxdefg"
##################################################################################
# setex (set with expire) # 设置过期时间
# setnx (set if not exist) # 不存在设置 （在分布式锁中会常用）
127.0.0.1:6379> setex key3 30 "sds" # key3 30秒存在时间
OK
127.0.0.1:6379> ttl key3
(integer) 26
127.0.0.1:6379> setnx mykey "hello" # 设置成功返回1 否则返回0 如果不存在则创建，如果存在就创建失败
(integer) 1
127.0.0.1:6379> setnx mykey "Mongo"
(integer) 0
127.0.0.1:6379> get mykey
"hello"
##################################################################################
# 同时设置（获取）多个键值对
mset
mget
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k1"
3) "k2"
127.0.0.1:6379> mget k1 k2 k3
1) "v1"
2) "v2"
3) "v3"
同样的 msetnx msetex 同理（原子性操作，要么一起成功要么一起失败）
# 关于对象
set user:1 {name:zhangsan, age:3} # 这是一个user:1 对象 值为json字符来保存对象！

# 这里key的设计: user:{id}:{filed}
# 冒号：代表一个层级，方便查看和管理
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 2
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "2"
##################################################################################
getset # 先get再set

127.0.0.1:6379> getset db redis  # 如果不存在返回nil 并创建
(nil)
127.0.0.1:6379> get db
"redis"
127.0.0.1:6379> getset db mongodb # 如果存在则返回当前值，并修改
"redis"
127.0.0.1:6379> get db
"mongodb"
127.0.0.1:6379> 


```

数据结构是想通的！

String类型使用场景：value除了是字符串还可以是数字

- 计数器
- 统计多单位数量
- 粉丝数
- 对象缓存存储

### List（列表）

在redis的list中，可以做成栈，队列，阻塞队列！redis不区分大小写命令

list内部可以存在重复值

注：只有push和pop才分左右，其余前置l均为list的意思

```shell 
##################################################################################
127.0.0.1:6379> LPUSH list one  # lpush 中的l指的是left，从左边插入，同理有rpush，相当于压栈
(integer) 1
127.0.0.1:6379> LPUSH list teo
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1 # 查所有， 都是从零开始计算的
1) "three"
2) "teo"
3) "one"
127.0.0.1:6379> LRANGE list 0 1
1) "three"
2) "teo"

##################################################################################
移除命令，同理有lpop和rpop

##################################################################################
获取下标值index，也是分lindex和rindex
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "teo"
3) "one"
4) "four"
127.0.0.1:6379> lindex list 0
"three"

##################################################################################
获取list长度，llen
127.0.0.1:6379> llen list
(integer) 4

##################################################################################
移除指定的值
lrem  （list remove）
因为list内部可以存在重复值，所以出现lrem key count value
127.0.0.1:6379[1]> lrange list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379[1]> lrem list 1 one
(integer) 1
127.0.0.1:6379[1]> lrange list 0 -1
1) "three"
2) "three"
3) "two"

##################################################################################
只保留一部分值
ltrim
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello2"
4) "hello3"
127.0.0.1:6379> ltrim mylist 1 2 # 通过下标截取指定长度
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"
127.0.0.1:6379>

##################################################################################
rpoplpush # 移除列表最后一个元素，并添加到新的列表中
rpoplpush source destination
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"
3) "hello"
127.0.0.1:6379> rpoplpush mylist myotherlist
"hello"
127.0.0.1:6379> lrange myotherlist 0 -1
1) "hello"
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"
127.0.0.1:6379>

##################################################################################
lset 更新元素（前提:该元素存在，否则会报错）
127.0.0.1:6379> lpush list value1
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "value1"
127.0.0.1:6379> lset list 0 item # 更新元素
OK
127.0.0.1:6379> lrange list 0 -1
1) "item"

##################################################################################
linsert 从列表中具体的值前面（或后面）插入值
linsert key before|after pivot value
127.0.0.1:6379> lrange list 0 -1
1) "value2"
2) "item"
127.0.0.1:6379> linsert list before item before
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "value2"
2) "before"
3) "item"
```

> 小结

- 实际上是一个链表
- key不存在就创建，存在则新增
- 如果移除了所有值，也就是空链表，则代表不存在
- 在两边插入或改值时，效率最高，中间元素效率会低一点。

### Set（集合）

set是一个无序不重复集合

```bash 
##################################################################################
127.0.0.1:6379> sadd myset lovekuangshen # 建立集合，增加值
(integer) 1
127.0.0.1:6379> smembers myset  # 查看集合内容
1) "lovekuangshen"
2) "kuangshen"
3) "hello"
127.0.0.1:6379> sismember myset hello # 判断hello是否存在于myset中
(integer) 1
127.0.0.1:6379>
##################################################################################
127.0.0.1:6379> scard myset # 查看集合中元素的个数
(integer) 3

##################################################################################
srem key value # 移除集合中的元素

##################################################################################
SRANDMEMBER key [count] 随机筛选元素，默认筛选个数为1

##################################################################################
随机删除key，指定删除key
spop随机移除元素

##################################################################################
将一个指定的值移动到另外一个set中
smove source destination member
127.0.0.1:6379> smove myset myset2 hello
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "lovekuangshen"
2) "kuangshen"
127.0.0.1:6379> SMEMBERS myset2
1) "hello"
2) "set2"
127.0.0.1:6379>
##################################################################################
微博，B站，共同关注！（并集）
数字集合类：
- 差集
- 交集
- 并集
127.0.0.1:6379> sadd set a
127.0.0.1:6379> sadd set b
127.0.0.1:6379> sadd set c
127.0.0.1:6379> sadd set2 c
127.0.0.1:6379> sadd set2 d
127.0.0.1:6379> sadd set2 e
127.0.0.1:6379> SDIFF set set2 # 找不同，也就是差集
1) "a"
2) "b"
127.0.0.1:6379> SINTER set set2 # 交集 可以实现共同好友
1) "c"
127.0.0.1:6379> SUNION set set2 # 并集
1) "a"
2) "c"
3) "b"
4) "e"
5) "d"
127.0.0.1:6379>
```

微博, A用户将所有关注的人放在一个set集合中 !将它的粉丝也放在一个集合中!
共同关注,共同爱好,二度好友,推荐好友!(六度分割理论)

### Hash（哈希）

map集合，key-map

```bash  
##################################################################################
127.0.0.1:6379> hset hash filed1 kuangshen  # 设置值
(integer) 1
127.0.0.1:6379> hget hash filed1 # 获取值
"kuangshen"
127.0.0.1:6379> hmset hash filed2 k2 filed3 k3 filed4 k4 # 同时设置多个值
OK
127.0.0.1:6379> hmget hash filed1 filed2 filed3 # 同时获取多个值
1) "kuangshen"
2) "k2"
3) "k3"
127.0.0.1:6379> hgetall hash # 获取全部数据
1) "filed1"
2) "kuangshen"
3) "filed2"
4) "k2"
5) "filed3"
6) "k3"
7) "filed4"
8) "k4"
127.0.0.1:6379> hdel hash filed1 # 删除指定字段
(integer) 1
127.0.0.1:6379> hgetall hash
1) "filed2"
2) "k2"
3) "filed3"
4) "k3"
5) "filed4"
6) "k4"

##################################################################################
127.0.0.1:6379> hlen hash # 获取长度
(integer) 3

##################################################################################
 HEXISTS hash filed2 # 判断元素是否存在
 ##################################################################################
 127.0.0.1:6379> hkeys hash # 获取所有key
1) "filed2"
2) "filed3"
3) "filed4"
127.0.0.1:6379> hvals hash # 获取所有值
1) "k2"
2) "k3"
3) "k4"

##################################################################################
 HINCRBY hash f1 1 # f1自增1 HINCRBY hash f1 -1 相当于自减
 127.0.0.1:6379> hsetnx hash f1 5 # 如果不存在则可以设置，存在则不行
(integer) 0
127.0.0.1:6379> hsetnx hash f2 5
(integer) 1

```

hash存变更数据 user name age，尤其是用户信息之类的，经常变动的信息，hash更适合于对象的存储，String更适合字符串的存储！

### zset（有序集合）

```bash 
127.0.0.1:6379> zadd myset 1 one 2 two 3 three # 添加三个值,数字是排序标志
(integer) 3
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> zadd salary 2500 xiaohong 5000 zhangsan 500 kuangshen
(integer) 3

##################################################################################
排序
# ZRANGEBYSCORE key min max withscores(选填，附带成绩等排序标志)
127.0.0.1:6379> ZRANGEBYSCORE myset (-1 (3 # (后两个是区间,有括号表示不包含该数字)
1) "one"
2) "two"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf # 全部用户从小到大排序(范围)
1) "kuangshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZREVRANGE myset 0 -1 # 从大到小排列
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores # 2500以内
1) "kuangshen"
2) "500"
3) "xiaohong"
4) "2500"
##################################################################################
zrem 移除元素
127.0.0.1:6379> zrem salary xiaohong
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "kuangshen"
2) "zhangsan"
##################################################################################
zcard key 查看元素个数
zcount key min max 统计区间值的个数
127.0.0.1:6379> zcount myset 1 2
(integer) 2

##################################################################################

```

案例思路： set排序，存储班级成绩表，工资排序表，排行榜

普通消息， 1， 重要消息， 2， 带权重进行判断！

## 三种特殊数据类型

### geospatial（地理位置）

朋友定位，附近的人，打车距离计算

[经纬度查询(获取测试数据)](https://jingweidu.bmcx.com/)

> geoadd (添加地理位置)

```bash 
##################################################################################
# 规则：地球两极无法添加， 一般直接通过java程序一次性导入
geoadd key 经度 纬度 名字
127.0.0.1:6379> geoadd china:city 116.23128 40.22077 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121 31 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106 29 chongqing
(integer) 1
```

> geopos (获取)

```bash 
127.0.0.1:6379> geopos china:city xian
1) 1) "108.00000160932540894"
   2) "34.00000062127011091"
```

> geodist (获取两点距离)

单位：

- **m** for meters.
- **km** for kilometers.
- **mi** for miles.
- **ft** for feet.

```bash 
127.0.0.1:6379> geodist china:city beijing chongqing
"1557966.1279"
127.0.0.1:6379> geodist china:city beijing chongqing km
"1557.9661"
```

> georadius

附近的人(获取所有附近的人的地址，定位！)

georadius 经度 纬度 半径 单位

withcoord 显示他人的定位信息

```bash  
127.0.0.1:6379> georadius china:city  110 30 1000 km
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "hangzhou"
127.0.0.1:6379> georadius china:city  110 30 1000 km count 2 # 限制显示的数量
1) "chongqing"
2) "xian"
127.0.0.1:6379> georadius china:city  110 30 1000 km withdist# 显示到(110,30)中心的距离
1) 1) "chongqing"
   2) "402.8557"
2) 1) "xian"
   2) "483.2155"
127.0.0.1:6379> georadius china:city  110 30 1000 km withcoord
1) 1) "chongqing"
   2) 1) "106.00000172853469849"
      2) "28.99999952347345555"
2) 1) "xian"
   2) 1) "108.00000160932540894"
      2) "34.00000062127011091"
```

> georadiusbymember

```bash  
# 找出位于指定元素周围的其他元素
127.0.0.1:6379> georadiusbymember china:city chongqing 1000 km
1) "chongqing"
2) "xian"
```

> geohash - 返回一个或多个位置元素的个geohash表示

该命令返回11个字符的geohash字符串

```bash  
# 将二维的经纬度转换成一维的字符串，，如果两个字符串越接近，那么距离越近。
127.0.0.1:6379> geohash china:city beijing chongqing
1) "wx4sucvncn0"
2) "wm5kup6c5p0"
```

> geo 底层实现原理就是zset，可以使用zset命令操作geo！

```bash 
127.0.0.1:6379> zrange china:city 0 -1
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "hangzhou"
5) "shanghai"
6) "beijing"
127.0.0.1:6379> zrem china:city hangzhou # 移除geo建立的元素
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "chongqing"
2) "xian"
3) "shenzhen"
4) "shanghai"
5) "beijing"
```

### Hyperloglog

基数（一个集合中不重复的元素）

> 简介：用于做基数统计的算法

优点：占用内存固定， 2^64个不同的元素的基数，只需要废12KB的内存，从内存角度看Hyperloglog是首选

0.81%错误率！统计UV任务是可以忽略不计的。

```bash 
127.0.0.1:6379> pfadd mykey a b c d e f g h i j # 添加
(integer) 1
127.0.0.1:6379> PFCount mykey # 计数，统计基数数量
(integer) 10
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2 # 合并
OK
127.0.0.1:6379> PFCOUNT mykey3
(integer) 15

```

允许容错，一定可以使用Hyperloglog

不允许容错，就使用set或者自己的数据类型即可

### Bitmaps

> 按位存储

统计疫情感染人数：000000110101010

统计用户信息：活跃，不活跃！登录，未登录！365打卡！两个状态的都可以使用bitmaps

Bitmaps位图，数据结构！都是操作二进制位来进行记录的，只有0和1两种状态

> 测试

```bash  

127.0.0.1:6379> setbit sign 0 1 # 设置七天的打卡状态，0-6>周一到周天
127.0.0.1:6379> setbit sign 1 0
127.0.0.1:6379> setbit sign 2 0
127.0.0.1:6379> setbit sign 3 1
127.0.0.1:6379> setbit sign 4 1
127.0.0.1:6379> setbit sign 5 0
127.0.0.1:6379> setbit sign 6 0
127.0.0.1:6379> getbit sign 3 # 确认周四有没有打卡
(integer) 1
# 统计的操作，统计打卡天数，默认全部
127.0.0.1:6379> bitcount sign
(integer) 3
```

## redis的事务

==redis的单条命令可以保证原子性（要么同时成功，要么同时失败），但是redis的事务不行==

redis事务本质：一组命令的集合。一个事务中的所有命令都会被序列化，在事务的执行过程中，会按照顺序执行。

一次性，顺序性，排他性。执行一系列的命令！

==redis事务没有隔离级别的概念==

所有命令在事务中，并没有被直接执行！只有发起执行命令的时候才会执行！

redis事务：

- 开启事务（multi）
- 命令入队（......）
- 执行事务（exec）

> 正常执行事务

```bash 

127.0.0.1:6379> multi # 开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec # 执行事务
1) OK
2) OK
3) "v2"
4) OK

```

> 放弃事务

```bash  

127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> DISCARD # 取消事务
OK
127.0.0.1:6379> get k4 # 事务中队列的命令都不会被执行
(nil)
```

> 编译型异常（代码有问题，命令有错），事务中所有的命令都不会被执行

```bash 
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k1
(nil)
```

> 运行时异常（例如：1/0），如果事务队列中存在语法性错误，那么执行命令的时候，其他命令是可以正常执行的。错误命令会抛出异常。

```bash 
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec # 虽然第一条错了，但是后面仍然执行成功
1) (error) ERR value is not an integer or out of range
2) OK
3) OK
4) "v3"
127.0.0.1:6379> get k2
"v2"
```

 

> 监控 watch

悲观锁：

- 很悲观，认为什么时候都会出现问题，无论做什么都会加锁！

乐观锁：

- 很乐观，认为什么时候都不会出现问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据。
- 获取version，更新的时候去比较version

> 监视测试

```bash 
127.0.0.1:6379> set money 100
127.0.0.1:6379> set out 0
127.0.0.1:6379> watch money  # 监视money对象
127.0.0.1:6379> multi # 事务正常结束，数据期间没有发生变动，这个时候正常执行成功！(一旦事务执行后，监视会自动取消)
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

测试多线程修改值，使用watch相当于redis的乐观锁操作

```bash  
127.0.0.1:6379> WATCH money
127.0.0.1:6379> multi
127.0.0.1:6379> DECRBY money 10
QUEUED
127.0.0.1:6379> INCRBY out 10
QUEUED
127.0.0.1:6379> exec # 执行之前。另外一个线程修改了这个值，这个时候，会导致事务执行失败！（无论失败成功exec都会自动解锁）
(nil)
127.0.0.1:6379> unwatch # 停止监视，之后再重新监视执行
OK
127.0.0.1:6379> watch money
OK
```

