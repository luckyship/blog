---
title: 获取浏览器url参数方法总结
tags:
  - 知识
copyright: true
comments: true
date: 2018-8-8 23:21:52
categories: 知识
top: 107
photos:
---
 
1. 正则(xxx?typeId=1)
```javascript
function getQueryString(name) {
    var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
    var r = window.location.search.substr(1).match(reg);
    if (r != null) {
        return unescape(r[2]);
    }
    return null;
}
getQueryString("typeId") // "2"

```

2. split拆分
```javascript
function getQueryString() {
    var url = location.search; //获取url中"?"符后的字串
    var theRequest = new Object();
    if (url.indexOf("?") != -1) {
        var str = url.substr(1);
        strs = str.split("&");
        for(var i = 0; i < strs.length; i ++) {
            theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
        }
    }
    return theRequest;
}
getQueryString("typeId") // {typeId:"2}
```

3. 正则获值 和1一样
```javascript
function getQueryString(name) {  
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");  
    var r = window.location.search.substr(1).match(reg);  //获取url中"?"符后的字符串并正则匹配
    var context = "";  
    if (r != null)  
         context = r[2];  
    reg = null;  
    r = null;  
    return context == null || context == "" || context == "undefined" ? "" : context;  
}
```

4. 单参数获取(?1)获取？后面的数据
```javascript
var url=window.location.href
url.substring(url.indexOf('?') + 1)
// url.substr(url.indexOf('?') + 1)

var url=window.location.search
url.substring(1)
// url.substr(1)
```

