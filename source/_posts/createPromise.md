---
title: Promise诞生记
tags:
  - es6
  - Promise
copyright: true
comments: true
date: 2018-06-05 01:34:09
categories: JS
top: 107
photos:
---

{% fi https://cdn.lishaoy.net/createPromise/promise.png, Promise, Promise %}

前端近年的兴起，有大部分是因为 `NodeJS` 的诞生，而 `NodeJS` 是个适用于 **异步IO** 密集型的语言，一些基于 `NodeJS` 的框架，比喻 *KOA2、Adonis* 就有大量的 `async` 和 `await` 语法，`async`的函数的返回值就是 `Promise` 对象，我们可以用 `async` 和 `await` 语法，写出优雅的异步代码，来替换难看且难维护的回调函数。

## Promise 概念

`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。
生成实例时回执信作为参数的函数；<Br/> 
`resolve`函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
`reject`函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

---
<!-- more -->

```javascript
// 执行顺序
let promise = new Promise(function(resolve, reject) {
  console.log('Promise'); // 新建后立即执行
  resolve();
});
promise.then(function() {
  console.log('resolved.'); // 同步任务执行完成后才会执行
});
console.log('Hi!');
// Promise
// Hi!
// resolved
```
接下来我们就用`Promise`结合ajax来使用
```
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send(params);
  });
  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

这里我们会渐进式的来创建一个 `Promise` 的实现，如果，你还不了解 `Promise` ，赶快移步 [Promise](http://es6.ruanyifeng.com/#docs/promise) 了解学习，当然这个实现会符合 [Promise/A+](https://promisesaplus.com) 规范，`JavaScript` 中有很多第三方的 `Promise` 库，[bluebird](http://bluebirdjs.com/docs/getting-started.html) 就是一个第三方 `Promise` 类库，相比其它第三方类库或标准对象来说，其有以下优点：功能更齐全而不臃肿、浏览器兼容性更好,大家可以了解下。

---

废话不多说，直接开干。。。 😠

## 定义 Promise 类型

一个简单 `Promise` 语法，如下

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... doSomething

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure 第二个函数可选
});
```

### 实现 resolve 和 then

首先我们以上 👆 的语法，自己定义一个 `Promise` 实例

```javascript
function Promise(fn) {
  var callback = null;
  //实现 then 方法 , 先一步一步来，实现传一个参数 -- resolve
  this.then = function(cb) {
    callback = cb;
  };

  //实现 resolve , value:异步操作的最终值
  function resolve(value) {
    callback(value);
  }
  //执行 function 参数
  fn(resolve);
}
```

一个简单的实例写好了，然后，来用一下，看看 👀 结果如何

```javascript
const p = new Promise(function(resolve){
  resolve(66);
});

p.then(function(value){
  console.log(value);
});
```


{% note info %}

执行结果是：`callback is not a function`

{% endnote %}

### 修改 callback 为异步

**这里就遇到一个问题： **  

** 发现 `resolve()` 在 `then()` 之前调用，在 `resolve()` 被调用的时候， `callback` 还是 `null` ，我们的代码是同步的，而不是异步的。</br> 如是，想办法解决掉这个问题，就是利用 `setTimeout` , 把 `callback` 加入异步队列** 

代码如下 👇

```javascript
function Promise(fn) {
  var callback = null;
  //实现 then 方法 , 先一步一步来，实现传一个参数 -- resolve
  this.then = function(cb) {
    callback = cb;
  };

  //实现 resolve , value:异步操作的最终值
  function resolve(value) {
    // 用 setTimeout 把 callback 加入到异步队列，这样就会，先执行 then() 方法
    setTimeout(function(){
      callback(value);
    },1)
  }
  //执行 function 参数
  fn(resolve);
}
```

然后，再来用一下,看看 👀 结果如何

```javascript
const p = new Promise(function(resolve){
  resolve(66);
});

p.then(function(value){
  console.log(value);
});
```

{% note info %}

执行结果是：`66`

{% endnote %}

## Promise使用注意点
1. 一般来说，调用`resolve`或`reject`以后，`Promise`的使命就完成了，后继操作应该放到`then`方法里面，而不应该直接写在`resolve`或`reject`的后面。所以，最好在它们前面加上`return`语句，这样就不会有意外。

2. `Promise`实例具有`then`方法，也就是说，`then`方法是定义在原型对象`Promise.prototype`上的。它的作用是为`Promise`实例添加状态改变时的回调函数。前面说过，`then`方法的第一个参数是`resolved`状态的回调函数，第二个参数（可选）是`rejected`状态的回调函数。`then`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。因此可以采用链式写法，即then方法后面再调用另一个`then`方法。

3. `Promise.prototype.catch`方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。`getJSON`方法返回一个`Promise`对象，如果该对象状态变为`resolved`，则会调用`then`方法指定的回调函数；如果异步操作抛出错误，状态就会变为`rejected`，就会调用`catch`方法指定的回调函数，处理这个错误。另外，then方法指定的回调函数，如果运行中抛出错误，也会被`catch`方法捕获。

4. 一般来说，不要在then方法里面定义`reject`状态的回调函数（即then的第二个参数），总是使用`catch`方法。

5. 跟传统的`try/catch`代码块不同的是，如果没有使用`catch`方法指定错误处理的回调函数，`Promise`对象抛出的错误不会传递到外层代码，即不会有任何反应，`Promise`会吃掉错误。
