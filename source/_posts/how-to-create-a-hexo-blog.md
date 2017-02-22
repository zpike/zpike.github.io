---
title: Hexo 搭建笔记
date: 2016-07-07 16:07:16
tags: hexo
---


很久以前看过阮一峰的介绍Jekyll做个人博客的文章，那时候还没用Mac，所以也没法尝试。现在用了Mac之后想起来了，不过写博客还是挺费力的，没啥干货也不好意思出来唠叨。尽量写点有趣又有科技含量的东西吧，逼着自己更新知识库还是不错的。

Jekyll的官网虽然做得不错，但是流程我还没看明白，比Hexo的说明要复杂。所以下面就从弄Hexo博客开始吧。

<!-- more -->

这几天折腾了一个Hexo博客，并且部署到github和coding，而且租了个服务器，弄个了个人域名，过程中几个小问题搜了很多文章，但是也没能解决我的问题，还是挺费劲的。第一篇文章就用来记录这个过程我遇到的问题吧。

网上信息比较多，很多过期的文章，软件版本更新之后就不大能用了，或者Win下Mac下的区别较多，给新手会造成不必要的误解。

官网可以查到的东西我就不再写了，毕竟没有必要重新造轮子。

---


Hexo是一个台湾人弄的，在twitter上面我关注了下，头像还挺萌。希望可以一直更新吧，毕竟用起来整体感觉还不错，轻快简单。很多人推荐使用NexT主题，搜了一下发现好多人用这个主题，瞬间感觉没啥特点了。所以要想独特一点，有个性一点还是自己找个冷门点的主题改改，或者自己弄个主题，过程也不大复杂，专注于写作就行，主题基本功能都有就行。

Hexo安装依赖于node.js，npm安装比较不稳定，开个VPN比较好一点。

Hexo官网上面的文档挺全的，需要补充的就是主题的设置，还有网站的第三方服务的设置。

---

Hexo的配置都在`_config.yml`中，里面注释比较详细，不懂的查下官网，一般都知道怎么设置。需要注意的是文件类型是yml，语法要求比较严格，开头冒号后面必须空一格，不然不能识别。语法支持好的比如vim或者sublime，弄个插件，一般都能很好支持yml语法。还有这个文件要和主题目录下的同名文件相互区别，不要弄混淆了。

具体设置，比如language，中文简体的代码是`zh-hans`，其他设置试一下就知道了，一般默认就ok了。头像，logo什么的，弄个url比较好，或者放到Hexo的source目录下，这样`Hexo g` 的时候才能不丢失。

配置测试ok了，然后就可以写blog了。`Hexo new` 加文件名即可。这样就在source文件夹下新建了一个文件，写好标签和分类，就可以写正文了。网上有教你Hexo new之后直接打开编辑器的教程，这样就更方便了。直接在sublime中或者其他编辑器中写文章。需要注意的是Win下目录设置的问题。类似这样`D:\\Program Files\\Sublime Text 3\\...`。

这里推荐使用cmd markdown在线写文章，然后导出md格式，再稍微修改即可发布了。sublime的markdown还是不大易用和方便。

写文章不满意，当然也可以删掉，但是Hexo没有提供删除文章的命令，所以手动删掉也行，不过我测试了一下，在删光文章的时候Hexo g会出错，一定要Hexo new一篇文章才能进行Hexo g。

再然后就是配置主题什么的了。建议看看NexT主题的官网，里面有详细的教程，非常实用，很多官网没看明白的东西可以在那边看看。比如字体，图标，第三方服务什么的。



---

最后写好文章就可以部署到pages上去了。github或者coding都不错，国内建议用coding，速度很快。不过很多人遇到Hexo d的时候不成功的问题，我也被这个问题折磨了很久，网上很多回答都没有解决我的问题。

其实直接将public中的生成文件push到pages就行了，只是这样不优雅，比较费劲。所以还是希望一条命令（`Hexo g -d`）解决部署，这样才够爽。

首先想要部署到github需要安装一个插件，`Hexo-deployer-git`，这个插件里面的文档作用比各种网上的文件要有用太多，建议看看。最终解决我的Hexo d的问题就是看了那个文档。因为我是Win下Git bash环境，所以Hexo d会出现问题，配置文件中需要这样写：


```yml
# Deployment
## Docs: https://Hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:name/name.github.io.git
  #repo: https://github.com/name/name.github.io.git
  branch: master
  user: your_github_username
  email: your_email_address
```


当然前提是你ssh密钥以及github或者coding上主页都弄好了，具体看下各个网站的详细说明，coding上比较容易，github比较pro，都过一遍流程就更熟悉了。


---

最后列一下这个过程中用到的网站，方便查询。

* [Hexo官网文档](https://Hexo.io/zh-cn/docs/)
* [NexT主题官方网站详细配置](http://theme-next.iissnan.com/getting-started.html) 
* [Coding Pages使用文档](https://coding.net/help/doc/pages/index.html)
* [Hexo怎么优雅地插入图片](http://huangming.work/2016-05-02-Hexo-img-solution.html)
* [Hexo添加文章时自动打开编辑器](http://notes.wanghao.work/2015-06-29-Hexo%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E6%97%B6%E8%87%AA%E5%8A%A8%E6%89%93%E5%BC%80%E7%BC%96%E8%BE%91%E5%99%A8.html)
* [多说评论](http://duoshuo.com/)
* [Hexo进阶](http://www.twalksong.com/2015/09/12/Hexo%E8%BF%9B%E9%98%B6/)

还有图床可以用iPic，mac上面很好用。









