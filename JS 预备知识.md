最近准备好好啃一啃js，一步一步来。

# 线程机制与事件机制

## 进程与线程

进程（process）的基本概念:
- 程序的一次执行，它占有一片独有的内存空间
- 可以通过windows任务管理器查看进程
- 进程被称为cpu的最小资源单位

线程（thread）的基本概念：
- 是进程内的一个独立执行单位
- 是程序执行的一个完整的流程
- 是cpu的最小的调度单元

放上一张图，可以更加简单的了解，这2个之间的关系：
![image.png](https://upload-images.jianshu.io/upload_images/3378252-a95130975e74f579.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

------

进程与线程的相关知识：

* 应用程序必须运行在某个进程的某个线程上
* 一个进程中至少有一个运行的线程：主线程（既然有主线程肯定就有分线程这个说法），主线程是，进程启动后自动创建的
* 一个进程中也可以同时运行多个线程，我们会说程序是多个线程运行的
* 一个进程中的数据可以供其中的多个线程直接共享
* 多个进程之间的数据是不能共享的
* 线程池（thread pool）：保存多个线程对象的容器，实现线程对象的反复利用（复用性）
-----

有时候会问到跟进程与线程相关的问题，在这里总结一下。

1.何为多进程与多线程？
多进程运行：一个应用程序可以同时启动多个实例运行。
多线程运行：在一个进程内，同时有多个线程运行。

2.比较单线程与多线程？
- 多线程
优点：有效提升cpu的利用率
 缺点：1.创建多线程会增加开销；
             2线程切换也会有开销；
             3.死锁与状态同步问题

- 单线程
优点：顺序编程 简单易懂
缺点：效率低

3.JS是单线程还是多线程？
- js是单线程运行的
- H5中的web workers可以多线程运行

4.浏览器运行是单线程还是多线程？
都是多线程

5.浏览器运行是单进程还是多进程？
- 有的是单进程。比如：Firefox和老版本的IE
- 有的是多进程：比如：chrome和新版IE
- 如何查看浏览器是否是多线程运行的呢？通过任务管理器-->查看进程

# 浏览器内核
- 浏览器内核就是支撑浏览器运行的最核心的程序
- 不同的浏览器 内核可能不一样 这里举几个例子
Chrome，Safari：webkit
firefox：Gecko
IE：Trident
360、搜狗等国内浏览器：Trident+webkit
这里说一句题外话，国产浏览器称自己为双核，其实就是用了2个内核，还是比较垃圾。在正常显示网页的时候，国产浏览器使用的内核是webkit，当涉及到金钱交易的时候会把内核设置为Trident，IE的内核对安全性要求比较高。
- 内核是由很多模块组成的，下面简单列举一下
js引擎模块：负责js程序的编译与运行
html，css文档解析模块：负责页面文本的解析
DOM/CSS模块：负责dom/css在内存中的相关处理（这里相当于把解析后的文档抽象成数据结构，例如domtree cssomtree）
布局和渲染模块：负责页面的布局和效果绘制
以上这4个可以称为主线程。
...还有很多模块 这里就不过多提及。
下面说3个比较典型的3个分线程：
定时器模块：负责定时器的管理
Dom事件响应模块：负责事件的管理
网络请求模块：负责ajax请求
这里我把分线程理解为异步。
附上一张图，简单明了：
![image.png](https://upload-images.jianshu.io/upload_images/3378252-35a988ce46534d5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 定时器引发的思考
- 1. 定时器真是定时执行的吗?
  * 定时器并不能保证真正定时执行
  * 一般会延迟一丁点(可以接受), 也有可能延迟很长时间(不能接受)
```js
var start = Date.now()
	console.log('启动定时器前...')
	setTimeout(function() {
		console.log('定时器执行了', Date.now() - start)
	}, 200)
	console.log('启动定时器后...')

	// 做一个长时间的工作
	for(var i = 0; i < 1000000000; i++) {

	}
```
这是输出：
![image.png](https://upload-images.jianshu.io/upload_images/3378252-d17ca3a960a1117f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2. 定时器回调函数是在哪个线程执行的?
    * 在主线程执行的, js是单线程的
    * 这里可以这么说，先执行同步任务再执行异步任务，异步任务包括（promise的回调，dom的回调，settimeout的回调等）
- 3. 定时器是如何实现的?
    * 事件循环模型
![image.png](https://upload-images.jianshu.io/upload_images/3378252-f49b0c9a78d3de4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# JS是单线程执行的
- 1. 如何证明js执行是单线程的?
  * setTimeout()的回调函数是在主线程执行的
  * 定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行
- 2. 为什么js要用单线程模式, 而不用多线程模式?
      * JavaScript的单线程，与它的用途有关。
      * 作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。
     * 这决定了它只能是单线程，否则会带来很复杂的同步问题。
 这里简单举一个例子来说明，假如js是多线程的，那么线程p1和线程p2都可以对dom进行操作，如果p1的操作是更改dom，p2的操作是删除dom，那能一边删除一边更改么- -
- 3. 代码的分类
   * 初始化代码
   * 回调代码
   这是一种分法，我也喜欢分为同步任务+异步任务
- 4. js引擎执行代码的基本流程
   * 先执行初始化代码: 包含一些特别  的代码   回调函数(异步执行)
    * 设置定时器
    * 绑定事件监听
    * 发送ajax请求
   * 后面在某个时刻才会执行回调代码
 
```js
setTimeout(function() {
				console.log('timeout 2222')
				alert('22222222')
			}, 2000)
			setTimeout(function() {
				console.log('timeout 1111')
				alert('1111111')
			}, 1000)
			setTimeout(function() {
				console.log('timeout() 00000')
			}, 0)

			function fn() {
				console.log('fn()')
			}
			fn()

			console.log('alert()之前')
			alert('------') //暂停当前主线程的执行, 同时暂停计时, 点击确定后, 恢复程序执行和计时
			console.log('alert()之后')
```
输出：
![image.png](https://upload-images.jianshu.io/upload_images/3378252-e70af7aab71fc776.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 浏览器的事件循环（轮询）模型
- 模型原理图
![image.png](https://upload-images.jianshu.io/upload_images/3378252-81cabaed5c088e7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
https://segmentfault.com/a/1190000013739041
这篇文章写得挺好的

面试题:
  1. 使用setInterval()和setTimeout()分别实现每隔100ms输出当前次数, 共输出10次
  2. 分析比较这2种实现的区别
分析
  * setInterval的回调函数将尝试每隔100ms执行一次, 只要回调代码执行的时间不超过100ms就能定时
  * setTimeout的方式: 如果回调代码执行需要xms, 下次回调的时间就会被延迟xms
