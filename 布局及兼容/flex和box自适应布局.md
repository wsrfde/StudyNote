# flex和box自适应布局 #
&nbsp;
## flex中间布局
display:flex配合flex-wrap:wrap更适合做自适应模型
做中间布局的话注意在里面加一层div

```html
.flexbox{
	width: 500px;
	height: 500px;
	background: #ccc;
	display: flex;
	align-items: center;
	margin-top: 10px;
}
.flexbox div{
	text-align: center;
	flex: 1;
}
<div class="flexbox">
	<div>
		 <img src="logo.png" alt="jikexueyuan">
	</div>
</div>
```
### box中间布局 
仅仅是中央布局的话推荐box方法，注意前面加-webkit-

```html
.imgbox{
	width: 500px;
	height: 500px;
	background: #ccc;
	display: -webkit-box;
	-webkit-box-pack:center;
	-webkit-box-align:center;
}
<div class="imgbox">
	<img src="logo.png" alt="jikexueyuan">
</div>
```
