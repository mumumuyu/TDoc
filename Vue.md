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

##### Vue的组件

可以自定义标签

```html
<body>
    <div id = "app">
<!--        组件，传递组件值 props-->
        <lgd v-for="item in items" v-bind:lgd="item"></lgd>
    </div>
    <script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>
    Vue.component("lgd",{
        props:['lgd'],
        template: '<li>{{lgd}}</li>'
    });
    var vm = new Vue({
        el:"#app" ,
        data:{
            items:["java","linux","Vue","sql"]
        }
    });
</script>
</body>
```

#### Axios

axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范，有以下特点：

- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF

不建议使用jQuery,因为他操作Dom频繁

#####  Vue生命周期

![img](https://upload-images.jianshu.io/upload_images/13119812-5890a846b6efa045.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

##### 引入json

，如此简单

```html
<div id="app">
  <p>
      {{msg.age}}的{{msg.name}}
      使用的角色是{{msg.wife.city}}的
      {{msg.wife.name}}
  </p>
</div>
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
  var vm = new Vue({
    el: "#app",
    data(){
        return {
            // 其实这里用''也行
            msg: ''
            // msg:{
            //     name: null,
            //     age: null,
            //     wife: {
            //         name: null,
            //         city: null
            //     }
            // }
        }
    },
    mounted(){//链式编程
        axios.get('data.json').then(response=>(this.msg = response.data));
    }
  });
</script>
```

##### 计算属性

：计算出来的结果 保存在属性中，内存中运行：虚拟Dom

就是可以缓存数据变成属性

```HTML
<div id="app">
    <p>{{currentTime2()}}</p>
    <p>{{currentTime3}}</p>
</div>
<script src="https://cdn.bootcss.com/vue/2.5.21/vue.min.js"></script>
<script>
  var vm = new Vue({
      el:"#app",
      data:{
          message:"hello~lgd~"
      },
      methods: {
          currentTime2: function () {
              return Date.now();
          }
      },
      computed:{//计算属性
          currentTime3: function () {
              return Date.now();
          }
      }
  });
</script>
```

插槽slot:

```html
<div id="app">
    <book-title slot="book-title" :title="title"></book-title>
    <book-items v-for="item in items" :items="item"></book-items>
</div>
<script src="https://cdn.bootcss.com/vue/2.5.21/vue.min.js"></script>
<script>
    Vue.component("book",{
        template: '<div>\
                <slot name="book-title"></slot>\
                <ul>\
                    <slot name="book--items"></slot>\
                </ul>\
            </div>'
    });
    Vue.component("book-title",{
        props:['title'],
       template: '<div>{{title}}</div>'
    });
    Vue.component("book-items",{
        props:['items'],
        template: '<li>{{items}}</li>'
    });
    var vm = new Vue({
        el:"#app",
        data:{
            title:"书籍",
            items:['Vue','Java','Linux']
        }
    });
</script>
```

##### 自定义绑定事件

```html
<book-items v-for="(item,index) in items"
            v-on:remove="removeItems(index)" :key="index"
            :items="item" :index="index"></book-items>

    Vue.component("book-items",{
        props:['items','index'],
        template: '<li>{{items}}<button @click="remove(index)">删除</button></li>',
        methods:{
            remove:function (index) {
                console.log("删除了"+this.items[index]);
                // vm.items.splice(index,1);
                this.$emit("remove",index);
            }
        }
    });
    var vm = new Vue({
        el:"#app",
        data:{
            titlea:"书籍",
            items:['Vue','Java','Linux']
        },
        methods:{
            removeItems:function (index) {
                console.log("删除了"+this.items[index]);
                this.items.splice(index,1);
            }
        }
    });
```



```html
<div id="app">
    <book-title slot="book-title" :title="titlea"></book-title>
    <book-items v-for="(item,index) in items" :items="item" :index="index"></book-items>
</div>
<script src="https://cdn.bootcss.com/vue/2.5.21/vue.min.js"></script>
<script>
    Vue.component("book",{
        template: '<div>\
                <slot name="book-title"></slot>\
                <ul>\
                    <slot name="book--items"></slot>\
                </ul>\
            </div>'
    });
    Vue.component("book-title",{
        props:['title'],
       template: '<div>{{title}}</div>'
    });
    Vue.component("book-items",{
        props:['items','index'],
        template: '<li>{{items}}<button @click="remove(index)">删除</button></li>',
        methods:{
            remove:function (index) {
                console.log("删除了"+this.items[index]);
                vm.items.splice(index,1);
            }
        }
    });
    var vm = new Vue({
        el:"#app",
        data:{
            titlea:"书籍",
            items:['Vue','Java','Linux']
        },
        methods:{
            removeItems:function (index) {
                console.log("删除了"+this.items[index]);
                this.items.splice(index,1);
            }
        }
    });
</script>
```

所以可以发现，使用Vue后，后端就再也不用管前端视图层东西，只需要把前端Vue部分的data使用axios通信传过去就好。

Vue核心就是数据驱动，组件化

常用属性:

- v-if
- v-else-if
- v-else
- v-for(item in tems)
- on @clock
- v-model 双向绑定数据
- v-bind 绑定组件元素element

组件化

- slot插槽
- 内部绑定事件用到this.$emit("event",num)
- 计算属性，缓存计算数据

Vue的开发都基于NodeJS,实际开发采用Vue-cli脚手架开发，Vue-router路由，vuex做状态管理；Vue UI，界面我们一般使用ElementUI,ICE来快速搭建

