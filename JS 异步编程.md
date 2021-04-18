<a name="dZlHM"></a>
# 🚀 并发和并行区别


<a name="b4t7K"></a>
## Q：并发与并行的区别？


- 并发是宏观概念
   - 我分别有任务 A 和任务 B，在一段时间内通过任务间的切换完成了这两个任务，这种情况就可以称之为并发。



- 并行是微观概念
   - 假设 CPU 中存在两个核心，那么我就可以同时完成任务 A、B。同时完成多个任务的情况就可以称之为并行。




---

<a name="xfPcc"></a>
# 🚶 回调函数（Callback）


<a name="qe828"></a>
## Q：什么是回调函数？回调函数有什么缺点？如何解决回调地狱问题？

<br />🌰例子：
```javascript
ajax(url, () => {
    // 处理逻辑
})
```

<br />但是回调函数有一个致命的弱点，就是容易写出回调地狱（Callback hell）。假设多个请求存在依赖性，你可能就会写出如下代码：
```javascript
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})

```
以上代码看起来不利于阅读和维护，当然，你可能会想说解决这个问题还不简单，把函数分开来写不就得了
```javascript
function firstAjax() {
  ajax(url1, () => {
    // 处理逻辑
    secondAjax()
  })
}
function secondAjax() {
  ajax(url2, () => {
    // 处理逻辑
  })
}
ajax(url, () => {
  // 处理逻辑
  firstAjax()
})
```
以上的代码虽然看上去利于阅读了，但是还是没有解决根本问题。<br />
<br />**回调地狱**的**根本问题**就是：

- 嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身
- 嵌套函数一多，就很难处理错误


<br />当然，回调函数还存在着别的几个**缺点**

- 比如不能使用try catch捕获错误，不能直接return  


<br />下面介绍，如何解决这些问题<br />

<a name="ME0mY"></a>
# 🍿 Generator


<a name="TtYFi"></a>
## Q：你理解的 Generator 是什么？

<br />Generator最大的**特点**就是可以控制函数的执行
```javascript
// function* 声明方式 会定义一个生成器函数 返回一个 Generator 对象
function *foo(x) {
  let y = 2 * (yield (x + 1))
  console.log('第一行：',x,y,x)
  let z = yield (y / 3)
  console.log('第二行：',x,y,x)
  return (x + y + z)
}
let it = foo(5)
console.log(it.next())   // => {value: 6, done: false} 注意: x = 5
console.log(it.next(12)) // => {value: 8, done: false} 注意: y = 2 * 12
console.log(it.next(13)) // => {value: 42, done: true} 注意: z = 13
```
<br />你也许会疑惑为什么会产生与你预想不同的值，接下来就让我为你逐行代码分析原因：<br />
<br />首先Generator函数调用和普通函数不同，它会返回一个迭代器：

- 当执行第一次next时，传参会被忽略，并且函数暂停在yield (x + 1)处，所以返回5 + 1 = 6
- 当执行第二次next时，传入的参数 = **上一个yield**的**返回值**(**注意：上一个yield 就是 yield(x+1)，相当于这里 yield(x+1) = 12 也就是入参**)，如果你不传参，yield永远返回undefined (这里代表，如果第二次it.next()，就会返回NaN)
   - 此时let y = 2 * 12，所以第二个yield等于( 2 * 12[12是因为传入的12] ) / 3 = 8
- 当执行第三次next时，传入的参数会传递给z  【因为没有yield了，直接进入return】
   - 所以z = 13, x = 5, y = 24，相加等于42


<br />Generator函数一般见到的不多，其实也于他有点绕有关系，并且一般会配合 co 库去使用。当然，我们可以通过Generator函数解决回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：
```javascript
function *fetch() {
    yield ajax(url, () => {})
    yield ajax(url1, () => {})
    yield ajax(url2, () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()
```
<a name="frGMT"></a>
# 
<a name="S1HUc"></a>
# 👮‍♀️ Promise


<a name="CQO1q"></a>
## Q：Promise 的特点是什么，分别有什么优缺点？什么是 Promise 链？Promise 构造函数执行和 then 函数执行有什么区别？

<br />Promise翻译过来就是承诺的意思，这个承诺会在未来有一个确切的答复，并且该承诺有三种状态，分别是：

- 等待（pending）
- 完成 （resolved）
- 拒绝（rejected）


<br />这个承诺一旦从等待状态变成为其他状态就永远不能更改状态了，也就是说一旦状态变为 resolved 后，就不能再次改变
```javascript
new Promise((resolve, reject) => {
  resolve('success')
  // 无效
  reject('reject')
})
```
当我们在构造Promise的时候，构造函数内部的代码是立即执行的
```javascript
new Promise((resolve, reject) => {
  console.log('new Promise')
  resolve('success')
})
console.log('finifsh')
// new Promise 
// finifsh
```
Promise实现了链式调用，也就是说每次调用then之后返回的都是一个Promise，并且是一个全新的Promise。<br />**原因：**也是因为状态不可变。如果你在then中 使用了return，那么return的值会被Promise.resolve()包装
```javascript
Promise.resolve(1)
  .then(res => {
    console.log(res) // => 1
    return 2 // 包装成 Promise.resolve(2)
  })
  .then(res => {
    console.log(res) // => 2
  })
```
当然了，Promise也很好地解决了回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：
```javascript
ajax(url)
  .then(res => {
      console.log(res)
      return ajax(url1)
  }).then(res => {
      console.log(res)
      return ajax(url2)
  }).then(res => console.log(res))
```
前面都是在讲述Promise的一些优点和特点，其实它也是存在一些**缺点**

- 无法取消Promise
- 错误需要通过回调函数捕获 



<a name="QMRYr"></a>
# 🌞 async 及 await


<a name="XoJKi"></a>
## Q：async 及 await 的特点，它们的优点和缺点分别是什么？await 原理是什么？

<br />一个函数如果加上async，那么该函数就会返回一个Promise
```javascript
async function test() {
  return "1"
}
console.log(test()) // -> Promise {<resolved>: "1"}
```
async就是将函数返回值使用Promise.resolve()包裹了下，和then中处理返回值一样，并且await只能配套async使用
```javascript
async function test() {
  let value = await sleep()
}
```
async和await可以说是异步终极解决方案了，相比直接使用Promise来说。<br />
<br />**优势：**在于处理then的调用链，能够**更清晰**准确的写出代码，毕竟写一大堆then也很恶心，并且也能优雅地解决回调地狱问题。<br />**缺点：**因为await将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了await会导致性能上的降低。 【注意：以下代码没有依赖性的话，完全可以使用** Promise.all **的方式】
```javascript
async function test() {
  // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
  // 如果有依赖性的话，其实就是解决回调地狱的例子了
  await fetch(url)
  await fetch(url1)
  await fetch(url2)
}
```
<br />看一个使用await的例子：
```javascript
let a = 0
let b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
}
b()
a++
console.log('1', a) // -> '1' 1
```
流程解释：

- 首先 函数b 先执行，在执行到 await 10 之前 变量a 还是 0 ，因为await内部实现了generator，generator会保留堆栈中东西，所以这时候a = 0 被保存了下来
- 因为 await 是异步操作，后来的表达式不返回Promise的话，就会包装成Promise.reslove(返回值)，然后会去执行函数外的同步代码
- 同步代码执行完毕后开始执行异步代码，将保存下来的值**拿出来使用**，这时候**a = 0 + 10**


<br />上述解释中提到了await内部实现了generator，其实await就generator加上Promise的**语法糖 **await = generator + promise，且内部实现了自动执行generator。<br />如果你熟悉 co 的话，其实自己就可以实现这样的语法糖。<br />

<a name="PYBC5"></a>
# 📅 常用定时器函数


<a name="KtLnU"></a>
## Q：setTimeout、setInterval、requestAnimationFrame 各有什么特点？

<br />异步编程当然少不了定时器了，常见的定时器函数有setTimeout、setInterval、requestAnimationFrame。<br />

<a name="zJ4dE"></a>
## setTimeout
很多人认为setTimeout是延时多久，那就应该是多久后执行。其实这个观点是**不完全正确**的，因为 JS 是单线程执行的，如果前面的代码影响了性能，就会导致setTimeout不会按期执行。<br />当然，我们可以通过代码去修正setTimeout，从而使定时器相对准确
```javascript
let period = 60 * 1000 * 60 * 2
let startTime = new Date().getTime()
let count = 0
let end = new Date().getTime() + period
let interval = 1000
let currentInterval = interval

function loop() {
  count++
  // 代码执行所消耗的时间
  let offset = new Date().getTime() - (startTime + count * interval);
  let diff = end - new Date().getTime()
  let h = Math.floor(diff / (60 * 1000 * 60))
  let hdiff = diff % (60 * 1000 * 60)
  let m = Math.floor(hdiff / (60 * 1000))
  let mdiff = hdiff % (60 * 1000)
  let s = mdiff / (1000)
  let sCeil = Math.ceil(s)
  let sFloor = Math.floor(s)
  // 得到下一次循环所消耗的时间
  currentInterval = interval - offset 
  console.log('时：'+h, '分：'+m, '毫秒：'+s, '秒向上取整：'+sCeil, '代码执行时间：'+offset, '下次循环间隔'+currentInterval) 

  setTimeout(loop, currentInterval)
}

setTimeout(loop, currentInterval)
```


<a name="uunYP"></a>
## setInterval<br /><br />
其实这个函数作用和setTimeout基本一致，只是该函数是每隔一段时间执行一次回调函数。<br />
<br />通常来说不建议使用setInterval

- 它和setTimeout一样，不能保证在预期的时间执行任务

它存在执行累积的问题，请看以下伪代码 【比如：没有及时清除定时器，就会产生定时器累加的问题，具体表现为：调用函数的时间间隔越来越短，越来越快】
```javascript
function demo() {
  setInterval(function(){
    console.log(2)
  },1000)
  sleep(2000) // 伪代码
}

demo()
```
以上代码在浏览器环境中，如果定时器执行过程中出现了耗时操作，多个回调函数会在耗时操作结束以后同时执行，这样可能就会带来性能上的问题。<br />

<a name="zuWwM"></a>
## requestAnimationFrame
如果你有循环定时器的需求，其实完全可以通过requestAnimationFrame来实现
```javascript
function setInterval(callback, interval) {
  let timer
  const now = Date.now
  let startTime = now()
  let endTime = startTime
  const loop = () => {
    timer = window.requestAnimationFrame(loop)
    endTime = now()
    if (endTime - startTime >= interval) {
      startTime = endTime = now()
      callback(timer)
    }
  }
  timer = window.requestAnimationFrame(loop)
  return timer
}

let a = 0
setInterval(timer => {
  console.log(1)
  a++
  if (a === 3) cancelAnimationFrame(timer)
}, 1000)
```

- 首先requestAnimationFrame自带函数节流功能，基本可以保证在 16.6 毫秒内只执行一次（不掉帧的情况下）
- 并且该函数的延时效果是精确的，没有其他定时器时间不准的问题，当然你也可以通过该函数来实现setTimeout。
