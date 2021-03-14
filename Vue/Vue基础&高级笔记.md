# Vue基础学习笔记

<br>

### 方法和函数的区别
1. 面向对象的时候称为方法，面向流程的时候叫函数。  
2. 方法是作为一个对象实例的属性的函数  


<br>

### 生命周期

生命周期：事务从诞生到消亡的整个过程  
以下是生命周期函数，可以在某个周期做某件事

	[
		'beforeCreate',
		'created',		//组件创建完成
		'beforeMount',
		'mounted',		//DOM树创建完成
		'beforeUpdate',	//数据更新前   可以更改数据
		'updated',		//DOM更新完成   最好不要在这里改数据，会发生死循环
		'beforeDestroy',	//事件的移除  清空定时器
		'destroyed',	//手动移除组件或路由切换，会发生视图改变
		'activated',	//钩子函数，需要作用keep-alive下
		'deactivated',	//钩子函数，需要作用keep-alive下
		'errorCaptured',
		'serverPrefetch'
	  ]


<br>

### 计算属性  

> computed：运行时作为一个属性来添加，所以调用里面函数时不用加括号()  


注：函数起名时不要用动词，因为computed时作为属性来调用，如getBgColor => BgColor  

**区别：**  
	conputed在多次调用时只执行一次，有缓存，只做数据处理  
	methods是调用几次执行几次，没有缓存，浪费性能  



<br>

### 函数重载

在js中相同名的函数，下面的函数会覆盖上面的  

在typescript中，函数重载指
* 两(多)个函数的函数名相同
* 传入的参数不同
* 与返回值无关


<br>

### v-on的参数问题

1. 如果该方法不需要额外参数，那么调用方法时()可以不添加。  

		@click = 'getBtn'
2. 如果方法中有参数，调用时没有添加()和参数,则默认传入event
3. 如果需要同时传入某个参数，同时需要event时，可以通过$event传入事件。  

		@click = 'getBtn("abc",$event)'  
	

<br>

### v-on的修饰符

* .stop - 调用 停止事件冒泡 event.stopPropagation() 。

   `<a v-on:click.stop="doThis"></a>`

* .prevent - 调用 阻止默认事件 event.preventDefault()。
		`<form v-on:submit.prevent="onSubmit"></form>`
	
* 修饰符可以串联
		
	
		`<a v-on:click.stop.prevent="doThat"></a>`
	
* .{keyCode | keyAlias} - 只当事件是从特定键触发时才触发回调。

   ```js
   <input type="text" @keyup="keyup($event.which)"> 	//监听键码
   <input type="text" @keyup.tab="keyup()"> 
   <input type="text" @keyup.13='keyup()'>
   ```

* .self - 只当在 event.target 是当前元素自身时触发处理函数 

   即事件不是从内部元素触发的

   `<div v-on:click.self="doThat"></div>`

* .native - 监听组件根元素的原生事件。

* .once - 只触发一次回调。


<br>

### v-if、v-else-if、v-else  

> **原理：**  

v-if后面的条件为布尔值，为flase时对应的元素以及其子元素不会渲染。

	<h2 v-if="ishow">当ishow为true时，显示我</h2>
	<h2 v-else>当ishow为false时，显示我</h2>

这三个指令和JavaScript的指令类似  

> **bug注意：**  
* v-if的渲染时为虚拟dom，为了节省内存会把判断有无原标签，有的话原标签复用。  
* 但后果是如果输入input标签进行if else切换会出现原来输入的value还存在的问题。  
* 解决方法是在标签内部添加key，key值不一样不会复用。   _ps: key='new'_


<br>

### v-show

v-show和v-if非常相似，用于决定一个Dom会不会渲染
true渲染，false隐藏

> **区别：**
* v-show是在标签内部添加行内样式display='none'
* v-for是直接创建或移除Dom
* 实际开发中我们用v-for多一点


<br>

### v-for

> v-for遍历数组

```html
<!-- 项目，下标 -->
<p v-for="(item,index) in message">{{index}}-{{item}}</p>
```

<br>

> v-for遍历对象

```html
<!-- 值，键，下标 -->
<li v-for="(value,key,index) in message">{{index+'.'+key+'-'+value}}</li>
```

<br>

### v-for 组件的key属性

* 官方推荐我们使用v-for时，给对应的元素或组件添加上一个:key属性。  
* Vue的虚拟DOM的Diff算法在更新操作节点时，给某个元素插入赋值，
	后面的元素依次更新，没有则重新创建dom再赋值，来完成节点操作，
	这样的效率很慢```ps：ABCD => ABECD中，c=>e，d=>c，''=>d```
* 添加key可以给每个元素做唯一标识，Diff算法就可以正确识别节点准确更新
* **所以，key的作用主要是为了高效的更新虚拟DOM。**

#### 使用key的示例

```html
<!--如果是静态资源，key可以使用index，但如果不是，则使用id保证当前item的key则是唯一的-->  
<p v-for="item in message" :key='item.id'>{{item}}</p>
```

#### 使用template时不能用key怎么办?

> template是空标签，key必须要绑定到真实dom上

```html
<template v-for='(item,index) in arr'>
  <p :key='`name${index}`'>{{item}}</p>
  <p :key='`index${index}`'>{{index}}</p>
</template>
```

#### vue的复用策略

当使用if进行替换操作时，vue默认会复用原来的dom，解决办法便是加上key

```html
<template v-if='isShow'>
  <div>我是A</div>
  <input type="text" key='1'>
</template>
<template v-if='!isShow'>
  <div>我是B</div>
  <input type="text" key='2'>
</template>
```



<br>

### vue数据响应式

> 响应式是指数据可以动态响应到页面上，不需要手动刷新

**如果数组里是对象，直接更改即可，如果是纯数组，则需要用到以下的方法**

> 以下方法操作数组vue都是响应式的  
* push()
* pop()
* shift()
* unshift()
* splice()
* sort()
* reverse()  


> 直接使用下标的方式来改变数组不是响应式  

	this.arr[0] = 'aaa'  

解决办法  

	(被修改的obj/数组，key/下标，要修改的值)
	Vue.set(this.arr,0,'bbb')

> 删除数据delete也不是响应式

	delete this.obj.age;

解决办法

	(被删除的obj，key)
	Vue.delete(this.obj,'age')

### vue的初始化选项

获取vue最开始定义的属性值，并不会被后来的改变所影响

```js
let vm = new Vue({
  el:'#app',
  customOption:'foo'
})
vm.customOption = 'obj';

console.log(vm.$options.customOption) //foo
```



<br>

### 过滤器(filters)

* 过滤器filters和计算属性computed都是vue的一种方法  
* 内部也是一种函数方法，返回过滤后的值
* 和computed一样，调用时无需加括号()
* 但过滤器函数内可以传值，传递的是当前被过滤的数据


```js
<h2>{{数据 | 过滤器}}</h2>
```

```html
<div id='app'>
	<h2>{{message | changeMessage}}</h2>
</div>

<script>
	const app = new Vue({
		el:'#app',
		data:{
			message:1.23
		},
		filters:{
			changeMessage(value){
				//toFixed是显示小数点后几位
				return value.toFixed();
			}
		}
	})
</script>
```

<br>

### 监听data属性(watch)

> 用法是监听data的属性，当属性发生改变调用函数。和methods同级

注意：不要使用箭头函数来定义watch函数。因为箭头函数指向父级作用域上下文，不会指向vue实例
```js
	watch:{
	  name(){
      //执行函数...
			setTimeout(this.refresh,20)
	  }
	}
```

#### watch的高级用法

```js
watch:{
  name:{
    handler(newVal){    //watch的处理函数
      console.log(newVal);
    },
      immediate:true,   //即时：第一次定义data时也被监控
      deep:true,    //深层：监控data中对象中的参数
      lazy:true     //懒惰：compouted的实现
  }
}
```



<br>

### img加载监听

原生的js监听图片: 

	img.onload = function() {}  
Vue中监听:  

	@load='方法'

### $nextTick下一帧

$nextTick()内部传递箭头函数

意为箭头函数可以在其他函数运行完的下一帧运行。 

 


# Vue高级技巧	

### 事件总线

在非父子组件中是无法直接通信的。  
在vue中除了vuex可以统一管理状态，还有另外一种方法叫事件总线

**原理：**  
在vue原型中创建Vue实例，利用vue的事件总线进行发射接收

* 创建$bus
		`Vue.prototype.$bus = new Vue()`
* 发射事件 
		//参数可以省略
		`this.$bus.$emit('事件名',参数)`
* 监听事件 
		//监听事件总线一般会在生命周期函数mounted中监听
		`this.$bus.$on('事件名',()=>{})`
		

注意：  
使用$bus要在destroyed生命周期函数中使用$off销毁，要不然会叠加触发次数 
		

```js
	//全局销毁:当这个组件销毁的时候bus也跟着一起销毁
	this.$bus.$off('事件名')
	
	//局部销毁:如果你需多次调用总线，可以局部销毁
	//把箭头函数赋值给一个变量名，销毁时只需要销毁变量即可
	this.myFun = ()=>{fun()}
	this.$bus.$off('事件名称',this.myFun)
```


### 防抖debounce

在实际开发中我们可能每当用户输入一个字符就请求一次数据，这时一个很大的性能浪费，
可以当用户在300ms内持续输入时，取消数据请求，过了时间后再发送数据，缓解服务器压力。
这种操作就叫做防抖处理


```javascript
//ES6写法
debounce(fun,delay){
  let timer = null;
  return function (...args) {
    if(timer) clearTimeout(timer);
    timer = setTimeout(()=>{
      fun.apply(this,args)
    },delay)
  }
}

//ES5写法
function debounce(fun,delay){
  var time = null;
  return function (){
    var _this = this;
    var _arguments = arguments;
    if(time) clearTimeout(time);
    time = setTimeout(function(){
      fun.apply(_this,_arguments);
    },delay)
  }
}
```

**防抖函数讲解：**
**知识点：**seTimeout会返回一个ID池，通过clearTimeout内传入ID值来取消timeout

1. 第一次执行，没有timer跳过，直接执行setTimeout
2. 第二次执行，拿到了setTimeout第一次返回的timer，在delay时间内被清空。然后继续执行setTimeout
3. 第三十次，重复到第29次timer清空，执行setTimeout。这时没有第31次了，直接执行seTimeout内的函数
4. 通过apply指向当前function，是因为fun是形参，不是属于Vue实例的方法，要用Vue实例调用就得改变this指向

**使用方法**  

1. 传入函数和延时const backFun = debounce(function,delay)。  
	//这里function如果是一个方法不要加()，如果是待执行操作则嵌套再箭头函数内传递。
2. 调用返回函数backFun()
3. args是如果在调用backFun()时，里面可以传参数，(...args)时es6数组解构，可以传多个参数

**示例：**

```js
//默认已经编写好上面的防抖函数
let delay = 1000;
const myFun = function(e){
  console.log('我是防抖函数');
  console.log(e);			//tace
} 
const backFun = debounce(myFun,delay);

const btnEle = document.querySelector('#btn');
btnEle.onclick = function () {		//这里模拟多次点击调用防抖函数
	backFun('tace');		
}
```

注意事项
1. 如果我们在多个页面中使用防抖函数，可把防抖函数封装成函数导出
2. 把使用方法封装在mixin中，并把debouce返回的函数用data的属性来保存，不要用const或let
	1. 好处一、混入返回的是新的变量，不会影响原来的页面
	2. 好处二、如果在函数中调用，data保存可以防止防抖函数不断销毁重新创建的问题

### 节流throttle

节流函数是用来限制一个函数被调用的频率。使用场景如游戏：飞机大战，我们按键速度再快，飞机都一直按照固定频率发射子弹。

节流函数与防抖函数实现方式不同，我们采用时间戳的方式来完成：

1. 使用`last`变量来记录上次的执行时间
2. 每次执行前，把当前时间保存到`now`，当`now-last > interval`则运行函数
3. 函数执行时再将`now`赋值给`last`

```js
function throttle(fn,interval){
  var last = 0;
  return function (){
    var _this = this;
    var _arguments = arguments;
    var now = new Date().getTime();
    if(now - last > interval){
      fn.apply(_this,_arguments);
      last = now;
    }
  }
}
```

在节流函数中，最后一次值是不会被执行的，因为没有到达最终的时间，也就是`now - last < interval`没有被满足

我们可以在最后一次执行的设置一个setTimeout定时器，如果不满足条件`now - last < interval`则执行定时器的内容

```js
function throttle(fn,interval){
  var last = 0;
  var time = null;
  return function (){
    var _this = this;
    var _arguments = arguments;
    var now = new Date().getTime();
    if(now - last > interval){
      if(time) {
        clearTimeout(time);
        time = null;		//为了函数下次运行，设置time = null
      }
      fn.apply(_this,_arguments);
      last = now;
    }else if(time === null){		//这里还可以增加一个参数，如函数开头传入 isShowLast   ,这里用逻辑并（&&）判断是否为true，开启执行最后一次函数
      time = setTimeout(function(){
        time = null;		//执行后也设置为null，为了下次运行
        fn.apply(_this,_arguments);
      },interval)		//这里如果要严谨一点，可以改成interval - （now - last）
    }
  }
}
```







### 混入mixin

关于混入，即重复代码复用，减少代码的重复使用。
mixin中可以看作类似Vue实例，data/methods/生命周期函数等都可以在mixin中复用

**继承和混入的区别**：**继承是继承原来的变量，混入是返回一个新的变量**

使用示例
1. 导出mixin
		
	```js
	//myMixin.js
	
	export let myMixin = {
	  data(){
			return{
			  message:'hello mixin'
	    }
	  },
	  methods:{
			testFun(){
				console.log(this.message)
			}
	  }
	};
	```
	
	
	
2. 在需求页导入
      `import {myMixin} from "./mixin";`
      	  

      ```js
        new Vue({
      	  //mixins和data同级，注意这里有复数s
      	  mixins:[myMixin],
      	  create:{
      		  this.testFun()
      	  }
        })
      ```

### Vue封装插件

在vue中如果我们导入组件还需要再特定位置引用和设置参数等，我们可以把一个组件封装成一个插件，
直接一行代码就可以使用封装的组件

这里以toast显示插件为例。自己如果有更好的代码也可以用如下步骤进行封装

组件Toast的代码就是一个methods方法，调用显示文字。
这里就不引入了，自己按需创建。只讲解插件封装过程

```js
//3.导入Toast组件
import Toast from "./Toast";
//1.创建index文件，封装对象并导出
const obj = {};
//2.当使用Vue.use时自动执行install，并且会把Vue传递进去
obj.install = function (Vue) {
  //4.创建组件构造器
  const toastConstructor = Vue.extend(Toast);
  //5.通过new的方式，根据组件构造器可以创建出来一个组件对象
  const toast = new toastConstructor();
  //6.将组件对象，手动挂载到某一个元素上
  toast.$mount(document.createElement('div'));
  //7.这里toast.$el对应的就是div
  document.body.appendChild(toast.$el);
  //8.把toast挂载到vue原型上，通过vue的$toast方法来调用toast对象
  Vue.prototype.$toast = toast;
};

export default obj
```

9. 在main.js中导入，并使用vue进行安装
		`import toast from './toast'`
	`Vue.use(toast)`	
	
10. 通过this.$toast来调用对象(组件)toast的方法把~
	
	`this.$toast.show();`



### Vue自定义指令

除了核心功能默认内置的指令 (`v-model` 和 `v-show`)

当你需要对普通 DOM 元素**进行底层操作**，这时候就会用到自定义指令

```js
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})

//注册一个局部组件指令
directives:{
  focus:{
     // 指令的定义
    inserted:function(el){
      el.focus()
    }
  }
}
```

#### 钩子函数

一个定义指令对象，除了inserted，还可以使用如下几个钩子函数：

- **bind**：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- **inserted**：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- **update**：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新。
- **componentUpdated**：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
- **unbind**：只调用一次，指令与元素解绑时调用。

```js
//还可以写多个钩子函数
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  },
  update:function(el){
    console.log('我被更新了')
  }
})
```



#### 钩子函数参数

> 除了 `el` 之外，其它参数都应该是只读的

指令钩子函数会被传入以下参数：

- `el`：指令所绑定的元素，可以用来直接操作 DOM 。

- `binding`：一个对象，包含以下属性：

  - `name`：指令名，不包括 v- 前缀。

  - `value`：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。

  - `oldValue`：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。  

  - `expression`：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。

  - `arg`：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。

  - `modifiers`：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。

- `vnode`：Vue 编译生成的虚拟节点。可以拿到当前指令所在的上下文 context，也就是可以拿到当前实例和方法

- `oldVnode`：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

**参数演示**

```html
<div id="app">
	<div v-demo:foo.a.b="message"></div>
</div>

<script>
// 注册一个全局自定义指令 `v-focus`
Vue.directive('demo', {
  bind: function(el, binding, vnode){
    console.log('bind...');
    var s = JSON.stringify
    el.innerHTML =
    'name: '       + s(binding.name) + '<br>' +					//name:'demo'
    'value: '      + s(binding.value) + '<br>' +				//value:'hello world'
    'expression: ' + s(binding.expression) + '<br>' +		//表达式 expression:'message'
    'argument: '   + s(binding.arg) + '<br>' +					//参数 argument:'foo'
    'modifiers: '  + s(binding.modifiers) + '<br>' +		//修饰符 modifiers:' { a: true, b: true }'
    'vnode keys: ' + Object.keys(vnode).join(', ')		  //vnode keys:'tag,data,children,text,elm等'
    }
})

var vm = new Vue({
  el:'#app',
  data: {
     message: 'hello world'
  }
});
</script>
```

**使用示例**

> 除了 `el` 之外，其它参数都是只读的，但指令的参数可以是动态的。

例如，在` v-mydirective:[argument]="value" `中，argument 参数可以根据组件实例数据进行更新！

```html
//注意，这里left和right用中括号包裹，通过data进行赋值
<div id="app">
  <div id="dynamicexample">
    <h3>在此部分内向下滚动 ↓</h3>
    <p v-pin:[left]="100">我固定在页面左侧100px处。</p>
    <p v-pin:[right]="100">我固定在页面右侧100px处。</p>
  </div>
</div>

<script>
Vue.directive('pin', {
  bind: function (el, binding, vnode) {
    el.style.position = 'fixed'		//设置定位
    var s = (binding.arg == 'left' ? 'left' : binding.arg)	//判断参数left or right
    el.style[s] = binding.value + 'px'	//赋值value。
    //这里el.style[s]用中括号不是点，是因为s是动态变量，不是固定参数
  }
})

var vm = new Vue({
  el:'#app',
  data:{
    right: 'right',
    left: 'left'
  }
});

</script>
```

**使用示例二**

> 使用自定义指令监听日历弹窗的点击消失

```html
<div id="app">
  <div v-click-out>
    <input type="text" >
    <div class="box" v-if='isShow'>
      <button>按钮</button>
    </div>
  </div>
</div>

<script>
  let vm = new Vue({
    el: '#app',
    data: {
      isShow: false,
    },
    methods:{
      focus(){
        this.isShow = true;
      },
      blur(){
        this.isShow = false;
      }
    },
    directives:{
      clickOut:{
        bind:function(el,bindings,vnode){
          el.fn = (e) =>{
            if(el.contains(e.target)){
              vnode.context['focus']()
            }else{
              vnode.context['blur']()
            }
          }
          document.addEventListener('click',el.fn);
        },
        unbind:function(el){
          document.removeEventListener('click',el.fn);
        }
      }
    }
  })
</script>
```



### 插件必备provide / inject

> `provide` 和 `inject` 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在其上下游关系成立的时间里始终生效。

**provide：**用来传递数据

**inject：**用来接收数据，也可以设置来源和默认值



**基本使用方法：**

```js
// 父级组件提供 'foo'
let app = new Vue({
  el: '#app',
  provide: {
    foo: 'isFoo'
  },
})

// 子组件注入 'foo'
Vue.component('testCont', {
  template: `<div>我是组件</div>`,
  inject: ['foo'],
  created() {
 		console.log(this.foo) // => "isFoo"
  },
  props:{	//作为props传递
    bar: {
      default () {
        return this.foo
      }
    }
  },
  data () {	//作为data数据
    return {
      bar: this.foo
    }
  }
})
```

**给inject设置默认值：**

```js
//子组件
inject: {
  foo: { default: 'foo' }		// => foo
}
```

**声明inject的来源：**

> 即来自不同的provide

```js
//父组件
let app = new Vue({
  el: '#app',
  provide: {
    bar:'isBar',
  },
})

//子组件
inject: {
  foo: {
    from: 'bar',		// => isBar
    default: 'foo'
  }
}
```



### 函数组件之jsx-render的使用

> 在组件的使用中，除了使用`.vue`文件，我们也可以使用render函数的形式来进行渲染`html`

#### render函数组件的用法

```vue
//app.vue
<template>
  <div class="app">
    <renderCom tagNum="1">我是h1</renderCom>
    <renderCom tagNum="2">我是h2</renderCom>
    <renderCom tagNum="3">我是h3</renderCom>
  </div>
</template>

<script>
  import renderCom from "./renderCom";

  export default {
    name: "app",
    components:{
      renderCom
    }
  }
</script>
```

```js
//renderCom.js
export default {
  props: {
    tagNum: {
      type: String
    }
  },
  //<h1>我是h1</h1>
  //<h2>我是h2</h2>
  //<h3>我是h3</h3>
  render(h) {
    let count  = 'test'+this.tagNum;
    //h('标签名',属性,内容)
    //更多render函数属性  https://cn.vuejs.org/v2/guide/render-function.html
    return h('h'+this.tagNum,{
      attrs: {
        name: count,
        id: '#' + count
      },
    },this.$slots.default)
  }
}
```

#### jsx的用法

> 当我们只想简单渲染几个标签时，使用render函数过面太过于复杂，我们可以使用`jsx`来进行渲染`html`

使用方法：

* <> 	代表html
* {}      代表js



```js
//renderCom.js
export default {
  props: {
    tagNum: {
      type: String
    }
  },
  methods:{
    getAttr(e){
      console.log(e.target.getAttribute('name'))
    }
  },
  render(h) {
    // let count  = 'test'+this.tagNum;
    // return h('h'+this.tagNum,{
    //   attrs: {
    //     name: count,
    //     id: '#' + count
    //   },
    // },this.$slots.default)
    // return h('h'+this.name,{},this.$slots.default)
    
    let tag = 'h' + this.tagNum;
    //  <> 代表html   {} 代表js
    // name='1' 传递字符串属性  name={1}  传递Number属性
    return <tag name={this.tagNum} onclick={this.getAttr}> 															{this.$slots.default} 
    			 </tag>
  }
}

```

[更多关于jsx的属性用法](https://github.com/vuejs/jsx#installation)



