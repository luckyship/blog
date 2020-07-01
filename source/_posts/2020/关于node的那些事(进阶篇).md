---
title: 关于node的那些事(进阶篇)
tags:
  - NodeJS
copyright: true
comments: true
date: 2020-07-02 00:31:02
categories: Node
photos:
---

## 👨提问：注册路由时 app.get、app.use、app.all 的区别是什么？

### app.use(path,callback)

> app.use是express用来调用中间件的方法。中间件通常不处理请求和响应，一般只处理输入数据，并将其交给队列中的下一个处理程序，比如下面这个例子app.use
('/user')，那么只要路径以 /user 开始即可匹配，如 /user/tree 就可以匹配。

### app.all()

> app.all 是路由中指代所有的请求方式，用作路由处理，匹配完整路径，在app.use之后可以理解为包含了app.get、app.post等的定义，比如app.all('/
user/tree'),能同时覆盖：get('/user/tree') 、 post('/user/tree')、 put('/user/tree') ,不过相对于app.use()的前缀匹配，它则是匹配具体的路
由。

`all完整匹配，use只匹配前缀`

---
<!--more-->

## express response有哪些常用方法?
express response对象是对Node.js原生对象ServerResponse的扩展，express response常见的有：res.end()、res.send()、res.render()、res.
redirect()，而这几个有什么不同呢？更多请看文档[express Response](https://www.expressjs.com.cn/4x/api.html#res)

### res.end()
结束response - 如果服务端没有数据回传给客户端则可以直接用res.end返回，以此来结束响应过程。

### res.send(body)
如果服务端有数据可以使用res.send,可以忽略res.end，body参数可以是一个Buffer对象，一个String对象或一个Array。

### res.render
res.render用来渲染模板文件，也可以结合模版引擎来使用，下面看个简单的demo (express+ejs模版引擎)

[node-ejs](http://cdn.mydearest.cn/blog/images/node-ejs.png)

- 配置
```js
app.set('views', path.join(__dirname, 'views')); // views：模版文件存放的位置，默认是在项目根目录下
app.set('view engine', 'ejs'); // view engine：使用什么模版引擎
```

其次是根据使用的模版引擎语法编写模版，最后通过res.render(view,locals, callback)导出，具体使用参数

```
view：模板的路径
locals：渲染模板时传进去的本地变量
callback：如果定义了回调函数，则当渲染工作完成时才被调用，返回渲染好的字符串（正确）或者错误信息 ❌
```

### res.redirect
重定义到path所指定的URL，同时也可以重定向时定义好HTTP状态码（默认为302）
```js
res.redirect('http://baidu.com');
res.redirect(301, 'http://baidu.com');
```

## node如何利用多核CPU以及创建集群?
众所周知，nodejs是基于chrome浏览器的V8引擎构建的，一个nodejs进程只能使用一个CPU(一个CPU运行一个node实例)，举个例子：我们现在有一台8核的服务器，
那么如果不利用多核CPU，是很一种浪费资源的行为，这个时候可以通过启动多个进程来利用多核CPU。

Node.js给我们提供了cluster模块，用于nodejs多核处理，同时可以通过它来搭建一个用于负载均衡的node服务集群。
```js
const cluster = require('cluster')
const os = require('os')
const express = require('express')
const path = require('path')
const ejs = require('ejs')
const app = express()

const numCPUs = os.cpus().length

if (cluster.isMaster) {
    for (let i =0 ;i<numCPUs.length;++i) {
        cluster.fork()
    }
    cluster.on('exit', (worker, code, signal) => {
        console.log(`worker ${worker.process.pid} died`)
    })
} else {
    app.listen(3000, function(){
        console.log(`worker ${worker.process.pid} started`)
    })
}
// 一个端口被多个进程监听
```

```bash
lsof -i -P -n | grep 3000
ps -ef | grep pid
```

Master 通过 cluster.fork() 这个方法创建的，本质上还是使用的 child_process.fork()

## node是怎样支持https?
https实现，离不开证书，通过openssl生成公钥私钥（不做详细介绍），然后基于 express的 https模块 实现，设置options配置, options有两个选项，一个是
证书本体，一个是密码。

## node和客户端怎么解决跨域的问题？
app.all('*',(req,res,next)=>{}) 效果相当于app.use((req,res,next)=>{}), 这也是app.all的一个比较常见的应用，就是用来处理跨域请求。
```js
app.all('*', function (req, res, next) {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'X-Requested-With');
    res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
    res.header('Content-Type', 'application/json;charset=utf-8');
    if (req.method == 'OPTIONS') {
        res.send(200);
    } else {
        next();
    }
});
```

- cors模块

## 两个node程序之间怎样交互?
通过fork，原理是子程序用process.on来监听父程序的消息，用 process.send给子程序发消息，父程序里用child.on,child.send进行交互，来实现父进程和子
进程互相发送消息。
