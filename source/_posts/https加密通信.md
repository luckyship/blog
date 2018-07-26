---
title: https加密通信流程解析
tags:
  -http
copyright: true
comments: true
date: 2018-07-25 22:18:25
categories: 知识
photos:
---

https其实就是建构在SSL/TLS之上的 http协议，所以https比http要占用更多的资源。
http使用TCP 三次握手建立连接，客户端和服务器需要交换3个包，https除了 TCP 的三个包，还要加上 ssl握手需要的9个包，所以一共是12个包。

---

<!-- more -->

## 流程解析

