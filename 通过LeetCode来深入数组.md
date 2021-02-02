数组作为一个最基础的一维数据结构，也是我们平时用到很多的一个数据结构，但其实因为数组的方法较多， 如果包括ES7 规范，数组共包含 33 个标准的 API 方法和一个非标准的 API 方法，每个方法的参数和细节也比较零散，之前没做一个系统的学习，开发的时候频繁查询 MDN 文档，整体coding效率不高。

后面会涉及到LeetCode中的2道题来深入其用到数组api，在开始之前... 先提出4个问题

---

1. 数组的构造器有哪几种
2. 哪些是改变自身的方法
3. 哪些是不改变自身的方法
4. 遍历的方法有哪些

## 数组构造器

### 对象字面量

```js
let a = []
```

### 利用Array构造器

```js
{
  let a = Array(4) // 长度为4
  
  let b = []
  b.length = 4 
}
```

### Array.of  (ES6新增)

和Array构造器 基本一致，唯一的区别是在单个数字参数的处理上

```js
{
  Array.of(8.0); // [8]
  Array.of(8.0, 5); // [8, 5]
  Array.of('8'); // ["8"]
}
```



### Array.from (ES6新增)

个人感觉，这是一个很好用的东西

Array.from有3个参数：

- 类似数组的对象，必选
- 加工函数，新生成的数组会经过该函数的加工再返回
- this 作用域，表示加工函数执行时 this 的值

```js
{
  let obj = {0: 'a', 1: 'b', 2:'c', length: 3};
  Array.from(obj,(val,idx)=> val.repeat(3), obj);
  //  ["aaa", "bbb", "ccc"]
}
```

**Array.from 这个方法可以自己定义加工函数的处理方式，从而返回想要得到的值**

更优秀的一点，迭代器的对象还包括 String、Set、Map

```js
{
  Array.from('123') //string
  //  ["1", "2", "3"]
  
  Array.from(new Set(['abc', 'def'])); // set
  // ["abc", "def"]
  
  Array.from(new Map([[1, 'ab'], [2, 'de']])); // map
  // [[1, 'ab'], [2, 'de']]
}
```

## 

## 如何判断Array

ES5之前，有这些方法判断

```js
{
  const a = [];
  // 1.基于instanceof
  a instanceof Array;
  // 2.基于constructor
  a.constructor === Array;
  // 3.基于Object.prototype.isPrototypeOf
  Array.prototype.isPrototypeOf(a);
  // 4.基于getPrototypeOf
  Object.getPrototypeOf(a) === Array.prototype;
  // 5.基于Object.prototype.toString
  Object.prototype.toString.apply(a) === '[object Array]';
}
```

ES6 之后新增了一个 Array.isArray

## Leetcode 中的第 88 题 《合并两个有序数组》

```html
Q: 给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
输入:
nums1 = [1,2,3,0,0,0]； m = 3
nums2 = [2,5,6]；       n = 3
输出: [1,2,2,3,5,6]
```

因为不能新开数组，我们就需要用改变自身方法的api来完成这个问题...

利用splice、push和sort可以很简单解决这个问题

```js
{
  const merge = (nums1, m, nums2, n) => {
    nums1.splice(m);
    nums2.splice(n);
    nums1.push(...nums2);
    nums1.sort((a,b) => a - b);  // nums1从小到大排列，所以是a-b
	}
}
```





## 会改变自身的方法

ES6，会改变自身值的方法一共有 9 个，分别为 pop、push、reverse、shift、sort、splice、unshift，以及两个 ES6 新增的方法 copyWithin 和 fill

### 增 

```js
{
  // push
  let array = ["football", "basketball",  "badminton"];
  let i = array.push("golfball");
  console.log(array); 
  // ["football", "basketball", "badminton", "golfball"]
  console.log(i); // 4
  
  // unshift
  let array = ["red", "green", "blue"];
  let length = array.unshift("yellow");
  console.log(array); // ["yellow", "red", "green", "blue"]
  console.log(length); // 4
}
```

#### 反转

```js
{
  // reverse方法
  let array = [1,2,3,4,5];
  let array2 = array.reverse();
  console.log(array); // [5,4,3,2,1]
  console.log(array2===array); // true
}
```

### 删

```js
{
  // pop
  let array = ["cat", "dog", "cow", "chicken", "mouse"];
  let item = array.pop();
  console.log(array); // ["cat", "dog", "cow", "chicken"]
  console.log(item); // mouse
  
  // shift
  let array = [1,2,3,4,5];
  let item = array.shift();
  console.log(array); // [2,3,4,5]
  console.log(item); // 1
}
```

#### 排序

```js
{
  // sort
  let array = ["apple","Boy","Cat","dog",1,2,1];
  let array2 = array.sort();
  console.log(array); // [1, 1, 2, "Boy", "Cat", "apple", "dog"]
  console.log(array2 == array); // true
}
```

#### 切割

```js
{
  // splice
  var array = ["a","p","ple","boy"];
  var splices = array.splice(1,1);
  console.log(array); // ["a", "ple", "boy"]
  console.log(splices); // ["p"]
}
```

#### 覆盖 copyWithin

某些个位置的元素复制并覆盖到其他位置上去

```js
{
  // copyWithin 
  // Array.prototype.copyWithin(target, start = 0, end = this.length)
  let array = [1,2,3,4,5]; 
  let array2 = array.copyWithin(0,3);
  console.log(array===array2,array2);  // true [4, 5, 3, 4, 5]
}
```

#### 填充 fill

```js
{
  // fill
 	let array = [1,2,3,4,5];
  let array2 = array.fill(10,0,3);
  console.log(array===array2,array2); 
  // true [10, 10, 10, 4, 5], 可见数组区间[0,3]的元素全部替换为10
}
```

## Leetcode 中的第 88 题 《合并两个有序数组》

```html
Q: 给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
输入:
nums1 = [1,2,3,0,0,0]； m = 3
nums2 = [2,5,6]；       n = 3
输出: [1,2,2,3,5,6]
```

因为不能新开数组，我们就需要用改变自身方法的api来完成这个问题...

利用splice、push和sort可以很简单解决这个问题

```js
{
  const merge = (nums1, m, nums2, n) => {
    nums1.splice(m);
    nums2.splice(n);
    nums1.push(...nums2);
    nums1.sort((a,b) => a - b);  // nums1从小到大排列，所以是a-b
	}
}
```



## 不改变自身的方法

基于 ES7，不会改变自身的方法也有 9 个，分别为 concat、join、slice、toString、toLocaleString、indexOf、lastIndexOf、未形成标准的 toSource，以及 ES7 新增的方法 includes

#### 合并

```js
{
  // concat方法
  let array = [1, 2, 3];
  let array2 = array.concat(4,[5,6],[7,8,9]);
  console.log(array2); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
  console.log(array); // [1, 2, 3], 可见原数组并未被修改
}
```

#### join

```js
{
  const array = ['We', 'are', 'Chinese'];
  console.log(array.join()); // "We,are,Chinese"
  console.log(array.join('+')); // "We+are+Chinese"
}
```

#### slice 切割

```js
{
  const array = ["one", "two", "three","four", "five"];
  console.log(array.slice()); // ["one", "two", "three","four", "five"]
  console.log(array.slice(2,3)); // ["three"]
}
```

#### toString

```js
{
  const array = ['Jan', 1,'Feb', 'Mar', 'Apr'];
  const str = array.toString();
  console.log(str); //  Jan,1,Feb,Mar,Apr
}
```

#### toLocalString 更加适用日期

```js
{
  const array= [{name:'zz'}, 123, "abc", new Date()];
  const str = array.toLocaleString();
  console.log(str);
  // [object Object],123,abc,2021/1/31下午3:48:39
}
```

#### indexof 检索是否存在

存在：1  不存在：-1

```js
{
  const array = ['abc', 'def', 'ghi','123'];
	console.log(array.indexOf('def')); // 1
}
```

注意：lastIndexOf 和 indexOf 基本功能差不多，不过 lastIndexOf 是从后面寻找元素的下标

#### includes (ES7新增... 类似indexof)

```js
{
  const array = [-0, 1, 2];
  console.log(array.includes(+0)); // true
  console.log(array.includes(1)); // true
  const array = [NaN];
  console.log(array.includes(NaN)); // true
}
```

注意： includes 忽略了 +0 和 -0

 ## 数组遍历

基于 ES6，不会改变自身的遍历方法一共有 12 个，分别为 forEach、every、some、filter、map、reduce、reduceRight，以及 ES6 新增的方法 entries、find、findIndex、keys、values

#### forEach 和 map

```js
{
  // forEach
  let array = [1, 3, 5];
  let sReturn = array.forEach((value, index, array)=>{
    array[index] = value;
  });
  console.log(array); // [1, 3, 5]
  console.log(sReturn); // undefined, 可见返回值为undefined
  
  // map
  const array = [18, 9, 10, 35, 80];
  const a = array.map(item => item + 1);
  console.log(a);  // [19, 10, 11, 36, 81]
}
```

#### every 和 some

every:一假即假,必须所有都返回true才会返回true，哪怕有一个false，就会返回false；
some:一真即真, 只要其中一个为true 就会返回true

```js
{
  // every
  let o = {0:10, 1:8, 2:25, length:3};
  let bool = Array.prototype.every.call(o,(value, index, obj)=>value >= 8);
  console.log(bool); 
  
  // some
  let array = [18, 9, 10, 35, 80];
  let isExist = array.some((value, index, array) => value > 20);
  console.log(isExist); // true
}
```

#### filter

```js
{
  const array = [18, 9, 10, 35, 80];
  const array2 = array.filter((value, index, array) =>  value > 20);
  console.log(array2); // [35, 80]
}
```

#### reduce 

```js
{
  let array = [1, 2, 3, 4];
	array.reduce((p, v) => p * v); // 24 1*2*3*4
}
```

#### reduceRight

和reduce的区别就是从后往前累计

```js
{
  let array = [1, 2, 3, 4];
	array.reduceRight((p, v) => p * v); // 24 4*3*2*1
}
```

#### entries

```js
{
  let array = ["a", "b", "c"];
  let iterator = array.entries();
  console.log(iterator.next().value); // [0, "a"]
  console.log(iterator.next().value); // [1, "b"]
  console.log(iterator.next().value); // [2, "c"]
  console.log(iterator.next().value); // undefined, 迭代器处于数组末尾
}
```

#### find && findIndex

```js
{
  let array = [1, 3, 5, 7, 8, 9, 10];
  console.log(array.find((value)=>value%2==0)); // 8
  console.log(array.find((value)=>value>20)); // undefined
  console.log(array.findIndex((value)=>value%2==0)); // 4
  console.log(array.findIndex((value)=>value>20)); // -1
}
```

#### keys && values

```js
{
  // keys
  [...Array(20).keys()]; // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
  
  // values
  let array = ["abc", "xyz"];
  let iterator = array.values();
  console.log(iterator.next().value);//abc
  console.log(iterator.next().value);//xyz
}
```

## LeetCode

如何用reduce来解这个问题...

```json
Q: const arr = [ {name: 'brick1'}, {name: 'brick2'}, {name: 'brick3'},  {name: 'brick4'}]
希望最后返回到 arr 里面每个对象的 name 拼接数据为 'brick1, brick2, brick3 & brick4' 
```

### 解法

其实很简单

```js
{
  let arr =  [ {name: 'brick1'}, {name: 'brick2'}, {name: 'brick3'},  {name: 'brick4'}];
  arr.reduce((prev, current, index, array)=>{
    if (index === 0){
      return current.name;
    } else if (index === array.length - 1){
      return prev + ' & ' + current.name;
    } else {
      return prev + ', ' + current.name;
    }
  }, '');
  // brick1, brick2, brick3 & brick4
}
```

















