---
title: webpack
tags:
  - 打包
copyright: true
comments: true
date: 2019-08-18 14:37:40
categories: 工具
photos:
top: 260
---

## 谈谈你对webpack的看法
webpack是一个模块打包工具，可以使用它管理项目中的模块依赖，并编译输出模块所需的静态文件。它可以很好地管理、打包开发中所用到的HTML,CSS,JavaScript和静态文件（图片，字体）等，让开发更高效。对于不同类型的依赖，webpack有对应的模块加载器，而且会分析模块间的依赖关系，最后合并生成优化的静态资源。

## webpack的基本功能和工作原理？
- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等等
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载
- 模块合并：在采用模块化的项目有很多模块和文件，需要构建功能把模块分类合并成一个文件
- 自动刷新：监听本地源代码的变化，自动构建，刷新浏览器
- 代码校验：在代码被提交到仓库前需要检测代码是否符合规范，以及单元测试是否通过
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

## webpack构建过程
- 从entry里配置的module开始递归解析entry依赖的所有module
- 每找到一个module，就会根据配置的loader去找对应的转换规则
- 对module进行转换后，再解析出当前module依赖的module
- 这些模块会以entry为单位分组，一个entry和其所有依赖的module被分到一个组Chunk
- 最后webpack会把所有Chunk转换成文件输出
- 在整个流程中webpack会在恰当的时机执行plugin里定义的逻辑

## webpack打包原理
将所有依赖打包成一个bundle.js，通过代码分割成单元片段按需加载
---
<!--more-->

## 什么是webpack，与gulp,grunt有什么区别
- webpack是一个模块打包工具，可以递归地打包项目中的所有模块，最终生成几个打包后的文件。
- 区别：webpack支持代码分割，模块化（AMD,CommonJ,ES2015），全局分析

## 什么是entry,output?
- entry 入口，告诉webpack要使用哪个模块作为构建项目的起点，默认为./src/index.js
- output 出口，告诉webpack在哪里输出它打包好的代码以及如何命名，默认为./dist

## 什么是loader，plugins?
- loader是用来告诉webpack如何转换某一类型的文件，并且引入到打包出的文件中。
- plugins(插件)作用更大，可以打包优化，资源管理和注入环境变量

## 什么是bundle,chunk,module?
bundle是webpack打包出来的文件，chunk是webpack在进行模块的依赖分析的时候，代码分割出来的代码块。module是开发中的单个模块

## 如何自动生成webpack配置？
可以用一些官方脚手架

- webpack-cli
- vue-cli

```js
// 首先安装
npm install -g @vue/cli
// 新建项目hello
vue create hello
```

nuxt-cli
```js
// 确保安装了npx,npx在npm5.2.0默认安装了
// 新建项目hello
npx create-nuxt-app hello
```

## webpack如何配置单页面和多页面的应用程序？
```js
// 单个页面
module.exports = {
    entry: './path/to/my/entry/file.js'
}

// 多页面应用程序
module.entrys = {
    entry: {
        pageOne: './src/pageOne/index.js',
        pageTwo: './src/pageTwo/index.js'
    }
}
```

## 几个常见的loader
- file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
- url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去
- source-map-loader：加载额外的 Source Map 文件，以方便断点调试
- image-loader：加载并且压缩图片文件
- babel-loader：把 ES6 转换成 ES5
- css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
- style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。
- eslint-loader：通过 ESLint 检查 JavaScript 代码

## 几个常见的plugin
- define-plugin：定义环境变量
- terser-webpack-plugin：通过TerserPlugin压缩ES6代码
- html-webpack-plugin 为html文件中引入的外部资源，可以生成创建html入口文件
- mini-css-extract-plugin：分离css文件
- clean-webpack-plugin：删除打包文件
- happypack：实现多线程加速编译

```js
var UglifyJSPlugin = require('uglifyjs-webpack-plugin');
var webpack = require('webpack');
var ExtractTextPlugin = require("extract-text-webpack-plugin");
var providePlugin = new webpack.ProvidePlugin({$: 'jquery', jQuery: 'jquery', 'window.jQuery': 'jquery'});
module.exports = {
    entry: {
        index: './src/js/index.js',
        goodsInfo: './src/js/goodsInfo.js'
    },
    output: {
        filename: '[name].js',
        path: __dirname + '/out',
        publicPath: 'http://localhost:8080/out'
    },
    module: {
        rules: [
            {test: /.js$/, use: ['babel-loader']},
            // // {test: /.css$/, use: ['style-loader','css-loader']},
            // {
            //     test: /.css$/,
            //     use: ExtractTextPlugin.extract({
            //       fallback: "style-loader",
            //       use: "css-loader"
            //     })
            // },
            {test: /.jpg|png|gif|svg$/, use: ['url-loader?limit=8192&name=./[name].[ext]']}, 
            {test: /.less$/, use: ['style-loader', 'css-loader', 'less-loader']}
        ]
    },
    plugins: [
        new UglifyJSPlugin(),
        new webpack.optimize.CommonsChunkPlugin({
            name: "commons",
            filename: "commons.js",
            minChunks:2}),  
        new ExtractTextPlugin("[name].css"), 
        providePlugin     
    ]
}
```
