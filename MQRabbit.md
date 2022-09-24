# MQRabbit

### 第一种模型

![请添加图片描述](https://img-blog.csdnimg.cn/5fa803dc8afd4f2293ebdf60faf52962.jpg)

在上图的模型中，有以下概念：

1. 生产者，也就是要发送消息的程序
2. 消费者：消息的接受者，会一直等待消息到来。
3. 消息队列：图中红色部分。类似一个邮箱，可以缓存消息；生产者向其中投递消息，消费者从其中取出消息。

> 生产者

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.MessageProperties;
 
/**
 * @description: 简单模式Simple
 */
public class Producer {
 
 
    public static void main(String[] args) {
 
        // 所有的中间件技术都是基于tcp/ip协议基础之上构建新型的协议规范，只不过rabbitmq遵循的是amqp
        // ip port
 
        // 1: 创建连接工程
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("128.197.157.151");
        connectionFactory.setPort(5672);
        connectionFactory.setUsername("admin");//rabbitmq登录的账号
        connectionFactory.setPassword("admin");//rabbitmq登录的密码
        connectionFactory.setVirtualHost("/");
 
        //springboot ---rabbitmq
 
        Connection connection = null;
        Channel channel = null;
        try {
            // 2: 创建连接Connection Rabbitmq为什么是基于channel去处理而不是链接? 长连接----信道channel
            connection = connectionFactory.newConnection("生产者");
            // 3: 通过连接获取通道Channel
            channel = connection.createChannel();
            // 4: 通过通创建交换机，声明队列，绑定关系，路由key,发送消息，和接收消息
            String queueName = "queue1";
 
            /*
             * @params1 队列的名称
             * @params2 是否要持久化durable=false 所谓持久化消息是否存盘，如果false 非持久化 true是持久化? 非持久化会存盘吗？ 会存盘，但是会随从重启服务会丢失。
             * @params3 排他性，是否是独占独立
             * @params4 是否自动删除，随着最后一个消费者消息完毕消息以后是否把队列自动删除
             * @params5 携带附属参数
             */
            channel.queueDeclare(queueName, true, false, false, null);
            // 5: 准备消息内容
            String message = "Hello chenjinxian!!!";
            // 6: 发送消息给队列queue
            // @params1: 交换机  @params2 队列、路由key @params 消息的状态控制  @params4 消息主题
            // 面试题：可以存在没有交换机的队列吗？不可能，虽然没有指定交换机但是一定会存在一个默认的交换机。
            channel.basicPublish("", queueName, null, message.getBytes());
 
            System.out.println("消息发送成功!!!");
        } catch (Exception ex) {
            ex.printStackTrace();
        } finally {
            // 7: 关闭通道
            if (channel != null && channel.isOpen()) {
                try {
                    channel.close();
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
            // 8: 关闭连接
 
            if (connection != null && connection.isOpen()) {
                try {
                    connection.close();
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }
 
 
    }
}
```

> 消费者

```java
import com.rabbitmq.client.*;
import java.io.IOException;
 
public class Consumer {
 
 
    public static void main(String[] args) {
 
        // 所有的中间件技术都是基于tcp/ip协议基础之上构建新型的协议规范，只不过rabbitmq遵循的是amqp
        // ip port
 
        // 1: 创建连接工程
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("128.197.157.151");//服务器IP
        connectionFactory.setPort(5672);
        connectionFactory.setUsername("admin");
        connectionFactory.setPassword("admin");
        connectionFactory.setVirtualHost("/");
 
        Connection connection = null;
        Channel channel = null;
        try {
            // 2: 创建连接Connection
            connection = connectionFactory.newConnection("消费者");
            // 3: 通过连接获取通道Channel
            channel = connection.createChannel();
            // 4: 通过通创建交换机，声明队列，绑定关系，路由key,发送消息，和接收消息
 
 
            // true = ack 正常的逻辑是没问题 死循环 rabbit 重发策略
            // false = nack 消息这在消费消息的时候可能会异常和故障
            final  Channel channel2 = channel;
            channel2.basicConsume("queue1", false, new DeliverCallback() {
                public void handle(String consumerTag, Delivery message) throws IOException {
                    try {
                        System.out.println("收到消息是" + new String(message.getBody(), "UTF-8"));
                        channel2.basicAck(message.getEnvelope().getDeliveryTag(),false);
                    }catch (Exception ex){
                        ex.printStackTrace();
                        // 三次确认 -- reject + sixin
                    }
 
                }
            }, new CancelCallback() {
                public void handle(String consumerTag) throws IOException {
                    System.out.println("接受失败了...");
                }
            });
 
            System.out.println("开始接受消息");
            System.in.read();
 
        } catch (Exception ex) {
            ex.printStackTrace();
        } finally {
            // 7: 关闭通道
            if (channel != null && channel.isOpen()) {
                try {
                    channel.close();
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
            // 8: 关闭连接
 
            if (connection != null && connection.isOpen()) {
                try {
                    connection.close();
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }
 
 
    }
}
```

### AMQP

AMQP全称：Advanced Message Queuing Protocol（高级消息队列协议）。是应用层协议的一个开发标准，为面向消息的中间件设计

02 AMQP生产者流转过程

- 建立连接
- 开启通道
- 发送消息
- 释放资源

03 AMQP消费者流转过程

- 建立连接
- 开启信道
- 准备接受消息
- 发送确认
- 释放资源


3. RabbitMQ的核心组成部分
01 RabbitMQ的核心组成部分![请添加图片描述](https://img-blog.csdnimg.cn/f79737c748154092a773b903a78747c4.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5omT55CD5ZSU5Y-744CC,size_20,color_FFFFFF,t_70,g_se,x_16)

RabbitMQ整体架构是什么样子的？

![请添加图片描述](https://img-blog.csdnimg.cn/8a1659e81409485d966a130c43c39b64.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5omT55CD5ZSU5Y-744CC,size_20,color_FFFFFF,t_70,g_se,x_16)

RabbitMQ的运行流程




RabbitMQ支持的消息模型

- 简单模式 Simple
- 工作模式 Work
- 发布订阅模式
- 路由模式
- 主题 Topic模式
- 参数模式

#### fanout 模式(发布订阅)

是一种广播机制，它是没有路由 key的模式

