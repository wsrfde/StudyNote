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

安装PostCss和postcss-cli及postcss-loader：`npm install postcss postcss-cli postcss-loader -D`

因为postcss需要有对应的插件才会起效果，所以我们需要安装它的plugin：`npm install postcss-preset-env -D`

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

功能：将public的文件夹中其他文件复制到dist文件夹中

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

