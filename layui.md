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

