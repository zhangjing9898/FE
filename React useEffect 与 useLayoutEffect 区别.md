useEffect 与 [useLayoutEffect](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect) 的区别在哪里？



#### 本质



签名函数完全一样：在源码中，它们调用的是同一个函数





#### 运行效果



从运用效果上而言，useEffect 与 useLayoutEffect 两者都是用于处理副作用，包括改变 DOM、设置订阅、操作定时器等。在函数组件内部操作副作用是不被允许的，所以需要使用这两个函数去处理。

####  

##### 什么时候使用？



在 React 社区中最佳的实践是这样推荐的，大多数场景下可以直接使用useEffect，但是如果你的代码引起了页面闪烁，也就是引起了组件突然改变位置、颜色及其他效果等的情况下，就推荐使用useLayoutEffect来处理。那么总结起来就是如果有直接操作 DOM 样式或者引起 DOM 样式更新的场景更推荐使用 useLayoutEffect。



useLayoutEffect： HookLayout 的 effect 会在所有的 DOM 变更之后同步调用 

- 计算量较大的耗时任务必然会造成阻塞，所以这就需要根据实际情况酌情考虑了。如果非必要情况下，使用标准的 useEffect 可以避免阻塞

## 总结



共同点

- 底层的函数签名是完全一致的，都是调用的 mountEffectImpl，在使用上也没什么差异，基本可以直接替换，也都是用于处理副作用。



不同点

- useEffect 在 React 的渲染过程中是被异步调用的，用于绝大多数场景，而 LayoutEffect 会在所有的 DOM 变更之后同步调用，主要用于处理 DOM 操作、调整样式、避免页面闪烁等问题。也正因为是同步处理，所以需要避免在 LayoutEffect 做计算量较大的耗时任务从而造成阻塞。



在未来的趋势上，两个 API 是会长期共存的，暂时没有删减合并的计划，需要开发者根据场景去自行选择。React 团队的建议非常实用，如果实在分不清，先用 useEffect，一般问题不大；如果页面有异常，再直接替换为 useLayoutEffect 即可。
