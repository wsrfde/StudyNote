## Vue3组件化

> 全局组件和局部组件与Vue2相同，这里只记录Vue3特有的方法

### 组件命名

在组件命名的时候可以用大驼峰`MyComp`，也可以用分隔符的方式`my-comp`去注册组件。

但是在使用的时候推荐使用**分隔符的方式去使用**

```Vue
<template id="my-app">
<!-- 使用分隔符方式 -->
	<my-comp></my-comp>
</template>
<template id="my-comp">
  我是全局组件
</template>

<script>
const MyComp = {
  template: '#my-comp'
}
const app = Vue.createApp(App)
app.component('MyComp', MyComp)
app.mount('#app');
</script>
```

###  组件通信

#### 父传子Props

> 除了之前使用的数据传递方法，我们还可以直接使用`v-bind`把对象传递过去

```vue
// App.vue 
<template>
  <child-comp :name="info.name"></child-comp>		 <!-- vicer -->
  <child-comp v-bind="info"></child-comp>				 <!-- vicer -->
</template>

<script>
export default {
  name: 'App',
  components: {ChildComp},
  data(){
    return{
      info:{
        name:'vicer',
        age:18
      }
    }
  }
}
</script>
```

```vue
// ChildComp.vue
<template>
  <div>{{name}}</div>
</template>

<script>
export default {
  name: "ChildComp",
  props:['name']
}
</script>
```

##### Props的使用方法

* 使用数组定义传值

  ```js
  props:['name','age']
  ```

* 使用对象定义传值

  ```js
  props:{
  	//基础的类型检查（`null`和`undefined`匹配任何类型）
  	propA:Number,
  	//多个可能的类型
  	propB:[String,Number],
  	//必填的字符串
  	propC:{
  		type:String,
  		required:true
  	},
  	//带有默认值的数字
  	propD:{
  		type:Number,
  		default:100
  	},
  	//带有默认值的对象
  	propE:{
  		type:Object,
  		//对象或数组默认值必须从一个工厂函数获取
  		default(){
  			return {message:'hello'}
  		}
  	},
  	//自定义验证函数
  	propF:{
  		validator:function(value){
  			//这个值必须匹配下列字符串中的一个
  			return ['success','warning','danger'].indexOf(value) !== -1;	
  		}
  	}
    //具有默认值的函数
    propG:{
      type:Function,
      //与对象或数组默认值不同，这不是一个工厂函数。这是一个用作默认值的函数
      default(){
        return 'Default function'
      }
    }
  }
  ```

#### 非Prop的Attribute

**定义：当我们给某个组件传递属性，该属性并没有定义对应的props或emits时，就被称为非Prop的Attribute**

例如：`class、style、id`等属性

##### Attribute继承

当子组件`有单个根节点`时，`非Prop的Attribute将自动添加到根节点的Attribute中`

```vue
// App.vue 
<template>
  <child-comp :name="name" data-num="123" id="childComp"></child-comp>		
</template>
```

```vue
// ChildComp.vue
<template>
  <div>				<!-- 非Prop属性：data-num="123" id="childComp"  -->
  	<h2>{{name}}</h2>
  </div>		
</template>

<script>
export default {
  name: "ChildComp",
  props:['name']
}
</script>
```

![image-20210609091102461](D:\Vicer\link\study-note\Vue3\img\not-props.png)

##### 禁用Attribute继承

> 当我们不希望组件的根元素继承attribute，可以在组件中设置 `inheritAttrs: false`

禁用继承的场景：需要将attribute应用于根元素之外的其他元素；

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

##### 多个根节点继承

> 当我们子组件有多个根节点时，并不会进行默认绑定，需要`我们手动绑定某一个节点`，否则会报一个警告

手动绑定多根组件时，`无需设置inheritAttrs`属性

```vue
// ChildComp.vue
<template>
		<!-- 非Prop属性：data-num="123" id="childComp"  -->
    <h2 :id="$attrs.id">{{name}}</h2>  <!-- 结果： <h2 id="childComp"></h2> -->
    <h2>{{name}}</h2>  
    <h2>{{name}}</h2>  
</template>
```

#### 子传父emit

在传递之前进行emits得定义

* 使用数组进行定义

  ```js
  emits:['add','sub']
  ```

* 使用对象进行定义

  ```js
  emits:{
    add: null,
    sub(num) {
      return num > 5 ? true : false  // 当不符合条件时，会报一个警告，数据仍然正常返回
    }
  }
  ```

问题：在vue2中无需进行定义即可传递，vue3中定义会提高性能？？



### 非父子组件通信

#### provide和inject深度传递

> `provide` 和 `inject` 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在其上下游关系成立的时间里始终生效。

**provide：**用来传递数据

**inject：**用来接收数据，也可以设置来源和默认值



##### **基本使用方法**

```vue
// App.vue
<template>
  <child-comp></child-comp>
</template>

<script>
import ChildComp from "./ChildComp";

export default {
  name: 'App',
  components: { ChildComp },
  //provide: {
  //  name: 'vicer',
  //  age: 18,
  //}
  /**
  *  @desc:如果我们想要使用data中得数据，需要把provide改为函数，改变this得指向
  */
  provide(){
    return {
      name: 'vicer',
      age: 18,
      length: this.friends.length
    }
  },
  data() {
    return {
      friends: ['aaa', 'bbb', 'ccc']
    }
  },
}
</script>
```

```vue
// ChildComp.vue
<template>
  <div>{{name}}-{{age}}-{{length}}</div>		<!-- 结果：vicer-18-3 -->
</template>

<script>
export default {
  name: "SunChildComp",
  inject:['name','age','length'],
}
</script>
```

##### 动态改变provide数据

```js
// App.vue
import ChildComp from "./ChildComp";
import {computed} from "vue";		// 这个computed是vue3的新特性

export default {
  name: 'App',
  components: {ChildComp},
  provide(){
    return {
      name: 'vicer',
      age: 18,
      length: computed(()=> this.friends.length)		// 改为计算属性，通过es6得箭头函数语法糖return值
    }
  },
  created() {
    setTimeout(()=>{
      this.friends.push('jack')
    },2000)
  },
  data() {
    return {
      friends: ['aaa', 'bbb', 'ccc']
    }
  },
}
```

```vue
// ChildComp.vue
<template>
	<!-- length通过computed返回了ref对象，所以需要取出里面得value -->
  <div>{{name}}-{{age}}-{{length.length}}</div>		<!-- 结果：vicer-18-4 -->
</template>

<script>
export default {
  name: "SunChildComp",
  inject:['name','age','length'],
}
</script>
```

#### 全局事件总线mitt库

> Vue3从实例中移除了 $on、$off 和 $once 方法，所以我们如果希望继续使用全局事件总线，要通过第三方的库

Vue3官方有推荐一些库，例如 mitt

安装：`npm i mitt`

封装工具：方便我们在每个组件中引用

```js
// eventbus.js
import mitt from 'mitt';

const emitter = mitt();
// export const emitter1 = mitt();   //为了防止多个事件总线冲突，我们可以单独命名自己的事件总线

export default emitter;
```

使用：

```js
// 发射事件总线
import emitter from './utils/eventbus';

btnClick() {
   emitter.emit("why", {name: "why", age: 18});
}
```

```js
// 接收事件总线
import emitter from './utils/eventbus';
created() {
  // 接收单一事件总线
  emitter.on("why", (info) => {
    console.log("why:", info);
  });
	// 接收全部事件总线
  emitter.on("*", (type, info) => {
    console.log("* listener:", type, info);
  })
}
```

