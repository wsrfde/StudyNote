### slot插槽的基本使用

> 类似电脑的usb，使用接口给电脑增加不同的功能，键盘/音响，而不是直接在组件里添加  

**介绍：**  
默认自定义标签内不允许添加内容，而插槽可以让我们在内部添加内容

**封装方法：**  
抽取共性，保留不同。

**使用方法：**

1. 直接在template中插入slot空标签，即可在自定义标签中插入内容，内容会自动替换空slot		
	```html
	<template>
	  <div>
	    <slot></slot>
	  </div>
	</template>
	```
	
2. 组件标签中插入的无论插入多少值，都会一起作为替换元素替换slot

3. 如果在slot中添加默认值，组件标签中没有插入内容则显示默认值

    ```html
    <slot><b>默认显示我</b></slot>
    ```

    

<br>

### 具名插槽slot

> 当有多个插槽时，我们只想替换其中一个，就需要用到具名插槽了 

```html
// cpn.vue
<slot name='myslot'>myslot</slot>
<slot name='newslot'>newslot</slot>	

//app.vue
<cpn>
  替换方式一：<span slot='mysolt'>我替换了myslot</span>
  替换方式二：
  <template v-slot:newslot>
    <span>我替换了newslot</span>
  </template>
</cpn>
```
<br>

### 编译作用域

  父组件模板的所有东西都会在父级作用域内编译；  
  子组件模板的所有东西都会在子级作用域内编译。  

**通俗点讲：**看标签的父组件，标签是在父组件中实现的，
那么作用域就是父组件，属性也是父组件的属性

```html
<div id="app">
  //它的父组件是vue实例，所以属性也是vue实例中的属性，和自定义组件无关
  <cpn v-show="isShow"></cpn>		//isShow=true
</div>

<script>
  const app = new Vue({
    el:'#app',
    data:{
      isShow:true,
    },
    components:{
      cpn:{
        template:'#cpn',
        data(){
          return{
            isShow:false,
          }
        }
      },

    }
  })
</script>
```

### 插槽作用域

> 在当前作用域中的插槽中，只能引用当前组件的data。

#### 如何在父组件中拿到子组件的数据？

1. 首先在slot中命名接收属性的key`（如newdata）`，并动态绑定子组件data

   ```html
   //子组件cpn
   <template id="cpn">
     <div>
       <slot :newdata="msg"></slot>
     </div>
   </template>
   ```

2. 在Vue实例中通过template接收(两种方式)  

  * 通过v-slot接收  **(建议用v-slot)**

    ```html
    //父
    <cpn>
      <template v-slot={newdata}>
        <h2>{{newdata}}</h2>
      </template>
    </cpn>
    ```

  * 通过slot-scope接收

    ```html
    //父
    <cpn>
      <template slot-scope=newname>
        <h2>{{newname.newdata}}</h2>
      </template>
    </scpn>
    ```

  

### 插槽高级技巧

我们都知道当使用插槽时，自定义标签中的内容会替换插槽。但我们想要给插槽设置样式该怎么做呢?

使用div包裹插槽，给div设置样式，利用cs继承特性，被替换的插槽也就有了样式

```html
<template id="cpn">
  <div>
    <!-- 在组件data中动态设置class -->
    <div :class="{active:isActive}">
      <slot></slot>
    </div>
  </div>
</template>
```

