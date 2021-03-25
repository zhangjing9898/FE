- 理解
  * 就是一块“底盘”，一个代码段所在区域
  * 它是静态的（相对于上下文对象），在编写代码时就确定了 
- 分类
  * 全局作用域
  * 函数作用域
  * 没有块作用域（ES6有了）
- 作用
  * 隔离变量，不同作用域下同名变量不会有冲突
```
//没块作用域
  if(true) {
    var c = 3
  }
  console.log(c)
//输出 3

  var a = 10,
    b = 20
  function fn(x) {
    var a = 100,
      c = 300;
    console.log('fn()', a, b, c, x)
    function bar(x) {
      var a = 1000,
        d = 400
      console.log('bar()', a, b, c, d, x)
    }

    bar(100)
    bar(200)
  }
  fn(10);
  //输出
  // fn() 100 20 300 10
  //bar() 1000 20 300 400 100
  //bar() 1000 20 300 400 200
```
--------------------------------------------
# 作用域与执行上下文
- 区别1
  * 全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时就已经确定了，而不是在函数调用时
  * 全局执行上下文环境是在全局作用域确定之后，js代码马上执行之前创建
  * 函数执行上下文是在调用函数时，函数体代码执行之前创建
- 区别2
  * 作用域是静态的，只要函数定义好了就一直存在，且不会再变化
  * 执行上下文是动态的，调用函数时创建，函数调用结束时就会自动释放
- 联系
  * 执行上下文（对象）是从属于所在的作用域
  * 全局上下文环境===>全局作用域
  * 函数上下文环境===>对应的函数使用域
```
  var a = 10,
    b = 20
  function fn(x) {
    var a = 100,
      c = 300;
    console.log('fn()', a, b, c, x)
    function bar(x) {
      var a = 1000,
        d = 400
      console.log('bar()', a, b, c, d, x)
    }

    bar(100)
    bar(200)
  }
  fn(10);
  //3个作用域，4个上下文环境
```
---------------------------------------------------
# 作用域链
- 理解 
  * 多个上下级关系的作用域形成的链，它是方向是从下向上的（从内到外）
  * 查找变量时就是沿着作用域链来查找的
- 查找一个变量的查找规则
  * 在当前作用域下的执行上下文中查找对应的属性，如果有直接返回，否则进入2
  * 在上一级作用域的执行上下文中查找对应的属性，如果有直接返回，否则进入3
  * 再次执行2的相同操作，直到全局作用域，如果还找不到就抛出找不到的异常
```
  var a = 1
  function fn1() {
    var b = 2
    function fn2() {
      var c = 3
      console.log(c)
      console.log(b)
      console.log(a)
      console.log(d)
    }
    fn2()
  }
  fn1();
  //输出 3 2 1 ERROR:Uncaught ReferenceError: d is not defined
```
---------------------------------------------------
# 作用域面试题
```
  var x = 10;
  function fn() {
    console.log(x);
  }
  function show(f) {
    var x = 20;
    f();
  }
  show(fn);
//输出10


  var fn = function () {
    console.log(fn)
  }
  fn()
//输出  function（）{console.log（fn）}
  var obj = {
    fn2: function () {
//  	console.log(this===obj);     //true
//   console.log(fn2)			//fn2 is not defined
//     console.log(this.fn2)   //function(){console.log(this.fn2)}
			console.log(obj.fn2);			//function(){console.log(this.fn2)}
    }
  }
  obj.fn2()
  //输出  见上面
```
