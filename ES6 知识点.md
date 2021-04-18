<a name="LZAkB"></a>
# 🍿 var、let 及 const 区别


<a name="kRCDW"></a>
## Q：什么是提升？什么是暂时性死区？var、let 及 const 区别？


<a name="WcSUa"></a>
### 提升概念（hoisting）


<a name="b4kVH"></a>
#### var声明的变量 -> 发生提升
**注意：var声明的变量 -> 发生提升**
```javascript
console.log(a) // undefined
var a = 1
```

<br />虽然变量还没有被声明，但是我们却可以使用这个未被声明的变量，这种情况就叫做提升。并且提升的是声明。<br />对于这种情况，我们可以把代码这样来看
```javascript
var a
console.log(a) // undefined
a = 1
```
🌰例子2：
```javascript
var a = 10
var a
console.log(a)
```
可以这么拆解：所以打印的值肯定不是undefined，该是10。

var声明的变量会发生提升，其实不仅变量会提升函数也会被提升。<br />

<a name="LFrvq"></a>
#### 函数提升 优先于 变量提升
```javascript
console.log(a) // ƒ a() {}
function a() {}
var a = 1
```
<br />对于上述代码，打印结果会是ƒ a() {}，即使变量声明在函数之后，这也说明了函数会被提升，并且优先于变量提升。<br />
<br />**var的问题：**使用var声明的变量会_被提升到作用域的顶部_<br />
<br />**let和const：**
```javascript
var a = 1
let b = 1
const c = 1
console.log(window.b) // undefined
console.log(window. c) // undefined
```

- 全局作用域下使用let 和const声明变量，变量并不会被挂载到window上，这一点就和var声明有了**区别**
```javascript
function test(){
  console.log(a)
  let a
}
test()
```

- 当我们在声明a之前如果使用了a，就会出现报错的情况

![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618623758392-3e22eec7-ed07-403a-81b5-789a34431457.png#clientId=u3f3f306d-1d78-4&from=paste&height=92&id=u1aa19fa3&margin=%5Bobject%20Object%5D&originHeight=184&originWidth=920&originalType=binary&size=128818&status=done&style=none&taskId=u102f7e28-6efb-4c95-a738-325df8e8fab&width=460)<br />你可能会认为这里也出现了提升的情况，但是因为某些原因导致不能访问。<br />

- 报错的原因是因为**存在暂时性死区**，我们不能在声明前就使用变量，这也是**let和const优于var**的一点。然后这里你认为的提升和var的提升是有区别的，虽然变量在编译的环节中被告知在这块作用域中可以访问，但是访问是受限制的。


<br />那么到这里，想必大家也都明白var、let及const区别了，不知道你是否会有这么一个疑问，为什么要存在提升这个事情呢，其实提升存在的根本原因就是为了解决函数间互相调用的情况<br />

```javascript
function test1() {
    test2()
}
function test2() {
    test1()
}
test1()
```

<br />假如不存在提升这个情况，那么就实现不了上述的代码，因为不可能存在 test1 在 test2 前面然后 test2 又在 test1 前面。<br />

<a name="WMuPH"></a>
### 总结


- 函数提升**优先**于变量提升 
   - 函数提升会把整个函数挪到作用域顶部 
   - 变量提升只会把**声明**挪到作用域顶部
- var存在提升，我们能在_声明之前使用_。let、const因为_暂时性死区的原因_，_不能在声明前使用_
- var在全局作用域下声明变量会导致变量挂载在window上，其他两者不会
- let和const作用基本一致，但是后者声明的变量不能再次赋值




---

<a name="RJr48"></a>
# 🥛 原型继承和 Class 继承


<a name="Sdvs3"></a>
## Q：原型如何实现继承？Class 如何实现继承？Class 本质是什么？

<br />首先先来讲下class，其实在 JS 中并不存在类，class只是语法糖，本质还是函数。<br />

```javascript
class Person {}
Person instanceof Function // true
```

<br />在上一章节中我们讲解了原型的知识点，在这一小节中我们将会使用分别使用原型和class的方式来实现继承。<br />

<a name="yizoz"></a>
### 组合继承

<br />组合继承是最常用的继承方式，<br />

```javascript
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}
function Child(value) {
  Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```


以上继承的**方式核心**：是在子类的构造函数中通过Parent.call(this)继承父类的属性，然后改变子类的原型为new Parent()来继承父类的函数。<br />
<br />这种继承方式**优点**在于构造函数可以传参，不会与父类引用属性共享，可以复用父类的函数，但是也存在一个**缺点**就是在继承父类函数的时候调用了父类构造函数，导致子类的原型上多了不需要的父类属性，存在内存上的浪费。<br />
<br />![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618624370784-eb7a5df2-6c4f-4302-b0d5-78ada446b4f8.png#clientId=u1d829b2f-fe44-4&from=paste&height=384&id=u8d5caa24&margin=%5Bobject%20Object%5D&originHeight=768&originWidth=892&originalType=binary&size=493764&status=done&style=none&taskId=u02b3ba37-9023-4f51-be72-9fc646a5aa3&width=446)<br />

<a name="pakPk"></a>
### 寄生组合继承

<br />这种继承方式对组合继承进行了优化，**组合继承缺点**在于继承父类函数时调用了构造函数，我们只需要优化掉这点就行了。<br />

```javascript
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerable: false,
    writable: true,
    configurable: true
  }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

<br />以上继承实现的**核心**就是将父类的原型赋值给了子类，并且将构造函数设置为子类，这样既解决了无用的父类属性问题，还能正确的找到子类的构造函数。<br />![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618624616256-689ce99c-1d1d-4950-ade4-29aa26af7d3c.png#clientId=u3b98de6d-7f5c-4&from=paste&height=128&id=u70769125&margin=%5Bobject%20Object%5D&originHeight=256&originWidth=662&originalType=binary&size=87598&status=done&style=none&taskId=ubd4fc94b-f703-4be6-ac99-701a02fe50f&width=331)<br />

<a name="obmnW"></a>
### Class 继承

<br />以上两种继承方式(组合继承 + 寄生组合继承)都是通过原型去解决的，在 ES6 中，我们可以使用class去实现继承，并且实现起来很简单<br />

```javascript
class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}
class Child extends Parent {
  constructor(value) {
    super(value)
    this.val = value
  }
}
let child = new Child(1)
child.getValue() // 1
child instanceof Parent // true
```

<br />class实现继承的**核心**在于使用extends表明继承自哪个父类，并且在子类构造函数中必须调用super，因为这段代码可以看成Parent.call(this, value)。<br />
<br />当然了，之前也说了在 JS 中并不存在类，class的本质就是函数<br />


---

<a name="T5dkG"></a>
# 🥏 模块化


<a name="YQrva"></a>
## Q：为什么要使用模块化？都有哪几种方式可以实现模块化，各有什么特点？


<a name="XNpXw"></a>
#### 模块化带来的好处：

- 解决命名冲突
- 提供复用性
- 提高代码可维护性



<a name="wbznc"></a>
### 立即执行函数

<br />早期，使用立即执行函数实现模块化是常见的手段，通过函数作用域解决了命名冲突、污染全局作用域的问题
```javascript
(function(globalVariable){
   globalVariable.test = function() {}
   // ... 声明各种变量、函数都不会污染全局作用域
})(globalVariable)
```


<a name="dVxf1"></a>
### AMD 和 CMD

<br />鉴于目前这两种实现方式已经很少见到，只了解这两者是如何使用的
```javascript
// AMD
define(['./a', './b'], function(a, b) {
  // 加载模块完毕可以使用
  a.do()
  b.do()
})
// CMD
define(function(require, exports, module) {
  // 加载模块
  // 可以把 require 写在函数体的任意地方实现延迟加载
  var a = require('./a')
  a.doSomething()
})
```


<a name="xT22b"></a>
### CommonJS

<br />CommonJS 最早是 Node 在使用，目前也仍然广泛使用，比如在 Webpack 中你就能见到它，当然目前在 Node 中的模块管理已经和 CommonJS 有一些区别了。<br />

```javascript
// a.js
module.exports = {
    a: 1
}
// or 
exports.a = 1

// b.js
var module = require('./a.js')
module.a // -> log 1v
```

<br />先说 require 
```javascript
var module = require('./a.js')
module.a 
// 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，
// 重要的是 module 这里，module 是 Node 独有的一个变量
module.exports = {
    a: 1
}
// module 基本实现
var module = {
  id: 'xxxx', // 我总得知道怎么去找到他吧
  exports: {} // exports 就是个空对象
}
// 这个是为什么 exports 和 module.exports 用法相似的原因
var exports = module.exports // 地址相同
var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
};
// 然后当我 require 的时候去找到独特的
// id，然后将要使用的东西用立即执行函数包装下，over
```

<br />虽然 exports 和module.exports 用法相似，但是不能对exports直接赋值。<br />**原因：**因为var exports = module.exports这句代码表明了exports和module.exports享有相同地址，通过改变对象的属性值会对两者都起效，但是如果直接对exports赋值就会导致两者不再指向同一个内存地址，修改并不会对module.exports起效。<br />

<a name="QClrh"></a>
# 💐 Proxy


<a name="FYNeD"></a>
## Q：Proxy 可以实现什么功能？

<br />如果你平时有关注 Vue 的进展的话，可能已经知道了在 Vue3.0 中将会通过**Proxy**来替换原本的**Object.defineProperty**来实现数据响应式。 Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作。<br />

```javascript
let p = new Proxy(target, handler)
```

<br />target代表需要添加代理的对象，handler用来自定义对象中的操作，比如可以用来自定义set或者get函数。<br />接下来我们通过Proxy来实现一个数据响应式<br />

```javascript
let onWatch = (obj, getLogger, setBind) => {
  let handler = {
    get(target, property, receiver) {
      // 增加的自定义get fn
      getLogger(target, property)
      // 原本：Reflect.get()方法与从对象target[propertyKey]中读取属性类似
      return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
      // 增加的自定义set fn
      setBind(value, property)
      // 原本：xxxx
      return Reflect.set(target, property, value)
    }
  }
  return new Proxy(obj, handler)
}

let obj = { a: 1 }
let p = onWatch(
  obj,
  (target, property) => {
    // get 方法
    console.log(`'${property}' = ${target[property]}`)
  },
  (v, property) => {
    // set 方法
    console.log(`监听到属性${property}改变为${v}`)
  }
)
p.a = 2 // 监听到属性a改变
p.a // 'a' = 2
```
在上述代码中，我们通过自定义set和get函数的方式，在原本的逻辑中插入了我们的函数逻辑，实现了在对对象任何属性进行读写时发出通知。<br />
<br />当然这是简单版的响应式实现，如果需要实现一个 Vue 中的响应式，需要我们在get中收集依赖，在set派发更新，之所以 Vue3.0 要使用Proxy替换原本的 API **原因**在于Proxy无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到，但是Proxy可以完美监听到任何方式的数据改变，唯一缺陷可能就是浏览器的兼容性不好<br />


---

<a name="p0dza"></a>
# 🚀 map, filter, reduce


<a name="ft5ni"></a>
## Q：map, filter, reduce 各自有什么作用？


<a name="l7NhI"></a>
### map

<br />生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中。
```javascript
[1, 2, 3].map(v => v + 1) // -> [2, 3, 4]
```
另外map的回调函数接受三个参数，分别是当前索引元素，索引，原数组
```javascript
let newArray = arr.map(callback(currentValue[, index[, array]]) {
  // return element for newArray, after executing something
}[, thisArg]);
```
**🌰例子1：问输出结果？**
```javascript
['1','2','3'].map(parseInt) // 输出 1 NaN NaN
```

<br />思路：map是将数组的每个元素传递给指定的函数处理，并返回处理后的数组，所以 ['1','2','3'].map(parseInt) 就是将_字符串_1，2，3作为元素；0，1，2作为下标分别调用 parseInt 函数。即分别求出 parseInt('1',0), parseInt('2',1), parseInt('3',2)的结果。<br />

- 第一轮遍历 parseInt('1', 0) -> 1
- 第二轮遍历 parseInt('2', 1) -> NaN
- 第三轮遍历 parseInt('3', 2) -> NaN


<br />**注意**：parseInt(_string_, _radix_)，当参数_radix_的值为 0，或没有设置该参数时，parseInt() 会根据_string_来判断数字的基<br />

<a name="FeXCQ"></a>
#### filter
也是生成一个新数组，在遍历数组的时候将返回值为true的元素放入新数组，我们可以利用这个函数删除一些不需要的元素
```javascript
let array = [1, 2, 4, 6]
let newArray = array.filter(item => item !== 6)
console.log(newArray) // [1, 2, 4]
```
<br />和map一样，filter的回调函数也接受三个参数，用处相同。注意：没有符合的，会返回一个[] 空数组
```javascript
var newArray = arr.filter(callback(element[, index[, array]])[, thisArg]) 
```
<a name="ltw5r"></a>
#### reduce

<br />reduce可以将数组中的元素通过回调函数最终转换为一个值。<br />
<br />如果我们想实现一个功能：将函数里的元素全部相加得到一个值，可能会这样写代码
```javascript
const arr = [1, 2, 3]
let total = 0
for (let i = 0; i < arr.length; i++) {
  total += arr[i]
}
console.log(total) //6 
```
但是如果我们使用reduce的话就可以将遍历部分的代码优化为一行代码
```javascript
const arr = [1, 2, 3]
const sum = arr.reduce((acc, current) => acc + current, 0)
console.log(sum)
```
对于reduce来说，它接受两个参数，分别是回调函数和初始值，接下来我们来分解上述代码中reduce的过程。<br />

1. 首先**初始值为0**，该值会在执行第一次回调函数时作为第一个参数传入
1. 回调函数接受四个参数，分别为累计值、当前元素、当前索引、原数组，后三者想必大家都可以明白作用，这里着重分析第一个参数
1. 在第一次执行回调函数时，**当前值(1)**和**初始值(0)**相加得出**结果1**，该结果会在第二次执行回调函数时当做**第一个参数**传入
1. 在第二次执行回调函数时，相加的值就分别是1和2，以此类推，循环结束后得到结果6


<br />想必通过以上的解析就能明白reduce是如何通过回调函数将所有元素最终转换为一个值的，当然reduce还可以实现很多功能，接下来我们就通过reduce来实现map函数。
```javascript
const arr = [1, 2, 3]
const mapArray = arr.map(value => value * 2)
const reduceArray = arr.reduce((acc, current) => {
  acc.push(current * 2)
  return acc
}, [])
console.log(mapArray, reduceArray) // [2, 4, 6]
```
如果你对这个实现还有困惑的话，可以根据上一步的解析步骤来分析过程。<br />

