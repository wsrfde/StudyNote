### Vue3组件化

> 全局组件和局部组件与Vue2相同，这里只记录Vue3特有的方法

#### 组件命名

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

