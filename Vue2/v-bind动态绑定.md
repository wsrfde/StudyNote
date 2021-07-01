---
title: v-bind动态绑定
date: '2021-05-26'
categories:
 - Vue
tags:
 - Vue2
---

> **v-bind的基本用法**  

```html
<img v-bind:src="vHref" alt="">
```

<br><br>

### 动态绑定class（对象语法）  

> **用法一**  

* class以对象的形式绑定，当类名为true的时候class存在，为false时不存在  
* 和普通的类同时存在，并不冲突，普通类可以作为必备class存在  


	v-bind:class="{类名1:布尔值,类名2:布尔值}"

```html
<div id='app'>
  <h2 class="default" v-bind:class="{active:isActive,line:isLine}">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'HelloVue',
      isActive:true,
      isLine:true
    }
  })
</script>
```
<br>

> **用法二**

* 如果过于复杂，可以通过methods方法来定义class
* 注意这里的调用方法要加括号()

```html
<div id='app'>
  <h2 class="default" v-bind:class="getClass()">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'HelloVue',
      isActive:true,
      isLine:true
    },
    methods:{
      getClass:function () {
        return {active:this.isActive,line:this.isLine}
      }
    }
  })
</script>
```
<br><br>

### 动态绑定class（数组语法）  

（这种用法比较少，一般用对象语法）
* 以数组的形式动态绑定class
* 和对象语法类似，也可以添加普通类  

> **用法一**

* 直接通过vue的data数据传递
```html
<!-- 数组语法中，支持使用对象语法 -->
<h2 class="default" :class="[active,line,{title:isShowTitle}]">{{message}}</h2>
```
<br>

> **用法二**

* 通过methods返回class，把上面的数组拿到methods中return回去
```html
<div id='app'>
  <h2 class="default" :class="getClass()">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'HelloVue',
      active:'classA',
      line:'classB'
    },
    methods:{
      getClass:function () {
        return [this.active,this.line];
      }
    }
  })
</script>
```
<br><br>

### 动态绑定style（对象语法）  

> **用法一**  

* style用法相当于内联样式
* 属性名可以加 - 或者用驼峰命名法命名属性
* 例如font-size  ||  fontSize


	<h2 :style="{属性名:属性值}">{{message}}</h2>

```html
<!-- 属性名写死的话要加引号 -->
<h2 :style="{fontSize:'30px'}">{{message}}</h2>
<!-- 属性不想用驼峰的话要加上短横线 -->
<h2 :style="{'font-size':'30px'}">{{message}}</h2>
<h2 :style="{fontSize:finalNum+'px',backgroundColor:dataColor}">{{message}}</h2>
```

<br>

> **用法二**  

* 用methods方法返回stlye

```html
<div>
  <h2 :style="getStyles()">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'HelloVue',
      final:'50px',
      finalNum:50,
      dataColor:'red',
    },
    methods:{
      getStyles:function () {
        return {fontSize:this.finalNum+'px',backgroundColor:this.dataColor}
      }
    }
  })
</script>
```
<br><br>

### 动态绑定style（组数语法）  

> **方法**  

（这种用法比较少，一般用对象语法）
* 用数组的方式动态绑定style

```html
<div id="app">
  <h2 :style="[baseStyle,baseStyle1]">{{message}}</h2>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'HelloVue',
      baseStyle:{fontSize:'50px'},
      baseStyle1:{backgroundColor:'red'},
    }
  })
</script>
```

<br> 

### v-bind中的驼峰标识

* 动态绑定属性时尽量字母小写，因为v-bind不支持大写字母。

* 如果v-bind使用驼峰需要转义，大写字母转小写，并前面加个  -  ，其他地方正常使用驼峰即可

* 但仍不建议中间加-，有可能出现不可预期的错误，最好小写字母

	```html
	//这里以props为例
	<cpn :new-message='message'></cpn>  
	
	<template>
	  <div>{{newMessage}}</div>
	</template>
	```

### 动态绑定style高级技巧

动态绑定style不能像绑定class一样做布尔判定，我们可以用三元运算符来动态显示style

```js
//isActive为布尔值，activeColor为属性
activeStyle(){
	return this.isActive ? {color:this.activeColor} : {}
}
```

### v-for与v-bind的结合

当我们在一个列表中，需要点击列表某一项，来让其变色，这就需要用到两者的结合使用了

```html
<div id="app">
  <ul>
    <li v-for="(item,index) in movies" v-bind:class="{red:index===countIndex}" 
			v-on:click="changeCs(index)">{{item}}</li>
  </ul>
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      movies:['加勒比海盗','海王','海贼王','海尔兄弟'],
      countIndex:0
    },
    methods:{
      changeCs(i){
        this.countIndex = i;
      }
    }
  })
</script>

<style>
  .red{
      color: red;
  }
</style>
```

### 动态绑定属性名称

```html
<div :[name]="value">我是内容</div>   
<!-- <div aaa="123">我是内容</div> -->

<script>
  data(){
    return {
      name:'aaa',
      value:123,
    }
  }
</script>
```

### 绑定对象的所有属性到元素上

```html
<div v-bin="obj">我是内容</div>   
<!-- <div name="vicer" age="18" height="188">我是内容</div> -->

<script>
  data(){
    return {
      obj:{
        name:'vicer',
        age:18,
        height:188
      }
    }
  }
</script>
```

