# 函数



和 JavaScript 一样，TypeScript 函数可以创建有名字的函数和匿名函数



```
// 命名函数
function add(x, y) {
  return x + y
}
// 匿名函数
let myAdd = function(x, y) {
  return x + y
}
// 外部变量
let z = 100
// 函数可以使用函数体外部的变量
function addToZ(x, y) {
  return x + y + z
}
```



## 函数类型



```
// 只要参数类型是匹配的，那么就认为它是有效的函数类型，而不在乎参数名是否正确
// 第二部分是返回值类型。 对于返回值，我们在函数和返回值类型之前使用(=>)符号
let myAdd: (baseValue: number, increment: number) => number = function(
  x: number,
  y: number
): number {
  return x + y
}

let myAdd = function(x: number, y: number): number {
  return x + y
}

let myAdd: (baseValue: number, increment: number) => number = function(x, y) {
  return x + y
}
```



## 可选参数和默认参数



JavaScript 里，每个参数都是可选的，可传可不传。 没传参的时候，它的值就是 `undefined`。 在TypeScript 里我们可以在参数名旁使用 `?` 实现可选参数的功能



```
function buildName(firstName: string, lastName: string) {
  return firstName + ' ' + lastName
}

let result1 = buildName('Bob') // Error, 参数过少
let result2 = buildName('Bob', 'Adams', 'Sr.') // Error, 参数过多
let result3 = buildName('Bob', 'Adams') // OK

// 可选参数必须跟在必须参数后面。
function buildName(firstName: string, lastName?: string): string {
  if (lastName) return firstName + ' ' + lastName
  else return firstName
}

let result1 = buildName('Bob') // 现在正常了
let result2 = buildName('Bob', 'Adams', 'Sr.') // Error, 参数过多
let result3 = buildName('Bob', 'Adams') // OK

// 设置默认值
function buildName(firstName: string, lastName = 'Smith'): string {
  return firstName + ' ' + lastName
}

let result1 = buildName('Bob') // 返回 "Bob Smith"
let result2 = buildName('Bob', undefined) // 正常, 同样 "Bob Smith"
let result4 = buildName('Bob', 'Adams') // OK

// 与普通可选参数不同的是，带默认值的参数不需要放在必须参数的后面

function buildName(firstName = 'Will', lastName: string): string {
  return firstName + ' ' + lastName
}

let result1 = buildName('Bob') // Error, 参数过少
let result2 = buildName('Bob', 'Adams', 'Sr.') // Error, 参数过多
let result3 = buildName('Bob', 'Adams') // OK， 返回 "Bob Adams"
let result4 = buildName(undefined, 'Adams') // OK，  返回 "Will Adams"
```



#### 剩余参数



```
// 剩余参数
function buildName(firstName: string, ...restOfName: string[]): string {
  return firstName + ' ' + restOfName.join(' ')
}

let employeeName = buildName('Joseph', 'Samuel', 'Lucas', 'MacKinzie')
```



也可以用于定义函数类型



```
// 这个省略号也会在带有剩余参数的函数类型定义上使用到
function buildName(firstName: string, ...restOfName: string[]): string {
  return firstName + ' ' + restOfName.join(' ')
}
// 定义一个类型
// : (fname: string, ...rest: string[]) => string
// 然后通过 = 将buildName赋值过去
let buildNameFun: (fname: string, ...rest: string[]) => string = buildName
```



## this



```
// 老版写法
// 定义一个扑克牌的对象
let deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function() {
    // 箭头函数保留函数创建时候的this值
    return () => {
      // 随意random
      let pickedCard = Math.floor(Math.random() * 52)
      // 花色
      let pickedSuit = Math.floor(pickedCard / 13)

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }
    }
  }
}

let cardPicker = deck.createCardPicker()
let pickedCard = cardPicker()

console.log('card: ' + pickedCard.card + ' of ' + pickedCard.suit)

// 重构
// 这种写法 知道 createCardPicker 期望在某个 Deck 对象上调用。也就是说 this 是 Deck 类型的，而非 any。
interface Card {
  suit: string
  card: number
}

interface Deck {
  suits: string[]
  cards: number[]

  createCardPicker(this: Deck): () => Card
}

let deck: Deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  // NOTE: 函数现在显式指定其被调用方必须是 deck 类型
  createCardPicker: function(this: Deck) {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52)
      let pickedSuit = Math.floor(pickedCard / 13)

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 }
    }
  }
}

let cardPicker = deck.createCardPicker()
let pickedCard = cardPicker()

console.log('card: ' + pickedCard.card + ' of ' + pickedCard.suit)
```



#### this在回调函数里



```
interface UIElement {
  // 这里的参数的 类型 也是函数
  // this: void 意味着 addClickListener 期望传入的 onclick 方法不需要 this
  addClickListener(onclick: (this: void, e: Event) => void): void
}

class Handler {
  type: string

  onClickGood = (e: Event) => {
    this.type = e.type
  }
}

let h = new Handler()

let uiElement: UIElement = {
  addClickListener() {}
}

uiElement.addClickListener(h.onClickBad) // error!
```



## 重载



函数根据传入不同的参数而返回不同类型的数据。



TS中可为同一个函数提供多个函数类型定义来进行函数重载。



```
let suits = ['hearts', 'spades', 'clubs', 'diamonds']

// 函数声明
// 这样改变后，重载的 pickCard 函数在调用的时候会进行正确的类型检查。
function pickCard(x: { suit: string; card: number }[]): number
function pickCard(x: number): { suit: string; card: number }

function pickCard(x): any {
  if (Array.isArray(x)) {
    let pickedCard = Math.floor(Math.random() * x.length)
    return pickedCard
  } else if (typeof x === 'number') {
    let pickedSuit = Math.floor(x / 13)
    return { suit: suits[pickedSuit], card: x % 13 }
  }
}

let myDeck = [
  { suit: 'diamonds', card: 2 },
  { suit: 'spades', card: 10 },
  { suit: 'hearts', card: 4 }
]
// 它查找重载列表，尝试使用第一个重载定义。 如果匹配的话就使用这个。因此，在定义重载的时候，一定要把最精确的定义放在最前面。
// 注意，function pickCard(x): any 并不是重载列表的一部分，因此这里只有两个重载：一个是接收对象数组，另一个接收数字
let pickedCard1 = myDeck[pickCard(myDeck)]
console.log('card: ' + pickedCard1.card + ' of ' + pickedCard1.suit)

let pickedCard2 = pickCard(15)
console.log('card: ' + pickedCard2.card + ' of ' + pickedCard2.suit)
```

