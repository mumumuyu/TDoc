# Spring Cloud

中文文档:https://www.springcloud.cc/spring-cloud-dalston.html

- 微服务与微服务架构
- 基础工程 RestTemplate
- Eureka（AP，保证效率）
  - 集群配置
  - 对比Zookeeper(要重新选主节点，保证一致性CP)
- Ribbon
  - IRule
- Feign
  - 面向接口，社区要求
- Hystrix
  - 熔断
  - 降级
  - dashboard
- Zuul路由网关
  - 路由，拦截，过滤
- SpringCloud Config配置
  - C/S-Git

基本就是导入依赖——编写配置——开启@EnableXXX，具体yml自己配置

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

IRule

RoundRobinRule 轮询策略

 RandomRule 随机策略

AvailabilityFilteringRule ： 会先过滤掉，跳闸，访问故障的服务~，对剩下的进行轮询~

 RetryRule ： 会先按照轮询获取服务~，如果服务获取失败，则会在指定的时间内进行，重试

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

### Feign进行负载均衡

springcloud同时集成了Ribbon（调用微服务的名字）和Eureka，可在使用Feign时提供负载均衡的http客户端

Feign(调用接口和注解)整合了Ribbon和Hystrix，更符合我们直接Springboot或者SSM中，利用controller调用Service层

fegin本质还是和Ribbon一样，去找了Eureka，相对代码可读性更强

##### 选择Fegin还是Ribbon呢？

通过学习了解到Ribbon+RestTemplate或者Fegin面向接口的方法进行负载均衡。

所以更喜欢RestFul风格可以选择Ribbon，Fegin只是对他们进行了整合，所以喜欢调用接口式的可以选择Feign

```java
//api's service
@Component
@FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT")
public interface DeptClientService {
    @RequestMapping("/dept/add/{dname}")
    boolean addDept(@PathVariable("dname")String dname);

    @RequestMapping("/dept/get/{id}")
    Dept queryById(@PathVariable("id") Long id);

    @RequestMapping("/dept/list")
    List<Dept> queryAll();
}

//client's controller
@RestController
public class DeptConsumerController {

    @Autowired
    private DeptClientService service;

    @RequestMapping("/consumer/dept/add/{dname}")
    public Boolean add(@PathVariable("dname")String dname){
        return service.addDept(dname);
    }

    @RequestMapping("/consumer/dept/get/{id}")
    public Dept get(@PathVariable("id") Long id){
        return service.queryById(id);
    }

    @RequestMapping("/consumer/dept/list")
    public List<Dept> queryAll(){
        return service.queryAll();
    }
}

@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.lgd"})
public class FeignDeptComsumer {
    public static void main(String[] args) {
        SpringApplication.run(FeignDeptComsumer.class,args);
    }
}
```

### Hystrix

在分布式系统中不可避免地会出现问题，Hystrix就是一个用于处理延迟与容错的开源库。避免发生级联错误

@HystrixCommand

#### 服务雪崩

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其他的微服务，这就是所谓的“扇出”，如果扇出的链路上**某个微服务的调用响应时间过长，或者不可用**，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”。

#### Hystrix能干嘛？

- 服务降级
- 服务熔断
- 服务限流
- 接近实时的监控
- …

当一切正常时，请求流可以如下所示：

![format_png 2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9naXRodWIuY29tL05ldGZsaXgvSHlzdHJpeC93aWtpL2ltYWdlcy9zb2EtMS02NDAucG5n?x-oss-process=image/format,png)

当许多后端系统中有一个潜在阻塞服务时，它可以阻止整个用户请求：

![format_png 3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9naXRodWIuY29tL05ldGZsaXgvSHlzdHJpeC93aWtpL2ltYWdlcy9zb2EtMi02NDAucG5n?x-oss-process=image/format,png)

随着大容量通信量的增加，单个后端依赖项的潜在性会导致所有服务器上的所有资源在几秒钟内饱和。

应用程序中通过网络或客户端库可能导致网络请求的每个点都是潜在故障的来源。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，从而备份队列、线程和其他系统资源，从而导致更多跨系统的级联故障。

![format_png 4](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9naXRodWIuY29tL05ldGZsaXgvSHlzdHJpeC93aWtpL2ltYWdlcy9zb2EtMy02NDAucG5n?x-oss-process=image/format,png)

当使用**Hystrix**包装每个基础依赖项时，上面的图表中所示的体系结构会发生类似于以下关系图的变化。**每个依赖项是相互隔离的**，限制在延迟发生时它可以填充的资源中，并包含在回退逻辑中，该逻辑决定在依赖项中发生任何类型的故障时要做出什么样的响应：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200521131820586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU5MTk4MA==,size_16,color_FFFFFF,t_70#pic_center)

**官网资料**：https://github.com/Netflix/Hystrix/wiki

简单使用@HystrixCommand注解

```java
@RestController
public class DeptController {
    @Autowired
    private DeptService deptService;

    /**
     * create by: LGD
     * description: 根据id查询部门，如果出现问题就走hystrixGet方法
     * create time: 2022/1/19 14:26
     *
     * @return Dept
     * @Param: id     */
    @HystrixCommand(fallbackMethod = "hystrixGet")
    @GetMapping("/dept/get/{id}")
    public Dept queryById(@PathVariable("id") Long id){
        Dept dept = deptService.queryById(id);

        if (dept==null){
            throw new RuntimeException("这个id=>"+id+",不存在该用户，或信息无法找到~");
        }
        return dept;
    }

    public Dept hystrixGet(@PathVariable("id") Long id){
        return new Dept(id,"id:"+id+"=>不存在此id","not found");
    }
}

@SpringBootApplication
@EnableEurekaClient
@EnableCircuitBreaker
public class DeptServerHystrix_8001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptServerHystrix_8001.class,args);
    }
}

```

**为了避免因某个微服务后台出现异常或错误而导致整个应用或网页报错，使用熔断是必要的**

#### Hystrix降级处理



#### 服务降级

服务降级是指 当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理，或换种简单的方式处理，从而释放服务器资源以保证核心业务正常运作或高效运作。说白了，**就是尽可能的把系统资源让给优先级高的服务**。

**当某一时间内服务A的访问量暴增，而B和C的访问量较少，为了缓解A服务的压力，这时候需要B和C暂时关闭一些服务功能，去承担A的部分服务，从而为A分担压力，叫做服务降级**。

##### 服务降级需要考虑的问题

- 1）那些服务是核心服务，哪些服务是非核心服务
- 2）那些服务可以支持降级，那些服务不能支持降级，降级策略是什么
- 3）除服务降级之外是否存在更复杂的业务放通场景，策略是什么？

##### 自动降级分类

1）超时降级：主要配置好超时时间和超时重试次数和机制，并使用异步机制探测回复情况

2）失败次数降级：主要是一些不稳定的api，当失败调用次数达到一定阀值自动降级，同样要使用异步机制探测回复情况

3）故障降级：比如要调用的远程服务挂掉了（网络故障、DNS故障、http服务返回错误的状态码、rpc服务抛出异常），则可以直接降级。降级后的处理方案有：默认值（比如库存服务挂了，返回默认现货）、兜底数据（比如广告挂了，返回提前准备好的一些静态页面）、缓存（之前暂存的一些缓存数据）

4）限流降级：秒杀或者抢购一些限购商品时，此时可能会因为访问量太大而导致系统崩溃，此时会使用限流来进行限制访问量，当达到限流阀值，后续请求会被降级；降级后的处理方案可以是：排队页面（将用户导流到排队页面等一会重试）、无货（直接告知用户没货了）、错误页（如活动太火爆了，稍后重试）。

```java
@Component
public class DeptClientServiceFallBackFactory implements FallbackFactory {
    @Override
    public DeptClientService create(Throwable cause) {
        return new DeptClientService() {
            @Override
            public boolean addDept(String dname) {
                return false;
            }

            @Override
            public Dept queryById(Long id) {
                return new Dept(id,
                        "id=>" + id + "没有对应的信息，客户端提供了降级的信息，这个服务现在已经被关闭",
                        "没有数据~");
            }
            @Override
            public List<Dept> queryAll() {
                return null;
            }
        };
    }
}

@FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT",fallbackFactory = DeptClientServiceFallBackFactory.class)

# 开启降级feign.hystrix
feign:
  hystrix:
    enabled: true
```

#### 服务熔断与降级的区别

熔断->服务端，因为某些问题如超时或异常，崩了引起熔断，类似于保险丝的自我保护机制

降级——》客户端的，服务端为了让资源利用效率最大化，停止了某些服务，让放客户端返回一些之前设定好的默认值。

**熔断，降级，限流**：

限流：限制并发的请求访问量，超过阈值则拒绝；

降级：服务分优先级，牺牲非核心服务（不可用），保证核心服务稳定；从整体负荷考虑；

熔断：依赖的下游服务故障触发熔断，避免引发本系统崩溃；系统自动执行和恢复

#### Dashboard流监控

配置：

pom

```xml
<dependencies>
        <!--Hystrix依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
        <!--dashboard依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
        <!--Ribbon-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-ribbon</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
        <!--Eureka-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
        <!--实体类+web-->
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>springcloud-01-api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
    </dependencies>
```



```java
//在想监控的服务端增加
@Bean
    public ServletRegistrationBean registrationBean(){
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(new HystrixMetricsStreamServlet());
        servletRegistrationBean.addUrlMappings("/actuator/hystrix.stream");

        return servletRegistrationBean;
    }


//再在监控端开启
@SpringBootApplication
//开启监控
@EnableHystrixDashboard
public class DeptHystrixDashboard_9001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptHystrixDashboard_9001.class,args);
    }
}
```



#### hystrix可以监控

![image-20220120143650924](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220120143650924.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201121162612484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU5MTk4MA==,size_16,color_FFFFFF,t_70#pic_center)

健康程度：绿色<黄色<红色（报错），根据他们计算出helloKey，错误比例

### Zull路由网关

用户一进来会先进入API Gateway（网关），接着进入dashboard监控与config

zull：包含对请求的路由（用来跳转）与过滤俩个主要功能

就是代理+路由+过滤的功能

**路由功能负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础**，而过**滤器功能则负责对请求的处理过程进行干预，是实现请求校验，服务聚合等功能的基础**。Zuul和Eureka进行整合，将Zuul自身注册为Eureka服务治理下的应用，同时从Eureka中获得其他服务的消息，也即以后的访问微服务都是通过Zuul跳转后获得。

zuul最终也会进入Eureka被注册管理

pom

```xml
<dependencies>
    <!--导入zuul依赖-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zuul</artifactId>
        <version>1.4.6.RELEASE</version>
    </dependency>
    <!--Hystrix依赖-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-hystrix</artifactId>
        <version>1.4.6.RELEASE</version>
    </dependency>
    <!--dashboard依赖-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
        <version>1.4.6.RELEASE</version>
    </dependency>
    <!--Ribbon-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-ribbon</artifactId>
        <version>1.4.6.RELEASE</version>
    </dependency>
    <!--Eureka-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
        <version>1.4.6.RELEASE</version>
    </dependency>
    <!--实体类+web-->
    <dependency>
        <groupId>org.example</groupId>
        <artifactId>springcloud-01-api</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--热部署-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
    </dependency>
</dependencies>
```

yml

```yml
server:
  port: 9527
spring:
  application:
    name: springcloud-zuul #微服务名称
# eureka 注册中心配置
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance: #实例的id
    instance-id: zuul9527.com
    prefer-ip-address: true # 显示ip
info:
  app.name: lgd's cloud # 项目名称
  company.name: ZUST # 公司名称
# zull 路由网关配置
zuul:
  # 路由相关配置
  # 原来访问路由 eg:http://www.cspStudy.com:9527/springcloud-provider-dept/dept/get/1
  # zull路由配置后访问路由 eg:http://www.cspstudy.com:9527/haust/mydept/dept/get/1
  routes:
    mydept.serviceId: springcloud-provider-dept # eureka注册中心的服务提供方路由名称
    mydept.path: /mydept/** # 将eureka注册中心的服务提供方路由名称 改为自定义路由名称
  # 不能再使用这个路径访问了，*： 忽略,隐藏全部的服务名称~
  ignored-services: "*"
  # 设置公共的前缀
  prefix: /lgd
```

```java
@SpringBootApplication
@EnableZuulProxy
public class zuulProxy_9527 {
    public static void main(String[] args) {
        SpringApplication.run(zuulProxy_9527.class,args);
    }
}
```

使用了它zuul，就要求请求统一进入某个接口，根据该接口去访问其他服务器的接口，这样就可以避免暴露我们微服务的接口，并且在配置网关yml时，可以利用设置mydept.path以及设置公共前缀prefix隐藏你所访问的微服务名。

## SpringCloud Config分布式配置中心

springcloud配置中心把服务分为服务端和客户端

服务端也称为 **分布式配置中心**，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密，解密信息等访问接口。

客户端则是**通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息**。配置服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理。并且可用通过git客户端工具来方便的管理和访问配置内容。

**spring cloud config 分布式配置中心能干嘛？**

- 集中式管理配置文件
- 不同环境，不同配置，动态化的配置更新，分环境部署，比如 /dev /test /prod /beta /release
- 运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取配置自己的信息
- 当配置发生变动时，服务不需要重启，即可感知到配置的变化，并应用新的配置
- 将配置信息以REST接口的形式暴露

##### server端

通过springcloud-config，访问git库某文件成功！

![image-20220120173415386](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220120173415386.png)

流程

```java
//开启config
@EnableConfigServer
@SpringBootApplication
public class ConfigServer_3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServer_3344.class,args);
    }
}

```

进行git配置

```
server:
  port: 3344
spring:
  application:
    name: springcloud-config-server
  # 连接码云远程仓库
  cloud:
    config:
      server:
        git:
          # 注意是https的而不是ssh
          uri: https://github.com/mumumuyu/TDoc.git
          # 通过 config-server可以连接到git，访问其中的资源以及配置~
# 不加这个配置会报Cannot execute request on any known server 这个错：连接Eureka服务端地址不对
# 或者直接注释掉eureka依赖 这里暂时用不到eureka
eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

pom

```.xml
<dependencies>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--config-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
            <version>2.1.1.RELEASE</version>
        </dependency>
        <!--eureka-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
    </dependencies>
```

##### client端

消费者通过服务端获取信息

![image-20220120205617583](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220120205617583.png)

```java
@RestController
public class ConfigClientController {
    @Value("${spring.application.name}")
    private String applicationName; //获取微服务名称
    @Value("${eureka.client.service-url.defaultZone}")
    private String eurekaServer; //获取Eureka服务
    @Value("${server.port}")
    private String port; //获取服务端的端口号
    @RequestMapping("/config")
    public String getConfig(){
        return "applicationName:"+applicationName +
                "eurekaServer:"+eurekaServer +
                "port:"+port;
    }
}
```

```yml
# bootstrap.yml
# 系统级别的配置
spring:
  cloud:
    config:
      name: config-server # 需要从git上读取的资源名称，不要后缀
      profile: dev
      label: master
      uri: http://localhost:3344
# application.yml
# 用户级别的配置
spring:
  application:
    name: springcloud-config-server
```

实战：自己远程配置eureka以及provider的config

![image-20220121135059897](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20220121135059897.png)

成功！！！

同理：

就是个pom

```xml
        <!--config-->
        <!-- https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-start -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
            <version>2.1.1.RELEASE</version>
        </dependency>
```

两个yml配置

```yml
#application.yml
spring:
  application:
    name: springcloud-config-eureka
#bootstrap.yml
# 系统级别的配置
spring:
  cloud:
    config:
      name: config-eureka # 需要从git上读取的资源名称，不要后缀
      profile: dev
      label: master
      uri: http://localhost:3344
     
#或者也可以自己去直接连
spring:
  application:
    name: springcloud-config-server
  # 连接码云远程仓库
  cloud:
    config:
      server:
        git:
          # 注意是https的而不是ssh
          uri: https://github.com/mumumuyu/TDoc.git
```

若没成功基本就是网络问题，github进不去这样

