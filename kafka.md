# kafka

## 1.定义

这里学的是kafka3.0

记录用户到网站的一些行为数据，-->Log日志服务器-->Flume-->Hadoop集群

Flume采集速度<100M/S

Hadoop上行速度也就100m/s左右

11/11这种采集速度一般>200m/s

那么采集数据和获取数据速度不一致，就会需要一个容器缓存，这就是

![image-20220602103158906](C:\Users\L\Desktop\文档\photo\image-20220602103158906.png)

**kafka**：是一个分布式的基于发布/订阅模式的**消息队列**，一般用于大数据实时处理领域。

**发布/订阅**：消息的发布者不会将消息直接发送给特定的订阅者，而是将其分为不同类别。消费者只获取自己感兴趣的消息。

Kafka：是一个开源的分布式事件流平台，被用于高性能数据管道，流分析，数据集成，关键人物应用。

## 消息队列

常见的MQ(message queue)

:**Kafka,ActiveMQ,RabbitMQ,RocketMQ**

大数据场景一般用Kafka

J2EE一般RabbitMQ

#### 场景：

- 缓存/消峰：
- 解耦：
  - 源：MySQL,Oracle,Flume
  - 目的地：Hadoop,Flink,SpringBoot
  - 有了消息队列就把数据源写到MQ里，MQ再提供数据给后端
- 同步处理：必须一步一步来
- 异步处理：你注册了，把短信请求丢入MQ，然后直接返回响应注册成功

#### **消息队列的两种模式**

**点对点模式：**

p2p:生产者生产数据丢入MQ,消费者拿到信息后确认收到再MQ中删除对应的消息

![image-20220602105208475](C:\Users\L\Desktop\文档\photo\image-20220602105208475.png)

**发布/订阅模式：**

消费者消费数据之后并不删除数据，就像POP3和IMAP一样，POP3一旦邮件到了用户磁盘里，就把它删掉。而IMAP不会，只有用户认为是无用信息并把其删除掉时才会删除掉。

- 可以有多个topic(浏览，点赞，收藏，评论等等)
- 消费者消费数据之后并不会删除数据
- 每个消费者相互独立，都可以消费数据(分布式~)

![image-20220602105225708](C:\Users\L\Desktop\文档\photo\image-20220602105225708.png)

### kafka基础架构

Producer:生产者，外部来的数据

- 海量数据，分而治之，把一个topic分为多个partition

- 配合分区，消费者也有组，组内消费者并行消费，一个分区数据只能由一个消费者来消费

- 备份，防止某节点挂了，节点分为leader和follower(只做备份)。

  当leader挂了以后，follower可以成为新的leader。生产和消费只由leader来做。

kafka在2.8.0以前使用ZooKeeper记录节点如谁是leader，Kafka2.8.0以后可以配置不采用ZK而使用其他的比如Kraft

## Kafka快速入门

集群规划

| hadoop102 | hadoop103 | hadoop104 |
| --------- | --------- | --------- |
| zk        | zk        | zk        |
| kafka     | kafka     | kafka     |
