---
title: 面试题整理归纳
tags:
  - 面试
copyright: true
comments: true
date: 2018-06-23 10:58:48
categories: 知识
photos:
---

## 字符串扩展的方法
- includes()：返回布尔值，表示是否找到了参数字符串。数组也可以 a[1]=1 且能判断undefined
```js
var a=[1,2,3]
a[4]=5 // [1, 2, 3, undefined × 1, 5] empty
// a[3]=undefined [1, 2, 3, undefined, 5] 

a.indexOf(undefined) // -1
a.includes(undefined) // true 
```
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
```js
// polyfill
if (String.prototype.startsWith) {
  String.prototype.startsWith = function (search, index) {
    return this.substr((!index || index < 0) ? 0 : index, search.length) === search;
  }
}
```
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。
str | index
- repeat()：返回一个新字符串，表示将原字符串重复n次。参数如果是小数，会被取整(不四舍五入)。参数是负数或者Infinity，会报错。0/NaN返回空字符串,参数是字符串，则会先转换成数字，不传则为空字符串。
- padStart()：头部补全。
- padEnd()：尾部补全
~~~
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
~~~
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。默认使用空格

---
<!-- more -->

- 模板字符串（template string）是增强版的字符串，用反引号\`标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。\`${表达式、变量}\`
- commonjs 服务器端 amd 浏览器端
- const 必须赋值定义 let 在同一作用于无法重复命名 无法变量提升
- split(字符串或者正则,设置长度) 字符串=>数组
- substr(开始的索引//splice可以为负数-1则为字符串最后一个字符,length字符数)方法不同的是,substring(开始索引，结束索引+1)负的参数有区别
只有单参数时到字符串结尾
String exd=filePath.substring(filePath.lastIndexOf(".")+1,filePath.length)

## 声明函数作用提升?声明变量和声明函数的提升有什么区别?

(1) 变量声明提升：变量申明在进入执行上下文就完成了。
只要变量在代码中进行了声明，无论它在哪个位置上进行声明， js引擎都会将它的声明放在范围作用域的顶部；

(2) 函数声明提升：执行代码之前会先读取函数声明，意味着可以把函数申明放在调用它的语句后面。
只要函数在代码中进行了声明，无论它在哪个位置上进行声明， js引擎都会将它的声明放在范围作用域的顶部；

(3) 变量or函数声明：函数声明会覆盖变量声明，但不会覆盖变量赋值。
同一个名称标识a，即有变量声明var a，又有函数声明function a() {}，不管二者声明的顺序，函数声明会覆盖变量声明，也就是说，此时a的值是声明的函数function a() {}。注意：如果在变量声明的同时初始化a，或是之后对a进行赋值，此时a的值变量的值。
```javascript
eg: var a; var c = 1; a = 1; function a() { return true; } console.log(a);
```

## 如何判断数据类型？
typeof返回的类型都是字符串形式，可以判断function的类型；在判断除Object类型的对象时比较方便。
判断已知对象类型的方法： instanceof，后面一定要是对象类型，并且大小写不能错，该方法适合一些条件选择或分支。
```javascript
typeof null // object null instanceof Object // false

var str = 'abc';     
console.log(typeof str++);         //number NaN
console.log(typeof ('abc' + 1));   //string 'abc1'

console.log(typeof new Date());    //object
console.log(typeof Date());        //string
console.log(typeof Date);          //function
```

## 异步编程？
- 方法1：回调函数，优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。

- 方法2：事件监听，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以“去耦合”（Decoupling），有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。

- 方法3：发布/订阅，性质与“事件监听”类似，但是明显优于后者。

- 方法4：Promises对象，是CommonJS工作组提出的一种规范，目的是为异步编程提供统一接口。简单说，它的思想是，每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。

## 事件流？事件捕获？事件冒泡？
事件流：从页面中接收事件的顺序。也就是说当一个事件产生时，这个事件的传播过程，就是事件流。

IE中的事件流叫事件冒泡；事件冒泡：事件开始时由最具体的元素接收，然后逐级向上传播到较为不具体的节点（文档）。对于html来说，就是当一个元素产生了一个事件，它会把这个事件传递给它的父元素，父元素接收到了之后，还要继续传递给它的上一级元素，就这样一直传播到document对象（亲测现在的浏览器到window对象，只有IE8及以下不这样。

事件捕获是不太具体的元素应该更早接受到事件，而最具体的节点应该最后接收到事件。他们的用意是在事件到达目标之前就捕获它；也就是跟冒泡的过程正好相反，以html的click事件为例，document对象（DOM级规范要求从document开始传播，但是现在的浏览器是从window对象开始的）最先接收到click事件的然后事件沿着DOM树依次向下传播，一直传播到事件的实际目标；
```html
<nav id="root_b">
  <ul id="first_b">
    <li id="second_b"><a id="target_b" href="#">冒泡</a></li>
  </ul>
</nav>
<nav id="root_c">
  <ul id="first_c">
    <li id="second_c"><a id="target_c" href="#">捕获</a></li>
  </ul>
</nav>
```
```js
/**
* listen
* @param {string[]} ids 
* @param {boolean} isCatch 
*/
const listen = (ids, isCatch) => ids.forEach(id => document.getElementById(id).addEventListener('click', () => alert(id), isCatch))

// BubbleEvent
listen(['root_b', 'first_b', 'second_b', 'target_b'], false)

// CatchEvent
listen(['root_c', 'first_c', 'second_c', 'target_c'], true)
```

## 如何添加一个dom对象到body中?innerHTML、document.write和innerText区别?
body.appendChild(dom元素)；  
innerHTML:从对象的起始位置到终止位置的全部内容,包括Html标签。
innerText:从起始位置到终止位置的内容, 但它去除Html标签 
document.write只能重绘整个页面

## 简述ajax流程  
1)客户端产生触发js的事件  
2)创建XMLHttpRequest对象  
```js 
var client=null
if(window.XMLHttpRequest){
      client = new XMLHttpRequest();
}else{
      client = new ActiveXObject("Microsoft.XMLHTTP");
}
```
3)对XMLHttpRequest进行配置 
```js   
client.open("GET", url);
client.onreadystatechange = function(e) {
    if (client.readyState !== 4) { // client状态
      return;
    }
    if (client.status === 200) { // HTTP状态码
      console.log('success', client.responseText);
    } else {
      console.warn('error');
    }
}; // 指定回调函数
client.responseType = "json";
client.setRequestHeader("Accept", "application/json;");
client.setRequestHeader("Content-Type", "application/json;charset=utf-8");
```
4)通过AJAX引擎发送异步请求 
```javascript
  client.send()
```
`promise 封装`
```js
/**
  * @param {string} url
  * @param {string} method
  * @param {object} params
  * @returns
  */
function request(url, method = 'GET', params = null) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.open(method, url)
    xhr.addEventListener('readystatechange', () => {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          resolve(xhr.responseText)
        } else {
          reject({
            code: xhr.status,
            response: xhr.response
          })
        }
      }
    })
    setTimeout(() => reject('timeout:1000'), 1000)
    xhr.send(JSON.stringify(params))
  })
}
```
5)服务器端接收请求并且处理请求，返回html或者xml内容  
6)XML调用一个callback()处理响应回来的内容  
7)使用JS和DOM实现局部刷新

## 自执行函数？用于什么场景？好处？  
1、声明一个匿名函数  
2、马上调用这个匿名函数。  
作用：创建一个独立的作用域。  

好处：防止变量弥散到全局，以免各种js库冲突。隔离作用域避免污染，或者截断作用域链，避免闭包造成引用变量无法释放。利用立即执行特性，返回需要的业务函数或对象，避免每次通过条件判断来处理。

场景：一般用于框架、插件等场景，设计私有变量和方法，封闭私有作用域。

## 立即执行函数表达式(IIFE)
### 使用匿名函数表达式
```js
var a = 2;
(function IIFE(){
	var a = 3;
	console.log(a);//3
})();
console.log(a);//2
```
### 当作函数调用并传递参数进去
```js
var a = 2;
(function IIFE(global){
	var a = 3;
	console.log(a);//3
	console.log(global.a);//2
})(window);
console.log(a);//2
```
### 解决undefined标识符默认值被错误覆盖
```js
undefined = true;
(function IIFE(){
	var a ;
	if(a === undefined){
		console.log('Undefined is safe here!');
	}
})();
```
#### 倒置代码运行顺序
```js
var a = 2;
(function IIFE(def){
	def(window);
})(function def(global){
	var a = 3;
	console.log(a);//3
	console.log(global.a);//2
});
```

```js
var i = 1;
var IFun = (function(){
	var i = 1;
	console.log(i);
	return function(){
		i++;
		console.log(i);
}
})();
IFun();
IFun();
最终输出的结果为1，2，3，很多人会下意识的觉得结果会有4个值，但是运用了return 返回值以及自执行函数将函数返回给IFun变量，使得在第一次操作过程后，将返回函数直接赋给IFun。
```

## 回调函数？（传递地址，由非实现方调用）  
回调函数就是一个通过函数指针调用的函数。如果你把函数的指针（地址）作为参数传递给另一个函数，当这个指针被用来调用其所指向的函数时，我们就说这是回调函数。回调函数不是由该函数的实现方直接调用，而是在特定的事件或条件发生时由另外的一方调用的，用于对该事件或条件进行响应。

## 什么是闭包?堆栈溢出有什么区别？ 内存泄漏? 那些操作会造成内存泄漏？怎么样防止内存泄漏？impression  
闭包：就是能够读取其他函数内部变量的函数。一般是指内层函数。(子函数在外调用，子函数所在的父函数的作用域不会被释放。) 

堆栈溢出：就是不顾堆栈中分配的局部数据块大小，向该数据块写入了过多的数据，导致数据越界，结果覆盖了别的数据。经常会在递归中发生。

内存泄漏是指：用动态存储分配函数内存空间，在使用完毕后未释放，导致一直占据该内存单元。直到程序结束。指任何对象在您不再拥有或需要它之后仍然存在。

造成内存泄漏：
setTimeout 的第一个参数使用字符串而非函数的话，会引发内存泄漏。
闭包、控制台日志、循环（在两个对象彼此引用且彼此保留时，就会产生一个循环）

防止内存泄漏：  
1、不要动态绑定事件；      
2、不要在动态添加，或者会被动态移除的dom上绑事件，用事件冒泡在父容器监听事件；  
3、如果要违反上面的原则，必须提供destroy方法，保证移除dom后事件也被移除，这点可以参考Backbone的源代码，做的比较好；  
4、单例化，少创建dom，少绑事件。

原因：
- 全局变量
- 闭包
- dom清空，事件未清除
- 子元素存在引用
- 计时器未删除

## html和xhtml有什么区别?
HTML是一种基本的WEB网页设计语言，XHTML是一个基于XML的标记语言。

1.XHTML 元素必须被正确地嵌套。

2.XHTML 元素必须被关闭。

3.标签名必须用小写字母。

4.空标签也必须被关闭。

5.XHTML 文档必须拥有根元素。

## 什么是构造函数？与普通函数有什么区别?
构造函数：是一种特殊的方法(函数、对象)、主要用来创建对象时初始化对象，总与new运算符一起使用，创建对象的语句中构造函数的函数名必须与类名完全相同。

与普通函数相比只能由new关键字调用，构造函数是类的标识。

## 通过new创建一个对象的时候，函数内部有哪些改变？
```javascript
function Person(){}
Person.prototype.friend = [];
Person.prototype.age = 18;
  var a = new Person();
  a.friend[0] = '方涛'; // a.friend=['123'] 指向新对象 b.friend // []
  a.age = 18;
  var b = new Person();
  b.friend // ['方涛'] 
  b.age   // 18
```
> 1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。   
> 2、属性和方法被加入到 this 引用的对象中。  
> 3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。

`new 操作符新建了一个空对象，这个对象原型指向构造函数的prototype，执行构造函数后返回这个对象。`

- 创建一个空对象：var obj = {}

- 将空对象的__proto__指向构造函数的 prototype：obj.__proto__ = Person().prototype

- 使用空对象作为上下文调用构造函数： Person.call(obj)

## 事件委托的好处都有啥？说对了都给它=3=
- 利用冒泡的原理，把事件加到父级上，触发执行效果  

- 好处：新添加的元素还会有之前的事件；提高性能。

## 节点类型?判断当前节点类型?
- 元素节点 
- 属性节点 
- 文本节点 
- 注释节点 
- 文档节点

通过nodeObject.nodeType判断节点类型：其中，nodeObject 为DOM节点（节点对象）。该属性返回以数字表示的节点类型，例如，元素节点返回 1，属性节点返回 2 。

## 数组合并的方法？
```js
// 四种方法。
var arr1=[1,2,3];
var arr2=[4,5,6];
arr1 = arr1.concat(arr2);
console.log(arr1); 

var arr1=[1,2,3];
var arr2=[4,5,6];
Array.prototype.push.apply(arr1,arr2);
console.log(arr1);

var arr1=[1,2,3];
var arr2=[4,5,6];
for (var i=0; i < arr2.length; i++) {
arr1.push( arr2[i] );
}
console.log(arr1); 

var arr1=[1,2,3];
var arr2=[4,5,6];

arr1.push(...arr2)
```

## jquery和zepto有什么区别?
- 针对移动端程序，Zepto有一些基本的触摸事件可以用来做触摸屏交互（tap事件、swipe事件），Zepto是不支持IE浏览器的，这不是Zepto的开发者Thomas Fucks在跨浏览器问题上犯了迷糊，而是经过了认真考虑后为了降低文件尺寸而做出的决定，就像jQuery的团队在2.0版中不再支持旧版的IE（6 7 8）一样。因为Zepto使用jQuery句法，所以它在文档中建议把jQuery作为IE上的后备库。那样程序仍能在IE中，而其他浏览器则能享受到Zepto在文件大小上的优势，然而它们两个的API不是完全兼容的，所以使用这种方法时一定要小心，并要做充分的测试。

- Dom操作的区别：添加id时jQuery不会生效而Zepto会生效。

- zepto主要用在移动设备上，只支持较新的浏览器，好处是代码量比较小，性能也较好。
jquery主要是兼容性好，可以跑在各种pc，移动上，好处是兼容各种浏览器，缺点是代码量大，同时考虑兼容，性能也不够好。

## $(function(){})和window.onload 和 $(document).ready(function(){})

- window.onload:用于当页面的所有元素，包括外部引用文件，图片等都加载完毕时运行函数内的函数。load方法只能执行一次，如果在js文件里写了多个，只能执行最后一个。

- $(document).ready(function(){})和$(function(){})都是用于当页面的标准DOM元素被解析成DOM树后就执行内部函数。这个函数是可以在js文件里多次编写的，对于多人共同编写的js就有很大的优势，因为所有行为函数都会执行到。而且$(document).ready()函数在HMTL结构加载完后就可以执行，不需要等大型文件加载或者不存在的连接等耗时工作完成才执行，效率高。

## 简述下 this 和定义属性和方法的时候有什么区别?Prototype？

- this表示当前对象，如果在全局作用范围内使用this，则指代当前页面对象window； 如果在函数中使用this，则this指代什么是根据运行时此函数在什么对象上被调用。 我们还可以使用apply和call两个全局方法来改变函数中this的具体指向。

- prototype本质上还是一个JavaScript对象。 并且每个函数都有一个默认的prototype属性。

- 在prototype上定义的属性方法为所有实例共享，所有实例皆引用到同一个对象，单一实例对原型上的属性进行修改，也会影响到所有其他实例。

## ajax和jsonp的区别？
- 相同点：都是请求一个url
- 不同点：ajax的核心是通过XMLHttpRequest获取内容，jsonp只能get请求
- jsonp的核心则是动态添加`<script>`标签来调用服务器提供的js脚本。

## 常见的http协议状态码？
```js
200：请求成功
201：请求成功并且服务器创建了新的资源
302：服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来响应以后的请求。
304：自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。
400：服务器不理解请求的语法。
404：请求的资源（网页等）不存在
403：该状态表示服务器理解了本次请求但是拒绝执行该任务
405：方法不被允许
500：内部服务器错误
```

## sessionStorage和localstroage与cookie之间有什么关联, cookie最大存放多少字节？
三者共同点：都是保存在浏览器端，且同源的。

区别:
1、cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存

2、存储大小限制也不同，cookie数据不能超过4k，sessionStorage和localStorage 但比cookie大得多，可以达到5M

3、数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭

4、作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面(即数据不共享)；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的( 即数据共享 )。


## ajax的get与post区别？
- get和post都是数据提交的方式。
- get的数据是通过网址问号后边拼接的字符串进行传递的。post是通过一个HTTP包(request body)进行传递数据的。
- get的传输量是有限制的，post是没有限制的。(实际上HTTP 协议从未规定 GET/POST 的请求长度限制是多少。对get请求参数的限制是来源与浏览器或web服务器，浏览器或web服务器限制了url的长度)
- get的安全性可能没有post高，所以我们一般用get来获取数据，post一般用来修改数据。
- get请求, 倒退按钮是无害的, post会重新发起请求
- get会主动缓存, post不会
- get请求只能进行url编码，而post请求支持多种编码
- get产生一个TCP数据包，POST产生两个TCP数据包

get就是将货品放在车顶，post放在车内。

## GC机制？为什么闭包不会被回收变量和函数？
- GC：垃圾回收机制;  
- 外部变量没释放仍然保持着引用，所以指向的大函数内的小函数也释放不了。
引用类型是在没有引用之后, 通过 v8 的 GC 自动回收, 值类型如果是处于闭包的情况下, 要等闭包没有引用才会被 GC 回收, 非闭包的情况下等待 v8 的新生代 (new space) 切换的时候回收。

33 面向对象？
万物皆对象，把一个对象抽象成类，具体上就是把一个对象的静态特征和动态特征抽象成属性（属性名、属性值）和方法，也就是把一类事物的算法和数据结构封装在一个类之中，程序就是多个对象和互相之间的通信组成的。

面向对象具有封装性，继承性，多态性。
封装：隐蔽了对象内部不需要暴露的细节，使得内部细节的变动跟外界脱离，只依靠接口进行通信。封装性降低了编程的复杂性。通过继承，使得新建一个类变得容易，一个类从派生类那里获得其非私有的方法和公用属性的繁琐工作交给了编译器。而继承和实现接口和运行时的类型绑定机制所产生的多态，使得不同的类所产生的对象能够对相同的消息作出不同的反应，极大地提高了代码的通用性。

总之，面向对象的特性提高了大型程序的重用性和可维护性。

## jsonp的原理和缺点？
- 原理：使用script标签实现跨域访问，可在url中指定回调函数，获取JSON数据并在指定的回调函数中执行jquery实现jsop。
- 缺点：只支持GET方式的jsonp实现，是一种脚本注入行为存在一定的安全隐患。如果返回的数据格式有问题或者返回失败了，并不会报错。

## call和apply两者的区别和好处？
- call和apply都是改变this指向的方法，区别在于call可以写多个参数，而apply只能写两个参数，第二个参数是一个数组，用于存放要传的参数。
- 用call和apply实现更好的继承和扩展，更安全。

## 压缩合并目的？http请求的优化方式？
- Web性能优化最佳实践中最重要的一条是减少HTTP请求。而减少HTTP请求的最主要的方式就是，合并并压缩JavaScript和CSS文件。 

- CSS Sprites（雪碧图、CSS精灵）：把全站的图标都放在一个图像文件中，然后用CSS的background-image和background-position属性定位来显示其中的一小部分。

优点：
1. 减少 HTTP 请求数，极大地提高页面加载速度
2. 增加图片信息重复度，提高压缩比，减少图片大小
3. 更换风格方便，只需在一张或几张图片上修改颜色或样式即可实现

缺点：

1. 图片合并麻烦
2. 维护麻烦，修改一个图片可能需要重新布局整个图片，样式

- 合并脚本和样式表; 图片地图：利用image map标签定义一个客户端图像映射，（图像映射指带有可点击区域的一幅图像）具体看：http://club.topsage.com/thread-2527479-1-1.html 

- 图片js/css等静态资源放在静态服务器或CDN服务器时，尽量采用不用的域名，这样能防止cookie不会互相污染，减少每次请求的往返数据。 

- css替代图片, 缓存一些数据 

- 少用location.reload()：使用location.reload() 会刷新页面，刷新页面时页面所有资源 (css，js，img等) 会重新请求服务器。建议使用location.href="当前页url" 代替location.reload() ，使用location.href 浏览器会读取本地缓存资源。

- 图片懒加载 


## commonjs?requirejs?AMD|CMD|UMD?
- CommonJS就是为JS的表现来制定规范，NodeJS是这种规范的实现，webpack 也是以CommonJS的形式来书写。因为js没有模块的功能，所以CommonJS应运而生。但它不能在浏览器中运行。 CommonJS定义的模块分为:{模块引用(require)} {模块定义(exports)} {模块标识(module)} 

- RequireJS 是一个JavaScript模块加载器。 RequireJS有两个主要方法(method): define()和require()。这两个方法基本上拥有相同的定义(declaration) 并且它们都知道如何加载的依赖关系，然后执行一个回调函数(callback function)。与require()不同的是， define()用来存储代码作为一个已命名的模块。 因此define()的回调函数需要有一个返回值作为这个模块定义。这些类似被定义的模块叫作AMD (Asynchronous Module Definition，异步模块定义)。 

- AMD 是 RequireJS 在推广过程中对模块定义的规范化产出 AMD异步加载模块。它的模块支持对象 函数 构造器 字符串 JSON等各种类型的模块。 适用AMD规范适用define方法定义模块。

- CMD是SeaJS 在推广过程中对模块定义的规范化产出
AMD与CMD的区别：
（1）对于于依赖的模块，AMD 是提前执行(requirejs2.0+可以延迟执行了)，CMD 是延迟执行。
（2）AMD 推崇依赖前置，CMD 推崇依赖就近。
（3）AMD 推崇复用接口，CMD 推崇单用接口。
（4）书写规范的差异。

- umd是AMD和CommonJS的糅合。
AMD 浏览器第一的原则发展 异步加载模块。
CommonJS模块以服务器第一原则发展，选择同步加载，它的模块无需包装(unwrapped modules)。这迫使人们又想出另一个更通用的模式UMD ( Universal Module Definition ), 希望解决跨平台的解决方案。UMD先判断是否支持Node.js的模块( exports )是否存在，存在则使用Node.js模块模式。

CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

## js的几种继承方式？
- 使用对象冒充实现继承
- call、apply改变函数上下文实现继承
- 原型链方式实现继承

## js原型、原型链，有什么特点？
在JavaScript中,一共有两种类型的值,原始值和对象值.每个对象都有一个内部属性[[prototype]],我们通常称之为原型.原型的值可以是一个对象,也可以是null.如果它的值是一个对象,则这个对象也一定有自己的原型.这样就形成了一条线性的链,我们称之为原型链. 

访问一个对象的原型可以使用ES5中的Object.getPrototypeOf方法,或者ES6中的__proto__属性. 原型链的作用是用来实现继承,比如我们新建一个数组,数组的方法就是从数组的原型上继承而来的。

类的继承
特点：基于原型链，既是父类的实例，也是子类的实例
缺点：无法实现多继承

构造继承、组合继承、实例继承和拷贝继承...

## eval是做什么的？
- 将把对应的字符串解析成JS代码并运行； 应该避免使用eval，不安全，非常耗性能（2次，一次解析成js语句，一次执行）。
- 由JSON字符串转换为JSON对象的时候可以用eval，var obj =eval('('+ str +')');

## null和undefined
- null         表示一个对象是“没有值”的值，也就是值为“空”；
- undefined     表示一个变量声明了没有初始化(赋值)；

## json的理解？
- JSON（轻量级的数据交换格式），基于JS的子集，数据格式简单，易于读写，占用带宽小。
```javascript
JSON.parse() // 解析成JSON对象

JSON.strinify() // 解析成JSON字符串
```

## js延时加载的方式？
- defer(仅ie支持)和async
- 动态创建DOM
- 按需异步加载JS

## ajax（异步的js和xml）
- ajax是指一种创建交互式网页应用的网页开发技术。通过后台与服务器进行少量数据交换，AJAX可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

## 同异步的区别？
- 同步(sync)：按顺序执行。
- 异步(async)：不按顺序执行，可以跳过执行下面的代码。

## ajax的缺点？
- 不支持浏览器的back按钮(事件由浏览器内核控制)
- ajax暴露了与服务器的交互
- 对搜索引擎的支持较弱
- 破坏了程序的异常机制
- 不容易调试

## 跨域问题？
- 协议不同
- 端口不同
- 域名不同
- 常用解决方案：
1. jsonp
> <script src="http://example.com/data.php?callback=do"></script>
2. iframe
3. window.name
在一个窗口中，窗口载入的所有页面共享一个window.name，每个页面都对window.name具有读写权限，可以在window.name中设置想要的数据。
4. window.postMessage
```js
// iframe通信
iframe.contentWindow.postMessage(msg);
window.onmessage = function (e) {
    e = e || event;
    alert(e.data);
}
```
5. document.domain
将两个页面的document.domain设置成相同域名即可，js中设置，形如：
document.domain = "";
6. 服务器设置代理页面/响应header配置cors access-control-allow-origin
7. nginx反向代理

## 解决异步回调地狱有哪些方案？
promise、generator、async/await

## 图片的预加载和懒加载？
- 预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。
- 懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。 懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。

## mouseover和mouseenter的区别？
- mouseover：当鼠标移入元素或其子元素都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是mouseout

- mouseenter：当鼠标移除元素本身（不包含元素的子元素）会触发事件，也就是不会冒泡，对应的移除事件是mouseleave

- onmousedown 当元素上按下鼠标按钮时出发
- onmousemove 当鼠标指针移动到元素上移动触发
- onmouseover 当鼠标指针移动元素上时触发
- onmouseout 当鼠标指针移出指定的对象时发生。
- onmouseup 当在元素上释放鼠标按钮时触发
- onmouseenter 事件在鼠标指针移动到元素上时触发。(不冒泡)
- onmouseleave 事件在鼠标移除元素时触发。(不冒泡)

## 改变函数内部this指针的指向函数（bind，apply，call的区别）？
- 通过apply和call改变函数的this指向，他们两个函数的第一个参数都是一样的表示要改变指向的那个对象，第二个参数，apply是数组，而call则是arg1,arg2...这种形式。

- 通过bind改变this作用域会返回一个新的函数，这个函数不会马上执行。

## 说说前端中的事件流
HTML中与javascript交互是通过事件驱动来实现的，例如鼠标点击事件onclick、页面的滚动事件onscroll等等，可以向文档或者文档中的元素添加事件侦听器来预订事件。想要知道这些事件是在什么时候进行调用的，就需要了解一下“事件流”的概念。
什么是事件流：事件流描述的是从页面中接收事件的顺序,DOM2级事件流包括下面几个阶段。

事件捕获阶段
处于目标阶段
事件冒泡阶段

addEventListener：addEventListener 是DOM2 级事件新增的指定事件处理程序的操作，这个方法接收3个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序。
IE只支持事件冒泡。

## 如何让事件先冒泡后执行？
在DOM标准事件模型中，是先捕获后冒泡。但是如果要实现先冒泡后捕获的效果，对于同一个事件，监听捕获和冒泡，分别对应相应的处理函数，监听到捕获事件，先暂缓执行，直到冒泡事件被捕获后再执行捕获事件。

## 什么是事件委托？（事件代理）
简介：事件委托指的是，不在事件的发生地（直接dom）上设置监听函数，而是在其父元素上设置监听函数，通过事件冒泡，父元素可以监听到子元素上事件的触发，通过判断事件发生元素DOM的类型，来做出不同的响应。

举例：最经典的就是ul和li标签的事件监听，比如我们在添加事件时候，采用事件委托机制，不会在li标签上直接添加，而是在ul父元素上添加。

好处：比较合适动态元素的绑定，新添加的子元素也会有监听函数，也可以有事件触发机制。

```html
<ul id="proxy">
  <li><button id="1">1</button></li>
  <li><button id="2">2</button></li>
  <li><button id="3">3</button></li>
</ul>
```

```js
function main() {
  const proxy = document.getElementById('proxy')

  proxy.addEventListener('click', event => {
    const currentTarget = event.target
    const id = currentTarget.id
    switch (id) {
      case '1':
        alert(`proxy: ${1}`)
        break;
      case '2':
        alert(`proxy: ${2}`)
        break;
      case '3':
        alert(`proxy: ${3}`)
        break;
      default:
        break;
    }
  })
}
```
## 垂直居中
- margin:auto法
```css
div{
    width:200px;
    margin:0 auto;
}
/* 绝对定位居中 */
div {
    position: absolute;
    width: 300px;
    height: 300px;
    margin: auto;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-color: pink;    /* 方便看效果 */
}
```

- margin负值法 relative -> absolute 
```css
div {
    position: relative;        /* 相对定位或绝对定位均可 */
    width:500px;
    height:300px;
    top: 50%;
    left: 50%;
    margin: -150px 0 0 -250px;         /* 外边距为自身宽高的一半 */
    /* transform: translate(-50%, -50%); */
    background-color: pink;         /* 方便看效果 */

}
```

- flex布局
```css
.container {
    display: flex;
    align-items: center;         /* 垂直居中 */
    justify-content: center;    /* 水平居中 */

}
.container div {
    width: 100px;
    height: 100px;
    background-color: pink;        /* 方便看效果 */
}  
```

- table-cell未脱离文档流 设置父元素的display:table-cell,并且vertical-align:middle，这样子元素可以实现垂直居中。
- text-align:center 块级元素

## visibility=hidden, opacity=0，display:none
- opacity=0，该元素隐藏起来了，但不会改变页面布局，并且，如果该元素已经绑定一些事件，如click事件，那么点击该区域，也能触发点击事件的
- visibility=hidden，该元素隐藏起来了，但不会改变页面布局，但是不会触发该元素已经绑定的事件。
- display=none，把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删除掉一样。

## 块元素和行内元素
- 块元素：独占一行，并且有自动填满父元素，可以设置margin和padding以及高度和宽度。
- 行元素：不会独占一行，width和height会失效，并且在垂直方向的padding和margin会失效。  

## 深拷贝
- 深拷贝的方法 1-2适用于一般的对象和数组 4-5适用于数组 3通用

```JS
let obj = {
    a: 1,
    arr: [1, 2]
};
let obj2 = deepCopy(obj);
obj2.a = 2
console.log(obj) // { a:1, arr: [1,2] };
2.es6
Object.assign()方法(深复制只有一层，之后为浅复制（除非再次使用Object.assign嵌套方式赋值）)
let obj = {
    a: 1,
    arr: [1, 2]
};
let obj1 = Object.assign({}, obj);
obj1.a = 2
//不变
console.log(obj) // { a:1, arr: [1,2] };
3.immutable
4.arr1=arr.slice(0) slice() 返回新数组
5.arr1=arr.concat()
var deepCopy= function(source) { 
    var result={};
    for (var key in source) {
        result[key] = typeof source[key]==='object'? deepCoypy(source[key]): source[key];
     } 
   return result; 
}
```

## 判断一个变量是否是数组
```JS
var a = []; 
// 1.基于instanceof 
a instanceof Array; 
// 2.基于constructor 
a.constructor === Array; 
// 3.基于Object.prototype.isPrototypeOf 
Array.prototype.isPrototypeOf(a); 
// 4.基于getPrototypeOf 
Object.getPrototypeOf(a) === Array.prototype; 
// 5.基于Object.prototype.toString 
Object.prototype.toString.apply(a) === '[object Array]';
// 6.Array.isArray
Array.isArray(a); // true
```
以上，除了Object.prototype.toString外，其它方法都不能正确判断变量的类型。虽然Array继承自Object，也会有 toString方法，但是这个方法有可能会被改写而达不到我们的要求，而Object.prototype则是老虎的屁股，很少有人敢去碰它的，所以能一定程度保证其“纯洁性”：)😝

* 由于每个iframe都有一套自己的执行环境，跨frame实例化的对象彼此是不共享原型链的，因此导致检测代码失效。

举个🌰
```js
var iframe = document.createElement('iframe'); //创建iframe
document.body.appendChild(iframe); //添加到body中
xArray = window.frames[window.frames.length-1].Array;
var arr = new xArray(1,2,3); // 声明数组[1,2,3]
alert(arr instanceof Array); // false
alert(arr.constructor === Array); // false
```

* 最佳写法
```JS
var arr = [1,2,3,1];
var arr2 = [{ abac : 1, abc : 2 }];
function isArrayFn(value){
if (typeof Array.isArray === "function") {
return Array.isArray(value);
}else{
return Object.prototype.toString.call(value) === "[object Array]";
}
}
console.log(isArrayFn(arr));// true
console.log(isArrayFn(arr2));// true
```

## 优化
- 按需加载（懒加载）路由
- 业务代码拆分
- 第三方库提取vendor

- 降低请求量：压缩文件图片，合并文件 减少http请求、minify/gzip压缩，webP，lazyload
- 网络图、字体图标(雪碧图)
- 缓存：HTTP协议缓存请求，离线缓存manifest
- 加快请求速度：预解析DNS，减少域名数，并行加载，上cdn分发
- 渲染：js、css优化，加载顺序，服务端渲染，pipeline

## 行内、块级、空元素 
- 行内元素：a、b、span、img、input、strong、select、label、em、button、textarea
- 块级元素：div、ul、li、dl、dt、dd、p、h1-h6、blockquote
- 常见空元素：即没有内容的HTML元素，例如：br、meta、hr、link、input、img
- 不常见空元素：area、base、col、command、embed、keygen、param、source、track、wbr

## px、em、rem的区别
px和em都是长度单位,px的只是固定的,em的值是相对的继承父类元素的字体大小。浏览器的默认字体高位16px。1em=16px;
rem单位基于html根元素的字体大小。

- vw/vh(css3) vmin(vw和vh的较小值)/vmax(vw和vh的较大值)和百分比的区别

|单位|含义|
|:---|:---|
|%|大部分相对于祖先元素，也有相对于自身的情况比如（border-radius、translate等)|
|vm/vh|相对于视窗的尺寸|

## 路由权限管理
- route render方法里进行判断 redirect or return null
- 组件内部判断
- 高阶组件
- dva监听url subscription

## node中的事件循环是什么样子的?
- event loop其实就是一个事件队列，先加入先执行，执行完一次队列，再次循环遍历看有没有新事件加入队列．执行中的叫IO events, setImmediate是在当前队列立即执行,setTimout/setInterval是把执行定时到下一个队列，process.nextTick是在当前执行完，下次遍历前执行．所以总体顺序是: IO events >> setImmediate >> setTimeout/setInterval >> process.nextTick

## 清空数组
- 直接赋值空数组
- splice(0,数组length)清空
- 设置数组length=0

## 判断数组中出现次数最多的元素
1. 临时对象数组，原数组sort排序，判断前后位是否相等。临时对象数组排序

```javascript
function f(arr){
    var temp=[];//对象数组
    var i;
    temp[0]={value:arr[0],index:1};//保存数组元素出现的次数和值
    arr.sort();
    for(i=1;i<arr.length;i++){
        if(arr[i]==arr[i-1]){
            temp[temp.length-1].index++;
        }else{//不相同则新增一个对象元素
            temp.push({index:1,value:arr[i]});
        }
    }
    temp.sort(function(a,b){//按照出现次数从大到小排列
        return b.index-a.index;
    })
    var max=temp[0].index;
    var maxV=temp[0].value;
    var second=temp[1].index;
    var secondV=temp[1].value;

    return {max,maxV,second,secondV};
}
var arr=[2,2,3,4,5,100,100,,3,1,4,4,100,100];
var {max,maxV,second,secondV}=f(arr);
console.log(max,maxV,second,secondV); // 4 100 3 4
```

2. 临时对象 判断属性名存不存在 2次遍历
```javascript
var arr = [1,1,2,2,2,2,2,3,3,3,4,4];
    var obj = {};
    for(var i = 0; i<arr.length; i++){
        if(!obj[arr[i]]){
            obj[arr[i]] = 1;
        }else {
            obj[arr[i]]++;
        }
    }
    /*
        此时的obj对象包含了所有元素出现次数的信息
        然后再遍历obj对象就可以查询出出现次数最多/最少的元素了
    */
    var maxNum = 0;
    var minNum = obj[arr[0]];  //先随意给最少的变量赋值（但保证是数组中的元素出现的次数）
    var maxEleArr = [],minEleArr = [];
    //第一次遍历找出出现次数最多和最少的值
    for(var key in obj){
        if(obj[key] > maxNum){
            maxNum = obj[key];
        }
        if(obj[key] < maxNum){
            minNum = obj[key];
        }
    }
    //第二次遍历找出所有出现次数最多和最少的元素
    for(var key in obj){
        if(obj[key] == maxNum){
            maxEleArr.push(key);
        }
        if(obj[key] == minNum){
            minEleArr.push(key);
        }
    }
    console.log(maxEleArr + ":" + maxNum );  // 2:5
    console.log(minEleArr + ":" + minNum );  // 1,4:2
```

3. reduce简写
```js
var arr = 'abcdaabc';

var info = arr
    .split('')
    .reduce((p, k) => (p[k]++ || (p[k] = 1), p), {});

console.log(info); //{ a: 3, b: 2, c: 2, d: 1 }
```

4. 临时对象简写
```js
const map = {}
const str = 'hello world'

str.split('').forEach(key => {
  map[key] = -~map[key]
})

console.log(map)
// {" ": 1, d: 1, e: 1, h: 1, l: 3, o: 2, r: 1, w: 1}
// ~i = -(i + 1)，~undefined = -1
```

## 清除浮动
清除浮动是为了清除浮动元素产生的影响。浮动元素的高度会发生坍塌，使页面后面的布局不能正常显示。
设置成浮动后，display值会自动变成block。
> 浮动会引起高度塌陷和文字环绕。
- 使用空标签清除浮动clear: both （增加了无意义的标签）
- 使用overflow: auto/hidden （使用zoom: 1兼容IE）
zoom:1的清除浮动原理?
清除浮动，触发hasLayout；
Zoom属性是IE浏览器的专有属性，它可以设置或检索对象的缩放比例。解决ie下比较奇葩的bug。
譬如外边距（margin）的重叠，浮动清除，触发ie的haslayout属性等。

来龙去脉大概如下：
当设置了zoom的值之后，所设置的元素就会就会扩大或者缩小，高度宽度就会重新计算了，这里一旦改变zoom值时其实也会发生重新渲染，运用这个原理，也就解决了ie下子元素浮动时候父元素不随着自动扩大的问题。
- 使用after伪元素清除浮动（用于非IE浏览器）

```css
.clear {
    overflow: auto;
    zoom: 1;
}

.clear::after {
    display: block;
    content: '清除浮动',
    height: 0;
    clear: both;
}

.clearfix::before, .clearfix::after {
    content: " ";
    display: table;
}
```
发现：除了clear：both用来闭合浮动的，其他代码无非都是为了隐藏掉content生成的内容，这也就是其他版本的闭合浮动为什么会有font-size：0，line-height：0。

## margin-top和padding-left根据height还是width？
width

## vue的非父子组件传递
1. vuex 2. 在同一个vue实例上调用$emit和$on

## 原生js实现拖拽

## 实现函数监听函数发布订阅模式 on emit off 方法 类似 iflux
```js
function event() {
  this.elem = document.createElement('div')
}
event.prototype.on = function (name, fn) {
  this.e = new Event(name)
  this.elem.addEventListener(name, fn.call(this, this.e, this.e.type))
}
event.prototype.emit = function(name) {
  this.elem.dispatchEvent(this.e) // 这里接受的参数必须是Event类型的，不然报错
}
event.prototype.off = function (name, fn) {
  this.e = null
}
var ee = new event()
ee.on('foo', function(){console.log(110)})
ee.emit('foo') // 110
```

## 左边固定，右边自适应布局
1. float
2. flex布局
3. 通过position 父级relative

## mysql 字符转换
```sql
-- date转字符串
select date_format(now(), '%Y-%m-%d'); 
--  date转时间戳
select unix_timestamp(now())
--  字符串转时间
select str_to_date("2019-01-15",'%Y-%m-%d %H')
-- 20190115
-- 时间戳转时间
select from_unixtime(1451997924)
-- 时间戳转字符串
select from_unixtime(1451997924,"%Y-%m-%d %H")
```

## empty()和html("")
推荐使用empty html不会清除子组件的事件

## react、vue多个class
### react
方法一：ES6 模板字符串 ``

className={title ${index === this.state.active ? 'active' : ''}}
　　

方法二：join("")

className={["title", index === this.state.active?"active":null].join(' ')}

方法三：classnames

### vue
方法一：
:class="[box,shadow]"
方法二：
:class="{box:show1,shadow:show2}"

## vue组件通信
```
// 父传子
// 父组件
<note-address :data="msg"></note-address> 

// 子组件

<div>{{ data.partment }}</div>

export default {
  //props:['data']
  props: {
    data: Object
  }
}
// 子传父
// 父组件
<note-address @new="addNote"></note-address> 

// 子组件
<button type="small" class="confirm" @click="add">设为教案</button>

methods: {
 add () {
  this.$emit('new', false)
 }
}
// 兄弟相传
// 1.创建 公共bus.js

//bus.js
import Vue from 'vue'
export default new Vue()

// 2.父组件注册两个子组件
components:{
    firstChild,
    secondChild
}

// 3.firstChild组件

<input type="button" value="点击触发" @click="elementByValue">

<script>
// 引入公共的bus，来做为中间传达的工具
  import Bus from './bus.js'
  export default {
      methods: {
      elementByValue: function () {
        Bus.$emit('val', '兄弟，收到了吗？')
      }
    }
  }
</script>

// 4.secondChild组件

<span>{{msg}}</span>

<script>
  import Bus from './bus.js'
  export default {
      mounted(){
            let self = this;
            Bus.$on('val', function (msg) {
                console.log(msg)
                self.msg = msg
            })
      }
    }
  }
</script>
```

## 网络七层协议
![tcp](http://cdn.mydearest.cn/blog/images/tcp.png)

- 应用层
- 会话层
- 传输层
- 网络层
- 数据链路层
- 物理层
- 表示层

- 应用层（HTTP，FTP，NFS，SMTP）
与其它计算机进行通讯的一个应用，它是对应应用程序的通信服务的。例如，一个没有通信功能的字处理程序就不能执行通信的代码，从事字处理工作的程序员也不关心OSI的第7层。但是，如果添加了一个传输文件的选项，那么字处理器的程序员就需要实现OSI的第7层。示例：TELNET，HTTP，FTP，NFS，SMTP等。

- 表示层
这一层的主要功能是定义数据格式及加密。例如，FTP允许你选择以二进制或ASCII格式传输。如果选择二进制，那么发送方和接收方不改变文件的内容。如果选择ASCII格式，发送方将把文本从发送方的字符集转换成标准的ASCII后发送数据。在接收方将标准的ASCII转换成接收方计算机的字符集。示例：加密，ASCII等。

- 传输层（TCP，UDP，SPX）
这层的功能包括是否选择差错恢复协议还是无差错恢复协议，及在同一主机上对不同应用的数据流的输入进行复用，还包括对收到的顺序不对的数据包的重新排序功能。示例：TCP，UDP，SPX。

- 会话层（RPC，SQL）
它定义了如何开始、控制和结束一个会话，包括对多个双向消息的控制和管理，以便在只完成连续消息的一部分时可以通知应用，从而使表示层看到的数据是连续的，在某些情况下，如果表示层收到了所有的数据，则用数据代表表示层。示例：RPC，SQL等

- 网络层（IP，IPX）
这层对端到端的包传输进行定义，它定义了能够标识所有结点的逻辑地址，还定义了路由实现的方式和学习的方式。为了适应最大传输单元长度小于包长度的传输介质，网络层还定义了如何将一个包分解成更小的包的分段方法。示例：IP，IPX等。

- 数据链路层（IP，IPX）
它定义了在单个链路上如何传输数据。这些协议与被讨论的各种介质有关。示例：ATM，FDDI等。

- 物理层（Rj45，802.3）
OSI的物理层规范是有关传输介质的特这些规范通常也参考了其他组织制定的标准。连接头、帧、帧的使用、电流、编码及光调制等都属于各种物理层规范中的内容。物理层常用多个规范完成对所有细节的定义。示例：Rj45，802.3等。

## call使用
```js
function add(a,b){
console.log(a+b)
}

function sub(a,b){
console.log(a-b)
}

add.call(sub,3,1) // 4
```

## css选择器的优先级

标签选择器 < 类选择器 < ID选择器 < 内联style < !important

## 事件队列（eventLoop）
```js
// 以下是头条的类似题目，这个考得更全面
async function a1 () {
    console.log('a1 start')
    await a2()
    console.log('a1 end')
}
async function a2 () {
    console.log('a2')
}
console.log('script start')
setTimeout(() => {
    console.log('setTimeout')
}, 0)
Promise.resolve().then(() => {
    console.log('promise1')
})
a1()
let promise2 = new Promise((resolve) => {
    resolve('promise2.then')
    console.log('promise2')
})
promise2.then((res) => {
    console.log(res)
    Promise.resolve().then(() => {
        console.log('promise3')
    })
})
console.log('script end')

// script start
// a1 start
// a2
// promise2
// script end
// promise1
// a1 end
// promise2.then
// promise3
// setTimeout
```

## vue中watch和computed的区别

- computed，计算属性，随着依赖的数据响应式地改变，用于复杂逻辑处理

- watch，命令式地监听数据变化进行操作

## 简单介绍es6

ES6在变量的声明和定义方面增加了let、const声明变量，有局部变量的概念，赋值中有比较吸引人的结构赋值，同时ES6对字符串、 数组、正则、对象、函数等拓展了一些方法，如字符串方面的模板字符串、函数方面的默认参数、对象方面属性的简洁表达方式，ES6也 引入了新的数据类型symbol，新的数据结构set和map,symbol可以通过typeof检测出来，为解决异步回调问题，引入了promise和 generator，还有最为吸引人了实现Class和模块，通过Class可以更好的面向对象编程，使用模块加载方便模块化编程，当然考虑到 浏览器兼容性，我们在实际开发中需要使用babel进行编译。

## js模块化
在ES6出现之前，js没有标准的模块化概念，这也就造成了js多人写作开发容易造成全局污染的情况，以前我们可能会采用立即执行 函数、对象等方式来尽量减少变量这种情况，后面社区为了解决这个问题陆续提出了AMD规范和CMD规范，这里不同于Node.js的 CommonJS的原因在于服务端所有的模块都是存在于硬盘中的，加载和读取几乎是不需要时间的，而浏览器端因为加载速度取决于网速， 因此需要采用异步加载，AMD规范中使用define来定义一个模块，使用require方法来加载一个模块，现在ES6也推出了标准的模块 加载方案，通过export和import来导出和导入模块。

## 图片加载成功或者失败
onload/onerror

## 递归和迭代的区别
程序调用自身称为递归，利用变量的原值推出新值称为迭代，递归的优点 大问题转化为小问题，可以减少代码量，同时应为代码精简，可读性好， 缺点就是，递归调用浪费了空间，而且递归太深容易造成堆栈的溢出。迭代的好处 就是代码运行效率好，因为时间只因循环次数增加而增加，而且没有额外的空间开销， 缺点就是代码不如递归简洁

## 原生JS操作DOM的方法有哪些？
获取节点的方法getElementById、getElementsByClassName、getElementsByTagName、 getElementsByName、querySelector、querySelectorAll,对元素属性进行操作的 getAttribute、 setAttribute、removeAttribute方法，对节点进行增删改的appendChild、insertBefore、replaceChild、removeChild、 createElement等

## setTimeout和setInterval的区别，包含内存方面的分析？
setTimeout表示间隔一段时间之后执行一次调用，而setInterval则是每间隔一段时间循环调用，直至clearInterval结束。 内存方面，setTimeout只需要进入一次队列，不会造成内存溢出，setInterval因为不计算代码执行时间，有可能同时执行多次代码， 导致内存溢出。

## addEventListener有哪些参数？
有三个参数，第一个是事件的类型，第二个是事件的回调函数，第三个是一个表示事件是冒泡阶段还是捕获阶段捕获的布尔值，true表示捕获，false表示冒泡

## 将静态资源放在其他域名的目的是什么？
这样做的主要目的是在请求这些静态资源的时候不会发送cookie，节省了流量，需要注意的是cookie是会发送给子域名的（二级域名），所以这些静态资源是不会放在子域名下的， 而是单独放在一个单独的主域名下。同时还有一个原因就是浏览器对于一个域名会有请求数的限制，这种方法可以方便做CDN。

## http状态码301和302的区别
- 301 redirect: 301 代表永久性转移(Permanently Moved) 

如何避免301跳转https(在response中header)

- 302 redirect: 302 代表暂时性转移(Temporarily Moved)

详细来说，301和302状态码都表示重定向，就是说浏览器在拿到服务器返回的这个状态码后会自动跳转到一个新的URL地址，这个地址可以从响应的Location首部中获取（用户看到的效果就是他输入的地址A瞬间变成了另一个地址B）——这是它们的共同点。他们的不同在于。301表示旧地址A的资源已经被永久地移除了（这个资源不可访问了），搜索引擎在抓取新内容的同时也将旧的网址交换为重定向之后的网址；302表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。

[重定向](http://cdn.mydearest.cn/blog/images/redirectCache.png)

## 在什么情况下a === a-1 ?
- Infinity/-Infinity

```js
let a = Infinity;

console.log(a === a - 1); // true

let b = -Infinity;

console.log(b === b - 1);  // true

console.log(Infinity + Infinity); // Infinity
console.log(Infinity - Infinity); // NaN
console.log(Infinity * Infinity); // Infinity
console.log(Infinity / Infinity); // NaN
console.log(Infinity * 0); // NaN
```

- 安全数(-2 ** 53 + 1到2 ** 53 - 1) 即Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER 处理特殊情况

```js
let a = Number.MIN_SAFE_INTEGER - 1;
console.log(a === a - 1); // true
```

- a == a -1

```js
var x = 1
var a = { x, valueOf: () => a.x }
Object.defineProperty(a, 'x', { get() { return --x } })

var set = 1
Object.defineProperty(window, "a", {
  get : function(){
    return set++
  },
  enumerable : true,
  configurable : true
});

const a = {
 times: 0,

 valueOf() {
   if (this.times & 1) {
     return 0;
   }
   this.times += 1;
   return 1;
 },
};
```

## placeholder样式设置

```css
::-webkit-input-placeholder{}    /* 使用webkit内核的浏览器 */
:-moz-placeholder{}                  /* Firefox版本4-18 */
::-moz-placeholder{}                  /* Firefox版本19+ */
:-ms-input-placeholder{}           /* IE浏览器 */
```

## 巧用currentColor自定义checkbox样式
```css
/* 无法自定义checkbox样式 */
input[type=checkbox] {
  background-color: red;
  color: red;
  border: solid red;
}
```

```css
input[type=checkbox] {
  position: relative;
  color: inherit;
}

input[type=checkbox]::before,
input[type=checkbox]::after {
  position: absolute;
  display: inline-block;
  width: 12px;
  height: 12px;
  line-height: 12px;
  border-radius: 3px;
  text-align: center;
}

input[type=checkbox]:checked::after {
  content: 'x';
  color: white;
}

input[type=checkbox]::before {
  content: ' ';
  /* 表示将背景色设置为当前的文字颜色值。 */
  background-color: currentColor;
}
```

## 增补字符不适用String.prototype.charCodeAt和String.fromCharCode
```js
'🀄'.length // 2

const str = '🀄';
console.log(str.codePointAt(0)); // 126980

console.log(String.fromCodePoint(126980)); // 🀄

console.log([..."👨‍👩‍👧‍👦"])
// ["👨", "‍", "👩", "‍", "👦", "‍", "👦"]
```

## 三次握手、四次挥手
- 建立连接-TCP的三次握手

(1) 主机向服务器发送一个建立连接的请求

(2) 服务器接到请求后发送同意连接的信号

(3) 主机接到同意连接的信号后，再次向服务器发送了确认信号

发送端首先发送一个带SYN标志的数据包给对方。接收端收到后，回传一个带有SYN/ACK标志的数据包以示传达确认信息。最后，发送端再回传一个带ACK标志的数据包，代表“握手”结束。若在握手过程中某个阶段莫名中断，TCP协议会再次以相同的顺序发送相同的数据包。

- 断开连接-TCP的四次挥手
(1) 主机向服务器发送一个断开连接的请求

(2) 服务器接到请求后发送确认收到请求的信号；(此时服务器可能还有数据要发送至主机)

(3) 服务器向主机发送断开通知；(此时服务器确认没有要向主机发送的数据)

(4) 主机接到断开通知后断开连接并反馈一个确认信号，服务器收到确认信号后断开连接

> 注意：这里的四次挥手中服务器两次向主机发送消息，第一次是回复主机已收到断开的请求，第二次是向主机确认是否断开，确保数据传输完毕。

## 幂等性

其任意多次执行对资源本身所产生的影响均与一次执行的影响相同。

## src和href引入的区别
href 表示超文本引用（hypertext reference），在 link和a 等元素上使用。
src 的内容，是页面必不可少的一部分，是引入。href 的内容，是与该页面有关联，是引用。区别就是，引入和引用。

## a.b.c.d 和 a['b']['c']['d']，哪个性能更高？
```js
var obj = {
  a:{
      b:{
        c:{
          d:1
        }
      }
    }
}
console.time()
console.log(obj.a.b.c.d)
console.timeEnd() 
// console.time()
// console.log(obj["a"]["b"]["c"]["d"])
// console.timeEnd() 
// default:1.100ms 0.964ms
```

## 对象引用
```js
var a = {n: 1};
var b = a;
a.x = a = {n: 2};

console.log(a.x) 	
console.log(b.x)
// undefined
// {n:2}
```
首先，a和b同时引用了{n:2}对象，接着执行到a.x = a = {n：2}语句，尽管赋值是从右到左的没错，但是.的优先级比=要高，所以这里首先执行a.x，相当于为a（或者b）所指向的{n:1}对象新增了一个属性x，即此时对象将变为{n:1;x:undefined}。之后按正常情况，从右到左进行赋值，此时执行a ={n:2}的时候，a的引用改变，指向了新对象{n：2},而b依然指向的是旧对象。之后执行a.x = {n：2}的时候，并不会重新解析一遍a，而是沿用最初解析a.x时候的a，也即旧对象，故此时旧对象的x的值为{n：2}，旧对象为 {n:1;x:{n：2}}，它被b引用着。
后面输出a.x的时候，又要解析a了，此时的a是指向新对象的a，而这个新对象是没有x属性的，故访问时输出undefined；而访问b.x的时候，将输出旧对象的x的值，即{n:2}。


## Doctype作用？标准模式与兼容模式各有什么区别?
1. <!DOCTYPE>声明位于HTML文档中的第一行，处于 <html> 标签之前。告知浏览器的解析器用什么文档标准解析这个文档。DOCTYPE不存在或格式不正确会导致文档以兼容模式呈现。

2. 标准模式的排版 和JS运作模式都是以该浏览器支持的最高标准运行。在兼容模式中，页面以宽松的向后兼容的方式显示,模拟老式浏览器的行为以防止站点无法工作。

3. 有两种模式： 严格模式：以该浏览器支持的最高标准运行 混杂模式：向后兼容，模拟老浏览器，防止浏览器无法兼容页面。

## HTML5 为什么只需要写 <!DOCTYPE HTML>？
- HTML5 不基于 SGML，因此不需要对DTD进行引用，但是需要doctype来规范浏览器的行为（让浏览器按照它们应该的方式来运行）；

- 而HTML4.01基于SGML,所以需要对DTD进行引用，才能告知浏览器文档所使用的文档类型。

- 新增语义化标签
```html
<header>、<footer>、<section>、<article>、<nav>、<hgroup>、<aside>、<figure>
```

- 删除的标签
```html
<big>、<u>、<font>、<basefont>、<center>、<s>、<tt>
```

- 多媒体
```html
<audio>、<video>
```

```html
<!-- 只写属性名默认为true -->
<input type="checkbox"  checked/>
<!-- 属性名="属性名"也为true -->
<input type="checkbox"  checked="checked"/>
```

## 对浏览器内核的理解？
主要分成两部分：渲染引擎(layout engineer或Rendering Engine)和JS引擎。
渲染引擎：负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入CSS等），以及计算网页的显示方式，然后会输出至显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。

JS引擎则：解析和执行javascript来实现网页的动态效果。

最开始渲染引擎和JS引擎并没有区分的很明确，后来JS引擎越来越独立，内核就倾向于只指渲染引擎。


## html5新特性？
- HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加。
绘画 canvas;
用于媒介回放的 video 和 audio 元素;
本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失;
sessionStorage 的数据在浏览器关闭后自动删除;
语意化更好的内容元素，比如 article、footer、header、nav、section;
表单控件，calendar、date、time、email、url、search;
新的技术webworker, websocket, Geolocation;

- 移除的元素：
纯表现的元素：basefont，big，center，font, s，strike，tt，u;
对可用性产生负面影响的元素：frame，frameset，noframes；

- 支持HTML5新标签：
IE8/IE7/IE6支持通过document.createElement方法产生的标签，
可以利用这一特性让这些浏览器支持HTML5新标签，
浏览器支持新标签后，还需要添加标签默认的样式。
当然也可以直接使用成熟的框架、比如html5shim;

```js
<!--[if lt IE 9]>
<script> src="http://html5shim.googlecode.com/svn/trunk/html5.js"</script>
<![endif]-->
```

- 如何区分HTML5： DOCTYPE声明新增的结构元素功能元素
语义化标签：header,footer,nav,aside,section 增强表单：为input增加了color,email,data,range等类型 存储：sessionStorage，localStorage，离线存储 多媒体：audio，vedio canvas 拖放 地理定位 webworker websocket

## 对HTML语义化的理解？
用正确的标签做正确的事情。
html语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析;
即使在没有样式CSS情况下也以一种文档格式显示，并且是容易阅读的;
搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，利于SEO;
使阅读源代码的人对网站更容易将网站分块，便于阅读维护理解。

## iframe缺点
* iframe会阻塞主页面的Onload事件；
* 搜索引擎的检索程序无法解读这种页面，不利于SEO;
* iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。

使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值。

## 多个标签页通信
WebSocket、SharedWorker；
也可以调用localstorge、cookies等本地存储方式；

localstorge另一个浏览上下文里被添加、修改或删除时，它都会触发一个事件，
我们通过监听事件，控制它的值来进行页面信息通信；
注意quirks：Safari 在无痕模式下设置localstorge值时会抛出 QuotaExceededError 的异常；

## CSS的盒子模型 border-sizing (border-box)
（1）有两种， IE 盒子模型、W3C 盒子模型；
（2）盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)；
（3）区  别： IE的content部分把 border 和 padding计算了进去;

- W3C 标准盒模型(content-box)：
![w3c](http://cdn.mydearest.cn/blog/images/w3c-box.jpg)

- IE 盒模型(border-box)：
![ie](http://cdn.mydearest.cn/blog/images/ie-box.jpg)

在 IE 盒模型中，盒子宽高不仅包含了元素的宽高，而且包含了元素的边框以及内边距。

所以在同样的设置下，IE 下的元素会看起来相对于标准盒子来的小。

## CSS选择符有哪些？哪些属性可以继承？
* 1.id选择器（ # myid）
  2.类选择器（.myclassname）
  3.标签选择器（div, h1, p）
  4.相邻选择器（h1 + p）
  5.子选择器（ul > li）
  6.后代选择器（li a）
  7.通配符选择器（ * ）
  8.属性选择器（a[rel = "external"]）
  9.伪类选择器（a:hover, li:nth-child）

* 可继承的样式： font-size font-family color, UL LI DL DD DT;

* 不可继承的样式：border padding margin width height ;

## css优先级算法
* 优先级就近原则，同权重情况下样式定义最近者为准;
* 载入样式以最后载入的定位为准;

优先级为:
同权重: 内联样式表（标签内部）> 嵌入样式表（当前文件中）> 外部样式表（外部文件中）。
!important >  id > class > tag
important 比 内联优先级高

## css3新增的伪类
举例：
p:first-of-type    选择属于其父元素的首个 <p> 元素的每个 <p> 元素。
p:last-of-type    选择属于其父元素的最后 <p> 元素的每个 <p> 元素。
p:only-of-type    选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。
p:only-child        选择属于其父元素的唯一子元素的每个 <p> 元素。
p:nth-child(2)    选择属于其父元素的第二个子元素的每个 <p> 元素。

::after            在元素之前添加内容,也可以用来做清除浮动。
::before            在元素之后添加内容
:enabled          
:disabled         控制表单控件的禁用状态。
:checked        单选框或复选框被选中。

## display有哪些值？
block           块类型。默认宽度为父元素宽度，可设置宽高，换行显示。
none            元素不显示，并从文档流中移除。
inline          行内元素类型。默认宽度为内容宽度，不可设置宽高，同行显示。
inline-block    默认宽度为内容宽度，可以设置宽高，同行显示。
list-item       象块类型元素一样显示，并添加样式列表标记。
table           此元素会作为块级表格来显示。
inherit         规定应该从父元素继承 display 属性的值。

## position有哪些值？
- absolute
  生成绝对定位的元素，相对于值不为 static 的第一个父元素进行定位。
- fixed （老IE不支持）
  生成绝对定位的元素，相对于浏览器窗口进行定位。
- relative
  生成相对定位的元素，相对于其正常位置进行定位。
- static
  默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right z-index 声明）。
- inherit
  规定从父元素继承 position 属性的值。

## CSS3有哪些新特性？
新增各种CSS选择器    （: not(.input)：所有 class 不是“input”的节点）
圆角            （border-radius:8px）
多列布局        （multi-column layout）
阴影和反射    （Shadow\Reflect）
文字特效        （text-shadow、）
文字渲染        （Text-decoration）
线性渐变        （gradient）
旋转             （transform）
缩放,定位,倾斜,动画,多背景
例如:transform:\scale(0.85,0.90)\ translate(0px,-30px)\ skew(-9deg,0deg)\Animation: 

## BFC规范(块级格式化上下文：block formatting context)的理解？

（W3C CSS 2.1 规范中的一个概念,它是一个独立容器，决定了元素如何对其内容进行定位,以及与其他元素的关系和相互作用。）
一个页面是由很多个 Box 组成的,元素的类型和 display 属性,决定了这个 Box 的类型。

不同类型的 Box,会参与不同的 Formatting Context（决定如何渲染文档的容器）,因此Box内的元素会以不同的方式渲染,也就是说BFC内部的

元素和外部的元素不会互相影响。

- 创建规则：

1. 根元素
2. 浮动元素（float不是none）
3. 绝对定位元素（position取值为absolute或fixed）
4. display取值为inline-block,table-cell, table-caption,flex, inline-flex之一的元素
5. overflow不是visible的元素

- 作用：

1. 可以包含浮动元素
2. 不被浮动元素覆盖
3. 阻止父子元素的 margin 折叠

## css权重
> 标签的权重为1，class的权重为10，id的权重为100，以下例子是演示各种定义的权重值
> 如果权重相同，则最后定义的样式会起作用，但是应该避免这种情况出现

```css
/*权重为1*/
div{
}
/*权重为10*/
.class1{
}
/*权重为100*/
#id1{
}
/*权重为100+1=101*/
#id1 div{
}
/*权重为10+1=11*/
.class1 div{
}
/*权重为10+10+1=21*/
.class1 .class2 div{
}
```

## .gitignore说明
```shell
.a # 忽略所有 .a 结尾的文件
!lib.a # 但 lib.a 除外
/TODO # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/ # 忽略 build/ 目录下的所有文件
doc/.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
规则很简单，不做过多解释，但是有时候在项目开发过程中，突然心血来潮想把某些目录或文件加入忽略规则，按照上述方法定义后发现并未生效，原因是 .gitignore 只能忽略那些原来没有被 track 的文件，如果某些文件已经被纳入了版本管理中，则修改 .gitignore 是无效的。那么解决方法就是先把本地缓存删除（改变成未 track 状态），然后再提交：

```shell
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

## 外边距合并
外边距合并指的是，当两个垂直外边距相遇时，它们将形成一个外边距。合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。
[w3school介绍网址](https://www.w3school.com.cn/css/css_margin_collapsing.asp)

## 如何优化css，提高性能？
- 使用关键选择器，过滤掉无关的规则
- 提取项目公共样式，增强可复用性、模块化编写组件
- 预处理器以及构建工具(postcss`后处理器`补充前缀、打包压缩、自动优雅降级)

## margin和padding分别适合什么场景使用？
margin是用来隔开元素与元素的间距；padding是用来隔开元素与内容的间隔。
margin用于布局分开元素使元素与元素互不相干；
padding用于元素与内容之间的间隔，让内容（文字）与（包裹）元素之间有一段

## 如何修改chrome记住密码后自动填充表单的黄色背景 ？
```css
input:-webkit-autofill, textarea:-webkit-autofill, select:-webkit-autofill {
  background-color: rgb(250, 255, 189); /*#FAFFBD;*/
  background-image: none;
  color: rgb(0, 0, 0);
}
```

## 什么是Cookie 隔离？
如果静态文件都放在主域名下，那静态文件请求的时候都带有的cookie的数据提交给server的，非常浪费流量，
所以不如隔离开。

因为cookie有域的限制，因此不能跨域提交请求，故使用非主要域名的时候，请求头中就不会带有cookie数据，
这样可以降低请求头的大小，降低请求时间，从而达到降低整体请求延时的目的。

同时这种方式不会将cookie传入Web Server，也减少了Web Server对cookie的处理分析环节，
提高了webserver的http请求的解析速度。

## ajax缓存问题

1、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("If-Modified-Since","0")。

2、在ajax发送请求前加上 anyAjaxObj.setRequestHeader("Cache-Control","no-cache")。

3、在URL后面加上一个随机数： "fresh=" + Math.random();。

4、在URL后面加上时间戳："nowtime=" + new Date().getTime();。

5、如果是使用jQuery，直接这样就可以了 $.ajaxSetup({cache:false})。这样页面的所有ajax都会执行这条语句就是不需要保存缓存记录。

模块化开发，采用立即执行函数不暴露私有成员。

## jquery.extend 与 jquery.fn.extend的区别？
- jquery.extend：为jquery类添加类方法，可以理解为添加静态方法
- jquery.fn.extend: 源码中jquery.fn = jquery.prototype，所以对jquery.fn的扩展，就是为jquery类添加成员函数

> jquery.extend扩展，需要通过jquery类来调用，而jquery.fn.extend扩展，所有jquery实例都可以直接调用。

## jquery一个对象绑定多个事件
```js
// 多个事件同一个函数
$("div").on("click mouseover", function(){});

// 多个事件不同函数
$("div").on({
click: function(){},
mouseover: function(){}
});
```

## 如何不使用loop循环，创建一个长度为100的数组，并且每个元素的值等于它的下标？
```js
Array.from(Array(100).keys())

[...Array(100).keys()]
```

接着乱序排序
```js
arr.sort( () => Math.random() > .5 ? -1 : 1 )
```

前十个数相加
```js
var [a, b, c, d, e, f, g, h, i, j, ...last] = arr3;
var total = a + b + c + d + e + f + g + h + i + j;
```

```js
const foo = ((x, f = (y = x) => x + y) => {
      let y = f();
      x = y++;
      return [x, y, f(y)]
    })(2)
// foo = [4,5,9]
```

## link与@import的区别
- link 是 html 方式，@import是css方式

- link最大限度支持并行下载，@import过多嵌套导致串行下载

- link可以通过rel="alternate stylesheet"指定候选样式

- 浏览器对link支持早于@import，可以使用@import对老浏览器隐藏样式

- @import必须在样式规则之前，可以在 css 文件中引用其他文件

- 总体来说：link 优于@import

## PNG,GIF,JPG 的区别
- GIF:

8 位像素，256 色
无损压缩
支持简单动画
支持 boolean 透明
适合简单动画

- JPEG：

颜色限于 256
有损压缩
可控制压缩质量
不支持透明
适合照片

- PNG：

有 PNG8 和 truecolor PNG
PNG8 类似 GIF 颜色上限为 256，文件小，支持 alpha 透明度，无动画
适合图标、背景、按钮

## 什么是 FOUC?如何避免

Flash Of Unstyled Content：用户定义样式表加载之前浏览器使用默认样式显示文档，用户样式加载渲染之后再从新显示文档，造成页面闪烁。解决方法：把样式表放到文档的head

## focus/blur 与 focusin/focusout的区别和联系
1. focus/blur 不冒泡，focusin/focusout 冒泡
2. focus/blur 兼容性好，focusin/focusout 在除 FireFox 外的浏览器下都保持良好兼容性，如需使用事件托管，可考虑在 FireFox 下使用事件捕获 elem.addEventListener('focus', handler, true)
3. 可获得焦点的元素：
- window
- 链接被点击或键盘操作
- 表单空间被点击或键盘操作
- 设置tabindex属性的元素被点击或键盘操作

## == 和 === 判断相等的流程

- == 
如果两个值类型相同，按照===比较方法进行比较
如果类型不同，使用如下规则进行比较
如果其中一个值是 null，另一个是 undefined，它们相等
如果一个值是数字另一个是字符串，将字符串转换为数字进行比较
如果有布尔类型，将true 转换为 1，false 转换为 0，然后用==规则继续比较
如果一个值是对象，另一个是数字或字符串，将对象转换为原始值然后用==规则继续比较
其他所有情况都认为不相等

- ===
如果两个值不是相同类型，它们不相等
如果两个值都是 null 或者都是 undefined，它们相等
如果两个值都是布尔类型 true 或者都是 false，它们相等
如果其中有一个是NaN，它们不相等
如果都是数值型并且数值相等，他们相等， -0 等于 0
如果他们都是字符串并且在相同位置包含相同的 16 位值，他它们相等；如果在长度或者内容上不等，它们不相等；两个字符串显示结果相同但是编码不同==和===都认为他们不相等
如果他们指向相同对象、数组、函数，它们相等；如果指向不同对象，他们不相等

## 对象到字符串的转换
如果对象有 toString()方法，javascript 调用它。如果返回一个原始值（primitive value 如：string number boolean）,将这个值转换为字符串作为结果
如果对象没有 toString()方法或者返回值不是原始值，javascript 寻找对象的 valueOf()方法，如果存在就调用它，返回结果是原始值则转为字符串作为结果
否则，javascript 不能从 toString()或者 valueOf()获得一个原始值，此时 throws a TypeError

## 对象到数字的转换
如果对象有valueOf()方法并且返回元素值，javascript将返回值转换为数字作为结果
否则，如果对象有toString()并且返回原始值，javascript将返回结果转换为数字作为结果
否则，throws a TypeError

## <,>,<=,>=的比较规则
所有比较运算符都支持任意类型，但是比较只支持数字和字符串，所以需要执行必要的转换然后进行比较，转换规则如下:

如果操作数是对象，转换为原始值：如果 valueOf 方法返回原始值，则使用这个值，否则使用 toString 方法的结果，如果转换失败则报错
经过必要的对象到原始值的转换后，如果两个操作数都是字符串，按照字母顺序进行比较（他们的 16 位 unicode 值的大小）
否则，如果有一个操作数不是字符串，将两个操作数转换为数字进行比较

## +运算符工作流程
如果有操作数是对象，转换为原始值
此时如果有一个操作数是字符串，其他的操作数都转换为字符串并执行连接
否则：所有操作数都转换为数字并执行加法

## question
```js
let i = 0;
if (function temp(){}) {
 i+=typeof temp;
}
console.log(i)
// '0undefined'
```

- Element.getBoundingClientRect() 方法返回元素的大小及其相对于视口的位置。
属性将会对值四舍五入取整。如果需要小数值

## 什么是构造函数调用？
使用new关键字，被称为“构造函数调用”

## 什么是构造函数？
构造函数 ，是一种特殊的方法。主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，总与new运算符一起使用在创建对象的语句中。

## querySelectorAll 与 getElementsBy 系列的区别

1. querySelectorAll 属于 W3C 中 Selectors API 规范， 而 getElementsBy 系列则属于 W3C DOM 规范。

2. querySelectorAll 方法接受参数是 CSS 选择符，当传入的是不符合 CSS 选择符规范时会抛出异常，而 getElementsBy 系列则接受的参数是单一的 className，tagName 等等。

3. 从返回值角度来看，querySelectorAll 返回的是不变的结点列表，而 getElementsBy 系列返回的是动态的结点列表。

```js
// Demo 1
var ul = document.querySelectorAll('ul')[0],
  lis = ul.querySelectorAll("li");
for(var i = 0; i < lis.length ; i++){
  ul.appendChild(document.createElement("li"));
}

// Demo 2
var ul = document.getElementsByTagName('ul')[0], 
  lis = ul.getElementsByTagName("li"); 
for(var i = 0; i < lis.length ; i++){
  ul.appendChild(document.createElement("li")); 
}
```
因为 Demo 2 中的 lis 是一个动态的结点列表， 每一次调用 lis 都会重新对文档进行查询，导致无限循环的问题。

而 Demo 1 中的 lis 是一个静态的结点列表，是一个 li 集合的快照，对文档的任何操作都不会对其产生影响。

- 普遍认为：getElementsBy 系列性能比 querySelectorAll 好

- querySelectorAll 返回值为一个 NodeList，而 getElementsBy 系列返回值为一个 HTMLCollection

## NodeList 与 HTMLCollection 区别?
1. HTMLCollection 是元素集合而 NodeList 是节点集合(即可以包含元素，文本节点，以及注释等等)。

2. node.childNodes，querySelectorAll(虽然是静态的) 返回的是 NodeList，而 node.children 和 node.getElementsByXXX 返回 HTMLCollection。

## 如何判断函数是 new 调用还是普通调用?
- instanceof
```js
function Person() {
  if(this instanceof arguments.callee) {
    console.log('new 调用');
  }else {
    console.log('普通调用');
  }
}
let p1 = new Person(); // new 调用
let p2 = Person(); // 函数调用
```

- 通过 constructor
```js
function Person() {
  if(this.constructor === arguments.callee) {
    console.log('new 调用');
  }else {
    console.log('普通调用');
  }
}
let p1 = new Person(); // new 调用
let p2 = Person(); // 函数调用 
```

## fetch会发送两次请求的原因
fetch发送post请求时总是发送两次，第一次状态码是204，第二次才会成功。 原因是，用fetch发送post请求时，第一次发送了一个options请求，询问服务器是否支持修改的请求头，如果支持，则在第二次发送真正的请求

## webworker
在html页面中，如果在执行脚本时，页面的状态是不可响应的，直到脚本执行完成后页面才变为可响应。web worker是运行在后台的js，独立于其他脚本，不会影响页面。并且通过postMessage将结果回转至主线程。

## 一句话概括RESTFUL
用url定位资源，用http描述操作

## 设备像素比（DPR device pixel redio）
1DPR = 物理像素/分辨率 也就是物理像素在屏幕上最佳的逻辑像素大小

在不缩放的情况下，一个逻辑像素就等于一个DPR，即1逻辑像素 = 物理像素/分辨率

- 自适应

 - 媒体查询
 - 百分比
 - rem(都只相对于浏览器的根元素HTML 默认情况下html元素的font-size为16px，所以1rem=16px的font-size)
为了计算方便
```css
html {
    font-size: 62.5%
}
```
这个意思是默认字体尺寸的62.5%，也就是 16*62.5%=10px，此时我们就有1rem = 10px。

## click在ios上有300ms的延迟，原因和解决办法
苹果上有一个双击缩放网页的功能，当第一次click时候浏览器会等待300ms判定用户是否要双击，所以点击一次后会延迟300ms才发生click事件。

解决办法：
1. 禁用缩放：
```js
<meta name="viewport" content="width=device-width;user-scalable=no;">
```

2. 利用fastClick：
检测到touchend事件时立即发出模拟click事件，并且把浏览器300ms后发出的点击事件给阻断掉。

## 浏览器缓存的四种实现方法
- Service Worker
- Memory Cache
- Disk Cache
- Push Cache

## 在地址栏里输入一个url到页面呈现出来，中间发生了什么？
- 输入url，查询对应ip（dns解析）
  - 先查找缓存：浏览器缓存->系统缓存->路由器缓存
  - 没有查到则查找系统的hosts文件中是否有记录，
  - 没有则查询DNS服务器
- 建立tcp连接
- 根据ip和端口号构建一个http请求，封装为tcp包，请求传输：应用层->传输层->网络层->数据链路层->物理层
- 到达服务器，服务器解析请求作出响应
- 浏览器接收到html构建DOM树（中途如果遇到js就会停止构建而去执行会下载脚本）
- 构建CSSOM树
- 合并两树为渲染树（排除了非视觉结点）
- 布局（确定个元素的尺寸和位置）
- 渲染页面

## cache-control的值有哪些
cache-control是一个通用消息头字段，被用于HTTP请求和响应中。通过指定指令来实现缓存机制，这个缓存指令是单向的，常见的取值有：
- private(默认)
- no-cache
- max-age
- must-revalidate

## 如何画0.5px的线
- 采用视口控制
```html
<meta name="viewport" content="width=device-width;initial-scale=0.5;user-scalable=no;" />
```

- border-image
需要自己制作一个0.5px的线条图片
```css
p {
    border-width: 0 0 1px 0;
    border-image: url(xxx.png) 2 0 round;
}
```

- transform
```css
p {
    transform: scaleY(0.5);
    transform-origin: 50% 100%;
}
```

## transition和animation的区别
- animation：动画，不需要触发任何事件就可以改变属性值 
- transition：过渡，需要触发某事件才能改变属性

## 关于js动画和css3动画的差异性
渲染线程分为main thread和compositor thread。如果css动画只改变transform和opacity，这时候整个css动画在compositor thread得以完成（而js动画会在main thread执行然后compositor thread进行下一步操作），特别注意的是如果改变transform和opacity是不会layout和paint的.

- 区别
 - 功能覆盖面，js比css大 实现或者重构难度不一，
 - css3比js简单，性能调优方向固定 对帧速表现不好的浏览器，
 - css3可以做到自然降级 css动画有天然事件支持 css3有兼容性问题

## 快速下载git
https://npm.taobao.org/mirrors/git-for-windows/
科学计数法 1e3 === 1000
