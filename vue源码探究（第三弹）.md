# vue源码探究（第三弹）

结束了之前prepare，现在开始正式进入vue源码探究部分

## 数据代理

先从简单的入手，我们先手动实现一个简易版的数据代理吧。

#### 基础知识

1. vue数据代理: data对象的所有属性的操作(读/写)由vm对象来代理操作
2. 好处: 通过vm对象就可以方便的操作data中的数据
3. 实现:
  1). 通过Object.defineProperty(vm, key, {})给vm添加与data对象的属性对应的属性
  2). 所有添加的属性都包含get/set方法
  3). 在get/set方法中去操作data中对应的属性

**举个例子🌰**

```JS
<div id="test"></div>

<script type="text/javascript" src="js/mvvm/compile.js"></script>
<script type="text/javascript" src="js/mvvm/mvvm.js"></script>
<script type="text/javascript" src="js/mvvm/observer.js"></script>
<script type="text/javascript" src="js/mvvm/watcher.js"></script>
<script type="text/javascript">
  // 这里取名为mvvm 是因为在mvvm.js中 
  // 是这样暴露的 function MVVM(options) {} 这里的MVVM和vue是一样的 只是一个命名的问题
  const vm = new MVVM({
    el: "#test",
    data: {
      name: '张三2'
    }
  })
  console.log(vm.name)  // 读取的是data中的name,  vm代理对data的读操作
  vm.name = '李四2' // 数据保存到data中的name上, vm代理对data的写操作
  console.log(vm.name, vm._data.name) // 李四2 李四2
</script>
```

上面这部分是为了简单说明，我们写的mvvm实现了简单的数据代理，接下来，我们来剖析一下它的内部实现。
先揭一个底，它的核心实现就是之前讲过的**Object.defineProperty**

```js
/*
相关于Vue的构造函数
 */
function MVVM(options) {
  // 将选项对象保存到vm
  this.$options = options;
  // 将data对象保存到vm和datq变量中
  var data = this._data = this.$options.data;
  //将vm保存在me变量中
  var me = this;
  // 遍历data中所有属性
  Object.keys(data).forEach(function (key) { // 属性名: name
    // 对指定属性实现代理
    me._proxy(key);
  });

  // 对data进行监视
  observe(data, this);

  // 创建一个用来编译模板的compile对象
  this.$compile = new Compile(options.el || document.body, this)
}

MVVM.prototype = {
  $watch: function (key, cb, options) {
    new Watcher(this, key, cb);
  },

  // 对指定属性实现代理
  _proxy: function (key) {
    // 保存vm
    var me = this;
    // 给vm添加指定属性名的属性(使用属性描述)
    Object.defineProperty(me, key, {
      configurable: false, // 不能再重新定义
      enumerable: true, // 可以枚举
      // 当通过vm.name读取属性值时自动调用
      get: function proxyGetter() {
        // 读取data中对应属性值返回(实现代理读操作)
        return me._data[key];
      },
      // 当通过vm.name = 'xxx'时自动调用
      set: function proxySetter(newVal) {
        // 将最新的值保存到data中对应的属性上(实现代理写操作)
        me._data[key] = newVal;
      }
    });
  }
};
```

----------

#### Tips：

在源码学习的过程中，掌握到了一个小技巧，读源码的时候，debugger会更加的方便。

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1572926434269-55586aca-b5cc-46fb-906a-ebb574280453.png) 

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1572926463552-ae1d7b3c-52d9-4c1e-a229-340a9ae4bfd8.png) 

非常好用，我之前太蠢了，只会第一个`resume script execution`

#### 最后

未完待续... 
接下来，还有一个更有趣的东西

**下一章继续~**
