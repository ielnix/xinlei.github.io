# node

##初识node.js

###node.js的官方定义

Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engeine.

node.js是javascript运行的一个环境，基于Chrome浏览器的v8引擎

Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.

node.js的特点 事件驱动 无阻塞的io模型 使用node 轻量 高效

Node.js' package ecosystem,npm,is the largest ecosysetem of open sourse libraries in the world.

包生态系统--->npm node package manager 是一个客户端工具 世界上最大的包管理系统

###浏览器与node.js运行js的模型

1. 浏览器与js的关系  被动
2. node.js与js的关系 主动
3. node.js如何运行文件

node + 文件路径

### js的引擎

特点

1. 转化 js引擎将js代码转化成电脑能读取的  （字节码、机器码）
2. 可移植性

注：node没有兼容性问题了，node不是浏览器

###node的应用场景

1. 读写文件
2. 写网站后端（服务器）

###npm初识

###总结

注：node是后台的技术，是门技术不是语言，使用js干后台干的事情

###Global

global和window很像，都是全局对象

node运行js的时候一个js里面的代码的作用域就是这个js的

###断言

用来测试的，就是假定一个条件，如果条件成立输出什么内容。assert用于精准的定位错误的

```js
var a=10;
console.assert(a==11,'报错了');
```

用来检测一下代码的运行时间的

```js
console.time('test');
for(var i=0;i<100000000;i++){
}
console.timeEnd('test');
```

##node.js的模块化

1. 一个js文件就是一个模块，模块里面的代码的作用域就在这个文件中

    优点 
    - 一次编写多次使用
    - 便于维护

2. node的模块化符合commonjs规范

###module

模块对象，当运行js的时候，就有一个js，node就会为其创建一个module去存储这个js的相关信息

###require

第一次加载js（module）的时候，会执行一遍js里面的代码
第二次加载js的时候，不执行js代码的，直接去内存中找到第一次加载js对应的module对象,从中找到module.exports

###exports

exports是module.exports的别名，外号、花名

###模块化总结

1. 模块化的代码具有封装性，对代码进行包装，我想让你用什么接口，就使用什么
2. 有封装就有打破封装，需要给别人开接口 module.exports exports
3. module.exports直接赋值
4. exports当一个js中有跟多的属性跟方法的时候，就是用exports向外曝露
5. 一个js文件就是一个模块
6. 一个模块的信息被保存在module对象里面，每一个模块都一个module与之对应，module对象产生了以后，第二次加载就不会再去执行，而是去内存中的module对象中找module.exports

###3m安装法

1. npm
    - 下载jquery代码
    - 下载工具
2. nrm
    - npm 数据源管理工具
    - 安装 npm install -g nrm 加上-g是全局安装 不加-g是下载到当前目录下
    - nrm -h 查看帮助文档
    - nrm ls 查看npm的数据源有哪些，标星号的表示正在使用的
    - nrm use 数据源的名字 nrm use taobao
3. nvm
    - node version manager
    - nvm -h
    - nvm list
    - nvm install 版本号 下载
    - nvm use 版本号  使用

###REPL （Read Eval Print Loop）交互式运行环境

测试新的语法  两下crtl+c退出

##模块系统

###__dirname、__filename

>当前__dirname所在文件的路径
当前__filename所在的文件的全路径+当前的文件名

###简单的读取文件

fs.readFile();

```js
//引入fs模块，直接用名字的方式引入
var fs=require('fs');
//读文件的方法
//第一个参数是路径
//第二个参数使用utf8的编码格式
//第三个参数是回调函数，回调函数中有两个参数
//1. err 错误对象
//2. data对象
fs.reqdFile('01__dirname&__filename.js','utf8',function(err,data){
    if(err) throw err;
    console.log(data);
});
```

报错信息：no such file or directory  路径错误  返回data为undefined

读取成功  err对象为null 

##模块的种类

在Node.js中,模块分为两类

第一类,核心模块(原生模块),node自带,用名称直接可以加载
- 核心模块
    + fs file system 文件系统
    + http 制作网站
    + os 获取电脑信息
    + path 路径拼接用的
    + query
    + url
- 核心模块存在的意义
    我们需要node给我们提供一些方法，让我们更方便的去处理电脑的硬件

第二类，文件模块，用路径加载，有一种特殊的文件模块--->包，可以用名字

###require原理

```js
//模拟require
function myRequire(path){
    var fs=require('fs');
    //构造函数的形式去定义module对象
    function Module() {
        this.exports = {}
    }
    var source=fs.readFileSync(path,'utf8');
    //对代码做头尾包装,包装成可以执行的匿名函数
    var package = "(function(module,exports){  " + source + "returnmodule.exports })";
    //执行代码，让字符串变成真正的函数，再付给一个变量
    var packfn = eval(package);
    //new module对象
    var module = new Module();
    //调用packfn这个方法，obj就是module.exports
    var obj = packfn(module, module.exports);

    return obj;
}
```

###require参数解析

- 标识符中可以不包含扩展名
    + Node会按照`.js、.node、.json`的次序补足扩展名，一次尝试
- 注意点，.js可以不写,.json要写,为了节省系统资源

##包

还有一种特殊的文件模块，其实就是包
- 问题：你开发的一个功能模块，还是以文件的形式散列的，你给别人使用的时候很麻烦
- 包的作用：在模块的基础上进一步阻止JavaScript代码

###包的组成
- 包的结构
    包就是一个文件夹，文件夹的名字最好是包名
    1. 入口文件 index.js或app.js 写大的应用例如网站的时候，叫app.js
    2. 包说明文件，不只是给人看的~~~---->package.json
    3. lib文件夹 存放js文件的
    4. bin文件夹 二进制文件或者脚本文件
    5. test 测试文件夹
    6. docs 文档放这里
    7. node_modules 如果我需要引入别的包
- 包说明文件package.json
    + dependencies
        - 包名:"版本号"


###语义化版本号

1.2.3这种版本号的定义叫语义化版本号
- 3表示补丁包版本更新
- 2表示向下兼容性版本更新
- 1表示重大版本更新

##网络编程

- bs browser server 浏览器和服务器
- cs client server 客户端和服务端

###什么是服务器

其实就是一台电脑，是一台性能比较好的大电脑，他需要支持高扩展性，提供服务的。

###什么是web服务器

是一种应用服务器，提供了web服务，对内提供web应用程序的运行环境

###node没有web服务器（服务器容器）

###ip&端口号

- ip---->唯一标示一台上网的设备
- 端口---->唯一标示设备上的一个应用程序

###http协议

##http模块

```js
//引入http模块
var http=require('http');
//创建一个http服务器的实例
var server=http.createServer();
//要让服务器接受http请求
//回调函数里面有两个参数
//request对象存储浏览器发送过来的http请求的内容
//response对象封装一个返回的报文
var num=0;
server.on('request',function(request，response){
    num+=1;
    //一旦执行了end方法，响应报文就返回了，整个http请求到流程就结束了
    response.end('这是第'+num+'次请求~');
    //end方法后面不可以再对响应报文进行操作
    //但是可以继续执行后面跟操作报文无关的代码
    console.log(123);
});
//node默认用3000端口号
server.listen(3000);
```

```js
var http=require('http');
var server=http.createServer();
server.on('request',(req,res)=>{
    //req.headers 请求头
    //req.method 请求方法
    //req.url 请求路径
    //req.httpVersion 请求HTTP协议版本
    res.write('可以一直写，一直到end，报文体结束');
    //res.end('结束');
    res.end('<h1>可以扔页面</h1>')
});
server.listen(3000);
```

###response

- res.writeHead(200,{'Content-Type':'text/html;charset=utf-8'})
    + 如果需要设置相应头，需要优先于相应体的设置
- res.write()
- res.end()

##querystring 模块

querystring.parse() 转化路径后面的参数  即字符串--->对象

##url 模块

url.parse() 处理路径的方法

##异步i/o与事件驱动

###什么是进程
```
进程是为运行当中的应用程序提供运行环境的
一个运行当中的应用程序就会有一个进程与之相对应
```

###什么是线程
```
线程是用来运行应用程序中的代码的
一个线程在一个时间只能做一件事情
多线程，调度起来很麻烦
node是单线程执行，用异步替代了多线程
```

###同步、异步有什么不同
```
同步是按顺序执行，会阻塞后面代码的执行
异步是不需要等前面的代码再慢，也不会阻塞后面代码的执行
先执行的永远是同步代码
异步函数谁先调用callback函数是不一定
```

##异步io模型

###异步非i/o操作和异步i/o操作

- 异步非io setTimeout setInterval
- 异步IO操作 操作文件 网络操作 fs

###异步和多线程的比较

>node的异步就是通过异步处理的方式替你去完成多线程的编写
多线程编程更加灵活
异步使用起来更加简单
node擅长io操作，不擅长cpu密集型--->大量的计算

##express

###什么是框架？什么是类库？

类库是你调用他的方法。

框架提供了一套规范，你按照这套规范写，框架再来运行你写的代码

###generator 脚手架工具

帮助我们快速构架网站
```
npm install -g express-generator
```

###脚手架构架项目的目录结构

- app.js
- package.json
- /bin
- /node_modules
- /public 静态文件
- /routes 路由文件
- /views 模板文件

###path模块

```js
//引入path核心模块
var path=require('path');
var fs=require('fs');
//path.join 拼接多个字符串，输出正确的路径
fs.readFile(path.join(__dirname,'./foo.text'),'utf8',(err,data)=>{
    console.log(data);
});
//path.basename  取文件名
//path.dirname   取路径中文件夹路径部分
//path.extname   获取文件的后缀名
```

###路由

通过一种并联的结构，让你的请求找到唯一对应的处理

```js
var express = require('express');
var path=require('path');
var app=express();
//处理静态文件的中间件
app.use(express.static(path.join(__dirname,'public')));
//方法有两个参数
//第一个参数请求的路径
//第二个参数callback
app.get('/getdata',(req,res)=>{
    res.end('123');
});
app.post('/getdata',(req,res)=>{
    res.end('456');
});
//当路径中有一个字符不记得了，在这个符号后面使用“?”
//当路径中有n个字符不记得了，用()将这些字符包裹起来，后面使用“?”
app.get('/apple(new)?',(req,res)=>{
    res.end('apples');
});
//当不记得一个字符出现了几次
app.get('/go+gle',(req,res)=>{
    res.end('goole');
});
//当请求中不记得中间某段字符，用*匹配
app.get('/b*u',(req,res)=>{
    res.end('baidu');
});
//匹配正则
app.get(\.*fly$\,(req,res)=>{
    res.end('fly');
});
app.listen(3000);
```

###中间件

>通过串联的形式，把一堆过滤器串联起来
通过app.use使用的都是中间件

###中间件种类

- 内置中间件
- 第三方中间件
- 错误处理中间件
- 应用级中间件
- 路由级中间件

###使用中间件

- 挂载中间件
    app.use([path],function)
- 回调函数中的参数
    req,res,next
    next是一个方法，被调用后，继续走下一个中间件

```js
var express=require('express');
var app=express();
//有两个参数
//第一个参数路径 可选
//第二个callback  回调函数里面有三个参数
app.use(function(res,rep,next){
    console.log('处理1');
    next();
});
app.listen(3000);
```



