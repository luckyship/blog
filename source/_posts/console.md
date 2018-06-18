---
title: console控制台优化
tags:
  - 优化
copyright: true
comments: true
date: 2018-06-18 20:07:08
categories: JS
top: 107
photos:
---

谷歌开发者中心上面关于谷歌浏览器控制台console.log()的文档：

|格式说明符|描述|
|:---|---:|
|%s|将值格式化为字符串。|
|%d?or?%i|将值格式化为整数。|
|%f|将值格式化为浮点值。|
|%o|将值格式化为可扩展的DOM元素（如在元素面板中）。|
|%O|将值格式化为可扩展的JavaScript对象。|
|%c|根据您提供的CSS样式格式化输出字符串。|

\n是换行，可以将一个字符串设置成多行
%c标记之后的内容使用对应样式，格式如 console.log(‘%c第一个样式%c第二个样式’,’css1′,’css2′); 如此对应
样式和普通的css效果基本一致，可以设置文字颜色，背景颜色，字体大小，间距，边距等等。还支持部分css3高级效果。

---
<!-- more -->

## 3D字体效果 Text

```javascript
<script>console.log("%c3D Text"," text-shadow: 0 1px 0 #ccc,0 2px 0 #c9c9c9,0 3px 0 #bbb,0 4px 0 #b9b9b9,0 5px 0 #aaa,0 6px 1px rgba(0,0,0,.1),0 0 5px rgba(0,0,0,.1),0 1px 3px rgba(0,0,0,.3),0 3px 5px rgba(0,0,0,.2),0 5px 10px rgba(0,0,0,.25),0 10px 10px rgba(0,0,0,.2),0 20px 20px rgba(0,0,0,.15);font-size:5em")</script>
```

## Colorful彩色背景 CSS

```javascript
<script>console.log("%cColorful CSS","background: rgba(252,234,187,1);background: -moz-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%,rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -webkit-gradient(left top, right top, color-stop(0%, rgba(252,234,187,1)), color-stop(12%, rgba(175,250,77,1)), color-stop(28%, rgba(0,247,49,1)), color-stop(39%, rgba(0,210,247,1)), color-stop(51%, rgba(0,189,247,1)), color-stop(64%, rgba(133,108,217,1)), color-stop(78%, rgba(177,0,247,1)), color-stop(87%, rgba(247,0,189,1)), color-stop(100%, rgba(245,22,52,1)));background: -webkit-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -o-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: -ms-linear-gradient(left, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);background: linear-gradient(to right, rgba(252,234,187,1) 0%, rgba(175,250,77,1) 12%, rgba(0,247,49,1) 28%, rgba(0,210,247,1) 39%, rgba(0,189,247,1) 51%, rgba(133,108,217,1) 64%, rgba(177,0,247,1) 78%, rgba(247,0,189,1) 87%, rgba(245,22,52,1) 100%);filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#fceabb', endColorstr='#f51634', GradientType=1 );font-size:5em")</script>
```

## Rainbow彩虹字 Text

```javascript
<script>console.log('%cRainbow Text ', 'background-image:-webkit-gradient( linear, left top, right top, color-stop(0, #f22), color-stop(0.15, #f2f), color-stop(0.3, #22f), color-stop(0.45, #2ff), color-stop(0.6, #2f2),color-stop(0.75, #2f2), color-stop(0.9, #ff2), color-stop(1, #f22) );color:transparent;-webkit-background-clip: text;font-size:5em;');</script>
```

## 在Chrome控制台输出图片
```javascript
<script>console.log("%c", "padding:50px 700px;line-height:120px;background:url('http://wx1.sinaimg.cn/large/ba098b64ly1fjz4j8pju5j20p002sgm4.jpg') no-repeat;");</script>
```

## 示例
```javascript
<script>
console.log("%c%c源码作者%ccosyer chenyu@mydearest.cn", "line-height:26px;", "line-height:16px;padding:2px 6px;border-radius: 5px 0px 0px 5px;background:#35495e;color:#fff;font-size:12px;", "padding:2px 8px;background:#0093ff;color:#fff;line-height:16px;font-size:12px;border-radius: 0px 5px 5px 0px;");
console.log("%c%c网站地址%chttps://mydearest.cn", "line-height:26px;", "line-height:16px;padding:2px 6px;border-radius: 5px 0px 0px 5px;background:#35495e;color:#fff;font-size:12px;", "padding:2px 8px;background:#0093ff;color:#fff;line-height:16px;font-size:12px;border-radius: 0px 5px 5px 0px;");
console.log("%c%cgithub%https://github.com/cosyer", "line-height:26px;", "line-height:16px;padding:2px 6px;border-radius: 5px 0px 0px 5px;background:#35495e;color:#fff;font-size:12px;", "padding:2px 8px;background:#0093ff;color:#fff;line-height:16px;font-size:12px;border-radius: 0px 5px 5px 0px;");
</script>
```

https://icomoon.io/

https://zh-cn.cooltext.com/

https://www.logaster.cn/logo/#logos

http://www.logoko.com.cn/design

http://www.beipy.com/335.html