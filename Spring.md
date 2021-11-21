# 1.Spring

## 	1.1简介 + 特点

- SSH: Struct2 + Spring + Hibernate

- SSM: SpringMVC + Spring + Mybatis

- 控制反转(IOC) , 面向切面编程(AOP)***

-  现代化开发:  Spring Boot(构建) 

  - 约定大于配置
  - 快速开发单个微服务

  --> Spring Cloud(协调) --> Spring Cloud Date Flow

- 官网: [https://spring.io/]

- GitHub:[https://github.com/spring-projects/spring-framework]

- maven: 

  ```xml
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.9</version>
  </dependency>
  ```



## 1.2 IOC理论推导

- 之前:Dao + DaoImpl + Service + ServiceImpl

- 现在set注入，程序被动接受对象，大大降低耦合性

-  以下示例显示了基于 XML 的配置元数据的基本结构：

  id 为bean唯一标识符，类似于变量名，
  
  class为其全限定名，为包名+类型
  
  bean配置的name也是别名，可以有多个别名
  
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="hello" class="pojo.Hello">
          <property name="name" value="Spring hello!!!"/>
      </bean>
  
  </beans>
  ```



```java
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Hello hello  = (Hello) context.getBean("hello");
```

```java
@Test
    public void Test(){
        UserService userService = new UserServiceImpl();
        int num = 0;
        if(num == 1) {
            ((UserServiceImpl) userService).setUserDao(new UserMysqlDaoImpl());
        }else if (num == 2) {
            ((UserServiceImpl) userService).setUserDao(new UserDaoImpl());
        }else{
            ((UserServiceImpl) userService).setUserDao(new UserOracleDaoImpl());
        }
        userService.getUser();
    }
```

1.现在程序控制权在用户手上，使用set注入，你要啥用啥。

控制反转，这就是IOC的原型。

2.何为耦合，每个对象都紧紧联系在一起，强耦合，

我们要降低耦合，在各对象间通过ioc容器联系在一起。

可以用xml，也可以注解，也可以自动装配

Bean的定义信息是和实现分开的。

3.原来主动New 一个对象，现在把对象放容器里，

现在直接从Spring 容器里面拿beans

在Spring配置中，我们会配置id 及其对应的class位置，然后通过其内部set方法设置注入属性，没有set，我们的spring是跑不起来的

## 1.3 IOC 创建对象的方式

1.在获取context的时候就已经创建了所有的bean,

默认会使用无参构造创建对象

2.如果我们要使用有参构造

1. 下标赋值，

   index= "0"

2. 通过类型创建

   constructor-arg type = java.lang.String

3. 设置属性名

   <constructor-arg name ="name" value="lalala"/>

```xml
<bean id="hello" class="pojo.Hello">
        <constructor-arg name="name" value="Spring hello1!"/>
<!--        <property name="name" value="Spring hello!!!"/> 默认为无参构造-->
    </bean>
    
    <bean id="hello2" class="pojo.Hello">
        <constructor-arg type="java.lang.String" value="Spring 2"/>
    </bean>

    <bean id="hello3" class="pojo.Hello">
        <constructor-arg index="0" value="Spring 3"/>
    </bean>
```

## 1.4Spring 配置

1. alias

   别名,当然，原来名字也能用

   <alias name = "user" alias="useralias"/>

2. bean

   id 为bean唯一标识符，类似于变量名，

   class为其全限定名，为包名+类型

   bean配置的name也是别名，可以有多个别名

   并且name中可以自动识别分隔，如 user user2,user3;user5

3. import

   用于团队开发，将多个团队配置文件合并为一个

   <import resource = "beans2.xml"/>

## DI依赖注入 IOC的另一种

1. #### 构造器注入

   通过构造方法注入，前提：类有有参与无参构造方法

2. #### set方式注入

   重点：

   - 依赖注入：set注入
   - 依赖：bean对象创建依赖ioc容器
   - 注入，Ioc注入bean中所有的属性

   对于不同类型的属性配置：

   ```xml
   <bean id="address" class="com.pojo.Address">
       <property name="address" value="xian"></property>
   </bean>
   
   <bean id="student" class="com.pojo.Student">
       <property name="name" value="hou"/>
       <property name="address" ref="address"/>
   
       <!--数组注入-->
       <property name="books">
           <array>
               <value>三国</value>
               <value>西游</value>
               <value>水浒</value>
           </array>
       </property>
   
       <!--list-->
       <property name="hobbies">
           <list>
               <value>eat</value>
               <value>drink</value>
               <value>play</value>
           </list>
       </property>
   
       <property name="card">
           <map>
               <entry key="1" value="12"/>
               <entry key="2" value="23"/>
           </map>
       </property>
   
       <property name="game">
           <set>
               <value>wangzhe</value>
               <value>daota</value>
               <value>lol</value>
           </set>
       </property>
   
       <property name="wife">
           <null></null>
       </property>
   
       <!--properties-->
       <property name="infor">
           <props>
               <prop key="id">20200405</prop>
               <prop key="name">hdk</prop>
           </props>
       </property>
   </bean>
   ```

1. #### 其他方式

命名空间,p,c

p(preoterties)

```xml
<!--p命名空间注入/set注入-->
<bean id="use" class="com.pojo.User" p:name="dong" p:age="10">
```

```Xml
<!--c命名空间/构造器-->
<bean id="use2" class="com.pojo.User" c:name="kun" c:age="19" scope="prototype"></bean>
```

另外要加个

```xml
xmlns:c="http://www.springframework.org/schema/c"
```

## 1.5 Bean 的作用域

- 单例模式

  所有人用同一个对象

- 原型模式

  每次从容器get时候，都会New一个新对象

  | Scope                                                        | Description                                                  |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | [singleton](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
  | [prototype](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
  | [request](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
  | [session](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
  | [application](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
  | [websocket](https://docs.spring.io/spring-framework/docs/5.3.x/reference/html/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

```xml
<bean scope="prototype"></bean> 原型
<bean scope="singleton"></bean>	单例
```

## 1.6 Bean的自动装配

jdk1.5支持注解，Spring2.5支持注解

注解比xml配置起来方便很多

使用注解：

1. 导入约束 context

2. 配置注解支持

   ```xml
   <context:annotation-config/>
   ```

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```

3. 自动导入

   @Autowired

   其可以在属性上使用，也可以在set方法上使用

   注意，配置bean.xml的实体需要自己提前加好有参无参

   而且用了注解，注入之后，由于注解是由反射实现的，所以连这个类的set都不需要写了，不过前提是他已经在ioc中容器存在，且符合名字byname

   其他：

   ```xml
   @Nullable 增加注解，某属性为Null也不会报错
   ```

   在自动装配环境比较复杂的时候，可以与@Qualifier组合使用

   ```java
   @Qualifier(value="你要用的bean.xml里的id")
   ```

     以上都是Spring注解

   @Resource是java的注解

   如果ioc容器中一个类型的对象只有一个，那就可以直接用

   如果有多个，而且没有一个是原来名字如dog就是dog，那就会报错，另外推荐bean id同变量名一样，我们一般首字母都小写的

   当然它也可以指定

   比如

   ```java
   @Resource(name = "cat2")
   ```

   小结

   - 都是用来自动装配的，可以指定id
   - @Autowired 通过byname(常用)
   - @Resource通过byname,如果byname找不到就通过byType，俩个都找不到，就报错(常用)

## 1.7 使用注解开发

Spring4后使用注解开发需要aop的包

![image-20211120133826963](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20211120133826963.png)

增加context约束支持注解

1. bean

   @Component 组件,放在类上，那这个类就被Spring管理了，即Bean

   等价于<bean id ="xxx" class="xxx"/>

1. 属性注入

   @value("xxx")

   在属性上写，相当于property name = "xxx"

   一般用于简单的配置，复杂还是推荐配置这里自己配；

   

2. 衍生

   <context:comp

   Component有些衍生的注解

   开发用mvc

   - dao (@Repository)
   - service（@Service）
   - controller(@Controller)加上说明是把这个类注册到Spring，装配Bean

3. 自动装配

   同上@Autowired,@Resource

4. 作用域

   @Scope("singleton")单例

   @Scopre("prototype")原型

5. 小结

   注意，使用注解要开启支持，

   ```xml
   <context:annotation-config/>
   <!--指定要扫描的包-->
   <context:component-scan base-package="com"/>
   ```

xml与注解比：

- xml更万能，维护简单方便
- 注解，维护相对复杂，有局限性
- 一般xml用来管理bean,注解就负责完成属性的注入

@Configuration

@CompoentScan("com.lgd.pojo")扫描

@Bean,注册一个bean相当于之前写的<bean id="voidname方法名" class="方法的返回值"/>return返回要注入到bean的对象

如果完全通过配置类方法做，那就只能通过AnnotationConfig来获取容器，通过配置类的configuration

纯Java配置方式基本在SpringBoot中随处可见，确实

## 2.1 AOP 代理模式

为什么要去学代理模式？

这就是Spring AOP的底层，

代理模式的分类：

- 静态代理
- 动态代理

### 2.2.1静态代理

角色分析：

- 抽象角色：用接口，抽象类

- 真实角色:被代理的角色

- 代理角色：代理真实角色，并做一些附属操作

- 客户：访问代理对象的人

  代理会加了一层

  代理模式的好处：

  - 可以使真实角色更纯粹，不用关注其他的公共业务
  - 业务交给代理角色，实现分工,一个业务类该做啥做啥，有啥新需求我再new一个代理角色去做新操作，面向对象的原则，我们最好要解耦，尽量不动本来实现的业务代码，在公司里头是大忌
  - 公共业务发生扩展时候，方便集中管理

  缺点：

  - 一个真实角色就要一个代理角色；代码量会翻倍

    ，开发效率会降低，解决这个缺点可以用动态代理

  代码步骤：

  1. 接口

     ```java
     public interface Rent {
         public void rent();
     }
     ```

  2. 真实角色

     ```java
     public class Host implements Rent{
         @Override
         public void rent() {
             System.out.println("房东要出租房子");
         }
     }
     ```

  3. 代理角色

     ```java
     public class Proxy implements Rent{
         private Host host;
     
         public Host getHost() {
             return host;
         }
     
         public void setHost(Host host) {
             this.host = host;
         }
     
         public Proxy() {
         }
     
         public Proxy(Host host) {
             this.host = host;
         }
     
         @Override
         public void rent() {
             host.rent();
             seehourse();
             hetong();
         }
     
         public void seehourse(){
             System.out.println("中介来看房子了");
         }
     
         public void hetong(){
             System.out.println("中介签合同了");
         }
     }
     ```

  4. 客户端访问代理角色

  ```java
  @Test
  public void test(){
      Proxy proxy = new Proxy(new Host());
      proxy.rent();
  }
  ```

之前纵向开发完，一套了，实现新业务，我们就要横向开发，这就是AOP，横切进去一段业务

![image-20211120185505419](C:\Users\Asus\AppData\Roaming\Typora\typora-user-images\image-20211120185505419.png)



### 2.2.2动态代理

动态代理是动态生成的

其分为两类：

基于接口的动态代理——JDK动态代理，

基于类的动态代理——cglib

java字节码实现：javasist



了解两个类：Proxy（代理）,InvocationHandler（调用处理程序）

动态代理：通过类实现：

把自动生成代理类写好，然后set丢一个真实角色进去，在自动生成代理类处理完get得到他的代理类，然后就用.

动态代理本质就是反射机制的实现

```java
public class ProxyInvocation implements InvocationHandler {

    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces()
                ,this);
    }

    /**
     *处理代理实例，并返回结果
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        Object result = method.invoke(target, args);
        return result;
    }
    public void log(String msg){
        System.out.println("执行力"+ msg +"方法");
    }
}
```

测试

```java
@Test
public void test(){
        Host host = new Host();
        ProxyInvocation pi = new ProxyInvocation();
        pi.setTarget(host);
        Rent proxy = (Rent) pi.getProxy();
        proxy.rent();
}
```

好处是呢，这个新需要的业务类，可以自动生成，可以写通用的代理类

一个动态代理类可以代理多个类，被代理的接口类型为Object，得到的也是Object

## 2.2什么是AOP

面向切面编程，

加日志，安全，缓存，事务等

aspectjweaver

##### 方法一：使用Spring原生的API接口

对于express的execution表达式

* 包.类.方法

```xml
<bean id="log" class="log.Log"/>
<bean id="Afterlog" class="log.AterLog"/>
<bean id="userservice" class="service.UserServiceImpl"/>

<aop:config>
    <!--执行的切入点-->
    <aop:pointcut id="pointcut" expression="execution(* service.UserServiceImpl.*(..))"/>
    <!--执行环绕增加-->
    <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    <aop:advisor advice-ref="Afterlog" pointcut-ref="pointcut"/>
</aop:config>
```

```java
@Test
public void test(){
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    //动态代理的是接口
    UserService userservice = (UserService) context.getBean("userservice");
    userservice.add();
}
```

##### 方法二：自定义类

##### ，ref这个要引用的类

```xml
<aop:config>
    <!--切入面-->
    <aop:aspect ref="diy">
       <!--切入点-->
        <aop:pointcut id="point" expression="execution(* service.UserServiceImpl.*(..))"/>
        <!--通知-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:after method="after" pointcut-ref="point"/>
    </aop:aspect>
</aop:config>
```

##### 方法三：使用注解

，开启注解支持，JDK（默认） cglib

```java
@Aspect
public class Annocation {
    @Before("execution(* service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("执行前log");
    }

    @After("execution(* service.UserServiceImpl.*(..))")
    public void After(){
        System.out.println("执行后log");
    }
    //在环绕增强中，我们可以给地暖管一个参数，代表我们要获取切入的点
    @Around("execution(* service.UserServiceImpl.*(..))")
    public void Around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("around");
        Object proceed = joinPoint.proceed();
        System.out.println("around after");
    }
}
```

开启注解支持

```xml
<aop:aspectj-autoproxy/>
```

## Mybatis

insert into user(id,name,pwd) values(#{id},#{name},#{pwd})

## 事务管理

声明式AOP

编程式：在代码中进行事务管理

需要事务，因为要保证数据提交一致性

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-tx.aop">

    <!--data source-->
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?userSSL=true&amp;
                userUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="1328910"/>
    </bean>

    <!--sqlsession-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource" />
        <!--bound mybatis-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/mapper/*.xml"/>
    </bean>

    <!--声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="datasource" />
    </bean>

    <!--结合aop实现事务置入-->
    <!--配置事务的类-->
    <tx:advice id="tx1" transaction-manager="transactionManager">
        <!--给哪些方法配置事务-->
        <!--配置事务的传播特性-->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="*" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--配置事务切入-->
    <aop:config>
        <aop:pointcut id="txpointxut" expression="execution(* com.mapper.*.*(..))"/>
        <aop:advisor advice-ref="tx1" pointcut-ref="txpointxut"/>
    </aop:config>

</beans>
```

