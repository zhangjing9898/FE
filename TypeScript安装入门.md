# TypeScript安装入门

TypeScript 作为 JavaScript 语言的超集，它为 JavaScript 添加了可选择的类型标注，大大增强了代码的可读性和可维护性。同时，它提供最新和不断发展的 JavaScript 特性，能让我们建立更健壮的组件。

## 安装

```shell
tnpm install -g typescript
```

检查是否安装成功

```shell
tsc -v
```

# TypeScript的Hello world

Index.ts

```ts
function greeter(person: string) {
  console.log('hello' + person);
}

let user = '冬猫';

greeter(user);
```

执行TS编译，Node运行

```shell
// 进到该文件目录下
$ tsc index.ts
// 这时候 会有对应的js文件生成
$ node index.js
// 这时候的控制台会打印出
hello冬猫
```







