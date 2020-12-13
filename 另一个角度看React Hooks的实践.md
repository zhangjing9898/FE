如果你想掌握尽可能多的 Hook 的用法，[点击这里](https://zh-hans.reactjs.org/docs/hooks-reference.html)可以一键进入 React-Hooks API 文档


这里只简单介绍 useState、useEffect 这两个有代表性的 Hook


# useState


useState()是为函数组件引入**状态，**早期的函数组件相比于类组件，其一大劣势是缺乏定义和维护 state 的能力，useState 正是这样一个能够为函数组件引入状态的 API。


## 函数组件：轻便


函数组件真的很轻便，这里会用2个demo做区分。


### 用class做state的修改

```javascript
import React, { Component } from "react";

export default class TextButton extends Component {

  constructor() {
    super();
    this.state = {
      text: "初始文本"
    };
  }

  changeText = () => {
    this.setState(() => {
      return {
        text: "修改后的文本"
      };
    });
  };

  render() {
    const { text } = this.state;
    return (
      <div className="textButton">
        <p>{text}</p>
        <button onClick={this.changeText}>点击修改文本</button>
      </div>
    );
  }
}

```


### 用hook来实现

```javascript
import React, { useState } from "react";
export default function Button() {
  const [text, setText] = useState("初始文本");
  const changeText = () => {
    return setText("修改后的文本");
  }
  return (
    <div className="textButton">
      <p>{text}</p>
      <button onClick={changeText}>点击修改文本</button>
    </div>
  );
}
```

### 总结

同样逻辑的函数组件相比类组件而言，复杂度要低得多得多
**
**

## useState使用手册


理解这两点：

- useState 返回的是一个数组，数组的第一个元素对应的是我们想要的那个 state 变量，第二个元素对应的是能够修改这个变量的 API
- 通过数组解构的语法，将这两个元素取出来，并且按照我们自己的想法命名



🌰例子

```javascript
const [state, setState] = useState(initialState);
```


状态更新后会触发渲染层面的更新，这点和类组件是一致

在函数组件中调用useState 的时候，实际上是给这个组件关联了一个状态
注意，是“一个状态”而不是“一批状态”，在类组件中通常是“一批状态”


### class 一批状态

```javascript
this.state {
  text: "123",
  length: 10000
}
```

### hook 一个状态

```javascript
// 定义为数值
const [length, setLength] = useState(100);
// 定义为字符串
const [text, setText] = useState("123")
```

# useEffect


useEffect()：允许函数组件执行副作用操作。函数组件相比于类组件来说，最显著的差异就是 state 和生命周期的缺失。useState 为函数组件引入了 state，而 useEffect 则在一定程度上弥补了生命周期的缺失。

## 上手useEffect()


useEffect 可以接收两个参数，分别是回调函数与依赖数组

```javascript
useEffect(callBack, [])
```


useEffect 用什么姿势来调用，本质上取决于你想用它来达成什么样的效果

### 每次渲染都执行


每一次渲染后都执行：传入回调函数，不传依赖数组

```javascript
useEffect(callBack)
```


### 仅在挂载阶段执行一次


仅在挂载阶段执行一次的副作用：传入回调函数，**且这个函数的返回值不是一个函**数，同时传入一个空数组

```javascript
useEffect(()=>{
  // 这里是业务逻辑 
}, [])
```


### 仅在挂载阶段和卸载阶段执行


传入回调函数，**且这个函数的返回值是一个函数**，同时传入一个空数组。假如回调函数本身记为 A， 返回的函数记为 B，那么将在挂载阶段执行 A，卸载阶段执行 B

这里B函数的角色定位就类似于生命周期里 componentWillUnmount 方法里的逻辑

```javascript
useEffect(()=>{
  // 这里是 A 的业务逻辑
  // 返回一个函数记为 B
  return ()=>{
  }
}, [])
```

useEffect 回调中返回的函数被称为“清除函数”，当 React 识别到清除函数时，会在卸载时执行清除函数内部的逻辑。这个规律不会受第二个参数或者其他因素的影响，只要你在 useEffect 回调中返回了一个函数，它就会被作为清除函数来处理


### 根据一定的依赖条件来触发


传入回调函数（若返回值是一个函数，仍然仅影响卸载阶段对副作用的处理，此处不再赘述），同时传入一个非空的数组

```javascript
useEffect(()=>{
  // 这是回调函数的业务逻辑 
  // 若 xxx 是一个函数，则 xxx 会在组件卸载时被触发
  return xxx
}, [num1, num2, num3])
```

数组中的变量一般都是来源于组件本身的数据（props 或者 state）。若数组不为空，那么 React 就会在新的一次渲染后去对比前后两次的渲染，查看数组内是否有变量发生了更新（只要有一个数组元素变了，就会被认为更新发生了），并在有更新的前提下去触发 useEffect 中定义的副作用逻辑

# Hooks如何升级我们的工作模式


问题：为什么要设计 react hooks？


回答：

- 告别难以理解的class；
- 解决业务逻辑难以拆分的问题；
- 使状态逻辑复用变得简单可行；
- 函数组件从设计思想上来看，更加契合 React 的理念



## 告别难以理解的class


class 是“难以理解”的，这个说法的背后有2个痛点：

- this
- 生命周期



### this

为了解决 this 不符合预期的问题，各路前端也是各显神通，之前用 bind、现在用箭头函数。**本质上都是在用实践层面的约束来解决设计层面的问题**。现在有了 Hooks，我们可以在函数组件里放飞自我，不关心this

### 生命周期

问题：

- 学习成本
- 不合理的逻辑规划方式



## 更好的逻辑拆分


将对应的业务逻辑拆到不同的生命周期函数里去。**逻辑与生命周期耦合在一起。**比如在 componentDidMount 里获取数据，在 componentDidUpdate 里根据数据的变化去更新 DOM 等


```javascript
componentDidMount() {
  // 1. 这里发起异步调用
  // 2. 这里从 props 里获取某个数据，根据这个数据更新 DOM
  // 3. 这里设置一个订阅
  // 4. 这里随便干点别的什么 
  // ...
}
componentWillUnMount() {
  // 在这里卸载订阅
}
componentDidUpdate() {
  // 1. 在这里根据 DidMount 获取到的异步数据更新 DOM
  // 2. 这里从 props 里获取某个数据，根据这个数据更新 DOM（和 DidMount 的第2步一样）
}
```

**这些事情之间看上去毫无关联，逻辑就像是被“打散”进生命周期里了一样**。比如，设置订阅和卸载订阅的逻辑，虽然它们在逻辑上是有强关联的，但是却只能被分散到不同的生命周期函数里去处理

在 Hooks 的帮助下，我们完全可以把这些繁杂的操作**按照逻辑上的关联拆分进不同的函数组件里：**我们可以有专门管理订阅的函数组件、专门处理 DOM 的函数组件、专门获取数据的函数组件等。Hooks 能够帮助我们**实现业务逻辑的聚合，避免复杂的组件和冗余的代码**


## 状态复用


过去我们复用状态逻辑，靠的是 HOC（高阶组件）和 Render Props 这些组件设计模式，这是因为 React 在原生层面并没有为我们提供相关的途径。

现在可以自定义Hooks去完成这个功能，具体api可以参考[这里](https://zh-hans.reactjs.org/docs/hooks-custom.html)

