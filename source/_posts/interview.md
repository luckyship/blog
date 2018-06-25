---
title: 面试题整理归纳
tags:
  - 面试
copyright: true
comments: true
date: 2018-06-23 10:58:48
categories: 知识
photos:
---
字符串扩展的方法
- includes()：返回布尔值，表示是否找到了参数字符串。数组也可以 a[1]=1 且能判断undefined
```javascript
var a=[1,2,3]
a[4]=5 // [1, 2, 3, undefined × 1, 5] empty
// a[3]=undefined [1, 2, 3, undefined, 5] 

a.indexOf(undefined) // -1
a.includes(undefined) // true 
```
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。
str | index
- repeat()：返回一个新字符串，表示将原字符串重复n次。参数如果是小数，会被取整(不四舍五入)。参数是负数或者Infinity，会报错。0/Nan返回空字符串,参数是字符串，则会先转换成数字。
- padStart()：头部补全。
- padEnd()：尾部补全
~~~
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
~~~
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。默认使用空格补全
- 模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。`${表达式、变量}`
- commonjs 服务器端 amd 浏览器端
- const 必须赋值定义 let 在同一作用于无法重复命名 无法变量提升
- split(字符串或者正则,设置长度) 字符串=>数组
- substr(开始的索引//splice可以为负数-1则为字符串最后一个字符,length字符数)方法不同的是,substring(开始索引，结束索引+1)负的参数有区别
只有单参数时到字符串结尾
String exd=filePath.subString(filePath.lastIndexOf(".")+1,filePath.length)