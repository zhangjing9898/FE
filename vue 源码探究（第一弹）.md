# vue 源码探究（第一弹）

最近在深 vue，接下来会有记录一系列 vue 源码解析方面的东西，主要从 3 个方面，开始解读：

- 数据代理
- 模板解析
- 数据双向绑定

在解析这些的时候，会发现源码中用到了很多 js 中比较核心但平时用的比较少的东西，在这里也先做一个 prepare 的预热吧。

### 问题

1. [].slice.call(lis): 将伪数组转换为真数组
2. node.nodeType: 得到节点类型
3. Object.defineProperty(obj, propertyName, {}): 给对象添加属性(指定描述符)
4. Object.keys(obj): 得到对象自身可枚举属性组成的数组
5. obj.hasOwnProperty(prop): 判断 prop 是否是 obj 自身的属性
6. DocumentFragment: 文档碎片(高效批量更新多个节点)

### 解析

#### 一、如何将伪数组转换为真数组

首先抛出一个问题，什么叫做伪数组？

```JS
const lis = document.getElementsByTagName('li') // lis是伪数组(是一个特别的对象, length和数值下标属性)
console.log(lis instanceof Object, lis instanceof Array, lis.forEach)// 打印结果 true false undefined
```

在 ES6 中，我们可以通过这样一个方法，把伪数组转为真数组

```js
Array.from(lis); //es6中将伪数组->真数组
```

如果说，在 ES5 中，我们应该怎么做呢？

```js
const lis2 = Array.prototype.slice.call(lis); // lis.slice() 通过lis调用slice
console.log(lis2 instanceof Object, lis2 instanceof Array, lis2.forEach);
// true true ƒ forEach() { [native code] }
```

这里再顺便拓展一下 slice 吧

```JS
  // 数组的slice()截取数组中指定部分的元素, 生成一个新的数组  [1, 3, 5, 7, 9], slice(0, 3)
  // slice2() 内部实现
  Array.prototype.slice2 = function (start, end) {
    start = start || 0
    end = start || this.length
    const arr = []
    for (var i = start; i < end; i++) {
      arr.push(this[i])
    }
    return arr
  }
```

#### 二、node.nodeType: 得到节点类型

先 show code：

```js
const elementNode = document.getElementById('test');
const attrNode = elementNode.getAttributeNode('id');
const textNode = elementNode.firstChild;
console.log(elementNode, attrNode, textNode);
// <div id="test">教育</div> id="test" "教育"
console.log(elementNode.nodeType, attrNode.nodeType, textNode.nodeType);
// 1 2 3
```

#### 三、Object.defineProperty(obj, propertyName, {}): 给对象添加属性(指定描述符)

vue 源码中用到了很多次这个属性，数据的双向绑定底层实现也是运用到它，还是很有意思，hhh

简单举一个例，让它做到数据绑定。

```JS
  const obj = {
    firstName: 'A',
    lastName: 'B'
  }
  // 如果说，我们想得到这个：obj.fullName = 'A-B'
  // 参数介绍：
  // obj 要在其上定义属性的对象。
  // prop 要定义或修改的属性的名称。
  // descriptor 将被定义或修改的属性描述符
  Object.defineProperty(obj, 'fullName', {
    // 属性描述符:
    // 数据描述符
    // 访问描述符
    // 当读取对象此属性值时自动调用, 将函数返回的值作为属性值, this为obj
    get () {
      return this.firstName + "-" + this.lastName
    },
    // 当修改了对象的当前属性值时自动调用, 监视当前属性值的变化, 修改相关的属性, this为obj
    set (value) {
      const names = value.split('-')
      this.firstName = names[0]
      this.lastName = names[1]
    }
  })

  console.log(obj.fullName) // A-B
  obj.fullName = 'C-D'
  console.log(obj.firstName, obj.lastName) // C D
  // 是不是感觉很像v-module中的双向数据绑定
```

看了简单的绑定，我们再来深入一下~
紧接着上面的

```JS
  Object.defineProperty(obj, 'fullName2', {
    configurable: false, //是否可以重新define
    enumerable: true, // 是否可以枚举(for..in / keys()) 这个在下面的object.key() 中会 深受感触
    value: 'A-B', // 指定初始值
    writable: false // value是否可以修改
  })
  console.log(obj.fullName2)  // A-B
  obj.fullName2 = 'E-F'
  console.log(obj.fullName2) // A-B
  // 为什么会出现这个问题呢？是不是因为writable的原因？那我们可以改一下吗？

  Object.defineProperty(obj, 'fullName2', {
    configurable: true,
    enumerable: true,
    value: 'G-H',
    writable: true
  })
  // 这时候会报错
  // Uncaught TypeError: Cannot redefine property:fullName2
  //at Function.defineProperty (<anonymous>)
  // 相信你已经懂了吧
```

**讲到这里，会提到一个题外话，为什么说 vue 不支持 ie8 呢？**
因为 Object.defineProperty 是 es5 中新增的东西，它不支持 ie8，而 vue 中的数据绑定等多处用到该属性，所以，这也就是为什么 vue 不支持 ie8，因为这个方法不支持 ie8

#### 四、Object.keys(obj): 得到对象自身可枚举属性组成的数组

这里敲黑板、划重点 `对象自身` `可枚举`

```js
const names = Object.keys(obj);
console.log(names);
//  ["firstName", "lastName", "fullName2"]
// 为什么没有 fullName 因为fullname中的enumerable不是true 默认为false。
```

#### 五、obj.hasOwnProperty(prop): 判断 prop 是否是 obj 自身的属性

这个属性还是有很多地方会用到。具体是干嘛的？
相信直译就可以了解，看这个属性是不是自身的。
因为有些属性虽然可以用，但有可能是原型链上的，而不是自己的，这个方法可以用于检测

```js
console.log(obj.hasOwnProperty('fullName'), obj.hasOwnProperty('toString'));
// true false
```

未完待续... 
接下来，还有一个更有趣的东西，文档碎片(高效批量更新多个节点)，这也是为什么vue可以批量更新节点。

**下一章继续~**
