---
title: es6函数新特性
tags:
  - es6
copyright: true
comments: true
date: 2018-06-08 19:35:58
categories: JS
photos:
---

1. 设置函数默认值 
2. 结合解构赋值默认值使用
3. 利用rest(...变量名)传入任意参数

<!-- more -->

## ...rest和扩展运算符
rest参数（…变量名），用于获取函数的多余参数，rest参数搭配的变量是一个数组，该变量将多余的参数放入其中。
```javascript
function foo(...y){
    console.log(y)  // [1,2,3,4]
}
foo(1,2,3,4)
```
## 利用扩展运算符（…数组）替代数组的apply方法
在es5中，需要用apply将数组转化为函数参数，在es6中就不需要这种方式了，可以使用…来代替
```javascript
function f(x, y, z) {
    console.log(x + ' ' + y + ' ' + z);
}
var args = [1, 2, 3];
// f.apply(null, args)
f(...args)
```

数组push的例子：
```javascript
var arr1 = [0, 1, 2]
var arr2 = [3, 4, 5]
var arr3 = arr1.concat(arr2)
console.log(arr3) //[ 0, 1, 2, 3, 4, 5 ]

var arr4 = arr1.push(arr2)
console.log(arr1) //[ 0, 1, 2, [ 3, 4, 5 ] ]
```

如果想使用arr1.push方法的话，就需要用apply传arr2了
```javascript
var arr4 = Array.prototype.push.apply(arr1, arr2) //[ 0, 1, 2, 3, 4, 5 ]
```

如果使用扩展运算符的话，就简单一些了
```javascript
var arr4 = arr1.push(...arr2)
```

## name属性
函数的name属性可以返回函数名
```javasctrpt
function abc(){
    console.log(abc.name)
}
abc() // 'abc'
```