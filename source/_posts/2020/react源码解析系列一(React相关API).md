---
title: react源码解析系列一(React相关API)
tags:
  - react
copyright: true
comments: true
date: 2020-11-03 19:21:48
categories: React
photos:
---

今天开始学习react源码相关的内容，源码基于版本`v16.6.0`。React16相较于之前的版本是核心上的一次重写，虽然主要的API都没有变化，但是增加了很多能力。并且首次引入了`Fiber`的概念，之后新的功能都是围绕`Fiber`进行实现，比如`AsyncMode`，`Profiler`等。

## React与ReactDom的区别
问题：***react仅仅1000多行代码，而react-dom却将近2w行***

答： React主要定义基础的概念，比如节点定义和描述相关，真正的实现代码都是在ReactDom里面的，也就是“平台无关”概念，针对不同的平台有不同的实现，但
基本的概念都定义在React里。

## React16.6.0使用FlowType做类型检查
Flow 是 facebook 出品的 JavaScript 静态类型检查⼯具。所谓类型检查，就是在编译期尽早发现（由类型错误引起的）bug，⼜不影响代码运⾏（不需要运⾏时动态检查类型），使编写 JavaScript 具有和编写 Java 等强类型语⾔相近的体验。

[官方网站](https://github.com/facebook/flow)

简单示例🌰
```bash
npm install -g flow-bin
flow init
touch index.js
```

```js
// index.js 进行类型注释
/*@flow*/
function add(x: number, y: number): number {
  return x + y
}
add('Hello', 11)

// 报错
```

---
<!-- more -->

## React暴露的Api
```js
const React = {
  Children: {
    map,
    forEach,
    count,
    toArray,
    only,
  },

  createRef,
  Component,
  PureComponent,

  createContext,
  forwardRef,

  Fragment: REACT_FRAGMENT_TYPE,
  StrictMode: REACT_STRICT_MODE_TYPE,
  unstable_AsyncMode: REACT_ASYNC_MODE_TYPE,
  unstable_Profiler: REACT_PROFILER_TYPE,

  createElement: __DEV__ ? createElementWithValidation : createElement,
  cloneElement: __DEV__ ? cloneElementWithValidation : cloneElement,
  createFactory: __DEV__ ? createFactoryWithValidation : createFactory,
  isValidElement: isValidElement,

  version: ReactVersion,

  __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED: ReactSharedInternals,
};
```

## JSX转换成什么
- 核心`React.createElement`
`ReactElement`通过`createElement`创建，调用该方法需要传入三个参数
  - type
  - config
  - children

### type指代这个ReactElement的类型
- 字符串比如`div`原生DOM，称为`HostComponent`**首字母小写**
- 自定义组件变量(`functional Component`/`ClassComponent`)**首字母大写**不大写会识别为原生DOM解析
- 原生提供的`Fragment`、`AsyncMode`等是`Symbol`，会被特殊处理

### config
react会把关键参数解析出来，例如`key`、`ref`，在`createElement`中识别分离，这些参数不会和其他参数一起处理而是单独作为变量出现在
`ReactElement`上。

### children
第三个参数就是children，而且可以有任意多的参数，表示兄弟节点。可以通过`this.props.children`访问到。

### 相关源码以及注解⬇️
```js
export function createElement(type, config, children) {
    if (config != null) {
    if (hasValidRef(config)) { // 从第二个参数筛选出ref和key
      ref = config.ref;
    }
    if (hasValidKey(config)) {
      key = '' + config.key;
    }

    // Remaining properties are added to a new props object
    for (propName in config) {
      if (
        hasOwnProperty.call(config, propName) &&
        !RESERVED_PROPS.hasOwnProperty(propName) // 内置的ref、id就是这解析出来的
      ) {
        props[propName] = config[propName];
      }
    }
  }

  // 
  const childrenLength = arguments.length - 2; // 3以后的参数都是children
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    const childArray = Array(childrenLength); // 大于1 放到数组
    for (let i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }
   
    props.children = childArray; // 放到chidren props.children拿的就是这个
  }

  // Resolve default props
  if (type && type.defaultProps) { // 设置props的默认值
    const defaultProps = type.defaultProps;
    for (propName in defaultProps) {
      if (props[propName] === undefined) { // 如果props是undefined才会使用默认值
        props[propName] = defaultProps[propName];
      }
    }
  }
  
  return ReactElement(
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props,
  );
}

const ReactElement = function(type, key, ref, self, source, owner, props) {
  const element = {
    // This tag allows us to uniquely identify this as a React Element
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    type: type,
    key: key,
    ref: ref,
    props: props,

    // Record the component responsible for creating this element.
    _owner: owner,
  };

  return element
}
```

### ReactElement
`ReactElement`只是一个用来承载信息的容器，它会告诉后续的操作这个节点的以下信息：
- type类型，用于判断如何创建节点
- key和ref这些特殊信息
- props新的属性内容
- $$typeof用于确定是否属于`ReactElement`

> React通过提供这种类型的数据，来脱离平台的限制。

### $$typeof
在最后创建`ReactElement`我们👀看到了这么一个变量`$$typeof`。这是啥呢？React元素，会有一个`$$typeof`来表示该元素是什么类型。当本地有
`Symbol`，则使用`Symbol`生成，没有时使用16进制。但有一个特例：`ReactDOM.createPortal`的时候是`REACT_PORTAL_TYPE`，不过它不是通过
`createElement`创建的，所以它也不属于`ReactElement`

```js
export let REACT_ELEMENT_TYPE = 0xeac7;
export let REACT_PORTAL_TYPE = 0xeaca;
export let REACT_FRAGMENT_TYPE = 0xeacb;
....
if (typeof Symbol === 'function' && Symbol.for) {
  const symbolFor = Symbol.for;
  REACT_ELEMENT_TYPE = symbolFor('react.element');
  REACT_PORTAL_TYPE = symbolFor('react.portal');
  REACT_FRAGMENT_TYPE = symbolFor('react.fragment');
  ....
}
```

## cloneElement
```js
// 第一个参数传入ReactElement，第二、三个参数和createElement一致
export function cloneElement(element, config, children) {
  invariant(
    !(element === null || element === undefined),
    'React.cloneElement(...): The argument must be a React element, but you passed %s.',
    element,
  );

  let propName;

  // 复制传入的element props
  const props = Object.assign({}, element.props);

  // 提取内嵌的key和ref 不进行覆盖是原有的
  let key = element.key;
  let ref = element.ref;

  const self = element._self;
  // Source is preserved since cloneElement is unlikely to be targeted by a
  // transpiler, and the original source is probably a better indicator of the
  // true owner.
  const source = element._source;

  // Owner will be preserved, unless ref is overridden
  let owner = element._owner;

  // config和children的解析是一样的
  if (config != null) {
    if (hasValidRef(config)) {
      // Silently steal the ref from the parent.
      ref = config.ref;
      owner = ReactCurrentOwner.current;
    }
    if (hasValidKey(config)) {
      key = '' + config.key;
    }

    // Remaining properties override existing props
    let defaultProps;
    if (element.type && element.type.defaultProps) {
      defaultProps = element.type.defaultProps;
    }
    for (propName in config) {
      if (
        hasOwnProperty.call(config, propName) &&
        !RESERVED_PROPS.hasOwnProperty(propName)
      ) {
        if (config[propName] === undefined && defaultProps !== undefined) {
          // Resolve default props
          props[propName] = defaultProps[propName];
        } else {
          props[propName] = config[propName];
        }
      }
    }
  }

  // Children can be more than one argument, and those are transferred onto
  // the newly allocated props object.
  const childrenLength = arguments.length - 2;
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    const childArray = Array(childrenLength);
    for (let i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];
    }
    props.children = childArray;
  }

  return ReactElement(element.type, key, ref, self, source, owner, props);
}
```
> React.cloneElement()几乎等同于
```js
<element.type {...element.props} {...props}>{children}</element.type>
```

### 劫持组件
```js
function AddStyle({children}) {
  return React.cloneElement(children, {
    style: {
      background: '#dfdfdf'
    }
  }, 'hello world')
}
<AddStyle>
  <div></div>
</AddStyle>
```

## Component和PureComponent两个基类(ReactBaseClasses.js)
> 以element为样板克隆返回新的React元素，返回的props为新和旧的props进行浅层合并后的结果。新的子元素会替代旧的子元素，但是key和ref会保留。

源码解析↓
```js
function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  // 使用string ref
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

Component.prototype.isReactComponent = {};

Component.prototype.setState = function(partialState, callback) {
  // 校验第一个参数
  // partialState: 要更新的对象
  // 新的react版本推荐setState使用方法 => this.setState((preState) => ({count:preState.count+1}))
  invariant(
    typeof partialState === 'object' ||
      typeof partialState === 'function' ||
      partialState == null,
    'setState(...): takes an object of state variables to update or a ' +
      'function which returns an object of state variables.',
  );
  // 更新队列 实现在react-dom里 整个Component的初始化入口
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

// 强制更新
Component.prototype.forceUpdate = function(callback) {
  // 同样也在react-dom里实现
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};

function ComponentDummy() {}
ComponentDummy.prototype = Component.prototype;

/**
 * 和Component一致
 */
function PureComponent(props, context, updater) {
  this.props = props;
  this.context = context;
  this.refs = emptyObject;
  this.updater = updater || ReactNoopUpdateQueue;
}

// 继承
const pureComponentPrototype = (PureComponent.prototype = new ComponentDummy());
pureComponentPrototype.constructor = PureComponent;
// 复制拷贝 唯一的区别是标记上的区别 isPureReactComponent
Object.assign(pureComponentPrototype, Component.prototype);
pureComponentPrototype.isPureReactComponent = true;
```

## 设计思想
- 平台思想(React和ReactDOM分包)
抽象出概念，彻底剥离实现层，react只是处理了类型和参数的转换，不具体的实现任何业务。各个平台的实现放到ReactDom里处理。

未完待续...
