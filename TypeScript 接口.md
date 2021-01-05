# 接口



TypeScript 的核心原则之一是对值所具有的结构进行类型检查



```
// 1.类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以
// 2.接口里的属性不全都是必需的
interface Square {
  color: string
  area: number
}

interface SquareConfig {
  color?: string
  width?: number
}

function createSquare(config: SquareConfig): Square {
  let newSquare: Square = {
    color: 'white',
    area: 100
  }

  if (config.color) {
    newSquare.color = config.color
  }

  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}
createSquare({ color: 'black' })
```



## 只读属性



一些对象属性只能在对象刚刚创建的时候修改其值。 可以在属性名前用 `readonly` 来指定只读属性



```
interface Point {
  readonly x: number
  readonly y: number
}

let p1:Point = { x: 10, y: 20}

p1.x = 20; // error
```



`ReadonlyArray<T>` 类型，把所有可变方法去掉了，数组创建后再也不能被修改



```
let a: number[] = [1, 2, 3, 4]
let ro: ReadonlyArray<number> = a
ro[0] = 12 // error!
ro.push(5) // error!
ro.length = 100 // error! 
a = ro // error 赋值也不行
// 可以使用类型断言重写类型
a = ro as number[]  // ok
```



### readonly vs const



最简单判断该用 `readonly` 还是 `const` 的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 `const`，若做为属性则使用 `readonly`



## 额外的属性



当我们想传入额外的属性的时候，按照之前的写法，会有报错



```
interface SquareConfig {
  color?: string
  width?: number
}

function createSquare(config: SquareConfig) : { color: string, area: number} {
  let newC = {color:'white', area: 100}
  return newC;
}
// error: 'colour' 不存在于类型 'SquareConfig' 中
createSquare({ coulor: 'white', area: 10});
```



改写一下：



```
// 添加一个字符串索引签名,就可以增加额外的属性
interface SquareConfig {
  color?: string
  width?: number
  [propName: string]: any
}
```



在这我们要表示的是`SquareConfig` 可以有任意数量的属性，并且只要它们不是 `color` 和 `width`，那么就无所谓它们的类型是什么



## 函数类型



接口不光可以描述带有属性的普通对象，还能进行函数类型的描述



```
interface SearchFunc {
  // source和subString代表函数的入参  boolean 表示返回类型
  (source: string, subString: string): boolean
}

let myFn: SearchFunc

myFn = function(source: string, subString: string): boolean {
  let res = source.search(subString);
  return res > -1;
}

// 对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配
myFn = function (src: string, subStr: string): boolean {
  let res = src.search(subStr);
  return res > -1;
}

// 简化
myFn = function (src,subStr){
  let res = src.search(subStr);
  return res > -1;
}
```



## 可索引的类型



```
interface StringArray {
  [index: number]: string
}

let myArray: StringArray
myArray = ['a', 'ab']

let myStr: string = myArray[1]

console.log(myStr) // ab

interface StringArray {
  [index: string]: string
}

let myArray: StringArray
myArray = {
  a: '12',
  b: '34'
}

let myStr: string = myArray['a']

console.log(myStr) // 12

interface Animal {
  name: string
}

interface Dog extends Animal {
  breed: string
}

interface Not {
  [x: number]: Animal // error 但是数字索引的返回值必须是字符串索引返回值类型的子类型 
  [x: string]: Dog
}

interface NumberDic {
  [index: string]: number
  length: number // ok
  name: string // error 与索引类型返回值的类型不匹配
}
```



## 类类型



### 实现接口



同java，如果implement一个接口，需要实现里面的方法



接口一般描述了类的公共部分



```
interface Clock {
  currentTime: Date
  // 在接口中描述一个method 在class中实现它
  setTime(d: Date)
}

class ClockClass implements Clock {
  currentTime: Date
  setTime(d: Date) {
    this.currentTime = d
  }
  constructor(){}
}
```



### class的静态party和实例part的diff



- class一般有两个类型

- - 静态类型
  - 实例类型



```
// 专门为构造函数使用
interface ClockConstructor {
  new (hour: number, min: number)
}

// 专门为实例方法使用
interface ClockInterfact {
  tick()
}

function createClock(ctor: ClockConstructor, h: number, m: number): ClockInterfact {
  return new ctor(h, m)
}

class Test implements ClockInterfact {
  constructor(h: number, m: number) {}
  tick() {
    console.log('tick~~~~')
  }
}

let a = createClock(Test, 12, 5)
console.log(a.tick()) // tick~~~
```

## 继承接口



和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里



```
interface Square {
  area: number
}

interface BigSquare extends Square {
  length: number
}

// let square: BigSquare;
// 另一种写法
let square = {} as BigSquare;
square.area = 20
square.length = 10

// 一个接口可以继承多个接口，创建出多个接口的合成接口
interface Square {
  area: number
}

interface Stroke {
  color: string
}

interface Shape extends Square, Stroke {
  length: number
}

let testShape = {} as Shape;
testShape.area = 10
testShape.color = 'red'
testShape.length = 20
```



## 混合类型



接口能够描述 JavaScript 里丰富的类型，有时你会希望一个对象可以同时具有多种类型。一个例子就是，一个对象可以同时做为函数和对象使用，并带有额外的属性。



```
interface Counter {
  (start: number): string // 函数
  interval: number // 对象的interval 属性
  resetFn(): void // 函数
}

function getCounter(): Counter {
  let counter = function(start: number) {} as Counter // 强制断言为counter属性
  counter.interval = 20
  counter.resetFn = () => {
    console.log('resetFn')
  }
  return counter
}

let c = getCounter()
c(10) // 可以作为一个函数
c.resetFn()  // 调用内部函数
c.interval = 5.0  // 访问对象属性
```



## 接口继承class



当接口继承了一个类类型时，它会继承类的成员但不包括其实现。



```
class Control {
  // 私有属性
  private state: any
}
// 接口继承到class的私有成员 当你去implements 接口的时候 需要是实现里面的东西
interface SelectableControl extends Control {
  select(): void // 提供select函数
}

class Button extends Control implements SelectableControl {
  select() {}
}

class TextBox extends Control {
  select() {}
}

// Error：“ImageC”类型缺少“state”属性。
// 接口继承到class的私有成员 当你去implements 接口的时候 需要是实现里面的东西
// 因为ImageC 不是 control的子类 无法继承到state 所以报错
class ImageC implements SelectableControl {
  select() {}
}
```
