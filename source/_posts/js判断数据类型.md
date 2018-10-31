---
title: js判断数据类型
tags:
  - 整理
copyright: true
comments: true
date: 2018-10-31 14:28:15
categories: 知识
photos:
top: 115
---

## typeof
> typeof一般只能返回如下几个结果：number,boolean,string,function,object,undefined字符串

> 对于Array,null等特殊对象使用typeof一律返回object，这正是typeof的局限性。

> 在判断除Object类型的对象时比较方便。

## instanceof 
> instanceof适用于检测对象，它是基于原型链运作的。

> instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。换种说法就是如果左侧的对象是右侧对象的实例， 则表达式返回true, 否则返回false 。

> instanceof对基本数据类型检测不起作用，因为基本数据类型没有原型链。

```javascript
[1, 2, 3] instanceof Array // true 
/abc/ instanceof RegExp // true 
({}) instanceof Object // true 
(function(){}) instanceof Function // true
```

## Object.prototype.toString.call

可以检测各种数据类型，推荐使用。

```javascript
Object.prototype.toString.call([]); // => [object Array] 
Object.prototype.toString.call({}); // => [object Object] 
Object.prototype.toString.call(''); // => [object String] 
Object.prototype.toString.call(new Date()); // => [object Date] 
Object.prototype.toString.call(1); // => [object Number] 
Object.prototype.toString.call(function () {}); // => [object Function] 
Object.prototype.toString.call(/test/i); // => [object RegExp] 
Object.prototype.toString.call(true); // => [object Boolean] 
Object.prototype.toString.call(null); // => [object Null] 
Object.prototype.toString.call(); // => [object Undefined]
```

```javascript
var isType = function( type ){ 
    return function( obj ){ 
        return Object.prototype.toString.call( obj ) === '[object '+ type +']'; 
    } 
};

var isString = isType( 'String' ); 
var isArray = isType( 'Array' ); 
var isNumber = isType( 'Number' );

console.log( isArray( [ 1, 2, 3 ] ) ); //true
```