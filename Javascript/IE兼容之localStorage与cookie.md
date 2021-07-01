---
title: IE兼容之localStorage与cookie
date: '2021-03-17'
categories:
 - Javascript
---

### 页面保存——localStorage与cookie的兼容

> 首先我们来看下cookie的用法

```js
var db = {	
		//添加cookie
	  	setCookie:function(name,value){
	  		var Days = 30; 
	        var date= new Date(); 
	        date.setDate(date.getDate()+Days * 24 * 60 * 60 * 1000); 
	        document.cookie = name+"="+value+";expires="+date; 
	    },
	    //获得cookie
	    getCookie:function(name){ 
	        var arr = document.cookie.split(";"); 
	        for(var i=0; i<arr.length; i++){ 
	        	var arr2 = arr[i].split("="); 
	            if(arr2[0] == name){ 
	                return arr2[1]; 
	            } 
	        };
	        return null; 
	    },
       //删除cookie
      removeCookie:function(name){ 
        setCookie(name,"",0) 
      } 
}
```

当我们想要保存本地数据时，**chorme不兼容cookie。ie不支持localStorage。所以我们可以判断，当浏览器支持哪个则调用哪个方法**

这里示例是本地保存图片。
bg1s.png是小图片
bg1.jpg是背景图。





```js
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
	#bigbox {
		width: 100px;
		height: 330px;
		border: 2px solid red;
	}
	#bigbox div a {
		display: inline-block;
		width: 100px;
		height: 100px;
		margin: 10px;
	}
	</style>
</head>

<body>
	<div id="bigbox">
		<div>
			<a href="javascript:void(0)">
				<img src="bg1s.png" alt="1" data-src="bg1.jpg">
			</a>
		</div>
		<div>
			<a href="javascript:void(0)">
				<img src="bg2s.png" alt="2" data-src="bg2.jpg">
			</a>
		</div>
		<div>
			<a href="javascript:void(0)">
				<img src="bg3s.png" alt="3" data-src="bg3.jpg">
			</a>
		</div>
	</div>
	<script>
	var bigboxEle = document.getElementById('bigbox');
	var divEle = bigboxEle.getElementsByTagName('div');
	var key = "theme";

	for (var i = 0; i < divEle.length; i++) {
		console.log(i);
		divEle[i].onclick = function() {
			bigImg = this.getElementsByTagName('img')[0].getAttribute("data-src");
			saveImg(bigImg);
			document.body.style.background = "url(" + bigImg + ") no-repeat";
		}
	}

	function saveImg(tid) {
		if (window.localStorage) {
			localStorage.bg = tid;
		} else {
			db.setCookie(key, tid);
		}
	}
	function readImg() {
		if (window.localStorage) {
			bigImg = localStorage.bg;
			if (!localStorage.bg) {
				bigImg = "bg1.jpg";
			}
		} else {
			bigImg = db.getCookie(key);
			if (!db.getCookie(key)) {
				bigImg = "bg1.jpg";
			}
		}
		document.body.style.background = "url(" + bigImg + ") no-repeat";
	}
	var db = {
		//添加cookie
		setCookie: function(name, value) {
			var Days = 30;
			var date = new Date();
			date.setDate(date.getDate() + Days * 24 * 60 * 60 * 1000);
			document.cookie = name + "=" + value + ";expires=" + date + ";SameSite=Strict";
		},
		//获得cookie
		getCookie: function(name) {
			var arr = document.cookie.split(";");
			for (var i = 0; i < arr.length; i++) {
				var arr2 = arr[i].split("=");
				if (arr2[0] == name) {
					return arr2[1];
				}
			};
			return null;
		}
	}
	window.onload = function() {
		readImg();
	}
	</script>
</body>

</html>
```
