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

{% fi http://p33v4b0bc.bkt.clouddn.com/promise.png, Promise, Promise %}

前端近年的兴起，有大部分是因为 `NodeJS` 的诞生，而 `NodeJS` 是个适用于 **异步IO** 密集型的语言，一些基于 `NodeJS` 的框架，比如 *KOA2、Adonis* 就有大量的 `async` 和 `await` 语法，`async`的函数的返回值就是 `Promise` 对象，我们可以用 `async` 和 `await` 语法，写出优雅的异步代码，来替换难看且难维护的回调函数。

## Promise 概念
`Promise`是一种对异步操作的封装，主流的规范是Promise/A+。
`Promise`可以使得异步代码层次清晰，便于理解，且更加容易维护。
`Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由 JavaScript 引擎提供，不用自己部署。
生成实例时回执信作为参数的函数；<Br/> 
`resolve`函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 fulfilled），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
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
  let callback = null;
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

### 改进1：延时resolve，修改 callback 为异步

**这里就遇到一个问题： **  

** 目前的Promise有一个bug，假如fn中所包含的是同步代码，则resolve会立即执行，`callback` 还是 `null` ，我们的代码是同步的，而不是异步的。</br> 如是，想办法解决掉这个问题，就是利用 `setTimeout` , 把 `callback` 加入异步队列** 

代码如下 👇

```javascript
function Promise(fn) {
  let callback = null;
  //实现 then 方法 , 先一步一步来，实现传一个参数 -- resolve
  this.then = function(cb) {
    callback = cb;
  };

  //实现 resolve , value:异步操作的最终值
  function resolve(value) {
    // 用 setTimeout 把 callback 加入到异步队列，这样就会，先执行 then() 方法
    setTimeout(function(){
      callback(value);
    },0)
  }
  //执行 function 参数
  fn(resolve);
}
```

### 改进2：注册多个回调函数，并实现then的链式调用

```javascript
function Promise(fn) {
  let value = null
  let callbackList = [];
  this.then = function(cb) {
    callbackList.push(cb);
    // 实现链式调用
    return this
  };

  function resolve(newValue) {
    value = newValue
    setTimeout(function(){
       // 遍历callbackList数组依次执行
       callbackList.forEach((callback)=>{
         callback(value)
       })
    },0)
  }
  fn(resolve);
}
```
### 改进3：引入状态

```javascript
function Promise(fn) {
  let state='pending'
  let value = null
  let callbackList = [];
  this.then = function(cb) {
    if(state=='pending'){
      // pending加入队列
      callbackList.push(cb);
      return this
    }
    if(state=='fulfilled'){
      // fulfilled立即执行
      cb(value)
      return this
    }
  };

  function resolve(newValue) {
    value = newValue
    setTimeout(function(){
       callbackList.forEach((callback)=>{
         callback(value)
       })
    },0)
  }
  fn(resolve);
}
```

**手动实现一个Promise：**
```javascript
class Promise(){
  construtor(fn){
       // 执行队列
       this._wathcList=[]
       // 成功
       this._success_res=null
       // 失败
       this._error_res=null
       this._status="success"
       fn((...args))=>{
          // 保存成功数据
          this._success_res=args
          this._status='success'
          // 若为异步则回头执行then成功方法
          this._watchList.forEach(element => {
              element.fn1(...args);
          });
       },(...args)=>{
          // 保存失败数据
          this._error_res=args
          this._status='error'
          // 若为异步则回头执行then成功方法
          this._watchList.forEach(element => {
              element.fn2(...args);
          });
       }
  }
  // then 函数
  then(fn1, fn2) {
      if (this._status === "success") {
          fn1(...this._success_res);
      } else if (this._status === "error") {
          fn2(...this._error_res);
      } else {
          this._watchList.push({
              fn1,
              fn2
          })
      }
  }
}
```
**实现Promise.all**
```javascript
Promise1.all = function(arr) {
    // 存放结果集
    let result = [];
    return Promise1(function(resolve, reject) {
        let i = 0;
        // 进行迭代执行
        function next() {
            arr[i].then(function(res) {
                // 存放每个方法的返回值
                result.push(res);
                i++;
                // 若全部执行完
                if (i === result.length) {
                    // 执行then回调
                    resolve(result);
                } else {
                    // 继续迭代
                    next();
                }
            }, reject)
        }
    })
}
```
### 嵌套使用
Promise可以嵌套使用，这样可以是多个任务有条不紊地进行，假设p1是一个Promise对象而p2、p3都是能够产生Promise对象的方法(如果直接new那么Promise将会被直接执行)，那么你可以这样写，使得他们按照顺序执行，并且可以一次性处理他们产生的错误。

```javascript
let p1 = new Promise((resolve, reject) => {
    console.log('p1');
    setTimeout(() => {
        resolve('p2');
    }, 1000)
});

let p2 = (result) => new Promise((resolve, reject) => {
    console.log(result);
    setTimeout(() => {
        resolve('p3');
    }, 2000);
});

let p3 = (result) => new Promise((resolve, reject) => {
    console.log(result);
    setTimeout(() => {
        resolve('over');
    }, 3000);
});

p1
    .then(p2)
    .then(p3)
    .then((result) => {
        console.log(result);
    })
    .catch((error) => {
        console.log(error);
    });
```

## Promise使用注意点
1. 一般来说，调用`resolve`或`reject`以后，`Promise`的使命就完成了，后继操作应该放到`then`方法里面，而不应该直接写在`resolve`或`reject`的后面。所以，最好在它们前面加上`return`语句，这样就不会有意外。

2. `Promise`实例具有`then`方法，也就是说，`then`方法是定义在原型对象`Promise.prototype`上的。它的作用是为`Promise`实例添加状态改变时的回调函数。前面说过，`then`方法的第一个参数是`resolved`状态的回调函数，第二个参数（可选）是`rejected`状态的回调函数。`then`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。因此可以采用链式写法，即then方法后面再调用另一个`then`方法。

3. `Promise.prototype.catch`方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。`getJSON`方法返回一个`Promise`对象，如果该对象状态变为`resolved`，则会调用`then`方法指定的回调函数；如果异步操作抛出错误，状态就会变为`rejected`，就会调用`catch`方法指定的回调函数，处理这个错误。另外，then方法指定的回调函数，如果运行中抛出错误，也会被`catch`方法捕获。

4. 一般来说，不要在then方法里面定义`reject`状态的回调函数（即then的第二个参数），总是使用`catch`方法。

5. 跟传统的`try/catch`代码块不同的是，如果没有使用`catch`方法指定错误处理的回调函数，`Promise`对象抛出的错误不会传递到外层代码，即不会有任何反应，`Promise`会吃掉错误。
