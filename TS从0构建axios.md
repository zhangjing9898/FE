细碎点记录



### 1.package.json



scripts中加上这一行，可以使用shell命令：tnpm run dev 来达到node起服务的功能



```
"scripts": {
    "dev": "node examples/server.js"
 },
     
 // 如果脚本里面需要执行多个任务，那么需要明确它们的执行顺序。
 // 如果是并行执行（即同时的平行执行），可以使用&符号。
 // 如果是继发执行（即只有前一个任务成功，才执行下一个任务），可以使用&&符号。
 // eg:    
 "scripts" : {
    "dev": "tsc examples/lesson/index.ts&&node examples/lesson/index.js",  
 }
```



### 2.sever.js



```
const express = require('express')
const bodyParser = require('body-parser')
const webpack = require('webpack')
const webpackDevMiddleware = require('webpack-dev-middleware')
const webpackHotMiddleware = require('webpack-hot-middleware')
const WebpackConfig = require('./webpack.config')

const app = express()
const compiler = webpack(WebpackConfig)

// .use 代表使用express的中间件
app.use(
  webpackDevMiddleware(compiler, {
    publicPath: '/__build__/',
    stats: {
      colors: true,
      chunks: false
    }
  })
)

app.use(webpackHotMiddleware(compiler))

app.use(express.static(__dirname))

app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))


const router = express.Router()
// router 代理
router.get('/simple/get', function(req, res) {
  res.json({
    msg: `hello world`
  })
})

app.use(router)

// 端口号配置
const port = process.env.PORT || 8011
module.exports = app.listen(port, () => {
  console.log(`Server listening on http://localhost:${port}, Ctrl+C to stop`)
})
```



### 3.interface



```
export interface AxiosRequestConfig {
  url: string
  // 接口定义中 变量名+？ 表示可传可不选、非必传
  method?: Method
  data?: any
  params?: any
}
```



### 4.解构赋值 + 默认值



```
export default function xhr(config: AxiosRequestConfig): void {
  // 通过解构赋值的语法从 config 中拿到对应的属性值赋值给我的变量，并且还定义了一些默认值
  // 比如method的默认值是get
  const { data = null, url, method = 'get' } = config

  const request = new XMLHttpRequest()

  request.open(method.toUpperCase(), url, true)

  request.send(data)
}
```



### 5.webpack.config.js



```
const fs = require('fs')
const path = require('path')
const webpack = require('webpack')

module.exports = {
  mode: 'development',
  // entry多入口配置
  /**
   * 我们会在 examples 目录下建多个子目录
   * 我们会把不同章节的 demo 放到不同的子目录中
   * 每个子目录的下会创建一个 app.ts
   * app.ts 作为 webpack 构建的入口文件
   * entries 收集了多目录个入口文件，并且每个入口还引入了一个用于热更新的文件
   * entries 是一个对象，key 为目录名
   */
  entry: fs.readdirSync(__dirname).reduce((entries, dir) => {
    const fullDir = path.join(__dirname, dir)
    const entry = path.join(fullDir, 'app.ts')
    if (fs.statSync(fullDir).isDirectory() && fs.existsSync(entry)) {
      // 热更新 'webpack-hot-middleware/client‘
      entries[dir] = ['webpack-hot-middleware/client', entry]
    }

    return entries
  }, {}),

  /**
   * 根据不同的目录名称，打包生成目标 js，名称和目录名一致
   */
  output: {
    path: path.join(__dirname, '__build__'),
    filename: '[name].js',
    publicPath: '/__build__/'
  },

  module: {
    rules: [
      {
        test: /\.ts$/,
        enforce: 'pre',
        use: [
          {
            loader: 'tslint-loader'
          }
        ]
      },
      {
        test: /\.tsx?$/,
        use: [
          {
            loader: 'ts-loader',
            options: {
              transpileOnly: true
            }
          }
        ]
      }
    ]
  },

  resolve: {
    extensions: ['.ts', '.tsx', '.js']
  },

  plugins: [new webpack.HotModuleReplacementPlugin(), new webpack.NoEmitOnErrorsPlugin()]
}
```



### 6.联合类型



联合类型与交叉类型很有关联，但是使用上却完全不同。 偶尔你会遇到这种情况，一个代码库希望传入 `number` 或 `string` 类型的参数。 例如下面的函数：



```
function padLeft(value: string, padding: any) {
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value
  }
  if (typeof padding === 'string') {
    return padding + value
  }
  throw new Error(`Expected string or number, got '${padding}'.`)
}

padLeft('Hello world', 4) // returns "    Hello world"
```



`padLeft` 存在一个问题，`padding` 参数的类型指定成了 `any`。 这就是说我们可以传入一个既不是 `number` 也不是 `string` 类型的参数，但是 TypeScript 却不报错。



```
let indentedString = padLeft('Hello world', true) // 编译阶段通过，运行时报错
```



为了解决这个问题，我们可以使用 联合类型做为 `padding` 的参数：



```
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft('Hello world', true) // 编译阶段报错
```



联合类型表示一个值可以是几种类型之一。我们用竖线（`|`）分隔每个类型，所以 `number | string` 表示一个值可以是 `number` 或 `string`。

如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。



```
interface Bird {
  fly()
  layEggs()
}

interface Fish {
  swim()
  layEggs()
}

function getSmallPet(): Fish | Bird {
  // ...
}

let pet = getSmallPet()
pet.layEggs() // okay
pet.swim()    // error
```



这里的联合类型可能有点复杂：如果一个值的类型是 `A | B`，我们能够确定的是它包含了 `A` 和 `B` 中共有的成员。这个例子里，`Fish` 具有一个 `swim` 方法，我们不能确定一个 `Bird | Fish` 类型的变量是否有 `swim`方法。 如果变量在运行时是 `Bird` 类型，那么调用 `pet.swim()` 就出错了
