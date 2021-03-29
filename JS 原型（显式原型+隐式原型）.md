先讲
# 原型（prototype）

- 函数的prototype属性
  * 每个函数都有一个prototype属性，它默认指向一个Object空对象（即：原型对象）
  * 原型对象中有一个属性constructor，它指向函数对象
- 给原型对象添加属性（一般都是方法）
  * 作用：函数的所有实例对象自动拥有原型中的属性（方法）

```
// 每个函数都有一个prototype属性, 它默认指向一个Object空对象(即称为: 原型对象)
  console.log(Date.prototype, typeof Date.prototype)
  //输出 xxx（一大块）  typeof object
  function Fun () {//alt + shift +r(重命名rename)

  }
  console.log(Fun.prototype)  // 默认指向一个Object空对象(没有我们的属性)
//输出 object 里面有constructor+__proto__

// 原型对象中有一个属性constructor, 它指向函数对象
console.log(Date.prototype.constructor===Date)   //ture
console.log(Fun.prototype.constructor===Fun)			//ture

//给原型对象添加属性(一般是方法) ===>实例对象可以访问
Fun.prototype.test = function () {
    console.log('test()')
}
var fun = new Fun()
fun.test()   //输出test()
```
----------------
接下来讲讲
# 显式原型与隐式原型

- 每个函数function都有一个prototype，即显式原型（属性）
- 每个实例对象都有一个__proto__，可称为隐私原型（属性）
- 对象的隐式原型的值 等于 其对应构造函数的显式原型的值
- 内存结构
- 总结：
  * 函数的prototype属性：在定义函数时自动添加的，默认值是一个空object对象
  * 对象的__proto__属性：创建对象时自动添加的，默认值为构造函数的prototype属性值
 * 程序员能直接操作显式原型，但不能直接操作隐式原型（ES6之前）

这里加一点ECMAScript 是JavaScript的一种规范
```
  //定义构造函数
  function Fn() {   // 内部语句: this.prototype = {}

  }
  // 1. 每个函数function都有一个prototype，即显式原型属性, 默认指向一个空的Object对象
  console.log(Fn.prototype)
  //输出object
  
  // 2. 每个实例对象都有一个__proto__，可称为隐式原型
  //创建实例对象
  var fn = new Fn()  // 内部语句: this.__proto__ = Fn.prototype
  console.log(fn.__proto__)
  //输出 object
  
  // 3. 对象的隐式原型的值为其对应构造函数的显式原型的值
  console.log(Fn.prototype===fn.__proto__) // true
  //给原型添加方法
  Fn.prototype.test = function () {
    console.log('test()')
  }
  //通过实例调用原型的方法
  fn.test()  //输出 test()
```
![显式原型与隐式原型.png](https://upload-images.jianshu.io/upload_images/3378252-091e216042194cc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

