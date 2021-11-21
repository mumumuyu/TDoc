# JavaWeb

# 1配置

### 	使用Meaven,导入一下依赖

```xml
<dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>compile</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.servlet.jsp/javax.servlet.jsp-api -->
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>javax.servlet.jsp-api</artifactId>
      <version>2.3.3</version>
      <scope>provided</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl-api -->
    <dependency>
      <groupId>javax.servlet.jsp.jstl</groupId>
      <artifactId>jstl-api</artifactId>
      <version>1.2</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/taglibs/standard standard标签数据库-->
    <dependency>
      <groupId>taglibs</groupId>
      <artifactId>standard</artifactId>
      <version>1.1.2</version>
```

### Tomcat

记得配置Deployment--Application context得到网址中的/xxx/

# 2各种实例

### hello

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        String username = (String) context.getAttribute("lgd");
        resp.getWriter().print(username + "真帅");
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

```java
resp.setContentType("text/html");//设置编码类型
resp.setCharacterEncoding("utf-8");//设置编码格式
PrintWriter writer = resp.getWriter();//屏幕中加入...
writer.print("<h1>404 NOT FOUND</h1>");
```

### 发送请求的实例

```java
@Override//发送请求下载实例
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //1.获取文件路径
    String realPath = "E:\\IDE\\javaweb\\servlet-one\\target\\classes\\德丽莎.jpg";
    System.out.println("获取的文件路径为:" + realPath);
    //2.获取文件名
    String filename = realPath.substring(realPath.lastIndexOf("\\")+1);
    //3.客户端可支持化(中文文件URLEncoder.encode(filename,"utf-8"))
    resp.setHeader("Content-disposition","attachment;filename="+ URLEncoder.encode(filename,"utf-8"));
    //4.获取文件输入流
    FileInputStream in = new FileInputStream(realPath);
    //5.创建缓冲区
    int len = 0;
    byte[] buffer = new byte[1024];
    //6.获取OutputStram对象
    ServletOutputStream out = resp.getOutputStream();
    //7.将FileInputStream流写入到buffer缓冲区
    while ((len = in.read(buffer))>0){
        out.write(buffer,0,len);
    }
    in.close();
    out.close();
}
```

### 生成图形化二维码

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //让浏览器三秒刷新一次
    resp.setHeader("refresh","3");
    //在内存中创建一个图片
    BufferedImage image = new BufferedImage(80,30, BufferedImage.TYPE_INT_RGB);
    //得到图片
    Graphics2D pen = (Graphics2D) image.getGraphics();
    //设置背景
    pen.setColor(Color.BLACK);
    pen.fillRect(0,0,80,30);
    //图片写数据
    pen.setColor(Color.WHITE);
    pen.setFont(new Font(null,Font.BOLD,30));
    pen.drawString(YZM(),0,30);
    //告诉浏览器以图片形式打开
    resp.setContentType("image/jpg");
    //不让浏览器不缓存
    resp.setDateHeader("sxpires",-1);
    resp.setHeader("Pragma","no-cache");
    //图片给浏览器
    ImageIO.write(image,"jpg",resp.getOutputStream());
}
//生成验证码
private String YZM(){
    char[]Code= {'3','4','5','6','7','8','9',
            'A','B','C','D','E','F','G','H','G',
            'K','L','M','N','P','Q','R','S','T','U','V',
            'W','X','Y','a','b','c',
            'd','e','f','h','j','k','m','n','p','q','r','s','t','u','v','w','x','y'};
    boolean []flags=new boolean[Code.length];
    int xuhao;
    char []Yzm=new char[4];
    for (int i = 0; i < Yzm.length; i++) {
        do {
            xuhao=(int)(Math.random()*Code.length);
        }while(flags[xuhao]==true);
        Yzm[i]=Code[xuhao];
        flags[xuhao]=true;
    }
    return String.valueOf(Yzm);
}
```

### 重定向

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.sendRedirect(getServletContext().getContextPath() + "/error");//重定向
}
```

### 转发

```java
//重定向有"/"
//        resp.sendRedirect( getServletContext().getContextPath()  + "/success.jsp");

        //转发 无"/"
        req.getRequestDispatcher("success.jsp").forward(req,resp);
```

### Cookie

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setCharacterEncoding("GBK");
    resp.setCharacterEncoding("GBK");
    PrintWriter out = resp.getWriter();
    Cookie[] cookies = req.getCookies();
    if(cookies != null){
        out.write("您上次访问的时间是:");
        for (int i = 0; i<cookies.length;i++){
            Cookie cookie = cookies[i];
            if(cookie.getName().equals("lastLogintime")){
                long lastLoginTime = Long.parseLong(cookie.getValue());
                Date date = new Date(lastLoginTime);
                out.write(date.toLocaleString());
            }
        }
    }else{
        System.out.println("您是第一次访问");
    }
    Cookie cookie = new Cookie("lastLogintime",System.currentTimeMillis()+"");
    resp.addCookie(cookie);
    //cokkie.setMaxage(24*60*60);
}
```

### Session

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setCharacterEncoding("GBK");
    resp.setCharacterEncoding("GBK");
    resp.setContentType("text/html;charset=utf-8");

    HttpSession session = req.getSession();

    session.setAttribute("names",new Person("测试",10));

    String sessionID = session.getId();

    if(session.isNew()){
        resp.getWriter().write("session创建成功,ID为" + sessionID);
    }else{
        resp.getWriter().write("session已存在,ID为" + sessionID);
    }
}
```

## Web.xml配置实例

### servlet的配置

```xml
<servlet><!--注册-->
  <servlet-name>hello</servlet-name>
  <servlet-class>com.lgd.servlet.HelloServlet</servlet-class>
</servlet>
<servlet-mapping><!--请求路径-->
  <servlet-name>hello</servlet-name>
  <url-pattern>/hello</url-pattern>
</servlet-mapping>
<servlet-mapping><!--请求路径-->
  <servlet-name>hello</servlet-name>
  <url-pattern>/servlet/hello</url-pattern>
</servlet-mapping>
```

### error-page的配置

```xml
<error-page>
  <error-code>500</error-code>
  <location>/Error/500.jsp</location>
</error-page>
```

### 过滤器

```xml
<filter>
  <filter-name>CharacterEncodingFilter</filter-name>
  <filter-class>com.lgd.filter.CharacterEncodingFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>CharacterEncodingFilter</filter-name>
  <url-pattern>/servlet/*</url-pattern>
  <!-- <url-pattern>/*</url-pattern>-->
</filter-mapping>
```

### 监听器

```xml
<listener>
  <listener-class>com.lgd.listener.OnlineCountListener</listener-class>
</listener>
```

## jsp

### 用法

#### 1.获取参数age给String 

request.getParameter("age")

#### 2.导入java包

<%@ page import="java.util.Date" %>

#### 3.注释快速生成

```jsp
<%--ctrl + shift + /--%>
```

#### 4.${pageContext.request.contextPath}代表当前项目

比如这么用

<h2>当前有 <span><%=this.getServletConfig().getServletContext().getAttribute("OnlineCount")%></span>人在线</h2>

# JSP

### 	1.cookie

cookie是一个由服务端生成发给客户端的，以key-value形式表达

##### 		cookie的设置

```java
	//将str存入Cookie
 	Cookie cookie = new Cookie("number",str);
 	//设置Cookie的存活期为600秒
 	cookie.setMaxAge(600);
 	//将Cookie保存于客户端
 	response.addCookie(cookie);
```

##### 		cookie的使用

```java
//从Cookie获得number
	String str = null;
	Cookie[] cookies = request.getCookies();
	for(int i=0;i<cookies.length;i++){
		if(cookies[i].getName().equals("number")){
			str = cookies[i].getValue();
			break;
		}
	}
	int number = Integer.parseInt(str);
```



### 2.jsp使用dao和bean

```java
//1.导入所用java类
<%@ page import="com.lgd.Bean.Student" %>
<%@ page import="java.util.ArrayList" %>
//2.创建对象
StudentDao studentdao = new StudentDao();
ArrayList students = studentdao.queryAllStudents();
//3.获取各个student
for (int i = 0; i < students.size(); i++) {
                Student student = (Student) students.get(i);
    //4.获取student的属性
    student.getId();
    
```
