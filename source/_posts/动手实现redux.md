---
title: 动手实现redux
date: 2018-10-03 19:24:18
tags:
  - redux
  - react
categories: JS
copyright: true
comments: true
top: 106
photos:
---

概念：
- 一个app有一个store，一个store管理着一个全局state
- createStore 传入reducer，返回getState, dispatch, subscribe
- action是一个至少有type这个键的对象，可以写一个creactActioner 函数去return生成action对象
- createStore.dispatch(action) 根据action这个对象去更新state
- dispatch是一个函数，内部有将执行reducer函数
- reducer也是一个函数，传入state,action, 输出一个新的state . (switch case return…)

---
<!-- more -->

```html
<div id = 'title'></div>
<input type="button" id = "changeTheme" value="变成蓝色主题">
```

```javascript
// 实现createStore 传入reducer
function createStore(reducer){
    // 存储数据
    let state = null
    // 订阅列表
    const listenerList=[]
    // 增加订阅
    const subscribe=(listener)=>listenerList.push(listener)
    // 返回state
    const getState=()=>state 
    // dispatch
    const dispatch=(action)=>{
      // 新的state
      state=reducer(state,action)
      // 遍历执行
      listenerList.forEach(item)=>item()
    }
    // 初始化state
    dispatch({})
    return {getState,subscribe,dispatch}
}

// 实现reducer
function(state,action){
  if(!state){
    return {
      title:'红色',
      color:'red'
    }
  }
  switch(action.type){
    case 'UPDATE_TITLE':
    return {...state,title:action.title}
    case 'UPDATE_COLOR':
    return {...state,color:action.color}
    default:return state
  }
}

// 传入reducer生成store
const store =createStore(reducer)

// 渲染代码
function renderDom(state){
  const titleDOM = document.getElementById('title');
  titleDOM.innerHTML = state.title;
  titleDOM.style.color = state.color;
}

// 监听数据变化重新渲染页面
store.subscribe(() => renderApp(store.getState()));// 让每次dispatch时都会执行传入的这个函数，渲染页面

// 首次渲染页面
renderApp(store.getState());

// action
const updateThemeName = () => ({
  type: 'UPDATE_TITLE',
  title: '蓝色'
});
const updateThemeColor = () => ({
  type: 'UPDATE_COLOR',
  color: 'blue'
});

// 绑定事件
document.getElementById('changeTheme').onclick = () => {
  store.dispatch(updateThemeName());
  store.dispatch(updateThemeColor());
};
```