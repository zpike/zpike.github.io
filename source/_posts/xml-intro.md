
---
title: XML 文件介绍
date: 2016-05-01 
tags: xml
---

![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/094640412.jpg)

`XML`(`Extensible Markup Language`)是一套资料存储工具，可以用來建立包含结构化格式资料的文件。除了资料之外，还可以包含一組定义资料架构的详细规则。这些规则是由 XML 文件的作者负责定义。


<!-- more -->


## XML基础

`XML` 指可扩展标记语言，XML的目的是为了传输数据的内容，重点是数据内容，而HTML是用来显示数据，重点是数据的外观。XML文件结构如下：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```

上面的这条便签具有自我描述性。它包含了发送者和接受者的信息，同时拥有标题以及消息主体。
但是，这个 XML 文档仍然没有做任何事情。它仅仅是包装在 XML 标签中的纯粹的信息。我们需要编写软件或者程序，才能传送、接收和显示出这个文档。

上面实例中的标签没有在任何 XML 标准中定义过（比如 `<to>` 和 `<from>`）。这些标签是由 XML 文档的创作者发明的。这是因为 XML 语言没有预定义的标签。
而HTML 中使用的标签都是预定义的。HTML 文档只能使用在 HTML 标准中定义过的标签（如 `<p>`、`<h1>` 等等）。
XML 允许创作者定义自己的标签和自己的文档结构。


XML 是独立于软件和硬件的信息传输工具。XML是各种应用程序之间进行数据传输的最常用的工具。

XML 数据以纯文本格式进行存储，因此提供了一种独立于软件和硬件的数据存储方法。这让创建不同应用程序可以共享的数据变得更加容易。

## XML 文件结构

XML 文档形成了一种树结构，它从"根部"开始，然后扩展到"枝叶"。

结构如下：
```
<root>
<child>
<subchild>.....</subchild>
</child>
</root>
```

XML 文档必须包含根元素。该元素是所有其他元素的父元素。

XML 文档中的元素形成了一棵文档树。这棵树从根部开始，并扩展到树的最底端。

XML文件结构如下：


![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/095647403.gif)


示例：

```
<bookstore>

<book category="COOKING">
<title lang="en">Everyday Italian</title>
<author>Giada De Laurentiis</author>
<year>2005</year>
<price>30.00</price>
</book>

<book category="CHILDREN">
<title lang="en">Harry Potter</title>
<author>J K. Rowling</author>
<year>2005</year>
<price>29.99</price>
</book>

<book category="WEB">
<title lang="en">Learning XML</title>
<author>Erik T. Ray</author>
<year>2003</year>
<price>39.95</price>
</book>

</bookstore>
```



实例中的根元素是 `<bookstore>`。文档中的所有 `<book>` 元素都被包含在`<bookstore>` 中。
`<book>` 元素有 4 个子元素：`<title>`、`<author>`、`<year>`、`<price>`。


所有的 XML 元素都必须有一个关闭标签，在 XML 中，省略关闭标签是非法的。

在 XML 中，所有元素都必须彼此正确地嵌套：
`<b><i>This text is bold and italic</i></b>`
在上面的实例中，正确嵌套的意思是：由于 `<i>` 元素是在 `<b>` 元素内打开的，那么它必须在 `<b>` 元素内关闭。


## 命名空间

XML 文档经常有一个对应的数据库，其中的字段会对应 XML 文档中的元素。有一个实用的经验，即使用数据库的命名规则来命名 XML 文档中的元素。

不同xml文件中可能有相同的命名，引用时容易冲突，需要在命名之前添加标记，以示区别。

## XML元素vs属性

在XML中也可以像HTML中那样使用属性，但是更多的是使用元素，尽量避免使用属性，以提高文件的易用性。属性应该用来提供与数据无关的信息。

元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。



## 语法规则：

 - XML文档必须有一个根元素 
 - XML元素都必须有一个关闭标签 
 - XML标签对大小写敏感 
 - XML元素必须被正确的嵌套 
 - XML属性值必须加引号


## XSLT

XSL 指扩展样式表语言（EXtensible Stylesheet Language）, 它是一个 XML 文档的样式表语言。

通过使用 XSLT，您可以把 XML 文档转换成 HTML 格式。
XSLT 是在浏览器显示 XML 文件之前，先把它转换为 HTML。


## XML DOM

XML DOM定义访问和操作XML文档的标准方法。

所有元素可以通过DOM树来访问。可以修改或删除它们的内容，并创建新的元素。元素，它们的文本，以及它们的属性，都被认为是节点。


DOM中的 XMLHttpRequest函数非常有用。 

XMLHttpRequest 对象用于在后台与服务器交换数据。主要作用包括：

- 在不重新加载页面的情况下更新网页
- 在页面已加载后从服务器请求数据
- 在页面已加载后从服务器接收数据
- 在后台向服务器发送数据

更多XMLHttpRequest应用需学习XML DOM方面的知识。

所有现代浏览器都有内建的 XMLHttpRequest 对象。

创建 XMLHttpRequest 对象的语法：
`xmlhttp=new XMLHttpRequest();`

所有现代浏览器都有内建的 XML 解析器。

XML解析器把XML文档转换为XML DOM对象，然后就可以通过 JavaScript 操作的对象。

下面的实例把 XML 文档（"note.xml"）解析到 XML DOM 对象中，然后通过 JavaScript 提取一些信息：

```xml
<!DOCTYPE html>
<html>
<body>
<h1>W3Cschool Internal Note</h1>
<div>
<b>To:</b> <span id="to"></span><br>
<b>From:</b> <span id="from"></span><br>
<b>Message:</b> <span id="message"></span>
</div>

<script>
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.open("GET","note.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML;

document.getElementById("to").innerHTML=
xmlDoc.getElementsByTagName("to")[0].childNodes[0].nodeValue;
document.getElementById("from").innerHTML=
xmlDoc.getElementsByTagName("from")[0].childNodes[0].nodeValue;
document.getElementById("message").innerHTML=
xmlDoc.getElementsByTagName("body")[0].childNodes[0].nodeValue;
</script>

</body>
</html>


```


如需从上面的 XML 文件（"note.xml"）的 `<to>` 元素中提取文本 "Tove"，语法是：
`getElementsByTagName("to")[0].childNodes[0].nodeValue`
请注意，即使 XML 文件只包含一个 `<to>` 元素，您仍然必须指定数组索引 [0]。这是因为 getElementsByTagName() 方法返回一个**数组**。






## XML 编码

XML文档可以包含非ASCII字符，为了避免错误，需要规定 XML 编码，或者将XML文件存为Unicode。

中文支持的的编码一般用utf-8:
`<?xml version="1.0" encoding="UTF-8"?>`




## XML 总结


* XML 可用于交换、共享和存储数据。
* XML 文档形成树状结构，在"根"和"叶子"的分支机构开始的。
* XML 有非常简单的语法规则。带有正确语法的 XML 是"形式良好"的。有效的 XML 是针对 DTD 进行验证的。
* XSLT 用于把 XML 转换为其他格式，比如 HTML。
所有现代的浏览器有一个内建的 XML 解析器，可读取和操作 XML。
* DOM 定义了一个访问 XML 的标准方式。
* XMLHttpRequest 对象提供了一个网页加载后与服务器进行通信的方式。
* XML 命名空间提供了一种避免元素命名冲突的方法。


---

