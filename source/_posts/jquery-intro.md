---
title: jQuery 入门
date: 2016-05-05 
tags: jQuery
---

![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/101018642.png)


`jQuery` 是一个 `JavaScript` 函数库。
`jQuery` 极大地简化了 `JavaScript` 编程。
`jQuery` 库可以通过一行简单的标记被添加到网页中。

学习`jQuery`之前需要了解一些 HTML/CSS 和 JS 部分的相关内容。


<!-- more -->

## 安装 jQuery

有两个版本的 jQuery 可供下载：

* Production version - 用于实际的网站中，已被精简和压缩。
* Development version - 用于测试和开发（未压缩，是可读的代码）。

以上两个版本都可以从 [jquery.com][1] 中下载。现在最新的版本是3.0.0，在引用文件时注意。

jQuery 库是一个 JavaScript 文件，您可以使用 HTML 的 `<script>` 标签引用它：

```
<head>
<script src="jquery-1.10.2.min.js"></script>
</head>
```

将下载的文件放在网页的同一目录下，就可以使用jQuery。

如果您不希望下载并存放 jQuery，那么也可以通过 CDN（内容分发网络） 引用它。

百度、又拍云、新浪、谷歌和微软的服务器都存有 jQuery 。
如果你的站点用户是国内的，建议使用百度、又拍云、新浪等国内CDN地址，如果你站点用户是国外的可以使用谷歌和微软。

## jQuery 语法

通过 `jQuery`，您可以选取（查询，query） HTML 元素，并对它们执行"操作"（actions）。

`jQuery` 语法是通过选取 HTML 元素，并对选取的元素执行某些操作。


基础语法： `$(selector).action()`


**实例:**

`$(this).hide()` - 隐藏当前元素
`$("p").hide()` - 隐藏所有 `<p>` 元素
`$("p.test").hide()` - 隐藏所有 class="test" 的 `<p>` 元素
`$("#test").hide()` - 隐藏所有 id="test" 的元素


所有 jQuery 函数位于一个 document ready 函数中：

```
$(document).ready(function(){

   // jQuery methods go here...

});
```
`jQuery` 选择器允许您对 HTML 元素组或单个元素进行操作。

`jQuery` 选择器基于元素的 id、类、类型、属性、属性值等"查找"（或选择）HTML 元素。 它基于已经存在的 CSS 选择器，除此之外，它还有一些自定义的选择器。


如果您的网站包含许多页面，并且您希望您的 jQuery 函数易于维护，那么请把您的 jQuery 函数放到独立的 `.js` 文件中。像这样：


```
<head>
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js">
</script>
<script src="my_jquery_functions.js"></script>
</head>
```

## jQuery 事件 


`jQuery` 是为事件处理特别设计的。

页面对不同访问者的响应叫做事件。
事件处理程序指的是当 HTML中发生某些事件(比如在元素上移动鼠标，选取单选按钮或点击元素)时所调用的方法。



## jQuery 效果 

* 隐藏/显示 -- hide()/show()
* 淡入淡出 -- fadeIn()/fadeOut()
* 滑动 -- slideDown()/slideUp()
* 动画 -- animate()
`$(selector).animate({params},speed,callback);`

必需的 params 参数定义形成动画的 CSS 属性。
可选的 speed 参数规定效果的时长。它可以取以下值："slow"、"fast" 或毫秒。
可选的 callback 参数是动画完成后所执行的函数名称。


## jQuery 捕获设置


jQuery 中非常重要的部分，就是操作 DOM 的能力。
jQuery 提供一系列与 DOM 相关的方法，这使访问和操作元素和属性变得很容易。


三个简单实用的用于 DOM 操作的 jQuery 方法：


* text() - 设置或返回所选元素的文本内容
* html() - 设置或返回所选元素的内容（包括 HTML 标记）
* val() - 设置或返回表单字段的值

以上三个函数也可以用来设置内容和属性。


## jQuery 添加删除元素

* append() - 在被选元素的结尾插入内容
* prepend() - 在被选元素的开头插入内容
* after() - 在被选元素之后插入内容
* before() - 在被选元素之前插入内容
* remove() - 删除被选元素（及其子元素）
* empty() - 从被选元素中删除子元素



## jQuery 操作 CSS

addClass() - 向被选元素添加一个或多个类
removeClass() - 从被选元素删除一个或多个类
toggleClass() - 对被选元素进行添加/删除类的切换操作
css() - 设置或返回样式属性

## jQuery 遍历

jQuery 遍历，意为"移动"，用于根据其相对于其他元素的关系来"查找"（或选取）HTML 元素。以某项选择开始，并沿着这个选择移动，直到抵达您期望的元素为止。

下图展示了一个家族树。通过 jQuery 遍历，您能够从被选（当前的）元素开始，轻松地在家族树中向上移动（祖先），向下移动（子孙），水平移动（同胞）。这种移动被称为对 DOM 进行遍历。


![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/101438646.png)


## jQuery - AJAX



AJAX = 异步 JavaScript 和 XML（Asynchronous JavaScript and XML）。


简短地说，在不重载整个网页的情况下，AJAX 通过后台加载数据，并在网页上进行显示。

jQuery 提供多个与 AJAX 有关的方法。
通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON - 同时您能够把这些外部数据直接载入网页的被选元素中。


只需要一行简单的代码，jQuery 就可以实现 AJAX 功能。


jQuery load() 方法是简单但强大的 AJAX 方法。
load() 方法从服务器加载数据，并把返回的数据放入被选元素中。

语法：
`$(selector).load(URL,data,callback);`

必需的 URL 参数规定您希望加载的 URL。（因为浏览器的安全策略，测试时可以使用`"http://localhost/jquery/demo_test.txt"`，并使用chrome插件“allow-control-allow-origin”，还有其他方法可以实现跨域访问，这里不多说了）
可选的 data 参数规定与请求一同发送的查询字符串键/值对集合。
可选的 callback 参数是 load() 方法完成后所执行的函数名称。


jQuery get() 和 post() 方法用于通过 HTTP GET 或 POST 请求从服务器请求数据。

 - GET - 从指定的资源请求数据
 - POST - 向指定的资源提交要处理的数据

GET 基本上用于从服务器获得（取回）数据。注释：GET 方法可能返回缓存数据。
POST 也可用于从服务器获取数据。不过，POST 方法不会缓存数据，并且常用于连同请求一起发送数据。


语法：
`$.get(URL,callback);`
`$.post(URL,data,callback);`

## jQuery 插件


jQuery还有很多插件，可以实现各种各样的功能，比如折叠菜单，密码验证，表单处理等。可以根据需要调用。

常用的几个插件：

jQuery Validate -- 表单验证
jQuery Accordion -- 折叠菜单
jQuery Autocomplete -- 根据用户输入值进行搜索和过滤

更多实用的插件可以访问[jq22.com][3]。


## jQuery UI

[jQuery UI][4] 是建立在 jQuery JavaScript 库上的一组用户界面交互、特效、小部件及主题。

jQuery UI 是 jQuery 项目组中对桌面端的扩展，包括了丰富的控件和特效，与 jQuery 无缝兼容。

还有很多其他库，比如[Ext JS][5]（重量级，内容巨多，但多付费内容），[Dojo][6](稍微轻量级一点，内容也很多，效果赞)，[YUI][7]（前端框架鼻祖，但随着Yahoo的没落，比较不大行了）等），[Dojo][6]，[YUI][7]等。



  [1]: http://jquery.com/download/
  [3]: http://www.jq22.com/
  [4]: http://jqueryui.com/
  [5]: http://extjs.org.cn/
  [6]: http://dojotoolkit.org/
  [7]: http://yuilibrary.com/