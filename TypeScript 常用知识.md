# TypeScript

TypeScript是基于ES6语法之上构建的一门新的语言，相比较于ES6最大的变化就是增加了强类型，结合ES6新增的对面向对象的扩展，整体编码感觉更接近于编译型语言,比如java。

不会把所有的语法都详尽的罗列出来，主要记录一下，我们在开发过程中经常会用到的一些语法，方便后期回顾和温习。
[TypeScript中文手册](https://ts.xcatliu.com/introduction/what-is-typescript.html) 语法详尽，可以平时用于查阅。

----------------------------

## 数据类型

ts提供的基本数据类型主要有以下几种：

- Boolean `true or false 布尔值`
- number `数值`
- string `字符串`
- string[] `数组`
- enum `枚举`
	+ enum Color {Red, Green, Blue}
	+ let c:Color = Color.Green
- any `任意值`
- void `空`

## 声明变量

**let** **const** 同es6

## interface接口

### 1.数据类型

接口在TypeScript中代表一种`数据类型`，通过接口用户可以自己扩展基础类型。在ES6中不存在接口。

```js
interface SqareConfig {
    color: string
    height: numbe
}

function createSqare(config: SqareConfig) {
    // TODO ...
}

let mySqare = createSqare({color: 'red', height: 200})
```
### 2.函数类型

接口中的变量除了可以是基础数据类型外，还可以是`函数类型`

```js
interface searchFn {
    (source: string, substring: string): boolean
}

let mySearch: searchFn

mySearch = function(source: string, substring: string) {
    // ...
    return false
}
```
**接口可以被实现，用法和java类似**

## class类

在ES6中就有相关 类的语法糖，ts对其进行了一些extend。

- 增加了public(default)，private，protected访问限制符
	*   `public` 修饰的属性或方法是公有的，可以在任何地方被访问到，默认所有的属性和方法都是 `public` 的
	*   `private` 修饰的属性或方法是私有的，不能在声明它的类的外部访问
	*   `protected` 修饰的属性或方法是受保护的，它和 `private` 类似，区别是它在子类中也是允许被访问的
- 在构造函数的参数中使用访问限制符，可以直接set和init variable

[TypeScript手册之类的用法](https://ts.xcatliu.com/advanced/class.html)

## 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性

泛型主要用户两种场景: 
1\. 泛型函数 
2\. 泛型类，泛型类使用(<>)括起来，跟在类名后面


[TypeScript手册之泛型的用法](https://ts.xcatliu.com/advanced/generics.html)
