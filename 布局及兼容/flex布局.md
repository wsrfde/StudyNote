---
title: Flex布局
date: '2021-03-17'
categories:
 - 布局及兼容
---

在flex中，没有x和y轴，这里称之为主轴和交叉轴 

**应用在flex container 上的css属性**

- flex-flow   缩写属性  =>  flex-direction   ||   flex-wrap
- flex-direction  决定flex-item排序方向
- flex-wrap   决定flex-item是否换行      //默认在同一行显示
- justify-content  决定flex-item在主轴的对齐方式
- aligin-items   决定flex-item在交叉轴的对齐方式
- align-content   决定多行flex-item在交叉轴的对齐方式

**注意：**使用justify-content时不要给item用flex，给item固定宽度即可



**应用在flex items 上的css属性**

- flex
- flex-grow  成长，意为增加flex-item的宽度。当flex-grow总和大于1时，把剩余的size分给对应item，并占满父元素宽度     当总和小于1时，则分配剩余size的百分比
- flex-basis   决定flex-item的宽度   //优先级    max-width/min-width > flex-basis > width  >自身尺寸
- flex-shrink  收缩，和flex-grow相反，数值越大，收缩比例越高
- order   值越小，越排前面
- aligin-self   单独设置flex-item的交叉轴的对齐方式，覆盖aligin-items



### flex



flex是flex-grow || flex-shrink || flex-basis 的简写，flex属性可以指定1个，2个或3个值

单值语法：值必须为以下其中之一：

- 一个无单位数（number）:它会被当做flex-grow的值
- 一个有效的宽度（width）值：它会被当做flex-basis的值
- 关键字none，auto或initial



双值语法：第一个无单位数字，并且它会被当做flex-grow的值

- 第二个值必须为以下：
  - 一个无单位数：它会被当做flex-shrink的值
  - 一个有效的宽度（width）值：它会被当做flex-basis的值



三值语法：flex-grow  flex-shrink  flex-basis





