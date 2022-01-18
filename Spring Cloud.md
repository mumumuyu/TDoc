# Spring Cloud

Dubbo,Zookeeper/分布式基础

Spring,轻量级容器，管理Bean，简化开发

不过发展了这么多年，配置越来越麻烦了

所以用springboot，约定大于配置，自动装配，开箱即用，很多配置给你按常用的写好了，它内置了Tomcat

Spring FlowData与物联网有关

微服务架构问题:

- 服务很多，客户端怎么访问
- 服务太多，服务间如何通信
- 服务过多，如何治理
- 服务挂了，怎么处理
  - 备份一个

解决：Spring Cloud

- 他不是技术，它是一种生态
- Spring Cloud NetFilx一站式解决方案
  - api网关，zuul主键
  - Feign ---HttpClient--http通信方式，同步，阻塞
- Apache Dubbo Zookeeper半自动 需要整合别人的
  - 异步，rpc，非阻塞
  - api没有，找第三方组件
  - 没有熔断，借助Hystrix
- SpringCloud Alibaba

服务网格 server mesh

api

http

注册与发现

熔断机制

由于网络不可靠，可能丢包，丢数据...

![image-20220114153343580](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220114153343580.png)

什么是微服务？

是一种架构模式，或者一种架构风格，将单一的应用程序划分为很多个模块，由All in One 到 很多个模块。每个服务运行在自己的进程内，服务之间相互协调，相互配置。彻底地解耦，让一切清晰明了

优点：

- 单一职责原则
- 每个服务内聚程度高
- 开发简单，一个服务就是专一的干一件事
- 微服务为松耦合的，开发与部署都是独立的
- 微服务允许融合最新技术
- 微服务就是一堆业务逻辑代码，不会与前端混合
- 每个微服务都可以有自己的数据库

缺点：

- 运维压力大
- 数据一致性问题
- 集成测试
- 性能监控
- 服务间通信成本
- 部署依赖

APIGateway网关

##### SpringBoot与Springcloud关系

- springBoot专注于快速开发单个微服务
- springcloud依赖于springboot，而springboot可以离开springcloud独立使用
- springcloud关注全局的微服务治理框架，springboot专注于快速开发单个

### Restful

诸如/consumer/dept/add/{sss}之类的，在路径中写入变量

springboot提供了RestTemplate的封装类，可以通过设置

(url , map(实体参数), class<T> responseType的方式返回所需类型)

```java
//生产者
    @RequestMapping("/dept/add/{dname}")
    public boolean addDept(Dept dept, @PathVariable("dname") String dname){
        dept.setDname(dname);
        return deptService.addDept(dept);
    }

//消费者
    @RequestMapping("/consumer/dept/add/{dname}")
    public Boolean add(@PathVariable("dname")String dname){
        return restTemplate.getForObject(REST_URL_PREFIX+"/dept/add/"+dname,Boolean.class);
    }
```

### Eureka

#### 什么是Eureka

- Netflix在涉及Eureka时，遵循的就是API原则.
- Eureka是Netflix的有个子模块，也是核心模块之一。Eureka是基于REST的服务，用于定位服务，以实现云端中间件层服务发现和故障转移，服务注册与发现对于微服务来说是非常重要的，有了服务注册与发现，只需要使用服务的标识符，就可以访问到服务，而不需要修改服务调用的配置文件了，功能类似于Dubbo的注册中心，比如Zookeeper.

#### 原理理解

- **Eureka基本的架构**
  - Springcloud 封装了Netflix公司开发的Eureka模块来实现服务注册与发现 (对比Zookeeper).
  - Eureka采用了C-S的架构设计，EurekaServer作为服务注册功能的服务器，他是服务注册中心.
  - 而系统中的其他微服务，使用Eureka的客户端连接到EurekaServer并维持心跳连接。这样系统的维护人员就可以通过EurekaServer来监控系统中各个微服务是否正常运行，Springcloud 的一些其他模块 (比如Zuul) 就可以通过EurekaServer来发现系统中的其他微服务，并执行相关的逻辑.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200521130157770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU5MTk4MA==,size_16,color_FFFFFF,t_70#pic_center)

- 和Dubbo架构对比.

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201120091517323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU5MTk4MA==,size_16,color_FFFFFF,t_70#pic_center)

- Eureka 包含两个组件：**Eureka Server** 和 **Eureka Client**.

- Eureka Server 提供服务注册，各个节点启动后，回在EurekaServer中进行注册，这样Eureka Server中的服务注册表中将会储存所有课用服务节点的信息，服务节点的信息可以在界面中直观的看到.

- Eureka Client 是一个Java客户端，用于简化EurekaServer的交互，客户端同时也具备一个内置的，使用轮询负载算法的负载均衡器。在应用启动后，将会向EurekaServer发送心跳 (默认周期为30秒) 。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除掉 (默认周期为90s).

- **三大角色**

  - Eureka Server：提供服务的注册与发现
  - Service Provider：服务生产方，将自身服务注册到Eureka中，从而使服务消费方能狗找到
  - Service Consumer：服务消费方，从Eureka中获取注册服务列表，从而找到消费服务

相关配置

```yml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost # Eureka服务端实例名称
  client:
    register-with-eureka: false #表示是否向注册中心注册自己
    fetch-registry: false #表示自己是注册中心false
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

#### 自我保护机制：

某时刻微服务崩了，eureka不会马上清理，会对其进行信息保存

如果EurekaServer九十秒内没有接收到微服务实例心跳

self-preservaton可以设置为false，自我保护机制就关掉了

一句话总结就是：**某时刻某一个微服务不可用，eureka不会立即清理，依旧会对该微服务的信息进行保存！**

- 默认情况下，当eureka server在一定时间内没有收到实例的心跳，便会把该实例从注册表中删除（**默认是90秒**），但是，如果短时间内丢失大量的实例心跳，便会触发eureka server的自我保护机制，比如在开发测试时，需要频繁地重启微服务实例，但是我们很少会把eureka server一起重启（因为在开发过程中不会修改eureka注册中心），**当一分钟内收到的心跳数大量减少时，会触发该保护机制**。可以在eureka管理界面看到Renews threshold和Renews(last min)，当后者（最后一分钟收到的心跳数）小于前者（心跳阈值）的时候，触发保护机制，会出现红色的警告：`EMERGENCY!EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT.RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEGING EXPIRED JUST TO BE SAFE.`从警告中可以看到，eureka认为虽然收不到实例的心跳，但它认为实例还是健康的，eureka会保护这些实例，不会把它们从注册表中删掉。
- 该保护机制的目的是避免网络连接故障，在发生网络故障时，微服务和注册中心之间无法正常通信，但服务本身是健康的，不应该注销该服务，如果eureka因网络故障而把微服务误删了，那即使网络恢复了，该微服务也不会重新注册到eureka server了，因为只有在微服务启动的时候才会发起注册请求，后面只会发送心跳和服务列表请求，这样的话，该实例虽然是运行着，但永远不会被其它服务所感知。所以，eureka server在短时间内丢失过多的客户端心跳时，会进入自我保护模式，该模式下，eureka会保护注册表中的信息，不在注销任何微服务，当网络故障恢复后，eureka会自动退出保护模式。自我保护模式可以让集群更加健壮。
- 但是我们在开发测试阶段，需要频繁地重启发布，如果触发了保护机制，则旧的服务实例没有被删除，这时请求有可能跑到旧的实例中，而该实例已经关闭了，这就导致请求错误，影响开发测试。所以，在开发测试阶段，我们可以把自我保护模式关闭，只需在eureka server配置文件中加上如下配置即可：`eureka.server.enable-self-preservation=false`【不推荐关闭自我保护机制】

获取具体微服务信息

```java
//得到具体的微服务相关信息
    @Autowired
    private DiscoveryClient discoveryClient;

@GetMapping("/dept/discovery")
    public Object discovery(){
        List<String> services = discoveryClient.getServices();
        System.out.println("discovery=>services:" + services);
        List<ServiceInstance> instances = discoveryClient.getInstances("SPRINGCLOUD-PROVIDER-DEPT");
        for(ServiceInstance instance:instances) {
            System.out.println(
                    instance.getHost()+"/t"+
                    instance.getPort()+"/t"+
                    instance.getUri()+"/t"+
                    instance.getServiceId()
            );
        }
        return this.discoveryClient;
    }
```

![image-20220118134911624](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118134911624.png)

#### 搭建集群

集群好处：其中一个服务点崩了，其他集群可以继续保证其使用。

![image-20220118142336727](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118142336727.png)

搭建成功

#### CAP原则

所谓ACID（原子性，一致性，隔离性isolation，持久性）RDBMS(Mysql,Oracle,sqlServer)

Nosql(Redis, mongDB)-->CAP

CAP(Consistency, Availability, Partition tolerance)强一致性，可用性，分区容错性

CAP只能满足三进二原则

CA:可扩展性差

CP:性能不高

AP：一致性要求较低

所以一个分布式系统不可能同时满足三个

作为服务注册中心，Eureka比Zookeeper好在哪里

Zookeeper满足CP

Eureka满足AP

在出现故障时Zookeeper会重新选取leader，在此间服务器不可用且若此时处理数据较大，往往选取时间也较长30~120s，所以其性能不高

而Eureka设计时就优先保证可用性，每个节点都是平等的,只要还有一台Eureka注册中心存在，就可以继续用，不过数据可能不是最新的。除此之外，他还有一种保护机制，如果15分钟内85%的节点都崩掉了，他就会认为客户端与注册中心出现了网络故障。

此时，

- Eureka就不会再移除因长时间没收到心跳而应该过期的服务
- Eureka会继续接受新服务的注册以及查询请求，但不会同步到其他节点上
- 当网络稳定后，会将新的注册信息进行同步

因此，Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像zookeeper那样使整个注册服务瘫痪一会儿

### Ribbon：负载均衡

所谓Load Balance负载均衡：就是让请求均摊到每台服务器上，从而达到HA（高可用）

常见软件：Nginx（反向代理服务器）,Lvs (Linux虚拟机)

轮询，随机连接

- Dubbo、SpringCloud 中均给我们提供了负载均衡，**SpringCloud 的负载均衡算法可以自定义**。
- 负载均衡简单分类：
  - 集中式LB
    - 即在服务的提供方和消费方之间使用独立的LB设施，如**Nginx(反向代理服务器)**，由该设施负责把访问请求通过某种策略转发至服务的提供方！
  - 进程式 LB
    - 将LB逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选出一个合适的服务器。
    - **Ribbon 就属于进程内LB**，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址！

针对其使用

在consumer的url中使用private static final String REST_URL_PREFIX = "http://SPRINGCLOUD-PROVIDER-DEPT";进行拼接，基于服务名去访问。

并加上@LoadBalanced //负载均衡实现RestTemplate

一个微服务，三个节点提供服务

![image-20220118153910278](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118153910278.png)

访问请求由注册中心进行调度，可能接收到不同服务器节点的响应

![image-20220118153820260](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118153820260.png)

![image-20220118153834836](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118153834836.png)

![image-20220118153843869](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220118153843869.png)

自定义负载均衡的算法：

```java
@RibbonClient(name = "SPRINGCLOUD-PROVIDER-DEPT", configuration = LgdRule.class)
```

设置LgdRule的具体算法（可以把人家源码拿来进行一下小改造）

