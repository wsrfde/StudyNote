---
title: JavaScript设计模式
date: '2021-03-17'
categories:
 - Javascript
tags:
 - 设计模式
---

> 本笔记为JavaScript设计模式书籍 （张容铭）的总结思考

### 函数编写方式

#### 函数的另一种形式

我们都知道函数，正常情况下函数语法称为**函数声明**

```js
function myFun(){
  //...
}
```

这种情况下声明的函数是**全局变量**

这样声明函数当然没有问题，但在开发团队中我们不能只考虑自己，如果定义了很多方法而别人又用了相同的方法就会被覆盖，并且这种问题不易察觉

我们可以声明变量名来保存函数，一旦被覆盖，所有功能失效，这种现象很明显，也可以很轻易察觉到

这种声明方式叫**函数表达式**

```js
var myFun = function(){
  //...
}
```

#### 通过对象调用函数

1. 直接把函数放在对象内

   ```js
   var obj = {
     myfun:function(){
       //...
     }
   }
   
   //在js中函数也是对象，所以也可以这么做
   var obj = function(){};
   obj.myfun = function(){
     //...
   }
   //调用方法
   obj.myfun()
   ```

   缺点: 新创建的对象不能继承这个方法



2. return一个对象

   ```js
   var obj = function () {
     return{
       myfun:function(){
         console.log(this);
       }
     }
   }
   var foo = obj()
   foo.myfun()
   ```

   优点：返回一个新对象

   缺点：返回的对象foo和 obj没有任何关系

3. 使用类调用对象

   ```js
   var obj = function (age) {
     this.myfun = function () {
       console.log(this.name);
       console.log(this.age);
     }
     this.name = 'vicer';
     this.age = age;
   }
   var foo = new obj(18)
   foo.myfun()
   
   var bar = new obj(24)
   bar.myfun()
   ```

   优点：类进行实例化，每个new的对象都有一套属于自己的方法

   缺点：有时候这么做造成的消耗很奢侈，并且**无法共享公共属性**

4. 使用函数的protoype

   ```js
   var Obj = function(){}			//构造函数
   Obj.prototype.myfun = function(){	
     console.log(this.name)
   }
   Obj.prototype.name = 'vicer';
   
   var foo = new Obj()					//构造函数的对象实例
   foo.myfun()
   console.log(foo.name)
   ```

   ```js
   //prototype函数的简写
   var Obj = function(){}	
   Obj.prototype = {
     myfun:function(){
       console.log(this.name)
     },
     name:'vicer'
   }
   
   var foo = new Obj();
   foo.myfun();  
   ```
   

##### 对象搜索属性和方法的过程

- 搜索首先从`对象实例本身`开始

  * 如果在实例中找到了具有给定名字的属性，则返回该属性的值；

    之前定义了实例属性如`foo.name = 'tace'; ` 则返回`tace`

- 如果没有找到，则继续搜索指针指向的原型对象，在原型对象中查找具有给定名字的属性

  * 如果在原型对象中找到了这个属性，则返回该属性的值。

    `foo._proto_.name='vicer'`	则返回`vicer`

  


**总结：**也就是我们在调用`Obj.myfun()`的时候，会先后执行两次搜索。

所以每个实例中都包含一个constructor属性, 这是因为默认所有的原型对象中都包含了该属性

```js
foo.constructor === Obj;  //true
```

**注意：**当使用prototype函数的简写时，实例的constructor不再指向Obj构造函数

```js
foo.constructor === Obj;  //false
```

**原因：**

前面我们说过, 每创建一个函数, 就会同时创建它的prototype对象, 这个原型对象也会自动获取constructor属性；

当简写时相当于给prototype原型重新赋值了一个对象, 那么这个新对象的constructor属性, 会指向Object构造函数, 而不是我们定义的Obj构造函数了；所以简写构造函数时，实例的构造函数不再指向Obj构造函数。

**重新定义Obj的原型**

如果我们需要用到实例的constructor的值, 可以手动给constructor赋值

```js
//prototype函数的简写
var Obj = function(){}	
Obj.prototype = {
  constructor:Obj,
  myfun:function(){
    console.log(this.name)
  },
  name:'vicer'
}

var foo = new Obj();
foo.myfun();
console.log(foo.constructor === Obj;)	//true
```

**通过hasOwnProperty判断属性属于实例还是原型**

```js
console.log(foo.hasOwnProperty('name')); //属于实例属性返回true，否则返回false
```



这里再复习一下原型链

#### 原型链

构造函数 — `prototype` =>原型对象；

原型对象 — `constructor`  => 构造函数；

实例对象 — `_protp_` => 原型对象；

##### 原型对象的作用

存放构造函数创建的所有实例共享的属性和方法。

##### **原型链总结：**

构造函数的 prototype 指向原型对象，原型对象有一个 constructor 属性指回构造函数，每个构造函数生成的实例对象都有一个 *proto* 属性，这个属性指向原型对象。





#### 给每个函数添加方法

如果你想给每个函数都添加一个方法，可以这么做；

```js
Function.prototype.getName = function(){
  console.log('vicer');
}
var a = function(){}		//调用方法
a.getName()		
```

但是这样做在很多严格要求的团队是不被允许的，而且污染了原生对象Function，所以别人创建的函数也会被你创建的函数所污染。

正确的做法是你可以抽象出一个统一添加方法的功能方法。

```js
Function.prototype.addMethod = function (name,fn) {	//添加功能的方法
  this[name] = fn;  //等同于 this.name = fn; 动态改变对象key需要用方括号才能赋值
}

var methods = function () {}
//或者  var methods = new Function();
methods.addMethod('getName',function () {
  console.log('vicer');
})

methods.getName();
```

#### 链式添加方法

当我们写函数时，想要链式添加不想每次调用添加时，可以这样写

```js
Function.prototype.addMethod = function (name,fn) {	
  this[name] = fn;  
  return this;		//这里返回的this是被new的对象。所以当链式调用时，相当于每次使用new好的对象来调用下次函数
}

//链式添加方法
var methods = new Function();
methods.addMethod('getName',function () {	
  console.log('vicer');
  return this;			
}).addMethod('getAge',function () {		//这里就可以链式添加方法了
  console.log(18);
  return this;				//如果我们想链式调用怎么办？  没错，就是这里也返回this
})

methods.getName().getAge();
```

### 面向对象编程

> 定义：就是将你的需求抽象成一个对象，然后针对这个对象分析其特征（属性）与动作（方法）。这个对象我们称之为类

> 特点：面向对象编程思想有一个特点就是封装 , 就是将你所需要的功能放在一个函数里 

#### 封装一个类

在JS中创建类，首先要声明一个函数保存在一个变量中，按照编程习惯变量名首字母大写，然后在函数（类）中通过this添加属性和方法，来实现对类添加属性和方法。

```js
//通过this添加属性和方法
var Obj = function () {
  this.name = 'vicer';
  this.getName = function(){
    console.log(this.name);
  }
};
//也可以通过prototype添加
Obj.prototype =  {
  name:'vicer',
  getName:function () {
    console.log(this.name);
  }
}
```

#### 通过this或prototype添加属性和方法的区别

> 通过this和prototype都可以添加属性和方法，那么他们的区别是什么呢？

**this添加：**通过this添加的属性和方法，都是在当前的对象中添加的。所以我们通过类创建一个新对象时，this指向的属性和方法都会得到相应的创建。

 **prototype添加：**每个对象(函数也是对象)都有一个prototype指向其继承的属性和方法，这样通过prototype继承的方法并不是对象自身的，而是通过一级级查找得到的。所以我们通过类创建一个新对象时这些属性和方法不会再次创建。

**区别：**每次通过类新创建的新对象，this指向的属性和方法都会得到相应的创建。prototype继承的属性和方法则不会再次创建

##### 创建类实例

```js
function Person(name) {
  var age = 18;     //私有属性
  function sayHi() {  //私有方法
    console.log('Hi L');
  }
  this.setName = function (n) { //特权方法
    this.name = n;
  }
  this.getName = function () {  //特权方法
    console.log(this.name);
  }
  this.getAge = function () {   //特权方法调取私有属性
    console.log(age);
  }
  this.height = 180;    //公有属性
  this.copy = function(){	//公有方法
    console.log('aa')
  }

  this.setName(name);   //构造器
  sayHi();    //调用私有方法
}


var aa = new Person('vicer');
```

* 私有属性/方法：声明在函数内部外界访问不到的（例如一个人秘密只有自己知道）
* 公共属性/方法：在函数内通过this创建的属性和方法，当类创建对象时，定义的属性/方法可以复制到新创建的对象上，成为公有化的对象和方法

* 特权方法：在公有方法中，其中一些方法可以访问到类的私有属性和方法，权力比公有方法大，因而得名特权方法
* 构造器：在new关键字实例化对象时，会执行一遍类函数。我们在创建对象时调用的特权方法，会初始化对象的一些属性，这些创建对象时调用的特权方法，可以看作类的构造器。

##### 给类添加方法

通过点语法直接给类添加的属性和方法，对象是不能访问的。可以通过prototype方式添加

```js
Person.setAge = 20;
Person.prototype = {
  isCheck:false,
  addMethod:function(){
    //...
  }
}
var bb = new Person('vicer');

console.log(bb.setAge);		//undefined
console.log(bb.isCheck);		//false
bb.addMethod();
```

##### 静态私有变量

> 什么是静态私有变量：（经百度个人理解）改变一次静态私有变量，所有创建的实例都返回同一个值

一般我们通过闭包来实现类的静态变量

```js
    var Person = (function () {
      var Hobby = 'Internet'; //静态私有属性
      function sayHobby() { //静态私有方法
        console.log(Hobby);
      }

      function _person(newName,newAge,newHeight) {
        var name,age;   //私有变量
        function sayHello() { //私有方法
          console.log('Hello L');
        }
        this.setName = function (n) { //特权方法
          this.name = n;
        }
        this.getName = function () { //特权方法
          console.log(this.name);
        }
        this.height = newHeight; //公有属性
        this.changeHobby = function () { //公有方法
          Hobby = 'Guitar';
        }
        this.getHobby = function () {
          console.log(Hobby);
        }

        this.setName(newName); //构造器 
        sayHobby();   //调用静态私有方法
      }

      //构建原型
      _person.prototype.isEate = false;
      _person.prototype.isSleep =  function () {
        console.log('sleeping');
      }
      return _person; //返回类
    })()


    var aa = new Person('vicer',18,188);	//Internet
    aa.changeHobby();		//改变静态私有变量
    aa.getHobby();			//Guitar

    var bb = new Person('tace',20,160);		//Guitar
		//可以看到，两次创建实例，静态私有变量和上次的改变一致
```

