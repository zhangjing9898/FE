简单记录几个

### 字符串转数字

一元运算符 (+) 来实现

```js
//Longhand
let total = parseInt('453');
let average = parseFloat('42.6');
//Shorthand
let total = +'453';
let average = +'42.6';
```

### 指数幂

可以使用`Math.pow()`方法来得到一个数字的幂。有一种更短的语法来实现，即双星号 (**)

```js
//Longhand
const power = Math.pow(4, 3); // 64
// Shorthand
const power = 4**3; // 64
```

### Math.floor简写 ~~

```js
//Longhand
const floor = Math.floor(6.8); // 6
// Shorthand
const floor = ~~6.8; // 6
```

注意：双非位运算符只对 32 位整数有效，例如 (2**31)-1 = 2147483647。所以对于任何大于 2147483647 的数字，双非位运算符 (~~) 都会给出错误的结果，这种情况下推荐使用 Math.floor() 方法

### 一行 找出数组中的最大和最小数字

```js
// Shorthand
const arr = [2, 8, 15, 4];
Math.max(...arr); // 15
Math.min(...arr); // 2
```

### 获取字符串中的字符

```js
let str = 'jscurious.com';
//Longhand
str.charAt(2); // c
//Shorthand
str[2]; // c
```

### 解构赋值 交换变量

```js
let x = 'Hello', y = 55;
//Longhand
const temp = x;
x = y;
y = temp;
//Shorthand
[x, y] = [y, x];
```

### 解构赋值

```js
//Longhand
let a, b, c;
a = 5;
b = 8;
c = 12;

//Shorthand
let [a, b, c] = [5, 8, 12];
```

### includes()做多条件检查

```js
//Longhand
if (value === 1 || value === 'one' || value === 2 || value === 'two') {
     // Execute some code
}
// Shorthand 1
if ([1, 'one', 2, 'two'].indexOf(value) >= 0) {
    // Execute some code
}
// Shorthand 2
if ([1, 'one', 2, 'two'].includes(value)) {
    // Execute some code
}
```

