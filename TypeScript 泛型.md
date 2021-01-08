# 泛型



在 C# 和 Java 这样的语言中，可以使用泛型来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件



```
function identity(arg: any): any {
  console.log(typeof (arg + '')) // 打印 string
  return arg + ''
}

console.log(identity(1)) // 打印 1

// 这时候的arg的type和return的type 变得不一致 传入number return string
// 类型变量 可以使返回值的类型与传入参数的类型是相同的
// 它是一种特殊的变量，只用于表示类型而不是值

function identity2<T>(arg: T): T {
  return arg
}
```



*类型推论* -- 即编译器会根据传入的参数自动地帮助我们确定 `T` 的类型：



写法如下:



```
let output = identity2<string>('myString')
// *类型推论* 
let output = identity('myString')
// 类型推论帮助我们保持代码精简和高可读性
// 如果编译器不能够自动地推断出类型的话，只能像上面那样明确的传入 T 的类型
```



### 泛型变量



泛型变量可以让泛型变得更加的灵活



```
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length) // error 如果传入number number是没有.length属性的
  return arg
}
// 改写
// 我们把泛型变量 T 当做类型的一部分使用，而不是整个类型，增加了灵活性
function loggingIdentity<T>(arg: T[]): T[] {
  console.log(arg.length)
  return arg
}
```



### 泛型类型



```
// 可以使用不同的泛型参数名，只要在数量上和使用方式上能对应上就可以
function identity<T>(arg: T): T {
  return arg
}

let myIdentity: <U>(arg: U) => U = identity
```



推荐一种比较好的写法



使用 `GenericIdentityFn` 的时候，还得传入一个类型参数来指定泛型类型（这里是：`number`），锁定了之后代码里使用的类型



```
interface GenericIdentityFn<T> {
  (arg: T): T
}
function identity<T>(arg: T): T {
  return arg
}
let myIdentity: GenericIdentityFn<number> = identity
```



### 泛型类



泛型类看上去与泛型接口差不多。 泛型类使用（ `<>`）括起泛型类型，跟在类名后面。



泛型类使用起来十分的方便，可以使用下面的demo试一下



```
class GenericNumber<T> {
  zeroValue: T
  add: (x: T, y: T) => T // 成员方法
}

let myGenericNumber = new GenericNumber<number>()
myGenericNumber.zeroValue = 2
myGenericNumber.add = function(x, y) {
  return x + y
}

let stringNumeric = new GenericNumber<string>()
stringNumeric.zeroValue = 'hello '
stringNumeric.add = function(x, y) {
  return x + y
}

console.log(stringNumeric.add(stringNumeric.zeroValue, 'test'))  // hello test
```



Notice:



类有两部分：静态部分和实例部分。 泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型



### 泛型约束



```
// 定义一个接口来描述约束条件
interface Lengthwise {
  length: number
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length, arg)
  return arg
}

// 现在这个泛型函数被定义了约束，因此它不再是适用于任意类型
// loggingIdentity(3) // error
loggingIdentity({ length: 3, value: 20, other: 123 }) // ok 打印出3 { length: 3, value: 20, other: 123 }
```



##### 在泛型约束中使用类型参数



可以声明一个类型参数，且它被另一个类型参数所约束



比如，现在我们想要用属性名从对象里获取这个属性。 并且我们想要确保这个属性存在于对象 `obj` 上，因此我们需要在这两个类型之间使用约束



```
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}

let x = { a: 1, b: 2, c: 3, d: 4 }

getProperty(x, 'a') // okay
getProperty(x, 'm') // error m不属于 a、b、c、d
```
