---
title: Vue中父与孙组件的传递
date: '2021-05-26'
categories:
 - Vue
tags:
 - Vue2
---

#### 前言[#](https://www.cnblogs.com/lovecode3000/p/12709231.html#816807762)

多级组件嵌套需要传递数据时，通常使用的方法是通过vuex。如果仅仅是传递数据，而不做中间处理，使用 vuex 处理，未免有点杀鸡用牛刀。Vue 2.4 版本提供了另一种方法:

使用 v-bind=”$attrs”, 将父组件中不被认为 props特性绑定的属性传入子组件中，配合 interitAttrs 选项一起使用。

之所以要提到这两个属性，是因为两者的出现使得组件之间跨组件的通信在不依赖 vuex 和事件总线的情况下变得简洁，业务清晰。

首先分析以下应用场景：

![img](https://img2020.cnblogs.com/blog/1757428/202004/1757428-20200415224757059-33351268.png)

#### A 组件与 B 组件之间的通信： （父子组件）[#](https://www.cnblogs.com/lovecode3000/p/12709231.html#1030179632)

如上图所示，A、B、C三个组件依次嵌套，按照 Vue 的开发习惯，父子组件通信可以通过以下方式实现：

A to B 通过props的方式向子组件传递，
B to A 通过在 B 组件中 $emit

#### A 组件与 C 组件之间的通信： （跨多级的组件嵌套关系）[#](https://www.cnblogs.com/lovecode3000/p/12709231.html#3281878410)

如上图，A 组件与 C 组件之间属于跨多级的组件嵌套关系，以往两者之间如需实现通信，往往通过以下方式实现：

1. 借助 B 组件的中转，从上到下props依次传递，从下至上，$emit事件的传递，达到跨级组件通信的效果
2. 借助Vuex的全局状态共享
3. Vue Bus，使用Vue的实例，实现事件的监听和发布，实现组件之间的传递。

**第一种**通过props和$emit的方式，使得组件之间的业务逻辑臃肿不堪，B组件在其中仅仅充当的是一个中转站的作用。
**第二种** Vuex的方式，某些情况下似乎又有点大材小用的意味，（仅仅是想实现组件之间的一个数据传递，并非数据共享的概念）。
**第三种**情况的使用在实际的项目操作中发现，如不能实现很好的事件监听与发布的管理，往往容易导致数据流的混乱，在多人协作的项目中，不利于项目的维护。

`$attrs`以及`$listeners`的出现解决的就是第一种情况的问题，B组件在其中传递props以及事件的过程中，不必在写多余的代码，仅仅是将`$attrs`以及`$listeners`向上或者向下传递即可。

示例代码
如下所示：

**父组件（father.vue）**

```html
<template>
   <div id="app">
     <son
       :name="name"
       :age="age"
       @getName="getName" 
       @getAge="getAge"> 
     </son>
   </div>
</template>
<script>
   import son from './son.vue';
   export default {
     data () {
       return {
          name:'vicer',
          age:18
       };
     },
     components: { son },
     methods: {
       getName () {
         console.log(this.name);
       },
       getAge () {
         console.log(this.age);
       }
     }
   };
</script>
```

控制台查看结果:
'vicer'

18

**子组件（son.vue）**

```html
<template>
   <div>
     <p>我是子组件:</p>
     <p>props: {{name}}</p>    <!-- 从父组件得到props -->
     <p>$attrs: {{$attrs}}</p>        <!-- 被跨级传递的属性 -->
     
     <hr>
   <!-- 跨级传递事件:在中间组件(B组件)绑定了$listeners属性，孙组件中能直接触发父组件中的getName事件-->
   <!-- 跨级传递数据:通过在中间组件绑定$attrs属性，C组件可以直接获取到A组件中传递下来的props-->
 		<grandson v-bind="$attrs" v-on="$listeners"></grandson>
 </div>
</template>
<script>
   import grandson from './grandson.vue';
   export default {
    props: ['name'],
    data () {
      return {};
    },
    inheritAttrs: false,
    components: { grandson },
    mounted () {
      this.$emit('getName');		//vicer
    }
 	};
</script>
```

结果：
我是子组件:
props: vicer
$attrs: { "age": 18}

**孙组件 (grandson.vue)**

```html
<template>
   <div>
     <p>我是孙组件:</p>
     <p>props: {{age}}</p>
     <p>$attrs: {{$attrs}}</p>
		 <button @click="$listeners.getAge">按钮1</button>	<!-- 跨级发送事件方式一 -->
     <button @click="sendEvent">按钮2</button>	<!-- 跨级发送事件方式二 -->
     <hr>
   </div>
</template>
<script>
   export default {
  	 props: ['age'],
     mounted() {
      console.log(this.$listeners)		//{getName: ƒ, getAge: ƒ}
     },
     data () {
        return {};
     },
     methods () {
       sendEvent(){
         this.$emit('getAge');     
       }
     }
   };
</script>
```

结果：
我是孙组件:
props: 18
$attrs: {}



#### 知识点总结[#](https://www.cnblogs.com/lovecode3000/p/12709231.html#1600173760)

**$attrs**

在子组件引入孙组件，并在子组件声明属性`v-bind="$attrs"`，则可以跨级传递参数

**注意：如果子组件声明了props，则孙组件中$attrs只包含不被声明的props。如果子组件也想使用数据，只能通过$attrs获取**



**$listeners**

在子组件引入孙组件，并在子组件中声明`v-on="$listeners"`属性，则孙组件可跨级emit事件,让父组件监听到

```js
// 跨级发送事件方式一 
<button @click="$listeners.testC">按钮</button>	

//跨级发送事件方式二
mounted () {
  this.$emit('testC');     
}
```



**inheritAttrs**

> 首先我们在使用inheritAttrs属性之前要了解一个属性：非Prop的Attribute

**非Prop的Attribute：当我们给某个组件传递属性，该属性并没有定义对应的props或emits时，就被称为非Prop的Attribute**

例如：`class、style、id`等属性



**使用inheritAttrs：**

当子组件`有单个根节点`时，`非Prop的Attribute将自动添加到根节点的Attribute中`

当我们不希望组件的根元素继承attribute，可以在组件中设置 `inheritAttrs: false`

例如：需要将attribute应用于根元素之外的其他元素；

```vue
// App.vue 
<template>
  <child-comp :name="name" data-num="123" id="childComp"></child-comp>		
</template>
```

```vue
// ChildComp.vue
<template>
  <div>		<!-- 非Prop属性：data-num="123" id="childComp"  -->
    <h2 :id="$attrs.id">{{name}}</h2>  <!-- 结果： <h2 id="childComp"></h2> -->
    <!-- 或者 -->
    <h2 v-bind="$attrs">{{name}}</h2>  <!-- 结果： <h2 data-num="123" id="childComp"></h2> -->
  </div>		
</template>

<script>
export default {
  name: "ChildComp",
  inheritAttrs: false,		// 别忘记禁用
  props:['name']
}
</script>
```

