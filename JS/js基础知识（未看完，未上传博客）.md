### 运算符优先级



如果一个表达式拥有超过一个运算符，执行的顺序则由 **优先级** 决定。换句话说，所有的运算符中都隐含着优先级顺序。

从小学开始，我们就知道在表达式 `1 + 2 * 2` 中，乘法先于加法计算。这就是一个优先级问题。乘法比加法拥有 **更高的优先级**。

圆括号拥有最高优先级，所以如果我们对现有的运算顺序不满意，我们可以使用圆括号来修改运算顺序，就像这样：`(1 + 2) * 2`。

在 JavaScript 中有众多运算符。每个运算符都有对应的优先级数字。数字越大，越先执行。如果优先级相同，则按照由左至右的顺序执行。

这是一个摘抄自 Mozilla 的 [优先级表](https://developer.mozilla.org/en/JavaScript/Reference/operators/operator_precedence)**（你没有必要把这全记住，但要记住一元运算符优先级高于二元运算符）**：

科普：一元运算符，只有一个值即可进行运算，（二元和三元也可这么理解）

一元运算符有 `++自增 --自减 +取正 -取负 ~位非 !取反`

二元运算符顺序   算术运算符`*/+-`  >  比较运算符`>= !==` >  逻辑运算符`|| &&`  

| 优先级 | 名称                                             | 符号 |
| :----- | :----------------------------------------------- | :--- |
| …      | …                                                | …    |
| 17     | 一元加号   // 一元运算符                         | `+`  |
| 17     | 一元负号  //  一元运算符                         | `-`  |
| 15     | 乘号  //二元运算符                               | `*`  |
| 15     | 除号                                             | `/`  |
| 13     | 加号                                             | `+`  |
| 13     | 减号                                             | `-`  |
| …      | …                                                | …    |
| 3      | 赋值符  //赋值运算符为等级最低，在三元运算符下面 | `=`  |
| …      | …                                                | …    |

### 逻辑运算符

JavaScript 里有三个逻辑运算符：`||`（或），`&&`（与），`!`（非）。

#### || （或）

进行逻辑或判断时，两或多值判断`取其真`，没有真则返回最后一个值

```javascript
result = null || 1;		//1
result1 = null || undefined; //undefined
```

#### &&（与）

进行逻辑与判断时，两或多值判断`取其假`，没有假则返回最后一个值

```js
result = null && 1;	//null
result1 = null || undefined; //null
```

#### !（非）

进行值的相反运算

#### ?? (空)

> 空位合并运算符，这是js新出的功能，旧的浏览器可能需要polyfill

定义：如果判断的值不是null或undefined，则从列表中取第一个被定义的变量。如果都没有被定义，则取其最后一个值

**示例一**

```js
// ?? 等效于三元孙算符：
let a = undefined;
let b = null;

let x = (a !==undefined && b !==null) ? a : b;
console.log(x);		//null

let y = a ?? b;
console.log(y);		//null
```

**示例二**

```js
let a = null;
let b = null;
let c = 'cc';
console.log(a ?? b ?? c ?? 'dd');			//cc
```

可以看出，当取值中如果被定义，则取值第一个被定义的值。

##### ?? 与 ll 的区别

* `ll`返回第一个真值

* `??`返回第一个被定义的值

```js
let a = 0;

console.log(a ll 100);	//100
console.log(a ?? 100);	//0
```



##### ?? 的优先级

在运算符中只比`?`和`=`高，如果想要优先运算，可以包括在括号内

```js
let a = null;
console.log((a ?? 100) * (a ?? 50));
```



注意：`??`和`||`和`&&`不能同时使用，如果想要使用可以包裹在括号内

```js
let a = null;
console.log(a ?? 1 || 2);  //错误使用，返回error
console.log((a ?? 1) || 2); //正确，返回 1 
```











### null和undefined 的比较



#### null只在不严格相等下，等于undefined，并且不会进行任何的类型准换

```javascript
alert( null === undefined ); // false
alert( null == undefined ); // true
```

#### null vs 0

```js
alert( null > 0 );  // (1) false
alert( null == 0 ); // (2) false
alert( null >= 0 ); // (3) true
```

1. 这里null在进行值的比较时，会被转化为数字0，所以（1）为false；

2. null和undefined的相等性检查（==） ，和普通比较符号 （> || >= || < || <=）,的逻辑时互相独立的。null和undefined相等性检查中不会进行任何类型转换，除了他们相等外，null不会等于任何值，所以（2）为false；

3. 根据第一条，null为0，所以（3）为true

#### undefined vs 0

```js
alert( undefined > 0 ); // false (1)
alert( undefined < 0 ); // false (2)
alert( undefined == 0 ); // false (3)
```

1. undefined被转化为NAN，所以（1）（2）为false
2. null只与undefined相等，不会和其他值相等，所以（3）为false

#### 字符串比较

在字符串比较中，比较首字符大小，如果是英文则按照英文字母顺序来比较大小

如果首字符相等，则比较第二个字符，以此类推

```js
alert( 'Z' > 'A' ); // true		英文顺序 Z 在 A 后面
alert( '2' > '12' ); // true	首字符   2 > 1
```

#### 类型转换

**只有在类型不同时，才会转化为相同类型进行比较，优先转化为number类型比较**

```js
alert( '2' > 1 ); // true，字符串 '2' 会被转化为数字 2
alert( true == 1 ); // true
```



### 形参和实参和arguments

**形参：**函数定义的参数

**实参：**函数调用时实际传递的参数

1. 参数匹配时从左往右，如果实参少于形参，则后面的形参对应赋值undefined
2. 实参的个数如果多于形参，可以通过arguments进行访问。
3. 实参以数组的方式保存到arguments中
4. 如果参数多于4个，开发人员总不能记住每个参数，最好将参数封装成对象
5. arguments对象只有函数开始时才可用

```js
    (function (i) {
      console.log(i,arguments);
      // i = 123;
      // arguments = [123,234,345];
     })(123,234,345)
```



### this指向

#### 指向规则

> 在JS中，this是谁调用我，我指向谁。

也就是看括号左边的调用，直接在全局中调用`foo()`指向window，通过对象调用`obj.foo()`则指向对象obj

#### 改变指向

> 如果想改变this的指向可以通过apply，call，bind改变this的指向（这里就不示例）

#### 内置函数指向

那么在内置函数中，this的指向又是指向谁呢？

1. **setTimeout**

   setTimeout中会传入一个函数，这个函数中的this通常是window

   ```js
   setTimeout(function() {
     console.log(this); // window
   }, 1000);
   ```

   为什么这里是window呢？

- 这个和setTimeout源码的内部调用有关；
- setTimeout内部是通过apply进行绑定的this对象，并且绑定的是全局对象；



2. **数组的forEach**

   数组有一个高阶函数forEach，用于函数的遍历：

- 在forEach中传入的函数打印的也是Window对象；

- 这是因为默认情况下传入的函数是自动调用函数（默认绑定）；

  ```js
  var names = ["abc", "cba", "nba"];
  names.forEach(function(item) {
    console.log(this); // 三次window
  });
  ```

  我们是否可以改变该函数的this指向呢？

  ```js
  var names = ["abc", "cba", "nba"];
  var obj = {name: "why"};
  names.forEach(function(item) {
    console.log(this); // 三次obj对象
  }, obj);
  ```



3. **div的点击**

   在发生点击时，执行传入的回调函数被调用时，会将box对象绑定到该函数中；

   ```js
   <div class="box">点击</div>
   
   var boxEle = document.querySelector(".box");
   boxEle.onclick = function() {
     console.log(this); // box对象
   }
   ```

#### new构造函数指向

> 谁new我，我指向谁

JavaScript中的函数可以当做一个类的构造函数来使用，也就是使用new关键字。

使用new关键字来调用函数时，会执行如下的操作：

- 1.创建一个全新的对象；
- 2.这个新对象会被执行Prototype连接；
- 3.这个新对象会绑定到函数调用的this上（this的绑定在这个步骤完成）；
- 4.如果函数没有返回其他对象，表达式会返回这个新对象；

```js
// 创建Person
function Person(name) {
  console.log(this); // Person {}
  this.name = name; // Person {name: "why"}
}

var p = new Person("why");
console.log(p);
```

#### 隐式绑定与显式绑定

**隐式绑定**：调用方式是通过某个对象进行调用的

也就是它的调用位置中，是通过某个对象发起的函数调用。如`obj.foo()`

**显式绑定**：必须在调用的`对象内部`有一个对函数的引用。如果我们不希望在 **对象内部** 包含这个函数的引用，同时又希望在这个对象上进行强制调用，可以使用apply，call，bind

我们明确的绑定了this指向的对象，所以称之为 **显示绑定**。

#### this绑定优先级

##### 比较隐式和显示绑定

```js
function foo() {
  console.log(this);
}

var obj1 = {
  name: "obj1",
  foo: foo
}

var obj2 = {
  name: "obj2",
  foo: foo
}

// 隐式绑定
obj1.foo(); // obj1
obj2.foo(); // obj2

// 隐式绑定和显示绑定同时存在
obj1.foo.call(obj2); // obj2, 说明显式绑定优先级更高
```

##### 比较new和隐式绑定

```js
function foo() {
  console.log(this);
}

var obj = {
  name: "why",
  foo: foo
}

new obj.foo(); // foo对象, 说明new绑定优先级更高
```

##### 比较new和bind绑定

```js
function foo() {
  console.log(this);
}

var obj = {
  name: "obj"
}

// var foo = new foo.call(obj);
var bar = foo.bind(obj);
var foo = new bar(); // 打印foo, 说明使用的是new绑定
```



注意：new绑定和call、apply是不允许同时使用的（可以和bind同时使用），所以不存在谁的优先级更高。

**总结：new绑定 > 显示绑定> 隐式绑定 > 默认绑定**



####  this规则之外

##### 清除call绑定

当显示绑定时，可以给call传入null或undefined来清除绑定

```js
function foo() {
  console.log(this);
}

var obj = {
  name: "why"
}

foo.call(obj); // obj对象
foo.call(null); // window
foo.call(undefined); // window

var bar = foo.bind(null);
bar(); // window
```

##### 间接函数引用

- 赋值(obj2.foo = obj1.foo)的结果是foo函数；
- foo函数被直接调用，那么是默认绑定；

```js
function foo() {
  console.log(this);
}

var obj1 = {
  name: "obj1",
  foo: foo
}; 

var obj2 = {
  name: "obj2"
}

obj1.foo(); // obj1对象
(obj2.foo = obj1.foo)();  // window
```

##### ES6箭头函数

箭头函数不使用this的四种标准规则（也就是不绑定this），而是根据外层作用域来决定this。

我们来看一个模拟网络请求的案例：

- 这里我使用setTimeout来模拟网络请求，请求到数据后如何可以存放到data中呢？
- 我们需要拿到obj对象，设置data；
- 但是直接拿到的this是window，我们需要在外层定义：`var _this = this`
- 在setTimeout的回调函数中使用_this就代表了obj对象

```js
var obj = {
  data: [],
  getData: function() {
    var _this = this;
    setTimeout(function() {	//根据内置函数规则，this保存的是window，所以需要在外部保存this在里面引用
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      _this.data.push(...res);
    }, 1000);
  }
}

obj.getData();
```

上面的代码在ES6之前是我们最常用的方式，从ES6开始，我们会使用箭头函数：

- 为什么在setTimeout的回调函数中可以直接使用this呢？
- 因为箭头函数并不绑定this对象，那么this引用就会从上层作用域中找到对应的this

```js
var obj = {
  data: [],
  getData: function() {
    setTimeout(() => {	//箭头函数向外层查找，所以this指向obj
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      this.data.push(...res);
    }, 1000);
  }
}

obj.getData();
```

思考：如果getData也是一个箭头函数，那么setTimeout中的回调函数中的this指向谁呢？

- 答案是window；
- 依然是不断的从上层作用域找，那么找到了全局作用域；
- 在全局作用域内，this代表的就是window

```js
var obj = {
  data: [],
  getData: () => {
    setTimeout(() => {
      console.log(this); // window
    }, 1000);
  }
}

obj.getData();
```

### switch

我们都知道switch相当于多个if else。

#### switch与if比较

在switch中case中判断值是否相等，那么判断两个值是**等于**还是**严等于**呢？

答案：`case a:`  的值判断相当于 `if(a === b)`，也就是严等于

#### switch的case分组

当我们想让`case3`和`case5`执行同样的代码时，可以这样做

```js
let a = 3;

switch (a) {
  case 4:
    alert('Right!');
    break;
  case 3: // (*) 下面这两个 case 被分在一组
  case 5:
		alert('3和5同时执行代码')
    break;

  default:
    alert('The result is strange. Really.');
}
```

### 函数

> 这里学习一些平时没有注重的函数细节

<a name='a'></a>

#### 函数两种声明方式

下面的函数语法称为**函数声明**

```js
function sayHi() {
  alert( "Hello" );
}
```

另一种创建函数的语法称为**函数表达式**

```js
let sayHi = function() {
  alert( "Hello" );
};
```

注意：函数表达式是将函数作为一个值赋值给key，所以它是赋值语句，不是代码块，需要在结尾加上`分号;`

#### 回调函数

回调函数一般指在函数中回调 ，把函数作为值传递进来的函数，或者回调自身。这样说可能有点绕，让我们看代码

```js
function ask( yes, no) {
  if (confirm('请点击确认或取消')) yes()
  else no();
}

function showOk() {
  alert("你点击了确认.");
}

function showCancel() {
  alert("你点击了取消.");
}

// 用法：函数 showOk 和 showCancel 被作为参数传入到 ask
ask(showOk, showCancel);
```

`ask` 的两个参数值 `showOk` 和 `showCancel` 可以被称为 **回调函数** 或简称 **回调**。

#### 匿名函数

我们还可以对函数表达式进行简写

```js
function ask(yes, no) {
  if (confirm('请点击确认或取消')) yes()
  else no();
}

ask(
  function() { alert("你点击了确认."); },
  function() { alert("你点击了取消."); }
);
```

这里直接通过参数名来调用两个函数，这两个函数没有名字，所以叫**匿名函数**。

这样的函数在 `ask` 外无法访问（因为没有对它们分配变量），不过这正是我们想要的。

#### 函数表达式vs函数声明

> 在<a href='#a'>函数两种声明方式</a>中，我们已经学过他们的声明方式。那么他们除了声明的区别是否还有其他区别呢?

##### 创建函数

**函数表达式是在代码执行到达时被创建，并且仅从那一刻起可用。**

一旦代码执行到赋值表达式 `let sayHi= function…` 的右侧，此时就会开始创建该函数，并且可以从现在开始使用（分配，调用等）。

```javascript
sayHi("John"); // error!

let sayHi = function(name) {  // (*) no magic any more
  alert( `Hello, ${name}` );
};
```

函数声明则不同。

**在函数声明被定义之前，它就可以被调用。**

例如，一个全局函数声明对整个脚本来说都是可见的，无论它被写在这个脚本的哪个位置。

```js
sayHi("John"); // Hello, John

function sayHi(name) {
  alert( `Hello, ${name}` );
}
```

##### 块级作用域

在严格模式下，当函数声明在代码块内时，该代码块内的任何位置都是可见的。但在代码块外不可见

```js
'use strict';

let a = 1;
if (a > 0) {
  function myFun() {
    console.log('我被调用了');
  }
}
myFun();	//myFun is not defined
```

我们可以使用函数表达式的方式，在代码块外提前声明，保证函数的可见性，然后进行调用

```js
'use strict';

let a = 1;
let myFun = null;
if (a > 0) {
   myFun = function () {
     console.log('我被调用了');
   }
}
myFun();
```

##### 总结

在日常开发中我们使用函数声明即可，如果因为某种原因函数声明不适合我们，例如上面例子，那么应该使用函数表达式

### 对象

#### 对象的两种声明

1. 构造函数的语法

   `let user = new Object();`

2. 字面量的语法

   `let user = {};`

#### 对象属性名命名

当命名key时中间有空格，我们可以用引号或双引号包裹来进行命名

```js
    var obj = {
      name:'vicer',
      age:18,
      "first name":"liu"
    }
```

#### 对象属性调用

一般调用对象时，我们只需要运行`obj.name`即可获得其value。
当属性名中间有空格时，我们需要加方括号`obj.['first name']`来进行获取value。

#### 常量的更改

我们都知道，在const命名后不允许进行更改，但const命名的对象可以进行更改吗？

```js
const obj = {
    name:'vicer'
}
obj.name = 'test';

console.log(obj.name);   //test
```

看起来会报错，但const只固定其变量的值，不固定其变量的内容。如果我们直接更改obj=***;则会直接报错。

#### 自定义属性名（计算属性）

在实际开发中，我们的变量名有可能是个变量，当我们想要自定义变量名时，可以这么做

```js
let key = prompt('我的key是','name1');
let obj = {
    [key]:'vicer'
}
//当key='name1' 时，则可以直接通过key的value来获取属性
console.log(obj.name1);
```

也可以是变量+常量的方式命名key

```js
let num = 8;
let obj = {
    ['iphone'+num]:'苹果8'
}

console.log(obj.iphone8);       //苹果8
```

方括号比点符号更强大。它允许任何属性名和变量，但写起来也更加麻烦。

所以大部分时间里，当属性名是已知且简单的时候，就是用点符号`obj.name`。如果我们需要一些更复杂的内容，那么就用方括号`obj[name]`。

#### "in",判断属性是否在对象中

我们可以使用`in`来测试某个属性有没有在对象中

```js
let obj = {
    age:18
}
console.log('age' in obj);      //true    注意这里变量名`age`要加引号，转成字符串。
```

#### "for in",遍历对象

我们可以使用以下方式遍历对象

```js
let obj = {
    name:'vicer',
    age:18,
    height:188
}
for(let key in obj){
    console.log(key);       //name age  height
}
```

我们可以看到，对象按照创建的顺序遍历了出来。

一、升序排序
如果我们想要按照排序方式进行遍历，可以以数字命名key，则自动按照升序来排列对象

```js
let obj = {
    '200':'vicer',
    '18':18,
    '188':188
}
for(let key in obj){
    console.log(key);       //18 188 200
}
```

二、创建排序
如果我们的key是数字，但是想要按照创建顺序排序，可以给变量名变成非整数即可

```js
let obj = {
    '+200':'vicer',
    '+18':18,
    '+188':188
}
for(let key in obj){
    console.log(key);       //200 18 188
}
```

#### 删除对象属性

代码示例：`  delete obj.name;`

