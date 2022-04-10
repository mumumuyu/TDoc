# Spring MVC

MVC一次请求的全过程
1.用户发起请求
2.中央控制器dispatcherServlet
3.dispatcherServlet调用处理器映射器handlerMapping
4.handlerMapping找到对应处理器，并返回对应的处理器对象handler给中央控制器
5.dispatcherServlet将handler给handlerAdapter处理器适配器
6.handlerAdapter调用handler处理器（controller）
7.controller调用业务层
8.业务层调用dao层
9.dao层调用jdbc或Mybatis对数据库操作返回给业务层
10.controller得到业务层返回的数据，返回modelandview
11.dispatcherServlet调用视图解析器ViewResolve解析modelandview
12.ViewResolve返回view
13.前端指定页面得到model内数据并进行渲染

Spring MVC执行流程，重点！！！

及SSM框架的整合



实体类：用户名，密码，生日，爱好，....20+个

前端只要用户名+密码



那么pojo: User

vo:UserVo 视图层,细分了，去掉一些不需要的东西

MVC：

将业务逻辑，数据，显示分离开的方法组织代码

MVC用处是来降低视图与业务逻辑间的双向耦合

MVC是架构模式

Model:模型，提供数据，包含数据与行为，一般分离为Dao和Service.提供了数据查询与模型数据状态更新

View,进行模型展示，也就是界面

Controller:控制器，接收用户请求，委托为模型处理，处理完把数据返回给视图

常见mvc后端框架有：Struts,Spring MVC , ASP.NET MVC,Zend FrameWork,JSF;

前端MVC：Vue,angularjs,React,backbone

其他模式：MVP,MVVM

DispatcherServlet

## HelloSpringMVC

web.xml

```xml
<servlet>
  <servlet-name>springmvc</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

  <!--DispatcherServlet要绑定Spring的配置文件-->
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc-servlet.xml</param-value>
  </init-param>

  <!--启动级别-->
  <load-on-startup>1</load-on-startup>
</servlet>

<!--
在SpringMVC中
/：只匹配所有的请求，不会去匹配jsp页面
/*：匹配所有请求，包括jsp页面
-->


<servlet-mapping>
  <servlet-name>springmvc</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```

springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--视图解析器给ModelAndView
		先获取其数据，再解析他的视图名字，拼接视图名找到对应试图，最后将数据渲染到视图上
处理映射器-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!--处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器-->

    <!--视图解析器:DispatcherServlet给他的ModelAndView-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--BeanNameUrlHandlerMapping:bean-->
    <bean id="/hello" class="com.lgd.controller.HelloController"/>

</beans>
```

url基本都是前缀和后缀拼接起来的

其中springboot中的themleaf

在ThemleafProperties中人家前缀就是classpath:/templates/后缀就是.html给你写死了的

Controller

```java
ModelAndView mv = new ModelAndView();
//调用业务层
//封装对象放到mv中
mv.addObject("msg",Obect);
//封装要跳转的视图到mv中
mv.setViewName("hello");//我们在springmvc-servlet.xml中配置的bean

```

注意web.xml中springmvc的/与/*不同

/*包含所有的请求，包含jsp页面

/为所有请求，不包含Jsp

springmvc-servlet.xml:中需要有适配器，映射器，ViewResolver视图解析器（其中包含前缀prefix后缀suffix，一般都固定），正式开发可以自动配置

##### 注解版

需要增加的依赖

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.3.10</version>
  <scope>compile</scope>
</dependency>
```

如果一个类上有@Controller而且可以跳转页面，返回String值，那么它就会被视图解析器解析

### Restful风格

在spring mvc中可以使用@PathVariable注解吗，让Url携带方法参数的对应值

如

```java
	@RequestMapping("/add/{a}/{b}")
	public String index(@PathVariable int a,@PathVariable int b, Model model){
        int result = a + b;
        model.addAttribute("msg","结果为"+result);
        return "test";
    }
```

可以在RequestMethod中选择请求方式

如

- GET
- POST
- DELETE
- PUT
- Patch
- ...

组合注解：

@GetMapping

路径传变量，简洁，高效，支持缓存，相对安全

比如@GetMapping("/add/{a}/{b}")鬼知道你ab干啥用的

然后方法里用@PathVariable int a 

小黄鸭调试法，就是自己跟着代码走一遍

### SpringMVC的转发与重定向

当然，你可以使用纯纯的servlet的request和response

使用response.senRedirect("/sss")

reponse.forward("")

对于SpringMVC中，你可以使用

- return "/index"; //默认就是forward
- return "forward:/ index.jsp";//默认就是forward
- return "redirect:/ index.jsp"; //redirect；

对于视图解析器配置后

redirect:/xxx.jsp 并不会走视图解析器，且其不能访问Web-inf下的内容

而forward如果不写出来会走然后出现这种[/WEB-INF/jsp/hello.jsp.jsp] 畸形路径，显示就不走

### 接受请求参数与数据回显

@RequestParam("username") String name;

可以接受前端的参数名username

接受前端的对象：

如果前端传的多个参数与同一个后端类匹配，springmvc可以直接把参数自动装配成类对象接收，当然，条件比较苛刻，必须所有属性字段名字都一模一样

- Model方法不多，是精简版，只适合存储数据
- ModelMap,继承自LinkedMap,可以使用这个双向链表Map的全部功能
- ModelAndView 可以在存储数据的同时设置返回的视图

### SpringMVC的过滤器配置

```xml
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### JSON

前后端分离时代：

后端弄后端，提供接口，controller就是一个接口，url

json是种数据交换的格式，JavaScript Object Notation，

他就是个文本格式，String类型，它简介清晰

{"name":"LGD" ,  "age": "18"},Json键值对来保存js对象

- JSON.parse();把JSON字符串变JS对象

- JSON.stringify;把JS变JSON字符串

前端独立部署，渲染后端提供的数据

如前端定义对象

```javascript
var user={
    name:"lgd",
    age:18,
    sex:"男"
}
//把js变json
var json = JSON.stringify(user);
//console.log(json);
//把json变js
var obj = JSON.parse(json);
//console.log(obg);

```

工具包

#### Jackson

在springmvc中

用了@ResponseBody,它就不会走视图解析器，会直接返回一个字符串

用 ObjectMapper

mapper.writeValueAsString()//把一个字符串或值变成字符串

json乱码问题配置

```xml
<!--json乱码-->

<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

```java
@RequestMapping("/json2")
@ResponseBody
public String json2() throws JsonProcessingException {
    User user1 = new User(1,"lgd",18);
    User user2 = new User(2,"lgd",18);
    User user3 = new User(3,"lgd",18);
    List<User> list = new ArrayList<>();
    list.add(user1);
    list.add(user2);
    list.add(user3);
    list.add(user1);
    ObjectMapper objectMapper = new ObjectMapper();
    String str = objectMapper.writeValueAsString(list);
    return str;
}
```

### 拦截器的配置

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
        <!--包括这个请求下面的所有资源-->
        <mvc:mapping path="/**"/>
        <bean class="com.lgd.config.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>

<mvc:interceptors>
    <mvc:interceptor>
        <!--包括这个请求下面的所有资源-->
        <mvc:mapping path="/user/**"/>
        <bean class="com.lgd.config.LoginInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

```java
public class LoginInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session=request.getSession();
        //登陆页面放行
        //request.getRequestURI().contains("Login")
        if(request.getRequestURI().contains("login")){
         return true;
        }
        if(session.getAttribute("userLoginInfo")==null){
            request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request,response);
            return false;
        }
        return true;
    }
}
```

### 文件上传与下载

```xml
<!--文件上传配置-->
<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
    <property name="defaultEncoding" value="utf-8"/>
    <!-- 上传文件大小上限，单位为字节（10485760=10M） -->
    <property name="maxUploadSize" value="10485760"/>
    <property name="maxInMemorySize" value="40960"/>
</bean>
```

springmvc会帮我们封装文件解析

```java
//@RequestParam("file") 将name=file控件得到的文件封装成CommonsMultipartFile 对象
//批量上传CommonsMultipartFile则为数组即可
@RequestMapping("/upload")
public String fileUpload(@RequestParam("file") CommonsMultipartFile file , HttpServletRequest request) throws IOException, IOException {

    //获取文件名 : file.getOriginalFilename();
    String uploadFileName = file.getOriginalFilename();

    //如果文件名为空，直接回到首页！
    if ("".equals(uploadFileName)){
        return "redirect:/index.jsp";
    }
    System.out.println("上传文件名 : "+uploadFileName);

    //上传路径保存设置
    String path = request.getServletContext().getRealPath("/upload");
    //如果路径不存在，创建一个
    File realPath = new File(path);
    if (!realPath.exists()){
        realPath.mkdir();
    }
    System.out.println("上传文件保存地址："+realPath);

    InputStream is = file.getInputStream(); //文件输入流
    OutputStream os = new FileOutputStream(new File(realPath,uploadFileName)); //文件输出流

    //读取写出
    int len=0;
    byte[] buffer = new byte[1024];
    while ((len=is.read(buffer))!=-1){
        os.write(buffer,0,len);
        os.flush();
    }
    os.close();
    is.close();
    return "redirect:/index.jsp";
}

//编写Controller
/*
 * 采用file.Transto 来保存上传的文件
 */
@RequestMapping("/upload2")
public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {

    //上传路径保存设置
    String path = request.getServletContext().getRealPath("/upload");
    File realPath = new File(path);
    if (!realPath.exists()){
        realPath.mkdir();
    }
    //上传文件地址
    System.out.println("上传文件保存地址："+realPath);

    //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
    file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));

    return "redirect:/index.jsp";
}


@RequestMapping(value="/download")
public String downloads(HttpServletResponse response , HttpServletRequest request) throws Exception{
    //要下载的图片地址
    String  path = request.getServletContext().getRealPath("/upload");
    String  fileName = "基础语法.jpg";

    //1、设置response 响应头
    response.reset(); //设置页面不缓存,清空buffer
    response.setCharacterEncoding("UTF-8"); //字符编码
    response.setContentType("multipart/form-data"); //二进制传输数据
    //设置响应头
    response.setHeader("Content-Disposition",
            "attachment;fileName="+ URLEncoder.encode(fileName, "UTF-8"));

    File file = new File(path,fileName);
    //2、 读取文件--输入流
    InputStream input=new FileInputStream(file);
    //3、 写出文件--输出流
    OutputStream out = response.getOutputStream();

    byte[] buff =new byte[1024];
    int index=0;
    //4、执行 写出操作
    while((index= input.read(buff))!= -1){
        out.write(buff, 0, index);
        out.flush();
    }
    out.close();
    input.close();
    return null;
}
```

