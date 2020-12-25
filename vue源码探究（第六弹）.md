# vue源码探究(第六弹)

继续之前的，差不多到最后一part了，数据的双向绑定。

### 双向数据绑定

- 双向数据绑定是建立在单向数据绑定(model ==> view)的基础之上的
- 双向数据绑定的实现流程
  - 在解析v-model指令中，给当前元素添加input监听
  - 当input的value发生变化时，将最新的值赋值给当前表达式所对应的data属性

### 举个例子🌰

```html
<div id="test">
  <input type="text" v-model="msg">
  <p>{{msg}}</p>
</div>
<script type="text/javascript">
  new MVVM({
    el: '#test',
    data: {
      msg: 'haha'
    }
  })
  // 通过v-model 我们可以实现数据的双向绑定 下面来简述一下流程
  // 1. 进入 new MVVM()
  // 2. observe 对数据进行监视
  // 3. new Observer(value)
  // 4. 对指定属性实现响应式的数据绑定 defineReactive
  // 5. new Dep -> defineProperty 添加get和set fn
  // 6. 进入compile编译
  // 7. step1 -> node2Fragment 文档碎片 将node进行批量处理
  // 8. step2 -> init() -> compileElement() -> 元素ele node -> compile() -> attrName = v-module
  // 9. bind() -> 先getVal() -> modelUpdater 更新数据
  // 10. new watcher() 用于监听属性值的变化 自动调用 更新对应的节点
</script>
```

最后附上一张图
![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1572926239294-1f491bd8-1691-4778-980a-ea58669bdc63.png) 

#### End

vue的源码探究暂时就到这里了，bye🎓
