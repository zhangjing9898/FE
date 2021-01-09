# 类型推断

 TypeScript中的类型是在哪里 如何被推断的

TypeScript 里，在有些没有明确指出类型的地方，类型推断会帮助提供类型

```ts
let x = 3  // 这里x的类型被推断为 数字
```

#### 最佳通用类型

有些时候我们需要从几个表达式中推断类型，会使用这些表达式的类型来推断出一个最合适的通用类型

```ts
let x = [0, 1, null]  // 这时候的x是一个联合类型 number | null
```

我们可以自己指定推断类型

```ts
class Animal {
  numLegs: number
}
class Bee extends Animal {
}
class Lion extends Animal {
}
let zoo = [new Bee(), new Lion()]  // 这里的类型只能推断为 Bee | Lion 联合类型 而不是Animal
// 我们可以手动指定
let zoo: Animal[] = [new Bee(), new Lion()]
```

#### 上下文类型

有些时候，TypeScript 类型推断会按另外一种方式，我们称作“上下文类型”；上下文类型的出现和表达式的类型以及所处的位置相关

```ts
window.onmousedown = function(mouseEvent) {
  console.log(mouseEvent.clickTime)  // Error
  // window.onmousedown 函数的类型来推断右边函数表达式的类型
  // 推断出 mouseEvent 参数的类型是一个event 所以.clickTime报错
}
```

上下文类型也会做为最佳通用类型的候选类型

```ts
function createZoo(): Animal[] {
  return [new Bee(), new Lion()]
}

let zoo = createZoo()
// 最佳通用类型有 3 个候选者：Animal，Bee 和 Lion。 其中，Animal 会被做为最佳通用类型
```

