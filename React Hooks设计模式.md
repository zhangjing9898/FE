**“忘记生命周期，以 effects 的方式开始思考”**<br />
<br />Hooks 并不是解决组件如何复用的问题，而是解决内部逻辑抽象复用的问题。以前是通过生命周期的方式思考逻辑如何布局，而现在是以事务的角度归纳合并。
<a name="Wsx7r"></a>
### 
<a name="LcO9Q"></a>
## Hooks


<a name="FN5de"></a>
### useEffect

<br />用于处理副作用，比如：<br />

- 过去类组件的开发模式中，在 componentDidMount 中放置一个监听事件，还需要考虑在 componentWillUnmount 中取消监听，甚至可能由于部分值变化，还需要在其他生命周期函数中对监听事件做特殊处理。



- 在 Hooks 的设计思路中，可以将这一系列监听与取消监听放置在一个 useEffect 中，useEffect 可以不关心组件的生命周期，只需要关心外部依赖的变化即可。这是 Hooks 的设计根本。


<br />

<a name="Wmqcf"></a>
### useMemo


- React.memo vs React.useMemo
- React.memo 高阶组件
   - 它的效果类似于 React.pureComponent。但在 Hooks 的场景下，推荐使用React.useMemo
   - 原因：React.useMemo 可以实现更精细化的控制


<br />**🌰例子**<br />
<br />**React.memo:**
```javascript
const Banner = () => {
  let appContext = useContext(AppContext);
  let theme = appContext.theme;
  return <Slider theme={theme} />
}
export default React.memo(Banner)
```
**React.useMemo:**
```javascript
const Banner = () => {
  let appContext = useContext(AppContext);
  let theme = appContext.theme;
  
  return React.useMemo(() => {
    return <Slider theme={theme} />;
  }, [theme])
}
export default React.memo(Banner)
```


<a name="oXnkB"></a>
### useCallBack


<a name="Y4kQL"></a>
#### 常量

<br />函数组件每次渲染时都会重新执行，所以常量应该放置到函数外部去，避免每次都重新创建。而如果定义的常量是一个函数，且需要使用组件内部的变量做计算，那么一定要使用 useCallback 缓存函数。<br />
<br />

<a name="g86aA"></a>
## 总结

<br />首先用 Hooks 开发需要抛弃生命周期的思考模式，以 effects 的角度重新思考。<br />

- 过去类组件的开发模式中，在 componentDidMount 中放置一个监听事件，还需要考虑在 componentWillUnmount 中取消监听，甚至可能由于部分值变化，还需要在其他生命周期函数中对监听事件做特殊处理。



- 在 Hooks 的设计思路中，可以将这一系列监听与取消监听放置在一个 useEffect 中，useEffect 可以不关心组件的生命周期，只需要关心外部依赖的变化即可。这是 Hooks 的设计根本。



<a name="vzwo3"></a>
#### 开发规范


- React.useMemo 取代 React.memo，因为 React.memo 并不能控制组件内部共享状态的变化，而 React.useMemo 更适合于 Hooks 的场景。



- 常量，在类组件中，我们很习惯将常量写在类中，但在组件函数中，这意味着每次渲染都会重新声明常量，这是完全无意义的操作。其次就是组件内的函数每次会被重新创建，如果这个函数需要使用函数组件内部的变量，那么可以用 useCallback 包裹下这个函数。



- 就是 useEffect 的第二个参数容易被错误使用。大多人习惯在第二个参数放置引用类型的变量，通常的情况下，引用类型的变量很容易被篡改，难以判断开发者的真实意图，所以更推荐使用值类型的变量。当然有个小技巧是 JSON 序列化引用类型的变量，也就是通过 JSON.stringify 将引用类型变量转换为字符串来解决。但不推荐这个操作方式，比较消耗性能。



<a name="jLjiR"></a>
#### 设计模式

<br />采用外观模式，将业务逻辑封装到各自的自定义 Hook 中

- 比如用户信息等操作，就把获取用户、增加用户、删除用户等操作封装到一个 Hook 中。而组件内部是抽空的，不放任何具体的业务逻辑，它只需要去调用单个自定义 Hook 暴露的接口就行了，这样也非常利于测试关键路径下的业务逻辑。
