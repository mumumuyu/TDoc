# SpringBoot

javase:OOP

mysql:持久化

html+css+js+jquery9框架：视图层View

javaweb:原始mvc网站

war:tomcat 运行

ssm:框架，简化开发，配置复杂

再简化，springmvc-->springboot 微服务架构

springboot :内嵌tomcat

- ​	配置：yml

- ​	自动装配-->简化开发

- ​	继承数据库druid

- ​	分布式开发：Dubbo+zookeeper

- ​	swagger：接口文档

- ​	SpringSecurity / shiro(拦截器功能,方便)

- ​	Linux

- ​	SpringCloud :真正涉及到微服务,Restful,Eureka,Ribbon,Feign,HyStrix

  - SpringCloud:config : git 上远程操作

  JVM:常见内容知道就好

### **自动装配原理**

启动类的@SpringBootApplication注解由@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan三个注解组成，三个注解共同完成自动装配；

@SpringBootConfiguration 注解标记启动类为配置类
@ComponentScan 注解实现启动时扫描启动类所在的包以及子包下所有标记为bean的类由IOC容器注册为bean
@EnableAutoConfiguration通过 @Import 注解导入 AutoConfigurationImportSelector类，然后通过AutoConfigurationImportSelector 类的 selectImports 方法去读取需要被自动装配的组件依赖下的spring.factories文件配置的组件的类全名，并按照一定的规则过滤掉不符合要求的组件的类全名，将剩余读取到的各个组件的类全名集合返回给IOC容器并将这些组件注册为bean



### 原理：

spring-boot-starter-web : 帮忙导入各种web所需依赖

用什么功能，找到对应启动器

spring-boot-autoconfigure-2.2.0.RELEASE.jar ，整合javaEE

Spring-Boot-DevTools 热部署

**自动装配原理：**

1）通过注解@SpringBootApplication=>@EnableAutoConfiguration=>@Import({AutoConfigurationImportSelector.class})实现自动装配

2）AutoConfigurationImportSelector类中重写了ImportSelector中selectImports方法，批量返回需要装配的配置类

3）通过Spring提供的SpringFactoriesLoader机制，扫描classpath下的META-INF/spring.factories文件，读取需要自动装配的配置类

4）依据条件筛选的方式，把不符合的配置类移除掉，最终完成自动装配

**Spring Boot的主要优点：**

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

### YML

#### yml配置优先级顺序

- ​	项目下config文件下
  - 项目下
    - classpath(就是resource文件夹下)\config\文件下
      - classpath下

- yml的@ConfigurationProperties

- 松散绑定 yml 中last-name 与 lastName一样

- JSR303 数据效验类似于一层过滤器验证，保证数据合法

- 复杂类型封装，利用yml封装对象，使用@value就不支持了



### 注解集

- @RestController = @Controller + @RespnseBody(用于返回Json数据，异步处理Ajax会用)

- @RequsetMapping 通过返回跳转路径 + @RespnseBody返回值会写入到HTTP response body中，




### Springboot Web开发

- static资源
- 首页
- jsp,模板引擎Themeleaf
- 装配扩展
- crud
- 拦截器
- 国际化（中英文切换）

#### @PathVariable，@RequestParam，@RequestBody三者比较

```java
注解 	支持的类型 	支持的请求类型 	支持的Content-Type 	请求示例

@PathVariable 	url 	GET 	所有 	/test/{id}
@RequestParam 	url 	GET 	所有 	/test?id=1
                Body 	POST/PUT/DELETE/PATCH 	form-data或x-www.form-urlencoded 	id:1
@RequestBody 	Body 	POST/PUT/DELETE/PATCH 	json 	{"id":1}

将接口改成以@RequestBody注解方式接受json请求数据，而后将接收到的json数据转化为json对象，可以使用json对象的get()方法取得参数值，代码如下：

@PostMapping("/account")
public Object insertAccount(@RequestBody JSONObject jsonParam) {
	String userName=jsonParam.get("userName").toString()
	...
```



#### 静态资源如何导入？

spring.mvc.static.path

​	资源访问的优先级来说，1.resources   2.static   3. public   可以直接访问 , 但若设置过static.path的话，直接就return了，不会看后面的了

源码如下

```java
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
      "classpath:/resources/", "classpath:/static/", "classpath:/public/" };
```

webjars:

- ​	springboot可以以maven方式导入jquery



- 在templates的页面一般只通过controller来访问
- 改变首页路径需要用模板引擎thymeleaf

#### 使用hashmap<>模拟数据完成Dao层功能

```java
private static Map<Integer, Department> departments = null;

static{
    departments = new HashMap<Integer,Department>();

    departments.put(101,new Department(101,"销售部"));
    departments.put(102,new Department(102,"采购部"));
    departments.put(103,new Department(103,"运营部"));
    departments.put(104,new Department(104,"教育部"));
    departments.put(105,new Department(105,"工商部"));
}
```

以此为参考



### Thymeleaf的使用

- html标签加入

  ```html
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
      
  <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">
  <!-- Custom styles for this template -->
  <link th:href="@{/css/signin.css}" rel="stylesheet">
  ```



```properties
#关闭缓存
spring.thymeleaf.cache = false
```



关于前端模板的使用，

一般顶部导航栏topbar和侧边栏sidebar写在commons中

```html
<!--导航栏-->
<div th:insert="~{common/commons::topbar(active='list.html')}"></div>
<!--侧边栏-->
				<div th:insert="~{common/commons::sidebar(active='list.html')}"></div>
```

#### Durid

监控数据的，springboot数据池用的HikariDataSource （号称java WEB最快的连接池）

**！！！别忘了加log4j的依赖**

对应一个个的Config

将springboot的datasource的type设置为Druid，数据库底层仍然是jdbc

- filter 过滤 属性为字符串，stat为监控，log4j为日志，wall为防止sql注入

  Map<String,String>里加配置

  exclusions里*.js,*.css,/druid/*不进行统计

- mock 测试

- pool 测试

- proxy 代理

- sql ,Utils封装了一些东西可以toSting

- support 支持，ibatis,hibernate,spring

配置：

- 登陆的人，密码，可以访问权限人，不要忘记加上@Bean
- springboot内置serlvet，想使用可以用替代方法

##### 配置

```java
/*
   将自定义的 Druid数据源添加到容器中，不再让 Spring Boot 自动创建
   绑定全局配置文件中的 druid 数据源属性到 com.alibaba.druid.pool.DruidDataSource从而让它们生效
   @ConfigurationProperties(prefix = "spring.datasource")：作用就是将 全局配置文件中
   前缀为 spring.datasource的属性值注入到 com.alibaba.druid.pool.DruidDataSource 的同名参数中
 */
@ConfigurationProperties(prefix = "spring.datasource")
@Bean
public DataSource druidDataSource() {
    return new DruidDataSource();
}


//配置 Druid 监控管理后台的Servlet；
//内置 Servlet 容器时没有web.xml文件，所以使用 Spring Boot 的注册 Servlet 方式
//后台监控:相当于web.xml,ServletRegistrationBean
@Bean
public ServletRegistrationBean statViewServlet() {
    ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");

    // 这些参数可以在 com.alibaba.druid.support.http.StatViewServlet
    // 的父类 com.alibaba.druid.support.http.ResourceServlet 中找到
    //后台需要有人登陆,账号密码配置
    Map<String, String> initParams = new HashMap<>();
    initParams.put("loginUsername", "admin"); //后台管理界面的登录账号,key是固定的
    initParams.put("loginPassword", "123456"); //后台管理界面的登录密码

    //后台允许谁可以访问
    //initParams.put("allow", "localhost")：表示只有本机可以访问
    //initParams.put("allow", "")：为空或者为null时，表示允许所有访问
    initParams.put("allow", "");
    //deny：Druid 后台拒绝谁访问
    //initParams.put("deny", "192.168.1.20");表示禁止此ip访问

    //设置初始化参数
    bean.setInitParameters(initParams);
    return bean;
}



//配置 Druid 监控 之  web 监控的 filter
//WebStatFilter：用于配置Web和Druid数据源之间的管理关联监控统计
@Bean
public FilterRegistrationBean webStatFilter() {
    FilterRegistrationBean bean = new FilterRegistrationBean();
    bean.setFilter(new WebStatFilter());

    //exclusions：设置哪些请求进行过滤排除掉，从而不进行统计
    Map<String, String> initParams = new HashMap<>();
    initParams.put("exclusions", "*.js,*.css,/druid/*,/jdbc/*");
    bean.setInitParameters(initParams);

    //  "/*" 表示过滤所有请求
    bean.setUrlPatterns(Arrays.asList("/*"));
    return bean;
}
```

```yaml
spring:
  datasource:
    username: root
    password: 1328910
    url: jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
    driver-class-name: com.mysql.cj.jdbc.Driver

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

### 整合mybatis

配置mbatis-spring-boot-starter

配置的mapper接口需要@Mapper

Dao层加个repository

- @Component 是一个泛化的概念，仅仅表示一个组件 (Bean) ，可以作用在任何层次。
- @Service 通常作用在业务层，但是目前该功能与 @Component 相同。
- @Constroller 通常作用在控制层，但是目前该功能与 @Component 相同。

或者再springboot加载应用里加个@MapperScan("com.lgd.mapper")

Propertier中整合mybatis

mabatis.type-aliases-package = com.lgd.pojo

//(classpath指resources文件夹)

mybatis.mapper-locations = classpath:mybatis/mapper/*

### SpringSecurity

引入spring-boot-starter-security

与shiro很像，认证，授权（vip1,vip2,vip3）

- 功能权限
- 访问权限
- 菜单权限

@EnalbleWebSecurity

@Enablexxxxx 这个格式

安全：认证与授权

密码编码：passwordencoder，

BCrytPasswordEncoder，java原生md5,但是似乎已经不安全了

MD5+salt仍安全，

使用thymeleaf

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5">
 <a class="item"  th:href="@{/index}">首页</a>

            <!--登录注销-->
            <div class="right menu">
                <!--未登录-->
                <div sec:authorize="!isAuthenticated()">
                    <a class="item" th:href="@{/toLogin}">
                        <i class="address card icon"></i> 登录
                    </a>
                </div>


                <!--注销-->
                <div sec:authorize="isAuthenticated()">
                    <a class="item">
                        用户名:<span sec:authentication="name"></span>
                        角色:<span sec:authentication="principal.authorities"></span>
                    </a>
                </div>

                <div sec:authorize="isAuthenticated()">
                    <a class="item" th:href="@{/logout}">
                        <i class="sign-out icon"></i> 注销
                    </a>
                </div>


                <!--已登录
                <a th:href="@{/usr/toUserCenter}">
                    <i class="address card icon"></i> admin
                </a>
                -->
            </div>
        </div>
    </div>
<!--根据权限来显示内容-->
<div class="column" sec:authorize="hasRole('vip1')">
                <div class="ui raised segment">
                    <div class="ui">
                        <div class="content">
                            <h5 class="content">Level 1</h5>
                            <hr>
                            <div><a th:href="@{/level1/1}"><i class="bullhorn icon"></i> Level-1-1</a></div>
                            <div><a th:href="@{/level1/2}"><i class="bullhorn icon"></i> Level-1-2</a></div>
                            <div><a th:href="@{/level1/3}"><i class="bullhorn icon"></i> Level-1-3</a></div>
                        </div>
                    </div>
                </div>
            </div>
```

总体就是，前端传uesrname和password和checkbox的remeberme三个变量

然后springsecurity可以设置获取的这三个变量的名称以及自己顶定制登陆页面和表单提交请求的url，

```java
http.formLogin()
        .usernameParameter("username")
        .passwordParameter("password")
        .loginPage("/toLogin")//定制登陆页
        .loginProcessingUrl("/login"); // 登陆表单提交请求
http.csrf().disable();//关闭csrf功能:跨站请求伪造,默认只能通过post方式提交logout请求

        http.logout().logoutSuccessUrl("/");

        //记住我
        http.rememberMe().rememberMeParameter("remember");
```

### shiro

###### 依赖

```xml
<!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>

<!--
Subject 用户
SecurityManager 管理所有用户
Realm 连接数据
-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.12</version>
</dependency>

<!--shiro-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.1</version>
</dependency>

<!--thymeleaf模板-->
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>

<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```

###### 前端的使用示例

```html
<div th:if="${session.loginUser==null}">
    <a th:href="@{/toLogin}">登录</a>
</div>
<div shiro:hasPermission="user:add">
    <a th:href="@{/user/add}">add</a>
</div>
<div shiro:hasPermission="user:update">
    <a th:href="@{/user/update}">update</a>
</div>
```

###### shiroconfig基本配置参考实例

```java
@Configuration
public class ShiroConfig {

    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("getDefaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        bean.setSecurityManager(defaultWebSecurityManager);
        //添加shiro的内置过滤器
        /*
            anon:无需认证就能访问
            authc:必须认证才能访问
            user:必须拥有记住我功能才能访问
            perms:拥有某个资源的权限才能访问
            role:拥有某个角色权限才能访问
         */
        //拦截
        Map<String,String> filter = new LinkedHashMap<>();

        //授权
        filter.put("/user/add","perms[user:add]");
        filter.put("/user/update","perms[user:update]");
        //登录的请求
        bean.setLoginUrl("/toLogin");
        //未授权的请求
        bean.setUnauthorizedUrl("/noauth");

        bean.setFilterChainDefinitionMap(filter);

        return bean;
    }

    @Bean
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        defaultWebSecurityManager.setRealm(userRealm);

        return defaultWebSecurityManager;
    }

    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }

    //整合shiroDialect:用来整合shiro thymeleaf
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
}
```

###### UserRealm，授权认证实例

```java
public class UserRealm extends AuthorizingRealm {

   @Autowired
    UseraService useraService;

    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权");

        SimpleAuthorizationInfo info=new SimpleAuthorizationInfo();

        //info.addStringPermission("user:add");
        //拿到当前用户登陆对象
        Subject subject= SecurityUtils.getSubject();
        Usera currentUser= (Usera) subject.getPrincipal();//拿到User对象
        info.addStringPermission(currentUser.getPerms());//设置当前用户对象

        return info;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证");

        //用户名，密码，数据库中获取
        Usera usera = null;
        UsernamePasswordToken userToken=(UsernamePasswordToken) authenticationToken;
        try {
            usera = useraService.queryByName(userToken.getUsername());//获取用户名
        }catch (Exception e){
            System.out.println(e);
        }
        if(usera==null){//说明查无此人
            return null;
        }
        String name= usera.getName();
        String password = usera.getPwd();
        //密码认证,shiro做
        return new SimpleAuthenticationInfo(usera,password,"");//放入User对象
    }
}
```

### Swagger 2021/10/27

###### 依赖

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

###### 配置

```java
@Configuration
@EnableSwagger2
///swagger-ui.html
public class SwaggerConfig {
    @Bean
    public Docket createRestApi(Environment environment) {
        //获取要显示的swagger的环境
        Profiles profiles=Profiles.of("dev","test");

        //获取当前项目环境是否为指定环境
        Boolean flag=environment.acceptsProfiles(profiles);

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                //设置分组
                .groupName("lgd")
                //是否启用Swagger,false则不能浏览器访问
//                .enable(flag)
                .select()// 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口

                //指定要扫描的包
                /*
                any() // 扫描所有，项目中的所有接口都会被扫描到
                none() // 不扫描接口
                // 通过方法上的注解扫描，如withMethodAnnotation(GetMapping.class)只扫描get请求
                withMethodAnnotation(final Class<? extends Annotation> annotation)
                // 通过类上的注解扫描，如.withClassAnnotation(Controller.class)只扫描有controller注解的类中的接口
                withClassAnnotation(final Class<? extends Annotation> annotation)
                basePackage(final String basePackage) // 根据包路径扫描接口
                 */
                ////为当前包路径
                .apis(RequestHandlerSelectors.basePackage("com.lgd.controller"))
                .paths(PathSelectors.any())
                //过滤路径
//                .paths(PathSelectors.ant("/lgd/**"))
                .build();
    }

    private ApiInfo apiInfo() {
        Contact contact = new Contact("lgd","https://www.zust.edu.cn","2709296991@qq.com");
        return new ApiInfoBuilder()
                .title("接口文档")
                .description("lgd学习swagger")
                .contact(contact)
                .termsOfServiceUrl("https://www.zust.edu.cn")
                .version("1.0")
                .build();
    }
}
```

### 分布式，异步处理

异步任务

所在方法@Async

Application 上@EnableAsync

多线程

定时任务 timer

###### 邮件发送

mail的配置

```properties
spring.mail.username=1017276522@qq.com
spring.mail.password=dedjgrzqiskubdij
spring.mail.host=smtp.qq.com
spring.mail.properties.mail.smtp.ssl.enable=true
```

```java
@Autowired
    JavaMailSender javaMailSender;

    @Test
    void contextLoads() {

        SimpleMailMessage mailMessage=new SimpleMailMessage();
        mailMessage.setSubject("你好JavaMailSender");
        mailMessage.setText("hello world!!!");

        mailMessage.setTo("1017276522@qq.com");
        mailMessage.setFrom("1017276522@qq.com");
//        for (int i = 0; i < 2; i++) {
            javaMailSender.send(mailMessage);
//        }
    }

    @Test
    public void contextLoads2() throws MessagingException {
        //邮件设置2：一个复杂的邮件
        MimeMessage mimeMessage = javaMailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);

        helper.setSubject("通知-明天上午上课");
        helper.setText("<b style='color:red'>明天 8:00上课</b>",true);

        //发送附件
        helper.addAttachment("1.jpg",new File("C:\\Users\\Asus\\Pictures\\Saved Pictures\\Ren.jpg"));

        helper.setTo("1017276522@qq.com");
        helper.setFrom("1017276522@qq.com");

        javaMailSender.send(mimeMessage);
    }
```

###### 定时任务

@EnableScheduling //开始定时功能

TaskScheduler  任务调度者

TaskExecutor 任务执行者

Cron表达式

16:34.40

```java
@Scheduled(cron = "40 34 16 * * ?")
public void Hellos(){
    SimpleDateFormat df = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
    System.out.println("执行成功，现在是" +df.format(new Date()));
}
```

### Springboot继承Redis

留着学完Redis再来看 = =

### 分布式 Dubbo + Zookeeper + SpringBoot



Netty网络框架，约一个月，里头需要学习计算机网络

Zookeeper:

下载地址：https://dlcdn.apache.org/zookeeper/zookeeper-3.7.0/

简单部署教程：（需要系统配置JAVA_HOME变量）

https://blog.csdn.net/qq_33316784/article/details/88563482



npm镜像配置：

$ npm config set registry https://registry.npm.taobao.org 
$ npm info underscore （如果上面配置正确这个命令会有字符串response）



##### springboot读取resources文件夹下边的文件

```java 
//第一种方法
File file =  ResourceUtils.getFile("classpath:template/科研项目模板.xlsx");
//获取文件的相对路径  可在控制台打印查看输出结果
String filePath = ResourceUtils.getFile("classpath:template/科研项目模板.xlsx").getPath();
//第二种方法
//直接将目标文件读成inputstream  this指当前类的实例对象
InputStream ins = this.getClass().getClassLoader().getResourceAsStream("template/科研项目模板.xlsx");
File file = new File(ins);
```

### 异步任务

这次开发有个接口在一张有4亿条数据的表中做四次sql，十分耗时，所以需要使用**异步调用**，之前会自己维护一个线程池，然后异步调用，但是在Spring环境下可以一个@Async轻松搞定。

@Async

基于@Async标注的方法，称之为异步方法；这些方法将在执行的时候，将会在独立的线程中被执行，调用者无需等待它的完成，即可继续其他的操作

AsyncConfig示例

```java
@Configuration
@EnableAsync // 开启异步调用功能，即使@Async注解生效
public class AsyncConfig implements AsyncConfigurer {

    private final static Logger log = Logger.getLogger(AsyncConfig.class);

    @Bean(name = "default_async_pool", destroyMethod = "shutdown")
    public ThreadPoolTaskExecutor defaultAsyncPool() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        // 设置线程池前缀：方便排查
        executor.setThreadNamePrefix("default-async-");
        // 设置线程池的大小
        executor.setCorePoolSize(10);
        // 设置线程池的最大值
        executor.setMaxPoolSize(15);
        // 设置线程池的队列大小
        executor.setQueueCapacity(250);
        // 设置线程最大空闲时间，单位：秒
        executor.setKeepAliveSeconds(3000);
        // 饱和策略
        // AbortPolicy:直接抛出java.util.concurrent.RejectedExecutionException异常
        // CallerRunsPolicy:若已达到待处理队列长度，将由主线程直接处理请求
        // DiscardOldestPolicy:抛弃旧的任务；会导致被丢弃的任务无法再次被执行
        // DiscardPolicy:抛弃当前任务；会导致被丢弃的任务无法再次被执行
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.DiscardPolicy());
        return executor;
    }

    @Bean(name = "another_async_pool", destroyMethod = "shutdown")
    public ThreadPoolTaskExecutor anotherAsyncPool() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setThreadNamePrefix("another-task-");
        executor.setCorePoolSize(3);
        executor.setMaxPoolSize(6);
        executor.setQueueCapacity(5);
        executor.setKeepAliveSeconds(10);
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.DiscardPolicy());
        return executor;
    }

    /**
     * 自定义异步线程池，若不重写，则使用默认的
     */
    @Override
    public Executor getAsyncExecutor() {
        return defaultAsyncPool();
    }

    /**
     * 1.无参无返回值方法
     * 2.有参无返回值方法
     * 返回值为void的， 通过IllegalArgumentException异常, AsyncUncaughtExceptionHandler处理异常
     * 3.有参有返回值方法
     * 返回值是Future，不会被AsyncUncaughtExceptionHandler处理，需要我们在方法中捕获异常并处理
     * 或者在调用方在调用Future.get时捕获异常进行处理
     */
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        System.out.println("正在处理无返回值的@Async异步调用方法");
        return (throwable, method, objects) -> {
            log.info("Exception message - " + throwable.getMessage());
            log.info("Method name - " + method.getName());
            for (Object param : objects) {
                log.info("Parameter value - " + param);
            }
        };
    }

}
```

写完咯

示例

controller层

```java
Future<String> count = service.oracleCount();
            Future<List<Map<String, Object>>> mapList = service.oracleOldestData();
            Future<String> newTime = service.oracleNewTime();
            Boolean result = false;
            while (!result) {
                Thread.sleep(1000);
                if (count.isDone() && mapList.isDone() && newTime.isDone()) {
                    //通过Future的isDone方法来查询Future是否执行完毕
                    result = true;
                    break;
                }
            }
```

service层

```java
@Async
    @Override
    public Future<String> oracleCount() throws InterruptedException{
        String count = jdbcTemplate.queryForObject(
                "select count(1) from BMA_RT_CAMP_POSITION_DATA", String.class);
        //按照自己实际所需的逻辑写方法代码即可，入参视实际情况而定
        return new AsyncResult<String>(count);
    }

    @Async
    @Override
    public Future<List<Map<String, Object>>> oracleOldestData() throws InterruptedException{
        List<Map<String, Object>> mapList = jdbcTemplate.queryForList(
                "select a.CAMP_ID,a.TIMESTAMP from BMA_RT_CAMP_POSITION_DATA a where TIMESTAMP =\n" +
                        "(select min(TIMESTAMP) from BMA_RT_CAMP_POSITION_DATA where USED = '0')");
        return new AsyncResult<>(mapList);
    }

    @Async
    @Override
    public Future<String> oracleNewTime() throws InterruptedException {
        String newTime = jdbcTemplate.queryForObject(
                "SELECT TO_CHAR(TO_DATE(SUBSTR(MAX_TIME,1,14),'YYYYMMDDHH24MISS'),'YYYY-MM-DD HH24:MI:SS') FROM ( " +
                        "select max(TIMESTAMP) AS MAX_TIME from BMA_RT_CAMP_POSITION_DATA)", String.class);
        //按照自己实际所需的逻辑写方法代码即可，入参视实际情况而定
        return new AsyncResult<String>(newTime);
    }
```

### 注解集

@RequestAttribute与@RequestParam与@RequestBody

- @RequestAttribute注解的参数在项目里是自己解析出来的，并不是前端传递的。具体一点，在项目里的拦截器里会对Token信息进行解析，解析出来的参数重新放在请求里（用httpServletRequest.setAttribute(name, value)），后边接口接收参数时就用这个注解。
- @RequestParam注解则表示这个参数是通过前端传递过来的，如果请求里没有这个参数，则会报错400 Bad Request。这个注解用来解析请求路径里的参数（get请求）或者post请求中form表单格式的请求参数；
- @RequestBody注解用来接收POST请求BODY里的参数，格式为JSON格式。
