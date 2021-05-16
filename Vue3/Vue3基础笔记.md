## Vue3基础笔记

### template模板的两种写法

一、使用script标签，并且标记它的类型为 x-template

```html
<div id="app"></div>
<script type="x-template" id="why">
  <div>
    <div>{{count}}</div>
    <button @click="addCount">+</button>
    <button @click="lessCount">-</button>
  </div>
</script>

<script>
  Vue.createApp({
    template:`#why`,
  }).mount('#app')
</script>
```

二、使用任意标签（**通常使用template标签**，因为不会被浏览器渲染），设置id； 

template元素：是一种用于保存客户端内容的机制，该内容再加载页面时不会被呈现，但随后可以在运行时使 用JavaScript实例化；

```html
<div id="app"></div>
<template id="why">
  <div>
    <div>{{count}}</div>
    <button @click="addCount">+</button>
    <button @click="lessCount">-</button>
  </div>
</template>

<script>
  Vue.createApp({
    template:`#why`,
  }).mount('#app')
</script>
```

### 源码调试

一、在GitHub上搜索` vue-next` 

二、下载下来在`package.json`中，在`dev`命令中增加`--sourcemap` 

三、引入Vue，然后进行`debugger`

