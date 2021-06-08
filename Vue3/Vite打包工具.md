### Vite打包工具

Vite介绍：是一种新型前端构建工具，能够显著提升前端开发体验。

相比webpack拥有更快速的构建及打包，但是vite目前的版本是2.0，依然不是很稳定，插件支持也不够完善。

但仍然是一个很有前景的打包工具，未来vue-cli打算迁移到vite中去（目前仍是webpack）



Vite打包速度快的原因：

1. 特有的打包方式：现代的浏览器已经支持module模式，只是不支持解析ts、vue，等文件，那么我们只需要解析浏览器不支持的文件即可。

   在打包的时候，才将代码转化为ES5，让浏览器做支持。

2. vite缓存机制：vite在打包成功后，会在node_module/.vite文件中，对当前打包做一个缓存，这样我们在下一次打包的时候会极大的提升打包速度
3. vite使用ESbuild：ESbuild类似babel，但是比babel多了一些功能，比如代码压缩，Tree Shaking(没有引用的代码会自动帮我们删除),扩展插件，支持GO，JS的API等，最重要的特性就是超快的构建速度(go语言编写的ESbuild)，且不需要缓存



那么vite那么好我们什么时候可以开始使用呢？

答：当vue-cli开始使用vite的时候

### Vite基本使用

安装：`npm i vite -D` # 局部安装

```js
//项目名称 test-vite
test-vite/index.html		//   <script src="./src/main.js" type="module"></script>
test-vite/src/main.js		//	import {sum} from "./js/math";	console.log(sum(2,3))
test-vite/src/js/math.js	// export function sum(x, y) {return x + y}
```

启动项目：`npx vite`

vite会自动帮我们引用index.html，点开链接查看结果即可



### Vite使用CSS

* 普通css：

  直接引入即可

* less：

  安装less`npm install less -D`，并引入

* postcss的转换：

  安装postcss及插件，并配置postcss.config.js文件即可

  安装：`npm install postcss postcss-preset-env -D`

  ```js
  // postcss.config.js
  module.exports = {
    plugins: [
      require("postcss-preset-env")
    ]
  }
  ```

可以看到，vite并不需要使用任何loader及配置，使用起来非常的方便



### Vite使用TS

直接导入即可

vite实现原理：浏览器请求下来ts代码，使用connect服务器进行转发。获取ts编译后的（es6）代码，给浏览器直接解析



### Vite使用Vue

vite对vue提供第一优先级支持： 

* Vue 3 单文件组件支持：@vitejs/plugin-vue 

* Vue 3 JSX 支持：@vitejs/plugin-vue-jsx 

* Vue 2 支持：underfin/vite-plugin-vue2

安装vue3 和支持插件：`npm i vue@next @vitejs/plugin-vue -D`   

安装`.vue`文件解析模块：`npm i @vue/compiler-sfc -D`

配置文件：

```js
// vite.config.js
import vuePlugin from "@vitejs/plugin-vue";
module.exports = {
  plugins: [
    vuePlugin()
  ]
}
```



### Vite打包项目

打包：`npx vite build`

预览打包：`npx vite preview`

或者我们也可以直接在package.json中配置

```json
"scripts": {
  "serve": "vite",
  "build": "vite build",
  "preview": "vite preview"
},
```



### ESBuild解析

vite使用ESBuild解析

ESBuild的特点： 

* 超快的构建速度，并且不需要缓存； 

* 支持ES6和CommonJS的模块化； 

* 支持ES6的Tree Shaking； 

* 支持Go、JavaScript的API；

* 支持TypeScript、JSX等语法编译； 

* 支持SourceMap； 

* 支持代码压缩；

* 支持扩展其他插件；

构建速度对比：

* esbuild:0.37s

* webpack4:41.9s

* webpack5:54.5s



### Vite脚手架工

> 在开发中，我们不可能所有的项目都使用vite从零去搭建，比如一个react项目、Vue项目

```
npm install @vitejs/create-app -g  # 安装脚手架
create-app vite_demo  # 创建项目
```

官网使用方法：`npm init @vitejs/app`  (相比上面的步骤，省略了安装)

之后cd到目录安装并运行dev即可

