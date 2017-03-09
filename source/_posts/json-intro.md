---
title: JSON 文件入门
date: 2016-06-01 
tags: json
---

![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/101842293.png)


JSON: JavaScript Object Notation (JavaScript 对象表示法)

JSON 是存储和交换文本信息的语法。类似 XML。
JSON 比 XML 更小、更快，更易解析。

<!-- more -->

JSON和XML的相同和不同之处：

| 相同之处        | 不同之处           |
| :-------------: |:-------------:| 
| 纯文本     | 没有结束标签 |
| 具有"自我描述性"（人类可读）      | 更短      |  
| 具有层级结构（值中存在值） | 读写的速度更快      | 
|可通过 JavaScript 进行解析|能够使用内建的 JavaScript eval() 方法进行解析|
|数据可使用 AJAX 进行传输|使用数组|
||不使用保留字|




JSON 实例:


```
{
"employees": [
{ "firstName":"John" , "lastName":"Doe" }, 
{ "firstName":"Anna" , "lastName":"Smith" }, 
{ "firstName":"Peter" , "lastName":"Jones" }
]
}
```
在上面的实例中，对象 "employees" 是包含三个对象的数组。每个对象代表一条关于某人（有姓和名）的记录。



JSON 独立于语言，JSON 使用 Javascript语法来描述数据对象，但是 JSON 仍然独立于语言和平台。JSON 解析器和 JSON 库支持许多不同的编程语言。 目前非常多的动态（PHP，JSP，.NET）编程语言都支持JSON。



JSON 文本格式在语法上与创建 JavaScript 对象的代码相同。
由于这种相似性，无需解析器，JavaScript 程序能够使用内建的 `eval()` 函数，用 JSON 数据来生成 **原生的** JavaScript 对象。


`JSON 语法`


JSON 语法是 JavaScript 对象表示法语法的子集。
`"firstName" : "John"`


等价于js中的
`firstName = "John"`




JSON 最常见的用法之一，是从 web 服务器上读取 JSON 数据（作为文件或作为 HttpRequest），将 JSON 数据转换为 JavaScript 对象，然后在网页中使用该数据。


实例：

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>json教程</title>
</head>
<body>
<h2>从 JSON 字符串中创建对象</h2>

<p>
名: <span id="fname"></span><br> 
姓: <span id="lname"></span><br> 
</p> 

<script>
var txt = '{"employees":[' +
'{"firstName":"John","lastName":"Doe" },' +
'{"firstName":"Anna","lastName":"Smith" },' +
'{"firstName":"Peter","lastName":"Jones" }]}';

var obj = eval ("(" + txt + ")");

document.getElementById("fname").innerHTML=obj.employees[1].firstName 
document.getElementById("lname").innerHTML=obj.employees[1].lastName 
</script>

</body>
</html>

```


使用 JSON 解析器将 JSON 转换为 JavaScript 对象是更安全的做法。JSON 解析器只能识别 JSON 文本，而不会编译脚本。


```html
<!DOCTYPE html>
<html>
<body>
<h2>Create Object from JSON String</h2>
<p>
First Name: <span id="fname"></span><br> 
Last Name: <span id="lname"></span><br> 
</p> 
<script>
var txt = '{"employees":[' +
'{"firstName":"John","lastName":"Doe" },' +
'{"firstName":"Anna","lastName":"Smith" },' +
'{"firstName":"Peter","lastName":"Jones" }]}';

obj = JSON.parse(txt); //使用json解析器

document.getElementById("fname").innerHTML=obj.employees[1].firstName 
document.getElementById("lname").innerHTML=obj.employees[1].lastName 
</script>
</body>
</html>
```

在浏览器中，提供了原生的 JSON 支持，而且 JSON 解析器的速度更快。


`JSONP`

Jsonp (JSON with Padding) 是 JSON 的一种"使用模式"，可以让网页从别的域名那获取资料，即跨域读取数据。这里有兴趣可以去看一下浏览器的“[同源策略][1]”，就知道JSONP的意义了。

JSONP易于实现，但是也会存在一些安全隐患，如果第三方的脚本随意地执行，那么它就可以篡改页面内容，截获敏感数据。但是在受信任的双方传递数据，JSONP是非常合适的选择。


JSONP 实例：


假设客户端想要访问：[链接数据][2]。

期望返回JSON数据：["customername1","customername2"]。

服务端文件jsonp.php代码为：


```php
<?php
header('Content-type: application/json');
//获取回调函数名
$jsoncallback = htmlspecialchars($_REQUEST ['jsoncallback']);
//json数据
$json_data = '["customername1","customername2"]';
//输出jsonp格式的数据
echo $jsoncallback <?php
header('Content-type: application/json');
//获取回调函数名
$jsoncallback = htmlspecialchars($_REQUEST ['jsoncallback']);
//json数据
$json_data = '["customername1","customername2"]';
//输出jsonp格式的数据
echo $jsoncallback . "(" . $json_data . ")";
?>. "(" . $json_data . ")";
?>
```




客户端使用jQuery并使用JSONP访问客户端数据代码如下：


```html
<!DOCTYPE html>
<html>
<head>
	<title>JSONP usage</title>
	<script src="http://apps.bdimg.com/libs/jquery/1.8.3/jquery.js"></script>	
</head>
<body>
<div id="divCustomers"></div>
<script>
$.getJSON("http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=?", function(data) {
	
	var html = '<ul>';
	for(var i = 0; i < data.length; i++)
	{
		html += '<li>' + data[i] + '</li>';
	}
	html += '</ul>';
	
	$('#divCustomers').html(html); 
});
</script>
</body>
</html> 
```

以上实例是利用getJSON来实现的，地址参数中添加"callback=?"即可。

还可以用ajax函数中实现，比如：

```
$.ajax({
    datatype: "jsonp",
    url: ,
    succes: function(data){
    //处理data数据
    }
});
```


`PHP JSON`


下面介绍PHP 语言来编码和解码 JSON 对象。

在 php5.2.0 及以上版本已经内置 JSON 扩展。

|函数|描述|
|:----:|:----:|
|json_encode|对变量进行json编码|
|json_decode|对json格式的字符串进行解码，转换成php变量|
|json_last_error|返回最后发生的错误|


将 PHP 数组转换为 JSON 格式数据：

```
<?php
   $arr = array('a' => 1, 'b' => 2, 'c' => 3, 'd' => 4, 'e' => 5);
   echo json_encode($arr);
?>
```

执行结果为：

`{"a":1,"b":2,"c":3,"d":4,"e":5}`


将 PHP 对象转换为 JSON 格式数据：


```php
<?php
   class Emp {
       public $name = "";
       public $hobbies  = "";
       public $birthdate = "";
   }
   $e = new Emp();
   $e->name = "sachin";
   $e->hobbies  = "sports";
   $e->birthdate = date('m/d/Y h:i:s a', strtotime("8/5/1974 12:20:03"));

   echo json_encode($e);
?>
```


PHP json_decode() 函数用于对 JSON 格式的字符串进行解码，并转换为 PHP 变量。代码如下：


```php
<?php
   $json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';

   var_dump(json_decode($json));  //返回对象
   var_dump(json_decode($json, true));  //返回数组
?>
```

---


  [1]: http://www.jianshu.com/p/4e17445d66e2
  [2]: http://www.runoob.com/try/ajax/jsonp.php?jsonp=callbackFunction