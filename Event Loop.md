<a name="DmfLE"></a>
# 🍳 进程与线程


<a name="pNA8a"></a>
## Q：进程与线程区别？JS 单线程带来的好处？

<br />**JS 是单线程执行；线程是进程的更小单位**<br />

<a name="GwHPL"></a>
### 区别
进程和线程**本质**：两个名词都是CPU**工作时间片**的一个描述。

- 进程：描述了 CPU 在**运行指令及加载和保存上下文所需的时间**，放在应用上来说就代表了一个程序
- 线程是进程中的**更小单位**，描述了执行一段指令所需的时间。


<br />🌰例子：<br />把这些概念放到浏览器中来说。

- 当你打开一个 Tab 页时，其实就是创建了一个进程，一个进程中可以有多个线程，
   - 比如：渲染线程、JS 引擎线程、HTTP 请求线程等等。
- 当你发起一个请求时，其实就是创建了一个线程，当请求结束后，该线程可能就会被销毁。



<a name="qqXNm"></a>
### JS引擎线程 和 渲染线程

- 众所周知，在 JS 运行的时候可能会阻止 UI 渲染，这说明了两个线程是**互斥**的。


<br />**原因：**

- 因为 JS 可以修改 DOM，如果在 JS 执行的时候 UI 线程还在工作，就可能导致不能安全的渲染 UI。
- 这其实也是一个单线程的好处。得益于 JS 是单线程运行的
   - 可以达到节省内存，节约上下文切换时间。(PS:  前面这两点在服务端中更容易体现)
   - 不存在加🔐的问题
      - 锁：形象的来说就是当我读取一个数字 15 的时候，同时有两个操作对数字进行了加减，这时候结果就出现了错误。解决这个问题也不难，只需要在读取的时候加锁，直到读取完毕之前都不能进行写入操作

---

<a name="PYwNb"></a>
# 🍭 执行栈


<a name="iaIuE"></a>
## Q：什么是执行栈？

<br />执行栈：一个存储函数调用的**栈结构**，遵循先进后出的原则。<br />
<br />![](https://cdn.nlark.com/yuque/0/2021/gif/281196/1618734893691-14e13540-23fd-4fdb-9bd4-9f66e6bfd7a3.gif#clientId=uba9bc2c0-0253-4&from=drop&id=ub0759e0b&margin=%5Bobject%20Object%5D&originHeight=623&originWidth=1211&originalType=binary&size=140580&status=done&style=none&taskId=u409fba3f-f0bc-4c8a-aaa9-fbd0d81ae00)<br />
<br />执行 JS 代码的流程：

- 首先会执行一个main函数
- 然后执行我们的代码。根据先进后出的原则，后执行的函数会先弹出栈，在图中我们也可以发现，foo函数后执行，当执行完毕后就从栈中弹出了。


<br />平时在开发中，大家也可以在报错中找到执行栈的痕迹
```javascript
function foo() {
  throw new Error('error')
}
function bar() {
  foo()
}
bar()
```
![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618735094493-1dfb0ee9-aee0-4938-8230-374d4826983d.png#clientId=uba9bc2c0-0253-4&from=paste&height=198&id=u33fddd9d&margin=%5Bobject%20Object%5D&originHeight=396&originWidth=918&originalType=binary&size=172709&status=done&style=none&taskId=u0340142a-cc98-406e-a3e9-8c80b5f2863&width=459)

- 可以看到foo函数(报错) 
   - foo函数又是在bar函数中调用的

简单来说  **入栈：main() -> bar() -> foo()  出栈：反过来**<br />

<a name="prNi3"></a>
#### 爆栈

<br />因为栈可存放的函数是有限制的，一旦存放了过多的函数且没有得到释放的话，就会出现爆栈的问题。比如：使用递归
```javascript
function bar() {
  bar()
}
bar()
```
![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618735454558-45708f7c-1045-4869-90f8-fb63b71cf48e.png#clientId=uba9bc2c0-0253-4&from=paste&height=262&id=ub1783622&margin=%5Bobject%20Object%5D&originHeight=524&originWidth=874&originalType=binary&size=327836&status=done&style=none&taskId=u4bb2d2a3-001e-4bc1-9c67-38d3e54ee57&width=437)

---

<a name="Gg45V"></a>
# 🍿 浏览器中的 Event Loop


<a name="Fmsab"></a>
## Q：异步代码执行顺序？解释一下什么是 Event Loop ？

<br />大家也知道了当我们执行 JS 代码的时候其实就是往执行栈中放入函数，那么遇到异步代码的时候该怎么办？

- 其实当遇到异步的代码时，会被挂起并在需要执行的时候加入到 Task（有多种 Task） 队列中。
   - 一旦执行栈为空，**Event Loop **就会从** Task 队列 **中 **拿出** 需要执行的代码 并** 放入 **执行栈中执行。
      - 所以本质上来说 JS 中的异步还是同步行为。


<br />![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618735536839-2f91dbe6-9b02-4801-839f-c7925f6ea727.png#clientId=u9da77471-03db-4&from=paste&height=296&id=u6919eeb9&margin=%5Bobject%20Object%5D&originHeight=516&originWidth=1280&originalType=binary&size=229494&status=done&style=none&taskId=u8348f7ca-eee3-485b-95cf-086db6c7683&width=735)<br />
<br />不同的任务源会被**分配**到不同的 Task 队列中。

- 任务源可以分为微任务（microtask） 和宏任务（macrotask）。
- 在 ES6 规范中，microtask 称为jobs，macrotask 称为task。下面来看以下代码的执行顺序：



```javascript
console.log('script start')

async function async1() {
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end')
}
async1()

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')
// script start => async2 end => Promise => script end => promise1 => promise2 => async1 end => setTimeout
```

<br />**_注意：新的浏览器中不是如上打印的，因为 await 变快了，具体内容可以往下看_**<br />

- 先来解释下上述代码的** async **和** await **的执行顺序。
   - 当我们调用async1函数时，会马上输出async2 end，并且函数返回一个Promise
   - 接下来在**遇到 await **的时候会就让出线程开始执行async1外的代码，所以我们完全可以把await看成是让出线程的标志
- 然后当同步代码全部执行完毕【也就是: script start -> async2 end -> Promise -> script end】以后，就会去执行所有的异步代码。
   - 那么又会回到 await 的位置 执行返回的Promise的resolve函数，这又会把resolve丢到微任务队列中
   - 接下来去执行then中的回调，当两个then中的回调全部执行完毕以后
   - 又会回到await的位置处理返回值，这时候你可以看成是Promise.resolve(返回值).then()，然后await后的代码全部被包裹进了then的回调中，所以console.log('async1 end')会优先执行于setTimeout。


<br />如果你觉得上面这段解释还是有点绕，那么我把async的这两个函数改造成你一定能理解的代码
```javascript
new Promise((resolve, reject) => {
  console.log('async2 end')
  // Promise.resolve() 将代码插入微任务队列尾部
  // resolve 再次插入微任务队列尾部
  resolve(Promise.resolve())
}).then(() => {
  console.log('async1 end')
})
```
<br />所以 **Event Loop 执行顺序**如下所示：

- 首先执行同步代码，这属于宏任务
- 当执行完所有同步代码后，执行栈为空，查询是否有异步代码需要执行
- 执行所有微任务
- 当执行完所有微任务后，如有必要会渲染页面
- 然后开始下一轮 Event Loop，执行宏任务中的异步代码，也就是setTimeout中的回调函数
   - 所以以上代码虽然 setTimeout 写在 Promise 之前，但是因为 Promise 属于微任务而setTimeout属于宏任务，所以会有以上的打印。


<br />**微任务**包括 process.nextTick，promise，MutationObserver。<br />
<br />**宏任务**包括script，setTimeout，setInterval，setImmediate，I/O，UI rendering。<br />
<br />很多人会认为：微任务快于宏任务，这是错误的。

- 因为宏任务中包括了script，浏览器会先执行一个宏任务
   - 接下来有异步代码的话才会先执行微任务。



<a name="rQFFH"></a>
# 🐂 Node 中的 Event Loop


<a name="meZVw"></a>
## Q：Node 中的 Event Loop 和浏览器中的有什么区别？process.nexttick 执行顺序？

<br />Node 中的 Event Loop 和浏览器中的是完全不相同的东西。<br />
<br />Node 的 Event Loop 分为 6 个阶段，它们会按照顺序反复运行。<br />

- 每当进入某一个阶段的时候，都会从对应的回调队列中取出函数去执行。
- 当队列为空或者执行的回调函数数量到达系统设定的阈值，就会进入下一阶段。


<br />![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618737289167-b5f36aa5-58e2-45c6-9c6c-7f30ad183f10.png#clientId=uf978efdd-8d34-4&from=paste&height=442&id=u14e98d99&margin=%5Bobject%20Object%5D&originHeight=442&originWidth=745&originalType=binary&size=46677&status=done&style=none&taskId=u90a2a037-858e-4057-a94b-190083cc8d1&width=745)<br />

<a name="jtwqk"></a>
### timer
timers 阶段会执行 setTimeout 和 setInterval回调，并且是由 poll 阶段控制的。<br />同样，在 Node 中定时器指定的时间也不是准确时间，只能是尽快执行。<br />

<a name="xutkz"></a>
### I/O
I/O 阶段会处理一些上一轮循环中的少数未执行的 I/O 回调<br />

<a name="SldsS"></a>
### idle, prepare
idle, prepare 阶段内部实现<br />

<a name="dZUlP"></a>
### poll
poll 是一个至关重要的阶段，这一阶段中，系统会做两件事情：

- 回到 timer 阶段执行回调
- 执行 I/O 回调


<br />并且在进入该阶段时如果没有设定了 timer 的话，会发生以下两件事情：

- 如果 poll 队列不为空，会遍历回调队列并同步执行，直到队列为空或者达到系统限制
- 如果 poll 队列为空时，会有两件事发生
   - 如果有setImmediate回调需要执行，poll 阶段会停止 并且进入到 check 阶段执行回调
   - 如果没有setImmediate回调需要执行，会等待回调被加入到队列中并立即执行回调，这里同样会有个超时时间设置防止一直等待下去


<br />当然设定了 timer 的话且 poll 队列为空，则会判断是否有 timer 超时，如果有的话会回到 timer 阶段执行回调。
<a name="KDkGe"></a>
### check
check 阶段执行setImmediate
<a name="LIERe"></a>
### close callbacks
close callbacks 阶段执行 close 事件<br />在以上的内容中，我们了解了 Node 中的 Event Loop 的执行顺序，接下来我们将会通过代码的方式来深入理解这块内容。<br />首先在有些情况下，定时器的执行顺序其实是**随机**的
```javascript
setTimeout(() => {
    console.log('setTimeout')
}, 0)
setImmediate(() => {
    console.log('setImmediate')
})
```

<br />对于以上代码来说，setTimeout可能执行在前，也可能执行在后

- 首先setTimeout(fn, 0) === setTimeout(fn, 1)，这是由源码决定的
- 进入事件循环也是需要成本的，如果在准备时候花费了大于 1ms 的时间，那么在 timer 阶段就会直接执行setTimeout回调
- 那么如果准备时间花费小于 1ms，那么就是setImmediate回调先执行了

当然在某些情况下，他们的执行顺序一定是固定的，比如以下代码：
```javascript
const fs = require('fs')

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0)
    setImmediate(() => {
        console.log('immediate')
    })
})
```

<br />在上述代码中，setImmediate永远**先执行**。因为两个代码写在 IO 回调中，IO 回调是在 poll 阶段执行，当回调执行完毕后队列为空，发现存在setImmediate回调，所以就直接跳转到 check 阶段去执行回调了。<br />上面介绍的都是 macrotask 的执行情况，对于 microtask 来说，它会在以上每个阶段完成前**清空**microtask 队列，下图中的 Tick 就代表了 microtask<br />
<br />![](https://cdn.nlark.com/yuque/0/2021/png/281196/1618737664992-1b3a17d8-83fd-403a-8434-70ff91e0c305.png#clientId=uf978efdd-8d34-4&from=paste&height=1206&id=uad76dde1&margin=%5Bobject%20Object%5D&originHeight=1227&originWidth=644&originalType=binary&size=173428&status=done&style=none&taskId=u1641bdf7-912a-4592-b7f2-ca8581db955&width=633)<br />

```javascript
setTimeout(() => {
  console.log('timer21')
}, 0)

Promise.resolve().then(function() {
  console.log('promise1')
})

```
对于以上代码来说，其实和浏览器中的输出是一样的，microtask 永远执行在 macrotask 前面。<br />最后我们来讲讲 Node 中的process.nextTick，这个函数其实是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成后，如果存在 nextTick 队列，就会**清空队列中的所有回调函数**，并且优先于其他 microtask 执行。
```javascript
setTimeout(() => {
 console.log('timer1')

 Promise.resolve().then(function() {
   console.log('promise1')
 })
}, 0)

process.nextTick(() => {
 console.log('nextTick')
 process.nextTick(() => {
   console.log('nextTick')
   process.nextTick(() => {
     console.log('nextTick')
     process.nextTick(() => {
       console.log('nextTick')
     })
   })
 })
})

```
对于以上代码，大家可以发现无论如何，永远都是先把 nextTick 全部打印出来。
