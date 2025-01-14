---
title: 你所不知道的前端冷门小知识(长期更新)
tags:
  - 知识
copyright: true
comments: true
date: 2018-06-11 21:55:52
categories: 知识
top: 107
photos:
---

[javascript-puzzlers](http://javascript-puzzlers.herokuapp.com/)

## void 
void其实是javascript中的一个函数，接受一个参数，返回值永远是undefined
void expression

```javascript
void 0  
void() // 报错
void (0)  
void "hello"  
void (new Date())  
// all will return undefined  
context === void 666
```

---
<!-- more -->

## Element.scrollIntoViewIfNeeded
Element.scrollIntoViewIfNeeded()方法用来将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。 如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动。
 ```javascript
element.scrollIntoView(); // 等同于element.scrollIntoViewIfNeeded(true) 
element.scrollIntoViewIfNeeded(true); 
element.scrollIntoViewIfNeeded(false);
 ```
- 当元素已经在可视区域时，调用 Element.scrollIntoView()，无论设置什么参数，均发生滚动。
- 当元素已经在可视区域时，调用 Element.scrollIntoViewIfNeeded()，无论设置什么参数，均不发生滚动。

## JS取整数部分

```javascript
~~2.5 // 2 按位取反 -2^31~2^31-1 -2147483648~2147483647
2147483647 >> 0; // => 2147483647
2147483648 >> 0; // => -2147483648
2147483649 >> 0; // => -2147483647

0|3.123;// 3 或运算
4.3|0; // 4
4.3<<0; // 4
```

与Math.floor()的对比

|区别|Math.floor|~~|
|:---|:---:|---:|
|NaN|NaN|0|
|+0|+0|0|
|-0|-0|0|
|+Infinity|+Infinity|0|
|-Infinity|-Infinity|0|
|1.2|1.2|1.2|
|-1.2|-1|-1|

```javascript
1. 位运算：~ 的结果是 int32 的有符号整数，所以肯定不可能是 NaN 和无穷 x|0 x^0 x<<0 x>>0

2. Math.floor向下取整 Math.ceil向上取整 Math.trunc返回整数部分 实现：if(value>0){return Math.floor(value)}else{return Mathg.ceil(value)}

3. parseInt(string, radix);

parseInt() 函数解析一个字符串参数，并返回一个指定基数的整数 (数学系统的基础)。

parseInt 解析字符串 '-0' 会得到 -0。如果参数是数字 -0，会得到 0。

4. function trunc(num) {
  if(num >= 0) return Math.floor(num);
  return Math.ceil(num);
}

5. function trunc(num) {
  return num - num % 1;
}
```

> 在没有指定基数，或者基数为 0 的情况下，JavaScript 作如下处理：
> 如果字符串 string 以"0x"或者"0X"开头, 则基数是16 (16进制).
> 如果字符串 string 以"0"开头, 基数是8（八进制）或者10（十进制），那么具体是哪个
> 基数由实现环境决- 定。ECMAScript 5 规定使用10，但是并不是所有的浏览器都遵循这个
> 规定。因此，永远都要明确给出radix参数的值。
> 如果字符串 string 以其它任何值开头，则基数是10 (十进制)。

```javascript
parseInt(0.0000000003) // 3

parseInt('2017-07-04') // 2017

parseInt("010");	// 8
parseInt("010", 8);	// 8
parseInt("010", 10); // 10
```

- parseFloat(string)
它的规则与parseInt基本相同，但也有点区别：字符串中第一个小数点符号是有效的，另外parseFloat会忽略所有前导0，如果字符串包含一个可解析为整数的数，则返回整数值而不是浮点数值。
```js
parseInt("0xA") // 10
parseFloat("0xA") // 0

parseInt(".1") // NaN
parseFloat(".1") // 0.1

```

## js取小数部分
```js
function fract(num) {
  return num - Math.trunc(num);
}

console.log(fract(3.75)); // 0.75
console.log(fract(-3.75)); // -0.75

console.log(3.75 % 1); // 0.75
console.log(-3.75 % 1); // -0.75
```

## JS浮点数运算(原因浮点数无法用二进制准确表示)

```javascript
0.1+0.2 // 0.30000000000000004
1. parseFloat().toFixed() 
2. *100/100 
```

## Object.toString()
```javascript
var a = {name:'cosyer'}
a.toString() // [object Object] [typeof a instanceof a]
// 除了Object其他对象需要用call返回真正的类型信息
```
## 全等判断
javascript 中 +0 完全等于 -0，那么怎么分区两者呢？

```javascript
1/0 === 1/-0 // false 
+0 === -0 // true
Object.is(+0,-0) // false
```
区分NaN

```javascript
NaN !== NaN // true
NaN === NaN // false 
Object.is(NaN,NaN) // true
```

## 判断NaN
```js
Number.isNaN(); // true 与isNaN全局函数相比，不存在类型转换更加严格
Number.isNaN('测试') // false
isNaN('测试') // true
x!==x // =>NaN false
// {}!=={} []!==[]
```

## isFinite
判定一个数字是否是有限数字(如果参数是 NaN，正无穷大或者负无穷大，会返回false，其他返回 true)
```js
isFinite(Infinity);  // false
isFinite(NaN);       // false
isFinite(-Infinity); // false
isFinite(0);         // true
isFinite(2e64);      // true, 在更强壮的Number.isFinite(null)中将会得到false
isFinite("0");       // true, 在更强壮的Number.isFinite('0')中将会得到false
```

## try-catch跳出forEach循环
forEach遍历不能保证遍历的顺序，以及不能break;一般for循环的性能是forEach的20倍

```javascript 
try {
    [1, 2, 3].forEach(v => {
        if (v === 2) {
            throw new Error('my err')
        }
    })
} catch (e) {
    if (e.message === 'my err') {
        console.log('breaked') 
    } else {
        throw e
    }
}

// 用some也可以在遍历中跳出循环
[1,2,3].some((item)=>{
	console.log(item)
  return item === 2 // 如果item等于2就跳出循环
})
```

## fetch模拟post进行api测试
```javascript
fetch(apiUrl, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({q: 1})
}).then(async res => console.log(await res.json()))
```

## 实现var a = add(2)(3)(4) 函数柯里化
js中console.log一个对象时，会对这个对象进行toString()操作，还有些情况会对对象进行valueOf()操作
vauleOf优先于toString()被调用

```javascript
function add(num){
    var _add = function(args){
        num+=args;
        return arguments.callee; //  return add(num+args);
        // 现在已经不推荐使用arguments.callee();推荐这样写var _add = (function foo(args){num+=args return foo;}) callee被调用者、callee调用者
        // 原因：访问 arguments 是个很昂贵的操作，因为它是个很大的对象，每次递归调用时都需要重新创建。影响现代浏览器的性能，还会影响闭包。
    }
    _add.toString = _add.valueOf = function(){
        return num;
    }
    return _add;
}
add(2)(3)(4);// function 9
+add(2)(3)(4) // 9
add(2)(3)(4)+'' // '9'
```

## 阶乘
```javascript
function factorial(num){    
   if (num <=1) {         
      return 1;     
   } else {         
   return num * factorial(num-1)     
   } 
} 
// 定义阶乘函数一般都要用到递归算法；如上面的代码所示，在函数有名字，而且名字以后也不会变的情况下，这样定义没有问题。 
// 但问题是这个函数的执行与函数名 factorial 紧紧耦合在了一起。为了消除这种紧密耦合的现象，可以像下面这样使用 arguments.callee
// arguments 的主要用途是保存函数参数， 但这个对象还有一个名叫 callee 的属性，返回正被执行的 Function 对象，
// 也就是所指定的 Function 对象的正文，这有利于匿名函数的递归或者保证函数的封装性。 

function factorial(num){    
   if (num <=1) {         
      return 1;     
   } else {         
   return num * arguments.callee(num-1);
   } 
}

var trueFactorial = factorial;
factorial = function() {
    return 0;
}                
alert(trueFactorial(5));// 120 如果没有使用arguments.callee，将返回0
```

## arguments.callee的替换方案
```javascript
function show(n) {
    var arr = [];
    return (function () {
        arr.unshift(n);
        n--;
        if (n != 0) {
            arguments.callee();
        }
        return arr;
    })()
}
show(5)//[1,2,3,4,5]

// 给内部函数一个名字（当函数被调用时，它的arguments.callee对象就会指向自身，也就是一个对自己的引用。）
function show(n) {
    var arr = [];
    return (function fn() {
        arr.unshift(n);
        n--;
        if (n != 0) {
            fn();
        }
        return arr;

    })()
}
show(5)//[1,2,3,4,5]
```

## Date相关 

### Date构造函数
```javascript
4种表示时间戳的方式
1. Date.now()
2. new Date().getTime()
3. +new Date() / +new Date +相当于.valueOf();
4. new Date().valueOf()
5. new Date*1 / new Date()*1 

解释：JavaScript中可以在某个元素前使用'+'号，这个操作是将该元素转换秤Number类型，如果转换失败，那么将得到 NaN。
所以 +new Date 将会调用 Date.prototype 上的 valueOf 方法，而根据MDN，Date.prototype.value 方法等同于 Date.prototype.getTime()

Date.parse("2018-06-13") === new Date("2018-06-13").getTime()
// 浏览器之间解析时间不同 safari 解析横杠 - 会出错所以尽量用斜杠 /
```
### 当前时间
```javascript
let d = new Date()
let year=d.getFullYear();
let month=d.getMonth()+1; // 月份索引从0开始
let day=d.getDate(); // getDay()用于获取星期
let hour=d.getHours();
let minute=d.getMinutes();
let second=d.getSeconds();
console.log(`${year}-${month}-${day} ${hour}:${minute}:${second}`) // 2018-6-13 21:20:48
// 不足2位数补0
console.log([year, month, day].map((item)=>{
        item = item.toString();
    return item[1] ? item : "0" + item;
}).join("-") +" " +[hour, minute, second].map((item)=>{
        item = item.toString();
    return item[1] ? item : "0" + item;
}).join(":"))  // 2018-06-13 21:20:48
```
### Date计时
以博客存活时间为例

```javascript
var time = new Date(); 
var t = "博客存活了"+Math.floor((+new Date - 1527868800000) / (1000 * 60 * 60 * 24)) + "天" + time.getHours() + "小时" 
+ time.getMinutes() + "分" + time.getSeconds() + "秒"; 
// 博客存活了11天 21小时28分51秒 1527868800000当时的时间转的时间戳 new Date(1527868800000).toLocaleString() "2018/6/2 上午12:00:00"
```

### Date原型扩展方法
```javascript
Date.prototype.format = function (format) {
			var o = {
					"M+": this.getMonth() + 1,
					"d+": this.getDate(),
					"h+": this.getHours(),
					"m+": this.getMinutes(),
					"s+": this.getSeconds(),
					"q+": Math.floor((this.getMonth() + 3) / 3),
					"S": this.getMilliseconds()
			};
			if (/(y+)/.test(format)) {
					format = format.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
			}
			for (var k in o) {
					if (new RegExp("(" + k + ")").test(format)) {
							format = format.replace(RegExp.$1, RegExp.$1.length == 1 ? o[k] : ("00" + o[k]).substr(("" + o[k]).length));
					}
			}
			return format;
	};

	Date.prototype.addDays = function (d) {
			this.setDate(this.getDate() + d);
	};

	Date.prototype.addWeeks = function (w) {
			this.addDays(w * 7);
	};

	Date.prototype.addMonths = function (m) {
			var d = this.getDate();
			this.setMonth(this.getMonth() + m);
			//if (this.getDate() < d)
			//  this.setDate(0);
	};
```

## 页面加载时间
```javascript
window.onload = function () {
  var loadTime = window.performance.timing.domContentLoadedEventEnd-window.performance.timing.navigationStart; 
  console.log('Page load time is '+ loadTime);
}
// DOMContentLoad是HTML文档被加载和解析完成后触发
```
onload和onready的区别：

1. 执行时间

　　window.onload必须等到页面内包括图片的所有元素加载完毕后才能执行。 

　　$(document).ready()是DOM结构绘制完毕后就执行，不必等到加载完毕。

2. 编写个数不同

　　window.onload不能同时编写多个，如果有多个window.onload方法，只会执行一个。

　　$(document).ready()可以同时编写多个，并且都可以得到执行。

3. 简化写法

　　window.onload没有简化写法。

　　$(document).ready(function(){})可以简写成$(function(){});

## 常用标签
```javascript
<meta charset="utf-8">
<meta content="text/html; charset=utf-8" http-equiv="Content-Type">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon" />
// seo
<title></title>
<meta name="author" name="cosyer">
<meta name="keywords" name="cosyer">
<meta name="description" name="cosyer">
<link rel="stylesheet" href="">
<script src=""></script>
```

## 获取url参数
```javascript
let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)")
let r = window.location.search.substr(1).match(reg)
if (r != null) return decodeURIComponent(r[2]) // encodeURIComponent()
return null
```

## String原型方法扩展
```javascript
// 连字符转驼峰
String.prototype.hyphenToHump = function () {
		return this.replace(/-(\w)/g, function () {
				return arguments[1].toUpperCase()
		})
}

// 连字符转驼峰 简单版本
// 假设分隔符有_和-两种
function camel(str) {
  let cut = ['_', '-'];
  let ar = str.split('');
  let l = ar.length;
  let flag = false;
  for(let i = 0;i < l;i++) {
    let cur = ar[i];
    if(cut.indexOf(cur) !== -1) {
      ar[i] = '';
      flag = true;
    } else if(flag) {
      ar[i] = ar[i].toUpperCase();
      flag = false;
    }
  }
  return ar.join('');
}

// 驼峰转连字符
String.prototype.humpToHyphen = function () {
		return this.replace(/([A-Z])/g, "-$1").toLowerCase()
}
```

## 拦截控制台、右键和F12
```javascript
document.onkeydown = function () {
        var e = window.event || arguments[0];
        //屏蔽F12
        if (e.keyCode == 123) {
                return false;
                //屏蔽Ctrl+Shift+I
        } else if ((e.ctrlKey) && (e.shiftKey) && (e.keyCode == 73)) {
                return false;
                //屏蔽Shift+F10
        } else if ((e.shiftKey) && (e.keyCode == 121)) {
                return false;
        }
};
//屏蔽右键单击
document.oncontextmenu = function () {
        return false;
};
```

## 崩溃欺骗
```javascript
var OriginTitle = document.title;
var titleTime;
document.addEventListener('visibilitychange', function () {
    if (document.hidden) {
        $('[rel="icon"]').attr('href', "/img/TEP.ico");
        document.title = '╭(°A°`)╮ 页面崩溃啦 ~';
        clearTimeout(titleTime);
    }
    else {
        $('[rel="icon"]').attr('href', "/favicon.ico");
        document.title = '(ฅ>ω<*ฅ) 噫又好了~' + OriginTitle;
        titleTime = setTimeout(function () {
            document.title = OriginTitle;
        }, 2000);
    }
});
```

## a标签
```javascript
    // 邮件
	<a href={'mailto:'+props.email}></a>
	// 下载只有 Firefox 和 Chrome 支持 download 属性。
	<a href="/images/myw3schoolimage.jpg" download="w3logo"></a>
    // QQ
	<a href="tencent://message/?uin=535509852&Site=-&Menu=yes" target="_blank">QQ:535509852</a>
```

##  `<script>`元素放在 HTML 文件底部

我们将 `<script>`元素放在 HTML 文件底部的原因是，浏览器按照代码在文件中的顺序解析 HTML。如果 JavaScript在最前面被加载，HTML还未加载，JavaScript将无法作用于HTML，所以JavaScript无效，如果 JavaScript 代码出现问题则 HTML 不会被加载。所以将 JavaScript 代码放在底部是最好的选择。当然在当下，并不是说 script 标签必须放在底部，因为你可以给 script 标签添加 defer 或者 async 属性。

## 某个字符在字符串中的个数
```javascript
let str="11112234241"
console.log(str.split("1").length-1)
```

## 数组求最大值方法汇总
```javascript
1. es6拓展运算符...
Math.max(...arr)
2. es5 apply(与方法1原理相同)
Math.max.apply(null,arr)
3. for循环
let max = arr[0];
for (let i = 0; i < arr.length - 1; i++) {
    max = max < arr[i+1] ? arr[i+1] : max
}
4. 数组sort
arr.sort((a,b)=>{
	return b-a // 降序
})
5. 数组reduce
arr.reduce((a,b)=>{
	return a>b?a:b
})
```

```javascript
function foo(p1,p2) {
this.val = p1 + p2;
}
var bar = foo.bind( null, "p1" );
var baz = new bar( "p2" );
baz.val; // p1p2
```

## 回到顶部
```javascript
function goback() {
// 1.回到顶部
// scrollTo(0, 0); // 滚动条滚动 x y 
// 2.渐渐回到顶部 距离顶部高度
//var iScrollTop = document.body.scrollTop; //360,Chrome,
//var iScrollTop = document.documentElement.scrollTop;  //IE8,火狐
    var iScrollTop = document.body.scrollTop || document.documentElement.scrollTop;
    var timer = setInterval(function () {  //定时器
        scrollTo(0, iScrollTop -= 100);
        console.log(iScrollTop);
        if (iScrollTop <= 0) {
            clearInterval(timer);  //清除定时器
        }
    }, 100);
}
```

## 手机号处理中间4位替换成*(数据脱敏)

### 方法1 substr方法
```javascript
let phone="18883269663"
let phone1=phone.substr(0,3)+"****"+phone.substr(7)
```

### 方法2 正则匹配分组
```javascript
let tel = "13122223333";

let reg = /^(\d{3})\d{4}(\d{4})$/;

tel = tel.replace(reg, "$1****$2");
```

## 转换树形结构数据
```javascript
let arr=[{"departmentId":"2807369902638080","departmentName":"四合院大数据有限公司","departmentDesc":"四合院大数据有限公司是非常好的公司","createTime":null,"hasChild":false,"parentId":null},{"departmentId":"2805950137730048","departmentName":"产品策划部","departmentDesc":null,"createTime":null,"hasChild":true,"parentId":null},{"departmentId":"2805949110338560","departmentName":"研发中心","departmentDesc":null,"createTime":null,"hasChild":true,"parentId":null}];

function fn(data, p_id) {
  var result = [],
    temp;
  for (var i = 0; i < data.length; i++) {
    if (data[i].parentId == p_id) {
      var obj = data[i];
      obj.label = data[i].departmentName;
      temp = fn(data, data[i].departmentId);
      if (temp.length > 0) {
        obj.children = temp;
      }
      result.push(obj);
    }
  }
  return result;
}

let treeData=fn(treeData,null) // 传入原始数据/parentId
适用于ant design和element-ui等树形结构数据
```

## 浏览器返回上一页
```javascript
<a href="#" onclick="javascript:history.back(-1);">返回到上一页</a>

<a href="#" onClick="javascript:history.go(-1)">返回上一页</a>

// window.open(url,"_blank")
// window.location.href=url
// window.location.replace(url)
```

## 浏览器前进后退
```javascript
history.forward();//前进
history.back();//后退+刷新
history.back(-1)//后退,直接返回当前页的上一页，数据全部消息，是个新页面
history.go(1);//前进
history.go(-1);//后退也是返回当前页的上一页，不过表单里的数据全部还在
```

## 去重数组里对象相同值得key
```javascript
const key = 'id'
const arr = [{ id: 1 }, { id: 1 }, { id: 2 }, { id: 3 }]
const filted = arr.reduce((finalList, obj) => {
  if (!finalList.find(T => T[key] === obj[key])) {
    finalList.push(obj)
  }
  return finalList
}, [])
// [{ id: 1 }, { id: 2 }, { id: 3 }]

let map =new Map()
for (let item of arr){
map.set(item[key],item)
}
[...map.values()]
```

## 变量声明
```javascript
(function() {
      var a = b = 5; // var a=b;b=5 严格模式会报错
  })();   
console.log(b); // 5
console.log(a); // Uncaught ReferenceError: a is not defined

var a=[]; a[0]=1; a[1]=2; a[2]=3; a[5]=4; 
a.length // 6
while不改变作用域链
[]?true:false // true
[]==false?true:false // true
2==true //false
2==false // false

+new Array(017) // NaN

new Array(1,2,3) // [1,2,3]
new Array(1) // [empty]
Array.of(7) // [7] Array.of解决掉了这个陷阱
```

## with语句
with语句的作用是将代码的作用域设置到一个特定的对象中。

利：with语句可以在不造成性能损失的情况下，减少变量的长度。很多情况下，也可以不使用with语句，而是使用一个临时变量来保存指针，来达到同样的效果。

弊：with语句使得程序在查找该语句块中的所有的变量值时，都是先在该with语句指定的对象下面先寻找一遍，然后再去外面的作用域去寻找。所以尽量不要在该语句块中去使用一些不属于该对象中的变量

用法

```javascript
var x = {
  name : "古朋",
  nick_name : "gupeng"
};
with(x){
  console.log(name+'的小名是'+nick_name);
}
```

相当于

```javascript
var x = {
  name : "古朋",
  nick_name : "gupeng"
};
/*
 *这里将x对象赋值到当前局部变量中，减少不必要的指针路径解析运算
 *一般用于在在方法中将this对象局部化，比如：var this_ = this;
 */
var x_ = x;
console.log(x_.name+'的小名是'+x_nick_name);
```

## boolean类型在进行比较时会类型转换成number

```javascript
11==true // 11==1 false
1==true  // 1==1 true
```

## RGB到十六进制
使用按位左移运算符(<<)和toString(16)，然后padStart(6,'0')将给定的RGB参数转换为十六进制字符串以获得6位十六进制值。

```javascript
const rgbToHex = (r, g, b) => ((r << 16) + (g << 8) + b).toString(16).padStart(6, '0');
// ((1<<24) + (rgb.r<<16) + (rgb.g<<8) + rgb.b).toString(16).substr(1);
// rgbToHex(255, 165, 1) -> 'ffa501'
```

## 滚动到顶部
使用document.documentElement.scrollTop或document.body.scrollTop获取到顶部的距离。

```javascript
const scrollToTop = _ => {

    const c = document.documentElement.scrollTop || document.body.scrollTop;

    if (c > 0) {

    ​ window.requestAnimationFrame(scrollToTop);

    ​ window.scrollTo(0, c - c / 8);

    }
};

// scrollToTop()
```

## 求和
```javascript
const sum = arr => arr.reduce((acc, val) => acc + val, 0);

// sum([1,2,3,4]) -> 10 累加器
// 平均数 sum([1,2,3,4])/4
```

## 大写每个单词的首字母
使用replace()匹配每个单词的第一个字符，并使用toUpperCase()来将其大写。

```javascript
const capitalizeEveryWord = str => str.replace(/[a-z]/g, char => char.toUpperCase());
// capitalizeEveryWord('hello world!') -> 'HELLO WORLD!'

function titleCase(s) {  
    return s.toLowerCase().replace(/\b([\w|']+)\b/g, function(word) {  
        //return word.slice(0, 1).toUpperCase() + word.slice(1);  
        return word.replace(word.charAt(0), word.charAt(0).toUpperCase());  
    });  
}
// titleCase('hello world!') -> 'Hello World!'
```

## 计算数组中某值出现的次数
每次遇到数组中的特定值时，使用reduce()来递增计数器。

```javascript
const countOccurrences = (arr, value) => arr.reduce((a, v) => v === value ? a + 1 : a + 0, 0);

// countOccurrences([1,1,2,1,2,3], 1) -> 3
```

## 数组间的区别
从b创建一个Set，然后在a上使用Array.filter()，只保留b中不包含的值。

```javascript
const difference = (a, b) => { const s = new Set(b); return a.filter(x => !s.has(x)); };

// difference([1,2,3], [1,2]) -> [3]
```

## 阶乘
使用递归。如果n小于或等于1，则返回1。否则返回n和n - 1的阶乘的乘积。

```javascript
const factorial = n => n <= 1 ? 1 : n * factorial(n - 1);

// factorial(6) -> 720
```

## 获取滚动位置
如果已定义，请使用pageXOffset和pageYOffset，否则使用scrollLeft和scrollTop，可以省略el来使用window的默认值。

```javascript
const getScrollPos = (el = window) =>

({x: (el.pageXOffset !== undefined) ? el.pageXOffset : el.scrollLeft,
y: (el.pageYOffset !== undefined) ? el.pageYOffset : el.scrollTop});

// getScrollPos() -> {x: 0, y: 200}
```

## 最大公约数
```javascript
const gcd = (x, y) => !y ? x : gcd(y, x % y);

// gcd (8, 36) -> 4
1e3 // 1000
1E3 // 1000
```

## UUID生成器
使用crypto API生成符合RFC4122版本4的UUID。

```javascript
const uuid = _ =>
([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c => (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16));

// uuid() -> '7982fcfe-5721-4632-bede-6000885be57d'
```

## 检查变量是否声明
如果读取一个不存在的键，会返回undefined，而不是报错。可以利用这一点，来检查一个全局变量是否被声明。
前二种写法有漏洞，如果a属性是一个空字符串（或其他对应的布尔值为false的情况），则无法起到检查变量是否声明的作用。
最好的方法是使用in

```javascript
// 假设变量x未定义

// 写法一：报错
if (x) { return 1; }

// 写法二：不正确
if (window.x) { return 1; }

// 写法三：正确
if ('x' in window) { return 1; }
```

## 使用对象结构代替 switch…case
switch…case不使用大括号，不利于代码形式的统一。此外，这种结构类似于goto语句，容易造成程序流程的混乱，使得代码结构混乱不堪，不符合面向对象编程的原则。

```javascript
// 反模式
function doAction(action) {
    switch (action) {
        case 'hack':
            // ...
            break;
        case 'slash':
            // ...
            break;
        default:
            throw new Error('Invalid action.');
    }
}
// 好模式
function doAction(action) {
    var actions = {
        hack: function () {
            // ...
        },
        slash: function () {
            // ...
        },
    };

    if (typeof actions[action] !== 'function') {
        throw new Error('Invalid action.');
    }

    return actions[action]();
}
```

## 隐藏console打印在哪一行的信息
```javascript
setTimeout(console.log.bind(console, 'Hello world'), 2000)
```

## 手机震动
```javascript
window.navigator.vibrate(200); // vibrate for 200ms
```

## [1,2,11]用sort方法进行排序
```javascript
[1,2,11].sort() // [1,11,2] sort方法默认根据unicode进行排序

[1,undefined,null].sort() // [1, null, undefined]

[1,2,11].sort((a,b)=>a-b)  // [1,2,11]

// bind是函数柯里化得一种实现
```

## [1,2,3].map(parseInt)
```javascript
[1,2,3].map(parseInt) // [1,NaN,NaN] 这里第二个参数是map的index，3对应下标2，3没有二进制的。

parseInt("11",2);		//返回 3 (2+1)
```
parseInt第二个参数	
> 可选。表示要解析的数字的基数。该值介于 2 ~ 36 之间。

> 如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。

> 如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

## 图片上传
1. 传统的form表单上传
```javascript
<form action="" method="POST" enctype="multipart/form-data">
    <input type="file" name="img">
</form>
```

2. 隐藏iframe模拟Ajax上传
```javascript
// form的enctype属性为编码方式，常用有两种：application/x-www-form-urlencoded和multipart/form-data，默认为application/x-www-form-urlencoded。
// multipart/form-data是上传二进制数据;它告诉我们传输的数据要用到多媒体传输协议
<iframe  name="ajaxUpload" style="display:none"></iframe>
<form action="url" method="post" enctype="multipart/form-data" target="ajaxUpload">
    <input type="file" name="img">
</form>
```

3. h5 ajax上传
```javascript
// 使用已有的form表单，来实例化FormData对象
var form = document.getElementById('form');
var formData = new FormData(form);

// 可以使用append来添加更多的信息
var img = document.getElementById('img').files[0];
var formData = new FormData();
formData.append('img',img);

// 获取 
// formData.get('img') formData.getAll('img') [img]

// 设置修改
// formData.set('img',1) // 已存在则修改，不存在则新增

// 判断是否存在
// formData.has('img')

// 删除
// formData.delete('img')

// 返回迭代器
// formData.entries()
// formData.append("k1", "v1");
// formData.append("k1", "v2");
// formData.append("k2", "v1");

// var i = formData.entries();

// i.next(); // {done:false, value:["k1", "v1"]}
// i.next(); // {done:fase, value:["k1", "v2"]}
// i.next(); // {done:fase, value:["k2", "v1"]}
// i.next(); // {done:true, value:undefined}

// 利用XHR上传图片
var xhr = new XMLHttpRequest();
xhr.open('POST',url);
xhr.onreadystatechange = function(){};
xhr.send(formData);

// 获取上传的进度
xhr.upload.onprogress = function(evt){
    console.log(evt);
    var loaded = evt.loaded; //已经上传的大小
    var total = evt.total;//附件总大小
    var per = Math.floor(100 * loaded / total );//已经上传的百分比
}
```

4. 检验图片信息
```javascript
<input type="file" id="img" name="img"/>

// 获取file对象
var img = document.getElementById('img');
var files = img.files;
var file = files[0];
```

## 实现JSONP
```javascript
// 对象存储
const JSONP = (url, jsonpObj) => {
  let cbName = "cb" + JSONP.count++
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

// 数组存储
var JSONP = (url, descriptor) => {
  var script = document.createElement('script')
  var body = document.getElementsByTagName('body')[0]

  var parseParam = (paramObj) => {
    var paramStr = '?',
        prop = ''
    for (prop in paramObj) {
      if(paramObj.hasOwnProperty(prop)) {
        paramStr += (`${prop}=${encodeURIComponent(paramObj[prop])}&`)
      }
    }

    return paramStr
  }

  var params = parseParam(descriptor.data)
  var callback = (data) => {
    descriptor.callback(data)
    body.removeChild(script)
    delete JSONP.cbs[index]
  }
  var index = JSONP.cbs.push(callback) - 1

  script.src = url + params + 'callback=' + encodeURIComponent(`JSONP.cbs[${index}]`)
  body.appendChild(script)
}

JSONP.cbs = []
```

## chrome浏览器拦截多次window.open
```javascript
 // 遍历打开页面 chrome浏览器会拦截只允许一次的请求 这里模拟点击
[1,2,3].forEach(()=>{
        var aDom = document.createElement('a');
        aDom.href = 'https://nodejs.org/dist/v10.13.0/node-v10.13.0-x64.msi';
        aDom.target = '_blank';
        aDom.click();
});
```

## mixed
```javascript
1 << 31 // 1* 2^31 
2 >> 1  // 2/2^1
// ---------------
document.getElementById("#app")
document.querySelector("#app") // 第一个元素
document.querySelectorAll("#app") // 符合的所有元素
p.example // class 
a[target] // 属性
h1,h2 // 多个选择器按照文档中的位置设置
document.querySelector("body").contentEditable = true //设置内容是否可编辑 子元素继承父元素
isContentEditable // 查看是否可编辑
<div oncontextmenu="myFunction()" contextmenu="mymenu"/> // 右击菜单事件
html 
obj.oncontextmenu= function(){}
obj.addEventListener("contextmenu",function(){})
onabort // 用于img加载终止 alt title onerror发生错误
onhashchange // 路由锚点变化
onfocusin // 支持冒泡的onfocus
oninput // input onchange失去焦点触发 
onpropertychange// input 会实时触发，会在元素的属性改变时就触发事件当元素disable=true时不会触发只在IE 下支持，其他浏览器不支持，用oninput来解决
onreset // form表单被重置触发 input type="reset"
onsearch // input type search 搜索事件
onsubmnit // form 提交 action
onscroll // 元素滚动时触发
oncopy // 在元素上拷贝脚本
oncut // 剪切
onpaste // 粘贴
onafterprint // body 页面打印后触发 window.print() 对应onbeforeprint
oncanplay // 准备开始播放 设置或者返回
document.getElementsByTagName() // 返回标签集合
document.getElementsByClassName() // 返回类名集合
obj.removeEventListener()
removeChild()
removeAttribute()
```

## 高度
```javascript
window.scrollY // 225
window.pageYOffset // 225
window.document.body.scrollTop // 0
window.document.documentElement.scrollTop // 225
// 特殊字符:~ ! @ # % ^ * - _ = + ?
```

## 自适应单位
1. vw：1vw等于视口宽度的1%。

2. vh：1vh等于视口高度的1%。

3. vmin：选取vw和vh中最小的那个。

4. vmax：选取vw和vh中最大的那个

CSS百分比是相对于包含它的最近的父元素的高度和宽度

## dom解绑事件

1. attachEvent是IE有的方法，它不遵循W3C标准，而其他的主流浏览器如FF等遵循W3C标准的浏览器都使用addEventListener，所以实际开发中需分开处理。

2. 多次绑定后执行的顺序是不一样的，attachEvent是后绑定先执行，addEventListener是先绑定先执行。

### 为元素解绑事件
注意:用什么方式绑定事件,就应该用对应的方式解绑事件
* 1. 解绑事件
* 对象.on事件名字=事件处理函数--->绑定事件
* 对象.on事件名字=null;

```javascript
$("btn").onclick=function () {
console.log("我是绑定事件");
};
$("btn").onclick=null;
};
```

* 2. 解绑事件
* 对象.addEventListener("没有on的事件类型",命名函数,false);---绑定事件 
* 对象.removeEventListener("没有on的事件类型",函数名字,false);

```javascript
$("btn").addEventListener("click",f1,false);
$("btn").removeEventListener("click",f1,false);
```

* 3. 解绑事件(IE)
* 对象.attachEvent("on事件类型",命名函数);---绑定事件
* 对象.detachEvent("on事件类型",函数名字);
```javascript
$("btn").attachEvent("onclick",f2);
$("btn").detachEvent("onclick",f1);
```

## 位运算符
位运算符一般使用的很少，当他与数字一起使用的时候，比如~N => -(N+1),这个式子仅当 N == -1 的时候结果为0。位运算可以提高程序的运行效率。

我们可以在indexof(...)函数前面加一个~来进行布尔检查，检查一个项是否存在于Array或者string中。
```javascript
let userName = 'break jerry'

if(~userName.indexOf("break")) {
    console.log('access denied');
}else {
    console.log('access granted');
}

// 奇偶性
if(index & 1) {
    // 1 奇数 odd 
}else{
    // 0 偶数 even
}
```

## 关闭当前页面
- window.close()
- window.open("about:blank","_self").close();

## 字节节省写法
```javascript
//取整
parseInt(a,10); 
Math.floor(a); 
~~a; //节省之后的写法
a|0; //节省之后的写法 
a<<0

//四舍五入
Math.round(a); 
a+.5|0; //节省之后的写法

//内置值未定义
undefined; 
void 0; //节省之后的写法(快)
0[0]; //节省之后的写法(略慢)

//内置值 无穷
Infinity;


1/0; //节省之后的写法

//布尔值短写法
true; 
!0; //节省之后的写法
false; 
!1; //节省之后的写法

// 判断奇偶
num & 1 == 1 //奇数
num & 1 == 0 //偶数

num % 2 === 0 //偶数
// 递归判断
function isEven(num) {
  if (num < 0 || num === 1) return false;
  if (num == 0) return true;
  return isEven(num - 2);
}
```

## 简单计算页面FPS
整体思路是一秒有一千毫秒，先记录当前时间作为最后一次记录fps的时间，通过 requestAnimationFrame 回调不断给累加fsp计数器，
并且判断上次记录fps的时间是否达到1000毫秒以上，如果满足条件，就将fps累加器的值作为当前fps显示，并且重置fps累加器。

```javascript
var showFPS = (function () {
    // noinspection JSUnresolvedVariable, SpellCheckingInspection
    // 函数式
    var requestAnimationFrame =
        window.requestAnimationFrame || //Chromium  
        window.webkitRequestAnimationFrame || //Webkit 
        window.mozRequestAnimationFrame || //Mozilla Geko 
        window.oRequestAnimationFrame || //Opera Presto 
        window.msRequestAnimationFrame || //IE Trident? 
        function (callback) { //Fallback function 
            window.setTimeout(callback, 1000 / 60);
        };
    
    var dialog;
    var container;

    var fps = 0;
    var lastTime = Date.now(); // 时间戳
    // 遍历修改style
    function setStyle(el, styles) {
        for (var key in styles) {
            el.style[key] = styles[key];
    }
    }

function init() {
        dialog = document.createElement('dialog');
        // 初始化赋值样式
        setStyle(dialog, {
            display: 'block',
            border: 'none',
            backgroundColor: 'rgba(0, 0, 0, 0.6)',
            margin: 0,
            padding: '4px',
            position: 'fixed',
            top: 0,
            right: 'auto,',
            bottom: 'auto',
            left: 0,
            color: '#fff',
            fontSize: '12px',
            textAlign: 'center',
            borderRadius: '0 0 4px 0'
        });
        // container成了dom元素
        container.appendChild(dialog);
    }

    function calcFPS() {
        offset = Date.now() - lastTime;
        fps += 1;
    // 1s内的计数器
    if (offset >= 1000) {
            lastTime += offset; // Date.now();
            displayFPS(fps);
            fps = 0;
        }

        requestAnimationFrame(calcFPS);
    };

    function displayFPS(fps) {
        var fpsStr = fps + ' FPS';

        if (!dialog) {
            init();
        }

    if (fpsStr !== dialog.textContent) {
        // 重绘 
        dialog.textContent = fpsStr;
        }
}

return function (parent) {
    container = parent;
        calcFPS();
    };
})();
showFPS(document.body);
```

## 你不知道的new.target
new 是构造函数生成实例的命令, ES6为 new 命令引入了 new.target属性。这个属性用于确定构造函数是怎么调用的。
用法：在构造函数中, 如果一个构造函数不是通过 new操作符调用的, new.target会返回 undefined。

使用场景：
### es5写法
> 如果一个构造函数不通过 new 命令生成实例, 就报错提醒。
```javascript
function Shape(options) {
    if (this instanceof Shape) {
        this.options = options
    } else {
        // 要么手动给它创建一个实例并返回
        // return new Shape(options)
        
        // 要么提醒
        throw new Error('Shape 构造函数必须使用 new 操作符')
    }
}
```

###  es6写法
> 子类继承父类, 那么父类构造函数中的 new.target 是子类构造函数的名称。
```javascript
class Zoo {
    constructor() {
        if (new.target === Zoo) throw new Error('Zoo构造函数只能用于子类继承')
    }
}

const zoo = new Zoo()   // 报错

class Dog extends Zoo {
    constructor() {
        super()
    } 
}

const dog = new Dog()  // 不报错
```

## 文件扩展名
```javascript
var filePath = '21312321.mp3'
filePath.substring(filePath.lastIndexOf(".")+1,filePath.length) //'mp3'
```

## blur和click冲突
> 场景：平时做表单验证的时候一般都有个input框和删除按钮，然后习惯性在失去焦点的时候> 去验证输入的内容是否正确，做验证，发请求等等。
> 这个时候，那个点击删除按钮往往也就触发了input的失去焦点事件
1. 给失去焦点的时间加上延迟时间，让blur时间在click事件后执行，这个方法固然能够解决问题，但是本人并不是很推荐，因为影响性能，不到最后不用这个方法；
2. event.relatedTarget.id事件属性返回与事件的目标节点相关的节点。（非IE）
3. mousedown事件替代处理click事件

## object标签
object标签不在dom文档流里面，浏览器在解析的时候先把object放置在最上层，然后依次解析dom文档，放在下层。

如果这样引用多媒体文件推荐还是不使用object，只有iframe的层级在它之上处理起来很麻烦。

## escape、encodeURI、encodeURIComponent区别
1. escape是对字符串进行编码，对URL使用不需要 encodeURI encodeURIComponent
2. encodeURI方法不会对下列字符编码 ASCII字母、数字、!@#$&*()=:/,;?+'
3. encodeURIComponent方法不会对下列字符编码 ASCII字母、数字、!*()'
所以encodeURIComponent比encodeURI编码的范围更大。
实际例子来说，encodeURIComponent会把 http:// 编码成 http%3A%2F%2F 而encodeURI却不会。
当编码url时需要使用encodeURI，当需要编码参数时使用encodeURIComponent

## 链式调用
```javascript
1.方法体内返回对象实例自身(this)

var Obj = {
    a: 1,
    func: function(){
        this.a += 1;
        return this
    }
}
Obj.func().func();
console.log(Obj.a);    //3
2.对象传入后每次调用返回函数自身
    function show(str) {
        console.log(str);
        return show;
    }
    show(123)(456)(789);

// 控制台打印结果
// 123
// 456
// 789
```

## setTimeout 为不可执行的字符串时会造成内存泄漏
```javascript
setTimeout("fn()",100)
function fn(){
console.log(11111111)
}
```

## JSON.parse和JSON.stringify其他参数
```javascript
var obj ={name:'cosyer',age:15}
var newobj = JSON.stringify(obj)

JSON.parse(newobj,(key,value)=>{
console.log(1111111111111,key,value);
});
// name cosyer 
// age 15 
// "" {}

// 3个参数
// JSON.stringify(jsonObj,repalce,space)
// replace可以是数组或者回调函数
const testJSON = {
   name: 'test',
   cities: {
      shanghai: 1,
   },
};

JSON.stringify(testJSON, ['name']);

// "{"name": 'test'}"

JSON.stringify(testJSON, ['name', 'cities']);
 
//  "{"name": 'test', "cities": {}}"

JSON.stringify(testJSON, ['name', 'cities', 'shanghai']);

// "{"name": 'test', "cities": {"shanghai": 1}}"

JSON.stringify(testJSON, (key, value) => {
    // 遍历对象
    if (key === 'cities') {
       return  'cities';
    } 
    return value; // 确认value???
});

// "{"name": 'test', "cities": 'cities'}"

JSON.stringify(testJSON, undefined, '...');

// "{
//    ..."name": 'test',
//    ..."city": 'shanghai',
//   }"

JSON.stringify(testJSON, undefined, 7);

// "{
//          "name": 'test',
//          "city": 'shanghai',   // 缩进7个空格
// }"

JSON.stringify({name:123,age:24},null,'\t')
// "{
// 	"name": 123,
// 	"age": 21
// }"
```

## 随机数
```javascript
// [0,1) 左闭右开 min-max 
Math.floor(min+Math.random()*(max-min+1))
// toString() this is object方法 toString() valueOf
// 随机颜色
item.style.backgroundColor = '#' + Math.random().toString(16).slice(2, 8);
// "#" + ("00000" + ((Math.random() * 0x1000000) << 0).toString(16)).slice(-6)
```

## chrome浏览器跳转调试
问题描述：在chrome里调试接口的时候发现页面跳转之后会看不到之前的接口返回，即使你将Perserve log 勾上，能看到跳转之前的接口，但是看不到返回的内容

问题解决：查了一些资料，chrome之所以这么做（看不到跳转之前的接口返回）是为了节省内存开销，但是这个给调试带来了困难，然后我参照stackoverflow的解决方案， 在控制台执行 window.onunload = function() { debugger; } 为了在页面unload之前进入debug，但是尝试了还是看不到跳转之前接口返回的内容，最后的解决方案是用firefix调试，在火狐里在调试中的网络中勾选持续日志可以看到跳转之前的接口，且可以看到接口返回内容。

**参考资料** 
* [Chrome dev tools fails to show response ](https://stackoverflow.com/questions/38924798/chrome-dev-tools-fails-to-show-response-even-the-content-returned-has-header-con)
* [Chrome 开发者工具里看不到完整的 HTTP request 回应？](https://ephrain.net/chrome-chrome-%E9%96%8B%E7%99%BC%E8%80%85%E5%B7%A5%E5%85%B7%E8%A3%A1%E7%9C%8B%E4%B8%8D%E5%88%B0%E5%AE%8C%E6%95%B4%E7%9A%84-http-request-%E5%9B%9E%E6%87%89%EF%BC%9F/)

## 时间
**UTC**：世界协调时间，是经过平均太阳时(以格林威治时间GMT为准)、地轴运动修

正后的新时标以及以秒为单位的国际原子时所综合精算而成的时间。UTC比GMT更

加精准，其误差值必须保持在0.9秒以内。若大于0.9秒，则由位于巴黎的国际地

球自转事务中央局发布闰秒，使UTC与地球自转周期一致，所以基本上UTC的本质

强调的是比GMT更为精确的世界时间标准。

**GMT**：格林尼治是位于英国南郊的原格林尼治天文台所在地，也是地理经度的起

始点。这里所设定的时间就叫格林尼治时间，也是世界时间参考点，全世界都根

据时区的不同以格林尼治的时间作为标准来设定时间。

**夏令时**：因为夏天白天时间比较长，太阳会比较早出现，某些地区就将当地的时

间提早一段时间（例如一小时），以达到利用阳光节约能源的目的。 通常秋季

时，此地区会将当地时间推后一段时间（例如一小时），回复正常时间。

UTC和本地时间的关系：本地时间=UTC+时区+夏令时偏移量。

## uuid生成(Math.random)
```javascript
function uuid(len, radix) {
    var chars = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz'.split('');
    var uuid = [], i;
    var radix_new = radix || chars.length;

    if (len) {
        // Compact form
        for (i = 0; i < len; i++) {
            uuid[i] = chars[0 | Math.random() * radix_new];
        }
    } else {
        // rfc4122, version 4 form
        var r;

        // rfc4122 requires these characters
        uuid[8] = uuid[13] = uuid[18] = uuid[23] = '-';
        uuid[14] = '4';

        // Fill in random data. At i==19 set the high bits of clock sequence as
        // per rfc4122, sec. 4.1.5
        for (i = 0; i < 36; i++) {
            if (!uuid[i]) {
                r = 0 | Math.random() * 16;
                uuid[i] = chars[(i === 19) ? (r & 0x3) | 0x8 : r];
            }
        }
    }
    return uuid.join('');
};
```

```js
function generateDynamic32UUID() {
  return "xxxxxxxx-xxxx-xxxx-yxxx-xxxxxxxxxxxx"
    .replace(/[xy]/g, function(c) {
      let r = (Math.random() * 16) | 0;
      let v = c == "x" ? r : (r & 0x3) | 0x8;
      return v.toString(16);
    })
    .toUpperCase(); // "8ED4E1FA-DA4B-5B40-B5AF-05AC386B2753"
  //   return ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>
  //     (
  //       c ^
  //       (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (c / 4)))
  //     ).toString(16)
  //   );
}
```

```js
// hash name
function getHashName(prefix) {
    var number = randomNum(1, 9);
    var hash = parseInt(((new Date().getTime()) % 3839 + 256), 10).toString(16);
    hash = prefix + '-' + hash + number;
    return hash;
}

function randomNum(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

## UUID(createObjectURL)
```js
function uuid() {
  var temp_url = URL.createObjectURL(new Blob());
  var uuid = temp_url.toString(); // blob:https://xxx.com/b250d159-e1b6-4a87-9002-885d90033be3
  URL.revokeObjectURL(temp_url);
  return uuid.substr(uuid.lastIndexOf("/") + 1);
}
```

## jq原生对照
```javascript
1、元素获取
/*******************原生js**************************/
var ele = document.getElementById('idName');
var eleArr = document.getElementsByClassName('className');
var eleArr = document.getElementsByTagName('div');
var ele = document.querySelector('#idName');//('.className')//('tagName'); //取第一个满足条件的元素
var eleArr = document.querySelectorAll('.className');//('tagName');
/*******************jQuery**************************/
var ele = $('#idName');
var ele = $('.className:eq(0)'); //取第一个元素
var eleArr = $('tagName');
2、class选择器操作
/*******************原生js**************************/
//className 属性
ele.className = 'newClassName';
//classList操作
ele.classList.contains('className'); //是否含有该类
ele.classList.add('newClassName1,newClassName2...'); //添加类
ele.classList.remove('oldClassName1,newClassName2...'); //删除类
ele.classList.toggle('className'); //如果元素中有该className，则删除并返回false，否则添加并返回true

/*******************jQuery**************************/
ele.hasClass(className);
ele.addClass('newClassName1,newClassName2...');
ele.removeClass('newClassName1,newClassName2...'); // 元素本身remove()
ele.toggleClass('className');
3、元素节点操作
/*******************原生js**************************/
//创建节点
    var newNode = ele.createElement('<div>创建</div>');
//插入节点
    ele.appendChild(newNode); //在ele的子节点的末尾插入newNode
    ele.insertBefore(newNode,targetNode); //在ele的子节点targetNode前面插入newNode
//删除节点
    ele.removeChild(ele.childNodes[i]); //删除ele第i+1个子节点
//替换节点
    ele.replaceChild(newNode, oldNode); //替换ele中的子节点
//克隆节点
    var copyNode = ele.cloneNode(deep); //deep为true时，深拷贝（包括其子孙节点），为false时只复制本身节点

/*******************jQuery**************************/
//创建节点
    var newNode = $('<div>创建</div>');
//插入节点
    //在元素子节点末尾插入
    ele.append(newNode); //在ele的子节点的末尾插入newNode
    newNode.appendTo(ele); //在ele的子节点的末尾插入newNode
    //在元素子节点前端插入
    ele.prepend(newNode); //在ele的子节点的前端插入newNode
    newNode.prependTo(ele); //在ele的子节点的前端插入newNode

    //在元素后面添加
    ele.after(newNode);
    newNode.insertAfter(ele);
    //在元素前面添加
    ele.before(newNode);
    newNode.insertBefore(ele);

//删除节点
    ele.remove(); //移除ele及其所有文本、子孙节点、数据和事件
    ele.detach(); //移除ele及其所有文本、子孙节点，但是保留数据和事件
    ele.empty(); //清除ele所有的内容和子孙元素，但是ele节点本身和其属性事件等还在

//替换节点
    ele.replaceWith(newNode); //替换ele为新的内容(可以是html元素，dom元素，jQuery元素)
    newNode.replaceAll(ele); //替换ele为新的html元素
//克隆节点
    ele.clone(deep); //deep为true时，深拷贝（包括其事件处理函数），为false时只复制本身，默认false
4、元素节点遍历
/*******************原生js**************************/
// 子节点
var eleArr = ele.childNodes; // 所有子节点
var eleArr = ele.children; //所有子节点数组，用得较多

var firstEle = ele.firstChild; //第一个子节点 低版本浏览器firstElementChild
var lastEle = ele.lastChild; //最后一个子节点 低版本浏览器lastElementChild
// 父节点
var parentEle = ele.parentNode;
// 兄弟节点
var nextEle = ele.nextSibling; //下一个节点
var previousEle = ele.previousSibling; //上一个节点

/*******************jQuery**************************/
// 子节点
var eleArr = ele.children(); //所有子节点数组

var firstEle = ele.first(); //第一个子节点
var lastEle = ele.last(); //最后一个子节点
// 父节点
var parentEle = ele.parent(); //直接父元素
var parentEleArr = ele.parents(); //所有祖先元素
var parentEle = ele.offsetParent(); // 第一个有定位的父元素
var parentEleArr = ele.parentsUntil(stop, filter); // 满足条件之间的父节点

// 兄弟节点
var nextEle = ele.next(); //下一个节点
var nextEleArr = ele.nextAll(); //ele后面所有同级节点
var previousEle = ele.prev(); //上一个节点
var previousEle = ele.prevAll(); //ele之前所有同级节点
var siblingsArr = ele.siblings(); //所有同级元素节点
5、属性操作
/*******************原生js**************************/
// 属性数组
var attrArr = ele.attributes;
// 判断属性
var bool = ele.hasAttribute('attrName'); //是否有指定属性
var bool = ele.hasAttributes(); //是否有属性
// 获取属性值
var attrValue = ele.getAttribute('attrName');
// 设置属性值
ele.setAttribute('attrName','attrValue');
// 删除属性
ele.removeAttribute('attrName');
/*******************jQuery**************************/
//在设置disabled、selected、checked等这些Boolean类型自带属性时，我们需要用prop()方法；
// 其他字符串类型自带属性时，我们使用attr()方法即可;DOM节点可见的自定义属性我们也使用attr()方法。
// 获取属性值
var attrValue = ele.attr('attrName');
var propValue = ele.prop('propName');//
// 设置属性值
ele.attr('attrName','attrValue'); //自定义的一些属性
ele.prop('propName','propValue'); //disabled等一些属性，HTML元素的固有属性
// 删除属性
ele.removeAttr('attrName');
6、事件
/*******************原生js**************************/
//事件绑定及解绑
ele.addEventListener('click',func,false);
ele.removeEventListener('click',func,false);
ele.onclick = func;
ele.onclick = null;
ele.attachEvent('onclick',func); //ie8以下
ele.detachEvent('onclick',func); //ie8以下
/*******************jQuery**************************/
/*多种事件绑定和解绑方法*/
/**方法1
 * 1、直接绑定事件方法mouseenter()...等等
 * 2、这样的方式，下面两个事件不会被层叠，都会执行
 * 3、只能一个一个绑定
 */
$("div:eq(0)").click(function () {
    alert(1);
});
$("div:eq(0)").click(function () {
    alert(2);
});

/**方法2
 * 1、bind("event1 event2 ... eventx",fn)
 * 2、同时绑定多个事件触发条件，执行同一个函数
 */
$("div:eq(1)").bind("click mouseenter",function () {
    alert("bind绑定法");
});
//解绑
$("div:eq(1)").unbind("click"); //解绑指定的
//$("div:eq(1)").unbind();  //解绑所有的

/**方法3
 * 1、delegate("selector","event1 event2 ... eventx",fn)
 * 2、可以绑定父盒子里的子盒子触发事件，执行函数
 */
$("div:eq(2)").delegate("button","click mouseleave",function () {
    alert("delegate绑定法");
});
//解绑
$("div:eq(2)").undelegate();
/**方法4
 * 重点使用！！！
 * 1、on("event1 event2 ... eventx","selector",data,fn)
 * 2、可以绑定父盒子里的后代盒子触发事件，执行函数
 * 3、参数data由event.data带入函数中
 */

$("div:eq(3)").on("click mouseleave","button",{"name":"mjm","age":24},function (event) {
    alert("on绑定法"+event.data.name);

});
//解绑
$("div:eq(3)").off();
```

## 实现复制tiny
```javascript
// 复制连接地址
window.copyLink = function (str) {
    if (window.clipboardData) {
        // 兼容ie11以下浏览器
        window.clipboardData.setData('Text', str);
    } else {
        var $input = document.createElement('input');
        var body = document.querySelector("body")
        $input.value=str;
        body.appendChild($input);
        $input.select();
        document.execCommand('copy');
        // $input.remove();
        body.remove($input)
    }
};
```

## scrollbar滚动条样式优化
```css
::-webkit-scrollbar-corner {
  background-color: transparent;
}

::-webkit-scrollbar-button {
  width: 0;
  height: 0;
  display: none;
}

::-webkit-scrollbar-thumb {
  width: 7px;
  background-color: #b4babf;
  border-radius: 7px;
}

::-webkit-scrollbar {
  width: 7px;
  height: 7px;
}

::-webkit-scrollbar-track {
  width: 15px;
}

::-webkit-scrollbar:hover {
  background-color: transparent;
}
```

##  内网ip段
内网段10.0.0.0—10.255.255.255，172.16.0.0—172.31.255.255，192.168.0.0—192.168.255.255

## 获取当前鼠标坐标
```javascript
var getCoordInDocumentExample = function () {
  var coords = document.getElementById("colorPanel");
  coords.onmousemove = function (e) {
    var pointer = getCoordInDocument(e);
    var coord = document.getElementById("colorText");
    coord.innerHTML = "X,Y=(" + pointer.x + ", " + pointer.y + ")";
  }
}

var getCoordInDocument = function (e) {
  e = e || window.event;
  var x = e.pageX || (e.clientX +
    (document.documentElement.scrollLeft ||
      document.body.scrollLeft));
  var y = e.pageY || (e.clientY +
    (document.documentElement.scrollTop ||
      document.body.scrollTop));
  return {
    'x': x,
    'y': y
  };
}

window.onload = function () {
  getCoordInDocumentExample();
};
```

## 类数组对象转数组
```javascript
function func(){
    // 类数组对象转数组
    console.log(arguments) // {0:1,1:2,2:3,length:3}
    console.log([].slice.call(arguments))
    console.log(Array.prototype.slice.call(arguments))
    console.log(Array.from(arguments))
    console.log([...arguments])

    console.log(Array.from({length:0})); //[]
    console.log(Array.from('')); //[]
}
func(1,2,3)
```

## 页面滚动条到底判断
```javascript
//获取网页的总高度，主要是考虑兼容性所以把Ie支持的documentElement也写了，这个方法至少支持IE8
var htmlHeight = document.body.scrollHeight || document.documentElement.scrollHeight;
//clientHeight是网页在浏览器中的可视高度，
var clientHeight = document.body.clientHeight || document.documentElement.clientHeight;
//scrollTop是浏览器滚动条的top位置，
var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
//通过判断滚动条的top位置与可视网页之和与整个网页的高度是否相等来决定是否加载内容；
if (scrollTop + clientHeight == htmlHeight) {
    // 滚动条到底
}
// 没有垂直滚动条的情况下，scrollHeight值与元素视图填充所有内容所需要的最小值clientHeight相同
```

```
网页可见区域宽： document.body.clientWidth;
网页可见区域高： document.body.clientHeight;
网页可见区域宽： document.body.offsetWidth (包括边线的宽);
网页可见区域高： document.body.offsetHeight (包括边线的宽);
网页正文全文宽： document.body.scrollWidth;
网页正文全文高： document.body.scrollHeight;
网页被卷去的高： document.body.scrollTop;
网页被卷去的左： document.body.scrollLeft;
网页正文部分上： window.screenTop;
网页正文部分左： window.screenLeft;
屏幕分辨率的高： window.screen.height;
屏幕分辨率的宽： window.screen.width;
屏幕可用工作区高度： window.screen.availHeight;
```

## yield函数
```javascript
// 传入匿名函数
function* fibonacci() {
    let [prev, curr] = [0, 1];
    while (true) {
    [prev, curr] = [curr, prev + curr];
    yield curr;
    }
}
// 含有迭代器iterate
for (let n of fibonacci()) {
    if (n >= 1000) {
    break;
    }
    console.log(n);
}
// 1 2 3 5 8 
```

### 复制添加版权申明
```javascript
if (window.clipboardData) {
    // IE
    document.body.oncopy = function(){
        event.returnValue = false;
        var t=document.selection.createRange().text;
        var s=" 原文链接："+location.href;
        clipboardData.setData('Text',t+'\r\n'+s);
    };
} else {
    function addLink(){
        var body_element = document.getElementsByTagName('body')[0];
        var selection;
        selection = window.getSelection();
        var pagelink = " 原文链接："+location.href;
        var copytext = selection + pagelink;

        var newdiv = document.createElement('div');
        newdiv.style.position='absolute';
        newdiv.style.left='-99999px';
        body_element.appendChild(newdiv);
        newdiv.innerHTML = copytext;
        selection.selectAllChildren(newdiv);
        window.setTimeout(function(){body_element.removeChild(newdiv);},0);
    }
    document.oncopy = addLink;
}
```

## 包版本号
一个包的版本号基本由三位数字构成 x.x.x, 它们分别是主版本号, 次版本号, 修订号。

- *: 升级主版本号 + 次版本号 + 修订号;
- ^: 升级次版本号 + 修订号;
- ~: 升级修订号;

npm使用 a.b.c 的版本号来管理安装包，a大达版本号，有重大api改变，一般不向下兼容，b为小版本号，新增功能，向下兼容，c为补丁号，通常修复一些bug。

## script标签关键字
- defer 并发下载 顺序执行 渲染完再执行(延迟执行)
- async 异步下载 异步执行谁先下载完 谁先执行

## 防止对象篡改
* Object.preventExtensions(obj): obj 不能添加属性
* Object.seal(obj): obj 不能添加/删除属性
* Object.freeze(obj): obj 不能添加/删除/修改属性

Object.freeze工作方式与Object.preventExtensions相同，并且它使所有对象的属性不可写且不可配置。 唯一的缺点是`Object.freeze`仅适用于对象的第一级：嵌套对象不受操作的影响。

## module.exports和exports
module.exports才是真正的接口，exports只不过是它的一个辅助工具。　最终返回给调用的是module.exports而不是exports。
所有的exports收集到的属性和方法，都赋值给了module.exports。当然，这有个前提，就是module.exports本身不具备任何属性和方法。

1. 最好分别定义module.exports和exports

2. 建议导出对象用module.exports,导出多个方法和变量用exports

## meta自动跳转
```javascript
<meta http-equiv="Refresh" content="1; url=http://www.baidu.com"/>
```

## 前端生成文件并下载
Blob对象简要介绍

Blob 对象表示一个不可变、原始数据的类文件对象。Blob 表示的不一定是JavaScript原生格式的数据。File 接口基于Blob，继承了 Blob 的功能并将其扩展使其支持用户系统上的文件。生成Blob对象有两种方法：一种是使用Blob构造函数，另一种是对现有的Blob对象使用slice方法切出一部分。
> const aBlob = new Blob( array, options );
参数说明

- array 是一个由ArrayBuffer, ArrayBufferView, Blob, DOMString 等对象构成的 Array ，或者其他类似对象的混合体，它将会被放进 Blob。DOMStrings会被编码为UTF-8。
- options 是一个可选的BlobPropertyBag字典，它可能会指定如下两个属性：
type，默认值为 “”，它代表了将会被放入到blob中的数组内容的MIME类型。
endings，默认值为”transparent”，用于指定包含行结束符\n的字符串如何被写入。 它是以下两个值中的一个： “native”，代表行结束符会被更改为适合宿主操作系统文件系统的换行符，或者 “transparent”，代表会保持blob中保存的结束符不变

```javascript
// const debug = {hello: "world"};
// const blob = new Blob([JSON.stringify(debug, null, 2)],{type : 'application/json'});
function createAndDownloadFile(fileName, filePath) {
    const aTag = document.createElement('a');
    const blob = new Blob([filePath]);
    aTag.download = `${fileName}.json`;
    aTag.style.display = "none";
    aTag.href = URL.createObjectURL(blob);
    document.body.appendChild(aTag);
    aTag.click();
    document.body.removeChild(aTag)
    URL.revokeObjectURL(blob);
}
```

## 防止frame嵌套
```javascript
if(top != self){
    location.href = ”about:blank”;
}
```

## 取反运算简化indexOf判断
```javascript
if(~[1,2,3].indexOf(1)){
    // 存在
}else{
    // 不存在
}
[(1,2,3)] // 3
```

## 已知年月，求当月多少天
- 先判断该年份是否是闰年，来处理 2 月份情况，闰年 2 月共 29 天，非闰年 2 月共 28 天
- 再判断其他月份，如 1 月共 31 天，4 月共 30 天

**更简便的方法**
```javascript
// Date API 处理日期溢出时，会自动往后推延响应时间
function getMonthCountDay (year, month) {
  return 32 - new Date(year, month-1, 32).getDate();
  // 32 - (32-当月天数) = 当月天数
}
// better
function getMonthCountDay (year, month) {
  return new Date(year, month , 0).getDate();
}
```

## 日期格式转化 2019/3/19 => 2019-3-19
1. 正则
```js
let date = new Date()
let dateStr = date.toLocaleDateString().replace(/\//g,'-')
```

2. 数组计算
```js
let date = new Date()
let arr = date.toLocaleDateString().split('/')
arr = arr.map((item) => {
return parseInt(item) < 10 ? '0' + item : item
})
let dateStr = arr.join('-')
```

## 求数值的n次幂
```js
Math.pow(2,10); //1024
2**10 // 1024 es7
2<<9 //1024
```

## 分号的重要性
```js
const luke = {}
const leia = {}
[luke, leia].forEach(jedi => jedi.father = 'vader');

function foo() {
  return
    'search your feelings, you know it to be foo'
} // undefined
```

## 前端h5 download属性下载文件(appendChild兼容firefox)
```js
function downFile(content, filename) {
  // 创建隐藏的可下载链接
  var eleLink = document.createElement('a');
  // eleLink.setAttribute("href", "data:text/plain;charset=utf-8," + str);
  // eleLink.setAttribute("download", +new Date() + ".txt");
  eleLink.download = filename;
  eleLink.style.display = 'none';
  // 字符内容转变成blob地址
  var blob = new Blob([content]);
  eleLink.href = URL.createObjectURL(blob);
  // 触发点击
  document.body.appendChild(eleLink);
  eleLink.click();
  // 然后移除
  document.body.removeChild(eleLink);
};
```
```js
// 模拟点击 a 链接
const triggerClick = (node: HTMLElement) => {
  try {
    if (document.createEvent) {
      const evt = document.createEvent('MouseEvents')
      evt.initEvent('click', true, false)
      node.dispatchEvent(evt)
    } else if ((document as any).createEventObject) {
      (node as any).fireEvent('onclick')
    }
  } catch (e) {
    node.click()
  } 
}
// 多url批量下载
const download = (url) => {
  var iframe = document.createElement('iframe') //  先创建一个iframe 标签
  iframe.style.display = 'none' // 不能在页面中被看到
  iframe.style.height = '0px'  // 给个0的高度
  iframe.src = url  // 关联上下载地址
  document.body.appendChild(iframe) // 绑定在body上才能发挥作用
  setTimeout(() => { 
    iframe.remove() // iframe 没有onload事件，只能放在setTimeout里清除了，时间稍微大一点，免得zip包太大还没有下载完。
  }, 5000)
}
```

## 禁止滚动条滚动
```js
var keys = [37, 38, 39, 40];

function preventDefault(e) {
  e = e || window.event;
  if (e.preventDefault)
      e.preventDefault();
  e.returnValue = false;  
}

function keydown(e) {
    for (var i = keys.length; i--;) {
        if (e.keyCode === keys[i]) {
            preventDefault(e);
            return;
        }
    }
}

function wheel(e) {
  preventDefault(e);
}

function disable_scroll() {
  if (window.addEventListener) {
      window.addEventListener('DOMMouseScroll', wheel, false);
  }
  window.onmousewheel = document.onmousewheel = wheel;
  document.onkeydown = keydown;
}

function enable_scroll() {
    if (window.removeEventListener) {
        window.removeEventListener('DOMMouseScroll', wheel, false);
    }
    window.onmousewheel = document.onmousewheel = document.onkeydown = null;  
}
```

## map等遍历注意事项
```js
var it = []
it.map((item)=>{
    console.log(111111111111)
})
// 压根就不会执行
```

## 符合密码学要求的随机值
```js
function getRandomNumbers(min, max) {
    var minValue = min || '';
    var maxValue = max || '';
    var cryptoObj = window.crypto || window.msCrypto;
    var array = new Uint32Array(1);
    cryptoObj && cryptoObj.getRandomValues(array);
    var result = 0;
    if (minValue || maxValue) {
        if (!maxValue) {
            minValue = 0;
            maxValue = min;
        }
        result = parseInt(minValue, 10) + (array[0] % (parseInt(maxValue, 10) - parseInt(minValue, 10)));
    } else {
        result = array[0];
    }
    return result;
}
```

## 字符串反转
如果需要支持UTF-16或其他多字节字符的解决方案，请注意此函数将给出无效的Unicode字符串或看起来很有趣的有效字符串。[需要注意](https://stackoverflow.com/questions/958908/how-do-you-reverse-a-string-in-place-in-javascript/16776621#16776621)

![有意思的图](http://cdn.mydearest.cn/blog/images/reverse.jpg)

```js
function reverse(str) {
    return str.split('').reverse().join('');
    // Array.from(str).reverse().join('')
}

// 使用递归
function reverseString(str) {
  return (str === '') ? '' : reverseString(str.substr(1)) + str.charAt(0);
}

function reverse([h, ...t]) {
    return h ? reverse(t) + h : '';
}

function reverse(str){
    var s ='';
    for (let i = str.length;i > 0; i--) {
        s += str[i-1]; // charAt substring substr
    }
    return s;
}
```

## delete varible
```js
delete xxx // true
delete window //false
```

## jquery指定元素滚动到视图中间
```js
function scrollToViewCenter($element) {
    if (!$element) {
        return;
    }
    var winOffsetHeight = document.body.offsetHeight; // 浏览器窗口可视区域高度
    var eleOffsetTop = $element.offset().top;
    var top = eleOffsetTop - (winOffsetHeight / 2);
    window.scrollTo(0, top);
}
```

## new Array()
```js
var myArray = Array();
myArray['A'] = "Athens";
myArray['B'] = "Berlin";
myArray['0'] = 1;
// length 1 只统计数字索引
// length范围 0~2**32-1
```

## 构造函数scope-safe模式
```js
function Book(name){
    console.log(1111,this)
    if(!(this instanceof Book)){
        return new Book(name);
    }
    this.name=name;
}
var bookone = Book('harry-potter');

function a(b){
  alert(b);
  function b(){
    alert(b);
  }
  b();
}
a();
```

## jquery设置!important
```js
$("#container").css("top");
$("#container").css("top","15px");
$("#container").css("cssText","overflow:auto !important;")
```

## :empty 选择器区分样式
> :empty 选择器匹配没有子元素（包括文本节点）的每个元素。

这里举个小红点的例子：
![empty](http://cdn.mydearest.cn/blog/images/empty.png)

如图所示，小红点有内容以及无内容的样式差异，按照常规的处理方式，我们一般是通过类名区分，但是我们可以简单通过:empty选择器区分开。
```html
<div class="jd"><i>3</i></div>
```

```css
.jd i:empty {
    // 无内容的小红点样式
}
.jd i:not(:empty) {
    // 有内容的小红点样式
}
```

## arr.reduce(callback[, initialValue])
- reduce接受两个参数, 一个回调, 一个初始值
- 回调函数接受四个参数 previousValue, currentValue, currentIndex, array

> 第一个表达式等价于 Math.pow(3, 2) => 9; Math.pow(9, 1) =>9

> 第二个表达式异常 Uncaught TypeError: Reduce of empty array with no initial value

## 变量提升
在 JavaScript中， functions 和 variables 会被提升。变量提升是JavaScript将声明移至作用域 scope (全局域或者当前函数作用域) 顶部的行为。
```js
var name = 'World!';
(function () {
    if (typeof name === 'undefined') {
        var name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
// Goodbye Jack
```

## 最大安全数 + 1值没有变化 会导致循环
```js
var END = Math.pow(2, 53);
var START = END - 100;
var count = 0;
for (var i = START; i <= END; i++) {
    count++;
}
console.log(count); // 循环
```

## 稀疏数组
没有内容的数组, array 上的操作会跳过这些未初始化的’坑’.
```js
var ary = [0,1,2];
ary[10] = 10;
ary.filter(function(x) { return x === undefined;});
// []

var ary = Array(3);
ary[0]=2
ary.map(function(elem) { return '1'; });
// ["1", undefined × 2]
```

## 一个鲜为人知的实事: Array.prototype => []
```js	
Array.isArray( Array.prototype )
// true
```

## arguments在es6有初始值时不同
```js
function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
// 10 + 1 + 10 = 21

function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c=3) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
// 1 + 1 + 10 = 12
```

## reverse返回调用者
```js
var x = [].reverse;
x();
// window
```

## Number.MIN_VALUE > 0
```js
Number.MIN_VALUE > 0
// 5e-324 true
```

## 抽象相等
```js
var a = [0];
if ([0]) {
  console.log(a == true);
} else {
  console.log("wut");
}
// false
```
![equality](http://cdn.mydearest.cn/blog/images/equality.png)

## 隐式类型转换
```js
-、*、/、% ：一律转换成数值后计算
+：

数字 + 字符串 = 字符串， 运算顺序是从左到右
数字 + 对象， 优先调用对象的valueOf -> toString
数字 + boolean/null -> 数字
数字 + undefined -> NaN

[1].toString() === '1'
{}.toString() === '[object object]'
NaN !== NaN 、+undefined 为 NaN
```

```js
[1 < 2 < 3, 3 < 2 < 1]

// 1 < 2 => true < 3 => 1 < 3 true
// 3 < 2 => false < 1 => 0 < 1 true
// [true,true]

2 == [[[2]]] // true
```

## number + .
```js
3.toString() // error (3).toString()
3..toString() // '3'
3...toString() // error
```

## automatic global
```js
(function(){
  var x = y = 1;
})();
console.log(y); // 1
console.log(x); // x is not defined
```

## regexp expression
```js
var a = /123/,
    b = /123/;
a == b
a === b
// false false
```

## function name is readonly
```js
function foo() { }
var oldName = foo.name;
foo.name = "bar";
[oldName, foo.name]
// 'foo' 'foo'
```

## parseInt 坑
```js
"1 2 3".replace(/\d/g, parseInt)
// [1, 0], [2, 2], [3, 4]
// "1 NaN 3"

parseInt(3, 8) // 3
parseInt(3, 2) // NaN
parseInt(3, 0) // 3
```

## function prototype
```js
function f() {}
var parent = Object.getPrototypeOf(f);
f.name // f
parent.name // empty
typeof eval(f.name) // function
typeof eval(parent.name) //  error
```

## regexp translate
```js
var lowerCaseOnly =  /^[a-z]+$/;
[lowerCaseOnly.test(null), lowerCaseOnly.test()]
// 转化成'null' 'undefined'
// true true
```

## [,,,].join(", ")
```js
[,,,].join(", ")
// ", , "
```

## function length
```js
var a = Function.length,
    b = new Function().length
a === b
// 1 === 0 false
```

## Date equal
```js
var a = Date(0);
var b = new Date(0);
var c = new Date();
[a === b, b === c, a === c]
// false false false
```

## function scope param
```js
function foo(a) {
    var a;
    return a;
}
function bar(a) {
    var a = 'bye';
    return a;
}
[foo('hello'), bar('hello')]
// ["hello", "bye"]
```

## nodejs Event模块的简单实现
```js
function Event() {
    this.on = function (eventName, callback) {
        if (!this.handlers) {
            this.handlers = {};
        }
        if (!this.handlers[eventName]){
            this.handlers[eventName] = [];
        }
        this.handlers[eventName].push(callback);
    }

    this.emit = function (eventName, data) {
        if (this.handlers[eventName]) {
            this.handlers[eventName].forEach((item)=>{
                item(data);
            });
        }
    }
    return this;
}
```

## 实现超链接显示A网站实际跳转B网站
```html
<a href="//www.tmall.com" onclick="host='jd.com'">tmall</a>
```

## nodejs 恶作剧：给系统创建一个叫 node_modules 的用户，然后 npm install 就无法使用了

## 最简单的方式实现`console.log(1)`返回0
```js
console = Math;
console.log(1); // 0
```

## 如何将整个网站页面变成黑白色?(灰阶)
对于一些需要悼念的日子，很多人也都想把自己的网站弄成全站黑白来表示自己的哀悼之情。还有其他特殊原因（比如公祭日）对部分事件表示哀悼，所以需要把整个网站设置为灰色或者黑白方格。

```css
html {
-webkit-filter: grayscale(100%);
-moz-filter: grayscale(100%);
-ms-filter: grayscale(100%);
-o-filter: grayscale(100%);
filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);
_filter:none;
}
```
filter 是滤镜的意思，filter:gray 的意思就是说给页面加上一个灰度的滤镜，所以 html 里面的所有内容都会变成黑白的了。
![黑白色](http://cdn.mydearest.cn/blog/images/grayFilter.png)


## 文字模糊
```css
color: transparent;
text-shadow: #111 0 0 5px;
```

## 毛玻璃
```css
.blur {
    display: block;
    width: 300px;
    height: 300px;
    margin: 100px auto;
    filter: blur(10px);
}
```

## 多重边框
```css
div {
    box-shadow: 0 0 0 6px rgba(0, 0, 0, 0.2), 0 0 0 12px rgba(0, 0, 0, 0.2), 0 0 0 18px rgba(0, 0, 0, 0.2), 0 0 0 24px rgba(0, 0, 0, 0.2);
    height: 200px;
    margin: 50px auto;
    width: 400px
}
```

## 黑幕效果
```css
.heimu, .heimu a, a .heimu, .heimu a.new {
    background-color: #252525;
    color: #252525;
    text-shadow: none;
}
.heimu:hover, .heimu:active,
.heimu:hover .heimu, .heimu:active .heimu {
    color: white !important;
}
.heimu:hover a, a:hover .heimu,
.heimu:active a, a:active .heimu {
    color: lightblue !important;
}
.heimu:hover .new, .heimu .new:hover, .new:hover .heimu,
.heimu:active .new, .heimu .new:active, .new:active .heimu {
    color: #BA0000 !important;
}
```
<span class="heimu">太对了哥，哥太对</span>

## 字符串和base64相互转换(编码、解码)
```js
// 字符串转base64
function encode(str){
    // 对字符串进行编码
    var encode = encodeURI(str);
    // 对编码的字符串转化base64
    var base64 = btoa(encode);
    return base64;
}
 
// base64转字符串
function decode(base64){
    // 对base64转编码
    var decode = atob(base64);
    // 编码转字符串
    var str = decodeURI(decode);
    return str;
}
```

```shell
echo test|base64 #加密
echo dGVzdAo= |base64 -d #解密
```

## 随机名称
```js
function getRandomName() {
    let number = getRandomNum(1, 9);
    let hash = parseInt(
        ((new Date().getTime() % 3839) + 256).toString(),
        10
    ).toString(16);
    let randomName = 'prefix' + hash + number;
    return randomName;
}

function getRandomNum(max, min) {
    var range = max - min;
    var rand = Math.random();
    return min + Math.round(rand * range);
}
```

## IDEA连接Github时出现：Failed to connect to github.com port 443: Connection refused的解决方法
本地hosts文件中配置不正确导致的。

```
git config --global http.proxy http://127.0.0.1:1080

git config --global https.proxy http://127.0.0.1:1080
```

但创建完仓库后 push 不上去，这个时候就需要取消代理
```
git config --global --unset http.proxy

git config --global --unset https.proxy
```

## counter 

[FlagCounter](https://flagcounter.com/)

[不蒜子访问统计](http://busuanzi.ibruce.info)

```html
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<span id="busuanzi_container_site_pv">
    总访问次数:<span id="busuanzi_value_site_pv"></span>
</span>

<span id="busuanzi_container_site_uv">
  访问次数:<span id="busuanzi_value_site_uv"></span>
</span>
```

## i18n语料替换
```js
function i18nReplace(s, o) {
  if (!s || !o) {
    return;
  }
  return s.replace
    ? s.replace(/\{\s*([^\|\}]+?)\s*(?:\|([^\}]*))?\s*\}/g, function(
        match,
        key
      ) {
        return o[key] !== undefined ? o[key] : match;
      })
    : s;
}

i18nReplace('123{0}开始了',{0:'奇怪'})
// "123奇怪开始了"
```

## 一行代码实现简单模版引擎
```js
function template(tpl, data) {
  return tpl.replace(/{{(.*?)}}/g, (match, key) => data[key.trim()]);
}

// 使用：
template('我是{{name}}，年龄{{age}}，性别{{sex}}', {name: '陈宇', age: 25, sex: '男'}); 
// "我是陈宇，年龄25，性别男"
```

## 快速生成包含26个字母的数组
```js
Array.from({length: 26}, (_, i) => String.fromCharCode(65 + i));
```

## 单行文本的省略号
```css
.single-ellipsis{
  width: 500px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
```

## 多行元素的文本省略号
```css
.multiline-ellipsis {
  display: -webkit-box;
  word-break: break-all;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 4; // 需要显示的行数
  overflow: hidden;
  text-overflow: ellipsis;
}
```

## 如何实现sleep效果
### while
```js
function sleep(ms) {
    var start = Date.now(), expires = start + ms;
    while(Date.now()<expires);
    console.log('醒了');
}

sleep(2000);
```

### promise
```js
function sleep(ms) {
    return new Promise((resolve,reject)=>{
        setTimeout(resolve,ms);
    });
}

sleep(2000).then(()=>{
    console.log('醒了');
});
```

### generate
```js
function* sleep(ms){
   yield new Promise(function(resolve,reject){
             setTimeout(resolve,ms);
        })  
}
sleep(500).next().value.then(function(){console.log(111)})
```

### async/await
```js
function sleep(ms){
  return new Promise((resolve)=>setTimeout(resolve,ms));
}
async function test(){
  var temple=await sleep(1000);
  console.log(1111)
  return temple
}
test();
//延迟1000ms输出了1111
```

## 实现一些主流框架的循环渲染
### 问题
```js
var items = [
  { name: 'item1' },
  { name: 'item2' }
];
var str = '<div ali-for="item in items">{{item.name}}<div>';

// 对应生成的dom  
ParseDom(str);
// <div>item1</div>  
// <div>item2</div>  
```

```js
var items = [
  { name: 'item1' },
  { name: 'item2' }
];

// ***********
const s = {}
s.items = items
function ParseDom(str) {
	// 借助dom子节点使用dom方法
	const mid = document.createElement('div')
	mid.innerHTML = str
	const { children } = mid
	let res = ''
	// 遍历子节点
	;[...children].forEach(c => {
		// 找属性节点
		const attrs = [...c.attributes]
		const targetAttr = attrs.find(x => x.name === 'ali-for');
		const nodename = c.nodeName.toLocaleLowerCase();
		// 属性全部写进去
		const attrsStr = attrs.reduce((r, c) => {
			if (c.name !== 'ali-for') {
				r += ` ${c.name}="${c.value}"`
			}
			return r
		}, '')
		if (!targetAttr) {
			// 没有循环渲染标记
			res += `<${nodename}${attrsStr}>${c.innerHTML}</${nodename}>`
			return
		}
		// 循环渲染
		const vfor = targetAttr.nodeValue
		const o = vfor.split(' in ')[1]
		const k = c.innerText.match(/\{\{(.*)\}\}/)[1].split('.')[1]
		;s[o].forEach(x => {
			res += `<${nodename}${attrsStr}>${x[k]}</${nodename}>`
		})
	})
	return res
}
// ***********

var str = '<div ali-for="item in items">{{item.name}}</div>';
// 对应生成的dom  
ParseDom(str);
```

## 思考：如何实现 npm install ？
- 假如老板给你 一个 npm 包的列表 ，要你找出这些 npm 包的所有依赖包名，简述一下实现思路

- 对包和包的依赖列表进行缓存 -> A 和 B 都依赖了 C，从 A 分析一次后，B 还需要再分析吗？

- 使用并发，但要控制并发量 -> 一个包一个包的查会不会太慢？请求这么频繁 npm 会不会封你的 ip

- 找依赖的时候使用循环，而不是递归 -> 如果一个包的依赖非常非常复杂，内存爆了怎么办
具有失败重试和离线缓存 -> 中途网络挂掉怎么处理？失败的一直失败怎么办？


## Github无法访问 hosts 配置
```shell
# GitHub Start
192.30.253.112 github.com
192.30.253.118 gist.github.com
151.101.112.133 assets-cdn.github.com
151.101.184.133 raw.githubusercontent.com
151.101.112.133 gist.githubusercontent.com
151.101.112.133 avatars0.githubusercontent.com
151.101.112.133 avatars1.githubusercontent.com
151.101.184.133 avatars2.githubusercontent.com
151.101.12.133 avatars3.githubusercontent.com
151.101.12.133 avatars4.githubusercontent.com
151.101.184.133 avatars5.githubusercontent.com
151.101.184.133 avatars6.githubusercontent.com
151.101.184.133 avatars7.githubusercontent.com
151.101.12.133 avatars8.githubusercontent.com
151.101.184.133 cloud.githubusercontent.com
151.101.112.133 camo.githubusercontent.com
# GitHub End
```
修改方法：
- Windows:
修改文件 C:/Windows/system32/drivers/etc/hosts

- Linux系:
修改文件 /etc/hosts

- Android:
修改文件 /system/etc/hosts （需要Root）

## 保留两位小数
1. toFixed()方法
```js
var num =2.446242342;  
num = num.toFixed(2); 
console.log(num); //2.45
```
详见[toFixed方法注意点]('https://mydearest.cn/toFixed%E6%96%B9%E6%B3%95%E6%B3%A8%E6%84%8F%E7%82%B9.html')

2. Math.floor()
```js
num = Math.floor(num * 100) / 100;
console.log(num); //2.44
```

3. 字符串匹配
```js
num = Number(num.toString().match(/^\d+(?:\.\d{0,2})?/));
console.log(num); //2.44
```

4. 保留两位小数 浮点数四舍五入 位数不够 不补0
```js
function fomatFloat(src,pos){    
    return Math.round(src*Math.pow(10, pos))/Math.pow(10, pos);    
} 
console.log(fomatFloat(3.12645,2)); // 3.13
```

## H5 语音合成 SpeechSynthesisUtterance API
```js
var utterance = new SpeechSynthesisUtterance('cosyer');
utterance.lang = 'Google 普通话（中国大陆）';
utterance.rate = 1;
// 语音合成结束时候的回调
utterance.onend = (event) => {}
window.speechSynthesis.speak(utterance);
```

## JS实现语音播报
```js
function speckText(str){
    //var request=  new URLRequest();
    var url = "http://tts.baidu.com/text2audio?lan=zh&ie=UTF-8&text=" + encodeURI(str);        // baidu
    //url = "http://translate.google.cn/translate_tts?ie=UTF-8&tl=zh-CN&total=1&idx=0&textlen=19&prev=input&q=" + encodeURI(str); // google
    //request.url = encodeURI(url);
    // request.contentType = "audio/mp3"; // for baidu
    //request.contentType = "audio/mpeg"; // for google

　　var n = new Audio(url);

　　 n.src = url;

　　 n.play();

    // n.addEventListener('ended', function () {  
    //     alert('over');
    // }, false);
    　　
　　 // $("...").play();
　　 // var sound = new Sound(request);
　　 // sound.play();
}
speckText("收到付款10元。");
```

## instanceof测试练习
```js
Object instanceof Function
Function instanceof Object

Object instanceof Object
Function instanceof Function
// true
```

## 掘金头像旋转
```css
#pic:hover {
    transform: rotate(666turn);
    transition-delay: 1s;
    transition-property: all;
    transition-duration: 59s;
    transition-timing-function: cubic-bezier(.34,0,.84,1);
}
```
1. transform: rotate(666turn); 常见的用来旋转元素角度的css，这里用到的是turn，1turn = 360°
2. transition-delay：1s; 这个属性的作用就是效果执行前的等待时间
3. transition-property: all; 这个属性是指明效果变换的位置，比如width，height等，all是所有属性。
4. transition-duration: 59s; 过渡效果的持续时间。
5. transition-timing-function: cubic-bezier(.34,0,.84,1); 过渡效果的速度曲线 四个值的范围都是0-1 代表整个过程。

## 时间轴
```html
<div class="timeline-content">
  <div v-for='(item, index) in timeLine' :key='index' class="time-line">
    <div :class="`state-${item.state} state-icon`"></div>
    <div class="timeline-title">{{item.title}}</div>
  </div>
</div>
```

```css
/** 时间轴 */
.timeline-content{
  display: flex;
  .time-line{
    padding: 10px 10px 10px 20px;
    position: relative;
    &::before{
      content: '';
      height: 1px;
      width: calc(100% - 34px);
      background: #EBEBEB;
      position: absolute;
      left: 24px;
      top: 0;
    }
  }
  .state-icon{
    width: 20px;
    height: 20px;
    position: absolute;
    top: -12px;
    left: 0;
  }
  .state-1{
    background: url('https://static.daojia.com/assets/project/tosimple-pic/fen-zu-7-copy-6bei-fen_1589266208621.png') no-repeat;
    background-size: cover;
  }
  .state-2{
    background: url('https://static.daojia.com/assets/project/tosimple-pic/12_1589266226040.png') no-repeat;
    background-size: cover;
  }
  .state-3{
    background: url('https://static.daojia.com/assets/project/tosimple-pic/fen-zu-7-copy-3_1589266140087.png') no-repeat;
    background-size: cover;
  }
}
```

## 卡券效果
```css
.coupon{
  width: 300px;
  height: 100px;
  position: relative;
  background: radial-gradient(circle at right bottom, transparent 10px, #ffffff 0) top right /50% 51px no-repeat,
    radial-gradient(circle at left bottom, transparent 10px, #ffffff 0) top left / 50% 51px no-repeat,
    radial-gradient(circle at right top, transparent 10px, #ffffff 0) bottom right / 50% 51px no-repeat,
    radial-gradient(circle at left top, transparent 10px, #ffffff 0) bottom left / 50% 51px no-repeat;
  filter: drop-shadow(2px 2px 2px rgba(0,0,0,.2));
}
```

## css禁用鼠标事件
```css
.disabled {
    pointer-events: none;
    cursor: default;
    opacity: 0.6;
}
```

## css禁止用户选择
```css
body{
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
```

## jquery添加扩展方法
```js
$.fn.stringifyArray = function(array) {
  return JSON.stringify(array)
}
```

- jquery.extend 与 jquery.fn.extend的区别？
> jquery.extend 为jquery类添加类方法，可以理解为添加静态方法
> 源码中jquery.fn = jquery.prototype所以jquery.fn.extend扩展，所有jquery实例都可以直接调用。

```js
// 多个事件同一个函数：
$("div").on("click mouseover", function(){});
// 多个事件不同函数
$("div").on({
  click: function(){},
  mouseover: function(){}
});
```

## 万物转换(oﾟ▽ﾟ)o  
```js
'vue'.split('').sort().join('') // euv

'node'.split('').sort().join('') // deno
```

## require.context实现自动化导入
一个webpack的api,通过执行require.context函数获取一个特定的上下文,主要用来实现自动化导入模块,在前端工程中,如果遇到从一个文件夹引入很多模块的情况,可以使用这个api,它会遍历文件夹中的指定文件,然后自动导入,使得不需要每次显式的调用import导入模块


require.context函数接受三个参数

1. directory {String} -读取文件的路径

2. useSubdirectories {Boolean} -是否遍历文件的子目录

3. regExp {RegExp} -匹配文件的正则

```js
// 遍历当前目录下的test文件夹的所有.test.js结尾的文件,不遍历子目录
require.context('./test', false, /.test.js$/);

(r => {
r.keys().forEach(r);
})(require.context('./', true, /reducer.js/));
```

```js
// 简化vuex module index.js
const files = require.context('.', false, /\.js$/)
const modules = {}

files.keys().forEach((key) => {
  if (key === './index.js') return
  modules[key.replace(/(\.\/|\.js)/g, '')] = files(key).default
})

export default modules
```

## img标签之间的间距问题原理
众所周知，多个img并列显示时会有几像素间距，但是这并不是img标签特有的特性。

### 原理
> 实际上，所有display属性为inline ， inline-block 的盒模型都会有文字特性，间距就是由于两个标签之间的空白引起的。

### 常用方案
- 删除标签之间的空格
```html
<img src="img/test.jpg" alt=""><img src="img/test.jpg" alt=""><img src="img/test.jpg" alt="">
```

- 将父级设置为font-size: 0px：
```html
<div style="font-size: 0px">
  <img src="img/test.jpg" alt="">
  <img src="img/test.jpg" alt="">
  <img src="img/test.jpg" alt="">
  <img src="img/test.jpg" alt="">
  <img src="img/test.jpg" alt="">
</div>
```

-  将父级设置为使用负margin去除边距
```css
img {
    margin-left: -8px;
}
```

- 设置浮动
```css
img {
    float: left;
}
```

## prettier script
```js
"jsformat": "prettier --write \"./**/*.js\""

{
"editor.formatOnSave": true,
"files.autoSave": "off",
"git.ignoreMissingGitWarning": true,
"git.enableSmartCommit": true,
"fileheader.Author": "chenyu",
"fileheader.LastModifiedBy": "chenyu",
"powermode.enabled": false, // 屏振
"git.autofetch": true,
// tsconfig.json 文件会覆盖此设置。要求 TypeScript >=2.3.1。
"javascript.implicitProjectConfig.experimentalDecorators": true,
"prettier.disableLanguages": [
"vue",
"markdown"
],
"vetur.format.defaultFormatter.html": "js-beautify-html",
"javascript.updateImportsOnFileMove.enabled": "never",
"files.associations": {
".cjson": "jsonc",
".wxss": "css",
"*.wxs": "javascript"
},
"emmet.includeLanguages": {
"wxml": "html"
},
"minapp-vscode.disableAutoConfig": true
}
```

## HTML - 输入框 Input 按回车 Enter 自动刷新页面解决方案

### 原因
在一个 form 表单中，若只有一个 input，按回车键表单会自动提交，但是当表单中存在多个 input 时，按回车键不会执行任何操作，这是 form 表单的一个特性。

### 解决
1. 直接去除掉 form 表单，当然这是最简单粗暴的方法。

2. 如果一个 input 会自动提交，那么比较容易想到的是再加一个 input。值得注意的是 这里的 input 不能设置 type 为 hidden，这样一样是不生效的，form 一样会认为只有一个 input。需要设置成 <input type="text" class="form-control" style="display:none"> 。

3. 给 input 加上回车事件直接 return false。在 input 加上 οnkeydοwn="if(event.keyCode==13){return false;} 。

4. 直接阻止 form 表单的提交，在 form 表单加入 οnsubmit="return false;"。


## 为什么ES模块比CommonJS更好?
> ES模块是官方标准，也是JavaScript语言明确的发展方向，而CommonJS模块是一种特殊的传统格式，在ES模块被提出之前做为暂时的解决方案。 ES模块允许进行静态分析，从而实现像 tree-shaking 的优化，并提供诸如循环引用和动态绑定等高级功能。

## 什么是 `tree-shaking`（树摇）
Tree-shaking, 也被称为 "live code inclusion," 通常用于打包时移除js中未引用的代码(dead-code)，它依赖于ES6模块系统中的import 和 export 的**静态结构特性**

- 虽然生产模式下默认开启，但是由于经过babel编译全部模块被封装成IIFE

- IIFE存在副作用无法被tree-shaking掉，需要配置 { module: false } 和 sideEffects: false

- rollup和webpack的shaking程度不同，以一个class为例子

## scope hoisting
> Scope hositing 作用:是将模块之间的关系进行结果推测，可以让webpack文件打包出来的代码文件更小、运行的更快
scope hositing实现原理:分析出模块之间的依赖关系，尽可能的把打散的模块合并到一个函数中，但是前提是不能造成代码冗余， 因此只有哪些被引用了一次的模块可能被合并
由于scope hositing 需要分析出模块之间的依赖关系，因此源码必须使用ES6模块化语句，不然就不能生效，原因和 tree shaking一样。

## webpack优化
### production模式自带优化
- tree shaking
- scope hoisting
- 代码压缩混淆(UglifyJsPlugin)
- 图片压缩： imagemin、image-webpack-loader
### css优化
- css提取到独立文件(mini-css-extract-plugin)
- postcss添加前缀
- css压缩(optimize-css-assets-webpack-plugin)
### js优化
- 代码分离(code splitting)
1. 入口起点：使用entry配置，手动的分离代码（配置多入口）
```js
entry:{
 main: './src/main.js',
 other: './src/other.js'
},
```
2. 放置重复：使用 SplitChunksPlugin 去重和分离 chunk（抽取公共代码）
```js
optimization:{
  splitChunks:{
    chunks: "all"
  }
}
```
3. 动态导入：通过模块的内联函数调用来分离代码（懒加载）
webpack4默认是允许import语法动态导入的，但是需要babel的插件支持，最新版babel的插件包为:@babel/plugin-syntax-dynamic-import,需要注意动态
导入最大的好处就是实现了懒加载，用到那个模块才会加载那个模块，可以提高SPA应用程序的首屏加载速度，三大框架的路由懒加载原理一样。

### noParse(缩小文件搜索范围)
在引入一些第三方模块时，如jq等，我们知道其内部肯定不会依赖其他模块，因为我们用到的只是一个单独的js或者css文件，所以此时如果webpack再去解析他们的内部依赖关系，其实是非常浪费时间的，就需要阻
止webpack浪费精力去解析这些明知道没有依赖的库，可以在webpack的配置文件的module节点下加上noParse，并配置正则来确定不需要解析依赖关系的模块
```js
module:{
 noParse: /jquery|bootstrap/  // jquery|bootstrap 之间不能加空格变成 jquery | bootstrap， 会无效
}
```
- 通过 include 和 exclude 缩小命中范围
- 优化 resolve.modules 配置，指明存放第三方模块的绝对路径，减少寻找时间。
- 优化 resolve.alias/extensions配置

### DLLPlugin、DllReferencePlugin(预编译资源模块提高打包速度)
在引入一些第三方模块时，例如Vue、React等，这些框架的文件一般都是不会修改的，而每次打包都需要去解析他们，也会影响打包速度，就算是做了拆分，也只是提高了上线后的用户访问速度，并不会提高构建速度，所以如果需要提高构建速度，应该使用动态链接库的方式，类似windows的dll文件借助DLLPlugin插件实现将这些框架作为一个个的动态链接库，只构建一次，以后的每次构建都只会生成自己的业务代码，可以很好的提高构建效率。

> 将库和项目代码分离打包需要 dll 映射文件

主要思想在于，将一些不做修改的依赖文件，提前打包，这样我们开发代码发布的时候就不需要再对这些代码进行打包，从而节省了打包时间，主要使用两个插件: DLLPlugin和DLLReferencePlugin
需要注意的是，若是使用的DLLPlugin，CleanWebpackPlugin插件会存在冲突，需要移除CleanWebpackPlugin插件

### 配置缓存（提高打包速度，插件自带 babel-loader开启，不支持的可以用 cache-loader）

### 使用 HappyPack 开启多进程 Loader 转换（webpack4推荐thread-loader）
```js
rules: [
  {
    test: /\.js$/,
    include: path.resolve("src"),
    use: [
      "thread-loader",
      // your expensive loader (e.g babel-loader)
    ]
  }
]
```

```js
rules: [
  {
    test: /.js$/,
    use: 'happypack/loader',
  }
],
plugins: [
  new HappyPack({
    loaders: [ 'babel-loader?presets[]=es2015' ]
  })
];
```

### 使用 include 或 exclude 加快文件查找速度

### IgnorePlugin
在引入一些第三方模块时，例如momentJS、dayJS，其内部会做i18n处理，所以会包含很多语言包，而语言包打包时会比较占用空间，如果项目只需要用到中文或者少数语言，可以忽略掉所有的语言包，然后按需引
入语言包，从而使得构建效率更高，打包生成的文件更小

## 分析优化
在打包时，合理使用插件speed-measure-webpack-plugin和webpack-bundle-analyzer，可以对项目构建过程中的每个loader、plugin等进行耗时统计，并针对耗时的操作进行优化，打包结果可以进行体积的大
小的控制和优化。

## windows更新计算机策略
```bash
gpupdate /force
```

## 有一个数字字符串，它所代表的数字远大于js所能表示的最大数，可能有两万位，这样的数字你怎么判断它能不能被6整除
1. 调接口放到后台处理
2. 最后一位被2整除 && 所有位加起来被3整除
```js
function isValid(str) {
  const arr = str.split('')
  return Number(arr[arr.length -1]) %2 === 0 && arr.reduce((total, num) => total + num, 0) % 3 === 0
}
```

3. 高位判断
最大安全数 2**53 -1 // 9007199254740991
因为js用的是IEEE754标准，小数有效位为52位，加上默认的1一共是53位，超过这个就无法保持精确了。
```js
function isValid(str) {
  const arr = str.split('')
  let temp = 0
  for (let i of arr) {
    temp = i * temp + parseInt(i , 10)
    temp = temp % 6
  }
  if (temp === 0) {
    return true
  } else {
    return false
  }
}
```

## 什么时候适合使用 Map 而不是 Object
「Map」映射是一种经典的数据结构类型，其中数据以 「key/value」 的键值对形式存在
||Map|Object|
|:--|:--:|--:|
|默认值|默认不包含任何值，只包含显式插入的键|一个 Object 有一个原型，原型上的键名有可能和自己对象上设置的键名冲突|
|类型|任意|String 或 Symbol|
|长度|键值对个数通过 size 属性获取|键值对个数只能手动计算|
|性能|频繁增删键值对的场景下表现更好|频繁添加和删除键值对的场景下未作出优化|

>「Map」 是可迭代的，可以直接进行迭代，例如forEach循环或者for...of...循环

### 遍历Object
```js
for(const key of Object.keys(object)) {
  console.log(key);
}
// key1
// key2
// key3

for(const value of Object.values(object)) {
  console.log(value);
}
// value1
// value2
// value3

for(const entry of Object.entries(object)) {
  console.log(entry);
}
// ["key1", "value1"]
// ["key2", "value2"]
// ["key3", "value3"]

for(const [key,value] of Object.entries(object)) {
  console.log(key,value);
}
//"key1", "value1"
//"key2", "value2"
//"key3", "value3"

// for in遍历
for(const key in object) {
  console.log(key);
}
// key1
// key2
// key3
```

## JS中的宿主对象与原生对象有何不同？
宿主对象:这些是运行环境提供的对象。这意味着它们在不同的环境下是不同的。例如，浏览器包含像windows这样的对象，但是Node.js环境提供像Node List这样的
对象。 

原生对象:这些是JS中的内置对象。它们也被称为全局对象，因为如果使用JS，内置对象不受是运行环境影响。

## maven配置环境变量
> path配置需要具体的路径

## 个人使用觉得最好的word转pdf在线网站
- https://www.addpdf.cn/word-to-pdf

- http://www.ilovepdf.com/zh_cn

- https://smallpdf.com/

## knife4j 增强swagger的UI
正则就是有限的状态机

## mysql迁移postgresql问题记录：

1. jdbc连接字符串需要指定当前schema jdbc:postgresql://193.160.26.65:5432/engine?useUnicode=true&characterEncoding=utf-8&currentSchema=faker_vmware

2. postgresql列名是区分大小写的，大写的列名需要加上双引号

3. mysql没有bool数据类型，用的tinyint(1)，sql语句中where flag = false迁移报错，pg用的int(2)

4. ON DUPLICATE KEY UPDATE 重复插入约束 => on conflict (id) do update set

5. 在pg中的sql，单引号用来标识实际的值，双引号用来标识表名（table name）或列名（column name）等数据库中存在的值

## Typed Arrays 
一个TypedArray 对象描述一个底层的二进制数据缓存区的一个类似数组(array-like)视图

## markdown github stats
- ![my github stats](https://github-readme-stats.vercel.app/api?username=cosyer&show_icons=true&hide_border=true&hide=contribs,prs)

- ![主要使用语言](https://github-readme-stats.vercel.app/api/top-langs/?username=cosyer)

## 微信分享 hash 路由填坑记录
```js
encodeURIComponent(window.location.href.split('#')[0])
```

## Promise题目
```js
Promise.resolve(1).then(2).then(Promise.resolve(3)).then(console.log)
// 1 then的参数为回调函数 then(2)就断掉了 链式调用
// 可选链
a?.b?.c 
```

## mock总结
### json-server
mock文件夹配置db.json
```js
{
  "rankList": []
}
// 访问localhost:port/api/rankList
```

## 点击input事件触发的顺序
```js
const text = document.getElementById('text');
text.onclick = function (e) {
  console.log('onclick')
}
text.onfocus = function (e) {
  console.log('onfocus')
}
text.onmousedown = function (e) {
  console.log('onmousedown')
}
text.onmouseenter = function (e) {
  console.log('onmouseenter')
}
// onmouseenter
// onmousedown
// onfocus
// onclick
```

## 计算页面停留时间
1. websocket，前端开个长连接，后台统计长连接时间。
2. ajax轮询，隔几秒发一个查询，后台记录第一与最后一个查询间隔时间。
3. 关闭窗口或者跳转的时候会触发window.onbeforeunload函数，可以在该函数中做处理（有兼容性问题）；统计完数据记录到本地cookies中，一段时间后统一发送

## 二维码如何工作
1. pc端随机生成一个含有唯一uid的二维码，并与服务器建立一个长连接；
2. 手机扫描二维码，解析出二维码中的uid，并把这个uid和手机端的用户密码进行绑定，上传给服务器；
3. 服务器获得客户端信息之后，pc端的长连接轮询操作会获得该消息，显示该账号的信息；
4. pc端会再开一个长连接与手机端保持通信，等待手机端确认登陆后，获得服务器授权的token，就可以在pc端登陆进行正常通信了

## 兼容性查看
可以到Can I use上去查看，官网地址为：[caniuse.com](https://caniuse.com/)

## github访问量计数
- https://visitor-badge.glitch.me/badge?page_id=cosyer

- https://profile-counter.glitch.me/cosyer/count.svg
```html
<p align="center"> Visitor count </p> 
<img src="https://profile-counter.glitch.me/cosyer/count.svg" />
</a>
```

## github profile统计
![my github stats](https://github-readme-stats.vercel.app/api?username=cosyer&show_icons=true&hide_border=true&
hide=contribs,prs)

```html
<img src="https://github-profile-trophy.vercel.app/?username=cosyer&theme=flat&column=3&margin-w=10&title=Stars,Commit,Issues" alt="logo" height="160" align="center" />
```

## simple-icons
![github](https://cdn.jsdelivr.net/npm/simple-icons@3.1.0/icons/github.svg)

![github](http://simpleicons.p2hp.com/icons/github.svg)

## github emoji
[slackmojis](https://slackmojis.com/)

## github badge
![](https://img.shields.io/badge/github-cosyer-brightgreen.svg)

## 萌萌计数器
![cosyer](https://count.getloli.com/get/@:cosyer)

## 手动触发event
```js
// 阻塞视图渲染会引发高度错误
window.dispatchEvent(new Event('resize'))
```

## 状态判断简化
```js
v-if="scope.row.reason === 'Error' || scope.row.reason === 'Completed'"
v-if="['Error', 'Completed'].includes(scope.row.reason)"
```

## 多个tab页监听localstorage变化
```js
// 非己页面
window.addEventListener("storage", function(e) {
    if (e.key === 'projectName') {
      window.location.href = './?project=' + e.newValue
    } 
});
// 当前tab页监听
let orignalSetItem = localStorage.setItem;
localStorage.setItem = function(key, newValue){
    var setItemEvent = new Event("setItemEvent");
    setItemEvent.key = key;
    setItemEvent.value = newValue;
    window.lastProjectName = localStorage.getItem('projectName');
    window.dispatchEvent(setItemEvent);
    orignalSetItem.apply(this, arguments);
};
window.addEventListener("setItemEvent", function (e) {
    if(e.key === 'projectName' && window.lastProjectName !== e.value){
      window.location.href = './?project=' + e.value
    }
});
```

## mac启动台没有应用程序图标
```bash
mv ~/L*/Application\ Support/Dock/*.db ~/Desktop; killall Dock; exit;

# or
defaults write com.apple.dock ResetLaunchPad -bool true
killall Dock
```

## nodejs JavaScript heap out of memory
默认nodejs 使用的V8引擎官方默认配置是在64位的系统重视1.4g的内存，并且内存大小最大为4g，官方还建议如果使用较
大内存的话则需要分成多个子worker这样来规避大内存的使用。

1. --max_old_space_size=4096 这个参数，设置最大4gb的内存

2. 全局直接安装 一个npm包 `increase-memory-limit`这个包会自动为命令增加 --max_old_space_size=4096 参数

安装`increase-memory-limit`导致git commit失败解决方案：**删除node_modules->.bin->lint-staged 里面的--max-old-space-size=XXX**

https://github.com/endel/increase-memory-limit/issues/30

3. export NODE_OPTIONS=--max_old_space_size=4096

## 超链接 target="_blank" 要增加 rel="noopener noreferrer"
当你浏览一个页面点击一个a标签连接 <a href="www.baidu.com" target="_blank"> 跳转到另一个页面时，

在新打开的页面（baidu）中可以通过 window.opener获取到源页面的部分控制权， 即使新打开的页面是跨域的也照样可以（例如 location 就不存在
跨域问题）。

在chrome 49+，Opera 36+，打开添加了rel=noopener的链接， window.opener 会为null。在老的浏览器中，可以使用 rel=noreferrer 禁用
HTTP头部的Referer属性，使用下面JavaScript代替target='_blank' 的解决此问题：
```js
var otherWindow = window.open('https://mydearest.cn');
otherWindow.opener = null;
otherWindow.location = url;
```

## query参数里含有#需要encode

## 获取当天0点时间戳
```js
new Date(new Date().toLocaleDateString().replace(/\//g, '-'))
```
## 秒转换
```js
// 秒转换成day、hour、minutes、seconds
function  formatSecond(second: number) {
  const days = Math.floor(second / 86400);
  const hours = Math.floor((second % 86400) / 3600);
  const minutes = Math.floor(((second % 86400) % 3600) / 60);
  const seconds = Math.floor(((second % 86400) % 3600) % 60);
  const forMatDate = {
    days: days,
    hours: hours,
    minutes: minutes,
    seconds: seconds
  };
  return forMatDate;
}
console.log(formatSecond(90)) // 0天0小时1分30秒
```

## 文本区域textarea出现滚动条
```html
<!-- 此时除非遇到换行符，否则内容将一直向右 -->
<textarea wrap="off" style="overflow:scroll;"></textarea>
```

## case 里let声明
Unexpected lexical declaration in case block  no-case-declarations

```js
// 要求case 加上{}
case "cosyer": {
  let flag = false;
}
```

## every数组为空时返回true
```js
[].every(i => i === 1); // true
```

## 判断base64
```js
isBase64(str){
if(str === '' || str.trim() === ''){
      return false;
  }
  try{
      return btoa(atob(str)) == str;
  } catch(err){
      return false;
  }
},
```

## 带分隔线的button
```css
.pl-textbtn {
  font-size: 10px !important;
  -webkit-box-shadow: 1px 0 0 #DCDFE6;
  box-shadow: 1px 0 0 #DCDFE6;
  border-radius: 0 !important;
  padding: 3px 5px !important;
  margin-left: 0 !important;
}
```

## mysql varchar字段过长被截断
mysql在配置文件my.cnf中有一个配置项
[mysqld]
sql-mode = "xx_mode" sql-mode中有STRICT_TRANS_TABLES是在数据超长的情况下会插入失败，当删除这个限制时，插入超长会MySQL会
自动截断超长的字段

## 浏览器发送通知
```js
function notifyMe() {
  // 先检查浏览器是否支持
  if (!("Notification" in window)) {
    alert("This browser does not support desktop notification");
  }

  // 检查用户是否同意接受通知
  else if (Notification.permission === "granted") {
    // If it's okay let's create a notification
    var notification = new Notification("Hi there!");
  }

  // 否则我们需要向用户获取权限
  else if (Notification.permission !== "denied") {
    Notification.requestPermission().then(function (permission) {
      // 如果用户接受权限，我们就可以发起一条消息
      if (permission === "granted") {
        var notification = new Notification("Hi there!");
      }
    });
  }
  // 最后，如果执行到这里，说明用户已经拒绝对相关通知进行授权
  // 出于尊重，我们不应该再打扰他们了
}
```

## 文件大小转化
```js
function binaryFormatter(input) {
  const unitArr = [
    ['B', 'BYTE'],
    ['KB', 'KI', 'KIB', 'K'],
    ['MB', 'MI', 'MIB', 'M'],
    ['GB', 'GI', 'GIB', 'G'],
    ['TB', 'TI', 'TIB', 'T'],
    ['PB', 'PI', 'PIB', 'P'],
    ['EB', 'EI', 'EIB', 'E'],
    ['ZB', 'ZI', 'ZIB', 'Z'],
    ['YB', 'YI', 'YIB', 'Y']
  ]
  const k = 1024
  const dm = 2
  const pattern = /^(-?)(\d+(\.\d+)?)([A-Z]*)$/
  const exponentialPattern = /[-+]?[0-9]*\.?[0-9]+([eE][-+]?[0-9]+)/

  const inputStr = String(input).toUpperCase().replace(/\s+/g, '')

  if (exponentialPattern.test(inputStr)) { // 如果是科学计数法数字
    return cleverToFixed(input)
  }

  if (!pattern.test(inputStr)) {
    return input
  }

  const splitOperator = pattern.exec(inputStr)[1]
  const splitNumber = pattern.exec(inputStr)[2]
  const splitUnit = pattern.exec(inputStr)[4]

  let inputBytes
  if (splitUnit === '') {
    inputBytes = Math.round(parseFloat(splitNumber))
  } else {
    const unitIndex = findIndex(unitArr, (o) => {
      if (indexOf(o, splitUnit) > -1) {
        return o
      }
    })
    if (unitIndex === -1) {
      return inputStr
    }
    inputBytes = Math.round(parseFloat(splitNumber)) * Math.pow(k, unitIndex)
  }

  const i = inputBytes === 0 ? 0 : Math.floor(Math.log(inputBytes) / Math.log(k))
  const op = splitOperator === '-' ? -1 : 1
  return op * parseFloat((inputBytes / Math.pow(k, i)).toFixed(dm)) + unitArr[i][0]
}
```

## 每隔12个字符插入换行符
```js
''.replace(/.{12}/gm,"$&\r\n")
```

## 修复移动硬盘
```bash
chkdsk F: /f
```

## 解决 primordials is not defined 问题
> 在安装npm依赖的时候碰到了ReferenceError: primordials is not defined的问题
原因`安装gulp版本与node版本不兼容`
[stackoverflow给出的方法是回退node版本或升级gulp版本](https://stackoverflow.com/q/55921442/6304805)

## mac查看主机ip
```bash
ifconfig | grep "inet"
```
