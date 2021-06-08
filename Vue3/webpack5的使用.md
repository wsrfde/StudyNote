## webpack5的使用

### 安装

webpack需要安装部分为：`webpack`、`webpack-cli`

* `webpack`  需要使用这个打包工具
* `webpack-cli` 则方便我们直接在命令行中调用webpack

```
npm install webpack webpack-cli –g # 全局安装
npm install webpack webpack-cli –D # 局部安装
# 如果不能一起安装就分开安装
```

真实项目中都是每个项目本地安装一个webpack，因为每个项目的webpack版本不同，可能会出现不兼容的情况

### 使用示例

首先创建两个js文件，在文件中使用commonJS导出并使用

```js
// src/index.js
const {sum} = require('./js/sum')

console.log('结果'+sum(2,3))
```

```js
// src/js/index.js
const sum = function(x,y){
  return x + y
}
module.exports = {
  sum
}
```

然后**cd到项目根目录**，运行`webpack`，webpack会自动帮我们打包`src/index.js`引用的文件

### 基本使用

```
npm init -y   # -y 的意思是快速创建package
npm i webpack webpack-cli -D # 本地安装webpack，-D为开发时依赖
```

打包注意：

直接使用`webpack`命令的话是使用的全局的webpack，如果想使用本地有两种方法

```
# 方法一
# 使用npx，会调用本地的包
npx webpack
```

```
# 方法二
# 在package.json中增加脚本命令
"scripts": {
    "bulid": "webpack"   # 会自动调用本地的包
},
# 然后在命令行中运行一下命令
npm run build
```

### webpack配置

在根目录中新键`webpack.config.js`文件，当打包的时候webpack会默认去该文件中查找配置

```js
// webpack.config.js

const path = require('path');

module.exports = {
  entry: './src/index.js',  // 打包入口，默认为 ./src/index.js
  output: {									// 打包出口
    filename: 'js/bundle.js',
    path: path.resolve(__dirname, 'dist')	// 当前文件的路径拼接上dist
  }
};
```



### loader

> 在开发中，我们不仅要处理js文件，还要将cs，typescript，图片，es6等转换成浏览器能够处理的文件
> ，只有webpack是做不到那么多的，所以需要安装扩展loader

![loader](.\img\webpack\loader.png)

**重要提示：webpack官网的中文文档不是最新的，如果要查看最新文档，请查看英文文档！**

**重要提示：webpack官网的中文文档不是最新的，如果要查看最新文档，请查看英文文档！**

**重要提示：webpack官网的中文文档不是最新的，如果要查看最新文档，请查看英文文档！**

使用方法：

* 通过npm安装需要使用的loader，如`npm i css-loader style-loader -D`
* 在webpack.config.js中的`module:{}`中进行配置`rules`

#### css-loader使用

* 使用css-loader时，只是将css文件加载，还需要再安装style-loader进行渲染dom树

* module中的use规则，读取顺序是从右向左，所以应该先写style-loader，再写css-loader，

  如`[ 'style-loader', 'css-loader' ]`

#### Less-loader使用

安装Less和Less-loader`npm install less less-loader -D`

```js
rules:[
  {
    test: /\.(less|css)$/,	// 同时使用less和css
    use: [
      "style-loader",
      "css-loader",
      "less-loader"
    ]
  },
]
```

#### PostCSS的使用

> pPostCSS可以帮助我们进行一些CSS的转换和适配，比如自动添加浏览器前缀、css样式的重置，是一个通过JavaScript来转换样式的工具；

安装postcss和postcss处理插件：`npm install PostCss postcss-preset-env -D`

安装对应的postcss-loader：`npm install postcss-loader -D`

如果想直接在终端使用的话可以安装对应得cli，没有这个需求得话无需安装 ` npm i postcss-cli -D`

postcss-preset-env插件作用：

* 它可以帮助我们将一些现代的CSS特性，转成大多数浏览器认识的CSS，并且会根据目标浏览器或者运行时环境 添加所需的polyfill；
* 也包括会自动帮助我们添加autoprefixer（所以相当于已经内置了autoprefixer）；

##### PostCSS使用方法一

> 直接在webpack.config.js中配置postcss及插件

```js
// webpack.config.js
rules:[
  {
    test: /\.css$/, //正则表达式
    use: [
      "style-loader",
      "css-loader",
      {
        loader: "postcss-loader",
        options: {
          postcssOptions: {
            plugins: [
              require("postcss-preset-env")
            ]
          }
        }
      }
    ]
  },
]
```

##### PostCSS使用方法二

> 如果配置文件过多，可以把postcss单独抽离出来配置，首先我们创建一个文件名为`postcss.config.js`，与webpack.config.js一样放在根目录

```js
// postcss.config.js
module.exports = {
  plugins: [
    require("postcss-preset-env")
  ]
}
```

webpack.config.js该为以下格式，postcss会自动寻找目录下postcss.config.js文件

```js
// webpack.config.js
rules:[
  {
    test: /\.css$/, //正则表达式
    use: [
      "style-loader",
      "css-loader",
      "postcss-loader",
    ]
  },
]
```

#### asset module type

> webpack5中已不再使用raw-loader 、url-loader、file-loader，使用asset module type代替

**无需进行安装即可使用**

资源模块类型：

* `asset/resource` 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现； 

* `asset/inline` 导出一个资源的 data URI。之前通过使用 url-loader 实现；

* `asset/source` 导出资源的源代码。之前通过使用 raw-loader 实现； （不常用）

* `asset` 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资源体 积限制实现



##### 加载图片

> 开发中我们加载图片一般常用的是`asset`类型

```js
rules:[
  {
    test: /\.(png|jpe?g|gif|svg)$/i,
  	type: "asset",
    generator:{
      // 我们还可以使用placeholder进行命名文件  
      filename: "img/[name]_[hash:8][ext]"	//wenpack5中ext自带了前面的小数点
    },
    parser:{
      dataUrlCondition:{
        maxSize: 10 * 1024	// 把10kb以下的图片进行生成base64，通过bundle.js一起传输过来。大于10kb则引入文件
      }
    }
  }
]
```

##### 加载字体

```js
rules：[
  {
    test: /\.(woff2?|eot|ttf)$/i,
  	type: "asset/resource",
    generator:{
      filename: "font/[name]_[hash:8][ext]"	//wenpack5中ext自带了前面的小数点
    }
  }
]
```

**最常用的placeholder：**

* [ext]： 处理文件的扩展名；
* [name]：处理文件的名称； 
* [hash]：文件的内容，使用MD4的散列函数处理，生成的一个128位的hash值（32个十六进制）；
* [contentHash]：在file-loader中和[hash]结果是一致的（在webpack的一些其他地方不一样，后面会讲到）；
* [hash:]：截图hash的长度，默认32个字符太长了； 
* [path]：文件相对于webpack配置文件的路径



### plugin插件

> 用于执行更加广泛的任务，比如打包优化、资源管理、环境变量注入等

#### CleanWebpackPlugin

功能：打包前删除dist文件夹

安装：`npm install clean-webpack-plugin -D`

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
  plugins: [
    new CleanWebpackPlugin()
  ]
}
```

#### HtmlWebpackPlugin

功能：帮助我们在dist文件夹中生成index.html文件

安装：`npm install html-webpack-plugin -D`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  plugins: [
    new HtmlWebpackPlugin()
  ]
}
```

#### DefinePlugin

功能：定义**自定义模板(index.html)**内的**EJS数据变量**，如语法`<% 变量 %>`

安装：无需安装，webpack已经内置

```js
const {DefinePlugin} = require('webpack')

const BASE_NAME = 'vicer'
module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      // 如果不想用插件自带的模板，我们还可以进行自定义，自定义的数据需要搭配 DefinePlugin插件来使用
      template: './public/index.html',
      title: 'webpack案例'	// <%= htmlWebpackPlugin.options.title %>
    }),
    new DefinePlugin({
      BASE_NAME: 'BASE_NAME'  // 会自动找到上方定义的变量(vicer)，如果想直接使用字符串就在里面嵌套双引号	
      BASE_URL: '"./"'	// <%= BASE_URL %>
    })
  ]
}
```

```html
<!-- ./public/index.html -->

<!--<!DOCTYPE html>-->
<!--<html lang="">-->
<!--  <head>-->
<!--    <meta charset="utf-8">-->
<!--    <meta http-equiv="X-UA-Compatible" content="IE=edge">-->
<!--    <meta name="viewport" content="width=device-width,initial-scale=1.0">-->
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
<!--  </head>-->
<!--  <body>-->
<!--    <noscript>-->
<!--      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>-->
<!--    </noscript>-->
<!--    <div id="app"></div>-->
<!--    &lt;!&ndash; built files will be auto injected &ndash;&gt;-->
<!--  </body>-->
<!--</html>-->
```

#### CopyWebpackPlugin

功能：将public的文件夹中的文件（index.html除外）复制到dist文件夹中

安装：`npm install copy-webpack-plugin -D`

```js
const CopyWebpackPlugin = require('copy-webpack-plugin');

module.exports = {
  plugins: [
		new CopyWebpackPlugin({
      patterns: [
        {
          from: "public",
          to: "./",	// 可以省略，会自动找到要打包的文件夹
          globOptions: { 
            ignore: [ // 需要忽略的文件
              "**/index.html"	// 不需要复制，因为我们已经通过HtmlWebpackPlugin生成
            ]
          }
        }
      ]
    })
  ]
}
```

### Mode配置

> Mode配置选项，可以告知webpack使用响应模式的内置优化，不同的选项代表一个不同的配置集合

可选值：`'none' | 'development' | 'production'` (默认：production)

| 选项          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `development` | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `development`. 为模块和 chunk 启用有效的名。 |
| `production`  | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `production`。为模块和 chunk 启用确定性的混淆名称，`FlagDependencyUsagePlugin`，`FlagIncludedChunksPlugin`，`ModuleConcatenationPlugin`，`NoEmitOnErrorsPlugin` 和 `TerserPlugin` 。 |
| `none`        | 不使用任何默认优化选项                                       |

### Devtool

> 控制是否生成，以及如何生成 source map。

source map的作用：生成资源地图，在打包之后的代码中可以正确的找到报错代码的位置

`devloop:source-map`（默认为：`eval`）

注意：生成`source-map`会占用一部分的空间及较慢的打包速度，生产模式中需要注释掉此代码

### Babel

> webpack打包的js文件中还存在es6语法，由于部分浏览器不支持es6，所以我们需要帮它转换成es5

安装：	`npm i  babel-loader @babel/core -D`

**当前使用版本：webpack 5.x | babel-loader 8.x | babel 7.x**

注意：这样直接使用的话是转义是不用生效的，还需要安装各种插件，以达到我们想要的效果

插件：

把箭头函数转为普通函数：`plugin-transform-arrow-functions`

把let/cont转为var：`plugin-transform-block-scoping`

但这样使用babel一个个安装插件太麻烦，所以babel直接帮我们配置好了预设，根据预设来加载对应插件列表

`npm install @babel/preset-env -D`

**配置方法一：**

```js
// webpack.config.js
rules:[
  {
  test: /\.js$/,
    // 由于转义时会把所有引用的js一起转义，而node_modules不需要转义，所以需要进行排除处理
    exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
          options: {
         // plugins: [
         //   "@babel/plugin-transform-arrow-functions",
         //   "@babel/plugin-transform-block-scoping",
         // ]
            presets: ['@babel/preset-env']
          }
      }
	}
]
```

**配置方法二：**

```js
// webpack.config.js
rules:[
  {
     test: /\.js$/,
     loader: "babel-loader"
  }
]
```

```js
// babel.config.js
module.exports = {
  presets: [
    "@babel/preset-env"
  ]
}
```

### Vue3的打包

安装vue3：`npm i vue@next`

#### vue各个版本区别

* `vue(.runtime).global(.prod).js`

  通过浏览器中的 `<script src="...">` 直接使用；  

  我们之前通过CDN引入和下载的Vue版本就是这个版本；  

  会暴露一个全局的Vue来使用；  

* `vue(.runtime).esm-browser(.prod).js`

  用于通过原生 ES 模块导入使用 (在浏览器中通过` <script type="module">` 来使用)。  

* `vue(.runtime).esm-bundler.js  `

  用于 webpack，rollup 和 parcel 等构建工具；  

  构建工具中默认是vue.runtime.esm-bundler.js； 

  如果我们需要解析模板template，那么需要手动指定vue.esm-bundler.js；  

* `vue.cjs(.prod).js`  

  服务器端渲染使用；  

  通过require()在Node.js中使用；

#### 运行时编译和仅运行时

##### Vue开发的三种方式

* **render函数**的方式，使用h函数来编写渲染的内容

* **template模板**的方式（在createApp中写template）
* 通过**.vue文件**中的template来编写模板

##### 三种方式的处理方法

* **render函数**的h函数可以直接返回一个虚拟节点

* **template模板**要通过源码中一部分代码来进行编译

* **.vue文件**中的template可以通过在**vue-loader**对其进行编译和处理



**总结：**

**runtime**版本的代码量更少，但是不支持对**template模板**的编译  （默认版本）

**非runtime**版本的代码量更多，支持编译template

所以在webpack中我们不想引用更大的版本包，还需要编译.vue文件，就需要用到vue-loader

#### Vue-loader的使用

安装支持Vue3的loader：`npm i vue-loader@next -D` 

安装对`.vue`的解析：`npm i @vue/compiler-sfc -D`

```js
// vue 除了配置loader还需要搭配loader中的plugin来进行使用

const { VueLoaderPlugin } = require('vue-loader');

module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
}
```

#### 全局标识符的配置

我们会发现控制台还有另外的一个警告：

`You are running the esm-bundler build of Vue. It is recommended to configure your bundler to explicitly replace feature flag globals with boolean literals to get proper tree-shaking in the final bundle. See http://link.vuejs.org/feature-flags for more details.`

打开[链接](http:/link.vuejs.org/feature-flags)中的文档，我们可以看到vue3中默认帮我们配置了两个选项

* `__VUE_OPTIONS_API__` 是否支持vue2的api，默认为true
* `__VUE_PROD_DEVTOOLS__`是否可以在生产模式中使用devtools，默认为false

虽然vue3默认帮我们做了如下配置，但作者还是强烈建议我们进行手动配置

```js
// webpack.config.js

// 使用DefinePlugin插件，进行全局变量的配置
new DefinePlugin({
  BASE_URL: "'./'",
  __VUE_OPTIONS_API__: true,
  __VUE_PROD_DEVTOOLS__: false
}),
```



### webpack热更新

> 每次我们编写代码后还需要重新编译，非常的麻烦，可以使用以下方法，当代码更新时，自动进行编译代码来进行实时调试

#### 使用watch

> 只要有一个发生了更新，那么代码将被重新编译

```json
// package.json
"scripts": {
 "watch": "webpack --watch"
}
```

但是这个方法有一个缺点，只能重新编译代码，不能进行刷新页面

#### 使用webpack-dev-server

> 监听文件变化，并实时重新加载（live reloading）

安装：`npm install webpack-dev-server -D`

使用：

```json
// package.json
"scripts": {
 "serve": "webpack serve"  // 通过webpack-cli启动
}
```

然后运行`npm run serve` 即可启动。

**疑问： 为什么run serve的时候没有对文件进行实时打包然后再实时更新？**

原理：因为webpack开启本地服务是启用了node的一个express服务器来进行开启，webpack的打包好的内容被放在了内存中，express直接去内存中去取即可。

如果打包好了文件放在了dist文件夹中，express还需要去读取dist文件夹，然后再放在内存中引用，多了一个读取步骤，所以为了提高开发效率直接放在了内存中

##### webpack-dev-server配置：contentBase

> contentBase的作用：当webpack打包引用目录找不到资源时，就会来contentBase配置的文件夹下来寻找

```js
module.exports = {
  devServer:{
		contentBase: path.join(__dirname, 'public'),	// => ./public
  }
}
```

 **contentBase与CopyWebpackPlugin的区别**

* contentBase：是直接引用文件夹的资源
* CopyWebpackPlugin：把资源进行复制到dist文件夹中再进行引用

总结：

contentBase更适合在**开发中使用**，因为每次更改时还要进行复制文件，浪费时间

CopyWebpackPlugin更适合**在生产中使用**，生产的时候还是要把资源打包进dist文件中去的

##### webpack-dev-server配置：HMR

> 模块热替换HMR（全称Hot Module Replacement）。模块热替换是指在：应用程序运行过程中，替换、添加、删除模块，而无需重新刷新整个页面

优点：不重新加载整个页面，这样可以保留某些应用程序的状态不丢失

警告：**HMR 绝对不能用在生产环境**

不开启HMR的情况下，当我们修改了源代码之后，整个页面会自动刷新，使用的是live reloading；

```js
module.exports = {
  target: "web",	// 使用HMR的时候要指定哪个模块，如（node/web），不然会有一些问题 
  devServer:{
		hot: true
  }
}
```

当我们配置好`hot:true`的时候，**会自动进行`.vue`文件的模块热替换**，如果想热替换其他文件，需配置以下代码

**单个文件模块热替换**

```js
import './js/elemet'

if(module.hot){
  module.hot.accept('./js/elemet',()=>{
    console.log('elemet模块更新了')
  })
}
```

##### webpack-dev-server配置：host、port、open、compress

默认devServer访问的是本地IP，默认是localhost本地域名，会被解析成127.0.0.1。

当我们希望在同一网段下的主机访问我么的网页时，可以设置为自己的IPv4 地址，并配置默认端口号80

```js
module.exports = {
  devServer: {
    host: '192.168.1.109',	//设置主机地址
    port: 80,								//设置端口号
    open:true,							//是否打开浏览器，默认false
		compress:true						//为静态文件开启gzip,默认为false（没必要开启，本地访问的速度已经够快，开启gzip后浏览器还要解压）
  },
}
```

##### webpack-dev-server配置：proxy

> 当我们开发过程中出现跨域问题时，除了后端解决之外前端也可以解决（但上线后必须由后端解决）

我们可以把**请求先发送到代理服务器，然后由代理服务器发送请求和数据**，以解决跨域问题



**模拟跨域场景：端口号不同**

本地启动地址：`http://localhost:80`

API请求地址：`http://localhost:8888/list`

```js
module.exports = {
  devServer: {
		proxy: {
  		// "/api":"http://localhost:8888",   
      //这样直接设置是有缺点的，当我们使用axios请求“/api/list”数据时，会把API地址改为http://localhost:8888/api/list，但我们上面的请求没有‘api’字段，所以需要重写path地址
      "/api": {
        target: "http://localhost:8888",
        pathRewrite: {
          "^/api": ""
        },
        secure: false,		//默认情况下不接收转发到https的服务器上，如果希望则设置为false
        changeOrigin: true,	//如果后端设置了防止爬虫（即header上的host地址与本地相同才能请求），我们想要避免后端这个设置，把changeOrigin设置为true即可
        historyApiFallback:true // 当我们在vue中使用history模式，路由跳转后刷新页面会出现404，为了避免这个行为，我们可以设置historyApiFallback为true，当发生404时，自动返回index.html
      }
    }
  },
}
```

```js
//把'/api'替换为http://localhost:8888，这样我们请求则为正确的请求地址：http://localhost:8888/list
axios.get("/api/list").then(res => {  
  console.log(res);
})
```

### resolve模块解析

> 在webpack中我们引入模块，其实来自自己的代码和第三方库，但webpack怎么知道我们引用的是哪个模块呢，答案就是通过resolv模块解析

#### 路径解析

* 绝对路径：已经获得该文件的绝对路径，无需解析
* 相对路径：通过import/require的文件所在目录，定义为上下文目录，根据import/require给出的相对路径，拼接上下文形成绝对路径
* 模块路径：在`resolve.modules`中指定模块检索目录，默认值是` ['node_modules']`

#### 文件扩展名

> webpack会判断当前引用路径是文件or文件夹

* 文件：是否有扩展名，没有则根据`resolve.extensions`进行填充

  ```js
  module.exports = {
      resolve: {
        extensions:[".js", ".json", ".mjs", ".vue", ".ts", ".jsx", ".tsx"],// 默认只包括数组前3个值
      }
  }
  ```

* 文件夹：在文件夹中根据`resolve.mainFiles`查找引用文件，默认是`['index']`，再根据`resolve.extensions`来解析文件扩展名

#### 别名配置

> 当我们项目的目录结构比较深的时候，可以使用别名来引用资源

```js
module.exports = {
    resolve: {
      alias: {
        "@": path.resolve(__dirname, "./src"),
        "js": path.resolve(__dirname, "./src/js")
      }
    }
}
```

### 配置分离

> 配置分离非常的简单，当我们需要根据不同的环境选择对应的配置的时候，可以进行如下操作

```json
// package.json
"scripts": {
  "build": "webpack --config ./config/webpack.prod.config.js",
  "serve": "webpack serve --config ./config/webpack.dev.config.js"
},
```

如果想把公共配置抽理出到单独的文件，并在其他文件引用，需要用到`webpack-merge`这个插件

```js
// config/webpack.dev.config.js   or   config/webpack.prod.config.js
const { merge } = require('webpack-merge');
const commonConfig = require('./webpack.comm.config');

module.exports = merge(commonConfig, {
  //开发或生产时的配置
}
```

注意：如果文件放在config文件夹下时，引用路径要多向上查找一层，即`../`，同时改变webpack的上下文

```js
module.exports = {
  context:path.resolve(__dirname,"./"),
  entry:"../src/index.js"
}
```



 
