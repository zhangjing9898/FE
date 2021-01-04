# 变量声明

## 数组解构

```ts
let a = [1, 2]
let [first, second] = a
console.log(first, second) // 1 2

// 函数参数 解构
let c: [number, number] = [2, 3]
function test1([first, second]: [number, number]): void {
  console.log(first, second) // 2 3
}
test1(c)

// 在数组里使用 ... 语法创建剩余变量
let [a, ...b] = [1, 2, 3, 4, 5]
console.log(a + ' ---- ' + b)  // 1 ---- 2,3,4,5

// 也可以忽略你不关心的尾随元素
let [a] = [1, 2, 3, 4]
console.log(a) // 1

// 或其它元素
let [, a, b, e] = [1, 2, 3, 4]
console.log(a, b, e) // 2 3 4
```

## 对象解构

同上

## 默认值

```ts
function a(b: { name: string; age?: number }):void {
  // 现在即使age没传入 为undefined 也会有默认值为12
  let {name, age = 12} = b;
}
```

## 函数声明

```ts
type c = {
  a: string
  b?: number
}
function f({ a, b }: c): void {
  console.log(a, b)
}
const test1: c = {
  a: 1, // error
  b: 12
}
const test2: c = {
  a: '1', // ok
  b: 12
}
f(test1)

function f({ a = '', b = 0 } : c): void {
  console.log(a, b)
}
f({ a: 'test' }) // OK, 默认 b = 0
f() // erro
f({}) // Error
// 第二种写法
function f({ a = '', b = 0 } = { a: '' }): void {
  console.log(a, b)
}
f({ a: 'test' }) // OK, 默认 b = 0
f() // OK, 默认 a: '', b = 0
f({}) // Error, 一旦传入参数则 a 是必须的
```

## 展开

```ts
let a = [1, 2]
let b = [3, 4]
let c = [0, ...a, ...b, 5]
console.log(c)  // [ 0, 1, 2, 3, 4, 5 ]

let defaults = {
  food: 'spicy',
  price: '$10'
}
let search = { ...defaults, food: 'sweet' }
console.log(search) // { food: 'sweet', price: '$10' }  后面的会覆盖之前的
```

