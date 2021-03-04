# 自己动手实现一个react-redux



之前试过自己动手实现一个redux，这篇blog主要记录动手实现一个react-redux的过程。



这个react-redux还有一点点小瑕疵，我以一个计数器作为例子来实现的。



这是目录结构:

这里的connect.js文件就是react-redux。

```
├─component
│      connect.js
│      counter.js
│
└─store
        index.js
```

index.js：

```
import React from "react";
import ReactDom,{render} from "react-dom";
import Couter from "./component/counter";
import {Provider} from "./component/connect"
import store from "./store/index"   

ReactDom.render(<Provider store={store}><Couter/></Provider>,document.getElementById("root"));
```

./store/index.js:

```
import {createStore} from "redux";
function reducer(state={number:0},action) {
    switch (action.type){
        case "add":
            return {number:state.number+action.count}
        default:
            return state;
    }
}

export default createStore(reducer);
```

./component/connect.js:

```
import React from "react";
import PropTypes from "prop-types";
//Provider是一个组件  接受一个store属性 将其内容挂载在上下文context //这样后代才可以都有办法拿到 class Provider extends React.Component{

    static childContextTypes={
        //设置上下文的类型是对象
  store:PropTypes.object
  }

    getChildContext(){
        //获取并设置后代上下文的内容
  return {store:this.props.store}
    }
    render(){
        return this.props.children
  }
}

let connect=(mapStateToProps,mapDispatchToProps)=>(comp)=>{
    return class Proxy extends React.Component{
        static contextTypes={
            store:PropTypes.object
  }
        constructor(props,context){
            super(props);
            //将参数mapStateToProps 的解构赋值 代理组件的状态
  this.setState=mapStateToProps(context.store.getState())
        }
        componentDidMount(){
            this.context.store.subscribe(()=>{
                this.setState(mapStateToProps(this.context.store.getState()))
            })
        }
        render(){
            return <comp {...this.state}  {...mapDispatchToProps(this.context.store.dispatch)}/>
        }
    }
}

export {Provider,connect}
```



./component/counter.js:

```
import React from "react";
import {connect} from "./connect";
export default class Counter extends React.Component{
    constructor(props){
        super(props);
    }

    render(){
        return (
            <div>
                <button onClick={()=>{
                    this.props.add(5);
                }}>+</button>
                {this.props.n}
            </div>
        )
    }
}

let mapStateToProps=(state)=>{
    return {n:state.number}
};
let mapDispatchToProps=(dispatch)=>{
    return {
        add:(count)=>{
            dispatch({type:"add",count:count})
        }
    }
}

export default connect(mapStateToProps,mapDispatchToProps)(Counter)
```
