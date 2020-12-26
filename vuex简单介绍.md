# vuex简单介绍

前面对vue的源码进行了简单的解析，接下来vuex的简单剖析一下，当我们开发的不是单页面的时候，或者说涉及的项目比较大的时候，通过简答的父子组件之间值的传递，这种办法不太科学。

所以需要一个多组件状态共享的东西，简单来说，这就是vuex的作用。

### 1. vuex是什么

- GitHub站点 https://github.com/vuejs/vuex
- 在线文档: https://vuex.vuejs.org/zh-cn/
- 简单来说，它是对应用中组件的状态进行了集中式的一个管理(读/写)

### 2. 状态自管理应用

- state: 驱动应用的数据源
- view: 以声明方式将state映射到视图 声明方式 -> eg:{{count}}
- actions: 响应在view上的用户输入导致的状态变化(包含n个更新状态的方法) action->函数
![单向数据流](https://vuex.vuejs.org/flow.png)
- 举一个例子🌰
  - 点击一个button，状态改变到页面改变的过程：
  - view -> 点击 button -> 需要actions中的方法 -> 方法用于改变值 -> 进入state状态管理 -> 改变state中的状态 -> 状态改变后，view更新 

### 3. 多组件共享状态的problem

- 多个视图依赖同一个状态
- 来自不同的视图的行为 需要变更同一个状态
- 没有vuex的解决方法
  - 将数据和操作数据的行为都定义在父组件
  - 将数据和操作数据的行为传递给需要的各个子组件(有时候会多级传递)

![](https://vuex.vuejs.org/vuex.png)

## vuex的基础概念

下面列举一些vuex的基础概念

### state

- vuex管理的状态 它是一个`对象`
- 它是唯一的
- 例子🌰
  
```js
const state = {
  a: initValue
}
```

### mutations

- 包含多个`直接更新`state的方法 它也是一个`对象`
- 谁来触发？
  - action中的commit('mutation名称')
- 只能包含同步的代码，不能写异步
- 例子🌰
  
```js
const mutations = {
  xxx(state, data){
    // 更新state的某个属性
  }
}
```

### actions

- 包含多个事件回调函数 它也是一个`对象`
- 通过执行 commit()来触发mutation的调用 间接更新state
- 如何触发？
  - 组件中 store.dispatch('action名称)
- 可以异步执行(定时器，ajax)
- 例子🌰

```js
const actions = {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

### getters

- 包含多个计算属性(get) 同理 也是一个`对象`
- 如何读取？
  - store.getters.doneTodosCount
- 例子🌰

```JS
const getters = {
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

### modules 

- 包含多个module
- 一个module是一个store的配置对象

### 向外暴露store对象

```js
export default new Vuex.store({
  state,
  mutations,
  getters,
  actions
})
```

### 组件内

```js
import {mapGetters, mapActions} from 'vuex';
export default {
  computed: mapGetters(['aaa']);
  methods: mapActions(['bbb']);
}
```

### 映射store

```js
import store from './store';
new Vue({
  store
})
```
### store对象

- 所有用vuex管理的组件中都多了一个属性$store, 它就是一个store对象
- 属性:
  - state: 注册的state对象
  - getters: 注册的getters对象
- 方法:
	- dispatch(actionName, data): 分发调用action
