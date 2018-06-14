---
title: JavaScript深入之执行上下文
tags:
  - 上下文
copyright: true
comments: true
date: 2018-06-13 17:06:49
categories: JS
top: 107
photos:
---

## 变量/函数提升

```javascript
if(!"a" in window){
    var a=1;
}
console.log(a) // undefined
```

```javascript
function foo() {
    console.log('foo1');
}

foo();  // foo2

function foo() {
    console.log('foo2');
}

foo(); // foo2
```

JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。插槽slot
可执行代码的类型：全局代码、函数代码、eval代码。
JavaScript 引擎创建了执行上下文栈（Execution context stack，ECS）来管理执行上下文

--- 
<!-- more -->

为了模拟执行上下文栈的行为，让我们定义执行上下文栈是一个数组：

```javascript
ECStack = [];
```

试想当 JavaScript 开始要解释执行代码的时候，最先遇到的就是全局代码，所以初始化的时候首先就会向执行上下文栈压入一个全局执行上下文，我们用 globalContext 表示它，并且只有当整个应用程序结束的时候，ECStack 才会被清空，所以程序结束之前， ECStack 最底部永远有个 globalContext：

```javascript
ECStack = [
    globalContext
];
```

现在 JavaScript 遇到下面的这段代码了：

```javascript
function fun3() {
    console.log('fun3')
}

function fun2() {
    fun3();
}

function fun1() {
    fun2();
}

fun1();
```

当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。知道了这样的工作原理，让我们来看看如何处理上面这段代码：

```javascript
// 伪代码

// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文
ECStack.push(<fun2> functionContext);

// 擦，fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext
```

## 三大属性
- 变量对象
- 作用域链
- this

## 全局对象
{% note info %}

全局对象是预定义的对象，作为 JavaScript 的全局函数和全局属性的占位符。通过使用全局对象，可以访问所有其他所有预定义的对象、函数和属性。

{% endnote %}

{% note info %}

例如，当JavaScript 代码引用 parseInt() 函数时，它引用的是全局对象的 parseInt 属性。全局对象是作用域链的头，还意味着在顶层 JavaScript 代码中声明的所有变量都将成为全局对象的属性。

{% endnote %}

```javascript
// 在浏览器客户端 全局对象就是window对象
// 通过this引用
this === window  // true
this.window === window // true
this instanceof Object // 是通过Object构造函数实例出来的对象
Math.random() 
this.Math.random() // 预定义了一些函数和属性

// window指向自身
var a=1;
a // 1
this.a // 1
window.a // 1 
this.window.a // 1
```

## 函数声明>变量声明

```javascript
console.log(foo); // 打印函数

function foo(){
    console.log("foo");
}

var foo = 1;
```
执行上下文时，首先会处理函数声明，其次会处理变量声明，如果如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。