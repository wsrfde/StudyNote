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
    filename: 'bundle.js',
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

