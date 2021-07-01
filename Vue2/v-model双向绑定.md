---
title: v-model双向绑定
date: '2021-05-26'
categories:
 - Vue
tags:
 - Vue2
---

### v-model的用法

* v-model是表单和数据的双向绑定  
* 通俗讲也就是value和data的双向绑定  

### v-model的原理
v-model其实是一个语法糖，它的背后本质上是包含两个操作：  

1. v-bind绑定一个value属性  
2. v-on指令给当前元素绑定input事件  


	<input type="text" v-model="message">
等同于下面的代码

```js
//input中有一个input属性，可以动态检测输入框是否更新
<input type="text" v-bind:value="message" 
v-on:input="message = $event.target.value">
  
  //js写法
  <input type="text" v-bind:value="message" 
   v-on:input="changeMse">
     
 data:{
   message:''
 },
   methods:{
     changeMse(event){
       this.message = event.target.value;
     }
   }
```

### v-model与input-radio的结合

  1. 两个input-radio都写v-model的时候可以不用写name互斥
  2. 使用v-model传入空字符串即可，当给data添加与value相同的数据时，input-radio也会自动选中

### v-model与input-checkbox的结合

  1. 当checkbox是单选框时，v-model传入布尔值，选中为true，未选中false
  2. 当checkbox是多选框时，input中设置value，v-model传入空数组，
	 选中则把当前input的value值传入数组中
### v-model与select>option的结合

> select一般用的少  

select中v-model传入空字符串，如果多选则v-model传入空数组。option中传入value，
  选中时则把当前选项传入数组中  

### v-model修饰符

- lazy修饰符  
  - 默认input中v-model是同步更新value的，lazy可以在失去焦点或回车后才更新  



- number修饰符  

  - 默认input输入的值无论数字字母都会被当作字符串进行处理，

  - number可以直接把内容转换成等number类型。  
  - 也有人说直接使用input中type=number，但在v-model转换的时候还是会把值转换成字符串，
  - 所以使用v-model时需要number类型还是用number修饰符把



+ trim修饰符  
  - 去除内容中左右两边空格

