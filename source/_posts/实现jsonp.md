---
title: 实现jsonp
tags:
  - 知识
copyright: true
comments: true
date: 2018-10-30 14:08:48
categories: 知识
photos:
top: 112
---

作为常用的跨域解决方案，怎么能不用原生实现一下呢！

jsonp跨域其实也是JavaScript设计模式中的一种代理模式。
在html页面中通过相应的标签从不同域名下加载静态资源文件是被浏览器允许的，所以我们可以通过这个“犯罪漏洞”来进行跨域。
一般，我们可以动态的创建script标签，再去请求一个带参网址来实现跨域通信。

```javascript
// 实现目标
JSONP(url, {
  data: {
    key1: value1
  },
  callback: function (data) {
    // data 是服务端返回的数据
  }
})
```

--- 
<!-- more -->

```javascript
const JSONP = (url, jsonpObj) => {
  // 属性名 
  let cbName = "cb" + JSONP.count++
  // 回调函数 
  let cbQuery = "JSONP." + cbName
  let paramsToQuery = obj => {
    let query = '?'
    for (let k in obj) {
      if (obj.hasOwnProperty(k)) {
        query += `${k}=${obj[k]}&`
      }      
    }
    return query
  }
  JSONP[cbName] = data => {
    try {
      jsonpObj.callback(data)
    } finally {
      delete JSONP[cbName]
      document.body.removeChild(script)
    }   
  }
  let queryStr = paramsToQuery(jsonpObj.data) + 'callback=' + cbQuery
  let script = document.createElement('script')
  script.src = url + encodeURIComponent(queryStr)
  document.body.appendChild(script)
}
JSONP.count = 0
```

- 简单的不传参数的jsonp实现
```js
const jsonp = url => {
  new Promise({resolve, reject} => {
    // 创建标签
    const script = document.createElement('script')
    // 设置回调名
    const callbackId = `jsonp_${Date.now()}`
    // 拼接url
    script.src = url.includes('?') ? `${url}&callback=${callbackId}` : `${url}?callback=${callbackId}`

    // 设置读取返回结果的回调函数, 必须设置在window上
    window[callbackId] = result => {
      // 释放内存
      delete window[callbackId]
      // 移除标签
      document.body.removeChild(script)
      // 结果
      result ? resolve(result) : reject('404')
    }

    script.addEventListener('error', () => reject('script create fail'))
    // 发出请求
    document.body.appendChild(script)
}

jsonp('').then(console.log)
```