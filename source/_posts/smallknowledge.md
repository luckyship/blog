---
title: 你所不知道的前端冷门小知识(长期更新)
tags:
  - 知识
copyright: true
comments: true
date: 2018-06-11 21:55:52
categories: 知识
top: 107
photos:
---
---
<!-- more -->

## void 
void其实是javascript中的一个函数，接受一个参数，返回值永远是undefined
```javascript
void 0  
void (0)  
void "hello"  
void (new Date())  
// all will return undefined  
context == void 666
```
## Element.scrollIntoViewIfNeeded
 Element.scrollIntoViewIfNeeded（）方法用来将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。 如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动。
 ```javascript
element.scrollIntoViewIfNeeded(); // 等同于element.scrollIntoViewIfNeeded(true) 
element.scrollIntoViewIfNeeded(true); 
element.scrollIntoViewIfNeeded(false);
 ```
- 当元素已经在可视区域时，调用 Element.scrollIntoView()，无论设置什么参数，均发生滚动。
- 当元素已经在可视区域时，调用 Element.scrollIntoViewIfNeeded()，无论设置什么参数，均不发生滚动。

## JS取整
```javascript
~~2.5 // 2 按位取反 -2^31~2^31-1 -2147483648~2147483647
0|3.123;//3 或运算
0|4.3423 ;//4
```

与Math.floor()的对比
|区别|Math.floor|~~|
|:---|:---:|---:|
|NaN|NaN|0|
|+0|+0|0|
|-0|-0|0|
|+Infinity|+Infinity|0|
|-Infinity|-Infinity|0|
|1.2|1.2|1.2|
|-1.2|-1|-1|

1. ~ 的结果是 int32 的有符号整数，所以肯定不可能是 NaN 和无穷，因此 1、4、5 两者不同。

3. Math.floor 向 +∞ 取整。

3. parseInt(string, radix);

parseInt() 函数解析一个字符串参数，并返回一个指定基数的整数 (数学系统的基础)。

```javascript
parseInt(0.0000000003) // 3

parseInt('2017-07-04') // 2017
```
## 全等判断
javascript 中 +0 完全等于 -0，那么怎么分区两者呢？
```javascript
1/0 === 1/-0 // false 
+0 === -0 // true
Object.is(+0,-0) // false
```
区分NaN
```javascript
NaN !== NaN // true
NaN === NaN // false 
Object.is(NaN,NaN) // true
```
