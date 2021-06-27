## Composition API

为什么我们要使用composition API，而放弃传统的options API？

答：代码分散，实现一个功能要分散到各个属性中，当一个功能还好，如果多个功能的话将会使组件变复杂难以维护，代码难以阅读理解。

当使用Composition API时会帮我们把**同一个逻辑关注点相关的代码收集在一起**，也有人把Vue Composition API简称为**VCA**

### Composition API基础使用

#### setup函数

> **注意：setup函数中不能使用this！**

##### setup函数的参数

* `props`  通过props传递的属性，可以通过props参数获取
* `context`  上下文，也称之为是一个**SetupContext**，其中包含三个属性
  * `attrs`：所有的非prop的attribute（如id、class，没有通过props传递的属性）
  * `slots`：父组件传递过来的插槽（这个在以render函数返回时会有作用）
  * `emit`：当我们组件内部需要发出事件时会用到emit（我们不能访问this，及通过 this.$emit发出事件）

```vue
// home.vue
<script>
export default {
  name: "Home",
  props: {
    msg: {
      type: String,
      default: ''
    }
  },
  // (props, context)
  setup({msg}, {attrs, slots, emit}) {	// 对象结构
    console.log(msg)	// => hhh
    console.log(attrs)	// => {id: "home"}
  }
}
</script>
```

```vue
// A
<home id="home" msg="hhh"><p>我是插槽</p></home>
```



##### setup函数的返回值

> setup的返回值可以在模板template中被使用，也就是说我们无需再使用data属性

```vue
<template>
  <div>
    <span>home：{{message}}</span>
  </div>
</template>
<script>
  setup({msg}, {attrs, slots, emit}) {
    let message =  msg + '111'
    return {
      message
    }
  }
</script>
```

#### setup函数中定义响应式

> 如果我们想要在setup中定义响应式，可以引用以下两种方法，推荐Ref API

##### Reactive API

> 我们在编写data选项时，其内部响应式处理就是交给了Reactive函数

**Reactive API的缺点：**要求我们必须传入的是**一个对象或者数组类型**，否则会报警告并无法展示

```vue
<template>
  <div>
    <!--  reactive  -->
    <p>{{ status.count }}</p>
    <button @click="changCount">按钮</button>
  </div>
</template>

<script>
import { reactive } from "vue";

export default {
  name: "Home",
  setup(props, context) {
    let status  =  reactive({
      count:100
    })
    let changCount = ()=>{
      status.count += 1
      console.log(status.count)
    }
    return {
      status,
      changCount
    }
  }
}
</script>
```

##### Ref API

> ref 会返回一个可变的响应式对象，内部数据通过value来进行维护

**Ref API注意事项：**

* 由于ref数据通过value维护，所以在setup函数中我们以然需要使用`ref.value`的方式去使用

* 在template中引入ref的值时，Vue会自动帮助我们进行解包操作，即我们不需要在template中使用`ref.value`，直接使用`ref`即可

```vue
<template>
  <div>
    <!-- ref  -->
    <p>{{ count }}</p> 	<!-- => ref变量：自动解包 -->
    <!-- 普通obj  -->
    <p>{{ obj.count.value }}</p>  <!-- => 普通的obj包含ref变量：不会解包 -->
    <!-- ref obj -->
    <p>{{ newObj.count }}</p>  <!-- => ref对象包含(ref/普通)变量：自动解包 -->
    <!--  reactive  -->
    <p>{{ status.count }}</p>  <!-- => reactive对象包含ref变量：自动解包 -->
    
    <button @click="changCount">按钮</button>
  </div>
</template>

<script>
import {reactive, ref} from "vue";

export default {
  name: "Home",
  setup(props, context) {
    let count = ref(100)	// count.value += 1
    let obj = {   // obj.count.value += 1
      count
    }
    let newObj = ref({  // newObj.value.count += 1
      //count:100       // newObj.value.count += 1  :包含非ref变量，使用方式一样
      count
    })
    let status = reactive({ // status.count += 1
      count
    })
    let changCount = () => {
	  // count.value += 1
      // obj.count.value += 1
      // newObj.value.count += 1
      // status.count += 1
    }
    return {
       count,
         obj,
      newObj,
      status,
      changCount
    }
  }
}
</script>
```

**总结：**

* Ref 在setup中无论以那种形式都需要通过value改变属性的值。

* (reactive/ref)对象包裹ref变量，都可以帮助其自动解包，但在setup中使用方法略有不同，`统一起见直接使用ref包裹最外层对象或变量即可，内部无需进行ref包裹，并且ref支持所有数据类型`

