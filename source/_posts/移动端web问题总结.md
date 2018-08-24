---
title: 移动端web问题总结（长期更新）
tags:
  - 知识
copyright: true
comments: true
date: 2018-08-21 10:14:35
categories: 知识
top: 107
photos:
---

### meta基础知识
#### H5页面窗口自动调整到设备宽度，并禁止用户缩放页面
```javascript
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no" />
```