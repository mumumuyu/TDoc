# Vue

#### 第一个demo

```html
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<body>
<div id="app">
    {{msg}}
</div>
<script>
    var vm = new Vue({
        el:"#app",
        data:{
            msg:"Hello!Vue!"
        }
    })
</script>
</body>
```

##### 悬停时显示信息

```html
<span v-bind:title="msg">
    悬停查看信息。
</span>
```

这种v-开头的是指令，用标签的形式绑定，不用标签也可以{{}}

##### if

```html
<p v-if="ok==='ok的'">这是true</p>
<p v-else>这是false</p>
```

对于===，因为js是弱类型数据，1和"1"==是true,所以用===要求连类型也一样，为绝对等于

##### for循环

```html
<p v-for="(item,index) in items">{{item.msg}}--{{index+1}}</p>

items: [
    {msg:"lgd学Vue"},
    {msg:"lgd学Java"},
    {msg:"lgd学javaWeb"}
]
```

##### v-on

```html
<button v-on:click="count+=1">add 1</button>
{{count}}
```

vue方法定义到methods中

```html
<button v-on:click="sayHi">click me to say hi</button>

methods:{
    sayHi: function (){
        alert(this.ok)
    }
}
```

MVVM:

##### 双向数据绑定

view viewmodel

```html
<input type="text" v-model="inmsg"/>{{inmsg}}

inmsg:""
```

radio中

```html
<input type="radio" value="男" v-model="sex"/>男
<input type="radio" value="女" v-model="sex"/>女
<br>
<p>你选中了 {{sex}}</p>

 sex:""
```

select:

```html
<select v-model="abc">
        <option value="" disabled>--请选择--</option>
        <option value="A">A</option>
        <option value="B">B</option>
        <option value="C">C</option>
        <option value="D">D</option>
    </select>
```

