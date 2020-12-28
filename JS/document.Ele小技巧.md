# document.getElement小技巧 #
&nbsp;

```js
	var gEle = function(tid){
		return document.getElementById(tid);
	}
```




这样调用document.getID的时候可以直接写 gEle("xx")即可

```js
//例如
var btnEle = gEle("btn");
```

