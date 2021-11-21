# AJAX

Asynchronous JavaScript and XML

他就是个WEB应用的技术，但是可以让网页更快，更流畅，体验更好

以下学习示例使用jquery，且框架采用Spring

原生ajax，需要再看

https://www.cnblogs.com/small-hao/p/13190663.html

示例：

html版本

```html
<script src="../js/jquery-3.6.0.js" type="text/javascript" charset="UTF-8"></script>
<script>
    function a3(){
            $.post({
                url:"/a5",
                data:{"pwd":$("#pwd").val(),"name":$("#name").val()},
                success:function(data) {
                    if (data.toString() == 'ok') {//信息核对成功
                        window.location.href="/index";
                    } else {
                        $('#loginInfo').css("color", "red");
                    }
                    $("#loginInfo").html(data);
                }
            })
        }
</script>
```

controller

```java
@RequestMapping("/a5")
@ResponseBody
public String ajax4(String name,String pwd){
    String msg="";
    if(name!=null){
        if("admin".equals(name)){
            msg="ok";
        }else{
            msg="用户名有误";
        }
    }
    if(pwd != null){
        if("123456".equals(pwd)){
            msg="ok";
        }else{
            msg="密码输入有误";
        }

    }
    return msg;
}
```



jsp版本

```html
$.post({
                url:"${pageContext.request.contextPath}/login.do",
                data:{"pwd":$("#pwd").val(),"id":$("#id").val()},
                success:function(data) {
                    var msg = JSON.parse(data).msg;
                    if (msg === 'ok') {//信息核对成功
                        layer.alert('登陆成功~!', {
                            skin: 'layui-layer-molv' //样式类名
                            ,closeBtn: 0
                        }, function(){
                            var index = layer.load(0, {shade: false}); //0代表加载的风格，支持0-2
                            window.location.href="${pageContext.request.contextPath}/jsp/theme.jsp";
                        });
                    } else {
                        layer.alert('用户名或密码错误！', {
                            icon: 5,
                            title: "提示"
                        });
                        $(".acc2").val('');
                        draw(show_num);
                        return;
                    }
                }
            })
```

servlet

```java
String msg = null;
//获取用户名和密码
String id = req.getParameter("id");
String pwd = req.getParameter("pwd");
//调用service方法，进行用户匹配
UserService userService = new UserServiceImpl();
User user = userService.login(id,pwd);
PrintWriter out = resp.getWriter();
if(null != user){
   //登录成功
   System.out.println("succeed ============ " );
   req.getSession().setAttribute(Constants.USER_SESSION,user);
   msg="ok";
}else {
   System.out.println("登录失败 ============ ");
   msg="no";
}
JSONObject jsonObject = new JSONObject();
jsonObject.put("msg", msg);
resp.setCharacterEncoding("utf-8");
resp.getWriter().write(jsonObject.toString());
```
