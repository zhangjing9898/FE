## What Why How



## Hooks设计初衷



React 团队给的第一个 Hooks 使用案例，也是在函数组件中使用 state，使用状态管理



React 团队在过去五年时间里，他们遇到了这些问题。



- 组件之间难以复用状态逻辑
- 复杂的组件变得难以理解

- - 主要指出生命周期函数没能提供最佳的代码编程实践范式；进而componentDidMount，变成了一个大杂烩

- - 订阅与取消订阅并没有直接关联在一起，而是通过生命周期函数(componentDidMount[订阅]、componentWillUnmount[取消订阅])去使用

- 人和机器都容易混淆class 类函数

- - this 

- - - 比如：需要用 bind 函数包一下来绑定事件



## 方案原理



首先展示一下，hooks的使用效果：函数中调用 useState 会返回当前状态与更新状态的函数



```
 const [count, setCount] = useState(0);
```



Rudi Yardley 写过一篇 《React hooks: not magic, just arrays》









## 其他



React Hooks 的限制主要有两条：

- 不要在循环、条件或嵌套函数中调用 Hook；
- 在 React 的函数组件中调用 Hook。



那为什么会有这样的限制呢？就得从 Hooks 的设计说起。Hooks 的设计初衷是为了改进 React 组件的开发模式。在旧有的开发模式下遇到了三个问题

- 组件之间难以复用状态逻辑。过去常见的解决方案是高阶组件、render props 及状态管理框架。

- 复杂的组件变得难以理解。生命周期函数与业务逻辑耦合太深，导致关联部分难以拆分。
- 人和机器都很容易混淆类。常见的有 this 的问题，但在 React 团队中还有类难以优化的问题，他们希望在编译优化层面做出一些改进。



这三个问题在一定程度上阻碍了 React 的后续发展，所以为了解决这三个问题，Hooks 基于函数组件开始设计。然而第三个问题决定了 Hooks 只支持函数组件。



那为什么不要在循环、条件或嵌套函数中调用 Hook 呢？因为 Hooks 的设计是基于数组实现。在调用时按顺序加入数组中，如果使用循环、条件或嵌套函数很有可能导致数组取值错位，执行错误的 Hook。当然，实质上 React 的源码里不是数组，是链表。



这些限制会在编码上造成一定程度的心智负担，新手可能会写错，为了避免这样的情况，可以引入 ESLint 的 Hooks 检查插件进行预防。

- 其实只需要在 ESLint 中引入 eslint-plugin-react-hooks 完成自动化检查就可以了。在处理代码编写方式的问题时，都应该优先想到从 Lint 工具入手。

- [如何使用 eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation)

- - tnpm install eslint-plugin-react-hooks --save-dev
  - .eslintrc中进行配置



```
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```
