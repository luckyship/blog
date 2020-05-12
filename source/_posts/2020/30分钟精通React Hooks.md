---
title: 30分钟精通React Hooks
tags:
  - 面试
copyright: true
comments: true
date: 2020-05-12 11:40:08
categories: 知识
photos:
---

你还在为该使用无状态组件（Function）还是有状态组件（Class）而烦恼吗？
—— 拥有了hooks，你再也不需要写Class了，你的所有组件都将是Function。
你还在为搞不清使用哪个生命周期钩子函数而日夜难眠吗？
—— 拥有了Hooks，生命周期钩子函数可以先丢一边了。
你在还在为组件中的this指向而晕头转向吗？
—— 既然Class都丢掉了，哪里还有this？你的人生第一次不再需要面对this。

## useState
### 状态组件
```js
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

### hooks改造
```js
import { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
是不是简单多了！可以看到，Example变成了一个函数，但这个函数却有自己的状态（count），同时它还可以更新自己的状态（setCount）。

除了`useState`这个hook外，还有很多别的hook，比如`useEffect`提供了类似于`componentDidMount`等生命周期钩子的功能，`useContext`提供了上下文（context）的功能等等。

## React为什么要搞一个Hooks？
***想要复用一个有状态的组件太麻烦了！***
我们都知道react的核心思想是，将一个页面拆成一堆独立的，可复用的组件，并且用自上而下的单向数据流的形式将这些组件串联起来。但假如你在大型的工作项目中用react，你会发现你的项目中实际上很多react组件冗长且难以复用。尤其是那些写成class的组件，它们本身包含了状态（state），所以复用这类组件就变得很麻烦。

那之前，官方推荐怎么解决这个问题呢？答案是：[渲染属性（Render Props）](https://reactjs.org/docs/render-props.html)和[高阶组件（Higher-Order Components）](https://reactjs.org/docs/higher-order-components.html)。

### 渲染属性
渲染属性指的是使用一个值为函数的prop来传递需要动态渲染的nodes或组件。如下面的代码可以看到DataProvider组件包含了所有跟状态相关的代码，而Cat组件则可以是一个单纯的展示型组件，这样一来DataProvider就可以单独复用了。

```js
import Cat from 'components/cat'
class DataProvider extends React.Component {
  constructor(props) {
    super(props);
    this.state = { target: 'Zac' };
  }

  render() {
    return (
      <div>
        {this.props.render(this.state)}
      </div>
    )
  }
}

<DataProvider render={data => (
  <Cat target={data.target} />
)}/>
```