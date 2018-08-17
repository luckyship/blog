---
title: react组件实现按需加载
tags:
  - react
copyright: true
comments: true
date: 2018-8-15 10:45:27
categories: JS
top: 107
photos:
---

第一种：ReactTraining/react-router 介绍的基于 webpack, babel-plugin-syntax-dynamic-import, 和 react-loadable.

主要是利用了react-loadable这个高级组件，他是专门用来异步加载（也可以预加载）组件的。

https://github.com/ReactTraining/react-router/blob/master/packages/react-router-dom/docs/guides/code-splitting.md

 ---
 <!-- more --> 

第二种：SF网友介绍的Bundle组件配合webpack组件bundle-loader

bundle-loader效用其实和require.ensure()一样，把组件分片成单独的chunk，在Bundle组件中引入

https://segmentfault.com/a/1190000009539836

 

但是我尝试的时候发现总是报错说不要在import中引入webpack的loader。。。

然后我继续查，发现第三种实现

 

第三种：简书AlienZHOU介绍的同样基于create-react-app的方式，在Bundle组件中props入一个()=>import('path')函数

http://www.jianshu.com/p/547aa7b92d8c

 

第四种，create-react-app文档给的react-router按需加载实现：用一个类Bundle组件的异步函数

https://serverless-stack.com/chapters/code-splitting-in-create-react-app.html

这种方式我试了下，确实能分片，但是讨论里有人指出性能上不如Bundle组件，那个人好像还是Create-react-app的主要贡献者

 

四种方式我都试过，期间。。这个过程略痛苦。。。不过结论是：第三种是目前我认为最好的，基于create-react-app，且实现最简单。

但是较大的项目，一般我们都会eject出来自己配置webpack啦，其实影响也不大。

