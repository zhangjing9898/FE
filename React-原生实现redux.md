## redux学习 从原生开始

之前开发react全家桶，使用了react-redux，对redux和react-redux也只停留在对api的使用，最近读了读源码，尝试自己的写了一个简单版的redux。
感觉这样收获挺大的，对于redux的运用会更加熟悉，对整体也有个清晰的脉络。

这是一个简单的例子：

首先是index页面:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>redux</title>
</head>
<body>
<div id="root">
    <h1 id="title"></h1>
    <div id="content"></div>
</div>
<script src="index.js"></script>
</body>
</html>
```

这是index.js页面:

```js
function createStore(reducer){
    let state,listener=[];
    //默认第一次  给state赋一个初始值
      dispatch({});
     //防止action是一个undefined的，而undefined.type会报错
     //要求 传一个参数 action 一个对象(type:"修改某个状态的标识",其他的参数是修改后的值（载荷）)          function dispatch(action) {
            //根据action中type值不同进行不同的处理匹配
             //reducer的作用就是修改state  state=reducer(action,state);
            //依次让订阅的方法执行
             //  listener.forEach(item=>item());
        }
    let subscribe=(fn)=>{
        listener=[...listener,fn];
        //返回一个函数 取消订阅
  return ()=>{
            //将数组中的fn 删除
  listener=listener.filter(item=>item!=fn)
        }
    }
    //防止state泄露 外面可以修改 我们可以返回一个跟state长得一样的对象  但是地址不一样
 //我们创建一个函数：这个函数返回一个跟state一样的对象  let getState=()=>(JSON.parse(JSON.stringify(state)));
    // let getState=()=>(...state);  // 这种办法也可以 但是源码中是上面这种写法 因为...是ES7语法 怕某些不支持
  return {dispatch,getState,subscribe};
}
//使用常量来设置 类型 //一般写成大写 const TITLE_CHANGE_TEXT="title_change_text";
const CONTENT_CHANGE_TEXT="content_change_text";
const CONTENT_CHANGE_COLOR="content_change_color";
let initState={
    titleState:{text:"HOW I MET YOUR MOTHER",color:"red"},
    contentState:{text:"Ted Robin",color:"red"}
}
function reducer(action,state=initState) {
    switch (action.type){
        case TITLE_CHANGE_TEXT:
            //这里修改的是状态state 注意修改状态一定要给他一个新的地址
 //这里不能直接赋值更改，因为是一个地址套另一个地址，你只改了里面的地址，但是外面地址没变 //... 扩展复制 //return 一个修改后的state  return {...state,titleState:{...state.titleState,text:action.text}};
            break;
        case CONTENT_CHANGE_TEXT:
            return {...state,contentState:{...state.contentState,text:action.text}};
            break;
        case CONTENT_CHANGE_COLOR:
            return {...state,contentState:{...state.contentState,color:action.color}};
            break;
    }
    return state;
}
//创建容器,传一个参数 函数 let store=createStore(reducer);
store.subscribe(render);
let unSub=store.subscribe(()=>{console.log("121212")});
unSub();
function Title() {
    let title=document.getElementById("title");
    title.innerHTML=store.getState().titleState.text;
    title.style.color=store.getState().titleState.color;
}
function Content() {
    let content=document.getElementById("content");
    content.innerHTML=store.getState().contentState.text;
    content.style.color=store.getState().contentState.color;
}
function render() {
    Title();
    Content();
}
setTimeout(()=>{
    store.dispatch({type:"title_change_text",text:"寻妈记"});
    store.dispatch({type:"content_change_text",text:"ted robin"});
    store.dispatch({type:"content_change_color",color:"blue"});
    // render();//派发完成之后 重新渲染页面 },1000);
render();
```

自己写的简易版createStore:

```js
function createStore(reducer) {
    let state,listener=[],getState,dispatch,subscribe;
    dispatch=(action)=>{
        state=reducer(state,action);
        listener.forEach(item=>item());
    };
    dispatch({});
    subscribe=(fn)=>{
        listener=[...listener,fn];
        return ()=>{
            listener=listener.filter(item=>item!=fn)
        }
    };
    getState=()=>(JSON.parse(JSON.stringify(state)));
    return {dispatch,getState,subscribe}
}
```

关于这方面的东西，我觉得查看代码，可读性更加强一些，就放上[GitHub地址]，里面放了所有跟redux有关以及react-redux有关的code
今天读到一篇文章，用通俗易懂的文字讲述了redux，点击这里
