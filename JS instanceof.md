- instanceof是如何判断的？
  * 我是这么认为的instanceof就是查看A是不是B的实例
  * 表达式:A instanceof B （A相当于实例对象，B是构造函数）
  * 如果B函数的显式原型在A对象的原型链上，返回true，否则返回false
- Function是通过new自己产生的实例

```
/*
  案例1
   */
  function Foo() {  }
  var f1 = new Foo()
  console.log(f1 instanceof Foo) // true
  console.log(f1 instanceof Object) // true
```
![案例一](https://upload-images.jianshu.io/upload_images/3378252-1d0fc10da85d8697.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
/*
  案例2
   */
  console.log(Object instanceof Function) // true
  console.log(Object instanceof Object) // true
  console.log(Function instanceof Function) // true
  console.log(Function instanceof Object) // true

  function Foo() {}
  console.log(Object instanceof  Foo) // false
```
![案例二](https://upload-images.jianshu.io/upload_images/3378252-d1a0326ed355aefe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

