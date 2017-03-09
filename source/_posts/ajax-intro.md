---
title: AJAX 入门
date: 2016-05-09 
tags: ajax
published: true
---

![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/093610991.jpg)

# AJAX 介绍



`AJAX` = `Asynchronous JavaScript and XML`（异步的 `JavaScript` 和 `XML`）。
`AJAX` `是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
学习`AJAX`需要了解一些 HTML/XHTML，CSS，JavaScript/DOM方面的知识。

<!-- more -->


`AJAX` 工作原理：


<center>![head](![ajax.gif-9.1kB][1])</center>




`XMLHttpRequest` 是 `AJAX` 的基础。
`XMLHttpRequest` 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

创建 `XMLHttpRequest` 对象的语法：
`variable=new XMLHttpRequest();`


如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open() 和 send() 方法：
`xmlhttp.open(method,url,async);`

规定请求的类型、URL 以及是否异步处理请求。

* method：请求的类型；GET 或 POST
* url：文件在服务器上的位置
* async：true（异步）或 false（同步）



`xmlhttp.send(string);`
将请求发送到服务器。

* string：仅用于 POST 请求

与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
然而，在以下情况中，请使用 POST 请求：

* 无法使用缓存文件（更新服务器上的文件或数据库）
* 向服务器发送大量数据（POST 没有数据量限制）
* 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠


XMLHttpRequest 对象如果要用于 AJAX 的话，其 open() 方法的 async 参数必须设置为 true：
`xmlhttp.open("GET","ajax_test.html",true);`

通过 AJAX，JavaScript 无需等待服务器的响应，而是：

* 在等待服务器响应时执行其他脚本
* 当响应就绪后对响应进行处理


如需获得来自服务器的响应，请使用 XMLHttpRequest 对象的 responseText （获得字符串形式的响应数据）或 responseXML （获得 XML 形式的响应数据）属性。


AJAX 可用来与数据库或者XML文件进行动态通信。

**实例：**

用`AJAX`从XML文件返回数据：


```html
<html><!DOCTYPE html>
<html>
<head>
<script>
function loadXMLDoc(url)
{
var xmlhttp;
var txt,x,xx,i;
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.onreadystatechange=function()
  {
  if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    txt="<table border='1'><tr><th>Title</th><th>Artist</th></tr>";
    x=xmlhttp.responseXML.documentElement.getElementsByTagName("CD");
    for (i=0;i<x.length;i++)
      {
      txt=txt + "<tr>";
      xx=x[i].getElementsByTagName("TITLE");
        {
        try
          {
          txt=txt + "<td>" + xx[0].firstChild.nodeValue + "</td>";
          }
        catch (er)
          {
          txt=txt + "<td>&nbsp;</td>";
          }
        }
      xx=x[i].getElementsByTagName("ARTIST");
        {
        try
          {
          txt=txt + "<td>" + xx[0].firstChild.nodeValue + "</td>";
          }
        catch (er)
          {
          txt=txt + "<td>&nbsp;</td>";
          }
        }
      txt=txt + "</tr>";
      }
    txt=txt + "</table>";
    document.getElementById('txtCDInfo').innerHTML=txt;
    }
  }
xmlhttp.open("GET",url,true);
xmlhttp.send();
}
</script>
</head>
<body>

<div id="txtCDInfo">
<button onclick="loadXMLDoc('cd_catalog.xml')">Get CD info</button>
</div>

</body>
</html>
```

测试所用xml文件在[这里][2]。


当用户点击上面的"获得 CD 信息"这个按钮，就会执行 loadXMLDoc() 函数。
loadXMLDoc() 函数创建 XMLHttpRequest 对象，添加当服务器响应就绪时执行的函数，并将请求发送到服务器。
当服务器响应就绪时，会构建一个 HTML 表格，从 XML 文件中提取节点（元素），最后使用已经填充了 XML 数据的 HTML 表格来更新 txtCDInfo 占位符。




  [1]: http://static.zybuluo.com/spikett/ev01i2s5lwd3cbceh9cop28c/ajax.gif
  [2]: http://www.runoob.com/try/demo_source/cd_catalog.xml