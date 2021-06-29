#### 说说 JavaScript 中的基本类型有哪些？以及各个数据类型是如何存储的？细分又是什么？

> 原始类型和引用类型
>
> 原始类型储存在栈，引用类型储存再堆
>
> string,number,boolean,null,undefined,symbol
>
> object,array,date,regExp,function

#### 为什么 typeof null 等于 Object?

在 JavaScript 中二进制前三位都为 0 的话会被判
断为 object 类型， null 的二进制表示是全 0

#### typeof 与 instanceof 有什么区别？

typeof一般用来判断基本类型，instanceof 用来判断是不是一个对象的实例

#### 类型转换

> **数据 => String类型**

-   String(123);       // "123"
-   String(true);      // "true"
-   String(null);    // "null"
-   String(undefined);   // "undefined"
-   String([1,2,3])     // "1,2,3"
-   String({});      // "[object Object]"




> **其他数据类型转布尔类型**

​		除了特殊的几个值 `‘’`、 `undefined`、 `NAN`、 `null`、 `false`、 `0` 转化为 `Boolean` 为 `false` 之外，其他类型值都转化为 `true`。



> **转化为数字类型**

  - Number(10);          // 10 
  - Number('10');        // 10 
  - Number(null);        // 0 
  - Number('');        // 0  
  - Number(true);        // 1 
  - Number(false);        // 0 
  - Number([]);       // 0 
  - Number([1,2]);       // NaN
  - Number('10a');       // NaN
  - Number(undefined);    // NaN



> 对象类型转原始类型

- 转化原始类型分为两种情况：转化为**字符串类型**或**其他原始类型**。

- 如果已经是原始类型，不需要再进行转化。 

- 如果转字符串类型，就调用内置函数中的 toString()方法。 

- 如果是其他基本类型，则调用内置函数中的 valueOf()方法。 

- 如果返回的不是原始类型，则会继续调用 toString() 方法。 
- 如果还没有返回原始类型，则报错。



**四则运算**

##### 加法运算

##### 其他运算



#### 逻辑运算符

A && B

A || B

#### 比较运算符

===

==

#### this

#### 什么是 this 指针?以及各种情况下的 this 指向问题。

如何改变 this 的指向？

如果我们使用上边的方法改变箭头函数的 this 指针，会发生什么情况呢？能否进行改变呢？

#### New

new 内部发生了什么过程？可不可以手写实现一个 new 操作符？

有几种创建对象的方式，字面量相对于 new 创建对象有哪些优势？

new/字面量 与 Object.create(null) 创建对象的区别？

#### 闭包

什么是作用域？什么是作用域链？

什么是闭包？闭包的作用？闭包的应用？

 

#### 原型和原型链

什么是原型？什么是原型链？如何理解？

instanceOf 的原理是什么？

 

#### 继承

说一说 JS 中的继承方式有哪些？以及各个继承方式的优缺点。

#### 垃圾回收机制

什么是内存泄漏？为什么会导致内存泄漏？

怎么解决内存泄漏？说一说 JS 垃圾回收机制的运行机制的原理？

 

#### 深拷贝和浅拷贝

什么是深拷贝？什么是浅拷贝？

浅拷贝和深拷贝分别如何实现的？有哪几种实现方式？

 

#### 异步编程

JS 为什么是单线程？又带来了哪些问题呢？

JS 如何实现异步编程？

如何解决回调地狱问题呢？

说说异步代码的执行顺序？Event Loop 的运行机制是如何的运行的？

![js运行机制](..\面试总结\img\js运行机制.png)