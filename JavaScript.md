# JavaScript

### 操作BOM对象

JS和浏览器关系：

js诞生是为了让浏览器运行脚本

B/S:B means 浏览器

BOM：浏览器对象模型

内核

- IE
- Chrome Google
- 火狐-FireFox - Linux默认
- Safari

window：

```javascript
window.alert(xxx)
window.innerHeight
window.innerWidth
window.outerWidth
window.Navigator
```

Navigator:封装了浏览器的信息

```javascript
navigator.appName
'Netscape'
navigator.appVersion
'5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36'
navigator.userAgent
'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36'
navigator.platform
'Win32'
```

但一般不会使用这个对象，可以进行人为修改

python爬虫中我记得我好像用过userAgent,以证明是人在访问网站而不是机器

screen：

- screen.width
- screen.height
- ...

location，可以实现重定向,代表当前页面URL的信息

Web里的redirect,forward之类也可以换页面，emmm，过去三个月了

location.reload()

设置新地址

location.assign('http://www.leigd.cn')

document:代表当前的页面信息 html有个DOM文档树

比如

```javascript
document.title = 'lgd'
document.getElementById('id');//通过id获取具体的文档树节点
//能获取就能修改和删除
document.cookie
```

如果document可以直接获取cookie，那么网页就可以恶意劫持你的cookie，获取你的cookie后，就可以直接登录网站了

服务器端通过设置 cookie: httpOnly就好了

history: 浏览器的历史记录

history.back();

history.forward();

history.(goto)具体不记得了前进或者后退都可以用数字代表

//前进，后退

### 操作DOM对象

DOM：文档对象模型

浏览器网页就是一个DOM树形结构

- update
- 遍历dom节点:得到Dom
- delete
- add

要操作一个DOM节点，就必须要先获得这个DOM节点

DOM节点：

id #

class .

获取：

document.getElementByTaName('h1'); // 标签选择器

document.getElementById('p1'); //通过id获取

document.getElementByClassName('h2')//class名获取

这是原生代码，之后就用jQuery();

j三种定义变量的方式

#### const， var， let的区别

1.const定义的变量不可以修改，而且必须初始化。(类似java里static final?)

```
1 const b = 2;//正确
2 // const b;//错误，必须初始化 
3 console.log('函数外const定义b：' + b);//有输出值
4 // b = 5;
5 // console.log('函数外修改const定义b：' + b);//无法输出 
```

2.var定义的变量可以修改，如果不初始化会输出undefined，不会报错。

```
1 var a = 1;
2 // var a;//不会报错
3 console.log('函数外var定义a：' + a);//可以输出a=1
4 function change(){
5 a = 4;
6 console.log('函数内var定义a：' + a);//可以输出a=4
7 } 
8 change();
9 console.log('函数调用后var定义a为函数内部修改值：' + a);//可以输出a=4
```

3.let是块级作用域，函数内部使用let定义后，对函数外部无影响。（在哪个块就是哪个值）

```
1 let c = 3;
2 console.log('函数外let定义c：' + c);//输出c=3
3 function change(){
4 let c = 6;
5 console.log('函数内let定义c：' + c);//输出c=6
6 } 
7 change();
8 console.log('函数调用后let定义c不受函数内部定义影响：' + c);//输出c=3
```

