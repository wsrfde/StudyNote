#  Vue组件化开发

**步骤**
1. Vue.extend创建组件构造器,并传入template自定义我们的模板
2. Vue.component注册组件，传入两个参数('注册组件的标签名',组件构造器)
3. 必须挂载到Vue实例中使用组件标签  

**注意：**
* 组件构造器必须在new Vue的前面创建，注册。不然无法使用
* template内的html代码要用根标签包裹，如div。因为子组件不能碎片化，vue只能有一个根目录碎片
* 组件的标签命名尽量不要用驼峰命名法，直接写小字母，具体看<a href='#props'>@v-bind中的驼峰标识</a> 

### 全局组件与局部组件

* 直接注册在JavaScript内的component是全局组件  
	
	```js
	let cpnC = {
	  template:`<div>我是组件</div>`
	}
	//Vue.component('标签名',定义的template)
	Vue.component('cpn',cpnC)
	```
	
* 在vue内注册的components是局部组件
	
	```js
	const app = new Vue({
	  el:'#app',
	  components:{
	    cpn:cpnC
	  }
	})
	```
	
	

### 父组件与子组件

* 有两个组件1、2，直接在创建组件2内写components，注册组件1。则我们称2为父组件，1为子组件  
_ps：子组件要写在前面_
* 子组件只能在父组件被使用时使用。无法单独使用子组件，除非子组件也在根组件内注册  
_ps：根组件指new Vue()_

### 组件化开发语法糖

全局组件：  
```html
<div id='app'>
	<cpn1></cpn1>
</div>

<script>
	Vue.component('cpn1',{
	  template:
		`<div>
			<h2>标题1</h2>
			<p>内容哈哈哈</p>
		 </div>`
	})
</script>
	
```
<br>

局部组件：  
```html
<div id='app'>
	<cpn2></cpn2>
</div>

<script>
	const app = new Vue({
	  el:'#app',
	  components:{
      cpn2:{
        template:
          `<div>
            <h2>标题2</h2>
            <p>内容哈哈哈</p>
           </div>`
      }
	  }
	})
</script>
```
<br>

### 组件模板的分离写法

> 建议用方法2，直接写一个标签即可通过template='#id'调用

1. 直接写在script中，注意类型是text/x-template  

    ```html
    <script type="text/x-template" id="Tem1">
    		<div>
    		  <h2>标题1</h2>
    		  <p>内容哈哈哈</p>
    		</div>
    </script>
    ```

    

2. 直接写一个template标签

	```html
	<template id="Tem2">
			<div>
			  <h2>标题2</h2>
			  <p>内容哈哈哈</p>
			</div>
	</template>
	```

组件的数据存放

* 组件中写mustache，也就是{{}}，不能访问vue中data的数据，
* 这个模板有属于自己的html模板，也应该有自己的data
* **组件中data要使用函数来调用，然后返回一个对象**

	```js
	data(){
		return {
			//在返回的对象中定义key和value
			title:'我是标题'
		}
	}
	```
	
	

### 为什么组件中的data是一个函数(面试必考)


* Vue让每个组件对象都返回一个新的对象，
	因为如果是同一个对象的，组件在多次使用后会相互影响。  

<br>
	

1. 每个组件返回一个新对象（对象之间互不影响）  

```javascript
	//在函数中每次运行的时候都会重新执行上下文return new obj，
	//内存地址不同所以是不同对象
	Vue.component('cpn',{
	  template:'#cpn',
	  data(){
		return {
		  name:'vicer',
		  age:18
		}
	  }
	})
```

<br>

2. 每个组件使用同一个对象（一般没有这样用的）

```javascript
	const obj = {
      name : 'vicer',
	  age:18
    }
	//这里在调用obj时每次调用都是相同的内存地址，
	//所以是同一个对象
	Vue.component('cpn',{
	  template:'#cpn',
	  data(){
		return obj
	  }
	})
```

<br>

### props父传子

> 把父组件的数据传递给子组件  

1. 属性声明：在**父实例**中传递的属性

		//app被挂载vue实例中，所以我这里是父组件
	```html
		<div id='app'>
			<cpn :被传递的属性='父组件属性'></cpn>  
		</div>
	```
	
	
	
2. 传递属性：在**子组件**中，通过props来配置被传递的属性及类型。(props和methods同级)  

3. 然后即可在子组件中使用父属性		
	
	```html
	<template>
			//我是子组件
			<div>{{被传递的属性}}</div>
	</template>
	```
	
	

注意事项：  
* 父传子前面不加v-bind时，传递的是固定的String属性，  ``` num='1'  ```  
* 父传子前面加v-bind时，传递是动态属性或Number等类型    ```:num='1' ```


<a name='props'></a>
#### v-bind中的驼峰标识  

* 动态绑定props子属性时字母小写
* 因为v-bind不支持大写字母，如果非要用驼峰中间加个  -  ，其他位置正常使用驼峰
* 但仍不建议中间加-，有可能出现不可预期的错误，最好小写字母

	```html
	<cpn :c-movies='movies'></cpn>  
	<template>
		<div>{{cMovies}}</div>
	</template>
	```
	

#### props两种配置类型

1. 数组类型（一般很少用到，基本用对象）
		props:['cmovies','cmessage']
	
2. 对象类型  
对象中type类型验证都包括  

		String/Number/Boolean/Array/Object/Date/Function/Symbol

	注意： 当type为Object 或Array时，default为对象类型

		default(){
			return {}  //或[]
		}
#### 对象类型用法示例

```javascript
	Vue.component('my-component',{
		props:{
			//基础的类型检查（`null`匹配任何类型）
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
				default:function(){
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
		}
	})
```

<br>

#### 自定义类型

当我们有自定义构造函数时，也可以自定义类型  

```javascript
	function Person(firstName,lastName){
		this.firstName = firstName
		this.lastName = lastName
	}
	Vue.component('log-post',{
		props:{
			author:Person
		}
	})
```

<br>

### $emit()子传父

> 子组件向父组件传递数据，要用到自定义事件。  

自定义事件的流程：  
1. 在子组件中，通过$emit()来触发事件。  
2. 在父组件当前实例中，通过v-on来监听子组件事件。  


	$emit('事件名',数据)
	v-on:'事件名'=调用函数名。

当$emit内传递数据，监听事件调用函数不写括号()，默认把当前数据传递给函数。  
ps：正常不写()调函数浏览器会把event事件传递过去，这里是把$emit内的数据传递过去

**注意监听事件写在自定义标签内**
```html
  <!-- 父组件 -->
  <div id="app">
    <cpn v-on:emitName="getName"></cpn>
  </div>

  <!-- 子组件 -->
  <template id="cpn">
    <div>
      <button @click="btnShow()">{{name}}</button>
    </div>
  </template>

  <script>
    //子组件
    const cpn = {
      template:'#cpn',
      data(){
        return {
          name:'vicer'
        }
      },
      methods:{
        btnShow(){
          this.$emit('emitName',this.name)
        }
      }
    }
    //父组件
    const app = new Vue({
      el:'#app',
      components:{
        cpn
      },
      methods:{
        getName(name){
          console.log('get'+name)
        }
      }
    })
  </script>
```

<br>

### (扩展)组件与动态绑定v-model 

**需求：**  
1. 父组件中的数据传到子组件中
2. 子组件input框中输入number，父组件的data同时发生改变
3. 子组件2是子组件1的100倍

**伪代码：**
1. 父传子，把父组件data传到子组件中显示
2. 子组件中尝试动态绑定v-model动态改变props
3. 绑定失败不支持直接修改props数据，报错建议通过data或computed修改
4. 通过子组件定义一个data，data=props中的数据
5. 通过v-model源代码调用函数，把动态数据绑定到子组件数据中
6. 把子组件数据发射到父组件
7. 通过父组件函数修改父组件的data
8. 接着完成需求3，让子组件2=子组件1*100，但发现bug，子组件2中的props数据没有更改
9. 把子组件2发射到父组件，通过第7步函数更改父组件，让父传子的props数据得到改变
10. 至此全部完成，撒花~

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <cpn :number1="num1" :number2="num2"  @shownum1="getNum1" @shownum2="getNum2"></cpn>
</div>

<template id="cpn">
  <div>
    <!--子组件1-->
    <h2>props:{{number1}}</h2>
    <h2>data:{{cnumber1}}</h2>
    <!--<input type="text" v-model="number1"> -->
    <!--      不能通过v-model直接修改-->
    <input type="text" :value="cnumber1" @input="changeNum1">
    <!--子组件2-->
    <h2>props:{{number2}}</h2>
    <h2>data:{{cnumber2}}</h2>
    <!--<input type="text" v-model="number2">-->
    <input type="text" :value="cnumber2" @input="changeNum2">
  </div>
</template>
<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el:'#app',
    data:{
      num1:0,
      num2:1
    },
    //子传父
    methods:{
      getNum1(event){
        this.num1 = parseInt(event)
      },
      getNum2(event){
        this.num2 = parseInt(event)
      }
    },
    components:{
      cpn: {
        template: '#cpn',
        props: {
          number1: Number,
          number2: Number,
        },
        //不能直接修改props，通过data或computed
        data(){
          return {
            cnumber1:this.number1,
            cnumber2:this.number2
          }
        },
        methods:{
          changeNum1(event){
            //动态绑定子组件data
            this.cnumber1 = event.target.value;
            //发射通过父传子改变父data
            this.$emit('shownum1',this.cnumber1)
            //完成需求3
            this.cnumber2 = this.cnumber1*100
            //完成子组件2props不能动态修改的bug
            this.$emit('shownum2',this.cnumber2)
          },
          changeNum2(event){
            this.cnumber2 = event.target.value;
            this.$emit('shownum2',this.cnumber2)
            this.cnumber1 = this.cnumber2 / 100
            this.$emit('shownum1',this.cnumber1)
          },
        }
      }
    }
  })
</script>
</body>
</html>
```


### 父子组件访问方式

#### $children

* 类似与js操作Dom中的children，实际开发用的很少
* 通过$children访问子组件是一个数组类型，访问其中的子组件必须通过索引值。
* 我们想明确获取其中一个特定的组件，可以使用$refs  

#### $refs (使用频率高)

调用时默认是一个空对象，只有在标签中加ref='属性名'才会显示，这样就可以具体到某一个标签

```html
<cpn></cpn>
<cpn ref="aaa"></cpn>

showChild(){
	return this.$refs.aaa
}
```
注意：  
* ref如果绑定在组件中，那么获取到的是组件对象
* ref如果绑定到普通元素中，那么获取到是元素对象

#### $parent

* 类似js操作Dom中的parent，实际开发用的很少
* 子组件应该尽量避免直接访问父组件的数据，因为这样耦合度太高了。

#### $root

直接访问根组件，也就是vue实例

#### $el（访问组件）

所有组件都有一个属性$el，用于获取组件中的元素

<br>

### 监听组件事件

正常我们只能监听标签的事件，
如果想要监组件点击事件，需要在v-on事件后添加一个修饰符  
之前在vue基础笔记中写过

**监听组件根元素的原生事件**。
`.native `

用法示例  

```js
@click.native = 'backClick'
```

### sync与update同步更新数据

> 当我们从父向子传递数据，并且需要父子的数据同步时，可以进行如下操作

```html
//father.vue
<div>{{count}}</div>
<!--    <son :count="count" @update:count="newVal => count = newVal"></son> -->
<son :count.sync="count"></son>  <!-- sync等于如上操作 -->

<script>
	data(){
  	return {
    	count:0
  	}
	}  
</script>
```

```html
//son.vue
<button @click="changeCount">更改count</button>

<script>
	methods:{
    changeCount(){
      this.$emit('update:count',100)
    }
  }
</script>
```

