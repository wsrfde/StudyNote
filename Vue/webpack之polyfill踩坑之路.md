## @babel/polyfill的踩坑之路

> 在网页需要向低版本兼容时，如IE不支持promise等新特性，我们仅仅使用babel进行es5转换是不够的，还需要把这些新特性进行转换


### polyfill介绍

有些人不理解@babel/polyfill到底做了什么，以为运行了命令行工具也会将新的api转换成原有代码，事实上只是引入了大量的polyfill而已
浏览器环境下使用@babel/standalone动态的将ES6转换成ES5，针对IE11，模块引用了Object.assign这个新的api,因此需要使用@babel/polyfill。当然，自己写一个polyfill实现也是可以的。



### polyfill作用

Babel 包含一个polyfill 库。这个库里包含 regenerator 和 core-js.

这个库将会模拟一个完全的 ES2015+ 的环境。

这意味着你可以使用 新的内置语法 比如 promise 或者 WeakMap， 静态方法比如Array.from 或 Object.assign, 实例方法 比如 Array.prototype.includes 和 generator 函数。



### 使用步骤

**1. 安装必备配置**  

​	1.1 初始化项目
​	```npm init -y```    

​	2.2 安装babel和webpack  
​	

```
npm i -D babel-loader@8 @babel/core@7 @babel/preset-env   
npm install webpack@3 --save-dev
```

   注意：这里版本要一致。webpack 3.x | babel-loader 8.x | babel 7.x



**2. 安装polyfill**

​	 ```npm install --save @babel/polyfill```
​	这个是在你的source code前运行的，所以安装的时候是 --save  



**3  导入polyfill**  
	polyfill 用起来很方便，但是你应该和 @babel/preset-env 以及 useBuiltIns 的option 一起用。如果不这样做的	话，我们建议你手动引入需要的每个polyfill  

​	确保 它在 其他代码或者 引用前被调用

 - 使用CommonJS导入

   ```require('@babel/polyfill')```

 - 或者使用ES6导入**  
   ```import '@babel/polyfill'```



**4. 选择使用方式**

webpack 集成polyfill  

 + 和babel配置文件一起用的时候

   - 如果在.babelrc中指定 useBuiltIns: 'usage'的话  

     那么就不要在webpack.config.js 的 entry array 和source 中包含 @babel/polyfill 了。注意，@babel/polyfill 依然需要安装

   - 如果在.babelrc 中指定 useBuiltIns: 'entry'的话  

     那么就要和上面讨论的一样，在你应用的入口文件顶部通过require 或者 import 引入@babel/polyfill

   - 如果在.babelrc 中没有指定useBuiltIns的值或者useBuiltIns: false 

     可以直接在webpack.config.js 的 entry array 中添加 @babel/polyfill

     ```js
     module.exports = {
     	entry: ['@babel/polyfill','./src/main.js']
     }
     ```

- 如果没有使用babel配置文件.那么就可以像我们上面讨论的一样把@babel/polyfill 添加到webpack 的entry array 中。你也可以直接通过import 或require 把它添加到应用的入口文件顶部。但是我们并不推荐这么做



### babel配置文件中.babelrc和babel.config.js的区别

当我们需要用到node_modules进行编译时，建议用babel.config.js文件来配置

(文中我们使用的是webpack，需要用到node_modules，所以使用babel.config.js配置babel)

当我们只需要配置编译js不需要其他操作时，可以用.babelrc



### babel的文件配置

这里只兼容到IE10，如需要添加其他配置可自行添加

```js
module.exports = function (api) {
  api.cache(true);

  const presets = [
      ["@babel/preset-env", {
        "targets": {
          "ie": "10",
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
        "useBuiltIns": "false"
      }]
  ];

  return {
    presets
  };
}
```



### webpack文件配置

由于转义时会把所有引用的js一起转义，而node_modules不需要转义，所以这里使用exclude进行排除处理

```js
const path = require('path');

module.exports = {
  entry: ['@babel/polyfill','./src/main.js'],
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
            plugins:[
              "@babel/plugin-transform-runtime",
              "@babel/plugin-proposal-object-rest-spread"
            ]
          }
        }
      }
     ]
   }
};
```

**扩展**：细心的小伙伴可能注意到，我的babel配置中还增加了两个插件plugins，如不需要删除配置文件即可。

plugin-transform-runtime可重新使用Babel注入的帮助程序代码以节省代码大小，也可转义迭代器。

安装：

​	```	npm install --save-dev @babel/plugin-transform-runtime```

​	```	npm install --save @babel/runtime```

配置如上

plugin-proposal-object-rest-spread使用Babel的`objectSpread`程序生成符合规范的代码，也可转义数组解构。

安装：

​	`npm install --save-dev @babel/plugin-proposal-object-rest-spread`

配置如上



### 注意事项

1. babel只会转换E6语法，而不会转换新的api，让新的api生效的方法是使用传统的polyfill，为此需要引入这个模块

2. 安装的时候必须用 --save 保证引用到生产环境而不是开发环境，当然，弄错了，自己手动在package.json中修改也是可以的。

3. 如果只是测试下浏览器环境使用，用script标签引用模块下的polyfill.min.js文件，即可实现polyfill的导入，方法如下，但实际使用不建议！

   ```html
   <!-- 引入polyfill 让es6新的api能够在ie11上使用 这其实和babel没什么关系 -->
   	<script type="text/javascript" src="./polyfill.min.js"></script>
   <!-- 引用@babel/standalone 这个是babel在浏览器上使用的模块 会把ES6转成ES5 但是它依赖ES6的新api -->
   	<script type="text/javascript" src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
   
   ```

   

