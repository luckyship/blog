PWA是Progressive Web App的英文缩写， 也就是就是渐进式增强WEB应用， 是Google 在2016年提出的概念，2017年落地的web技术。

目的就是在移动端利用提供的标准化框架，在网页应用中实现和原生应用相近的用户体验的渐进式网页应用。

一个 PWA 应用首先是一个网页, 可以通过 Web 技术编写出一个网页应用. 随后添加上 App Manifest 和 Service Worker 来实现 PWA 的安装和离线等功能

## 核心技术

- Service Worker （可以理解为服务工厂）
- Manifest （应用清单）
- Push Notification（推送通知）

## service worker (web worker)
1. 外链的js文件，拦截网络请求
2. 一旦注册不可删除除非unregister
3. 运行在https协议(安全性)
4. 消息推送和处理后台同步

web worker
web worker  是运行在后台的JavaScript，独立于其他脚本，不会影响页面的性能 。SW 作用于 浏览器于服务器之间，相当于一个代理服务器。

浏览器一般有三类 web Worker

- Dedicated Worker ：专用的worker，只能被创建它的 JS 访问，创建它的页面关闭，它的生命周期就结束了。


- Shared  Worker ：共享的 worker，可以被同一域名下的JS访问，关联的页面都关闭时，它的生命周期就结束了。


- Service Worker ：是事件驱动的 worker，生命周期与页面无关，关联页面未关闭时，它也可以退出，没有关联页面时，它也可以启动。

## Service Worker生命周期 
看成红绿灯
红 下载和解析
黄 正在执行 还没准备好
绿 随时可使用
且第一次加载页面 sw还没有激活 不会处理任何请求 只有安装和激活后才能使用。（刷新页面和跳转新页面才会生效）

- 步骤
1. 用户导航到url
2. 注册sw 过程中浏览器下载解析执行sw
3. 一旦执行激活安装时间
4. 安装成功就可以控制客户端功能事件

### Parsed （ 解析成功 ）

首次注册 SW 时，浏览器解决脚本并获得入口点，如果解析成功，就可以访问到 SW 注册对象，在这一点中我们需要在 HTML 页面中添加一个判断，判断该浏览器是否支持 SW 。
```js
// 注册ServiceWorker
// 检查当前浏览器是否支持sw
if ('serviceWorker' in navigator) {
    // 如果支持开始注册sw
    navigator.serviceWorker
        .register('./service-worker.js')
        .then(registration => { 
            console.log('Service Worker Registered');
            Notification.requestPermission(function(result) {
                    console.log('result', result)
                    if (result === 'granted') {
                        registration.showNotification('Vibration Sample', {
                            body: 'Buzz! Buzz!',
                            icon: './img/mario.png',
                            vibrate: [200, 100, 200, 100, 200, 100, 200],
                            tag: 'vibration-sample'
                        });
                    } else {
                        alert(result);
                    }     
            });
        })
        .catch(err => console.log(`registration failed with ${err.stack}`));
}
```

### 安装中 （installing）指定缓存

这个状态发生在 Service Worker 注册之后，表示开始安装，触发 install 事件回调指定一些静态资源进行离线缓存。

install 事件回调中有两个方法：
```js
event.waitUntil() // 传入一个 Promise 为参数，等到该 Promise 为 resolve 状态为止。如果 Promise 被拒绝，则安装失败，SW会进入 Redundant（ 废弃 ）状态。

self.addEventListener('install', e => {
    console.log('[ServiceWorker] Install');
    e.waitUntil(
        // 使用指定的缓存名来打开缓存
        caches.open(cacheName)
            .then(cache => {
                console.log('[ServiceWorker] Caching app shell');
                // 将文件添加到缓存中
                return cache.addAll(filesToCache);
            })
            // 可加
            .then(() => {
                console.log('skip waiting')
                return self.skipWaiting() // 为了在页面更新的过程当中，新的 SW 脚本能够立刻激活和生效。无需刷新或者跳转新页面。
            })
    );
});

self.skipWaiting() // self 是当前 context 的 global 变量（ self 是 SW 中作用于全局的对象），执行该方法表示强制当前处在 waiting 状态的 Service Worker 进入 activate 状态。
```

### 已安装 （installed）
Service Worker 已经完成了安装，并且等待其他的 Service Worker 线程被关闭。

### 激活中 （activating）更新缓存

在这个状态下没有被其他的 Service Worker 控制的客户端，允许当前的 worker 完成安装，并且清除了其他的 worker 以及关联缓存的旧缓存资源，等待新的 Service Worker 线程被激活。

```js
// 激活 缓存更新
self.addEventListener('activate', e => {
    console.log('[ServiceWorker] Activate');
    e.waitUntil(
        caches.keys()
            .then(keyList => Promise.all(keyList.map(key => {
                if (key !== cacheName) {
                    console.log('[ServiceWorker] Removing old cache', key);
                    return caches.delete(key);
                }
            })))
            // 可加
            .then(() => {
            return self.clients.matchAll()
                .then(clients => {
                if (clients && clients.length) {
                    clients.forEach((v,i) => {
                        // 发送字符串'sw.update'
                        v.postMessage('sw '+i+' update')
                    })
                }
                })
            })
    );
    return self.clients.claim();
});

self.clients.claim() // 在 activate 事件回调中执行该方法表示取得页面的控制权, 这样之后打开页面都会使用版本更新的缓存。旧的 Service Worker 脚本不再控制着页面，之后会被停止。
```

### 已激活 （activated）

在这个状态会处理 activate 事件回调 (提供了更新缓存策略的机会)。并可以处理功能性的事件 fetch (请求)、 sync (后台同步)、 push (推送)。

### 废弃 （redunant）

这个状态表示一个 Service Worker 的生命周期结束。

这里特别说明一下，进入废弃 (redundant) 状态的原因可能为这几种：

- 安装 (installing) 失败

- 激活 (activating) 失败

- 新版本的 Service Worker 替换了它并成为激活状态

### 处理动态缓存
监听 fetch 事件，在 caches 中去 match 事件的 request ，如果 response 不为空的话就返回 response ，最后返回 fetch 请求，在 fetch 事件中我们可以手动生成 response 返回给页面。
```js
// 捕获请求
self.addEventListener('fetch', e => {
    console.log('[Service Worker] Fetch', e.request.url);
    e.respondWith(
        caches.match(e.request)
            .then(response => response || fetch(e.request))
    );
    // e.respondWith(
    //     caches.match(e.request)
    //         .then(response => {
    //             if(response) {
    //                 return response; // || fetch(e.request)
    //             }
    //             // 新的内容添加到缓存中
    //             // 复制请求 请求是一个流 只能使用一次
    //             var requestToCache = e.request.clone();
    //             return fetch(requestToCache).then(function(response){
    //                 if(!response || response.status !==200) {
    //                     // 错误信息立即返回
    //                     return response;
    //                 }
    //                 var responseToCache = response.clone();
    //                 // 将响应添加到缓存中
    //                 caches.open(cacheName).then(function (cache){
    //                     cache.put(requestToCache, responseToCache);
    //                 })
    //             })
    //         }) 
    // );

    // 自定义响应
    // e.respondWith(new Response('<p>it is a response</p>', {
    //     headers:{
    //         'Content-Type': 'text/html'
    //     }
    // }))
});
```

通过存放到 Cache Storage 中，我们下次访问的时候如果是弱网或者断网的情况下，就可以不走网络请求，而直接就能将本地缓存的内容展示给用户，优化用户的弱网及断网体验。

## Service Worker 注册

```js
// index.html
if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('./sw.js')
        .then(function(reg){
            console,log('注册成功',reg)
        })
        .catch(function(error){
            console.log('注册失败',error)
        })
}
// 查看是否注册成功可以在 PC 上chrome 浏览器, 输入 chrome://inspect/#service-workers
```

## Service Work 安装
```js
// sw.js
self.addEventListener('install', function (event) { 
    return self.skipWaiting(); 
});

// self.oninstall = e => {}
```

## Service Worker 调试

- 借助 Chrome 浏览器 debug
使用 Chrome 浏览器，可以通过进入控制台 Application -> Service Workers 面板查看和调试。

- 查看缓存
Service Worker 使用 Cache API 缓存只读资源，可以在 Chrome DevTools 上查看缓存的资源列表。

http缓存：由服务器告知资源何时缓存和何时过期。sw缓存是对http缓存的增强

## Service Worker 网络跟踪
经过 Service Worker 的 fetch 请求 Chrome 都会在 Chrome DevTools Network 标签页里标注出来，其中：

- 来自 Service Worker 的内容会在 Size 字段中标注为 from ServiceWorker

- Service Worker 发出的请求会在 Name 字段中添加 ‘齿轮’ 图标。

## APP Manifest 与添加到主屏幕

允许将站点添加至主屏幕，是 PWA 提供的一项重要功能

1. 定义 manifest.json 配置添加到主屏幕功能

2. 创建 manifest.json 文件，并将它放到你的站点目录中

3. 在所有页面引入该文件

4. 可以在 Service Worker 中监听 beforeinstallprompt 事件做一些应用内的行为处理

```js
{ 
    "name" : "Minimal PWA" , 
    "short_name" : "PWA Demo" , 
    "display" : "standalone" , 
    "start_url" : "/" , 
    "theme_color" : "#313131" , 
    "background_color" : "#313131" , 
    "icons" : [ 
        { 
        "src" : "e.png" , 
        "sizes" : "256x256" , 
        "type" : "image/png" 
        } 
    ] 
}
```

- name ：定义此PWA的名称。

- icons ：定义一系列的图标以适应不同型号的设备。

- theme_color ：主题颜色（影响手机状态栏颜色）。

- background_color ：背景颜色。

- start_url ：启动地址。由于PWA实际上是一个web页面，所以需要定义PWA 在启动时应该访问哪个地址。

- display ："standalone"表示其以类似原生APP的全屏方式启动。

### IOS Safari 设置
```js
应用图标： 
<link rel="apple-touch-icon" href="apple-touch-icon.png" > 
启动画面： 
<link rel="apple-touch-startup-image" href="launch.png" > 
应用名称： 
<meta name="apple-mobile-web-app-title" content="Todo-PWA" > 
全屏效果： 
<meta name="apple-mobile-web-app-capable" content="yes" > 
设置状态栏颜色： 
<meta name="apple-mobile-web-app-status-bar-style" content="#fff" >
```

### window10 贴片图标
```js
<meta name="msapplication-TileImage" content="images/logo/144x144.png" > 
<meta name="msapplication-TileColor" content="#2F3BA2" >
```

### 在线生成 manifest.json 文件

- https://app-manifest.firebaseapp.com/

- https://tomitm.github.io/appmanifest/

- https://brucelawson.github.io/manifest/

### 参考资料

1. [Web App Manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest)

2. [manifest.json 简介](https://lavas.baidu.com/doc/engage-retain-users/add-to-home-screen/introduction)

## App Shell

App Shell，顾名思义，就是`壳`的意思，也可以理解为`骨架屏`，说白了就是在内容尚未加载完全的时候，优先展示页面的结构、占位图、主题和背景颜色等，它们都是一些被强缓存的html，css和javascript。

要用好App Shell，就必须保证这部分的资源被Service Worker缓存起来。我们在组织代码的时候，可以优先完成App Shell的部分，然后把这部分代码分别打包构建出来。

### 优势

- 始终快速的可靠性能

- 如同本机一样的交互

- 数据的经济使用

### 参考资料

1. [App Shell 模型](https://developers.google.cn/web/fundamentals/architecture/app-shell)

## 使用Offine-Plugin把网站升级成 PWA

### 参考资料

1. [offline-plugin](https://github.com/NekR/offline-plugin)

2. [offline-plugin DEMO](https://offline-plugin.now.sh)

3. [使用offline-plugin搭配webpack轻松实现PWA](https://segmentfault.com/a/1190000010669126)

## 与PWA相关的开源框架

### Lavas

基于 Vue 的 PWA 解决方案，帮助开发者快速搭建 PWA 应用，解决接入 PWA 的各种问题

1. [Lavas 官 网](https://lavas.baidu.com/)

2. [Lavas GitHub](https://github.com/lavas-project/lavas)

### 加载库
importScripts() // sw里的全局函数
```js
importScripts('workbox-sw.prod.v1.1.0.js');

const workboxSW = new self.WorkboxSW();

workboxSW.router.registerRoute('https://test.org/css/(.*)',
workboxSW.strategies.cacheFist);
```
