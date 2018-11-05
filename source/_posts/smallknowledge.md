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
context == void 666
```

---
<!-- more -->

## Element.scrollIntoViewIfNeeded
 Element.scrollIntoViewIfNeeded（）方法用来将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。 如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动。
 ```javascript
element.scrollIntoViewIfNeeded(); // 等同于element.scrollIntoViewIfNeeded(true) 
element.scrollIntoViewIfNeeded(true); 
element.scrollIntoViewIfNeeded(false);
 ```
- 当元素已经在可视区域时，调用 Element.scrollIntoView()，无论设置什么参数，均发生滚动。
- 当元素已经在可视区域时，调用 Element.scrollIntoViewIfNeeded()，无论设置什么参数，均不发生滚动。

## JS取整
```javascript
~~2.5 // 2 按位取反 -2^31~2^31-1 -2147483648~2147483647
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

1. 位运算：~ 的结果是 int32 的有符号整数，所以肯定不可能是 NaN 和无穷 x|0 x^0 x<<0 x>>0

2. Math.floor向下取整 Math.ceil向上取整 Math.trunc返回整数部分 实现：if(value>0){return Math.floor(value)}else{return Mathg.ceil(value)}

3. parseInt(string, radix);

parseInt() 函数解析一个字符串参数，并返回一个指定基数的整数 (数学系统的基础)。

parseInt 解析字符串 '-0' 会得到 -0。如果参数是数字 -0，会得到 0。

```javascript
parseInt(0.0000000003) // 3

parseInt('2017-07-04') // 2017
```

## JS浮点数运算(原因浮点数无法用二进制准确表示)
```javascript
0.1+0.2 // 0.30000000000000004
1. parseFloat().toFixed(10) 
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

## 实现var a = add(2)(3)(4)
js中console.log一个对象时，会对这个对象进行toString()操作，还有些情况会对对象进行valueOf()操作
vauleOf优先于toString()被调用

```javascript
function add(num){
    var _add = function(args){
        num+=args;
        return arguments.callee; //  return add(num+args);
    }
    _add.toString = _add.valueOf = function(){
        return num;
    }
    return _add;
}
add(2)(3)(4);// function 9
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
// 博客存活了11天 21小时28分51秒 1527868800000当时的时间转的时间戳
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

## 两数组去重合并
```javascript
function filter(a,b){
	for(let m in a){ 
	let isExist=false;
	for(let n in b ){
	if(b[n]==a[m]){
	isExist=true;
	break;
	}
	}
if(!isExist){
	b.push(a[m]);
}
	}
	return b;
}
// filter([1,2,3,4],[2,3])
// [2, 3, 1, 4]
```

##  `<script>`元素放在 HTML 文件底部

我们将 `<script>`元素放在 HTML 文件底部的原因是，浏览器按照代码在文件中的顺序解析 HTML。如果 JavaScript在最前面被加载，HTML还未加载，JavaScript将无法作用于HTML，所以JavaScript无效，如果 JavaScript 代码出现问题则 HTML 不会被加载。所以将 JavaScript 代码放在底部是最好的选择。

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
return false;
}
```

## 手机号处理中间4位替换成*

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
使用按位左移运算符(<<)和toString(16)，然后padStart(6，'0')将给定的RGB参数转换为十六进制字符串以获得6位十六进制值。

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
使用replace（）匹配每个单词的第一个字符，并使用toUpperCase（）来将其大写。

```javascript
const capitalizeEveryWord = str => str.replace(/[a-z]/g, char => char.toUpperCase());

// capitalizeEveryWord(‘hello world!’) -> ‘Hello World!’
```

## 计算数组中某值出现的次数
每次遇到数组中的特定值时，使用reduce（）来递增计数器。

```javascript
const countOccurrences = (arr, value) => arr.reduce((a, v) => v === value ? a + 1 : a + 0, 0);

// countOccurrences([1,1,2,1,2,3], 1) -> 3
```

## 数组间的区别
从b创建一个Set，然后在a上使用Array.filter（），只保留b中不包含的值。
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

​ y: (el.pageYOffset !== undefined) ? el.pageYOffset : el.scrollTop});

// getScrollPos() -> {x: 0, y: 200}
```

## 最大公约数
```javascript
const gcd = (x, y) => !y ? x : gcd(y, x % y);

// gcd (8, 36) -> 4
```

## UUID生成器
使用crypto API生成符合RFC4122版本4的UUID。

```javascript
const uuid = _ =>

([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>

​ (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)

);

// uuid() -> ‘7982fcfe-5721-4632-bede-6000885be57d’
```

## 检查变量是否声明
如果读取一个不存在的键，会返回undefined，而不是报错。可以利用这一点，来检查一个全局变量是否被声明。
后二种写法有漏洞，如果a属性是一个空字符串（或其他对应的布尔值为false的情况），则无法起到检查变量是否声明的作用。
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
function
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
