# jquery技术小结   
<br>

## 使用css清除浮动
设置好左右浮动后,使用after清除浮动  

    .list:after{display: block;content: "";clear: both;}

## 滚动到顶部设置
这里用$()选择器的时候，选择body加scrollTop如果滚动不上去，可以在选择器中增加html试试  

	$(".top").click(function(){
			$("body,html").animate({scrollTop:0}, 300)
	});
