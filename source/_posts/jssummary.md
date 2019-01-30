---
title: JavaScript整理总结
date: 2018-06-21 19:37:27
tags:
  - 整理
categories: JS
---

JS的相关知识点比较繁杂，特此开篇整理一波，方便回顾总结查阅。

--- 
<!--more -->

## 概念
JavaScript 是一门跨平台、面向对象、基于原型的轻量级动态脚本语言。

与java的对比：

|JavaScript|Java|
|:---|:---|
|面向对象。不区分对象类型。通过原型机制继承，任何对象的属性和方法均可以被动态添加。|基于类系统。分为类和实例，通过类层级的定义实现继承。不能动态增加对象或类的属性或方法。|
|变量类型不需要提前声明(动态类型)。|变量类型必须提前声明(静态类型)。|
|不能直接自动写入硬盘。|可以直接自动写入硬盘。|

## 变量声明
### var(存在变量提升)
声明一个变量，可赋一个初始化值。

### let(let 同一变量在同一作用域不能同时声明)
声明一个块作用域的局部变量，可赋一个初始化值。

### const(const 声明时必须赋初始值,也不可以在脚本运行时重新声明)
声明一个块作用域的只读的命名常量。
const声明创建一个值的只读引用。但这并不意味着它所持有的值是不可变的，只是变量标识符不能重新分配。
如const a=[1,2,3] a[1]=4; const b={} b.name="1" 数组元素和对象属性不受保护。

## 变量的作用域
在所有函数之外声明的变量，叫做全局变量，因为它可被当前文档中的任何其他代码所访问。在函数内部声明的变量，叫做局部变量，因为它只能在该函数内部访问。全区变量是全局对象的属性，在浏览器中可以用window.xx或xx来访问。

```javascript
if(true){
    var a=5
}
console.log(a) // 5 使用let声明块级则是undefined
```
## 变量提升
JavaScript 变量的另一特别之处是，你可以引用稍后声明的变量而不会引发异常。这一概念称为变量声明提升(hoisting)；
var ok ; let 和 const 则不会存在变量提升

```javascript
1.
console.log(x === undefined); // true
var x = 3;

2.
var myvar = "my value";

(function() {
  console.log(myvar); // undefined
  var myvar = "local value";
})();

1.1 也可写作
var x;
console.log(x === undefined); // true
x = 3;

2.1
var myvar = "my value";
 
(function() {
  var myvar;
  console.log(myvar); // undefined
  myvar = "local value";
})();
```

## 函数提升
声明函数的两种方式：

```javascript
function foo(){} // 函数声明 存在函数提升且大于变量提升
var foo=function (){} // 函数表达式 var foo=function foo1(){} 函数名可写
```
此时的3种递归调用自身的方式 
- foo()
- foo1()
- arguments.callee() 

现在已经不推荐使用arguments.callee()；
原因：访问 arguments 是个很昂贵的操作，因为它是个很大的对象，每次递归调用时都需要重新创建。影响现代浏览器的性能，还会影响闭包。

## 数据类型 7种
### 原始类型
- Boolean
- null
- undefined
- String 
- Number 标识范围 -2^53~2^53-1 数字均为双精度浮点类型
- Symbol(它的实例是唯一且不可改变)
### 对象Object
对象被定义为“无序属性的集合，其属性可以包含基本值，对象或者函数”。

只有null和undefined无法拥有方法

Function Array Number Boolean String Date Math RegExp类

```javascript
typeof null === 'object' // true
null instanceof Object // false 
null instanceof null // error

[]+[] // ""
[]+{} // "[object Object]"
{}+[] // 0

!+[]+[]+![] // "truefalse"

Math.max() // -Infinity
Math.min() // Infinity
```

## MDN基本数据类型的定义
> 除 Object 以外的所有类型都是不可变的（值本身无法被改变）。例如，与 C 语言不同，JavaScript 中字符串是不可变的
>（译注：如，JavaScript 中对字符串的操作一定返回了一个新字符串，原始字符串并没有被改变）。我们称这些类型的值为“原始值”。

```javascript
var a = 'string'
a[0] = 'a'
console.log(a)  // string
```
我们通常情况下都是对一个变量重新赋值，而不是改变基本数据类型的值。在 js 中是没有方法是可以改变布尔值和数字的。
倒是有很多操作字符串的方法，但是这些方法都是返回一个新的字符串，并没有改变其原有的数据。

### 引用数据类型
引用类型（object）是存放在堆内存中的，变量实际上是一个存放在栈内存的指针，这个指针指向堆内存中的地址。每个空间大小不一样，要根据情况开进行特定的分配。

### 传值和传址
了解了基本数据类型与引用类型的区别之后，我们就应该能明白传值与传址的区别了。
在我们进行赋值操作的时候，基本数据类型的赋值（=）是在内存中新开辟一段栈内存，然后再将值赋值到新的栈中。例如:

```javascript
var a = 10;
var b = a;

a ++ ;
console.log(a); // 11
console.log(b); // 10
```
所以说，基本类型的赋值的两个变量是两个独立相互不影响的变量。

但是引用类型的赋值是传址。只是改变指针的指向，例如，也就是说引用类型的赋值是对象保存在栈中的地址的赋值，这样的话两个变量就指向同一个对象，因此两者之间操作互相有影响。例如：

```javascript
var a = {}; // a保存了一个空对象的实例
var b = a;  // a和b都指向了这个空对象

a.name = 'jozo';
console.log(a.name); // 'jozo'
console.log(b.name); // 'jozo'

b.age = 22;
console.log(b.age);// 22
console.log(a.age);// 22

console.log(a == b);// true
```

## 字面量
字面量是由语法表达式定义的常量

- 数组字面量(Array literals) []
- 布尔字面量(Boolean literals) true/false
- 浮点数字面量(Floating-point literals) 3.14
- 整数(Intergers) 5 
- 对象字面量(Object literals) {}
- RegExp literals 一个正则表达式是字符被斜线（译注：正斜杠“/”）围成的表达式 /a+b/ 
RegExp.test() RegExp.exec() string.match() 
- 字符串字面量(String literals) "1212" '1212'
JavaScript会自动将字符串字面值转换为一个临时字符串对象，调用该方法，然后废弃掉那个临时的字符串对象。你也能用对字符串字面值使用类似

```javascript
String.length的属性：
console.log("John's cat".length)
```

```javascript
var obj={
  say:funciton(){

  },
  // 简写
  say(){

  }
}
```
十进制整数字面量由一串数字序列组成，且没有前缀0。
八进制的整数以 0（或0O、0o）开头，只能包括数字0-7。
十六进制整数以0x（或0X）开头，可以包含数字（0-9）和字母 a~f 或 A~F。
二进制整数以0b（或0B）开头，只能包含数字0和1。

## 模板字符串
```javascript
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

## 布尔环境的假值
* false
* undefined
* null
* 0
* NaN
* 空字符串（""）

## try-catch 
如果finally块返回一个值，该值会是整个try-catch-finally流程的返回值，不管在try和catch块中语句返回了什么：

```javascript
function f() {
  try {
    console.log(0);
    throw "bogus";
  } catch(e) {
    console.log(1);
    return true; // this return statement is suspended
                 // until finally block has completed
    console.log(2); // not reachable
  } finally {
    console.log(3);
    return false; // overwrites the previous "return"
    console.log(4); // not reachable
  }
  // "return false" is executed now  
  console.log(5); // not reachable
}
f(); // console 0, 1, 3; returns false
```

## for of 和 for in循环
```javascript
let arr = [3, 5, 7];
arr.foo = "hello";

for (let i in arr) {
   console.log(i); // logs "0", "1", "2", "foo"
}

// 所有可枚举的属性名
for (let i of arr) {
   console.log(i); // logs "3", "5", "7" // 注意这里没有 hello
}

// for in 的循环顺序 => 遍历首先数字的可以接着按照创建顺序遍历
// 排序规则同样适用于下列API：
// Object.entries
// Object.values
// Object.keys
// Object.getOwnPropertyNames
// Reflect.ownKeys

var a = {1:1,name:'cosyer',2:2}
for (let i in a) {
  if(a.hasOwnProperty(i)){
      console.log(i)
  }
}
// 1 2 name
```

## 嵌套函数和闭包
一个闭包是一个可以自己拥有独立的环境与变量的的表达式。
- 内部函数包含外部函数的作用域。
- 内部函数只可以在外部函数中访问。
- 内部函数可以访问外部函数的参数和变量，但是外部函数却不能使用它的参数和变量。

## 多层嵌套函数
函数可以被多层嵌套。例如，函数A可以包含函数B，函数B可以再包含函数C。B和C都形成了闭包，所以B可以访问A，C可以访问B和A。因此，闭包可以包含多个作用域；他们递归式的包含了所有包含它的函数作用域。这个称之为作用域链。

```javascript
function A(x) {
  function B(y) {
    function C(z) {
      console.log(x + y + z);
    }
    C(3);
  }
  B(2);
}
A(1); // logs 6 (1 + 2 + 3)
```

在这个例子里面，C可以访问B的y和A的x。这是因为：

1. B形成了一个包含A的闭包，B可以访问A的参数和变量
2. C形成了一个包含B的闭包
3. B包含A，所以C也包含A，C可以访问B和A的参数和变量。换言之，C用这个顺序链接了B和A的作用域

反过来却不是这样。A不能访问C，因为A看不到B中的参数和变量，C是B中的一个变量，所以C是B私有的。

## 作用域链
当同一个闭包作用域下两个参数或者变量同名时，就会产生命名冲突。更近的作用域有更高的优先权，所以最近的优先级最高，最远的优先级最低。这就是作用域链。链的第一个元素就是最里面的作用域，最后一个元素便是最外层的作用域。

```javascript
function outside() {
  var x = 5;
  function inside(x) {
    return x * 2;
  }
  return inside;
}

outside()(10); // returns 20 instead of 10
```
命名冲突发生在return x上，inside的参数x和outside变量x发生了冲突。这里的作用链域是{inside, outside, 全局对象}。因此inside的x具有最高优先权，返回了20（inside的x）而不是10（outside的x）。

## 闭包
JavaScript 允许函数嵌套，并且内部函数可以访问定义在外部函数中的所有变量和函数，以及外部函数能访问的所有变量和函数。但是，外部函数却不能够访问定义在内部函数中的变量和函数。这给内部函数的变量提供了一定的安全性。此外，由于内部函数可以访问外部函数的作用域，因此当内部函数生存周期大于外部函数时，外部函数中定义的变量和函数将的生存周期比内部函数执行时间长。当内部函数以某一种方式被任何一个外部函数作用域访问时，一个闭包就产生了。

```javascript
var pet = function(name) {          //外部函数定义了一个变量"name"
  var getName = function() {            
    //内部函数可以访问 外部函数定义的"name"
    return name; 
  }
  //返回这个内部函数，从而将其暴露在外部函数作用域
  return getName;               
};
myPet = pet("Vivie");
    
myPet();                            // 返回结果 "Vivie"
```

## arguments 对象
函数的实际参数会被保存在一个类似数组的arguments对象中。

```javascript
arguments[i] // 访问
```
arguments变量只是 ”类数组对象“，并不是一个数组。称其为类数组对象是说它有一个索引编号和length属性。尽管如此，它并不拥有全部的Array对象的操作方法。

## 函数参数(默认参数、剩余参数(rest))
剩余参数语法允许将不确定数量的参数表示为数组

```javascript
function multiply(a, b = 1,...[1,2,3]) {
  return a*b;
}
```

## 箭头函数
箭头函数总是匿名的
引入箭头函数的原因

1. 更简洁的语法
2. 捕捉闭包上下文的this值
```javascript
function Person(){
  this.age = 0;

  setInterval(() => {
    this.age++; // |this| properly refers to the person object
  }, 1000);
}

var p = new Person();
```

## 扩展语句
适用于对象，数组
```javascript
function f(x, y, z) { }
var args = [0, 1, 2];
f(...args);
```
## 临时对象
你可以在String字面值上使用String对象的任何方法—JavaScript自动把String字面值转换为一个临时的String对象, 然后调用其相应方法,最后丢弃销毁此临时对象.在String字面值上也可以使用String.length属性.

```javascript
var s1 = "2 + 2"; // Creates a string literal value
var s2 = new String("2 + 2"); // Creates a String object
eval(s1); // Returns the number 4
eval(s2); // Returns the string "2 + 2"
Number(null) // 0
```

## 数组方法

### concat() 连接两个数组并返回一个新的数组。
```javascript
var myArray = new Array("1", "2", "3");
myArray = myArray.concat("a", "b", "c"); 
// myArray is now ["1", "2", "3", "a", "b", "c"]
```

###  join() 将数组的所有元素连接成一个字符串。
```javascript
var myArray = new Array("Wind", "Rain", "Fire");
var list = myArray.join(" - "); // list is "Wind - Rain - Fire"
```

### push() 在数组末尾添加一个或多个元素，并返回数组操作后的长度。
```javascript
var myArray = new Array("1", "2");
myArray.push("3"); // myArray is now ["1", "2", "3"]
```

### pop() 从数组移出最后一个元素，并返回该元素。
```javascript
var myArray = new Array("1", "2", "3");
var last = myArray.pop(); 
// myArray is now ["1", "2"], last = "3"
```

### shift() 从数组移出第一个元素，并返回该元素。
```javascript
var myArray = new Array ("1", "2", "3");
var first = myArray.shift(); 
// myArray is now ["2", "3"], first is "1"
```

### unshift()在数组开头添加一个或多个元素，并返回数组的新长度。
```javascript
var myArray = new Array ("1", "2", "3");
myArray.unshift("4", "5"); 
// myArray becomes ["4", "5", "1", "2", "3"]
```

### slice(开始索引，结束索引) 从数组提取一个片段，并作为一个新数组返回。 
```javascript
var myArray = new Array ("a", "b", "c", "d", "e");
myArray = myArray.slice(1, 4); // until index 3, returning [ "b", "c", "d"]
```
slice 方法可以用来将一个类数组（Array-like）对象/集合转换成一个新数组。只需将该方法绑定到这个对象上。 一个函数中的 arguments 就是一个类数组对象的例子。

```javascript
Array.prototype.slice.call({0:1,1:3,length:2});
```

### splice(index, count_to_remove, addElement1, addElement2, ...)从数组移出一些元素，（可选）并替换它们。
```javascript
var myArray = new Array ("1", "2", "3", "4", "5");
myArray.splice(1, 3, "a", "b", "c", "d"); 
// myArray is now ["1", "a", "b", "c", "d", "5"]
```

### reverse() 颠倒数组元素的顺序：第一个变成最后一个，最后一个变成第一个。
```javascript
var myArray = new Array ("1", "2", "3");
myArray.reverse(); 
// transposes the array so that myArray = [ "3", "2", "1" ]
```

### sort() 给数组元素排序。
```javascript
var arr=[2,1,3]
arr.sort() // [1,2,3]
```
sort() 也可以带一个回调函数来决定怎么比较数组元素。这个回调函数比较两个值，并返回3个值中的一个：
- 如果 a 小于 b ，返回 -1(或任何负数) 降序
- 如果 a 大于 b ，返回 1 (或任何正数) 升序
- 如果 a 和 b 相等，返回 0。

### indexOf(searchElement[, fromIndex]) 在数组中搜索searchElement 并返回第一个匹配的索引。
```javascript
var a = ['a', 'b', 'a', 'b', 'a'];
console.log(a.indexOf('b')); // logs 1
// Now try again, starting from after the last match
console.log(a.indexOf('b', 2)); // logs 3
console.log(a.indexOf('z')); // logs -1, because 'z' was not found
```

### lastIndexOf(searchElement[, fromIndex]) 和 indexOf 差不多，但这是从结尾开始，并且是反向搜索。

### forEach() 循环数组 不定的顺序 不能用break,return false跳出循环遍历  

### map() 循环数组返回新数组
```javascript
var a1 = ['a', 'b', 'c'];
var a2 = a1.map(function(item) { return item.toUpperCase(); });
console.log(a2); // logs A,B,C
```

### find() 找到满足条件的第一个元素

### filter() 循环数组返回符合条件的元素
```javascript
var a1 = ['a', 10, 'b', 20, 'c', 30];
var a2 = a1.filter(function(item) { return typeof item == 'number'; });
console.log(a2); // logs 10,20,30
```
### every() 循环数组 如果全部元素满足条件则返回true 否则返回false

### some() 循环数组 只要有一项满足条件则返回true 全部不满足返回false

### reduce() 迭代 使用回调函数 callback(firstValue, secondValue) 把数组列表计算成一个单一值 reduceRight() 从右边开始
```javascript
var a = [10, 20, 30];
var total = a.reduce(function(first, second) { return first + second; }, 0);
console.log(total) // Prints 60
```

## Map简单的键值对集合
```javascript
var sayings = new Map();
sayings.set('dog', 'woof');
sayings.set('cat', 'meow');
sayings.set('elephant', 'toot');
sayings.size; // 3
sayings.get('fox'); // undefined
sayings.has('bird'); // false
sayings.delete('dog');
sayings.has('dog'); // false

for (var [key, value] of sayings) {
  console.log(key + ' goes ' + value);
}
// "cat goes meow"
// "elephant goes toot"

sayings.clear();
sayings.size; // 0
```
new Map() 参数可以是一个数组或者其他 iterable 对象，其元素或为键值对，或为两个元素的数组。 每个键值对都会添加到新的 Map。null 会被当做 undefined。

**Object和Map的比较**

1. 一般地，objects会被用于将字符串类型映射到数值。Object允许设置键值对、根据键获取值、删除键、检测某个键是否存在。而Map具有更多的优势。
2. Object的键均为Strings类型，在Map里键可以是任意类型。
3. 必须手动计算Object的尺寸，但是可以很容易地获取使用Map的尺寸。
4. Map的遍历遵循元素的插入顺序。
5. Object有原型，所以映射中有一些缺省的键。（可以理解为map = Object.create(null)）。

如果键在运行时才能知道，或者所有的键类型相同，所有的值类型相同，那就使用Map。
如果需要将原始值存储为键，则使用Map，因为Object将每个键视为字符串，不管它是一个数字值、布尔值还是任何其他原始值。
如果需要对个别元素进行操作，使用Object。

## Set集合
```javascript
var mySet = new Set();
mySet.add(1);
mySet.add("some text");
mySet.add("foo");

mySet.has(1); // true
mySet.delete("foo");
mySet.size; // 2

for (let item of mySet) console.log(item);
// 1
// "some text"

mySet2 = new Set([1,2,2,4]);
Array.from(mySet2);  // [1,2,3] 常用来去重

// argument对象（类数组）转成数组
// Array.from({0:111,1:222,2:333,length:3}) [111,222,333]
```

**Array和Set的比较**

1. 数组中用于判断元素是否存在的indexOf 函数效率低下。
2. Set对象允许根据值删除元素，而数组中必须使用基于下标的 splice 方法。
3. 数组的indexOf方法无法找到NaN值。
4. Set对象存储不重复的值，所以不需要手动处理包含重复值的情况。
5. 数组是特殊的对象,对象是关联数组 字符串是特殊的数组
6. 方括弧取值为动态判定[]，数字非有效的js标识符   

## setter和getter (get set修饰function)
```javascript
var o = {
  a: 7,
  get b() { 
    return this.a + 1;
  },
  set c(x) {
    this.a = x / 2
  }
};

console.log(o.a); // 7
console.log(o.b); // 8 取b值时调用
o.c = 50;         // 给c设置值调用
console.log(o.a); // 25
-----------------------
var o = {
  a: 7,
  b:function(){ 
    return this.a + 1;
  }
};

console.log(o.b()); // 8
```

## 访问所有可枚举对象属性
1. for in 无序
2. Object.keys() 不包括原型的属性名数组
3. Object.getOwnPropertyNames()

## Symbol(原始数据类型) 不可枚举的 符号类型
```javascript
var  myPrivateMethod  = Symbol(); // 不能使用new Symbol()创建，它是一个不完整的类
this[myPrivateMethod] = function() {...};
```

for in 和 Object.getOwnPropertyNames()访问不到，只能通过myPrivateMethod或者Object.getOwnPropertySymbols()来访问

```javascript
Symbol("foo") !== Symbol("foo") // true
const foo = Symbol()
const bar = Symbol()
typeof foo === "symbol" // true
typeof bar === "symbol" // true
let obj = {}
obj[foo] = "foo"
obj[bar] = "bar"
JSON.stringify(obj) // {}
Object.keys(obj) // []
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [ foo, bar ]
```

## Proxy 代理
`let p= new Proxy(target,handler)`
- target
用Proxy包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。

- handler
一个对象，其属性是当执行一个操作时定义代理的行为的函数。

```javascript
// 设置缺省值
let handler = {
    get: function(target, name){
        return name in target ? target[name] : 37;
    }
};

let p = new Proxy({}, handler);

p.a = 1;
p.b = undefined;

console.log(p.a, p.b);    // 1, undefined

console.log('c' in p, p.c);    // false, 37

// 转发代理
let target = {};
let p = new Proxy(target, {});

p.a = 37;   // 操作转发到目标

console.log(target.a);    // 37. 操作已经被正确地转发

// demo
let book  = {"name":"《ES6基础系列》","price":56 };
let proxy = new Proxy(book,{
    get:function(target,property){
        if(property === "name"){
            return "《入门到懵逼》";
        }else{
            return target[property];
        }
    },
    set:function(target,property,value){
        if(property === 'price'){
            target[property] = 56;
        }
    }
})
```

## 生成器 generator 
function* 来修饰GeneratorFunction函数

```javascript
function* idMaker() {
  var index = 0;
  while(true)
    yield index++;
}

var gen = idMaker();

console.log(gen.next().value); // 0
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
// ...
```

对象实现迭代行为

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};

for (let value of myIterable) { 
    console.log(value); 
}
// 1
// 2
// 3

or
[...myIterable]; // [1, 2, 3]
```

## 3行实现Promise
```javascript
function Promise (fn) {
    this.then = cb => this.cb = cb
    this.resolve = data => this.cb(data)
    fn(this.resolve)
}
// 使用
new Promise((resolve) => {
    setTimeout(() => {
        resolve("延时执行")
    }, 1000)
}).then((data) => {
    console.log(data)
})
```

## 现一个函数，将一个字符串中的空格替换成“%20”
```javascript
function convertSpace2percent20(str){
  return str.replace(/\s+?/g,"%20"); //开启非贪婪模式
}

convertSpace2percent20("hellow world")
// "hellow%20world"
```

## var obj = { 1: "Hello", 2: "World" }打印Hello World
```javascript
var obj = {
    1: "Hello",
    2: "World"
};

var str = '';

// 方法1
for (var i = 1; i < 3; i++) {
	str += obj[i] + " "; //注意不能使用obj.i
}

// 方法2
for (var key of Object.keys(obj)) {
	str += obj[key] + " ";
}

// 方法3
for (let i in obj) {
  str += obj[i] + " "; 
}

// 方法4
Object.values(obj).join(" ")
```

## 循环
- while - 只要指定的条件成立，则循环执行代码块
- do...while - 首先执行一次代码块，然后在指定的条件成立时重复这个循环
- for - 循环执行代码块指定的次数
- foreach - 根据数组中每个元素来循环代码块
### forEach等函数的第二个参数的用法
forEach函数用得平时用得比较多，但是从来没想到forEach函数还有第二个参数。

简单点来说，就是我们可以直接使用第二个参数来指定函数里的this的值，而不需要使用箭头函数或者在外面定义var that = this;等操作。

```javascript
var obj = {
    name: "小明",
    say: function() {
        console.log(this.name); // "小明"
    },
    think: function() {
        var arr = [1];
        arr.forEach(function(item) {
            console.log(this); // window
        })
        console.log('---------')
        arr.forEach(function(item) {
            console.log(this); // obj
        }, this)
    }
}

obj.say();
obj.think();

[3,2,4,1].sort((a,b)=>{
    return a > b ? 1 : -1; // return a-b
})
```

## 数组扁平化(将多维数组展开为一维数组)
```javascript
//  es6
const flattenES6 = (arr) => {
  let result = [];
  arr.forEach((item, i, arr) => {
    if (Array.isArray(item)) {
      result = result.concat(flatten(item));
    } else {
      result.push(arr[i])
    }
  })
  return result;
};
console.log(flattenES6([1, [2, [3, [4]], 5]]))

// es5
function flattenES5(arr) {
  var result = [];
  for (var i = 0, len = arr.length; i < len; i++) {
    if (Array.isArray(arr[i])) {
      result = result.concat(flatten(arr[i]))
    } else {
      result.push(arr[i])
    }
  }
  return result;
}
console.log(flattenES5([1, [2, [3, [4]], 5]]))

[1, [2, [3, [4]], 5]] // 1,2,3,4,5

// toString方法
// es6
const flattenES6 = (arr) => arr.toString().split(',').map((item) => +item);
console.log(flattenES6([1, [2, [3, [4]], 5]]))

// es5
function flattenES5(arr) {
  return arr.toString().split(',').map(function (item) {
    return +item;
  });
}
console.log(flattenES5([1, [2, [3, [4]], 5]]))
```