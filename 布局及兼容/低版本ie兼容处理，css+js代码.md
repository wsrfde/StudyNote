# ie兼容处理 #

&nbsp;
## css兼容代码示例

## csshack使用
ie的css兼容是一层层覆盖关系。

/*IE6 绿色  IE7 黑色	IE8紫色  IE9橘黄	1E10灰色*/

```js
.hack{
	background-color:red; /*默认*/
	background-color:purple\0; /* IE8 IE9 IE10 紫色 9 10被下一条覆盖*/
	background-color:orange\9\0;/*IE9 IE10 橘黄色*/
	*background-color:black; /* IE6, IE7 黑色，IE6会被下一条覆盖为绿色 */
	_background-color:green; /* IE6 绿色 */
}

/*for IE10+ 灰色 此写法可以适配到高对比度和默认模式，故可覆盖所有ie10的模式 */
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {   
	.hack { background-color:#A3A0A0; } 
}
/*Chrome Safari深红*/
@media screen and (-webkit-min-device-pixel-ratio:0){
	.hack { background-color:#5A090A;} 
}    
/*除了IE6 大杀器，一旦开启，无人能挡*/
.important{
	background-color:blue !important;
}
```

## css兼容利器
ie-css3.htc 可以使ie支持css3的部分效果
用法 ：

	behavior: url(js/ie-css3.htc);  /*（括号里是ie-css3.htc的地址）*/

&nbsp;

## js代码兼容示例

### 方法一，做event兼容

```js
//window.event不支持firefox，直接获取不到
//使用event使其兼容，srcElement和target也是兼容代码

function eventDemo1(event){
	var event = event ? event : window.event;
	var se = event.srcElement || event.target;
	console.log(se.innerHTML);
}
```

### 方法二，使用js删除原有，然后创建重新替换

```js
//这里演示的案例是ie不支持直接更换input type元素

function changeIptType2(){		//替换元素法
	//获取input框父元素iptBox
	var iptBox=document.getElementById("iptBox");
	//获取当前的input框，并且获取其当前的value
	var myIpt=document.getElementById("myipt");
	var sValue=myIpt.value;
	//删除原来的input框
	iptBox.removeChild(myIpt);

	//新建一个input框并指定它的id、type、value
	var newIpt=document.createElement("input");
	newIpt.id="myipt";
	newIpt.type="password";
	newIpt.value=sValue;
	//将新建的input放到原来的位置，完成替换
	iptBox.appendChild(newIpt);
}
```

### 方法三，使用第三方播放器代替video元素
这里不做演示，只需要把代码下载下来进行更改即可
如使用ckplayer替代video

### 方法四，使用if IE方法来判断使用什么代码
注意，if IE中写在body代码中

```js
<!--[if IE]>
	这段文字只在IE浏览器显示
	<link rel="stylesheet" href="css/ie.css">
	<style>
		body{ background:#ccc;}
	</style>
<![endif]-->

<!--[if IE 6]>
	<br/>这段文字只在IE6浏览器显示
	<script>
		alert("请更新到最新浏览器");
	</script>
	<style>
		body{display:none}
	</style>
<![endif]-->
```



&nbsp;

## js浏览器版本显示

可以使用js在不同版本设定不同样式，或者location到其他页面

```js
var showEle=document.getElementById('show');
showEle.innerHTML="当前浏览器标示为:"+window.navigator.userAgent
	+"<br/>版本为："+window.navigator.appVersion;
try{
	if(navigator.appName == "Microsoft Internet Explorer" && navigator.appVersion.match(/6./i)=="6."){ 
		aboutEle.innerHTML+="<br/>识别结果：IE 6";
		//设置样式 location指向另一个页面
	} 
	else if(navigator.appName == "Microsoft Internet Explorer" && navigator.appVersion.match(/7./i)=="7."){ 
		aboutEle.innerHTML+="<br/>识别结果：IE 7";
	} 
	else if(navigator.appName == "Microsoft Internet Explorer" && navigator.appVersion.match(/8./i)=="8."){ 
		aboutEle.innerHTML+="<br/>识别结果：IE 8";
	} 
	else if(navigator.appName == "Microsoft Internet Explorer" && navigator.appVersion.match(/9./i)=="9."){ 
		aboutEle.innerHTML+="<br/>识别结果：IE 9";
	} 
	else if(navigator.userAgent.indexOf("Chrome") != -1) { 
		aboutEle.innerHTML+="<br/>识别结果：Chrome";
		// document.body.style.background="red";
	} 
	else if(navigator.userAgent.indexOf("Opera") != -1) { 
		aboutEle.innerHTML+="<br/>识别结果：Opera";
	} 
	else if(navigator.userAgent.indexOf("Firefox") != -1) { 
		aboutEle.innerHTML+="<br/>识别结果：Firefox";
	} 
	else if(navigator.userAgent.indexOf("Netscape") != -1) { 
		aboutEle.innerHTML+="<br/>识别结果：Netscape";
	} 
	else if(navigator.userAgent.indexOf("Safari") != -1) { 
		aboutEle.innerHTML+="<br/>识别结果：Safari";
	} 
	else{ 
		aboutEle.innerHTML+="<br/>识别结果：无法识别的浏览器";
	} 
	console.log(window.navigator);
}
catch(e){}
```
