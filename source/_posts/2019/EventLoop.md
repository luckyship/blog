---
title: EventLoop
tags:
  - 深入理解
copyright: true
comments: true
date: 2019-06-16 12:38:46
categories: JS
photos:
top: 240
---

## 描述事件队列的过程？

- js是单线程的，会出现阻塞问题，因此有了任务队列的出现
- 主线程同步执行任务，异步的工作一般会交给其他线程完成，然后回调函数会放到事件队列中
- 等候主线程执行完毕后再执行事件队列中的操作

![event-queue](http://cdn.mydearest.cn/blog/images/event-queue.png)

> 同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；

> 异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行

![task-queue](http://cdn.mydearest.cn/blog/images/task-queue.png)
任务队列中存着的是异步任务，这些异步任务一定要等到执行栈清空后才会执行。
异步任务，会先到事件列表中注册函数。如果事件列表中的事件触发了，会将这个函数移入到任务队列中（DOM操作对应DOM事件，资源加载操作对应加载事件，定时器操作可以看做对应一个“时间到了”的事件）

---
<!--more-->

## 什么是宏任务什么是微任务？
script(宏任务) - 清空微任务队列 - 执行一个宏任务 - 清空微任务队列 - 执行一个宏任务， 如此往复。
1.先执行script里的同步代码（此时是宏任务）。碰到异步任务，放到任务队列。
2.查找任务队列有没有微任务，有就把此时的微任务全部按顺序执行 （这就是为什么promise会比setTimeout先执行，因为先执行的宏任务是同步代码，setTimeout被放进任务队列了，setTimeout又是宏任务，在它之前先得执行微任务(就比如promise)）。
3.执行一个宏任务（先进到队列中的那个宏任务），再把这次宏任务里的宏任务和微任务放到任务队列。
4.一直重复2、3步骤
- 异步队列中有宏任务微任务之分
- 一次事件循环：先运行宏任务队列中的一个，然后运行微任务队列中的所有任务。接着开始下一次循环

![macro-micro](http://cdn.mydearest.cn/blog/images/macro-micro.png)

## 哪些是宏任务哪些是微任务？

### 宏任务(优先级由高到低)
- 主线程同步代码
- setTimeout
- setImmediate node无
- setInterval
- requestAnimationFrame node无
- I/O
- UI rendering

### 微任务(优先级由高到低)
- process.nextTick 浏览器无
- Promise.then
- Object.observe(该方法已废弃)
- MutationObserver node无

### 微任务的意义
减少更新时的渲染次数
因为根据HTML标准，会在宏任务执行结束之后，在下一个宏任务开始执行之前，UI都会重新渲染。如果在microtask中就完成数据更新，当 macro-task结束就可以得到最新的UI了。如果新建一个 macro-task来做数据更新的话，那么渲染会执行两次。

### 事件循环
![eventloop](http://cdn.mydearest.cn/blog/images/eventloop.png)

1. 整体的script(作为第一个宏任务)开始执行的时候，会把所有代码分为两部分：“同步任务”、“异步任务”；
2. 同步任务会直接进入主线程依次执行；
4. 异步任务会再分为宏任务和微任务；
5. 宏任务进入到Event Table中，并在里面注册回调函数，每当指定的事件完成时，Event Table会将这个函数移到Event Queue中；
6. 微任务也会进入到另一个Event Table中，并在里面注册回调函数，每当指定的事件完成时，Event Table会将这个函数移到Event Queue中；
7. 当主线程内的任务执行完毕，主线程为空时，会检查微任务的Event Queue，如果有任务，就全部执行，如果没有就执行下一个宏任务；
上述过程会不断重复，这就是Event Loop事件循环；

![summary-loop](http://cdn.mydearest.cn/blog/images/summary-loop.png)

**精炼**事件循环是指: 执行一个宏任务，然后执行清空微任务列表，循环再执行宏任务，再清微任务列表。

### Node 的 Event Loop: 6个阶段
- timer 阶段: 执行到期的setTimeout / setInterval队列回调
- I/O 阶段: 执行上轮循环残留的callback
- idle, prepare
- poll: 等待回调
  - 执行回调
  - 执行定时器
    - 如有到期的setTimeout / setInterval， 则返回 timer 阶段
    - 如有setImmediate，则前往 check 阶段
- check
  - 执行setImmediate
- close callbacks

### 练习题
```js
console.log(1)
setTimeout(()=>{
    console.log(2)
}, 0)
var promise = new Promise(function(resolve, reject) {
    console.log(3)
    resolve();
})
promise.then(function(){
    console.log(4)
})
console.log(5)
// 13542

console.log(1)
setTimeout(()=>{
    console.log(2)
},0)

var intervalId = setInterval(function(){
    console.log(3)
}, 0)
setTimeout(function(){
    console.log(10)
    new Promise(function(resolve){
        console.log(11)
        resolve()
    }).then(()=>{
        console.log(12)
    }).then(()=>{
        console.log(13)
        clearInterval(intervalId)
    })
}, 0)
Promise.resolve().then(()=>{
    console.log(7)
}).then(()=>{
    console.log(8)
})
console.log(9)
// 1 9 7 8 2 3 10 11 12 13

setTimeout(()=>{
    console.log(1)
}, 0);

new Promise((resolve, reject)=>{
    console.log(2);
    resolve();
}).then(()=>{
    console.log(3);
}).then(()=>{
    console.log(4);
});

process.nextTick(()=>{
    console.log(5);
});

console.log(6);
// 2 6 5 3 4 1
```

```js
console.log(1);

setTimeout(()=>{
    console.log(2);   
    new Promise((resolve,reject)=>{
    console.log(3);
    resolve()
}).then(res=>{
    console.log(4); 
})
})

new Promise((resolve,reject)=>{
    resolve()
}).then(res=>{
    console.log(5); 
}).then(res=>{
    console.log(6);
    
})

new Promise((resolve,reject)=>{
    console.log(7);
    resolve()
}).then(res=>{
    console.log(8); 
}).then(res=>{
    console.log(9);
    
})

setTimeout(()=>{
    console.log(10);   
    new Promise((resolve,reject)=>{
    console.log(11);
    resolve()
}).then(res=>{
    console.log(12); 
})
})

console.log(13);
// 1 7 13 5 8 6 9 2 3 4 10 11 12
```