# Dubbo+Zookeeper

### 简介

阿里SOA服务化治理方案的核心框架，也是一个分布式，高性能，透明化的RPC服务框架，提供服务自动注册，自动发现等高效服务治理方案，可以和Spring框架无缝集成。

### 核心功能

Remoting：网络通信框架，提供对多种NIO框架封装，包含同步转异步，请求-响应 模式的信息交换方式

Cluster: 服务框架，提供基于接口方法的透明远程贵哦成调用，包含多协议支持，软负载均衡，失败容错，地址路由，动态配置等集群支持

Registry：服务注册，基于注册中心目录服务，使服务消费方能动态的查找服务提供方，使地址透明，使服务提供方可以平滑增加或减少机器。

### 与Spring Cloud区别

Dubbo是SOA时代(面向服务的架构)的产物，主要关注服务调用，流量分发，流量监控与熔断。

​	使用Netty的NIO框架，基于TCP传输，配合Hession序列化完成RPC通信

Spring Cloud诞生于微服务架构时代，考虑的是微服务治理，是一个生态

​	基于HTTP协议+REST接口调用远程过来的通信。

http会有更大报文，占更多带宽，不过REST比RPC更灵活

## 架构

### 核心组件

- Provider：服务提供者
- Customer：调用服务的消费者
- Registry：服务注册与发现的注册中心
- Container：服务运行容器

### 架构层次

- 服务接口层：Service，业务逻辑
- Config：对外配置接口
- 服务代理层：服务接口透明代理Proxy
- 服务注册层：封装服务地址的注册与发现
- 集群层Cluster：封装多个提供者的路由及负载均衡
- 监控层Monitor：RPC调用次数和调用时间监控
- 远程调用层Protocol：封装RPC调用
- 信息交换层Exchange：封装请求响应模式，同步转异步

### 服务如何注册与发现

1）Provider（提供者）绑定指定端口并启动服务；

2）指供者连接注册中心，并发本机 IP、端口、应用信息和提供服务信息发送至注册中心存储；

3）Consumer（消费者）连接注册中心 ，并发送应用信息、所求服务信息至注册中心；

4）注册中心根据消费者所求服务信息匹配对应的提供者列表发送至 Consumer 应用缓存；

5）Consumer 在发起远程调用时基于缓存的消费者列表择其一发起调用；

6）Provider 状态变更会实时通知注册中心、在由注册中心实时推送至Consumer。

### Dubbo 注册流程的好处

1）Consumer 与 Provider 解偶，双方都可以横向增减节点数。

2）注册中心可做对等集群，可动态增减节点，宕机后自动切换。

3）去中心化，双方不直接依懒注册中心，即使注册中心全部宕机短时间内也不会影响服务的调用。

4）服务提供者无状态，任意一台宕掉后，不影响使用。

### Dubbo 支持哪些注册中心

- multicast：基于网络中组播传播实现，去中心化，只要广播地址，就能进行服务注册与发现
- Zookeeper：基于分布式协调系统实现，利用watch机制实现数据变更
- Redis：key存储服务名和类型，Map中key存储服务URL，value存储服务过期时间。基于redis的发布-订阅模式通知数据变更
- Simple：标准RPC服务

### Dubbo 支持哪些通信协议

Dubbo 支持 dubbo、rmi、hessian、http、webservice、thrift、redis 等多种协议，但是官网推荐使用 dubbo 协议。

同时，Dubbo 允许配置多协议，即**在不同服务上支持不同协议或者同一服务上同时支持多种协议**。

Dubbo 采用了什么序列化框架？
Dubbo 支持多种协议，而不同的协议默认使用的序列化框架也不同。如：

dubbo 协议默认使用 Hessian2 序列化。（Hessian2 是阿里在 Hessian 基础上进行的二次开发，故起名为 Hessian2）
rmi 协议默认为 Java 原生序列化。
http 协议默认为为 Json。
此外，

hessian 协议默认是 hessian 序列化。
webservice 协议默认是 soap 文本序列化 。
除了上面说的默认序列化框架外，也可以指定使用第三方的序列化框架，如 Kryo、FST 等。

### 负载均衡策略

- RandomLoadBalance	随机负载均衡
- RoundRobinLoadBalance 轮询负载均衡
- LeastActiveLoadBalance 最少活跃负载均衡：相同活跃数的随机，活跃数指调用前后计数差，慢的Provider收到更少请求，因为越慢的 Provider 的调用前后计数差会越大。
- ConsistentHashLoadBalance 一致性哈希负载均衡：相同请求落在同一台机器上

### 负载均衡粒度以及优先级

如果不指定负载均衡，默认使用随机负载均衡。我们也可以根据自己的业务显式指定一个负载均衡。

可以在多个地方类来配置负载均衡，如 Provider 的服务或方法上，Consumer 的服务或方法上。

和 Dubbo 其它配置类似，多个配置是有覆盖关系的：Consumer 方法 > Consumer 接口 > Provider 方法 > Provider 接口。

### Dubbo 集群的容错策略？

正常情况下，当我们进行系统设计时候，不仅要考虑正常逻辑下代码该如何走，还要考虑异常情况下代码逻辑应该怎么走。当服务消费方调用服务提供方的服务出现错误时候，Dubbo 提供了多种容错方案，缺省模式为 failover，也就是失败重试。

Failover Cluster（失败重试）
当服务消费方调用服务提供者失败后自动切换到其他服务提供者服务器进行重试。这通常用于读操作或者具有幂等的写操作，需要注意的是重试会带来更长延迟。可通过 retries="2" 参数来设置重试次数（不含第一次）。

Failfast Cluster（快速失败）
当服务消费方调用服务提供者失败后，立即报错，也就是只调用一次。通常这种模式用于非幂等性的写操作。

Failsafe Cluster（失败安全）
当服务消费方调用服务出现异常时，直接忽略异常。这种模式通常用于写入审计日志等操作。

Failback Cluster（失败自动恢复）
当服务消费端用服务出现异常后，在后台记录失败的请求，并按照一定的策略后期再进行重试。这种模式通常用于消息通知操作。

Forking Cluster（并行调用）
当消费方调用一个接口方法后，Dubbo Client 会并行调用多个服务提供者的服务，只要一个成功即返回。这种模式通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks="2" 参数来设置最大并行数。

Broadcast Cluster（广播调用）
当消费者调用一个接口方法后，Dubbo Client 会逐个调用所有服务提供者，任意一台调用异常则这次调用就标志失败。这种模式通常用于通知所有提供者更新缓存或日志等本地资源信息。

如上，Dubbo 本身提供了丰富的集群容错模式，但是如果您有定制化需求，可以根据 Dubbo 提供的扩展接口 Cluster 进行定制。

## Zookeeper

简介：zookeeper是一个开放源码的分布式协调服务，是集群的管理者，监视各个节点状态及反馈并进行合理操作。最终将简单易用的接口与高效稳定的系统提供给用户。分布式系统可以基于Zookeeper实现诸如数据发布/订阅，负载均衡，命名服务，分布式协调/通知，集群管理，Master选举，分布式锁，分布式队列等功能

其包含了**文件系统**与**通知机制**

- 文件系统：提供一个多层级的节点命名空间，与文件系统不同，这些节点都可以设置关联的数据，而文件系统中只有文件节点可以存放数据而目录节点不行
- 为了保证高吞吐与低延迟，在内存中维护了这个树状的目录结构，使得Zookeeper不可以存放大量数据，每个节点上限为1M.

### ZAB协议

一种支持崩溃恢复的原子广播协议，两种基本模式：崩溃恢复和消息广播

当集群刚启动或者leader宕机，重启或者网络古筝而导致过半服务器不能与leader保持正常通信时，所有进程进入崩溃恢复模式，选举新的leader，接着集群中其余Follower服务器开始与新leader服务器进行数据同步，当集群中超过半数机器完成同步后，退出恢复进入消息广播模式，leader开始接收客户端的事务请求生成事务提案来进行事务请求处理

### 四种类型的数据节点Znode

- PERSISTENT-持久节点：除非主动删除，否则将一直存在于Zookeeper中
- EPHEMERAL-临时节点，生命周期与客户端会话绑定，一旦会话失效，则所有改客户端创建的临时节点都会被移除
- PERSISTENT_SEQUENTIAL-持久顺序节点，增加了顺序，节点后增加一个父节点维护的自增数字
- EPHEMERAL_SEQUENTIAL-临时顺序节点

### Watcher机制-数据变更

允许客户端向服务端某Znode注册一个Watcher监听，当服务端一些事件触发了Watcher，会向指定客户端发送一个时间通知实现分布式通知功能

工作机制：

1、客户端注册watcher

2、服务端处理watcher

3、客户端回调watcher

Watcher特性总结

1、一次性

无论是服务端还是客户端，一旦一个Watcher被触发，Zookeeper都会将其从相应的存储中移除。这样的设计有效的减轻了服务端的压力，不然对于更新非常频繁的节点，服务端会不断的向客户端发送事件通知，无论对于网络还是服务端的压力都非常大。

2、客户端串行执行

客户端Watcher回调的过程是一个串行同步的过程。

3、轻量

3.1、Watcher通知非常简单，只会告诉客户端发生了事件，而不会说明事件的具体内容。

3.2、客户端向服务端注册Watcher的时候，并不会把客户端真实的Watcher对象实体传递到服务端，仅仅是在客户端请求中使用boolean类型属性进行了标记。

4、watcher event异步发送watcher的通知事件从server发送到client是异步的，这就存在一个问题，不同的客户端和服务器之间通过socket进行通信，由于网络延迟或其他因素导致客户端在不通的时刻监听到事件，由于Zookeeper本身提供了ordering guarantee，即客户端监听事件后，才会感知它所监视znode发生了变化。所以我们使用Zookeeper不能期望能够监控到节点每次的变化。Zookeeper只能保证最终的一致性，而无法保证强一致性。

5、注册watcher getData、exists、getChildren

6、触发watcher create、delete、setData

7、当一个客户端连接到一个新的服务器上时，watch将会被以任意会话事件触发。当与一个服务器失去连接的时候，是无法接收到watch的。而当client重新连接时，如果需要的话，所有先前注册过的watch，都会被重新注册。通常这是完全透明的。只有在一个特殊情况下，watch可能会丢失：对于一个未创建的znode的exist watch，如果在客户端断开连接期间被创建了，并且随后在客户端连接上之前又删除了，这种情况下，这个watch事件可能会被丢失。

###  客户端注册 Watcher实现

1、调用getData()/getChildren()/exist()三个API，传入Watcher对象

2、标记请求request，封装 Watcher到WatchRegistration

3、封装成Packet 对象，发服务端发送request

4、收到服务端响应后，将Watcher注册到ZKWatcherManager中进行管理

5、请求返回，完成注册。

### 服务端处理Watcher实现

1、服务端接收Watcher并存储接收到客户端请求，处理请求判断是否需要注册Watcher，需要的话将数据节点的节点路径和 ServerCnxn（ServerCnxn代表一个客户端和服务端的连接，实现了Watcher的process接口，此时可以看成一个Watcher对象）存储在WatcherManager的WatchTable和watch2Paths中去。

2、Watcher触发

以服务端接收到setData() 事务请求触发 NodeDataChanged事件为例：

2.1 封装WatchedEvent将通知状态（SyncConnected）、事件类型（NodeDataChanged）以及节点路径封装成一个WatchedEvent 对象

2.2 查询Watcher从WatchTable中根据节点路径查找Watcher

2.3 没找到；说明没有客户端在该数据节点上注册过Watcher

2.4 找到；提取并从WatchTable和Watch2Paths中删除对应 Watcher（从这里可以看出Watcher在服务端是一次性的，触发一次就失效了）

3、调用process方法来触发Watcher

这里process主要就是通过ServerCnxn对应的TCP连接发送Watcher事件通知
