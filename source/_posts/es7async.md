---
title: 使用 Async/Await 让你的代码更简洁
tags:
  - es7
  - Async/Await
copyright: true
comments: true
date: 2018-06-11 01:30:51
categories: JS
top: 107
photos:
---

Async/Await是一种允许我们像构建没有回调函数的普通函数一样构建Promise的新语法。以往的异步方法无外乎回调函数和`Promise`。但是Async/Await建立于[Promise](https://mydearest.cn/createPromise.html)之上。

---
<!-- more -->

## Async/Await语法
### async关键字(放置在一个函数前面)。

```javascript
async function f() {
    return 1
    // return Promise.resolve(1)
}
f().then(value) // 1
```

async修饰过的函数总是返回一个promise，如果代码中有return <非promise>语句，JavaScript会自动把返回的这个value值包装成promise的resolved值。
### await关键字(只能在async函数内部使用)
关键词await可以让JavaScript进行等待，直到一个promise执行并返回它的结果，JavaScript才会继续往下执行。

```javascript
function timeout(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms);
    });
}

async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
}

asyncPrint('hello world', 50);
```

### 错误处理(使用try-catch捕获)
```javascript
async function f() {
    try {
        let response = await fetch('http://no-such-url')
    } catch (err) {
        alet(err) // TypeError: failed to fetch 也能捕获多行语句 类似链式Promise最后的单个catch函数
    }
}
f()
```

如果我们不使用try-catch，然后async函数f()的调用产生的promise变成reject状态的话，我们可以添加.catch去处理它：

```javascript
async function f() {
    let response = await fetch('http://no-such-url')
}
// f()变成了一个rejected的promise
f().catch(alert) // TypeError: failed to fetch
```

### 结合Promise.all
async/await能够与Promise.all友好的协作，当我们需要等待多个promise时，我们可以将他们包装在Promise.all中然后使用await：

```javascript
// 直到数组全部返回结果
let results = await Promise.all([
   fetch(url1),
   fetch(url2),
   ...
])
```
## 总结

随着单页JavaScript web程序的兴起和对NodeJS的广泛采用，如何优雅的处理并发对于JavaScript开发人员来说比任何以往的时候都显得更为重要。Async/Await缓解了许多因为控制流问题而导致bug遍地的这个困扰着JavaScript代码库数十年的问题，并且几乎可以保证让任何异步代码块变的更精炼，更简单，更自信。