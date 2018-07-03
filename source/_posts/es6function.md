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

---
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

## 函数的静态变量和函数
```javascript
function Box(){}

Box.num = 12;  //静态变量
Box.fn = function(){};  //静态函数

console.log(Box.num);  //12
console.log(Box.fn);  //function(){}
console.log(typeof Box.fn);  //function

var t = new Box();
console.log(t.num);  //undefined
console.log(t.fn);  //undefined
console.log(typeof t.fn);//undefined
```

静态变量和静态函数是Box对象的属性和方法，不属于实例。

## 函数的实例函数和变量
```javascript
function Box(){
  this.a = [];  //实例变量
  this.fn = function(){};  //实例方法
}

console.log(Box.a);  //undefined
console.log(Box.fn);  //undefined
console.log(typeof Box.fn);  //undefined

var t = new Box();
var t2 = new Box();

console.log(t.a);  //[]
console.log(t2.a);  //[]
t.a.push(1);   // t.a [1]
console.log(t2.a);  //[]

console.log(typeof t.fn);
```
每个实例都有一套实例属性和实例方法，互不影响。

原型上的属性和方法，是实例共用的。

## 函数传参

### 基本类型 (基本类型的变量复制)
```javascript
var count = 10;
function num(num1){
   num1 = 1;
   return num1;
}
var result = num(count);
console.log(result);//1
console.log(count);//10，并未变成1
```
### 引用类型
```javascript
var person  = {
    name : "Tom"
};
function obj(peo){
    peo.name = "Jerry";
    return peo;
}
var result = obj(person);
console.log(result.name);// Jerry
console.log(person.name);// Jerry
```

```javascript
var person = {
    name : "Tom"
}; 
function obj(peo){
    peo = {
       name : "Jerry"
    };
    return peo;
}
var result = obj(person);
console.log(result.name);// Jerry
console.log(person.name);// Tom
```
person传递给函数中的peo，但在函数内部peo又指向了一个新对象，所以result.name是新对象的值，person还是指向原对象，所以并没有改变。

ECMAScript中所有函数的参数都是按值传递的。 ——《JS高程》

我们可以把ECMAScript函数的参数想象成局部变量，在向参数传递基本类型的值时，被传递的值被复制给一个局部变量。

在向函数传递引用类型时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

即使在函数内部修改了参数的值，但原始的引用仍然保持未变。