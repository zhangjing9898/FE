## 基础类型



TypeScript 支持与 JavaScript 几乎相同的数据类型，此外还提供了实用的枚举类型方便我们使用。



### 布尔值 boolean

### 数字 number

- 所有数字都是浮点数
- 支持十进制和十六进制 + 二进制和八进制

### 字符串 string

- 支持模板字符串



```
let name: string = 'dongmao'
let age: number = 21
let sentence: string = `hello, my name is ${name}. I will be ${ age + 1 } years old next year`
```



### 数组



```
let list1: number[] = [1,2,3]
// 数组泛型 确定了array中必须是number
let list2: Array<number> = [1,2,3]
```



### 元祖



可以定义一对值分别为 `string` 和 `number` 类型的元组。



```
let x: [string, number]
x = ['hello', 100]  // ok
x = [11 , 'dongmao']  // error

console.log(x[0].substr(1));  // ok 打印出ello
console.log(x[1].substr(1)); //error number没有substr的方法
```



### 枚举



`enum` 类型是对 JavaScript 标准数据类型的一个补充



```
enum Color {
  Red,
  Green,
  Blue = 1
}
let c: Color = Color.Blue
console.log(c) // 打印出1

enum Color {
  Red,
  Green,
  Blue
}
let c: Color = Color.Blue
console.log(c) // 打印出2,默认 red: 0, green: 1, Blue: 2

enum Color {
  Red,
  Green,
  Blue = 1
}
let c: Color = Color.Blue
let a: Color = Color.Red
let b: Color = Color.Green
console.log(c, a, b) // 打印出 1 0 1

// 枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为 2，但是不确定它映射到 Color 里的哪个名字，我们可以查找相应的名字：
enum Color {
  Red = 1,
  Green = 2,
  Blue = 3
}
let colorName: string = Color[2]
console.log(colorName) // 打印出 Green
```



### any



```
// 不会有任何报错
let notSure: any = 4
notSure = 'hello'
notSure = false
notSure = [1,'strng', false]

// 在对现有代码进行改写的时候，any 类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。并且当你只知道一部分数据的类型时，any 类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据：
let array: any[] = [1, true, 'free']
array[1] = 'free'
array[1] = true
array[1] = [1,3213,1]
```



### void

- 表示没有任何类型

### null undefined

### never

表示的是那些永不存在的值的类型



```
function error(message: string): never {
  throw new Error(message); // ok
  console.log('123'); // error
  return error("error"); // ok
  while (true) {} // ok
}
```



### object



`object` 表示非原始类型，也就是除 `number`，`string`，`boolean`，`symbol`，`null`或`undefined` 之外的类型。



```
declare function create(o: object | null): void

create({ prop: 0}) // ok
create([1, 2, 3]) // ok
create(null) // ok
create(42) // error
create(true) // error
create(undefined)  // error
```



### 类型断言



相当于其它语言里的类型转换



```
let someValue: any = 123
let lengthVal: number = (someValue as string).length  // 这时候的someValue的类型变成了string
```
