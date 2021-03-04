# 基础知识



React-Hooks 这个东西比较特别，这背后其实涉及对**类组件**和**函数组件**两种组件形式的思考和侧重。



## 类组件（Class Component）



基于 ES6 Class 这种写法，通过继承 React.Component 得来的 React 组件。以下是一个典型的类组件



```
class dongmaoTest extends React.Component {
  // 初始化类组件的 state
  state = {
    text: ''
  }

  // 编写生命周期方法 didMount
  componentDidMount() {
    // 省略业务逻辑
  }

  // 编写自定义的实例方法
  changeText = newText => {
    // 更新 state
    this.setState({
      text: newText
    })
  }

  // 编写生命周期方法 render
  render() {
    return (
      <div className="demoClass">
        <p>{this.state.text}</p>
        <button onClick={this.changeText}>点我</button>
      </div>
    )
  }
}
```

##  

## 函数组件（Function Component）



#### 函数组件也可以叫做无状态组件



顾名思义，就是**以函数的形态**存在的组件。早期没有 React-Hooks ，函数组件内部是无法定义和维护 state，所以也可以称为“无状态组件”。



```
const dongmaoTest = (props) => {
  const { text } = props
  return (
    <div className="demoFunction">
      <p>{`文本内容是：[${text}]`}</p>
    </div>
  );
}
```



## 对比 类和函数组件



简单罗列一下：



- 类组件需要继承 class，函数组件不需要；
- 类组件可以访问生命周期方法，函数组件不能；
- 类组件中可以获取到实例化后的 this，并基于这个 this 做各种各样的事情，函数组件不可以；
- 类组件中可以定义并维护 state，函数组件不可以；
- ... and so on



# 深入



### 理解类组件



**包裹在面向对象思想下的“重装战舰”**



类组件是面向对象编程思想的一种表征，面向对象2大特征：



- 封装：将一类属性和方法，“聚拢抽象”到一个 Class 里去。
- 继承：新的 Class 可以通过继承现有 Class，实现对某一类属性和方法的复用。



#### 一个例子🌰



```
class dongmaoTest extends React.Component {
  // 初始化类组件的 state
  state = {
    text: ''
  }

  // 编写生命周期方法 didMount
  componentDidMount() {
    // 省略业务逻辑
  }

  // 编写自定义的实例方法
  changeText = newText => {
    // 更新 state
    this.setState({
      text: newText
    })
  }

  // 编写生命周期方法 render
  render() {
    return (
      <div className="demoClass">
        <p>{this.state.text}</p>
        <button onClick={this.changeText}>点我</button>
      </div>
    )
  }
}
```



类组件内部有相当多的“已有的东西”等着你去使用，state 和生命周期就是这些“现成东西”中的典型。如何得到这些东西？你只需要**继承**一个 React.Component



React 类组件，它提供了多少东西，你就需要学多少东西。假如背不住生命周期，你的组件逻辑顺序大概率会变成一团糟。**“大而全”的背后，是不可忽视的学习成本**。这也是类组件的一个不便，**它太重了**，对于解决许多问题来说，编写一个类组件实在是一个过于复杂的姿势。复杂的姿势必然带来高昂的理解成本。



由于开发者编写的逻辑在**封装**后是和组件粘在一起的，这就使得类组件内部的逻辑难以实现拆分和复用。



如果你想要打破这个僵局，则需要进一步学习更加复杂的设计模式（比如高阶组件、Render Props 等），用更高的学习成本来交换一点点编码的灵活度。让人头秃



### 理解函数组件



**呼应 React 设计思想的 轻巧、简洁**



```
const dongmaoTest = props => {
  const { text } = props

  const showAlert = () => {
    alert(`我接收到的文本是${text}`)
  }

  return (
    <div className="demoFunction">
      <p>{`function 组件所接收到的来自外界的文本内容是：[${text}]`}</p>

      <button onClick={showAlert}>点击弹窗</button>
    </div>
  )
}
```



相比于类组件，函数组件肉眼可见的特质自然包括轻量、灵活、易于组织和维护、较低的学习成本等



推荐一篇[文章](https://overreacted.io/how-are-function-components-different-from-classes/) 



总结一下，文章的核心点是：**函数组件会捕获 render 内部的状态，这是两类组件最大的不同。**这句话可能有点抽象，下面会有一个例子来专门讲



#### React公式



**React 组件本身的定位就是函数，一个吃进数据、吐出 UI 的函数**

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/129546/1604564166549-06ce30a2-dd7d-4db1-bb62-5246b3bebe89.png)



React 的数据应该总是紧紧地和渲染绑定在一起的，而类组件做不到这一点。



为什么类组件做不到？



参考上面文章中的一个例子，我们先看功能的内部实现



```
import React from 'react';

class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

  handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}

export default ProfilePage;
```



[在线Demo](https://codesandbox.io/s/pjqnl16lm7?file=/src/index.js)



尝试点击基于类组件形式编写的 ProfilePage 按钮，点击的时候是Dan，点击后 3s 内把用户切换为 Sophie



这是class

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/129546/1604564580207-a1f6a98b-46f4-4a96-b3eb-be9b2eb4f169.png?x-oss-process=image%2Fresize%2Cw_1500)



这是function



![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/129546/1604564640538-2b52f843-2e12-4b86-88eb-3e51327286e4.png?x-oss-process=image%2Fresize%2Cw_1500)



明明我们是在 Dan 的主页点击的关注，结果却提示了“Followed Sophie”



> **虽然 props 本身是不可变的，但 this 却是可变的，this 上的数据是可以被修改的**，this.props 的调用每次都会获取最新的 props，而这正是 React 确保数据实时性的一个重要手段。
>
> 
>
> 这个案例中，**我们通过 setTimeout 将预期中的渲染推迟了 3s，打破了 this.props 和渲染动作之间的这种时机上的关联**，进而导致渲染时捕获到的是一个错误的、修改后的 this.props





函数组件会捕获 render 内部的状态，这句话也就是：**函数组件真正地把数据和渲染绑定到了一起**

**
**

## 总结



函数组件比起类组件“少”了很多东西，比如生命周期、对 state 的管理等。这就给函数组件的使用带来了非常多的局限性。



React-Hooks 的出现，就是为了帮助函数组件补齐这些（相对于类组件来说）缺失的能力
