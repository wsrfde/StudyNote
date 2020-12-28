### 初次邂逅weex



1、安装weex

```bash
npm install weex-toolkit -g
```
2、检查是否安装成功

​	`weex -v`

3、创建项目

```bash
# 从官方模板中创建项目
$ weex create my-project
```

也可以自己指定模板 

weex create 模板名/项目名

```bash
weex create '<template-name>#<branch-name>' <project-name>
# 例如
weex create weex-templates/webpack#v1.0 my-project
```

4、编译页面

可以进行全部编译，也支持单个页面编译

例如：

```bash
$ weex compile src build
```

或

```bash
$ weex compile src/index.vue build
```

**代码环境检查**

```bash
weex doctor
```



### 扩展Web组件

#### 	扩展内置组件

1. 使用.vue创建组件  ，如// sidebar.vue

2. 在全局中注册
   ```js
   import Vue from 'vue'
   import weex from 'weex-vue-render'
   import Sidebar from './path/to/sidebar.vue'
   weex.init(Vue)
   // 全局注册 sidebar 组件
   weex.registerComponent('sidebar', Sidebar)
   // 或者使用 Vue.component
   // Vue.component('sidebar', Sidebar)
   ```

3. 使用组件<sidebar>



#### 	扩展内置模块

​		引入了 `weex-vue-render` 这个库之后，在全局能获取到 `weex` 这个变量，其中提供了 `registerModule` 方法可以扩展内置模块

**API格式**

```
registerModule
```

- `name`: {string} 必选，模块名称
- `define`: {object} 必选，模块的定义
- `meta`: {object} 可选，模块元数据。将非 iterable 的属性或方法注册到模块对象里，才需要用到这个参数，将 `{ registerType: 'assignment' }` 作为 meta 参数传入即可

**扩展模块示例**

下边的代码注册了一个名为 `guide` 的模块：

```js
weex.registerModule('guide', {
  greeting () {
    console.log('Hello, nice to meet you. I am your guide.')
  },
  farewell () {
    console.log('Goodbye, I am always at your service.')
  }
})
```

在 `weex` 上提供了 `require` 方法用于获取已注册的模块，直接传递模块名即可：

```js
// 获取模块
const guide = weex.requireModule('guide')

// 可以直接调用模块中的方法
guide.greeting()
guide.farewell()
```

### 模块和组件检测

我们可以使用以下方法

检测**组件**是否可用

```js
weex.supports('@component/slider') // true
weex.supports('@component/my-tab') // false
```

检测**模块**是否可用

```js
weex.supports('@module/stream')  // true
weex.supports('@module/abcdef')  // false
```

### 页面降级

“降级” 通常是指以普通 Web 页面的模式渲染 Weex 的页面。

如果你使用了较高版本中的特性，但是又无法升级已经存在的低版本 App，就可以使用“降级”的方式在低版本 App 中以普通 Web 页面的模式来渲染



1.首先安装 `@weex-project/downgrade` 模块，然后在页面代码中引入，调用其中的接口即可触发降级。

```js
import downgrade from '@weex-project/downgrade'
```

2.1 强制降级。调用该接口可以无条件立即降级。

```js
downgrade.force()
```

2.2 使用`check(options)`检查环境信息是否满足 `options` 的描述，返回校验结果，**并不会触发降级**。使用方法看[官网]([https://weex.apache.org/zh/guide/advanced/downgrade.html#%E8%BE%85%E5%8A%A9%E5%B7%A5%E5%85%B7](https://weex.apache.org/zh/guide/advanced/downgrade.html#辅助工具))

2.3 使用`condition(options)`检查环境信息是否满足 `options` 的描述，**不满足条件则触发降级**。使用方法看[官网]([https://weex.apache.org/zh/guide/advanced/downgrade.html#%E8%BE%85%E5%8A%A9%E5%B7%A5%E5%85%B7](https://weex.apache.org/zh/guide/advanced/downgrade.html#辅助工具))



### weex中的vue-router

**路由模式**

vue-router 提供了三种运行模式：

- `hash`: 使用 URL hash 值来作路由。默认模式。
- `history`: 依赖 HTML5 History API 和服务器配置。查看 [HTML5 History 模式](https://router.vuejs.org/zh-cn/essentials/history-mode.html)。
- `abstract`: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。

配置方法是在定义路由时，传递 `mode` 属性：

```js
new Router({
  mode: 'abstract',
  // ...
})
```

从三种模式的介绍中也可以看出来，Weex 环境中只支持使用 abstract 模式。不过，vue-router 自身会对环境做校验，**如果发现没有浏览器的 API，vue-router 会自动强制进入 abstract 模式**，所以在使用时只要不写 `mode` 配置即可。默认 vue-router 会在浏览器环境中使用 hash 模式，在移动端原生环境中使用 abstract 模式。

**编程式导航**

vue-router 中使用 `` 创建导航链接，不过在其中使用了基于 DOM 事件的一些特性，在 Weex 原生环境中并不能很好的工作。在 Weex 中，你必须使用[编程式导航](https://router.vuejs.org/zh-cn/essentials/navigation.html)来编写页面跳转逻辑。

编程式导航其实就是通过主动调用 router 实例上的 `push` 方法实现跳转。

使用 `<router-link>`的代码示例：

```html
<!-- 只能在 Web 中使用，Native 环境不支持！ -->
<template>
  <div>
    <router-link to="profile">
      <text>Profile</text>
    </router-link>
  </div>
</template>
```

在 Weex 中，需要写成这个样子：

```html
<template>
  <div>
    <text @click="jump">Profile</text>
  </div>
</template>

<script>
  import router from './path/to/router'
  export default {
    methods: {
      jump () {
        router.push('profile')
      }
    }
  }
</script>
```



### weex的网络请求

直接上代码示例

```js
//导入网络请求模块
const stream = weex.requireModule('stream')
//在生命周期函数中加载数据
created() {
    var api = '我是URL地址';
    this.getData(api, (res) => {
        this.requestData = JSON.parse(res.data).data;
        console.log(this.requestData)
    })
},
//定义请求方法
methods:{
    getData(url, callback) {
        return stream.fetch({
            method: 'GET',
            url: url
        }, callback)
    },
}
```



### 使用weex导入打包好的页面

#### 一、使用a标签导入

1. 把写好的组件进行打包
2. 找到打包好的文件名，一般和组件名相同
3. 新建文件并把打包好的代码复制进去，在本地ip下测试是否能打开，也可以直接运行npm start，端口名后面加上文件名，如 http://192.168.2.110:8081/loadTest.js
4. 然后在a标签的href中增加刚才的链接（也就是上方的链接）。
5. 在手机端测试。（在web端打开是代码，手机端则是组件中的页面）

#### 二、使用web标签导入

也可以使用web导入。不过web标签类似与html的iframe的功能，加载一个网页框架

如果需要控制网页的前进和后退和刷新，可以搭配webview组件使用



### weex的导航按钮
使用`navigator`进行网页跳转操作，具体看文档



### weex的注意事项

1. 只能使用flex写法
2. 不能用合并式写法，如border:1px solid #ccc;  这个不支持
3. 只能用px单位
4. class不能是组合选择器
5. 最外层必须是div
6. 直接使用lines就可以使文本在一行并增加三个点
1. 单标签记得要闭合   如`<input />`
2. input框不能输入，加入属性 `:autofocus="true"`
9. 元素的百分百像素值  width=750px=100%; height=1250px=100%;
10. 没有ul和li标签，被换成了list和cell   。
11. 还可以设置下拉加载属性。一般是事件loadmore 配合loadmoreoffset使用   。或者使用weex的组件loading
12. 上拉刷新属性：refresh、用法和loading差不多。refresh事件基本够用，pullingdown事件则细致一点



### weex的样式

1. flex-basis、flex-flow不能使用

2. css文档中未说明的属性，皆不能使用

   



