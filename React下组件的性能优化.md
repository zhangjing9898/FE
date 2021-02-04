在 React 中，**只要父组件发生了更新，那么所有的子组件都会被无条件更新**



提出问题：在函数组件中，有没有什么通用的手段可以阻止“过度 re-render”的发生呢？



整体思路如下：1和2侧重类组件 3是函数组件



1. **使用 shouldComponentUpdate 规避冗余的更新逻辑**
2. **PureComponent + Immutable.js**
3. **React.memo 与 useMemo**



## shouldComponentUpdate



shouldComponentUpdate 是 React 类组件的一个生命周期。顾名思义，就不细讲了



shouldComponentUpdate 默认会返回“true”，也就代表**无条件re-render**



开发者可以这样改一下... 加一些门槛，让其不再无条件渲染



```
shouldComponentUpdate(nextProps, nextState) {
  // 判断 text 属性在父组件更新前后有没有发生变化，若没有发生变化，则返回 false
  if(nextProps.text === this.props.text) {
    return false
  }
  // 只有在 text 属性值确实发生变化时，才允许更新进行下去
  return true
}
```



## PureComponent + Immutable.js



PureComponent可以理解为“封装版”shouldComponentUpdate。开发同学，不需要每次都手动实现一次shouldComponentUpdate



PureComponent 将会在 shouldComponentUpdate 中对组件更新前后的 props 和 state 进行**浅比较**，并根据浅比较的结果，决定是否需要继续更新流程。



注意：“浅比较”将针对值类型数据对比其值是否相等，而针对数组、对象等引用类型的数据则对比其引用是否相等



由于PureComponent的浅比较，如果数据类型为引用类型，就会存在2个风险



1. 若数据内容没变，但是引用变了，那么浅比较仍然会认为“数据发生了变化”，进而触发一次不必要的更新，导致过度渲染；
2. 若数据内容变了，但是引用没变，那么浅比较则会认为“数据没有发生变化”，进而阻断一次更新，导致不渲染



> 这时候就需要Immutable了



首先下定义：**Immutable 可称为 “持久性数据”，指的是这个数据只要被创建出来了，就不能被更改。我们对当前数据的任何修改动作，都会导致一个新的对象的返回**



题外话：平时类组件用的不多，所以记录了个思路



## React.memo ||  useMemo



### React.memo



React.memo 会帮我们“记住”函数组件的渲染结果，在组件前后两次 props 对比结果一致的情况下，它会直接复用最近一次渲染的结果



注意：React.memo中只能对props进行比较，不能会组件内部的state进行比较，useMemo刚好可以解决这个问题



### useMemo



可以理解为：更加细致化的memo。React.memo是对组件级别的 re-render 进行管控，useMemo是对逻辑片段的 re-render进行管控。



**简单来说：React.memo 控制是否需要重渲染一个组件，而 useMemo 控制的则是是否需要重复执行某一段逻辑**



借用官网的一段代码



```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```



## React.memo ||  useMemo



### React.memo



React.memo 会帮我们“记住”函数组件的渲染结果，在组件前后两次 props 对比结果一致的情况下，它会直接复用最近一次渲染的结果



### useMemo



可以理解为：更加细致化的memo。React.memo是对组件级别的 re-render 进行管控，useMemo是对逻辑片段的 re-render进行管控



**简单来说：React.memo 控制是否需要重渲染一个组件，而 useMemo 控制的则是是否需要重复执行某一段逻辑**



借用官网的一段代码



```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```
