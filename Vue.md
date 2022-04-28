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

![img](C:\Users\L\Desktop\文档\photo\webp.webp)

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

### Vue-cli

```
Project name：项目名称，默认回车即可
Project description：项目描述，默认回车即可
Author：项目作者，默认回车即可
Install vue-router：是否安装vue-router，选择n不安装（后期需要再手动添加）
Use ESLint to lint your code:是否使用ESLint做代码检查，选择n不安装（后期需要再手动添加)
Set up unit tests:单元测试相关，选择n不安装（后期需要再手动添加）
Setupe2etests with Nightwatch：单元测试相关，选择n不安装（后期需要再手动添加）
Should we run npm install for you after the,project has been created:创建完成后直接初始化，选择n，我们手动执行；运行结果
```



```
npm config set registry https://registry.npm.taobao.org
npm config get registry

npm install cnpm-g
cnpm install vue-cli -g
vue list

vue init webpack myvue

npm install vue-router --save-dev

npm i element-ui -S

cnpm install sass-loader node-sass --save-dev

```

```
npm install moduleName // 安装模块到项目目录下
npm install -g moduleName //全局
npm install --save moduleName //save：安装到项目目录并在package的dependencies节点写入依赖 -S为缩写
npm install --save-dev moduleName: devDependencies写入依赖，-D为缩写
```



类似于Maven的脚手架

npm install

npm run dev

模块化开发：

一个js文件可以通过

var xxx = require("./xxx.js");

xxx.xxxx();

#### webpack

通过

```
cnpm webpack install//进行下载
cnpm webpack-cli install
cnpm webpack-dev-server

cnpm webpack -v //测试版本
```

webpack.config.js 配置文件

- entry：入口
- output：输出文件
- module: 模块，处理各种类型文件
- plugins: 插件，热更新，代码重用等
- resolve: 设置路径庄子乡
- watch: 监听，设置文件改动后直接打包，热部署



比如如下简单例子

```javascript
//暴露一个方法出来
exports.sayhi = function () {
    document.write("<h1>Hello~ LGD </h1>")
}
exports.sayhi2 = function () {
    document.write("<h1>Hello~ LGD </h1>")
}
exports.sayhi3 = function () {
    document.write("<h1>Hello~ LGD </h1>")
}
exports.sayhi4 = function () {
    document.write("<h1>Hello~ LGD </h1>")
}
//main.js
var hello = require("./hello");
hello.sayhi();
hello.sayhi2();
hello.sayhi3();
hello.sayhi4();
//webpack.config.js：配置
module.exports = {
    entry: {
        app: './modules/main.js'
    },
    output: {
        filename: './output/bundle.js'
    }
}
```

输出效果

![image-20220326105756387](C:\Users\L\Desktop\文档\photo\image-20220326105756387-16511124826842.png)

### vue-router路由

Vue专注于视图

```
cnpm install vue-router@x.x.x --save-dev
```

类似于打成jar包？

```vue
import mycontext from './components/context.vue'
//我简单导入一个我写的组件,然后使用直接在别人的
<template>
	<mycontext/>
</template>
//即可使用
至于创建，只需要用create(xxx).mount('#xxx')
//不错，我好像明白了，还是HbuildX行！
```

路由用于跳转页面

写好一个router的配置

```javascript
//导入路由方法
import { createRouter, createWebHistory } from 'vue-router'
//导入组件
// 1. 定义路由组件.
// 也可以从其他文件导入
const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }
//路由规则
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About }
]
//路由创建
const router = createRouter({
  history: createWebHistory(),
  routes
})
export default router

//main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App).use(router).mount('#app')

//使用
<div class="nav">
    <!--使用 router-link 组件进行导航 -->
    <!--通过传递 `to` 来指定链接 -->
    <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
    <router-link to="/">首页</router-link> |
    <router-link to="/about">关于</router-link>
</div>
    <hr width="100%">
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
<router-view></router-view>
```

### 整合ElementUI

```
vue init webpack myvue

npm install vue-router --save-dev

npm i element-ui -S

cnpm install sass-loader node-sass --save-dev
```

简单使用//全局(Vue2)

https://element.eleme.cn/#/zh-CN

```
import 'element-ui/lib/theme-chalk/index.css';
Vue.use(Element, { size: 'small', zIndex: 3000 });
```

```javascript
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <fadetest/>
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import fadetest from './components/fadetest.vue'

export default {
  name: 'App',
  components: {
    HelloWorld,
    fadetest
  }
}
</script>
//直接嫖人家组件用，舒舒服服
```

这版本，太痛苦了，换jvm 😡
