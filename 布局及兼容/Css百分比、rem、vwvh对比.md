---
title: Css百分比、rem、vwvh对比
date: '2021-03-17'
categories:
 - 布局及兼容
---

> 布局中常用的单位，比如px、%、rem和vw等在响应式布局中的优缺点。这里是看文章后总结的一些点，具体可查看<a href="https://www.jianshu.com/p/ba26509bc5b3">原文</a>

首先我们要知道什么是像素：

**css像素**：为web开发者提供，在css中使用的一个抽象单位

**物理像素**：只与设备的硬件密度有关，任何设备的物理像素都是固定的



### px的优缺点

> px看似是固定值，但在不同的设备上展现的却是不同的像素。

1. `1 CSS像素 = 物理像素／分辨率`

2. `PC端： 1 CSS像素 = 物理像素／分辨率 = 750 ／ 980 =0.76 px`
3. `iphone6：1 CSS像素 = 物理像素 ／分辨率 = 750 ／ 375 = 2 px`

所以我们无法使用px一套样式适应多个设备



### 媒体查询

> 有人可能会想，我们可以通过媒体查询写多套样式啊

但这样太繁琐，要写多套代码



### 百分比

> css中的子元素中的百分比（%）到底是谁的百分比？



1. 子元素**width**或**height**的百分比是**父元素width或height的百分比**

2. **top**、**bottom**的百分比是相对于(默认定位)**父元素的height**
3. **left**、**right**的百分比是相对于(默认定位)**父元素的width**

4. **padding**、**margin**不论是垂直方向或者是水平方向，都相对于直接**父元素的width**

5. **border-radius**、**translate**、**background-size**的百分比，则是相对于**自身的width**



##### 百分比单位布局应用

使用padding-top来填充高度,来实现宽高自适应

```html
<style>
	.trangle{
  height:0;
  width:100%;
  padding-top:20%;
}
</style>

<div class="trangle"></div>
```

##### 百分比单位缺点

1. 计算困难，如果我们要定义一个元素的宽度和高度，按照设计稿，必须换算成百分比单位。 
2. 百分比布局不全是相对于父元素的单位,而且父元素的单位也不是唯一的



### rem解决方案

> 只相对于根元素(html)的font-size大小来决定

rem：相对长度单位。相对于根元素(即html元素) font-size 值的倍数。

#### rem的用法

当html元素的font-size为12px时 ,  `1rem =  12px`;



#### 一、自行换算rem

比如给定的视觉稿为750px（物理像素），如果我们要将所有的布局单位都用rem来表示，一种比较笨的办法就是对所有的height和width等元素，乘以相应的比例，现将视觉稿换算成rem单位，然后一个个的用rem来表示。

如果我们需要实现设备的自适应,只需要使用js监听设备的大小,动态改变font-size即可.

```js
//网上还有很多这样的代码,可以自行查找
function refreshRem() {
    var docEl = doc.documentElement;
    var width = docEl.getBoundingClientRect().width;
    var rem = width / 10;
    docEl.style.fontSize = rem + 'px';
    flexible.rem = win.rem = rem;
}
win.addEventListener('resize', refreshRem);
```



#### 二、px2rem

> 表示的是从px转化为rem。

另一种比较方便的解决方法就是，在css中我们还是用px来表示元素的大小，最后编写完css代码之后，将css文件中的所有px单位，转化成rem单位。

px2rem的原理也很简单，重点在于预处理以px为单位的css文件，处理后将所有的px变成rem单位。可以通过两种方式来实现：

1） webpack loader的形式：

```undefined
npm install px2rem-loader
```

在webpack的配置文件中：

```tsx
module.exports = {
  // ...
  module: {
    rules: [{
      test: /\.css$/,
      use: [{
        loader: 'style-loader'
      }, {
        loader: 'css-loader'
      }, {
        loader: 'px2rem-loader',
        // options here
        options: {
          remUni: 75,
          remPrecision: 8
        }
      }]
    }]
  }
```

}

2）webpack中使用postcss plugin

```undefined
npm install postcss-loader
```

在webpack的plugin中:

```tsx
var px2rem = require('postcss-px2rem');

module.exports = {
  module: {
    loaders: [
      {
        test: /\.css$/,
        loader: "style-loader!css-loader!postcss-loader"
      }
    ]
  },
  postcss: function() {
    return [px2rem({remUnit: 75})];
  }
}
```

####  rem 布局的缺点

在响应式布局中，必须通过js来动态控制根元素font-size的大小。或者是必须引入相关插件

也就是说css样式和js代码有一定的耦合性。且必须将改变font-size的代码放在css样式之前。



###  通过vw/vh来实现自适应

vw：相对于视口的宽度。视口被均分为100单位的vw

vh：相对于视口的高度。视口被均分为100单位的vh

vmax：相对于视口的宽度或高度中较大的那个。

vmin：相对于视口的宽度或高度中较小的那个。

```css
h1 {
 font-size: 8vw;
}
```

展示结果：可视区域的宽度÷100*你设置的vw的值

Vw和vh的区别是：一个相对与你的屏幕的宽度来设置，一个是你屏幕的高度来设置

和百分比的区别：百分比相对于父元素设置。而vw和vh相对于视窗来设置

#### vw单位换算

比如对于iphone6/7 375*667的分辨率，那么px可以通过如下方式换算成vw：

```undefined
0.266~ vw = (1/375）*100 vw  = 1px				//这里小数无限后循环用~表示   如0.26666 = 0.26~
1 vw = 1/100*375 = 3.75px
```

此外，也可以通过postcss的相应插件，预处理css做一个自动的转换，[postcss-px-to-viewport](https://links.jianshu.com/go?to=https%3A%2F%2Flink.juejin.im%2F%3Ftarget%3Dhttps%3A%2F%2Fgithub.com%2Fevrone%2Fpostcss-px-to-viewport)可以自动将px转化成vw。 postcss-px-to-viewport的默认参数为：

```csharp
var defaults = {
  viewportWidth: 320,
  viewportHeight: 568, 
  unitPrecision: 5,
  viewportUnit: 'vw',
  selectorBlackList: [],
  minPixelValue: 1,
  mediaQuery: false
};
```

通过指定视窗的宽度和高度，以及换算精度，就能将px转化成vw。



作者：一片空白1233
链接：https://www.jianshu.com/p/ba26509bc5b3
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

