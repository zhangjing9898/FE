![img](https://gw.alicdn.com/imgextra/i3/O1CN01jCLbLF1PZAycw28CF_!!6000000001854-2-tps-1072-956.png)



虚拟 DOM 并不是只有 React 才有，只是经过 React 的发扬光大，变得很是流行。

## 

## DOM如何出现在React中



- 在 JSX 的使用中，JSX 所描述的结构，会转译成 React.createElement 函数，大致像这样：

```
// JSX 描述
 <input type="button"/>
 // Babel 转译后
 React.createElement('input', { type: "button" })
```

- React 会持有一棵虚拟 DOM 树，在状态变更后，会触发虚拟 DOM 树的修改，再以此为基础修改真实 DOM。



#### 结论：

基于上面的信息，可以推导出 React.createElement 返回的结果应该是一个 JavaScript Object，而且是树结构，类似这种

```
{
  tag: 'input',
  props: {
    type: 'button'
  },
  children: []
}
```



同时，React 有两个函数：

1. diff 函数，去计算状态变更前后的虚拟 DOM 树差异
2. 渲染函数，渲染整个虚拟 DOM 树或者处理差异点



这也就是 React 与 ReactDOM 是两个库了的原因。由于计算(React)与渲染(React-dom)的分工。在日常的开发中，就像下面的代码案例一样，需要同时引入 React 与 ReactDOM 两个库：



```
import React from 'react';
import ReactDOM from 'react-dom';
ReactDOM.render(<h1>hi!</h2>, document.getElementById('root'));
```

其中 React 主要的工作是组件实现、更新调度等计算工作；而 ReactDOM 提供了在网页上渲染的基础。

也正因为这样的拆分，当 React 向 iOS、Android 开发时，只需要通过 React Native 提供 Native 层的元素渲染即可完成。



### 其他

```
虚拟 DOM 的工作原理是通过 JS 对象模拟 DOM 的节点。在 Facebook 构建 React 初期时，考虑到要提升代码抽象能力、避免人为的 DOM 操作、降低代码整体风险等因素，所以引入了虚拟 DOM。

虚拟 DOM 在实现上通常是 Plain Object，以 React 为例，在 render 函数中写的 JSX 会在 Babel 插件的作用下，编译为 React.createElement 执行 JSX 中的属性参数。

React.createElement 执行后会返回一个 Plain Object，它会描述自己的 tag 类型、props 属性以及 children 情况等。这些 Plain Object 通过树形结构组成一棵虚拟 DOM 树。当状态发生变更时，将变更前后的虚拟 DOM 树进行差异比较，这个过程称为 diff，生成的结果称为 patch。计算之后，会渲染 Patch 完成对真实 DOM 的操作。

虚拟 DOM 的优点主要有三点：改善大规模 DOM 操作的性能、规避 XSS 风险、能以较低的成本实现跨平台开发。

虚拟 DOM 的缺点在社区中主要有两点。

内存占用较高，因为需要模拟整个网页的真实 DOM。

高性能应用场景存在难以优化的情况，类似像 Google Earth 一类的高性能前端应用在技术选型上往往不会选择 React。
```
