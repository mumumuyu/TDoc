# MQRabbit

简单下载使用：

前置erl erlang语言写的

然后再 xxx(mq所在文件位置)\bin	配置一下环境变量 

cmd中	\sbin 下 

```bash
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl status
rabbitmq-server.bat
```

访问 http://localhost:15672 (guest 	guest)

pom:

```xml
		<dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.10.0</version>
        </dependency>
```

java中使用

```java
public class ConnectionUtil {

    private final static String QUEUE_NAME = "simple_queue";

    /**
     * 建立与RabbitMQ的连接
     * @return
     * @throws Exception
     */
    public static Connection getConnection() throws Exception {
        //定义连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置服务地址
        factory.setHost("127.0.0.1");
        //端口
        factory.setPort(5672);
        //设置账号信息，用户名、密码、vhost
        factory.setVirtualHost("/");//设置虚拟机，一个mq服务可以设置多个虚拟机，每个虚拟机就相当于一个独立的mq
        factory.setUsername("guest");
        factory.setPassword("guest");
        // 通过工厂获取连接
        Connection connection = factory.newConnection();
        return connection;
    }

    public static void main(String[] args) throws Exception {
        send();
        send();
        receive();
    }

    public static void send() throws Exception {
        // 1、获取到连接
        Connection connection = ConnectionUtil.getConnection();
        // 2、从连接中创建通道，使用通道才能完成消息相关的操作
        Channel channel = connection.createChannel();
        // 3、声明（创建）队列
        //参数：String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        /**
         * 参数明细
         * 1、queue 队列名称
         * 2、durable 是否持久化，如果持久化，mq重启后队列还在
         * 3、exclusive 是否独占连接，队列只允许在该连接中访问，如果connection连接关闭队列则自动删除,如果将此参数设置true可用于临时队列的创建
         * 4、autoDelete 自动删除，队列不再使用时是否自动删除此队列，如果将此参数和exclusive参数设置为true就可以实现临时队列（队列不用了就自动删除）
         * 5、arguments 参数，可以设置一个队列的扩展参数，比如：可设置存活时间
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        // 4、消息内容
        String message = "Hello World!";
        // 向指定的队列中发送消息
        //参数：String exchange, String routingKey, BasicProperties props, byte[] body
        /**
         * 参数明细：
         * 1、exchange，交换机，如果不指定将使用mq的默认交换机（设置为""）
         * 2、routingKey，路由key，交换机根据路由key来将消息转发到指定的队列，如果使用默认交换机，routingKey设置为队列的名称
         * 3、props，消息的属性
         * 4、body，消息内容
         */
        channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        System.out.println(" [x] Sent '" + message + "'");

        //关闭通道和连接(资源关闭最好用try-catch-finally语句处理)
        channel.close();
        connection.close();
    }

    public static void receive() throws Exception {
        // 获取到连接
        Connection connection = ConnectionUtil.getConnection();
        //创建会话通道,生产者和mq服务所有通信都在channel通道中完成
        Channel channel = connection.createChannel();
        // 声明队列
        //参数：String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        /**
         * 参数明细
         * 1、queue 队列名称
         * 2、durable 是否持久化，如果持久化，mq重启后队列还在
         * 3、exclusive 是否独占连接，队列只允许在该连接中访问，如果connection连接关闭队列则自动删除,如果将此参数设置true可用于临时队列的创建
         * 4、autoDelete 自动删除，队列不再使用时是否自动删除此队列，如果将此参数和exclusive参数设置为true就可以实现临时队列（队列不用了就自动删除）
         * 5、arguments 参数，可以设置一个队列的扩展参数，比如：可设置存活时间
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        //实现消费方法
        DefaultConsumer consumer = new DefaultConsumer(channel){
            // 获取消息，并且处理，这个方法类似事件监听，如果有消息的时候，会被自动调用
            /**
             * 当接收到消息后此方法将被调用
             * @param consumerTag  消费者标签，用来标识消费者的，在监听队列时设置channel.basicConsume
             * @param envelope 信封，通过envelope
             * @param properties 消息属性
             * @param body 消息内容
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                //交换机
                String exchange = envelope.getExchange();
                //消息id，mq在channel中用来标识消息的id，可用于确认消息已接收
                long deliveryTag = envelope.getDeliveryTag();
                // body 即消息体
                String msg = new String(body,"utf-8");
                System.out.println(" [x] received : " + msg + "!");
            }
        };

        // 监听队列，第二个参数：是否自动进行消息确认。
        //参数：String queue, boolean autoAck, Consumer callback
        /**
         * 参数明细：
         * 1、queue 队列名称
         * 2、autoAck 自动回复，当消费者接收到消息后要告诉mq消息已接收，如果将此参数设置为tru表示会自动回复mq，如果设置为false要通过编程实现回复
         * 3、callback，消费方法，当消费者接收到消息要执行的方法
         */
        channel.basicConsume(QUEUE_NAME, true, consumer);
    }
}
```

mq快速入门 https://blog.csdn.net/kavito/article/details/91403659





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

