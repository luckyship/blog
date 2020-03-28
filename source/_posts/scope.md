---
title: JavaScript深入之作用域
tags:
  - 作用域
copyright: true
comments: true
date: 2018-06-12 21:43:41
categories: JS
top: 107
photos:
---

## 作用域

作用域指的是程序源代码中定义变量的区域。通常来说，一段程序代码中所用到的名字并不总是有效/可用的，而限定这个名字的可用性的代码范围就是这个名字的作用域。

作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。

JavaScript 采用词法作用域(lexical scoping)，也就是静态作用域。它的作用域是指在词法分析阶段就确定了，不会改变。而与词法作用域相对的是动态作用域，

函数的作用域是在函数调用的时候才决定的。

**函数的作用域在函数定义的时候就决定了，函数的作用域基于函数创建的位置。**

---
<!-- more -->

```javascript
var value=1
function print(){
  console.log(value)
}
function foo(){
  var value=2
  print()
}
foo() // value 1 如果是动态作用域则是2
```
我们再来看两道题：

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```
两道题的执行结果都是 "local scope"

JavaScript 函数的执行用到了作用域链，这个作用域链是在函数定义的时候创建的。嵌套的函数 f() 定义在这个作用域链里，其中的变量 scope 一定是局部变量，不管何时何地执行函数 f()，这种绑定在执行 f() 时依然有效。

```javascript
var name="999999"
var b = {
name :"The Window",
object:{
　　　　name : "My Object",
　　　　getNameFunc : function(){
　　　　　　return function(){
                return this.name
              }
　　　　}
　　}
}
console.log(b.object.getNameFunc()()) // 999999
---
var name="999999"
var b = function(){
var name = "The Window";
　　var object = {
　　　　name : "My Object",
　　　　getNameFunc : function(){
 
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};
　　　　}
　　};
　console.log(object.getNameFunc()())
}
b() // 999999
```