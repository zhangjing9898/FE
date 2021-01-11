# 高级类型



## 交叉类型



可以简单理解为 是被拼接的类型



```
// T & U 交叉类型
function extend<T, U>(first: T, second: U): T & U {
  let result = {} as T & U // 断言
  for (let id in first) {
    result[id] = first[id] as any
  }
  for (let id in second) {
    // result id 如果不存在 再赋值
    if (!result.hasOwnProperty(id)) {
      result[id] = second[id] as any
    }
  }
  return result
}

class Person {
  constructor(public name: string) {}
}

interface Loggable {
  log(): void
}

class ConsoleLogger implements Loggable {
  log() {
    // ...
    console.log('1221')
  }
}
// 交叉类型 是被拼接的类型
// 现在既有.name的属性 也有.log方法
const jim = extend(new Person('Jim'), new ConsoleLogger())
const n = jim.name
jim.log()
```



## 联合类型



```
// 联合类型表示一个值可以是几种类型之一。我们用竖线（|）分隔每个类型，所以 number | string 表示一个值可以是 number 或 string
function padLeft(value: string, padding: string | number) {
  // 判定类型
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value
  }
  if (typeof padding === 'string') {
    return padding + value
  }
  throw new Error(`Expected string or number, got '${padding}'.`)
}

console.log(padLeft('Hello world', 4)) // returns "    Hello world"

// 如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员
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
pet.swim() // error 只能包含交叉集合
```



如何解决上面提到的问题，可以使用类型保护



## 类型保护



### 类型谓词



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

// pet is Fish 就是类型谓词
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined
}

if (isFish(pet)) {
  pet.swim()
} else {
  pet.fly()
}
```



### Typeof 类型保护



```
function padLeft(value: string, padding: string | number) {
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value
  }
  if (typeof padding === 'string') {
    return padding + value
  }
  throw new Error(`Expected string or number, got '${padding}'.`)
}
```



Notice:



这些 `typeof` 类型保护只有两种形式能被识别：`typeof v === "typename"` 和 `typeof v !== "typename"`， `"typename"`必须是 `"number"`， `"string"`，`"boolean"` 或 `"symbol"`。 但是 TypeScript 并不会阻止你与其它字符串比较，只是 TypeScript 不会把那些表达式识别为类型保护。



### instanceof 类型保护



```
class Bird {
  fly() {
    console.log('bird fly')
  }

  layEggs() {
    console.log('bird lay eggs')
  }
}

class Fish {
  swim() {
    console.log('fish swim')
  }

  layEggs() {
    console.log('fish lay eggs')
  }
}

function getRandomPet() {
  return Math.random() > 0.5 ? new Bird() : new Fish()
}

let pet = getRandomPet()

if (pet instanceof Bird) {
  pet.fly()
}
if (pet instanceof Fish) {
  pet.swim()
}
```



## 可以为 null 的类型



默认情况下，类型检查器认为 `null` 与 `undefined` 可以赋值给任何类型。 `null` 与 `undefined` 是所有其它类型的一个有效值



```
// --strictNullChecks
let s = 'foo'
s = null // 错误, 'null'不能赋值给'string'
let sn: string | null = 'bar'
sn = null // 可以

sn = undefined // error, 'undefined'不能赋值给'string | null'
```



```
function f(x: number, y?: number) {
  return x + (y || 0)
}
f(1, 2)
f(1)
f(1, undefined)
f(1, null) // error, 'null' 不能赋值给 'number | undefined'

class C {
  a: number
  b?: number
}
let c = new C()
c.a = 12
c.a = undefined // error, 'undefined' 不能赋值给 'number'
c.b = 13
c.b = undefined // ok
c.b = null // error, 'null' 不能赋值给 'number | undefined'

function f(sn: string | null): string {
  return sn || 'default'
}
```



如果编译器不能够去除 null 或 undefined，你可以使用类型断言手动去除。语法是添加 ! 后缀： identifier! 从 identifier 的类型里去除了 null 和 undefined：



```
function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return name!.charAt(0) + '.  the ' + epithet // ok
  }
  name = name || 'Bob'
  return postfix('great')
}
```



## 字符串字面量类型



字符串字面量类型允许你指定字符串必须具有的确切值



```
// 组成一个联合类型
type Easing = 'ease-in' | 'ease-out' | 'ease-in-out'

class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === 'ease-in') {
      // ...
    } else if (easing === 'ease-out') {
      // ...
    } else if (easing === 'ease-in-out') {
      // ...
    } else {
      // error! 不能传入 null 或者 undefined.
    }
  }
}

let button = new UIElement()
button.animate(0, 0, 'ease-in')
button.animate(0, 0, 'uneasy') // error
// Argument of type '"uneasy"' is not assignable to parameter of type '"ease-in" | "ease-out" | "ease-in-out"'
```
