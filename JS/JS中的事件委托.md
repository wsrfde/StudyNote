### JS中的事件委托

在JS中，事件委托又叫事件代理

> 在普通事件绑定中，我们会给每个元素都绑定一个监听事件，这样很浪费性能，我们可以给一个父级统一监听

但父级那么多子元素，怎么分辨子元素呢，答案就是event，它会记录事件源，也就是发生事件的子元素



#### 原来我们监听列表点击的方法：

```html
<!-- ul#ulEle>li[data-index='$']{我是第$个}*5 -->
<ul id="ulEle">
  <li data-index="1">我是第1个</li>
  <li data-index="2">我是第2个</li>
  <li data-index="3">我是第3个</li>
  <li data-index="4">我是第4个</li>
  <li data-index="5">我是第5个</li>
</ul>

<script>
  var ulEle = document.getElementById('ulEle');
  var liEle = ulEle.getElementsByTagName('li');

  for (var i = 0; i < liEle.length; i++) {
    liEle[i].onclick = function (e) {
      console.log(this.innerHTML);
    }
  }
</script>
```

**问题：**

1. 给每个li元素都绑定了监听事件
2. 如果我想要动态添加li，还需要重新循环数组



#### 使用事件委托监听：

```html
<!-- js中事件委托 -->
<script>
  var ulEle = document.getElementById('ulEle');

  ulEle.onclick = function (e) {
    var el = e || window.e;
    var target = el.target || el.srcElement;		//IE兼容
    if(target.nodeName.toLowerCase() === 'li'){	//过滤标签
      console.log(target.dataset.index);    //index
      console.log(target.innerHTML);        //内容
    }
  }
</script>
```



```html
<!-- vue中事件委托 -->

<!-- Vue中一般不需要做事件委托，事件委托中的优点vue已经帮你做了，除非你有数百或数千行，否则作用不大 -->
<ul @click='handleClick'>
  <li v-for='(item,index) in arr' :data-index='index'>{{item}}</li>
</ul>

<script>
  data:{
    arr:['a','b','c']
  },
  methods:{
    handleClick(e){
      if(e.target.nodeName.toLowerCase() === 'li'){	//过滤标签
        console.log(e.target.dataset.index);    //index
        console.log(e.target.innerHTML);        //内容
      }
    }
  }
</script>
```

