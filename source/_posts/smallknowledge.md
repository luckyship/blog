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
context === void 666
```

---
<!-- more -->

## Element.scrollIntoViewIfNeeded
Element.scrollIntoViewIfNeeded()方法用来将不在浏览器窗口的可见区域内的元素滚动到浏览器窗口的可见区域。 如果该元素已经在浏览器窗口的可见区域内，则不会发生滚动。
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

```javascript
1. 位运算：~ 的结果是 int32 的有符号整数，所以肯定不可能是 NaN 和无穷 x|0 x^0 x<<0 x>>0

2. Math.floor向下取整 Math.ceil向上取整 Math.trunc返回整数部分 实现：if(value>0){return Math.floor(value)}else{return Mathg.ceil(value)}

3. parseInt(string, radix);

parseInt() 函数解析一个字符串参数，并返回一个指定基数的整数 (数学系统的基础)。

parseInt 解析字符串 '-0' 会得到 -0。如果参数是数字 -0，会得到 0。
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

## 实现var a = add(2)(3)(4) 函数柯里化
js中console.log一个对象时，会对这个对象进行toString()操作，还有些情况会对对象进行valueOf()操作
vauleOf优先于toString()被调用

```javascript
function add(num){
    var _add = function(args){
        num+=args;
        return arguments.callee; //  return add(num+args);
        // 现在已经不推荐使用arguments.callee();推荐这样写var _add = (function foo(args){num+=args return foo;})
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

​ y: (el.pageYOffset !== undefined) ? el.pageYOffset : el.scrollTop});

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

([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, c =>

​ (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)

);

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

num & 1 == 1 //奇数
num & 1 == 0 //偶数
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

## setTimeout 为不可执行的字符串时会造成内存泄露
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
```

## 随机数
```javascript
// [0,1) 左闭右开 min-max 
Math.floor(min+Math.random()*(max-min+1))
// toString() this is object方法 toString() valueOf
// 随机颜色
item.style.backgroundColor = '#' + Math.random().toString(16).slice(2, 8);
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

## uuid生成
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
        $input.remove();
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

## script标签关键字
- defer 并发下载 顺序执行 渲染完再执行
- async 并发下载 异步执行谁先下载完 谁先执行

## 防止对象篡改
* Object.preventExtensions(obj): obj 不能添加属性
* Object.seal(obj): obj 不能添加/删除属性
* Object.freeze(obj): obj 不能添加/删除/修改属性

## module.exports和exports
module.exports才是真正的接口，exports只不过是它的一个辅助工具。　最终返回给调用的是module.exports而不是exports。
所有的exports收集到的属性和方法，都赋值给了Module.exports。当然，这有个前提，就是module.exports本身不具备任何属性和方法。

1. 最好分别定义module.exports和exports

2. 建议导出对象用module.exports,导出多个方法和变量用exports

## meta自动跳转
```javascript
<meta http-equiv="Refresh" content="1; url=http://www.baidu.com"/>
```