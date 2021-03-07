### 谈一谈你对MVVM原理的理解

**MVC：**传统的`MVC`指，请求服务端路由，将结果返给前端，页面重新渲染

**MVVM：**传统的方法是前端操作`DOM`节点，将数据手动渲染到页面上。

`MVVM`模式不需要操作`DOM`元素，而是将数据绑定到`ViewModel`层上，会自动将数据渲染到页面。

当数据变化时更新页面，当视图变化时通知`ViewModel`更新数据，`ViewModel`就是`MVVM`模式的桥梁



### 响应式数据的原理

**核心点：**`Object.defineProperty`

**理解：**在Vue初始化数据时，`Object.defineProperty`会给data中的属性重新定义，当页面取到对应属性时，会进行**依赖收集**（收集当前组件的watcher），如果属性发生变化，会通知相关依赖进行更新操作



### 为何Vue采用异步渲染

**理解：** 为了性能考虑，因为**Vue是组件级更新**

什么是组件级更新，假如在一个组件中，有两个数据a和b，我们修改一个数据就对一个数据进行更新太浪费性能。所以干脆就给同一组件中的数据一个相同的`watcher.id` （防止多次更新），并把`watcher`push到更新队列，等他们都改完之后，再进行**异步更新视图**，也就是调用`nextTick`方法



### nextTick的实现原理

利用**宏任务**和**微任务**，来进行异步操作。调用nextTick时会把回调函数存入数组，然后依次使用监听**异步方法**是否可用，最后调用

**异步方法：**Promise（微任务回调）>MutationObserve（监听DOM变化完成的回调）>setImmediate（等语句全部完成的回调）> setTimeout ()

**这些方法依次检测，从前到后，哪个符合条件用哪个，最后实在不行再使用setTimeout**



### Vue中Dom操作是同步还是异步

**js里面的Dom操作代码，是同步执行，但浏览器进行的Dom渲染，是异步操作。**

**渲染一次Dom的时机是，当前宏任务和小尾巴微任务执行完，下一个宏任务开始前**

`vue`的`nextTick`方法，则是使用H5的Api---`MutationObserver`，监听浏览器将Dom渲染完成的时机。

提醒：js操作Dom是同步的，但操作Dom，毕竟超出了js本身语言的Api，每操作一次Dom，都需要消耗一定的性能，所以，在适合的情况下，最好先把要修改的Dom的内容，以字符串或者虚拟Dom的形式拼接好，然后操作一次Dom，把组装好的Dom字符串或虚拟Dom，一次性的塞进HTML页面的真实Dom中。



### Vue中Computed的特点

默认 computed 也是一个 watcher 是具备缓存的，只要当依赖的属性发生变化时才会更新视图



### Watch中的deep:true 是如何实现的

deep:true的核心方法就是递归，把监听的数据进行遍历都进行取值（所以性能不高），并把watcher存入对应的属性依赖中，这样数据发生变化时会通知数据更新



### Vue的生命周期

**理解：**

**每个生命周期什么时候被调用：**

* `beforeCreate`在实例初始化之后，数据观测（data observer）之前被调用
* `create`实例 已经创建完成之后被调用，在这一步，实例已经完成以下的配置：数据观测（data observe），属性和方法的运算，watch/event事件回调，这里没有$el
* `beforeMount`在挂在开始之前被调用：相关的render函数首次被调用
* `mounted` el 被创建的`vm.$el`替换，并挂载到实例上去之后调用该钩子
* `beforeUpdate`数据更新时调用，发生在虚拟DOM重新渲染和打补丁之前
* `updated`由于数据更改导致的虚拟DOM重新渲染和打补丁，在这之后可调用该钩子
* `beforeDestroy`实例销毁后调用。调用后，Vue实例指示的所有东西都会解除绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。**该钩子在服务器渲染期间不被调用**

**要掌握每个生命周期内部可以做什么事情**        (这才是面试要考的点)

* `created`实例已经创建完成，因为它时最早触发的原因，可以进行一些数据/资源的请求。
* `mounted`实例已经挂载完成，可以进行一些DOM操作
* `beforeUpdate`可以在这个钩子更改状态，因为它是在渲染之前，不会触发附加的重渲染
* `uodated`可以执行依赖于DOM操作。然而在大多数情况下，**你应该避免在此期间更改状态**，因为这可能会导致更新无线循环。**该钩子在服务器服务器渲染期间不被调用**
* `beforeDestory`可以执行一些优化操作，清空定时器，解除绑定事件



### Ajax请求放在哪个生命周期中

* 在`created`的时候，视图中的 dom 并没有渲染出来，所以此时如果直接去操 dom 节点，无法找到相
  关的元素
* 在`mounted`中，由于此时 dom 已经渲染出来了，所以可以直接操作 dom 节点

> 服务端渲染不支持mounted方法，所以在服务端渲染的情况下统一放到created中



### 何时需要使用beforeDestroy

* 可能在当前页面中使用了` $on `方法，那需要在组件销毁前解绑。
* 清除自己定义的定时器
* 解除事件的绑定` scroll` `mousemove` 等



### 虚拟DOM和AST树

**虚拟DOM(即Vnode)：**就是用一个对象，来描述一个DOM元素

**AST树：**就是用一个对象，来描述一个HTML或JS语法



**一、四则运算表达式**

表达式: 1+3*(4-1)+2

***抽象语法树为：***

[<img src="http://blog.chinaunix.net/attachment/201203/19/26750235_1332131475E5KN.png" alt="img" style="zoom: 80%;" />



**二、函数转代码树**

```js
//AST语法树代码示例
function square(n) {
  return n * n;
}

// 转换后的AST
let ast = {
  type: "FunctionDeclaration",
  id: {
    type: "Identifier",
    name: "square",
  },
  params: [
    {
      type: "Identifier",
      name: "n"
    }
  ]
}
```



###  Vue中模板编译原理

> 将 template 转化成 render 函数

1. 首先将模板转化成ast语法树             

   模板：`<div><p>Hello world!</p></div>`     ast语法树：上面示例

2. 将ast语法树遍历成一个字符串，并用with包装起来           

   生成字符串：`with(this){return _c('div',{attrs:{id:container}},['p',[('hello'),('span')]])}`

3. 最后通过`new Function(render字符串)`实现模板引擎



### Vue中v-if和v-show的区别

* `v-if` 如果条件不成立不会渲染当前指令所在节点的 dom 元素
* `v-show` 只是切换当前 dom 的显示或者隐藏

原理：

`v-if`：将模板转换成ast语法树，然后遍历字符串用with包装。返回code时通过三元运算符，判断true/false，是否渲染DOM

`v-show`：内部调用的是指令，当true时展示，false时display为none



### 为什么v-for和v-if不能连用

`v-for`会比`v-if`的优先级高一些，如果连用的话会把`v-if`给每个元素都添加判断，造成性能问题



### 用Vnode来描述一个DOM结构

虚拟节点就是用一个对象来描述真实的 dom 元素

```js
function createElement(tag,data,...children){
  let key = data.key;
  delete data.key;
  children = children.map(child=>{
    if(typeof child === 'object'){	//函数也是对象
      return child
   }else{
      return vnode(undefined,undefined,undefined,undefined,child)
   }
 })
  return vnode(tag,data,key,children);
}
function vnode(tag,data,key,children,text){
  return {
    tag, // 表示的是当前的标签名
    data, // 表示的是当前标签上的属性
    key, // 唯一表示用户可能传递
    children,
    text
 }
}

let r = createElement('div',{id:'contain'},createElement('p',{},'hello'),'zf');
console.log(r);
```

**template转虚拟DOM内部实现流程**

将template  ast树 => codegen => 转换成render函数  =>  内部调用createElement方法 =>  虚拟DOM



### diff算法的时间复杂度

> 面试中一般会问vue中的diff做了哪些优化 



### 简述Vue中diff算法原理

理解：

1. 先同级比较，在比较子节点
2. 先判断一方有子节点，一方没子节点的情况
   * 当新节点有，旧节点没有子节点时，给旧节点添加子节点
   * 当新节点没有，旧节点有子节点时，删除旧节点子节点
3. 比较都有子节点的情况
4. 递归比较子节点



### v-for中为什么要用key

**当没有使用key时：**

1. 在删除元素时，diff算法会暴力比对，先查看节点是否可复用，然后进行复用并操作DOM，最后删除多余的元素

   ```html
   <li>1</li>
   <li>2</li>
   <li>3</li>
   
   //如删除1元素
   
   //对比发现可复用，于是1变成2，2变成3，最后删除是原来的<li>3</li>
   <li>2</li>
   <li>3</li>
   ```

2. 原来元素的状态也被复用（如input框被选中，根据第一条经验，元素1被删除，状态会直接被复用到元素2中）

**当使用key时：**

diff算法直接进行对比，直接删除指定元素，不需要复用操作DOM等

**注意：**当使用key时最好不要使用index，有id使用id（没id找后端，如果是静态列表没有问题）



### 描述组件渲染和更新过程

**渲染组件**时，会通过` Vue.extend` 方法构建子组件的构造函数，并进行实例化。最终手动调用
$mount() 进行挂载。

**更新组件**时会进行 patchVnode 流程.核心就是diff算法



### 组件中的 data为什么是一个函数?

同一个组件会被复用多次，如果`data`是一个对象，那么所有组件都公用了一个对象，修改一个值其他页面中数据也会跟着变化。为了保证组件数据的独立性，每个组件必须通过data函数返回一个新的对象作为组件状态

延伸问题：为什么vue是对象？

因为vue只被new一次，不会考虑复用问题

### v-model中的实现原理及如何自定义v-model

**原理：**v-model 可以看成是 value+input方法 的语法糖

**代码：**`<input type="text" :value="val" @input="val = $event.target.value">`



**自定义v-model：**

```jade
<el-checkbox v-model="check">			//别忘记在data中定义check属性

Vue.component('el-checkbox',{
  template:`<input type="checkbox" :checked="check"
						@change="$emit('change',$event.target.checked)">`,		//通过事件发送给自定义model
  model:{
    prop:'check', // 更改默认的value的名字
    event:'change' // 更改默认的方法名
 },
  props: {
    check: Boolean
 },
})
```



### Vue中v-html会导致哪些问题?

* 可能会导致`xss`攻击 (里面的代码会被当做脚本运行，来获取用户的cookie)
* `v-html`会替换掉标签内部的子元素



### Vue父子组件生命周期调用顺序

**加载渲染过程**

* 父`beforeCreate`->父`created`->父`beforeMount`->子`beforeCreate`->子`created`->子`beforeMount`->子`mounted`->父`mounted`

**子组件更新过程**

* 父`beforeUpdate`->子`beforeUpdate`->子`updated`->父`updated`

**销毁过程**

* 父`beforeDestroy`->子`beforeDestroy`->子`destroyed`->父`destroyed`



#### **理解:**

组件的**调用顺序**都是**先父后子**

**渲染完成的顺序**肯定是**先子后父**

组件的**销毁操作**是**先父后子**

**销毁完成**的顺序是**先子后父**



### Vue组件如何通信

* 父子间通信 父->子通过 props 、子-> 父 $on、$emit (发布订阅)
* 获取父子组件实例的方式 $parent、$children
* 在父组件中提供数据子组件进行消费 Provide、inject (插件中用到)
* `ref `获取实例的方式调用组件的属性或者方法
* `$attrs`和 `$listeners`父孙组件通信
* `Event Bus` 实现跨组件通信
* `Vuex `状态管理实现通信 



### Vue中相同逻辑如何抽离？

`Vue.mixin` 用法 给组件每个生命周期，函数等都混入一些公共逻

源码理解：mixin的核心代码是merge(合并)

生命周期的合并方式：是把生命生命变成数组，然后进行`concat`



### 为什么要使用异步组件？

**理解:**
如果组件功能多打包出的结果会变大，我可以采用异步的方式来加载组件。主要依赖 import() 这
个语法，可以实现文件的分割加载。

```js
//router的组件异步调用
components:{ 
AddCustomerSchedule:(resolve)=>import("../components/AddCustomer") 
}
```



### 什么是作用域插槽

 **普通插槽：**插槽的作用域为父组件

**作用域插槽：**插槽的作用域为子组件



### 谈谈你对 keep-alive 的了解？

`keep-alive `可以实现组件的缓存，当组件切换时不会对当前组件进行卸载,常用的2个属性
`include / exclude` ,2个生命周期 `activated `,`deactivated `

**LRU算法：最久未使用算法**

数组中每使用一次组件，移动到数组末端，当超出限制时，移除数组前端的key



### Vue中常见性能优化

#### 编码优化

1. 不要将所有的数据都放在data中，data中的数据会增加getter和setter，会收集对应的watcher
2. vue在v-for时给元素绑定事件最好用事件代理
3. SPA页面采用keep-alive缓存组件
4. 拆分组件（提高复用性、增加代码的可维护性，减少不必要的渲染）
5. v-if当值为false时内部指令不会执行，具有阻断功能。很多情况下使用v-if代替v-show
6. key保证唯一性（默认vue会采用就地服用策略）
7. `Object.freeze`冻结数据（当有一大串数据不需要改变只需要进行渲染时，可以冻结数据，减少data的数据劫持达到优化性能）
8. 合理使用路由懒加载、异步组件
9. 尽量采用runtime运行时版本
10. 数据持久化问题（防抖、节流）

#### Vue加载性能优化

* 第三方模块按需导入 ( babel-plugin-component )

* 滚动到可视区域动态加载 ( https://tangbc.github.io/vue-virtual-scroll-list )			//只显示3页的DOM，上页，当前页，下页

* 图片懒加载 (https://github.com/hilongjw/vue-lazyload.git)									//不全部加载，滚动到显示区域加载图片

  

#### 用户体验

* app-skeleton 骨架屏   //比如饿了么加载页面，先显示一个灰色框架
* app-shell app壳     //就是先渲染个框架，或者是导航，就是告诉用户正在加载中
* pwa                       //可以实现h5的离线缓存，就是serviceworker ，国外很多，国内一般用的不多，兼容太差



#### SEO优化

* 预渲染插件 ( prerender-spa-plugin)		//就是把代码提前运行起来，把代码保存下来。缺点：如果页面实时数据要求很高，只能使用ssr
* 服务端渲染 ssr                                   //通过服务端渲染，有很好的SEO优化       缺点：把客户端的压力转给服务器，会有性能问题，可以尝试缓存来解决问题



#### 打包优化

* 使用 cdn 的方式加载第三方模块   //如  `<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>`
* 多线程打包 happypack       //小项目一般看不到效果
* splitChunks 抽离公共文件
* sourceMap 生成         //sourceMap 是一个信息文件，里面储存着位置信息。当代码被压缩成如`xx.min.js`时，代码报错将找不到指定的指定的位置，报错信息也毫无头绪，而sourceMap可以在出错时显示原始代码。    当代码被压缩混淆编译后，上线时我们无法找到报错位置，需要用到sourceMap



#### 缓存，压缩

* 客户端缓存、服务端缓存
* 服务端 gzip 压缩



### Vue3.0你知道有哪些改进?

* Vue3 采用了TS来编写
* 支持 Composition API                                   //composition API 解决了什么问题：传统的option配置写到特定区域，后续维护非常的复杂，同时代码可复用性不高，composition API 就是为了解决这个问题，可以写出高内聚、低耦合的代码
* Vue3 中响应式数据原理改成 proxy             //之前的data是只要初始化，要递归所有的data，增加拦截，性能不高
* vdom 的对比算法更新，只更新 vdom 的绑定了动态数据的部分



### 实现hash路由和history路由

* `onhashchange `#                       //就是看着有点丑，多一个#号
* `history.pushState` h5 api         //可能导致页面不存在，需要服务端来解决



### 	Vue-Router中导航守卫有哪些？

#### 组件内导航守卫：

`beforeRouterEnter`: 在页面还未进入的时候。这个时候vue实力还未创建。因此访问不到this。
`beforeRouterUpdate`:当前页面组件被复用的时候，页面的路径发生了改变，会触发此方法。在这个时候是可以访问到this的
`beforeRouterLeave`:离开当前路由的时候，即要离开当前页面的时候，这个时候是可以访问到this的

#### 全局导航守卫

`router.beforeEach`: 全局前置守卫进入路由之前触发
`router.beforeResolve`:全局解析守卫在beforeRouteEnter调用之后调用
`router.beforeAfter`: 全局后置钩子进入路由后触发



### action 和 mutation区别

*  **action 异步操作**，可以获取数据后调佣 mutation 提交最终数据
* **mutation 是同步更新数据**(内部会进行是否为异步方式更新数据的检测) $watch 严格模式下会报
  错



### 简述Vuex工作原理

循环：`Vue components` —> `Actions` —> `Mutation` —> `State`  —>  `Vue components`



### 双向绑定和 vuex 是否冲突?

冲突，用计算属性返回即可

```html
name：<input type="text" v-on:input="setVal"  v-bind:value="val">
val:{{val}}

<script>
  computed:{
    val(){
      return this.$store.state.counter;
    }
  },
  methods:{
    setVal(e){
      let currentVal =  e.target.value;
      this.$store.commit('changeCount',currentVal)
    },
  }
</script>
```

```js
const store = new Vuex.Store({
    state:{
      counter:1000,
    },
    mutations:{
      changeCount(state,payload){
        this.state.counter = payload;
      },
    },
}
```

