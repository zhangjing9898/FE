在函数执行的时候，this总是指向调用该函数的对象。
要判断this的指向，其实就是判断this所在的函数属于谁。

在《JavaScript语言精粹》这本书中，把this出现的场景分为4类，简单说就是：

###1. 有对象  就指向调用对象
```
var myObject = {value:100};  
 myObject.getValue = function(){  
     console.log(this.value);//输出100  
     console.log(this);//输出 { value: 100, getValue: [Function] }  
 }  
 myObject.getValue();  
// getValue() 属于对象 myObject，并由 myOjbect 进行 . 调用，因此 this 指向对象 myObject。 
```

###2.没调用对象就指向全局对象
```
var myObject = { value: 100 };  
myObject.getValue = function() {  
    var foo = function() {  
        console.log(this.value) // 输出 undefined  
        console.log(this); // 输出全局对象 global    
         //foo 函数虽然定义在 getValue 的函数体内，但实际上它既不属于 getValue 也不属于 myObject。  
        // foo 并没有被绑定在任何对象上，所以当调用时，它的 this 指针指向了全局对象 global。  
    };  
    foo();  
    return this.value; //这个this 在 getValue中，从而指向 myObject。  
};  
console.log(myObject.getValue()); // 输出 100 
```

3) 用new构造就指向新对象         
```
//js 中，我们通过 new 关键词来调用构造函数，此时 this 会绑定在该新对象上。  
var SomeClass = function() {  
    this.value = 100;  
}  
var myCreate = new SomeClass();  
console.log(myCreate.value); // 输出10
```

   4) 通过 apply 或 call 或 bind 来改变 this 的所指
```
// apply 和 call 调用以及 bind 绑定： 指向绑定的对象  
// apply() 方法接受两个参数第一个是函数运行的作用域， 另外一个是一个参数数组(arguments)。  
// call() 方法第一个参数的意义与 apply() 方法相同， 只是其他的参数需要一个个列举出来。  
// 简单来说， call 的方式更接近我们平时调用函数， 而 apply 需要我们传递 Array 形式的数组给它。 它们是可以互相转换的。  
//apply和call的功能是一样的，只是传入的参数列表形式不同。
//var a1 = add.apply(sub,[4,2]);　　//sub调用add的方法
/*call的用法*/
//var a1 = add.call(sub,4,2);
var myObject = { value: 100 };  
var foo = function() {  
    console.log(this);  
};  
foo(); // 全局变量 global  
foo.apply(myObject); // { value: 100 }  
foo.call(myObject); // { value: 100 }  
var newFoo = foo.bind(myObject);  
newFoo(); // { value: 100 }</span>  
```
