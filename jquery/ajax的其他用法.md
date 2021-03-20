### 监听ajax开始和结束

```js
//调用ajaxStart与ajaxComplete，监听ajax的开始和结束
$(document).ready(function () {
  //要在前面开始监听
  $(document).ajaxStart(function () {
    console.log('开始调用ajax');
  });

  $(document).ajaxComplete(function () { //ajax Complete
    console.log('ajax请求完成');
  });

  $.ajax({
    type: 'GET',
    url: 'https://xxx', //这里自行放置接口
    success: function (res) {
      console.log(res);
    }
  });

})
```

### ajax载入js并调用传递参数

```html
//main.html
<button id=btn>按钮</button>	
<script>
$.getScript("test.js", function (script, textStatus, jqXHR) {
  $('#btn').click(function () {
    testFun('vicer') //调用test.js中的函数，并传入值
  })
});
</script>


//test.js
function testFun(param) {
  console.log('我接受到参数'+param);
}
```

### ajax载入HTML最新版本

```js
$.ajax({
  url: "test.html",
  cache: false,
  success: function(html){
    $("#box").append(html);
  }
});
```

