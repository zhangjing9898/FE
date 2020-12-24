# vue源码探究(第五弹)

不知不觉，到了vue源码探究的最后一部分，也是最为复杂的一部分，数据劫持和数据的双向绑定。

## 数据劫持

数据劫持我们也可以称为数据绑定。

- 初始化显示：页面(表达式/指令)能从data读取数据显示(编译/解析)
- 更新显示：更新data中的属性数据 ===》 页面更新

#### 相关问题 prepare

- dep
  + 与data中的属性一一对应
- watcher
  + 与模板中一般指令/大括号表达式一一对应

1. 什么时候一个dep中关联多个watcher?
  多个指令或表达式用到了当前同一个属性  {{name}} {{name}}
2. 什么时候一个watcher中关联多个dep?
  多层表达式的watcher对应多个dep    {{a.b.c}}

#### 例子🌰

```html
<div id="test">
  <p>{{name}}</p>
  <p v-text="name"></p>
  <p v-text="wife.name"></p>
  <button v-on:click="update">更新</button>
</div>
<script type="text/javascript">
  new MVVM({
    el: '#test',
    data: {
      name: 'sadamu',  // dep0
      wife: { // dep1
        name: 'binbin', // dep2
        age: 18 // dep3
      }
    },
    methods: {
      update () {
        this.name = 'avatar'
      }
    }
    // 主要梳理一下，数据改变如何 -> 页面变化
    // 1. tap button -> update Fn -> this.name = 'xxx'
    // 2. 数据劫持中的set方法
    // 3. newVal ->是object,需要observe(类似a.b.c这种) —> 不是对象,进入dep.notify
    // 4. notify -> 通知all watcher -> forEach -> sub.update()
    // 5. watcher中的update 方法，进入 run()
    // 6. old 和 new value 进行比较，若不同，进入callback更新界面
    // 7. callback是updateFn,同上
  })
</script>
```

#### Dep

接下来讲讲什么是Dep？

##### Dep(Depend)

- data中的每个属性`(所有层次)`都对应一个dep对象
- dep创建的时机？
  - 在初始化define data中，每个属性会创建对应的dep对象
  - 在data中的某个属性值被设置为新的对象的时候
- 个数
  - 与data中的属性一一对应
- dep对象的结构
  {
    id, // 每个dep都有一个唯一的id
    subs //包含n个对应watcher的数组(subscribes的简写) 它是一个array[]
  }
  
  - subs属性说明
    - 当一个watcher被创建时，内部会将当前watcher对象添加到对应的dep对象的subs中
    - 当此data属性的值发生变化时，所有subs中的watcher都会收到更新的通知，从而更新对应的界面

#### Watcher

继续讲讲watcher

- 个数
  - 模板中每一个`非事件指令`或者`表达式`都对应一个watcher对象
    - 事件指令，eg:v-on、v-bind...
- 监视当前表达式数据的变化
- 创建的时机
  - 在初始化编译模板的时候
- watch对象的组成
  {
    vm, // vm对象
    exp, // 对应指令的表达式
    cb, // callback 当表达式所对应的数据发生变化时候的回调函数
    value, //当前表达式的值
    depIds // 表达式中各级属性所对应的dep对象的集合对象，它是一个对象object
  } 

#### 总结

##### dep与watcher的关系是: n:n(多对多)

  - 一个data中的属性对应一个dep，一个dep中可能包含多个watcher
    - 举个例子🌰：模板中多个地方使用一个表达式或者说使用一个属性。
    - `<div>{{name}}</div>` 
    - `<p>{{name}}</p>`
    - 这个时候，一个dep对应多个watcher
  - 模板中每一个`非事件指令`或`表达式`都对应一个watcher，一个watcher中可能包含多个dep
    - 举个例子🌰：表达式中包含了几个data属性
    - `<div>{{a.b.c}}</div>`
    - 这里的一个watcher，就对应了3个dep

##### 数据绑定使用到的2个核心技术
  
  - defineProperty()
  - 消息订阅与发布
