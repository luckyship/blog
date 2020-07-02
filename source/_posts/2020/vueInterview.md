---
title: vue面试题记录
tags:
  - vue
  - 面试
copyright: true
comments: true
date: 2020-04-23 00:10:18
categories: JS
photos:
---

## Vue面试问题

---
<!--more-->

### vue 双向绑定的原理
采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

### v-show和v-if有什么区别

- v-if
v-if 是真正的条件渲染，因为它会确保在切换过程中条件块内的事件监听和子组件适当地被销毁和重建，也是惰性的，如果在初始渲染条件为假时，则什么也不做——直到条件第一次变为真时才开始渲染条件块。

- v-show
v-show就简单得多，不管初始条件是什么，元素总是会被渲染，并且只是简单地基于css的display进行切换。

所以，v-if适用于切换不频繁的场景，v-show适用于切换频繁的场景。

### class和style如何动态绑定

class可以通过对象语法和数组语法进行动态绑定：
- 对象语法
```js
<div v-bind:class="{active: isActive, 'text-danger': hasError }"></div>
data: {
    isActive: true,
    hasError: false
}
```

- 数组语法
```js
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
data: {
    activeClass: 'active',
    errorClass: 'text-danger'
}
```
style也可以通过对象语法和数组语法进行动态绑定

### 理解vue里的单向数据流
所有的prop都使得其父子prop之间形成一个单向下行绑定：父级prop的更新会向下流动到子组件中，但是反过来不行，这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流难以解释.

额外地，每次父级组件发生更新时，子组件中的所有prop都会刷新为最新的值，这意味着你不应该在一个子组件内部改变prop，如果你这样做了，vue会在浏览器的控制台发出警告，子组件想修改时，只能通过$emit派发一个自定义事件，父组件接收到后，由父组件修改.

### computed和watch的区别和运用场景
- computed：是计算属性，依赖其他属性值，并且computed的值有缓存，只有它依赖的属性值发生改变时下一次获取computed的值时候才会重新计算computed的值。

- watch：更多的是观察作用，类似于某些数据的监听回调，每当监听的数据发生变化时都会执行回调进行后续操作。

运用场景：

- 当我们需要进行数值计算，并依赖于其他数据时，应该使用computed，因为可以利用computed的缓存特性，避免每次获取值时都要重新计算。

- 但我们需要在数据变化时执行异步或开销较大的操作时应该使用watch，使用watch选项允许我们执行异步操作，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态，这些都是计算属性无法做到的。

### 直接给一个数组项赋值，vue能检测到吗
由于js的限制，vue不能检测到以下数组的变动：

- 当你利用索引直接设置一个数组项时，例如vm.item[indexOfItem] = newValue
- 当你修改数组的长度时，例如vm.items.length = newLength

为了解决第一个问题，vue提供了以下操作方法：
```js
Vue.set(vm.items, indexOfItem, newValue)

Vue.$set(vm.items, indexOfItem, newValue)

Vue.items.splice(indexOfItem, 1, newValue)
```

为了解决第二个问题，vue提供了以下操作方法：
```js
vm.items.splice(newLength)
```

### vue生命周期的理解（10个）
- 生命周期是什么
vue实例有一个完整的生命周期，也就是从开始创建，初始化数据，编译模板，挂载dom->渲染更新->渲染卸载等一些过程，我们称这是vue的生命周期

- 各个生命周期的作用
  - beforeCreate：组件被创建之初，组件的属性生效之前
  - created：组件实例已经完全创建，属性也绑定，但是真实的dom还没有生成，$el还不能用
  - beforeMount：在挂载开始之前被调用，相关的render函数首次被调用
  - mounted：el被新创建的vm.$el替换，并挂载到实例上去后调用该钩子
  - beforeUpdate：组件数据更新之前调用，发生在虚拟dom打补丁之前 
  - updated：组件数据更新之后
  - activited：keep-alive专属，组件被激活时调用
  - deactivated：keep-alive专属，组件被销毁时调用
  - beforeDestroy：组件被销毁前
  - destroyed：组件被销毁后调用

- _init_
  - initLifecycle/Event，往vm上挂载各种属性
  - callHook: beforeCreate: 实例刚创建
  - initInjection/initState: 初始化注入和 data 响应性
  - created: 创建完成，属性已经绑定， 但还未生成真实dom
  - 进行元素的挂载： $el / vm.$mount()
  - 是否有template: 解析成render function
    - *.vue文件: vue-loader会将<template>编译成render function
  - beforeMount: 模板编译/挂载之前
  - 执行render function，生成真实的dom，并替换到dom tree中
  - mounted: 组件已挂载

- update:
  - 执行diff算法，比对改变是否需要触发UI更新
  - flushScheduleQueue
    - watcher.before: 触发beforeUpdate钩子		
    - watcher.run(): 执行watcher中的 notify，通知所有依赖项更新UI
  - 触发updated钩子: 组件已更新

- actived / deactivated(keep-alive): 不销毁，缓存，组件激活与失活
- destroy:
  - beforeDestroy: 销毁开始
  - 销毁自身且递归销毁子组件以及事件监听
    - remove(): 删除节点
    - watcher.teardown(): 清空依赖
    - vm.$off(): 解绑监听
  - destroyed: 完成后触发钩子

```js
new Vue({})

// 初始化Vue实例
function _init() {
	 // 挂载属性
    initLifeCycle(vm) 
    // 初始化事件系统，钩子函数等
    initEvent(vm) 
    // 编译slot、vnode
    initRender(vm) 
    // 触发钩子
    callHook(vm, 'beforeCreate')
    // 添加inject功能
    initInjection(vm)
    // 完成数据响应性 props/data/watch/computed/methods
    initState(vm)
    // 添加 provide 功能
    initProvide(vm)
    // 触发钩子
    callHook(vm, 'created')
		
	 // 挂载节点
    if (vm.$options.el) {
        vm.$mount(vm.$options.el)
    }
}

// 挂载节点实现
function mountComponent(vm) {
	 // 获取 render function
    if (!this.options.render) {
        // template to render
        // Vue.compile = compileToFunctions
        let { render } = compileToFunctions() 
        this.options.render = render
    }
    // 触发钩子
    callHook('beforeMounte')
    // 初始化观察者
    // render 渲染 vdom， 
    vdom = vm.render()
    // update: 根据 diff 出的 patchs 挂载成真实的 dom 
    vm._update(vdom)
    // 触发钩子  
    callHook(vm, 'mounted')
}

// 更新节点实现
funtion queueWatcher(watcher) {
	nextTick(flushScheduleQueue)
}

// 清空队列
function flushScheduleQueue() {
	 // 遍历队列中所有修改
    for(){
	    // beforeUpdate
        watcher.before()
         
        // 依赖局部更新节点
        watcher.update() 
        callHook('updated')
    }
}

// 销毁实例实现
Vue.prototype.$destory = function() {
	 // 触发钩子
    callHook(vm, 'beforeDestory')
    // 自身及子节点
    remove() 
    // 删除依赖
    watcher.teardown() 
    // 删除监听
    vm.$off() 
    // 触发钩子
    callHook(vm, 'destoryed')
}
```

### vue父子组件生命周期钩子函数的执行顺序
- 加载渲染过程
父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted

- 子组件更新过程
父beforeUpdate->子beforeUpdate->子updated->父updated

- 父组件更新过程
父beforeUpdate->父updated

- 销毁过程
父beforeDestroy->子beforeDestroy->子destroyed->父destroy

### 在哪个生命周期内调用异步请求
可以在函数created，beforeMount，mounted中进行调用，因为在这三个钩子函数中data已经可以创建，可以将服务端返回的数据进行赋值，但是比较推荐在created钩子函数中调用异步请求，因为：

- 能更快的获取到服务端数据，减少页面loading时间
- ssr不支持beforeMount，mounted钩子函数，所以放在created中有助于一致性

### 在什么阶段才能访问操作DOM
在钩子函数mounted被调用之前，vue已经把编译好的模板挂载到页面上，所以在mounted中可以访问操作dom，vue具体的生命周期。

### 父组件可以监听到子组件的生命周期吗
- 手动设置$emit来发布监听
```js
// parent
<Child @mounted="fn" />
// child
mounted() {
    this.$emit("mounted");
}
```

- @hook
```js
// parent
<Child @hook:mounted="fn" />
fn() {
    console.log('get')
}
// child
mounted() {
    console.log('emit');
```

### 谈谈你对keep-alive的了解
keep-alive是vue内置的一个组件，可以使被包含的组件保留状态，避免重复渲染，其有以下特性：

- 一般结合路由和动态组件使用，用于缓存组件
- 提供include和exclude属性，两者都支持字符串或正则表达式，include表示只有名字匹配的组件会被缓存，exclude表示任何名称匹配的组件都不会被缓存，其中exclude的优先级比include高
- 对应两个钩子函数actived和deactivated

### 组件中的data为什么是个函数
因为组件是拿来复用的，且js里的对象是引用关系，如果组件中的data是一个对象，那么这样作用域没有隔离，子组件中的data属性值会相互影响，如果组件中的data是一个函数，那么每个实例可以维护一份被返回对象的独立的拷贝，组件实例之间的data属性值不会互相影响，而new Vue的实例是不会被复用的，因此不存在引用对象的问题。

### v-model的原理
我们在vue项目中主要使用v-model指令在表单input，textarea，select等元素上创建双向绑定，我们知道v-model本质上不过是语法糖，v-model在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text和textarea元素使用value属性和input事件
- checkbox和radio使用checked和change
- select字段将value作为prop并将change作为事件

### vue组件间通信有哪几种方式（6种）
- props和$emit
适用父子组件通信

- ref和$parent $children
ref：如果在普通dom上使用，引用指向的就是dom元素，如果用在子组件上，引用就指向组件实例 $parent/$children：访问父子实例

- EventBus（$emit/$on）
这种方法通过一个空的vue实例作为中央事件总线（事件中心），用它来触发事件和监听事件，从而实现任何组件间的通信，包括父子，隔代，兄弟组件

- $attrs/$listeners
$attrs：包含了父作用域里不被prop所识别（且获取）的特性绑定（class和style除外）。当一个组件没有声明任何prop时，这里会包含所有父作用域的绑定（class和style除外），并且可以通过v-bind="$attrs"传入内部组件。通常配合inheritAttrs选项一起使用

$listeners：包含了父作用域中的v-on事件监听器，它可以通过v-on="$listeners"传入内部组件

- provider、inject
祖先组件通过provider来提供变量，然后在子孙组件中通过inject来注入变量，provide / inject API主要解决了跨级组件间的通信问题，不过他的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立一种主动提供和依赖注入的关系

- vuex
vuex是一个专为vue应用程序开发的状态管理模式，每一个vuex应用的核心就是store，store基本上就是一仓库，它包含着你的应用中大部分的状态

vuex的状态存储是响应式的，当vue从store中读取状态时候，若store中的状态发生变化，那么相应的组件也会相应的得到高效更新

改变store中的状态的唯一的途径就是显式地提交mutation，这样使我们可以方便地跟踪每一个状态的变化

### 你使用过vuex吗
vuex是一个专门为vue应用程序开发的状态管理模式，每一个vuex应用的核心是store，store基本上就是一个容器，它包含着你的应用中大部分的状态（state）

主要包括以下几个模块：

- state：定义了应用状态的数据结构，可以在这里设置默认的初始状态
- Getter：允许组件从State中获取数据，mapGetters辅助函数仅仅是将store中的getter映射到局部计算属性
- Mutation：是唯一更改store中状态的方法，且必须是同步函数
- Action：用于提交mutation，而不是直接更改状态，可以包含任意的异步操作
- Module：允许将单一的Store拆分成多个store且同时保存在单一的状态树里

### vue SSR
vue是构建客户端应用程序的框架，默认情况下，可以在浏览器中输出vue组件，进行生成dom和操作dom，然而，也可以将同一个组件渲染为服务端的html字符串，将他们直接发送到客户端，然后将这些静态标记激活为客户端上可以交互的应用程序。

即ssr的意思就是vue在服务端完成将标签渲染成整个html片段的工作，然后将片段直接返回给客户端使用

- ssr优点：

  - 更好的seo：因为spa页面的内容是通过ajax获取，而搜索引擎爬取工具并不会等待ajax一步完成后再抓取页面内容，所以在spa中是抓取不到页面通过ajax获取到的内容；而ssr是直接由服务器返回已经渲染好的页面（数据已经包含在页面中），所以搜索引擎爬取工具可以抓取到渲染好的页面

  - 更快的内容到达时间（首屏加载快）：spa会等待所有vue编译后的js文件都下载完成后，才开始进行也免得渲染，文件下载需要一定的时间等，所以首屏加载需要时间，而ssr直接由服务器渲染好页面返回显示，无需等待js文件再去渲染，所以ssr有更快的内容到达时间

- ssr缺点：

  - 更多的开发条件限制：例如服务端渲染只支持beforeCreate和created两个钩子函数，这会导致一些外部扩展库需要特殊处理，才能在服务端渲染程序中运行；并且与可以部署在任何静态文件服务器上的完全静态单页面应用程序spa不同，服务端渲染应用程序，需要处于nodejs server中才能运行

  - 更多的服务器负载

### nextTick
在下次dom更新循环结束之后执行延迟回调，可用于获取更新后的dom状态。

- 新版本中默认是microtasks, v-on中会使用macrotasks

###  数据响应(数据劫持)
数据响应的实现由两部分构成: 观察者( watcher ) 和 依赖收集器( Dep )，其核心是 defineProperty 这个方法，它可以重写属性的 get 与 set 方法，从而完成监听数据的改变。

- Observe (观察者)观察 props 与 state
  - 遍历 props 与 state，对每个属性创建独立的监听器( watcher )

- 使用 defineProperty 重写每个属性的 get/set(defineReactive）
  - get: 收集依赖
  - Dep.depend()
    - watcher.addDep()

  - set: 派发更新
    - Dep.notify()
    - watcher.update()
    - queenWatcher()
    - nextTick
    - flushScheduleQueue
    - watcher.run()
    - updateComponent()

```js
let data = {a: 1}
// 数据响应性
observe(data)

// 初始化观察者
new Watcher(data, 'name', updateComponent)
data.a = 2

// 简单表示用于数据更新后的操作
function updateComponent() {
    vm._update() // patchs
}

// 监视对象
function observe(obj) {
	// 遍历对象，使用 get/set 重新定义对象的每个属性值
  Object.keys(obj).map(key => {
      defineReactive(obj, key, obj[key])
  })
}

function defineReactive(obj, k, v) {
    // 递归子属性
    if (type(v) == 'object') observe(v)
    
    // 新建依赖收集器
    let dep = new Dep()
    // 定义get/set
    Object.defineProperty(obj, k, {
        enumerable: true,
        configurable: true,
        get: function reactiveGetter() {
        	  // 当有获取该属性时，证明依赖于该对象，因此被添加进收集器中
            if (Dep.target) {
                dep.addSub(Dep.target)
            }
            return v
        },
        // 重新设置值时，触发收集器的通知机制
        set: function reactiveSetter(nV) {
            v = nV
            dep.nofify()
        },
    })
}

// 依赖收集器
class Dep {
    constructor() {
        this.subs = []
    }
    addSub(sub) {
        this.subs.push(sub)
    }
    notify() {
        this.subs.map(sub => {
            sub.update()
        })
    }
}

Dep.target = null

// 观察者
class Watcher {
    constructor(obj, key, cb) {
        Dep.target = this
        this.cb = cb
        this.obj = obj
        this.key = key
        this.value = obj[key]
        Dep.target = null
    }
    addDep(Dep) {
        Dep.addSub(this)
    }
    update() {
        this.value = this.obj[this.key]
        this.cb(this.value)
    }
    before() {
        callHook('beforeUpdate')
    }
}
```

### 虚拟dom原理实现
- 创建 dom 树

- 树的diff，同层对比，输出patchs(listDiff/diffChildren/diffProps)
  - 没有新的节点，返回
  - 新的节点tagName与key不变， 对比props，继续递归遍历子树
    - 对比属性(对比新旧属性列表):
      - 旧属性是否存在与新属性列表中
      - 都存在的是否有变化
      - 是否出现旧列表中没有的新属性

  - tagName和key值变化了，则直接替换成新节点

- 渲染差异
  - 遍历patchs， 把需要更改的节点取出来
  - 局部更新dom

```js
// diff算法的实现
function diff(oldTree, newTree) {
	 // 差异收集
    let pathchs = {}
    dfs(oldTree, newTree, 0, pathchs)
    return pathchs
}

function dfs(oldNode, newNode, index, pathchs) {
    let curPathchs = []
    if (newNode) {
        // 当新旧节点的 tagName 和 key 值完全一致时
        if (oldNode.tagName === newNode.tagName && oldNode.key === newNode.key) {
        	  // 继续比对属性差异
            let props = diffProps(oldNode.props, newNode.props)
            curPathchs.push({ type: 'changeProps', props })
            // 递归进入下一层级的比较
            diffChildrens(oldNode.children, newNode.children, index, pathchs)
        } else {
        	  // 当 tagName 或者 key 修改了后，表示已经是全新节点，无需再比
            curPathchs.push({ type: 'replaceNode', node: newNode })
        }
    }

	 // 构建出整颗差异树
    if (curPathchs.length) {
    		if(pathchs[index]){
    			pathchs[index] = pathchs[index].concat(curPathchs)
    		} else {
    			pathchs[index] = curPathchs
    		}
    }
}

// 属性对比实现
function diffProps(oldProps, newProps) {
    let propsPathchs = []
    // 遍历新旧属性列表
    // 查找删除项
    // 查找修改项
    // 查找新增项
    forin(olaProps, (k, v) => {
        if (!newProps.hasOwnProperty(k)) {
            propsPathchs.push({ type: 'remove', prop: k })
        } else {
            if (v !== newProps[k]) {
                propsPathchs.push({ type: 'change', prop: k , value: newProps[k] })
            }
        }
    })
    forin(newProps, (k, v) => {
        if (!oldProps.hasOwnProperty(k)) {
            propsPathchs.push({ type: 'add', prop: k, value: v })
        }
    })
    return propsPathchs
}

// 对比子级差异
function diffChildrens(oldChild, newChild, index, pathchs) {
		// 标记子级的删除/新增/移动
    let { change, list } = diffList(oldChild, newChild, index, pathchs)
    if (change.length) {
        if (pathchs[index]) {
            pathchs[index] = pathchs[index].concat(change)
        } else {
            pathchs[index] = change
        }
    }

	 // 根据 key 获取原本匹配的节点，进一步递归从头开始对比
    oldChild.map((item, i) => {
        let keyIndex = list.indexOf(item.key)
        if (keyIndex) {
            let node = newChild[keyIndex]
            // 进一步递归对比
            dfs(item, node, index, pathchs)
        }
    })
}

// 列表对比，主要也是根据 key 值查找匹配项
// 对比出新旧列表的新增/删除/移动
function diffList(oldList, newList, index, pathchs) {
    let change = []
    let list = []
    const newKeys = getKey(newList)
    oldList.map(v => {
        if (newKeys.indexOf(v.key) > -1) {
            list.push(v.key)
        } else {
            list.push(null)
        }
    })

    // 标记删除
    for (let i = list.length - 1; i>= 0; i--) {
        if (!list[i]) {
            list.splice(i, 1)
            change.push({ type: 'remove', index: i })
        }
    }

    // 标记新增和移动
    newList.map((item, i) => {
        const key = item.key
        const index = list.indexOf(key)
        if (index === -1 || key == null) {
            // 新增
            change.push({ type: 'add', node: item, index: i })
            list.splice(i, 0, key)
        } else {
            // 移动
            if (index !== i) {
                change.push({
                    type: 'move',
                    form: index,
                    to: i,
                })
                move(list, index, i)
            }
        }
    })

    return { change, list }
}
```

### Proxy 相比于 defineProperty 的优势
- 数组变化也能监听到
- 不需要深度遍历监听

```js
let data = { a: 1 }
let reactiveData = new Proxy(data, {
	get: function(target, name){
		// ...
	},
	// ...
})
```

### vue-router
- mode
  - hash
  - history
- 跳转
  - this.$router.push()
  - <router-link to=""></router-link>
- 占位
  - <router-view></router-view>
