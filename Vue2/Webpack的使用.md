# webpack3的使用

webpack中的代码都是以模块化来进行编写  



## 和gulp的区别

* gulp实现自动化压缩js，css，less代码等，
* webpack是把项目的js，css，less等文件打包成一个或多个，主要用于模块化方案

全局安装  

	npm install webpack@3.6.0 -g
局部安装  

	npm install webpack@3.6.0 --save-dev
	
	//如果想要安装3系列最高版本，只需要后面加个3即可
	npm install webpack@3 --save-dev

`save-dev`是开发时依赖，项目打包后不需要继续使用的。



## **使用方法**

运行如下命令，让webpack自动帮你打包main.js，它会自动帮你处理好各种依赖  

	//3.x用法
	webpack ./src/main.js ./dist/bundle.js
	//4.0用法
	webpack ./src/main.js -o ./dist/bundle.js

**手动配置package**

	npm init

**package全局与本地命令**  
* 当直接在终端运行命令时在全局环境中查找
* 如果给package中script设置命令，则优先在本地查找

**配置webpack.config.js**

1. 安装path，拼接当前目录的绝对路径

   这里path不需要安装，是init时，node包中自带的模块
   
2. 配置文件

```javascript
	const path = require('path')

	module.exports = {
	  entry : './src/main.js',
	  output : {
		//resolve拼接当前目录，绝对路径
		path:path.resolve(__dirname,'dist'),
		filename:'bundle.js'
	  }
	}
```

**注意：**  

* 直接在终端运行webpack是在全局环境中运行，而全局安装和项目中版本不同会报错，所以需要改成局部安装
* 要使用本地可以在package中配置script，然后npm run build即可
* 运行package中script的时候，会现在局部环境中寻找，找不到再去全局



## loader

> 在开发中，我们不仅要处理js文件，还要将cs，typescript，图片，es6等转换成浏览器能够处理的文件
，只有webpack是做不到那么多的，所以需要安装扩展loader

![loader](./img/webpack/loader.png)

使用方法：

* 通过npm安装需要使用的loader，如`npm i css-loader`
* 在webpack.config.js中的module关键字下进行配置

### css-loader使用注意

* 使用css-loader时，只是将css文件加载，还需要再安装style-loader进行渲染dom树

* module中的use规则，读取顺序是从右向左，所以应该先写style-loader，再写css-loader，

  如`[ 'style-loader', 'css-loader' ]`

### file-loader的使用

**url-loader和file-loader的配置文件只能使用一个**  

安装：`npm install file-loader -D`

开发时默认调用html路径下的图片文件。

如果图片和html不在同一个文件夹，可以在webpack.config.js的output中设置`publicPath:'dist/'` 。
就会默认引用该路径中的文件

> 开发中打包图片我们想要使用原来的名字，可以在options中自定义文件的名字

```js
{
  test: /\.(png|jpe?g|gif|svg)$/i,
    use:{
      loader:"file-loader",
        options:{
          // 我们还可以使用placeholder进行命名文件
          //img文件夹中，原来文件名+8位哈希值+原格式extension的缩写
          name:'img/[name]_[hash:8].[ext]'
        }
    }
}
```

**最常用的placeholder：**

* [ext]： 处理文件的扩展名；
* [name]：处理文件的名称； 
* [hash]：文件的内容，使用MD4的散列函数处理，生成的一个128位的hash值（32个十六进制）；
* [contentHash]：在file-loader中和[hash]结果是一致的（在webpack的一些其他地方不一样，后面会讲到）；
* [hash:]：截图hash的长度，默认32个字符太长了； 
* [path]：文件相对于webpack配置文件的路径

### url-loader的使用

**url-loader和file-loader的配置文件只能使用一个**  

安装：`npm install url-loader -D`

* 当css引入图片时，需要添加并配置url-loader。

* 图片文件字节小于limit时，会对图片进行base64编码，css中url通过base64编码显示

* 如果图片大于limit时，则会提示安装file-loader。（limit默认=8kb）

* 为了防止文件命名重复，还可以在options中配置文件hash值

  ```js
  {
    test: /\.(png|jpe?g|gif|svg)$/i,
      use:{
        loader:"url-loader",
          options:{
            name:'img/[name]_[hash:8].[ext]',
              limit:10 * 1024		// 只把10kb以下的图片进行base64b
          }
      }
  }
  ```

<br>

### file-loader和url-loader的区别

* 打包时file-loader会把图片进行哈希值命名并一起打包进dist文件夹中
* url-loader只是作为base64字符串来使用，不需要存储单独图片或其他格式的文件

**使用url-loader的优缺点**

**优点：**图片转成base64和打包文件一起加载，减少服务器的请求次数及服务器压力，达到更快的加载页面。

**缺点：**当图片大小超过100kb时转成base64的文件大小也很大，这样将得不偿失

**总结：**当图片小于100kb时使用`url-loader`，当大于100kb时使用`file-loader`

## babel => es6语法处理

webpack打包的js文件中还存在es6语法，由于部分浏览器不支持es6，所以我们需要帮它转换成es5

1. **安装方法**：	

	 `npm i -D babel-loader @babel/core @babel/preset-env`

* i是install的缩写

* -D是--save-deb的缩写

  **注意：这里版本要一致。webpack 3.x | babel-loader 8.x | babel 7.x**

  

2. **配置文件**：由于转义时会把所有引用的js一起转义，而node_modules不需要转义，所以需要进行排除处理

```js
rules:[
  {
  test: /\.js$/,
    //exclude 排除
    //include 包含
    exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
      }
	}
]
```



<br>

## 配置vue

vue运行时需要依赖，所以安装时不用添加-dev

```js
npm install vue --save
```

**注意：**  
1. vue模块导出时使用的export default方法，所以导入不用加大括号{}。

		import Vue from 'vue'
	
2. vue.runtime-only版本不可以有template。但vue实例默认就是template，所以打包时报错。
	应该切换包含compiler的版本

	```js
	//在webpack.config中配置，和module同级
	resolve:{
	  //alias:别名。切换vue版本
	  alias:{
		//esm:esmodule
		'vue$':'vue/dist/vue.esm.js'
	  }
	}
	```
	
3. 引用组件时必须写扩展名，我们也可以设置，使不写扩展名也可引用
	
	```js
	//在webpack.config中配置，和module同级
		resolve:{
		  //alias:别名。切换vue版本
		  alias:{
			//esm:esmodule
			'vue$':'vue/dist/vue.esm.js'
		  },
		  //extensions:扩展名
		  extensions:['.js','.css','vue']
		}
	```
	
	我们引用vue时直接写`import app from 'App'`即可
	<br>

### vue文件封装处理

1. 安装`vue-loader`和`template-compiler`

		`npm install vue-loader vue-template-compiler --save-dev -D `

2. 配置文件  
	
	Vue Loader 的配置和其它的 loader 不太一样。除了通过一条规则将 vue-loader 应用到所有扩展名为 .vue 的文件上之外，
	请确保在你的 webpack 配置中添加 Vue Loader 的插件：
	[配置vue-loader官网](https://vue-loader.vuejs.org/zh/guide/#vue-cli)
		

```js
//引用插件，将下行代码添加至webpack.config头部
const { VueLoaderPlugin } = require('vue-loader');
//配置vue-loader
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
    // new一个插件，并记得引用！
    new VueLoaderPlugin()
  ]
}
```

<br>

## plugin插件

webpack中的插件，就是对webpack现有功能的各种扩展，比如打包优化，文件压缩等等。

使用方法：

1. 通过npm安装需要使用的plugins(某些webpack已经内置的插件不需要安装) 

2. 在`webpack.config.js`中的`plugins`中配置插件。
	
	```js
	//别忘记在头部引入
		const webpack = require('webpack')
	
	//plugins。和module同级
		plugins:[new webpack.BannerPlugin('这里是Vicer的版权')]
	```

### 打包html的plugin

默认html文件在根目录中，而我们需要将index.html文件打包到dist文件夹中发布

所以需要使用HtmlWebpackPlugin插件

1. 自动生成一个index.html文件(可以指定模板来生成)
2. 将打包的js文件，自动通过script标签插入到body中


```js
npm install html-webpack-plugin@3 --save-dev
```

配置文件：

```js
//头部引入
var HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [
  new HtmlWebpackPlugin({template:'index.html'})
]
```

### js压缩的Plugin

对打包的js文件进行压缩  
这里我们使用1.1.1版本，与cl2保持一致

```js
npm install uglifyjs-webpack-plugin@1.1.1 --save-dev
//或者
npm install uglifyjs-webpack-plugin@1 --save-dev
```

配置文件：

```js
const uglifyJsPlugin = require('uglifyjs-webpack-plugin');

plugins: [
  new uglifyJsPlugin()
]
```

## 搭建本地服务器

webpack提供了一个可选的本地开发服务器，这个本地服务器基于node.js搭建，内部使用express框架，
可以实现我们想要的让浏览器自动刷新显示我们修改后的结果。

**简而言之就是热更新**  
这里我们使用2.9.0版本，与webpack3.6.0相对应

```js
npm install --save-dev webpack-dev-server@2.9.1
```

配置选项：  
* contentBase：为哪一个文件夹提供本地服务，默认是根文件夹，我们这里要填写./dist

* port：端口号  //默认8080

* inline：页面实时刷新

* historyApiFallback：在SPA页面中，依赖HTML5的history模式
	
	  ```js
	//和module同级
	devServer:{
		contentBase:'./dist',
		inline:true
	}
	```
	
	注意：  
1. 这里不需要引用。  
2. 通过本地环境命令来调用，也就是在package中设置script
3. 命令中可以添加 --open，在自动更新时打开页面

		 `"dev": "webpack-dev-server --open"`
		 
## 配置分离

在开发过程中，有的配置开发时用，有的配置生成时用，为了对应不同的时期我们可以做一个配置分离

1. 在文件夹中把配置分离成三个文件，一个基础base，一个开发dev，一个生产prod。

2. 安装webpack合并工具
	
	`npm install webpack-merge --save-dev`     //无需配置webpack.config文件,使用时导入即可
	
3. 导入webpack-merge和基础config文件，其他插件按需导入，然后进行合并
```javascript
	//这里以生产prod.config.js为例
	const uglifyJsPlugin = require('uglifyjs-webpack-plugin');
	//必备导入
	const webpackMerge = require('webpack-merge');
	const baseConfig = require('./base.config.js');
	//合并配置并导出
	module.exports = webpackMerge(baseConfig,{
	  plugins: [
		//插件按需导入
		new uglifyJsPlugin()
	  ]
	});
```

4. 修改pageage文件

	* pageage文件中使用npm打包默认调用当前文件夹中webpack.config。
* 但我们要使用配置分离中的文件，所以script要手动引用相应的config文件
	
		  ```js
		"scripts": {
			"test": "echo \"Error: no test specified\" && exit 1",
			"build": "webpack --config ./build/prod.config.js",
			"dev": "webpack-dev-server --open --config ./build/dev.config.js"
		  }
	 ```
5. 修改基础配置文件

	打包中发现直接打包到了配置文件夹，但我们希望其打包到父级目录
	
	```js
		output : {
			//resolve拼接当前目录，在父级文件夹打包
			path:path.resolve(__dirname,'../dist'),
			filename:'bundle.js'
		}
	```
	
6. 总结

	使用npm run build 生产时打包文件  
	使用npm run dev   开发时打包文件