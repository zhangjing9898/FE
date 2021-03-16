- 变量声明提升
  * 通过var定义（声明）的变量，在定义语句之前就可以访问的
  * 值:undefined
- 函数声明提升
  * 通过function声明的函数，在之前就可以直接调用
  * 值:函数定义（对象）
- 问题：变量提升和函数提升是如何产生的？

```
 console.log('-----')
  /*
  面试题 : 输出 undefined
   */
  var a = 3
  function fn () {
    console.log(a)
    var a = 4
  }
  fn()  //输出undefined

  console.log(b) //undefined  变量提升
  fn2() //可调用  函数提升  //输出fn2()
  // fn3() //不能  变量提升

  var b = 3
  function fn2() {
    console.log('fn2()')
  }

  var fn3 = function () {
    console.log('fn3()')
  }
```
