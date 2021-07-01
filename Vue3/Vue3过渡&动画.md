---
title: Vue3过渡&动画
date: '2021-06-07'
categories:
 - Vue
tags:
 - Vue3
---

> 没有动画的情况下，整个内容的显示和隐藏会非常的生硬。可以给单元素或者组件实现过渡动画，可以使用 transition 内置组件来完成动画；

### Transition

#### Transition组件的原理

> transition内置组件，可以给任何元素和组件添加进入/离开过渡

transition组件提供了以下处理方式：

1. 自动嗅探目标元素是否应用了CSS过渡或者动画，如果有，那么在恰当的时机添加/删除 CSS类名
2. 如果 transition 组件提供了JavaScript钩子函数，钩子函数将在恰当的时机被调用；
3. 如果没有找到JavaScript钩子和CSS过渡/动画，DOM插入、删除操作将会立即执行；

##### 过渡动画class

![](.\img\vue3-animate\animate原理.png)

**进入动画钩子：**

`v-enter-from`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。 

`v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动 画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。 

`v-enter-to`：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡/ 动画完成之后移除。

**离开动画钩子：**

`v-leave-from`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。 

`v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在 过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。 

`v-leave-to`：离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡/ 动画完成之后移除。

##### 过渡动画class的命名规则

* 如果transition没有name，则的class以 `v- `作为默认前缀

* 如果有name属性，比如 `<transtion name="why">`，那么所有的class会以 `why-` 开头（如`why-enter-from`）；

#### 基础Transition案例

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition>  <!-- 根据命名规则，没有设置name属性，以v-作为前缀class -->
      <p v-if="isShow" class="demo">我是动画</p>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style>
.demo{
  position: relative;
  width: 200px;
  background: #ccc;
  left:0
}
/* 进入动画前和动画结束后，demo应为隐藏状态 */
.v-enter-from, .v-leave-to {
  left: -100%;
}
/* 进入动画完成和结束动画开始，demo应为显示状态 */
.v-enter-to, .v-leave-from {
  left:0
}
/* 为整个动画过程设置过渡效果 */
.v-enter-active, .v-leave-active {
  transition: left 1s ease;
}
</style>
```

#### 过渡CSS动画

> 我们也可以通过animation来实现过渡效果

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition>  <!-- 根据命名规则，没有设置name属性，以v-作为前缀class -->
      <p v-if="isShow" class="demo">我是动画</p>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style>
.demo{
  position: relative;
  width: 200px;
  background: #ccc;
  left:0
}
  
/*.v-enter-from,.v-leave-to{*/
/*  opacity: 0;*/
/*}*/
/*.v-enter-to,.v-leave-from{*/
/*  opacity: 1;*/
/*}*/
/*.v-enter-active,.v-leave-active{*/
/*  transition: opacity 2s ease;*/
/*}*/

.v-leave-active {		/* 当第一次点击的时候是隐藏demo，所以首次运行是leave动画效果，也就是left从0到-100% */
  animation: slide-animate 1s ease reverse;  /* 反转动画效果 */
}

@keyframes slide-animate {
  0% {
    left: -100%;
  }
  100% {
    left: 0;
  }
}

</style>
```

**总结：**通过两个案例我们可以看到

当设置`v-if或v-show===false`时(隐藏元素)，则`CSS钩子是leave`

当设置`v-if或v-show===true`时(展示元素)，则`CSS钩子是enter`

#### Transition的属性

* name：用于自动生成 CSS 过渡类名，如`name:fade`，CSS过渡名为`.fade-enter-from`

* type：指定过渡事件类型，如`transition`和`animation`，当我们的CSS中同时指定transition和animation时，Transition组件并不知道我们以哪个过渡类型为准，有可能当动画执行完成但是过渡效果还没有完成，所以我们使用多个属性时，需指定具体的过渡事件

* duration：过渡的时间(毫秒)，`1000 | { enter: 800, leave: 1000 }`，但一般都直接在CSS中设定
* appear：是否在初始渲染时使用过渡。默认为 `false`。

更多的属性可以查看[官网的API](https://v3.cn.vuejs.org/api/built-in-components.html#transition)

#### 多个元素的切换

> 当我们使用多个元素进行过渡时，会发现两个元素同时进行动画效果，但我们想让第一个元素完成动画效果后，第二个元素再开始动画效果，这时我们就可以使用mode属性

mode过渡属性：

* `in-out`: 新元素先进行过渡，完成之后当前元素过渡离开； 
* `out-in`: 当前元素先进行过渡，完成之后新元素过渡进入；  (一般out-in是大多数想要的状态)

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition mode="out-in">
      <p v-if="isShow" class="demo">我是节点</p>
      <p v-else>我是另一个节点</p>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {},
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style>
.demo{
  position: relative;
  width: 200px;
  background: #ccc;
  left:0;
}
.v-enter-from,.v-leave-to{
  opacity: 0;
  /*left:-100%;*/
}
.v-enter-to,.v-leave-from{
  opacity: 1;
  /*left:0;*/
}
.v-enter-active,.v-leave-active{
  transition: all 1s ease;
}
</style>
```

##### 动态组件的切换

> 与上方配置基本一致，只需要把标签换成动态组件即可

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition mode="out-in">
      <component  :is="isShow ? 'home':'about'" class="demo">我是节点</p>
    </transition>
  </div>
</template>
<!-- 注册home和about组件 -->
```

#### 自定义过渡class

我们可以transition内置组件中，通过以下 attribute 来自定义过渡类名：

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 [Animate.css](https://daneden.github.io/animate.css/).

```html
<!-- 使用方法： -->
<transition enter-from-class="custom-class">
```

#### JavaScript钩子

> 除了CSS钩子回调，transition组件给我们提供了JavaScript钩子方法

```html
<transition
  @before-enter="beforeEnter"		//进入之前
  @enter="enter"					//进入动画
  @after-enter="afterEnter"			//进入动画之后
  @enter-cancelled="enterCancelled" //取消动画
  @before-leave="beforeLeave"
  @leave="leave"
  @after-leave="afterLeave"
  @leave-cancelled="leaveCancelled"
  :css="false"				//添加:css="false"，跳过 CSS 的检测，避免过渡过程CSS规则的影响
>
</transition>
```

当我们需要动态改变CSS的属性值时，推荐只用JavaScript钩子。响应的JS钩子动画库为Gsap库，该库又依赖GreenSock平台，所以如需使用该库可上[GreenSock平台](https://greensock.com/docs/v3/Installation)查看文档即可

#### 第三方动画库

##### Animate.css

> Animate.css是一个已经准备好的、跨平台的动画库为我们的web项目，对于强调、主页、滑动、注意力引导 非常有用；并且Animate.css在github中有71k的star

[Animate.css官网](https://animate.style/)

安装引用：

```cmd
#安装：
npm i animate.css

#在main.js中导入：
import "animate.css"
```

**使用方式一：**

在官网复制动画名称，然后在`v-enter-active`和`v-leave-active`css中使用`animation`属性，并使用复制好的名称

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition mode="out-in">
      <p v-if="isShow" class="demo">我是节点</p>
      <p v-else class="demo">我是另一个节点</p>
    </transition>
  </div>
</template>

<style lang="scss">
#app {
  overflow: hidden;
  .demo {
    margin: 100px auto;
    width: 200px;
    background: #ccc;
  }
}
.v-enter-active {
  animation: bounceInRight 1s ease;
}

.v-leave-active {
  animation: bounceInLeft 1s ease reverse; /* 反转动画效果 */
}
</style>
```

**使用方式二：**

点击animate.css官网中，动画名右侧的复制按钮

![image-20210614185411703](.\img\vue3-animate\animate.css.png)

将复制好的类名，添加进transition组件的自定义过渡class中

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <!-- 由于所有动画都在animate__animated的class下，所有动画要把前缀加上 -->
    <transition mode="out-in"
                enter-active-class="animate__animated animate__backInRight"
                leave-active-class="animate__animated animate__backInLeft">
      <p v-if="isShow" class="demo">我是节点</p>
      <p v-else class="demo">我是另一个节点</p>
    </transition>
  </div>
</template>

<style>
.animate__backInLeft{
  animation-direction: reverse;  /* 设置动画的反转 */
}
</style>
```

##### GSAP库

> 当我们需要动态改变CSS的动画属性时，推荐使用JS钩子和Gsap库一起使用

GSAP可以使下面的所有内容动画化：

- **CSS**: 2D 和 3D transforms（如下方缩写表）, colors, `width`, `opacity`, `border-radius`, `margin`, 以及几乎所有的CSS值。
- **SVG attributes**: `viewBox`, `width`, `height`, `fill`, `stroke`, `cx`, `r`, `opacity`, etc. 插件像[MorphSVG]和[DrawSVG]可以用于高级效果。

- **Any numeric value** 例如，一个被渲染为`<canvas>`的对象。动画的摄像机位置在3D场景或滤镜的值。GSAP经常与Three.js和Pixi.js一起使用

###### 2D和3D transforms属性缩写表

> 只有transforms属性缩写即可，其他css属性正常使用无缩写

<img src="./img/vue3-animate/transforms 属性缩写表.png" style="zoom:67%;" />

安装引用：

```cmd
# 安装gsap
npm install gsap
# 引用gsap
import { gsap } from "gsap";
# 引用对应插件（如果不用插件则无需引用和注册插件）
import { PixiPlugin } from "gsap/PixiPlugin.js";
import { MotionPathPlugin } from "gsap/MotionPathPlugin.js";
# 注册插件（如果不用插件则无需引用和注册插件）
gsap.registerPlugin(PixiPlugin, MotionPathPlugin);
```

使用示例：

[GSAP库文档](https://greensock.com/docs/v3/GSAP/gsap.from())

GSAP库中一般常用得是两个方法：

* `gsap.from()` 从隐藏到显示得初始动画值
* `gsap.to()` 从显示到隐藏得目标动画值

###### 案例一：标签的隐藏与展示

```vue
<template>
  <div>
    <button @click="isShow = !isShow">按钮</button>
    <transition mode="out-in"
                @enter="enter"
                @leave="leave"
    >
      <p v-if="isShow" class="demo">我是节点</p>
      <p v-else class="demo">我是另一个节点</p>
    </transition>
  </div>
</template>

<script>
import {gsap} from 'gsap'

export default {
  name: 'App',
  components: {},
  data() {
    return {
      isShow: true
    }
  },
  methods: {
    enter(el, done) {
      gsap.from(el, {
        opacity: 0,			// css属性
        x: -100,				// transforms属性缩写
        duration: 1,		// gsap.from()API，动画执行时长
        onComplete: done 	// gsap.from()API，动画完成时要调用的函数
      });
    },
    leave(el, done) {
      gxsap.to(el, {
        opacity: 0,	
        x: 100,
        duration: 1,
        onComplete: done
      });
    }
  }
}
</script>

<style>
.demo {
  margin: 100px auto;
  width: 200px;
  background: #ccc;
}
</style>
```

###### 案例二：动画计数器

> 除了定义css属性动画，`gsap.from()`和`gsap.to()`还可以自定义目标值

```vue
<template>
  <div>
    <input type="number" step="200" v-model="count">
    <h2>{{ newNumber.toFixed(0) }}</h2>
  </div>
</template>

<script>
import {gsap} from 'gsap'

export default {
  name: 'App',
  data() {
    return {
      count: 0,
      newNumber: 0
    }
  },
  watch: {
    count(val) {
      gsap.to(this, {
        newNumber: val,	// 给目标值赋值,如赋值String，Number，对象的引用等。赋值成功后可以如往常一样引用
        duration: 1			// gsap.to()API，动画执行时长
      })
    }
  }
}
</script>
```

### Transition-group

> 当我们需要增加的动画状态不止一个，需要过渡整个列表的动画时，则需要用到Transition-group

**Transition-group的Attribute：**

* `tag`：如果想给每个循环的item一个包裹，可以使用tag属性（Vue3支持片段代码，所以无需根节点，如果仍想要一个包裹可以使用tag）
* `name`：和Transition一样，定义class的前缀

**Transition-group的过渡css：**

除了Transition原有的过渡class，还新增了一个

* `v-move`：当列表移动时触发此class钩子。可以通过name来自定义前缀

**注意：Transition-group没有mode属性，且内部循环元素必须提供key**



**基础案例一：**

![Transition-group1](.\img\vue3-animate\Transition-group1.gif)

```vue
<template>
  <div>
    <button @click="addNum">添加数字</button>
    <button @click="removeNum">删除数字</button>
    <transition-group tag="p">
      <span v-for="item in numbers" :key="item" class="item">{{ item }}</span>
    </transition-group>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      numbers: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
      numCounter: 10
    }
  },
  methods: {
    addNum() {
      // this.numbers.push(this.numCounter++)
      this.numbers.splice(this.randomIndex(), 0, this.numCounter++)
    },
    removeNum() {
      this.numbers.splice(this.randomIndex(), 1)
    },
    randomIndex() {
      return Math.floor(Math.random() * this.numbers.length)
    }
  }
}
</script>

<style scoped>
.item {
  display: inline-block;
  margin-right: 10px;
}

.v-enter-from, .v-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.v-enter-active, .v-leave-active {
  transition: all 1s ease;
}
.v-leave-active{  /* 当组件离开时还占据元素位置，需要脱离文档流 */
  position: absolute;
}

.v-move{
  transition: transform 1s ease;
}
</style>
```

**基础案例二：交错动画**

![2](.\img\vue3-animate\Transition-group2.gif)

```vue
<template>
  <div>
    <input type="text" v-model="inputVal">
    <transition-group tag="ul" @before-enter="beforeEnter" @enter="enter" @leave="leave">
      <li v-for="(item,index) in filterNames" :key="item" :data-index="index">
        {{ item }}
      </li>
    </transition-group>
  </div>
</template>

<script>
import {gsap} from 'gsap'

export default {
  name: "App",
  data() {
    return {
      inputVal: '',
      names: ['abc', 'bbb', 'james', 'jack', 'viceroy', 'codewhy']
    }
  },
  computed: {
    filterNames() {
      return this.names.filter(item => item.indexOf(this.inputVal) !== -1)
    }
  },
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
      el.style.height = '0'
    },
    enter(el, done) {
      gsap.to(el, {
        opacity: 1,
        height: '1.5em',
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    },
    leave(el, done) {
      gsap.to(el, {
        opacity: 0,
        height: 0,
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    }
  }
}
</script>
```

