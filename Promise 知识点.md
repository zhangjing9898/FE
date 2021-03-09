
> 一个 Promise 就是一个代表了异步操作最终完成或者失败的对象。这是MDN上关于Promise的解释。在前端开发中，Promise经常被拿来用于处理异步和回调的问题，来规避回调地狱和更好排布异步相关的代码


# 1.Promise的三种状态 

promise翻译过来就是承诺，既是承诺，那承诺的结果就会有成功和失败两种。而且，我们许下承诺之后不会立即得到结果，在获得成功或是失败的结果之前，我们还需要一点时间来履行这个承诺。Promise的构造其实像极了我们生活中的承诺

https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1571279716754-a807f47b-7ad3-44d4-89e7-af7b497867f0.png

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568864354725-04c7d9b5-efa2-48c4-9a7d-f6cc81e2594d.png) 

上面这张图就是Promise的结构图。就像我们生活中的承诺一样，Promise也存在三种状态,一种是履行承诺的pending状态，一种是承诺失败时的Rejected状态，再就是承诺成功时Fullfilled状态。



```js

let love = new Promise((resolve, reject) => {
    // 模拟异步处理
    setTimeout(() => {         //开始谈恋爱，不过恋爱的结果要以后才知道
        let happy = Math.random() >= 0.3 ? true : false
        if ( happy ) {
            resolve('marry')    //恋爱成功，决定结婚
        } else {
            reject('break')     //恋爱失败，决定分手
        }    
    }, 500)
})

love.then(result => {
    console.log(result)   //处理恋爱成功的回调，result是上面resolve传过来的'marry'
}).catch(result => {
    console.log(result)   //处理恋爱失败的回调，result是上面reject传过来的'break'
})

```

上面的代码就是一个简单却完整的Promise的例子。需要特别注意的是，Promise在经过pending状态达到成功或失败状态时就会凝固，即到达成功状态后再也不会失败，失败以后也不会回到成功状态。

所以下面的Promise一定是失败状态的，即便reject后面跟了resolve也没用

```js
let love = new Promise((resolve, reject) => {
    reject('break')
    resolve('marry')
})

love.then(result => {
    console.log(result)  // break
}).catch(result => {
    console.log(result)
})
```

# 2.Promise的then与catch的几种写法

#### 第一种，最常见的就是上面的写法, 使用then来捕捉resolve状态，使用catch来捕捉reject状态

```js
love.then(result => {
    console.log(result)
}).catch(result => {
    console.log(result)
})

```

####  第二种，不写catch, 把用来捕捉reject状态的函数也写到then里，但是效果和上面一样

```js
love4.then(result => {
    console.log('succ', result)
}, err => {
    console.log('err', err)
})

```

#### 第三种，分开写，也是可以的

```js
love.then(result => {     //只捕捉和处理成功状态
    console.log(result)
})

love.catch(result => {    //只捕捉和处理失败状态
    console.log(result)  
})

```


# 3.快速构建一个成功或是失败状态的Promise

Promise自带了两种方法，我们可以利用它们快速构建一个Promise，一个是Promise.resolve(), 用于构建成功状态的Promise;另一个是Promise.reject()，用于构建失败状态的Promise。

```js

let p1 = Promise.resolve('success')
console.log(p1)   // 打出来的是 Promise {'success'}
p1.then(result => {
    console.log(result)  //打出来上面resolve传过来的字符串'success'
})

let p2 = Promise.reject('failed')   //上面是一个成功状态Promise，这是一失败状态的Promise
p2.catch(result => {
    console.log(result)
})

```

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568865397118-bafbfc94-7fe1-4dcc-b421-c4ec27a48404.png) 

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568865406317-6ed7a0fd-7f22-4ea9-a637-882cab15eb01.png) 

# 4.使用Promise.all()来处理一类前端场景

在前端的开发实践中，我们有时会遇到需要发送多个请求并根据请求顺序返回数据的需求，比如，我们要发送a、b、c三个请求，这三个请求返回的数据分别为a1、a2、a3，而我们想要a1、a2、a3按照我们希望的顺序返回。那么，使用Promise.all()方法可以完美的解决这一问题。

```js
//模拟异步请求的函数
let request = (name, time) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            let random = Math.random()
            if (random >= 0.2) {
                resolve(`${name}成功了`)
            } else {
                reject(`${name}失败了`)
            }
        }, time)    
    })
}

//构建三个Promise实例
let a = request('小明', 1000)
let b = request('小红', 500)
let c = request('小华', 1500)

//使用Promise.all(), 注意它接收的是一个数组作为参数 
Promise.all([b,a,c]).then(result => {
    console.log(result)
}).catch(result => {
    console.log(result)
})

```

把上面的代码复制下来放到浏览器的调试控制台里多执行几次（第二次执行需要刷新）会发生什么事情呢？你可能猜到了：如果三个请求都成功的话，那么这三个请求所返回的数据就是按照发送请求的顺序排列的，即['小红成功了', '小明成功了', '小华成功了']，而且还是以数组形式返回的；而当其中有请求失败了的话，就只会返回最先失败的结果。

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568873629169-2f166304-329c-4a9e-a680-4d37386a27d5.png) 

当然，除了这个场景以外，Promise.all()方法还能用于其它地方。比如说，一个页面上有两个请求，只有拿到了这两个请求的数据，页面才会展示，在这之前会显示一个loading加载图。使用Promise.all()也是可以非常简洁的解决这个问题。


# 5.Promise的链式调用

上面说过的then方法，在每次使用后依然会继续返回一个Promise对象。

```js
let p = Promise.resolve('success')
let response = p.then(result => {
    console.log(result)
})
console.log(response)   //打出来的response是一个Promise对象
```

因为then之后返回的还是一个Promise对象，那我们就可以继续then，只不过后面then拿到的参数是上一个then里return的内容，而这个return的内容既可以是普通的字符串、数字等（最后都会被封装成Promise）也可以是自己写的一个Promise对象。

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568873900649-40133e84-b681-4d95-bb7a-b58210ae5fc6.png) 

一个经典的链式调用：

```js
let love = new Promise((resolve, reject) => {
    setTimeout(() => {
        let happy = Math.random() >= 0.3 ? true : false
        if ( happy ) {
            resolve('marry')
        } else {
            reject('break')
        }    
    }, 500)
})

let haveChild = new Promise((resolve, reject) => {
    setTimeout(() => {
       resolve('孩子生了！') 
    }, 1000)
})


love.then(result => {
    console.log(result)
    return haveChild  // 这里返回一个Promise对象,它的resolve会被下一个then捕捉
}).then(result => {
    console.log(result)
    return '最后，他们白头偕老！' //这里返回的字符串会传给下一个then
}).then(result => {
    console.log(result)
}).catch(error => { 
    console.log(error)
})

```

![undefined](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/129546/1568874016311-05b73a10-a5e6-42e7-835d-2f232f59130f.png) 

这里需要注意的是，在链式调用的最一定要加上一个catch来捕捉链条中可能出现的错误！

# 6.Promise链式调用可以处理的一个业务场景

当我们需要发送多个请求，而后一个请求总是依赖前一个请求的结果时，Promise的链式操作就可以派上用场了。

我们使用axios来演示，axios本身就使用Promise进行封装，代码如下：

```js

let request = (url) => {
    let result = axios.get(url)  //result是Promise对象
    result.then(response => {
        return response
    }).catch(error => {
        throw new Error('出错了!')
    })
}

request(url0).then(response => {
    return request(response.data.link)
}).then(result => {
    console.log(result)
}).catch(error => {
    console.log(error)
})

```
