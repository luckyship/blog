---
title: JavaScript 中 this 的详解
date: 2018-06-08 16:58:32
tags:
  - this
  - es6
categories: JS
---

## this 的指向

`this` 是 js 中定义的关键字，它自动定义于每一个函数域内。在实际应用中，`this`的指向大致可以分为以下四种情况。

---
<!-- more -->
### 作为普通函数调用
当函数作为一个普通函数被调用，`this`指向全局对象。在浏览器里，全局对象就是 window。
```javascript
window.name = 'cosyer';
function getName(){
    console.log(this.name);
}
getName();                   // cosyer
```
可以看出，此时`this`指向了全局对象 window。
在ECMAScript5的严格模式下，这种情况`this`已经被规定不会指向全局对象了，而是undefined。
```javascript
'use strict';
function fun(){
    console.log(this);
}
fun();                      // undefined
```
### 作为对象的方法调用
当函数作为一个对象里的方法被调用，`this`指向该对象
```javascript
var obj = {
    name : 'cosyer',
    getName : function(){
        console.log(this.name);
    }
}

obj.getName();              // cosyer
```
如果把对象的方法赋值给一个变量，再调用这个变量：
```javascript
var obj = {
    fun1 : function(){
        console.log(this);
    }
}
var fun2 = obj.fun1;
fun2();                     // window
```
此时调用 fun2 方法 输出了 window 对象，说明此时`this`指向了全局对象。给 fun2 赋值，其实是相当于：
```javascript
var fun2 = function(){
    console.log(this);
}
```
可以看出，此时的`this`已经跟 obj 没有任何关系了。这时 fun2 是作为普通函数调用。

### 作为构造函数调用
js中没有类，但是可以从构造器中创建对象，并提供了`new`运算符来进行调用该构造器。构造器的外表跟普通函数一样，大部分的函数都可以当做构造器使用。当构造函数被调用时，`this`指向了该构造函数实例化出来的对象。
```javascript
var Person = function(){
    this.name = 'cosyer';
}
var obj = new Person();
console.log(obj.name);      // cosyer
```
如果构造函数显式的返回一个对象，那么`this`则会指向该对象。
```javascript
var Person = function(){
    this.name = 'cosyer';
    return {
        name : 'chenyu'
    }
}
var obj = new Person();
console.log(obj.name);      // chenyu
```
如果该函数不用`new`调用，当作普通函数执行，那么`this`依然指向全局对象。

### call() 或 apply() 调用
通过调用函数的 call() 或 apply() 方法可动态的改变`this`的指向。
```javascript
var obj1 = {
    name : 'cosyer',
    getName : function(){
        console.log(this.name);
    }
}
var obj2 = {
    name : 'chenyu'
}

obj1.getName();             // cosyer
obj1.getName.call(obj2);    // chenyu
obj1.getName.apply(obj2);   // chenyu
```
## 箭头函数 
{% note info %}
箭头函数的引入有两个方面的作用：一是更简短的函数书写，二是对`this`的词法解析。
在箭头函数出现之前，每个新定义的函数都有其自己的`this`值（例如，构造函数的`this`指向了一个新的对象；严格模式下的函数的`this`值为 undefined；如果函数是作为对象的方法被调用的，则其`this`指向了那个调用它的对象）。在面向对象风格的编程中，这会带来很多困扰。
{% endnote %}

### ES6 的箭头函数 ()=>，指向与一般function定义的函数不同，比较容易绕晕，箭头函数`this`的定义：箭头函数中的`this`是在定义函数的时候绑定，而不是在执行函数的时候绑定。本质来说箭头函数没有自己的`this`，它的`this`是派生而来的。箭头函数会捕获其所在上下文的`this`值，作为自己的`this`值，即指向所在上下文的执行环境(当前定义时的对象)。

### 基础语法
```javascript
// 等价于: => { return expression; } 
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression

// 如果只有一个参数，圆括号是可选的:
(singleParam) => { statements }
singleParam => { statements }

// 无参数或者多参数的箭头函数需要使用圆括号或者下划线:
() => { statements } _ => { statements }
```
### 高级语法
```javascript
// 只返回一个对象字面量,没有其他语句时, 应当用圆括号将其包起来:
params => ({foo: bar})

// 支持 Rest parameters 和 default parameters:
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => { statements }

// 支持参数列表中的解构赋值
var f = ([a, b] = [1, 2], = ) => a + b + c;
f(); // 6
```

### 箭头函数不可以使用arguments对象，
arguments对象在函数体内不存在，如果要用的话，可以用rest参数代替

### 箭头函数没有原型
```javascript
var Foo = () => {};

console.log(Foo.prototype); // undefined
```
### 箭头函数无法构造函数
```javascript
var Foo = () => {};

var foo = new Foo(); // TypeError: Foo is not a constructor
```
### 箭头函数无法使用yield
yield 关键字通常不能在箭头函数中使用（除非是嵌套在允许使用的函数内）。因此，箭头函数不能用作生成器。


let index = 0;
let arr = [];
while (index < 50000) {
    arr.push(index);
    index++;
}

console.time('one');
for (let i = 0; i < arr.length; i++) {

}
console.timeEnd('one');

console.time('two');
for (let i = 0, len = arr.length; i < len; i++) {

}
console.timeEnd('two');

console.time('three');
for (let i = 0, item; item = arr[i++];) {

}
console.timeEnd('three');

console.time('four');
for (let i of arr) {

}
console.timeEnd('four');
console.time('five');
for (let i in arr) {

}
console.timeEnd('five');