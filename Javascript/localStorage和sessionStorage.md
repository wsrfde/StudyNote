---
title: localStorage和sessionStorage使用
date: '2021-03-17'
categories:
 - Javascript
---

### localStorage和sessionStorage使用

> 示例浏览器Storage使用方法

**localStorage和sessionStorage使用方法一样**

```html
<input type="text"  id="shuru">
<input type="button" value="保存" onclick="saveIT()">
<input type="button" value="删除" onclick="deleteIT()">
<input type="button" value="读取" onclick="readIT()">
<input type="button" value="清空" onclick="clearIT()">
<p id="disp"></p>


<script>
  //当前浏览器是否支持localStorage
  if(window.localStorage){
    // alert("您的浏览器支持");
  }
  else{
    alert("您的浏览器不支持");
  }

  var shuruIpt=document.getElementById("shuru");
  var dispEle=document.getElementById("disp");
  var Key="jikexueyuan2";
  //保存
  function saveIT(){
    //localStorage.setItem(名称，相应的值)
    //localStorage.名称=相应的值
    localStorage.setItem(Key,shuruIpt.value);
  }
  //删除
  function deleteIT(){
    //localStorage.removeItem(名称);
    localStorage.removeItem(Key);
    dispEle.innerHTML=localStorage.getItem(Key);
  }
  //读取
  function readIT(){
    //localStorage.getItem(名称);
    //localStorage.名称
    dispEle.innerHTML=localStorage.getItem(Key);
  }
  //清空
  function clearIT(){
    localStorage.clear();
  }
</script>
```



### 监听浏览器Storage的更改

> Storage更改监听事件必须要至少打开2个页面，本页面无法检测自己的变化，具体操作可参考如下。
>
> 例如页面1往页面2添加音乐：在1页面保存storage，那么在2页面做个监听。

`注意要使用localStorage`

```js
//监听函数
window.addEventListener("storage",function(event){
              //localStorage的key+ 旧val + 新val
	console.log(event.key+":"+event.oldValue+"____"+event.newValue);
})
```

监听事件的兼容性代码：

```js
window.addEventListener("storage",自定义事件); 

window.attachEvent("onstorage",handle_storage); 
```





