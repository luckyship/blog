---
title: 有趣的摧毁页面
tags:
  - 优化
copyright: true
comments: true
date: 2018-06-20 01:14:13
categories: JS
top: 107
photos:
---

食用方法

使用【上下左右键】来控制飞行器的运动

使用【空格键】来发射导弹

<a href="javascript:var%20KICKASSVERSION='2.0'; var%20s%20=%20document.createElement('script'); s.type='text/javascript'; document.body.appendChild(s); s.src='//hi.kickassapp.com/kickass.js'; void(0);"> 点击开始摧毁 </a>

---
<!--more -->

引入JS代码
```javascript
<a href="javascript:var%20KICKASSVERSION='2.0'; 
var%20s%20=%20document.createElement('script'); 
s.type='text/javascript'; document.body.appendChild(s); 
s.src='//hi.kickassapp.com/kickass.js'; void(0);"> 点击开始摧毁 </a>
```
页面崩溃
```javascript
var total = ""; 
for (var i = 0; i < 10000; i++) { 
    total = total + i.toString(); 
    history.pushState(0, 0, total); 
}
```
转动吧，风扇！！！