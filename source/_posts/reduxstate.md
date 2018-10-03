---
title: redux状态传播
date: 2018-06-21 19:37:18
tags:
  - redux
  - react
categories: JS
---

## Redux三大概念
> Redux 是JavaScript状态容器，提供可预测化的状态管理

> action 普通的JS对象描述发生什么

> reducer 只是一个接收 state 和 action，并返回新的 state 的函数

### 三大原则

1. 单一数据源

整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。和根级的reducer,拆成多个reduce而不是多个store。

2. State 是只读的

唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。

3. 使用纯函数来执行修改

为了描述 action 如何改变 state tree ，你需要编写 reducers。期望转呗要修改state了,传递数据.payload规范。

当 state 变化时需要返回全新的对象，而不是修改传入的参数。

### api
~~~javascript
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 唯一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch({ type: 'INCREMENT' });
// 1
store.dispatch({ type: 'INCREMENT' });
// 2
store.dispatch({ type: 'DECREMENT' });
// 1
~~~ 

不要再reducer里做以下操作保持纯净
> 修改传入参数；

> 执行有副作用的操作，如 API 请求和路由跳转；

> 调用非纯函数，如 Date.now() 或 Math.random()。

#### 维持应用的 state；
1. 提供 getState() 方法获取 state；

2. 提供 dispatch(action) 方法更新 state；发送通知

3. 通过 subscribe(listener) 注册监听器;

4. 通过 unsubscribe() 返回的函数注销监听器。

纯函数是这样一种函数，即相同的输入，永远会得到相同的输出

store.dispatch方法会触发 Reducer 的自动执行。为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，将 Reducer 传入createStore方法

使用 复杂性 数据交互 结构复杂繁琐 大型
- "如果你不知道是否需要 Redux，那就是不需要它。"
- "只有遇到 React 实在解决不了的问题，你才需要 Redux。"


