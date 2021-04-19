

<a name="bqwLd"></a>
# 🚀 实现一个简易版 Promise

<br />我们先来搭建构建函数的**大体框架**
```javascript
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'

function MyPromise(fn) {
  
  const that = this // 代码可能会异步执行，用于获取正确的this对象
  that.state = PENDING // 一开始Promise的状态应该是pending
  that.value = null // value变量用于保存resolve或者reject中传入的值
  
  that.resolvedCallbacks = []
  that.rejectedCallbacks = []
  // 待完善 resolve 和 reject 函数
  // 待完善执行 fn 函数
}
```

- 首先我们创建了三个常量用于表示状态 （PS：对于经常使用的一些值都应该通过常量来管理，便于开发及后期维护）
- 在函数体内部首先创建了常量that，因为代码可能会异步执行，用于获取正确的this对象
- 一开始Promise的状态应该是**pending**
- value变量用于保存resolve或者reject中传入的值
- **resolvedCallbacks** 和** rejectedCallbacks** 用于保存then中的回调，因为当执行完Promise时状态可能还是等待中，这时候应该把then中的回调保存起来用于状态改变时使用


<br />接下来完善** resolve **和** reject** 函数，添加在MyPromise函数体内部
```javascript
function resolve(value) {
  // 判断当前状态是否为等待中，因为规范规定只有等待态才可以改变状态
  if (that.state === PENDING) {
    that.state = RESOLVED
    that.value = value
    that.resolvedCallbacks.map(cb => cb(that.value))
  }
}

function reject(value) {
  if (that.state === PENDING) {
    that.state = REJECTED
    that.value = value
    that.rejectedCallbacks.map(cb => cb(that.value))
  }
}
```
这两个函数代码类似：

- 首先两个函数都得判断当前状态是否为等待中，因为规范规定只有等待态才可以改变状态
- 将当前状态更改为对应状态，并且将传入的值赋值给value
- 遍历回调数组并执行


<br />完成以上两个函数以后，我们就该实现如何执行Promise中传入的函数了
```javascript
try {
  fn(resolve, reject)
} catch (e) {
  reject(e)
}
```

- 实现很简单，执行传入的参数并且将之前两个函数当做参数传进去
- 要注意的是，可能执行函数过程中会遇到错误，需要捕获错误并且执行reject函数


<br />最后来实现较为复杂的then函数
```javascript
MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : r => {
          throw r
        }
  
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value)
  }
  if (that.state === REJECTED) {
    onRejected(that.value)
  }
}
```


- 首先判断两个参数是否为函数类型，因为这两个参数是可选参数
- 当参数不是函数类型时，需要创建一个函数赋值给对应的参数，同时也实现了透传，比如如下代码
```javascript
// 该代码目前在简单版中会报错
// 只是作为一个透传的例子
Promise.resolve(4).then().then((value) => console.log(value))
```
接下来就是一系列判断状态的逻辑，当状态不是等待态时，就去执行相对应的函数。如果状态是等待态的话，就往回调函数中push函数，比如如下代码就会进入等待态的逻辑
