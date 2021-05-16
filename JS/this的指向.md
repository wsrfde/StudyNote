### this的指向

全局作用域中指向window，但一般没有人在全局作用域中使用，一般都是在函数中



#### 普通函数中的this

```js
function fun(){
    console.log(this)
}
var obj = {
  name:'vicer',
  foo:fun
}
obj.foo()		// obj

fun()	//window

// 将obj的foo赋值给bar
var bar = obj1.foo;
bar();		// window

// 使用call
fun.call(obj)	//obj
```

可以看到，this的指向（除了call之外）

1. 和不同的位置调用有关
2. 和调用时的绑定有关，绑定的（对象/变量）指向谁this就指向谁

#### 内置函数

##### **setTimeout**

```js
setTimeout(function() {
  console.log(this); // window
}, 1000);
```

##### **数组的forEach**

```js
var names = ["abc", "cba", "nba"];
names.forEach(function(item) {
  console.log(this); // 三次window
});
```

改变forEach中的this指向

```js
var names = ["abc", "cba", "nba"];
var obj = {name: "why"};
names.forEach(function(item) {
  console.log(this); // 三次obj对象
}, obj);
```

#### **div的点击**

```html
<div class="box"></div>

<script>
	var box = document.querySelector(".box");
  box.onclick = function() {
    console.log(this); // box对象
   // 这是因为在发生点击时，执行传入的回调函数被调用时，会将box对象绑定到该函数中；
  }
</script>
```

#### new绑定

- 1.创建一个全新的对象；
- 2.这个新对象会被执行Prototype连接；
- 3.这个新对象会绑定到函数调用的this上（this的绑定在这个步骤完成）；
- 4.如果函数没有返回其他对象，表达式会返回这个新对象；

```js
function Person(name) {
  console.log(this); // Person {}
  this.name = name; // Person {name: "why"}
}

var p = new Person("why");
console.log(p);
```

简单来说，就是谁new我，我指向谁



### ES6箭头函数

箭头函数不使用this的四种标准规则（也就是不绑定this），而是根据外层作用域来决定this。

**案例1**：通过外部定义`_this`来获取`this`

```js
var obj = {
  data: [],
  getData: function() {
    var _this = this;
    setTimeout(function() {
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      _this.data.push(...res);
    }, 1000);
  }
}

obj.getData();
```

**案例2**：使用箭头函数获取

* 为什么在setTimeout的回调函数中可以直接使用this呢？

* 因为箭头函数并不绑定this对象，那么this引用就会从上层作用域中找到对应的this**

```js
var obj = {
  data: [],
  getData: function() {
    setTimeout(() => {
      // 模拟获取到的数据
      var res = ["abc", "cba", "nba"];
      this.data.push(...res);
    }, 1000);
  }
}
// setTimeout中的this向外找就是getData函数，函数又被obj调用，所以this指向obj
obj.getData();
```

**案例3**：如果getData也使用箭头函数呢？

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
// setTimeout的箭头函数this指向getData,而getData被obj调用，此时this为obj，由于getData也是箭头函数，
// 便继续向上查找，this即为window
obj.getData();
```

