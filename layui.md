# layui

layui的使用

jsp中加入

```jsp
<link rel="stylesheet" type="text/css" href="${pageContext.request.contextPath}/static/layui/css/layui.css">
<script src="${pageContext.request.contextPath}/static/layui/layui.js" type="text/javascript" charset="UTF-8"></script>
```

body中加入

```jsp
<script>
    layui.use(['layer', 'form'], function(){
        var layer = layui.layer
            ,form = layui.form
            ,table = layui.table;

        layer.msg('欢迎你');
    });
</script>
```



##### layui中富文本编辑器LayEdit的使用

   html部分：

```html
<textarea id="demo" style="display: none;"></textarea>

js部分：
<script>
//编辑器
layui.use('layedit', function(){
    var layedit = layui.layedit;
    layedit.build('demo');   //建立编辑器
    layedit.set({
        //暴露layupload参数设置接口 --详细查看layupload参数说明
        uploadImage: {
            url: ''    //上传接口url
            ,type: 'post' //默认post
        }
        , devmode: true
        //插入代码设置
        , codeConfig: {
            hide: true,  //是否显示编码语言选择框
            default: 'javascript' //hide为true时的默认语言格式
        }
        , tool: [
            'html', 'code', 'strong',  'underline', 'del', 'addhr', '|', 'fontFomatt', 'colorpicker', 'face'
            , '|', 'left', 'center', 'right', '|', 'link', 'unlink','images', 'image_alt',
            , '|', 'fullScreen'
        ]
        ,height: 500, //设置编辑器高度
    });
});
</script>

取值：
var layedit = layui.layedit;
var index= layedit.build('demo',{

});
var content = layedit.getContent(index);  //获取html
```

##### layedit图片上传功能实现

```html
//别忘了调用layui/lay/modules/layedit.js
layui.use(['layedit', 'layer', 'jquery'], function () {
            var $ = layui.jquery;
            var layedit = layui.layedit;
            layedit.set({
                //暴露layupload参数设置接口 --详细查看layupload参数说明
                //这里layui会用js用post方式给后台传递一下参数
                uploadImage: {
                    url: '/Attachment/LayUploadFile',//接口地址
                    accept: 'image',
                    acceptMime: 'image/*',
                    exts: 'jpg|png|gif|bmp|jpeg',
                    size: '10240'
                }
                , uploadVideo: {
                    url: '/Attachment/LayUploadFile',
                    accept: 'video',
                    acceptMime: 'video/*',
                    exts: 'mp4|flv|avi|rm|rmvb',
                    size: '20480'
                }
                //右键删除图片/视频时的回调参数，post到后台删除服务器文件等操作，
                //重点来了，这里layui会用post 方式给后台传你想删的图片路径，在后台你可以接受并删除
                //传递参数：
                //图片： imgpath --图片路径
                //视频： filepath --视频路径 imgpath --封面路径
                , calldel: {
                    url: '/Attachment/DeleteFile'
                }
                //开发者模式 --默认为false
                , devmode: true
                //插入代码设置
                , codeConfig: {
                    hide: true,  //是否显示编码语言选择框
                    default: 'javascript' //hide为true时的默认语言格式
                }
                , tool: [
                    'html', 'code', 'strong', 'italic', 'underline', 'del', 'addhr', '|', 'fontFomatt', 'colorpicker', 'face'
                    , '|', 'left', 'center', 'right', '|', 'link', 'unlink',‘images’, 'image_alt', 'video', 'anchors'
                    , '|',‘table’, 'fullScreen'
                ]
                , height: '90%'
            });
            var ieditor = layedit.build('layeditDemo');
        })
```

##### 二级联动js的实现：

```javascript
//获取分类
            $.ajax({
              url: '/ajax/getAllCatas',
              dataType: 'json',
              type: 'get',
              headers : {
                Authorization: '[[${session.token}]]'
              },
              async:false,
              success: function (res) {
                optionforcata = "";
                optionforboke = "";
                if (res.code == 0){
                  for(var i in res.data){
                    optionforcata += "<option value='"+res.data[i]['id']+"'>"+res.data[i]['name']+"</option>";
                  }
                }else {
                  optionforcata += "<option value='0'>"+"权限不够"+"</option>";
                }
                $("[name='c_id']").append(optionforcata);
                form.render("select");
              }
            })
            //通过分类获取博客（二级联动的应用）
            form.on('select(c_id)', function(data) {
              $.ajax({
                type : 'POST',
                url : '/ajax/FindBokeByCata',
                data : {
                  'c_id' : $("#c_id").val()
                },
                dataType : 'json',
                success : function(res) {
                  optionforcata = "";
                  optionforboke = "";
                  //empty() 方法从被选元素移除所有内容
                  $("#b_id").empty();
                  if (res.code == 0){
                    for(var i in res.data){
                      optionforboke += "<option value='"+res.data[i]['id']+"'>"+res.data[i]['title']+"</option>";
                    }
                }else {
                  optionforboke += "<option value='0'>"+"权限不够"+"</option>";
                }
                $("[name='b_id']").append(optionforboke);
                  //重新刷新下拉框
                  form.render('select'); //重新渲染
                }
              });
            });
```

