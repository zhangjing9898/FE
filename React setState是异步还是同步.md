🎃 **这是个老生常谈的问题了**



结论：setState 并非真异步，只是看上去像异步。



在源码中，通过 isBatchingUpdates 来判断。



### setState本质



setState 本身变更状态，触发组件重新渲染，更新视图 UI。



### 合成事件



举一个例子🌰：假设一个列表的 ul 标签下面有 10000 个 li 标签。现在需要添加点击事件，通过点击获取当前 li 标签中的文本。那该如何操作？如果按照现在 React 的编写方式，就是为每一个 li 标签添加 onclick 事件。有 10000 个 li 标签，则会添加 10000 个事件。这是一种非常不友好的方式，会对页面的性能产生影响。



**那该怎么优化呢**？最恰当的处理方式是采用**事件委托**。通过将事件绑定在 ul 标签上这样的方式来解决。当 li 标签被点击时，由事件冒泡到父级的 ul 标签去触发，并在 ul 标签的 onclick 事件中，确认是哪一个 li 标签触发的点击事件。



出于性能考虑，合成事件也是如此：

1. React 给 **document** 挂上事件监听；
2. DOM 事件触发后冒泡到 document； （注意：React 17之前，挂在document上，17及之后，挂在dom上， ReactDom.Render 所调用的节点上）
3. React 找到对应的组件，造出一个合成事件出来
4. 并按组件树模拟一遍事件冒泡。



![image](https://gw.alicdn.com/imgextra/i2/O1CN01qR2UPm1eG8kbkTm1p_!!6000000003843-2-tps-1344-590.png)



到底哪些事件会被捕获生成合成事件？

```
Array [
      "abort",
      "animationEnd",
      "animationIteration",
      "animationStart",
      "auxClick",
      "beforeInput",
      "blur",
      "canPlay",
      "canPlayThrough",
      "cancel",
      "change",
      "click",
      "close",
      "compositionEnd",
      "compositionStart",
      "compositionUpdate",
      "contextMenu",
      "copy",
      "cut",
      "doubleClick",
      "drag",
      "dragEnd",
      "dragEnter",
      "dragExit",
      "dragLeave",
      "dragOver",
      "dragStart",
      "drop",
      "durationChange",
      "emptied",
      "encrypted",
      "ended",
      "error",
      "focus",
      "gotPointerCapture",
      "input",
      "invalid",
      "keyDown",
      "keyPress",
      "keyUp",
      "load",
      "loadStart",
      "loadedData",
      "loadedMetadata",
      "lostPointerCapture",
      "mouseDown",
      "mouseEnter",
      "mouseLeave",
      "mouseMove",
      "mouseOut",
      "mouseOver",
      "mouseUp",
      "paste",
      "pause",
      "play",
      "playing",
      "pointerCancel",
      "pointerDown",
      "pointerEnter",
      "pointerLeave",
      "pointerMove",
      "pointerOut",
      "pointerOver",
      "pointerUp",
      "progress",
      "rateChange",
      "reset",
      "scroll",
      "seeked",
      "seeking",
      "select",
      "stalled",
      "submit",
      "suspend",
      "timeUpdate",
      "toggle",
      "touchCancel",
      "touchEnd",
      "touchMove",
      "touchStart",
      "transitionEnd",
      "volumeChange",
      "waiting",
      "wheel",
    ]
```



### 调用顺序

####  

#### 异步场景



**🌰例子1：**

Q：输出什么？

```
class Test extends Component {
  state = {
    count: 0
  };
  componentDidMount() {
    this.setState(
      {
        count: 1
      },
      () => {
        console.log(this.state.count); 
      }
    );
    console.log(this.state.count);  
  }
  render() {}
}
```

A：输出0，然后1；先执行最外层log，然后再setState，再log



**🌰例子2：**

Q：输出什么？

```
class Test extends Component {
  state = {
    count: 0
  };

  componentDidMount() {
    this.setState(
      {
        count: this.state.count + 1
      },
      () => {
        console.log(this.state.count);
      }
    );
    this.setState(
      {
        count: this.state.count + 1
      },
      () => {
        console.log(this.state.count);
      }
    );
  }

  render() {}
}
```

A：输出1,1；会发现当前拿到的 this.state.count 的值并没有变化，都是 0，所以输出结果应该是 1,1。

注意：setTimeout 外的 setState 会积攒起来一次性批处理，呈现出异步处理的状态。但其实两次 this.state.count 拿到的实际上都是 0



**🌰例子3：**

Q：输出什么？

```
class Test extends Component {
  state = {
    count: 0
  };

  componentDidMount() {
    this.setState(
      preState => ({
        count: preState.count + 1
      }),
      () => {
        console.log(this.state.count);
      }
    );
    this.setState(
      preState => ({
        count: preState.count + 1
      }),
      () => {
        console.log(this.state.count);
      }
    );
  }

  render() {}
}
```

A: 输出 2,2；当调用 setState 函数时，就会把当前的操作放入队列中。React 根据队列内容，合并 state 数据，完成后再逐一执行回调，根据结果更新虚拟 DOM，触发渲染。所以回调时，state 已经合并计算完成了，输出的结果就是 2,2 了。



**感觉一切都好绕，**[**为什么选择了这样一个行为模式**](https://github.com/facebook/react/issues/11527)**，而不是同步进行呢？**

- **优化：**通过异步的操作方式，累积更新后，批量合并处理，减少渲染次数，提升性能。但同步就不能批量合并吗？

- **保持内部一致性**。如果改为同步更新的方式，尽管 setState 变成了同步，但是 props 不是。
- **为后续的架构升级启用并发更新**。为了完成异步渲染，React 会在 setState 时，根据它们的数据来源分配不同的优先级，这些数据来源有：事件回调句柄、动画效果等，再根据优先级并发处理，提升渲染性能。





#### 同步场景



**🌰例子1：**

Q：输出什么？

```
class Test extends Component {
  state = {
    count: 0
  };

  componentDidMount() {
    this.setState({ count: this.state.count + 1 });
    console.log(this.state.count); 
    setTimeout(() => {
      this.setState({ count: this.state.count + 1 });
      console.log('setTimeout: ' + this.state.count);
    }, 0);
  }

  render() {}
}
```

A: 0,2； setState 并不是真正的异步函数，它实际上是通过队列延迟执行操作实现的，通过 isBatchingUpdates 来判断 setState 是先存进 state 队列还是直接更新。值为 true 则执行异步操作，false 则直接同步更新。



**dirtyComponent - 待更新组件**



![image](https://gw.alicdn.com/imgextra/i3/O1CN01LkfnlH24aSKYFJF5g_!!6000000007407-2-tps-864-818.png)



**True：**

- 在 onClick、onFocus 等事件中，由于合成事件封装了一层，所以可以将 isBatchingUpdates 的状态更新为 true
- 在 React 的生命周期函数中，同样可以将 isBatchingUpdates 的状态更新为 true
- 那么在 React 自己的生命周期事件和合成事件中，可以拿到 isBatchingUpdates 的控制权，将状态放进队列，控制执行节奏。

**False：**

- 外部的原生事件中，并没有外层的封装与拦截，无法更新 isBatchingUpdates 的状态为 true。这就造成 isBatchingUpdates 的状态只会为 false，且立即执行。所以在 addEventListener 、setTimeout、setInterval 这些原生事件中都会同步更新。



### 例子🌰

Q：输出什么？

```
class Test extends React.Component {
  state = {
    count: 0
  };

  componentDidMount() {
    this.setState({ count: this.state.count + 1 });
    console.log(this.state.count);

    this.setState({ count: this.state.count + 1 });
    console.log(this.state.count);

    setTimeout(() => {
      this.setState({ count: this.state.count + 1 });
      console.log(this.state.count);

      this.setState({ count: this.state.count + 1 });
      console.log(this.state.count); 
    }, 0); 
  }

  render() {
    return null;
  }
}
```

A：0，0，2，3；首先第一次和第二次的 console.log，都在 React 的生命周期事件中，所以是异步的处理方式，则输出都为 0；而在 setTimeout 中的 console.log 处于原生事件中，所以会同步的处理再输出结果，但需要注意，虽然 count 在前面经过了两次的 this.state.count + 1，但是每次获取的 this.state.count 都是初始化时的值，也就是 0；所以此时 count 是 1，那么后续在 setTimeout 中的输出则是 2 和 3。

### 总结



setState 是先存进 state 队列还是直接更新？如果值为 true 则执行异步操作，为 false 则直接更新。

- 什么情况下 isBatchingUpdates 会为 true 呢？

- - 在 React 可以控制的地方，就为 true，比如在 React 生命周期事件和合成事件中，都会走合并操作，延迟更新的策略。
  - 在 React 无法控制的地方，比如原生事件，具体就是在 addEventListener 、setTimeout、setInterval 等事件中，就只能同步更新。
