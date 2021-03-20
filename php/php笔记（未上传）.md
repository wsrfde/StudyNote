## 初遇php



### 标识符

1. 标识符

   ```php
   <?php
   
   //我们在标记住符里写代码
     
     echo 'hello world'
   
   ?>
     
     //我们还可以与html代码混写
     
     <b>我是b标签</b>
   ```

2. 注意代码结束后记得写分号 ;  表示结束。if判断不用写
3. php代码在服务器端进行解析，解析成html代码发送给客户端

### 标识符简写

1. 正常书写`<?php ?>`
2. 简写  `<? ?>`
3. 简写echo  `<?='我是内容'?>`

### 打印方式

1. `echo` 直接输出结果 ；
2. `var_dump`输出结果和变量类型;      (一般在调试的时候都使用`var_dump`)
3. `var_export`输出结构信息，和var_dump的区别是输入合法的php代码
4. `print_r`以人类易读的格式显示一个变量的信息。 

### 变量名

1. 以$开头，首字符不能是数字
2. 正确变量名 $a1    ,  错误变量名 $1a

### 判断

1. 使用if或else判断后面要加冒号（ : ）
2. if和else要写在php标签内
3. 判断结束要声明结束标签  `endif;`   并加上分号

示例

```php
<?php $abc = true ?>

<?php   if($abc ===true): ?>
  我是正确
<?php else: ?>
	我是错误
<?php endif; ?>
```

只有if判断时不写else即可



### 循环

1. 在判断后面加冒号( : )
2. 循环结束声明`endfor`

代码示例

```php
<?php for($i=0;$i<4;$i++): ?>
我被循环4次
<?php endfor; ?>s
```

## 数据类型

PHP 支持 9 种原始数据类型。   

   四种标量类型：   

- boolean（布尔型）         
- integer（整型）         
- float（浮点型，也称作 double)         
- string（字符串）         

   三种复合类型：   

- array（数组）         
- object（对象）         
- callable（可调用）         

   最后是两种特殊类型：   

- resource（资源）         
- NULL（无类型）     



### 布尔值转换

当转换为 boolean 时，以下值被认为是 **`FALSE`**：   

- 布尔值 **`FALSE`** 本身         
- 整型值 0（零）         
-  浮点型值 0.0（零）         
- 空字符串，以及字符串 "0"         
- 不包括任何元素的数组       
- 特殊类型null（包括尚未赋值的变量）         
- 从空标记生成的 SimpleXML 对象 

```php
<?php
var_dump((bool) "");        // bool(false)
?>
```

### 整数类型

php没有整除运算符，可以使用round()四舍五入，或float向下取整

### 浮点数

由于内部表达方式的原因，比较两个浮点数是否相等是有问题的。  为了减少误差值，可以比较计算中所能接受的最小的差别值。 

**在小数点后五位精度内都是相等的**

### 字符串

字符串可以用双引号或单引号表示，也可以用heredoc 语法或nowdoc 语法

注意：如果要使用转义字符，必须包裹在双引号内  

如`<?php echo " 我是斜杠 \\ " ?>`

#### heredoc语法

1. 在等号后写<<<，然后跟上声明字符（建议英文大写字符），如FOOBAR
2. 换行中写入内容。
3. 结尾处写入开头的字符FOOBAR即可

**特点：我们可以直接将heredoc语法视为双引号语法来看待，可以进行变量解析。同时双引号单引号都能显示**

```php
<?php
$name = 'vicer';
$str =  <<<FOOBAR
Hello '$name"!
FOOBAR;
  
echo $str;		//Hello 'vicer"!
?> 
```

#### nowdoc 语法

Nowdoc 结构很象 heredoc 结构，但是 nowdoc 中*不进行解析操作*。这种结构很适合用于嵌入 PHP     代码或其它大段文本而无需对其中的特殊字符进行转义

```php
<?php
  //nowdoc 结构可以用在任意的静态数据环境中，最典型的示例是用来初始化类的属性或常量
class foo {
    public $bar = <<<'EOT'
bar
EOT;
}
?> 
```

#### 变量解析

简单用法

```php
<?php
  $jump = 'apple';
  echo "i drank some $jump juice".PHP_EOL;
	//注意这里要在双引号中
?>
```

#### 复杂（花括号）语法

复杂语法不是因为其语法复杂而得名，而是因为它可以使用复杂的表达式。

```php
<?php
$great = 'aaaa';
$arr = ['a1','a2','a3'=>'isA3'];
//有两种用法
echo "my name is ${great} \n";
echo "my name is {$great}";
//解析对象用法
echo "This works {$arr[0]}"; 
echo "This works {$arr['1']}";
echo "This works {$arr['a3']}";
?>
```

#### 字符串拼接胶水（.）点

我们都知道，在php中，如下代码是不能够被解析的

```php
$str = 'php';

echo "$straaa";
```

解决办法：

1. 是给**变量名加上大括号**
2. 在变量名后面**加个空格**或**特殊符号**

3. 还有一种方法是使用**字符串拼接胶水（.）点**

```php
$str = 'php';

echo $str . "aaa";	//phpaaa
```



#### 双引号与单引号的区别

**【重要知识点】PHP面试题中，高概率面试题（建议背诵并实验三遍以上）**

  1.双引号解析变量，但是单引号不解析变量。

  2.在双引号里面插入变量，变量后面如果有英文或中文字符，它会把这个字符和变量拼接起来，视为一整个变量。一定要在变      量后面接上特殊字符，例如空格等分开。

  3.如果在双引号里面插变量的时候，后面不想有空格，可以拿大括号将变量包起来。

  4.双引号解析转义字符，单引号不解析转义字符。但，单引号能解析\' 和\

  5.单引号效率高于双引号，尽可能使用单引号

  6.双号和单引号可以互插！！！双引号当中插入单引号，单引号当中插入变量，这个变量会被解析。

  7.神奇的字符串拼接胶水——（.）点，用来拼接字符串。

  8.我们将定界符声明字符串视为双引号一样的功能来看待。

### 数组类型

#### 概念

##### 多维数组

数组中包含数组时，我们叫做**多维数组**

如 `$arr = [1,2,[1,2,3]]`;

##### 索引数组

如`$arr = [1,2,3]`;

##### 关联数组

如`$arr = ['a'=>1,'b'=>2]`

当我们声明键(key)的时候，就是关联数组，如果让php自动生成，则是索引数组

#### 两种声明数组的方法

1. ```php
   $array = array(  "foo" => "bar",  "bar" => "foo" );
   ```
   
2. ```php
   $array = [  "foo" => "bar",  "bar" => "foo" ];
   ```



#### 数组键名强制转换

- 包含有合法整型值的字符串会被转换为整型。例如键名 *"8"* 实际会被储存为 *8*。但是 *"08"*则不会强制转换，因为其不是一个合法的十进制数值。             
- 浮点数也会被转换为整型，意味着其小数部分会被舍去。例如键名*8.7* 实际会被储存为 *8*。             
- 布尔值也会被转换成整型。即键名 *true* 实际会被储存为 *1*        而键名 *false* 会被储存为 *0*。             
- null会被转换为空字符串，即键名*null* 实际会被储存为 *""*。             
- 数组和对象*不能*被用为键名。坚持这么做会导致警告：*Illegal offset type*。

```php
<?php
  $array = [
    1=>'a',			//key=1
    2.2=>'b',		//key=2
    true=>'c',	//key=1
    ''=>'d',		//key=''
    null=>'e'		//key=''
  ];
  var_dump($array) ;		//
?>
```

#### 数组索引值

如果没有给出索引值，则数组中添加的元素是最大索引+1, 如果更改索引，则后面的元素使用最大的索引+1

```php
<?php
 $arr = ['1','2',6=>'3','4'];

 var_dump($arr);	//索引 0，1，6，7
 ?>
```

#### 数组的增删改查

当添加数组元素时，key不赋值则自动为数组最大索引+1

```php
 <?php
 $arr = [1=>'a',2=>'b'];
 $arr[] = 'c';		//增加  $arr[3] = 'c'
 unset($arr[3]);	//删除
$arr[2] = 'd'; 		//改变	$arr[2] = 'd'
echo $arr[2];			//查找
 var_dump($arr); //$arr=['a','b'];
 ?>
```

#### 索引初始化

如果数组中元素全部删除，再重新添加数组元素时，索引按照删除前的最大索引+1继续增加元素

```php
<?php
$arr=[1,2,3,4];
foreach($arr as $i =>$value){
  unset($arr[$i]);
}

$arr[]='a';    // $arr = [4=>'a']

//我们可以使用array_values($arr);进行索引初始化
$brr = array_values($arr);
print_r($brr);  //$brr = [0=>'a']
?>
```

#### 访问数组中元素

调用数组中的变量名时，请使用字符串嵌套变量名

```php
 <?php
   $arr = ['fruit'=>'apple','veggie'=>'carrot'];
   print $arr['fruit'];
   print "$arr[fruit]";	//方式二
	 print "hello $arr['fruit']"; //字符串和变量拼接
 ?>
```

#### 数组遍历

```php
$arr = ['red','yellow','green'];

foreach( $arr as $arritem){
  echo "you like $arritem?\n";
}
```

### 对象

#### 对象初始化

```php
<?php
  class foo {
    function fooFun(){
      echo 'i am fooFun';
    }
  }

  $a = new foo;
  $a->fooFun();	//调用类foo中fooFUn函数
?>
```

###  Callback/Callable 类型

Callback === Callable  二者含义完全相同

回调函数不止可以是简单函数，还可以是对象的方法，包括静态类方法。

```php
<?php
// An example callback function
function my_callback_function() {
  echo 'hello world!';
}

// An example callback method
class MyClass {
  static function myCallbackMethod() {
      echo 'Hello World!';
  }
}

// Type 1: 简单的回调函数
call_user_func('my_callback_function');

// Type 2: 静态类方法调用
call_user_func(array('MyClass', 'myCallbackMethod'));

// Type 3: 对象方法调用
$obj = new MyClass();
call_user_func(array($obj, 'myCallbackMethod'));
?>
```

### resource

资源  resource 是一种特殊变量，保存了到外部资源的一个引用。

借此引用，php可以和外部资源进行数据交换。

常见的有：打开一个文件，创建一个数据库链接等。

注：普通开发人员不能手动创建此类型

### null

当以下情况时为null

* 手动赋值
* 定义变量，但从未赋值
* unset($变量)



### 类型强制转换

当数字与其他类型相加时，则尝试把其他类型转换为数字类型

```php
1+'1' = 2   //当字符串可转化为数字时，转化成整数或浮点类型相加
1+'aaa1'= 1  //当字符串不可转化为数字时，转化为0相加
1+'1aaa'= 2  //当字符串前面为数字时，转化前面的数字进行相加
1+true = 2	 //ture为1  false为0
```

#### 手动转换方法

除了php自动转换，我们也可以手动进行转换

```php
$a = (string)1;  //转换成字符串
$b = (bool)1;		//转换成布尔值
$c = (int)true;		//转换成整数
$d = (float)'1.5';		//转换为浮点型
$e = (string)1;			//转换为字符串
$f = (array)null;		//转换为数组			如果将null转化为数组会得到空数组
$g = (object)'str';		//转换为对象		如果没有key则默认key为scalar 如 $g->scalar
$h = (unset)1;				//转化为NULL
```

也可以直接使用`settype(变量，类型)`直接改变变量

```php
$foo = 127.11;
settype($foo,int);	//127
```

**类型(变量)转换和settype转换的区别：**

* `$目标变量 = 类型(变量)`只会改变**目标变量**的类型

* `settype`是改变原值



#### 布尔值为false的类型转换

1. 整型的`0`
2. 浮点类型，小数点后没有非0数值，`0.0`
3. 字符串的0，也是假`'0'`
4. 空字符串，中间没有空格`''`
5. 空数组`[]`
6. 没有进行声明变量，或赋值`null`
7. 未声明成功的资源



### 类型判断

我们使用is_* 系列函数。 is_types这一系列的函数，来进行判断某个东西是不是某个类型。如果是这个类型返回真，不是这个类型返回假。

* is_int 是否为整型

* is_bool 是否为布尔

* is_float 是否是浮点

* is_string 是否是字符串

* is_array 是否是数组

* is_object 是否是对象

* is_null 是否为空

* is_resource 是否为资源

* is_scalar 是否为标量

* is_numeric 是否为数值类型

* is_callable 是否为函数

  

**标量：**标量变量是指那些包含了`integer`、`float`、`string`或`boolean`的变量，而`array`、`object `和`resource`则不是标量。

```php
//换成echo,is_int试试，为什么echo执行假区间呢？
if(is_callable('var_dump')){
    echo '执行真区间';
}else{
    echo '执行假区间';
}

//为什么最后的实验中echo执行假区间呢？
//答：因为echo 不是函数，是基本语法。大家眼前可以不用理解函数和基本语法的区别。了解和不了解在实际开发中没有影响。知道有这个知识点就行。
```



### 资源类型之图片合成

> 此案例可以合成 图片文字/合成两个图片/圆角图片，以下代码需要用到哪段打开哪段即可

github资源代码示例[查看](https://github.com/wsrfde/generateImg)

注意：PHP操作图片需打开配置文件中 extension=php_gd2.dll

```php
<?php
//PHP操作图片需打开配置文件中 extension=php_gd2.dll
//==================================================生成图片==================================================
$imgname = "./test1.png";
//根据给定路径图片名或 web 路径创建 png 图片模板对象，图片不存在会报错，需加 @
$im = @imagecreatefrompng ( $imgname );
//对应的创建jpg图片的方法为 imagecreatefromjpeg ，例
//$im = imagecreatefromjpeg("http://wx.qlogo.cn/mmopen/Q3auHgzwzM4fA6602v35iaPhicn4NerIoHhHIsM6uBFjDnbn6saxic3QJBibmibterqicNwiajic1ff8Y2sPhj1ictV0wMw/096");
if (! $im) {
 //=======================创建图片对象
 //如果指定图片不存在，则创建指定大小的空图片模板对象，宽800，高500
 $im = ImageCreate ( 800, 500 );
 //第二种创建图片方法
 $im = ImageCreateTrueColor(800, 500);

 //=======================创建颜色对象
 //依据一个模板对象，生成颜色对象，0为red值，100为green值，30为blue值
 //ImageCreate 创建的对象在此会直接将颜色填充至模板对象中，ImageCreateTrueColor 创建的对象则只创建颜色对象，不填充
 $bgc = ImageColorAllocate ( $im, 0, 100, 30 );
 $tc = ImageColorAllocate ( $im, 0, 0, 0 );

 //=======================填充颜色
 //为图片对象填充矩形颜色，如果空模板对象不填充颜色则为黑色
 //100，50对应矩形填充区域左上角的横纵坐标，150，200对应矩形右下角的横纵坐标
 //这里坐标对ImageCreate模板对象不起作用，全局填充，对ImageCreateTrueColor起作用。
 ImageFilledRectangle ($im, 100, 50, 150, 200, $bgc);
 //第二种填充方法200,100同样为左上角横纵坐标，但这里坐标对两种方法创建的模板对象都不起作用，完全填充
 ImageFill($im, 200, 100, $bgc);

 //=======================填充文字
 //在模板对象中添加文字两种方法
 //40字体粗度，50字体左边距距离，5字体上边距距离，$tc字体颜色，这种方式只能填充英语，填中文乱码
 ImageString ( $im, 40, 50, 5, "just is English code", $tc );
 //添加中文18为字体大小，0字体旋转程度，100左边距距离，200上边距距离，项目目录下要有"MSYH.TTF"这个字体文件
 imagettftext($im, 18, 0, 100, 200, $tc, "msyh.TTF", "填充中文");
}



//==================================================合成图片==================================================
// // 载入图象
// $img1 = imagecreatefromjpeg("timg1.jpg");
// $img2 = imagecreatefromjpeg("timg2.jpg");

// //方法一合成图象 ，将参数二合成到参数一中，500离主图片左边距,400离主图片上边距,0新合图片内右边距偏移量,10新合图片内下边距偏移量, 243合到参数一后所占位置宽，90合到参数一后所占位置高
// // imagecopy($img1,$img2,500,400,0,10,243,90);

// //得到原始大图片尺寸
// $imgage = getimagesize("timg1.jpg");
// $src_W = $imgage[0]; //获取原图片宽度
// $src_H = $imgage[1]; //获取原图片高度
// //方法二合成图象并改变大小，500,400,0,10,与imagecopy作用一致，200分别是缩小后宽，高，$src_W，$src_H原图宽，高
// // imagecopyresampled($img1, $img2, 500,400,0,10, 200, 200, $src_W, $src_H);
// //方法三，并且合透明图必须用此方法，别的合成方法合成后该透明的地方无法透明
// //500,400,0,10, 243,90,与imagecopy作用一致，100表示参数二在参数一中透明程度，范围0~100,0完全透明，100完全不透明
// imagecopymerge($img1, $img2, 500,400,0,10, 243,90, 100);



//==================================================合成圆角图片==================================================
// //每次生成圆的四个角中一个角，中心透明，边角有色的图片，用于合成
// function get_lt($halfWidth) {
//  //根据圆形弧度创建一个正方形的图像
//  $img = imagecreatetruecolor($halfWidth, $halfWidth);
//  //图像的背景
//  $bgcolor = imagecolorallocate($img, 223, 0, 0);
//  //填充颜色
//  imagefill($img, 0, 0, $bgcolor);
//  //定义圆中心颜色
//  $fgcolor = imagecolorallocate($img, 0, 0, 0);
//  // $halfWidth,$halfWidth：以图像的右下角开始画弧
//  // $halfWidth*2, $halfWidth*2：已宽度、高度画弧
//  // 180, 270：指定了角度的起始和结束点
//  // fgcolor：指定画弧内的颜色
//  imagefilledarc($img, $halfWidth, $halfWidth, $halfWidth*2, $halfWidth*2, 180, 270, $fgcolor, IMG_ARC_PIE);
//  //将图片中指定色设置为透明
//  imagecolortransparent($img, $fgcolor);
//  //变换角度
//  // $img = imagerotate($img, 90, 0);
//  // $img = imagerotate($img, 180, 0);
//  // $img = imagerotate($img, 270, 0);
//  // header('Content-Type: image/png');
//  // imagepng($img);
//  return $img;
// }

// $img1 = imagecreatefrompng("test.png");
// $img2 = imagecreatefromjpeg("timg2.jpg");
// //获取图片尺寸
// $imgSize = getimagesize("timg2.jpg");
// $image_width = $imgSize[0];
// $image_height = $imgSize[1];

// //圆角长度，最好是图片宽，高的一半
// $halfWidth = $image_width / 2;

// //获取四分之一透明圆角
// $lt_img = get_lt($halfWidth);

// //改造$img2 左上角圆角透明
// imagecopymerge($img2, $lt_img, 0, 0, 0, 0, $halfWidth, $halfWidth, 100);
// //旋转图片
// $lb_corner = imagerotate($lt_img, 90, 0);
// //左下角
// imagecopymerge($img2, $lb_corner, 0, $image_height - $halfWidth, 0, 0, $halfWidth, $halfWidth, 100);
// //旋转图片
// $rb_corner = imagerotate($lt_img, 180, 0);
// //右上角
// imagecopymerge($img2, $rb_corner, $image_width - $halfWidth, $image_height - $halfWidth, 0, 0, $halfWidth, $halfWidth, 100);
// //旋转图片
// $rt_corner = imagerotate($lt_img, 270, 0);
// //右下角
// imagecopymerge($img2, $rt_corner, $image_width - $halfWidth, 0, 0, 0, $halfWidth, $halfWidth, 100);

// //生成红色
// $red = imagecolorallocate($img2, 223, 0, 0);
// //去除参数二中红色设成透明
// imagecolortransparent($img2, $red);

// imagecopymerge($img1, $img2, 117,37,0,0, 64, 64, 100);



//设定http输出格式
header ( "Content-type: image/png" );
// header("Content-type: image/jpeg");

//将二进制文件流输出到网页
imagePng ( $im );       //合成图片和文字
//如果是jpg二进制流用 imagejpeg 输出图象，并且后面加路径则直接生成保存图片，不再在页面输出
//如果已有图片则覆盖生成
// imagejpeg($img1, "new.jpg");       //直接保存为图片
// imagejpeg($img1);                  //输出在网页
// imagejpeg($img2);                     //圆角图片

//注销模板对象
ImageDestroy($im);
ImageDestroy($img1);
ImageDestroy($img2);
?>
```



## 变量

### 变量使用注意事项

1. 变量不能以数字开头
2. 最好不要用中文命名变量   （有可能被骂死）
3. 变量可以用字母和下划线开头
4. $this不能被赋值

### 变量赋值

当一个变量的值赋予另外一个变量时，改变其中一个变量的值，将不会影响到另外一个变量。

### 引用赋值

新的变量简单的引用（或者 "指向"）了原始变量。改动新的变量将影响到原始变量，反之亦然。 

用法：赋值过程中在源变量前面加上&符号

注意：只有有名字的变量才可以引用赋值。  如`$foo = &(10*5)`是非法的，没有变量名

```php
<?php
$foo = 'Bob';              // 将 'Bob' 赋给 $foo
$bar = &$foo;              // 通过 $bar 引用 $foo
$bar = "My name is $bar";  // 修改 $bar 变量，$foo 的值也被修改
echo $bar;								//My name is Bob
echo $foo;                 // My name is Bob
?> 
```

### 变量初始化

虽然在 PHP 中并不需要初始化变量，但对变量进行初始化是个好习惯。

1. 布尔类型的变量默认值是 **`FALSE`**
2. 整形和浮点型变量默认值是零
3. 字符串型变量默认值是空字符串
4. 数组变量的默认值是空数组。 

未初始化的变量具有其类型的默认值，但如果把一个文件包含到另一个之中时碰上相同的变量名，会有安全隐患，使用未初始化的变量会发出E_NOTICE错误。`isset()`可以检测变量是否初始化



### 全局变量

> 在php中，可以声明全局变量，但在函数中使用时，必须声明global

```php
<?php
$aa = 'aa';

function test(){
  global $aa;		//不声明的话会提示undefined
  echo $aa;
}
test();
?>
```

小提示: 在函数之外只用global 不算错

### 超全局变量

> 超全局变量是在全部作用域中始终可用的内置变量

它们在一个脚本的全部作用域中都可用。在函数或方法中无需执行 **global $variable;** 就可以访问它们。

这些超全局变量是：    

- $GLOBALS 
- **$_SERVER**             打印环境变量信息
- **$_GET**             接收GET
- **$_POST**             接收POST
- $_FILES
- $_COOKIE
- $_SESSION
- **$_REQUEST**             可以接收GET/POST的请求
- $_ENV

#### $GLOBALS的使用

**$GLOBALS是一个关联数组，每一个变量为一个元素，键名对应变量名，值对应变量的内容**

```php
<?php
$name = 'i am jack';

function test(){
  echo $GLOBALS['name'];	//超全局变量 在任何范围内都有效，它们并不需要 'global' 声明
}
test();
?>

```

#### $SERVER环境变量

环境变量不用记，直接使用内置函数`phpinfo()`，即可打印所有环境信息

**使用方法：**

```php
//当前运行脚本所在的服务器的主机名
echo $_SERVER["SERVER_NAME"]		//localhost
```

**常用示例**

| 键名                        | 含义                                     |
| --------------------------- | ---------------------------------------- |
| $_SERVER["REQUEST_METHOD"]  | 请求当前PHP页面的方法                    |
| $_SERVER["REQUEST_URI"]     | 请求的URI                                |
| $_SERVER["SERVER_SOFTWARE"] | 用的是哪一种服务器                       |
| $_SERVER["REMOTE_ADDR"]     | 客户的IP地址                             |
| $_SERVER["SERVER_ADDR"]     | 当前服务器的IP地址                       |
| $_SERVER["SCRIPT_FILENAME"] | 主前请求文件的路径                       |
| $_SERVER["HTTP_USER_AGENT"] | 当前访问这个网址的电脑和浏览器的情况     |
| $_SERVER["HTTP_REFERER"]    | 上级来源（用户从哪个地址进入当前网页的） |
| $_SERVER["REQUEST_TIME"]    | 当前的时间                               |

 

### 静态变量

> 静态变量仅在局部函数域中存在，但当程序执行离开此作用域时，其值并不丢失。

```php
<?php
function test2(){
  static $aaa  = 0;	//当不写静态时,每次调用函数$aaa都会被重置,而想让其值不丢失,可以在变量前面加上static
  echo $aaa;
  $aaa += 2;
};
test2();	//0
test2();	//2
test2();	//4
?>
```

注意:静态变量只能用来赋值,不能用来声明表达式;   如`static $aaa =  1+2; //是错误的`



### 可更名变量

在实际开发中,我们有可能需要动态更改变量名,这样可以减少代码复用更有利开发

#### 一、通过双$号改变变量名

```php
<?php
$a = 'b';
$b = 'hello';

echo $$a;			//在已声明变量后又加个变量符
//输出 ：  hello
?>  
```

双$赋值过程：

`$$a	=>	${$a}	=>	${'b'}	=>	$b`

#### 二、通过函数方式动态改变变量名

```php
<?php
class foo {
    var $bar = 'I am bar;';
    var $arr = array('I am A;', 'I am B;', 'I am C;');
    var $r   = 'I am r;';
}

$foo = new foo();
$bar = 'bar';
$baz = array('foo', 'bar', 'baz', 'quux');
echo $foo->bar "\n";				//正常调用方式
echo $foo->$bar . "\n";			//通过变量名对应的value调用
echo $foo->$baz[1] . "\n";	//通过查找数组调用

$start = 'b';
$end   = 'ar';
echo $foo->{$start . $end} . "\n";	//进行数组拼接调用

$arr = 'arr';
echo $foo->$arr[1] . "\n";		//这里调用后赋值的arr
echo $foo->{$arr}[1] . "\n";	//直接调用对象中的数组arr
?> 
  
//以上代码输出
 I am bar;
 I am bar;
 I am bar;
 I am bar;
 I am r;
 I am B;
```



### 来自 PHP 之外的变量

#### HTML 表单（GET 和 POST）

在html中我们可以只用form表单向php文件发送数据。通过form的method来定义发送数据的方法

当发送GET数据是，在php中使用`$_GET`来进行接收

当发送POST数据时，在php中使用`$_POST`来进行接收

数据是以数组的方式来传递，key为表单中对应input的name，value为表单中对应input的value

```php
  <form action="getForm.php" method="POST">
    <input type="text" name="name">
    <input type="text" name="age">
    <input type="submit" value="提交">
    
    <!-- 还可以使用图片的方式提交 -->
    <input type="image" src="image.gif" name="sub" />
    <!-- 相应的表单会被传送到服务器，并加上两个变量 sub_x 和 sub_y。它们包含了用户点击图像的坐标。 -->
  </form>

var_dump($_GET['name']);
var_dump($_POST['name']);
```





## 常量

> 常量是一个简单值的标识符（名字）。如同其名，**在脚本执行期间该值不能改变**（除了所谓的**魔术常量**，它们其实不是常量）。
>
> 命名规则与变量相同，**传统上常量标识符总是大写的。**

```php
<?php
define("FOO", "i am foo");
echo FOO;		//自定义常量不要以__开头,也许将来有一天PHP会定义一个__FOO__的魔术常量,代码会冲突
?>
```

### 常量和变量有如下不同

1. 常量前面没有美元符号（*$*）；             
2. 常量只能用`define()`函数定义，而不能通过赋值语句；             
3. 常量可以不用理会变量的作用域而在任何地方定义和访问；             
4. 常量一旦定义就不能被重新定义或者取消定义；             
5. 常量的值只能是标量。 
6. 在字符串中调用常量的时候，必须在引号外面（即用点拼接）

### 常量的定义方法

一、 **定义常量**

```php
<?php
define("CONSTANT", "Hello World");
echo CONSTANT;		
?>
```

二、**使用关键字 *const* 定义常量**

```php
<?php
// 以下代码在 PHP 5.3.0 后可以正常工作
const CONSTANT = 'Hello World';
echo CONSTANT;
?> 
```

**使用const定义注意事项**:      

和使用 [define()](mk:@MSITStore:C:\Users\Vicer\Desktop\php_enhanced_zh.chm::/res/function.define.html) 来定义常量相反的是，使用 *const*      关键字定义常量必须处于最顶端的作用区域，因为用此方法是在编译时定义的。这就意味着不能在函数内，循环内以及      *if* 语句之内用 *const* 来定义常量。 



### 魔术常量

魔术常量它们的值随着它们在代码中的位置改变而改变

这里只列举三个，一共有八个魔术常量，可以自行去文档查看

| 名称           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| **`__LINE__`** | 文件中的当前行号。                                           |
| **`__FILE__`** | 文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。自          PHP 4.0.2 起，**`__FILE__`**          总是包含一个绝对路径（如果是符号连接，则是解析后的绝对路径），而在此之前的版本有时会包含一个相对路径。 |
| **`__DIR__`**  | 文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。它等价于          *dirname(__FILE__)*。除非是根目录，否则目录中名不包括末尾的斜杠。（PHP 5.3.0中新增） = |





## 运算符



### 运算符优先级

科普：一元运算符，只有一个值即可进行运算，（二元和三元也可这么理解）

**一元运算符**有  `++自增 --自减 +取正 -取负 ~位非 !取反`

**二元运算符顺序**   算术运算符`*/+-`  >  比较运算符`>= !==` >  逻辑运算符`|| &&`  

运算符表无需全记住，**但要记住一元运算符优先级高于二元运算符**



### 运算符定义

* **算数运算符：**加减乘除等常规运算

* **赋值运算符：**注意赋值运算将原变量的值拷贝到新变量中（传值赋值），所以改变其中一个并不影响另一个。

* **引用赋值：**引用赋值意味着两个变量指向了同一个数据，没有拷贝任何东西。 

  ```php
  //代码示例
  <?php
  $a = 1;
  $b = &$a;		//在普通赋值前面加 & 符号
  
  $a = 2;		//无论任意变量改变，两个都跟着改变
  
  echo "$a \r";		
  echo $b;
  ?>
  ```

* **位运算符：**对整型数中指定的位进行求值和操作。 （一般编程中很少用到）

* **比较运算符：**故名思意，和js中比较运算符差不多，多了一个结合比较运算符；

  1. 结合比较运算符: `$a <=> $b`   当$a小于、等于、大于than $b时 分别返回一个小于、等于、大于0的integer值。**PHP7开始提供.** 

     ```php
     //结合比较运算符

     // Integers
     echo 1 <=> 1; // 0
     echo 1 <=> 2; // -1
     echo 2 <=> 1; // 1

     // Strings
     echo "a" <=> "a"; // 0
     echo "a" <=> "b"; // -1
     echo "b" <=> "a"; // 1

     echo "a" <=> "aa"; // -1
     echo "zz" <=> "aa"; // 1
     ```

  2. 三元运算符：`true?1:2`和js中一样，但是不建议混合用，如下

     ```php
     // 乍看起来下面的输出是 'true'
     echo (true?'true':false?'t':'f');

     // 然而，上面语句的实际输出是't'，因为三元运算符是从左往右计算的

     // 下面是与上面等价的语句，但更清晰
     echo ((true ? 'true' : 'false') ? 't' : 'f');
     ```

  3. NULL 合并运算符: 类似于if/else 语句，检测值是否为null

     ```php
     // NULL 合并运算符的例子
     $action = $_POST['action'] ?? 'default';

     // 以上例子等同于于以下 if/else 语句
     if (isset($_POST['action'])) {
         $action = $_POST['action'];
     } else {
         $action = 'default';
     }
     ```



* **错误控制运算符：**：`@`。当将其放置在一个 PHP表达式之前，该表达式可能产生的任何错误信息都被忽略掉。 

  > @ 运算符只对`表达式`有效。对新手来说一个简单的规则就是：如果能从某处得到值，就能在它前面加上@运算符。
  >
  > 例如，可以把它放在变量，函数和include调用，常量，等等之前。不能把它放在函数或类的定义之前，也不能用于条件结构例如if和foreach等。
  
  ```php
  @$fp = fopen('aaa.txt','r');		//打开一个不存在的文件
  ```
  
  

* **执行运算符：**PHP将尝试将`反引号（``）`中的内容作为 shell 命令来执行，并将其输出信息返回

  ```php
  $host = 'www.baidu.com';
  echo `ping -n 3 {$host}`;
  ```

  

* **递增／递减运算符：**和js一样，如a++

	> 递增／递减运算符不影响布尔值。递减 **`NULL`** 值也没有效果，但是递增 **`NULL`** 的结果是 *1*。 

* **逻辑运算符：**与js一样，如$a || $b

* **字符串运算符：**用来拼接字符串

  ```php
  $a = "Hello ";
  $b = $a . "World!";		//Hello World！
  //或者
  $a .= "World!";
  ```

  

* **数组运算符：**

  `+` 运算符把右边的数组元素附加到左边的数组后面，两个数组中都有的键名，则只用左边数组中的，右边的被忽略。

  `==` 运算符在数组中不判断key，只判断value是否相等

  `===` 运算符在数组中判断key与value是否都相等

  ```php
  $a = array("a" => "apple", "b" => "banana");
  $b = array("a" => "pear", "b" => "strawberry", "c" => "cherry");
  
  $c = $a + $b; // Union of $a and $b
  var_dump($c);
  
  //结果
  //array(3) {
  //  ["a"]=>
  //  string(5) "apple"
  //  ["b"]=>
  //  string(6) "banana"
  //  ["c"]=>
  //  string(6) "cherry"
  //}
  
  $c = $b + $a; // Union of $b and $a
  var_dump($c);
  
  //结果
  //array(3) {
  //  ["a"]=>
  //  string(4) "pear"
  //  ["b"]=>
  //  string(10) "strawberry"
  //  ["c"]=>
  //  string(6) "cherry"
  //}
  ```

  

* **类型运算符：** *instanceof* 用于确定一个 PHP 变量是否属于某一类 class的实例

* **反引号插代命令**：用于执行windows系统命令，等价于shell_exec函数

  ```php
  echo '<pre>';
  echo `ipconfig`;
  echo '</pre>';
  //有些虚拟服务器是禁止执行这些的命令脚本的
  ```

  

### 短路

> 短路就是采用一个懒人模式来思考问题

我们都知道，逻辑与的特性是两边都为true执行真。逻辑或的特性是两边有一为true执行真

现在我们吧自己想象成懒汉，来思考逻辑与和逻辑或：

**逻辑与：**如果第一个条件为false，后面一个就不用执行了

**逻辑或：**如果第一个条件为true，后面判断就不用执行了

```php
<?php
$a = false;
$b = 2;
if($a && $b++){
  echo '真';
}else{
  echo '假';
}
echo $b;  
//执行结果：  假  2
```

```php
<?php
$a = true;
$b = 2;
if($a || $b++){
  echo '真';
}else{
  echo '假';
}
echo $b;  
//执行结果：  真  2
```

通过代码看出，懒人思维的代码逻辑是正确的。

如果想要进行判断，并且让后面逻辑运行怎么办？

```php
//逻辑与  &&  改成 &  
//逻辑或  ||  改成 |
$a = false;
$b = 2;

if($a & $b++){
  echo '真';
}else{
  echo '假';
}
echo $b;  
//执行结果：  假  3
```



## 流程控制

一个赋值语句，一个函数调用，一个循环，一个条件语句，用来控制流程线程走向的语句，都属于流程控制

### if/elseif/else

这里和js判断方式相同，不同的是elseif是合并写道一起的。

```php
if(){
  //...
}elseif(){
  //...
}else{
  //...
}
```

### 流程控制的替代语法

PHP 提供了一些流程控制的替代语法，包括   *if*，*while*，*for*，*foreach*   和 *switch*。

替代语法的基本形式是把左花括号`{`换成冒号`:`，把右花括号`}`分别换成   *`endif;`*，*`endwhile;`*，*`endfor;`*，*`endforeach;`*   以及 *`endswitch;`*。

```php
<?php if ($a == 5): ?>
这里判断a 等于 5
<?php endif; ?> 
```

小提示：代替语法也可以直接写在`<?php ?>`中

```php
<?php
  $a = 5;
if ($a == 5):
    echo "a equals 5";
elseif ($a == 6):
    echo "a equals 6";
else:
    echo "a is neither 5 nor 6";
endif;
?>  
```

### while/do-while/for/switch

用法和js一样，这里就不做演示



### foreach

```php
$arr = array(1,2,3,4);

foreach ($arr as $key => $value) {		//或者只循环value 。如：foreach ($arr as  $value)
  echo $value;
}
```

#### 引用赋值

故名思意，引用元素的同时进行赋值

通过在 *$value* 之前加上& 来修改数组的元素，此方法是引用赋值，而不是拷贝

```php
$arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
    $value = $value * 2;
}
// $arr is now array(2, 4, 6, 8)
unset($value); // 最后取消掉引用
```

#### 用 list() 给嵌套的数组解包

```php
<?php
$array = [
    [1, 2],
    [3, 4],
];

foreach ($array as list($a, $b)) {
    // $a包含嵌套数组的第一个元素
    // 而$b包含第二个元素。
    echo "A: $a; B: $b\n";
  	//A: 1; B: 2
		//A: 3; B: 4
}
?> 
```

### break/continue

用法和js相同，一个跳出循环，一个继续循环。

后面可以跟一个数字，表示跳出几重循环。

```php
$i = 0;
while (++$i) {
    switch ($i) {
    case 5:
        echo "At 5<br />\n";
        break 1;  /* 只退出 switch. */
    case 10:
        echo "At 10; quitting<br />\n";
        break 2;  /* 退出 switch 和 while 循环 */
    default:
        break;
    }
}
```

### declare

文档没看明白，只看到有个时钟周期，可以在解释器每执行n条就触发事件

### return

将立即结束此函数的执行并将它的参数作为函数的值返回



### require / include

require和*include* 一样，都是包含并运行指定文件。 

区别：include在指定路径中查找不到文件会全局查找，如果还是找不到会在当前目录和目录下查找，如果最后仍未找到include发出警告继续运行。require则会返回一个致命错误，并停止运行

**建议用include**

### require_once / include_once

如名字一样，只运行一次

### goto

在当前文件内跳转到指定代码位置，该目标位置可以用目标名称加上冒号来标记

```php
<?php
goto a;
echo 'a';

a:
echo 'b';
?>
```

目标位置只能位于`同一个文件和作用域`，也就是说无法跳出一个函数或类方法，也无法跳入到另一个函数。无法跳入一个循环，但可以跳出循环。通常的用法是用 *goto*代替多层的 *break*。 



## 数据库

### 了解数据库

用处：持久化储存数据

各个软件的关系：

* mysql    —— 服务端
* php                     ——客户端
* navicat               ——客户端         
* sequel pro         ——客户端
* phpmyadmin    ——客户端
* cmd...                ——客户端

mysql只是一个软件，可以进行数据分析，只不过运行在服务端。

但你想要进行数据分析，还得要一个客户端，我们通过客户端链接都服务器，就能对数据进行读写

### 链接MySQL

我们这里使用xampp和navicat15，安装xampp并启用mysql

在navicat上面链接mysql，端口号在xampp上查看mysql的端口号，初始账号密码一般默认为root。创建链接即可。



### 创建数据库

* **库**

  一般来说我们一个项目会使用一个数据库。 

  数据库名称：无特殊要求，项目名即可

  字符集：如果没有特殊要求一律使用`utf8mb4`

  排序方式：默认第一个

* **表**

  根据业务需求，创建多个表

* **列**

  列中存放对应的数据及数据类型等

  类型：

  * 整数 —— int
  * 字符串 —— varchar(最多储存的字符数)
  * 时间 —— int / datetime   （时间也可以是时间戳，所以可以是整数int类型）
  * 货币 —— decimal    （float小数点不精准）

  每个表都建议有一个id列：

  * id为int类型
  * id为主键（即小钥匙1）
  * id设置为自动递增，这样建表时id会自动递增数值

### SQL命令实现增删改查

在数据库中我们使用面板工具即可实现增删改查，但如果我们要操作大量数据呢？



* **增加数据**：`inster into 表名 (键) values (值)`

  示例：`INSERT INTO msg (name,age) VALUES ('vicer',18)`	

* **删除数据**：`DELETE FROM 表名 WHERE 筛选条件`

  示例：`DELETE FROM msg WHERE id=1`

* **更改数据**：`UPDATE 表名 SET 键='值' WHERE 查询条件`

  示例：`UPDATE msg SET name='tace',age=20 WHERE id=2`

* **查询数据**：`SELECT 筛选字段 FROM 表名 WHERE 查询条件 ORDER BY 筛选顺序 DESC LIMIT 查询个数`

  示例：查询表中全部字段，并且id>2，id<5的条件  `SELECT * FROM msg WHERE id>2 AND id<5` 

  示例：查询表中全部字段，排序按照age大小排序并倒序 `SELECT * FROM msg ORDER BY age DESC `

  示例：查询表中name字段，并只展示2条 `SELECT name FROM msg LIMIT 2`

**总结梳理**：

1. `WHERE`是判断条件，后面跟查询条件  可以使用多个条件，中间加 （或`OR`）或者（并且`AND`）判断
2. `ORDER BY` 是排序方式，后面接字段，按照相应字段进行排序
3. `DESC`是否倒叙，直接使用即可
4. `LIMIT`查询个数，后面跟数字即可



小提示：sql查询语句一般是大写的，实际不区分大小写。

​				key一般用``来包裹（num类型用不用也可以）

​				value用' '来包裹，（num类型用不用都可以）

​				不使用where查询条件，默认操作全部。如删除不增加where，直接删除表全部内容

​				筛选条件是可以拼接的

### PHP操作MySQL数据库

在预先创建好数据库，表之后，就可以通过php操作数据库了

- 连接（到数据库）
  - 提供数据库信息（主机、账号、密码、数据库名称）
- 读写（数据）
  - 通过 SQL 语句（增删改查）
- 关闭（连接）
  - 会自动关闭，基本不用我们自己写



#### 我们这里常用的方式PDO操作mysql

[官方文档](https://www.php.net/manual/zh/book.pdo.php)

这里一般我们常用的只有三个操作，其他的操作以后可以通过框架来进行操作

1. `PDO::__construct`  —  创建一个数据库链接实例
2. `PDO::prepare` — 准备要执行的语句，并返回语句对象
3. `PDOStatement::execute` — 执行一条预处理语句
4. `PDOStatement::fetchAll`— 返回一个包含结果集中所有行的数组

```php
<?php
//链接数据库
$dsn = 'mysql:dbname=php01;host=127.0.0.1'; //链接的数据库类型，库名，host地址
$pdo = new PDO($dsn,'root','root'); //库基本信息，数据库账号，密码

/*******写数据（增删改，都算写）以删为例***********/
// $sql = "INSERT INTO msg (name,age) VALUES ('vicer',18)";
// $sth = $pdo->prepare($sql);   //准备执行语句
// $sth->execute();              //执行对象


/*******读数据***********/
$sql = "select * from msg";
$sth = $pdo->prepare($sql);
$sth->execute();

$result = $sth->fetchAll();		//获取表中所有数组

var_dump($result[0]['name']);
?>
```

#### 使用表单提交到数据库

提交表单的html代码

```php
//myForm.php 
  <form action="getForm.php" method="POST">
    姓名：<input type="text" name="name"><br>
    年龄：<input type="text" name="age"><br>
    <input type="submit" value="提交">
  </form>
```

接收数据的代码

```php
$name = $_POST['name'];		//接收传递过来的post请求
$age = $_POST['age'];


$dsn = 'mysql:dbname=myform;host=127.0.0.1';		//链接数据库
$pdo = new PDO($dsn,'root','root');

$sql = "INSERT INTO msg (name,age,content) VALUES ('{$name}','{$age}','{$content}')";	//注意插入语句，要用点包裹'变量名'。因为这里是直接做为字符串来运行的

//echo $sql;		可以打印一下，成功得到里面的值代表变量插入成功

$sth = $pdo->prepare($sql);		
$sth->execute();

header('location:myForm.php');		//跳转回我们的网页
```



#### 数据校验

我们在接收数据的时候，有可能会判断数据是否符合我们的要求，可以进行如下判断

```	php
<?php

//trim()   去除两边空格
$name = trim($_POST['name']);
$age = trim($_POST['age']);
$content = trim($_POST['content']);
//exit;    停止运行
if( $name ==='' || $age === ''){
  echo '名字和年龄不能为空';
  exit;
}

$dsn = 'mysql:dbname=myform;host=127.0.0.1';
$pdo = new PDO($dsn,'root','root');

$sql = "INSERT INTO msg (name,age,content) VALUES ('${name}','{$age}','{$content}')";
//去除中文乱码
$pdo->query("set names utf8");
$sth = $pdo->prepare($sql);


$sth->execute();

header('location:myForm.php');
```

这里我们多了如下操作

* `trim()`   去除两边空格
* `exit;`    停止运行
* `$pdo->query("set names utf8");`	[去除中文乱码](https://blog.csdn.net/ysydao/article/details/11002295)

#### 读取并展示数据

当我们存储到数据库中，如何把数据展示到网页中呢？

首先定义一个公共的连接数据库文件，引入到页面中

```php
//db.php
<?php
$dsn = 'mysql:dbname=myform;host=127.0.0.1';
$pdo = new PDO($dsn,'root','root');
?>
```

在静态页中链接并查找数据库，展示时注意内容进行中文编码

```php
<?php
include('./db.php');			//引入数据库
$pdo->query("set names utf8");		//进行中文编码

$sql1 = 'SELECT * FROM msg  ORDER BY name';
$sth = $pdo->prepare($sql1);
$sth->execute();

$result = $sth->fetchAll();				//获取表中数据并保存到变量中
?>


<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
  .showData {
    display: flex;
  }

  .showData div {
    flex: 1;
  }

  .showData p {
    border: 1px solid violet;
    padding: 2px 5px;
  }
  </style>
</head>

<body>

  <div>

  </div>

  <form action="getForm.php" method="POST">
    姓名：<input type="text" name="name"><br>
    年龄：<input type="text" name="age"><br>
    内容：<textarea name="content"></textarea><br>
    <input type="submit" value="提交">
  </form>

  <div class="showData">

    <?php
      foreach ($result as $key => $value) {		//遍历数据
    ?>														//展示数据时注意$result是二维数组，即 $result = [[$name=>'vicer'],[],[]];

    <div>
      <p>name:<? echo $value['name'] ?></p>		
      <p>age:<? echo $value['age'] ?></p>
      <p>content:<? echo $value['content'] ?></p>
    </div>

    <?php				//将想要循环的hmlt代码用回括号}包裹进来
      }
    ?>
  </div>

</body>

</html>
```



## 函数

函数定义：大部分同JS相同，这里我们只记录几个不同之处

### 函数默认值

在php中，和ES6一样，函数的参数可以传递默认值

```php
function test($a,$b=10){
  return "$a,$b";
}
$result =  test(1);
echo $result;			//1,10
```

### 函数的全局与局部变量

在js中我们都知道，全局变量在函数内外都可调用，而在php中不同：
**全局变量只能在全局中调用**，**而局部变量只能在函数内调用**，如果函数内想要调用全局变量，可以通过函数的参数传递，来进行赋值