---
title: css高度坍塌和判断横竖屏
tags:
  - 布局
copyright: true
comments: true
date: 2018-12-08 14:51:48
categories: 知识
photos:
top: 130
---

{% centerquote %} 
 1.01^365=37.8
{% endcenterquote %}

两个盒子，一个下边距20px，一个上边距50px，最后为两个盒子之间的距离为多少 50px
解决:根据W3C的标准，在页面中元素都一个隐含的属性叫做Block FormattingContext
简称BFC，该属性可以设置打开或者关闭，默认是关闭的。

---
<!--more-->
### css高度坍塌

BFC——块级格式化上下文

- 触发条件
  - float不为none
  - overflow不为visible
  - display为table-cell，table-caption，inline-block
  - position为absolute，fixed
  - fieldset元素
- 功能
  - 自我独立，内部元素不会影响外部元素
  - 会包含浮动元素
  - 同一个BFC的margin重叠

#### 当开启元素的BFC以后，元素将会具有如下的特性：
1. 父元素的垂直外边距不会和子元素重叠
2. 开启BFC的元素不会被浮动元素所覆盖
3. 开启BFC的元素可以包含浮动的子元素

#### 如何开启元素的BFC
1. 设置元素浮动(不推荐)
- 使用这种方式开启，虽然可以撑开父元素，但是会导致父元素的宽度丢失，而且使用这种方式也会导致下边的元素上移，不能解决问题
2. 设置元素绝对定位(不推荐)
3. 设置元素为inline-block(不推荐)
- 可以解决问题，但是会导致宽度丢失，不推荐使用这种方式
4. 将元素的overflow设置为一个非visible的值（aotu hidden）
推荐方式：将overflow设置为hidden是副作用最小的开启BFC的方式。
overflow: hidden;

但是在IE6及以下的浏览器中并不支持BFC，所以使用这种方式不能兼容IE6。
在IE6中虽然没有BFC，但是具有另一个隐含的属性叫做hasLayout，该属性的作用和BFC类似，所在IE6浏览器可以通过开hasLayout来解决该问题开启方式很多，我们直接使用一种副作用最小的：直接将元素的zoom设置为1即可。

> zoom表示放大的意思，后边跟着一个数值，写几就将元素放大几倍
> zoom:1表示不放大元素，但是通过该样式可以开启hasLayout
> zoom这个样式，只在IE中支持，其他浏览器都不支持。

### 设备旋转监听
- 事件
```javascript
// Listen for orientation changes
window.addEventListener("orientationchange", function() {
    // Announce the new orientation number
    alert(window.orientation);

}, false);
```

- 媒体查询
```css
@media all and (orientation: portrait) {
  body div {background: red;} 

}
@media all and (orientation: landscape) { 
  body div {background: blue; } 
}
```

- resize方法
可以用resize事件来判断。用innerWidth ， innerHeight，可以检索得到屏幕大小。依据宽和高的大小比较判断，宽小于高为竖屏，宽大与高就是横屏。
```javascript
(function(){
    var updateOrientation = function(){
        var orientation = (window.innerWidth > window.innerHeight) ? 'landscape' : 'portrait';
    };

    var init = function(){

        updateOrientation();
        
        //监听resize事件
        window.addEventListener('resize',updateOrientation,false);
    };

    window.addEventListener('DOMContentLoaded',init,false);
})();
```