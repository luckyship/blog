---
title: css绘制几何图形
tags:
  - 布局
copyright: true
comments: true
date: 2018-08-14 15:04:09
categories: CSS
top: 107
photos:
---

### 圆
```css
div {
width: 100px;
height: 100px;
border-radius: 50px; 
/* 注意宽高相同圆角为一半 */
}
```

### 四个不同方向的半圆
```css
.top
{
  width: 100px;
  height: 50px;
  border-radius: 50px 50px 0 0;
}
.right {
  width: 50px;
  height: 100px;
  border-radius: 0 50px 50px 0;
}
.bottom {
  width: 100px;
  height: 50px;
  border-radius: 0 0 50px 50px;
}
.left {
  width: 50px;
  height: 100px;
  border-radius: 50px 0 0 50px;
}
```

--- 
<!-- more -->

### 四分之一圆
```css
{
  width:50px;
  height:50px;
  border-radius:50px 0 0 0;
}
```

### 菱形
```css
.quarter-ellipse{
    width: 200px;
    height: 150px;
    border-radius: 100% 0 0 0;
    background: black;
}
/*将菱形里的字转成正着的方向*/
.paralle p{
    transform: skew(20deg);
}
```

### 三角形
```css
#triangle-up {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid lightblue;
}
#triangle-down {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-top: 100px solid lightblue;
}

#triangle-left {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-right: 100px solid lightblue;
    border-bottom: 50px solid transparent;
}
#triangle-right {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-left: 100px solid lightblue;
    border-bottom: 50px solid transparent;
}
#triangle-topleft {
    width: 0;
    height: 0;
    border-top: 100px solid lightblue;
    border-right: 100px solid transparent;
}
#triangle-topright {
    width: 0;
    height: 0;
    border-top: 100px solid lightblue;
    border-left: 100px solid transparent; 
}
#triangle-bottomleft {
    width: 0;
    height: 0;
    border-bottom: 100px solid lightblue;
    border-right: 100px solid transparent;
}
#triangle-bottomright {
    width: 0;
    height: 0;
    border-bottom: 100px solid lightblue;
    border-left: 100px solid transparent;
}
```