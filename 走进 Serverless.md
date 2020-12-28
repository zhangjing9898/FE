#  走进 Serverless 



刚接触的时候， 我会认为 FaaS 就是 Serverless，或者说 Serverless 就没有服务器。对 Serverless 到底是什么并没有一个很清晰的认知。‘



其实可以从广义和狭义两个角度入手



## 广义的 Serverless



> 广义的 Serverless 是指：构建和运行软件时不需要关心服务器的一种架构思想



 虽然 Serverless 翻译过来是 “无服务器”，但这并不代表着应用运行不需要服务器，而是开发者不需要关心服务器



## 狭义的 Serverless



广义的 Serverless 更多是指一种技术理念，狭义的 Serverless 则是现阶段主流的技术实现



![image-20201226154711463](/Users/jingzhang/Library/Application Support/typora-user-images/image-20201226154711463.png)



来个例子🌰



假设你要实现一个接口，返回一个数组，你用的是 Node.js，传统的写法如下：



```js
// index.js
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

// 定义 /list 路由
app.get('/list', (req, res) => {
  const data = ['a', 'b', 'c'];
  // 返回 data 数组
  res.json(data);
});

// 监听 3000 端口并启动服务
app.listen(port, () => {
  console.log(`Express running on http://localhost:${port}`);
});

```

如果想让这个接口对外提供服务，还要买一台服务器，在服务器上安装 Node.js 运行环境，然后把这段代码上传到服务器上，通过 node index.js 命令运行起来。然后还要购买域名（如 www.example.com），将域名解析到服务器上，再在服务器上安装 Nginx ，编写 Nginx 配置监听服务器的 80 端口，并将域名下的请求转发到 3000 端口



既然 Serverless 架构可以让你不关心服务器，那怎么用 Serverless 架构去实现这个功能呢？**答案就是 FaaS。**

特点：

- 不用运维
- 事件驱动 
- 按量付费
- 弹性升缩

FaaS（Function as a Service）本质上是一个函数运行平台，大多 FaaS 产品都支持 Node.js、Python、Java等编程语言，你可以选择你喜欢的编程语言编写函数并运行。函数运行时，你对底层的服务器是无感知的，FaaS 产品会负责资源的调度和运维，**这是它的特点之一，不用运维**

FaaS 中的函数也不是持续运行的，而是通过**事件进行触发**，比如 HTTP 事件、消息事件等，产生事件的源头叫**触发器**，FaaS 平台会集成这些触发器，我们直接用就行，**这是 FaaS 的第二个特点，事件驱动**

**FaaS 的第三个特点是按量付费**

 FaaS 产品的收费方式，都是按照函数执行次数和执行时消耗的 CPU、内存等资源进行计费的

![image-20201226155110085](/Users/jingzhang/Library/Application Support/typora-user-images/image-20201226155110085.png)



基于 FaaS，实现上面的接口就简单了，你只需要写一个函数，然后把代码部署到 FaaS 产品上，设置 HTTP 触发器就可以了



```js
function handler(event) {
  // data 数组就是接口的返回数据
  const data = ['a', 'b', 'c'];
  return data;
}
```

那假设接下来你要实现一个计算 PV 的接口，简单一点你直接在内存中存储 PV，使用 FaaS 你可能会这样写：

```js
// 使用全局变量存储当前 PV
let pv = 0;
function handler(event) {
  // 用户每访问一次就给 PV 的值加 1
  pv++;
  return pv;
}
```



当你把函数部署到 FaaS，然后访问接口时，会发现得到的结果永远都是 1。这是因为 FaaS 每次执行函数时，都会初始化一个新的运行环境，然后从头开始执行整个代码，而不是只执行其中的 handler 方法



执行完毕后，运行环境就会被释放。这样每次函数执行，都是新的运行环境，自然不同函数之间就无法共用 pv 这个变量了。**这是 FaaS 的另一个特点，无状态**。



然而有时候你可能就想要在不同函数中共享内存，怎么办呢？这个问题抽象一下，就是分布式中的状态共享问题。**为了解决这个问题，所以 BaaS（Backend as a Service）也被纳入 Serverless 的一部分。**



 BaaS 本质上就是把后端功能封装起来，以接口的形式提供服务。BaaS 的涵盖范围比较广，包含任何应用运行所依赖的服务，典型的有数据库、中间件等。

在 Serverless 架构中，常见的 BaaS 产品有 AWS DynamoDB、阿里云表格存储、消息中间件等，这些服务都可以通过 API 进行访问

这样在 FaaS 中，你就可以通过接口来使用 BaaS，基于 BaaS 来存储数据、实现函数间状态共享了



还是刚才的例子🌰

要实现 pv 统计功能，你可以用表格存储这个 BaaS 服务，表格存储是一个 NoSQL 数据库，可以通过 API 来访问



```js
const tablestore = require('tablestore');
async function handler(event) {
  // 从表格存储中获取当前 PV
  let pv = await tablestore.get();
  // 用户每访问一次就给 pv 的值加 1
  pv += 1;
  // 保存最新 PV
  await tablestore.save(pv);
  return pv;
}
```



**基于 FaaS 和 BaaS 的架构，是一种计算和存储分离的架构**



计算由 FaaS 负责，存储由 BaaS 负责，计算和存储也被分开部署和收费

基于 FaaS 和 BaaS ，你的应用就实现了自动弹性伸缩、按量付费、不用关心服务器



### Serverless 的优缺点



**Serverless 的优点主要有：不用运维、弹性伸缩、节省成本、开发简单、降低风险、易于扩展**



#### 缺点

1. **应用性能瓶颈**
   1. 基于 Serverless 架构的应用，函数运行前需要现初始化函数运行环境，这个过程需要消耗一定时间。因为函数不是持续“在线”的，而是需要运行的时候才启动（不像传统应用，服务是一直启动的）
   2. 从资源利用率来讲，这种模式可以节省资源，但从应用性能上来讲，这就会降低应用性能

2. **通信效率低**
   1. 传统的 MVC（Model-View-Controller） 架构模式中，View 层方法调用 Model 层方法，都是在内存中进行的。
   2. 而在 Serverless 应用中，函数与函数之间就完全独立了。如果两个函数的数据有依赖，需要进行通信、交换数据，就要进行函数与函数之间的调用（调用方式是 HTTP 调用）。相比之前的内存调用，数据交互效率显然低了很多。而这个问题的本质，是 FaaS 还没有比较好的数据通信协议或方案。
3. **开发调试复杂**
   1. 开发、调试、部署工具链并不完善

