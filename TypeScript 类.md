# 类

```ts
class Greeter {
  greeting: string // 属性
  constructor(message: string) {
    this.greeting = message
  }
  greet() {
    return 'Hello, ' + this.greeting
  }
}
// 使用 new 构造了 Greeter 类的一个实例
// 会调用之前定义的构造函数，创建一个 Greeter 类型的新对象，并执行构造函数初始化它
let greeter = new Greeter('world')
greeter.greet()
```

## 继承

```ts
class Animal {
  name: string
  constructor(name: string) {
    this.name = name
  }
  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m.`)
  }
}

class Snake extends Animal {
  constructor(name: string) {
    super(name) // 调用父类构造器
  }
  move(distance: number = 5) {
    console.log('Slithering...')
    super.move(distance)
  }
}

class Horse extends Animal {
  constructor(name: string) {
    super(name)
  }
  move(distance: number = 45) {
    console.log('Galloping...')
    super.move(distance)
  }
}

let sam = new Snake('Sammy')
let tom: Animal = new Horse('Tommy')  // 可以tom指定为animal类型

sam.move() // Slithering... Sammy moved 5m.
tom.move(34) // Galloping...Tommy moved 34m.
```

## public private protected

- 在 TypeScript 里，成员都默认为 `public`
- 不需要专门写 public name: string  这种
- 当成员被标记成 `private` 时，它就不能在声明它的类的外部访问

```ts
class Animal {
  private name: string
  constructor(name: string) {
    this.name = name
  }
}

class Rhino extends Animal {
  constructor() {
    super('Rhino')
  }
}

class Employee {
  private name: string
  constructor(name: string) {
    this.name = name
  }
}

let animal = new Animal('Goat')
let rhino = new Rhino()
let employee = new Employee('Bob')

animal = rhino // 共享private name
animal = employee // 错误: Animal 与 Employee 不兼容.
```

- `protected` 修饰符与 `private` 修饰符的行为很相似，但有一点不同，`protected`成员只能在子类中访问

```ts
class Person {
  protected name: string
  constructor(name: string) {
    this.name = name
  }
}

class Employee extends Person {
  private department: string

  constructor(name: string, department: string) {
    super(name) // 继承person
    this.department = department
  }

  getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`
  }
}

let howard = new Employee('Howard', 'Sales')
console.log(howard.getElevatorPitch())
console.log(howard.name) // error 只能在子类/派生类 中使用 不能在外部使用

class Person {
  protected name: string
  protected constructor(name: string) {
    this.name = name
  }
}

// Employee 能够继承 Person
class Employee extends Person {
  private department: string

  constructor(name: string, department: string) {
    super(name)
    this.department = department
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`
  }
}

let howard = new Employee('Howard', 'Sales')
let john = new Person('John') // 错误: 'Person' 的构造函数是被保护的.
```

## readonly 修饰符

 `readonly` 关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

```ts
readonly name: string
```

### 参数属性

简化代码而已

```ts
class Person {
  constructor(readonly name: string) {}
}
let john = new Person('John')
console.log(john.name)
john.name = 'peter' // error
```



## 存取器

`TypeScript` 支持通过 `getters/setters` 来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问。

```ts
let passcode = 'secret passcode'

class Employee {
  // private 一般加_ 代表私有
  private _fullName: string

  get fullName(): string {
    return this._fullName
  }

  set fullName(newName: string) {
    if (passcode && passcode === 'secret passcode') {
      this._fullName = newName
    } else {
      console.log('Error: Unauthorized update of employee!')
    }
  }
}

let employee = new Employee()
employee.fullName = 'Bob Smith'
if (employee.fullName) {
  console.log(employee.fullName)
}

//会有一个报错
examples/lesson/index.ts:499:7 - error TS1056: Accessors are only available when targeting ECMAScript 5 and higher.
499   get fullName(): string {
          ~~~~~~~~
examples/lesson/index.ts:503:7 - error TS1056: Accessors are only available when targeting ECMAScript 5 and higher.
503   set fullName(newName: string) {
          ~~~~~~~~
Found 2 errors.

```

报错原因：存取器要求你将编译器设置为输出 ECMAScript 5 或更高。 不支持降级到 ECMAScript 3。其次，只带有 `get` 不带有 `set` 的存取器自动被推断为 `readonly`

这时候我们需要这样执行，即可

```shel
$ tsc index.ts --targe es5
$ node index.js
```

## 静态属性

静态属性，通过class本身.来获得 而不是this.

```ts
class Grid {
  // 静态属性
  // 通过class本身.来获得 而不是this.
  static origin = { x: 0, y: 0 }

  scale: number

  constructor(scale: number) {
    this.scale = scale
  }
  // 实例方法
  calculateDistanceFromOrigin(point: { x: number; y: number }) {
    // 坐标差
    let xDist = point.x - Grid.origin.x
    let yDist = point.y - Grid.origin.y
    return Math.sqrt(xDist * xDist + yDist * yDist) * this.scale
  }
}

let grid1 = new Grid(1.0) // 1x scale
let grid2 = new Grid(5.0) // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 3, y: 4 })) // 5
console.log(grid2.calculateDistanceFromOrigin({ x: 3, y: 4 })) // 25
```

## 抽象类

 一般不会直接被实例化

```ts
// 抽象类 支持访问修饰符
abstract class Animal {
  // 抽象方法
  abstract makeSound(): void
  move(): void {
    console.log('roaming the earth...')
  }
}
```

```ts
abstract class Department {
  name: string

  constructor(name: string) {
    this.name = name
  }

  printName(): void {
    console.log('Department name: ' + this.name)
  }

  abstract printMeeting(): void // 必须在派生类中实现
}

class AccountingDepartment extends Department {
  constructor() {
    super('Accounting and Auditing') // 在派生类的构造函数中必须调用 super()
  }

  // 实现abstract method
  printMeeting(): void {
    console.log('The Accounting Department meets each Monday at 10am.')
  }
  // 定义一个自己的成员方法
  generateReports(): void {
    console.log('Generating accounting reports...')
  }
}

let department: Department // 允许创建一个对抽象类型的引用
department = new Department() // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment() // 允许对一个抽象子类进行实例化和赋值
department.printName()
department.printMeeting()
department.generateReports() // 错误: 方法在声明的抽象类中不存在
// 如果想让他ok 就把 
let department: AccountingDepartment
```



## 高级tips

### 构造函数

```ts
class Greeter {
  static standardGreeting = 'Hello, there'

  greeting: string

  constructor(message?: string) {
    this.greeting = message
  }

  greet() {
    if (this.greeting) {
      return 'Hello, ' + this.greeting
    } else {
      return Greeter.standardGreeting
    }
  }
}

let greeter: Greeter
greeter = new Greeter()
console.log(greeter.greet())

// 创建了一个叫做 greeterMaker 的变量。这个变量保存了这个类或者说保存了类构造函数
// 使用 typeof Greeter，意思是取 Greeter 类的类型，而不是实例的类型
// 更确切的说，"告诉我 Greeter 标识符的类型"，也就是构造函数的类型。 这个类型包含了类的所有静态成员和构造函数
let greeterMaker: typeof Greeter = Greeter
greeterMaker.standardGreeting = 'Hey there'

let greeter2: Greeter = new greeterMaker()
console.log(greeter2.greet())
```

### 把类当做接口使用

不建议这种写法

```ts
class Point {
  x: number
  y: number
}

interface Point3d extends Point {
  z: number
}

let point3d: Point3d = { x: 1, y: 2, z: 3 }
```
