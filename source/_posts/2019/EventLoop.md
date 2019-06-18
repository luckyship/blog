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

- js是单线程的，会出现阻塞问题，因此有了异步队列的出现
- 主进程同步执行任务，异步操作将添加到异步队列中
- 等候主进程执行完毕后再执行异步队列中的操作

---
<!--more-->

## 什么是宏任务什么是微任务？

- 异步队列中有宏任务微任务之分
- 一次事件循环：先运行宏任务队列中的一个，然后运行微任务队列中的所有任务。接着开始下一次循环

## 哪些是宏任务哪些是微任务？

### 宏任务
- setTimeout
- setImmediate
- setIntarval
- requestAnimationFrame
- I/O
- UI rendering

### 微任务
- process.nextTick
- Promise.then
- Object.observe
- MutationObserver

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
```