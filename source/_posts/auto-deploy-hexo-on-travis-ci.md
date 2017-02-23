---
title: 使用 Travis-CI 来自动化部署 Hexo
date: 2017-02-23 18:45:43
tags: hexo travis
---



## 前言

之前写过一个 [Hexo 博客的搭建方法][1]，搭建好之后我就没有怎么写博客了。一来是因为自己没什么好分享的（其实就是懒哈），二来是感觉静态技术博客这种形式已经落伍了，如果要学某种技术，还是直接找视频教程比较快。

不过因为知道了“持续集成”，即 `Continuous integration`，这种技术已经很常见了，不过我最近才学习到。用在 hexo 博客的部署上比较合适，整个部署也比较简单，适合我这种小白。


<!-- more -->


之前部署 hexo 博客，只要在终端输入 `hexo g -d`即可，当然前提是你已经写好文章了呀，写不出文章的话我是帮不了的。而且已经搭建好博客环境，即 node.js 相关及 Hexo 本身。那换成 `Travis CI` 的持续集成服务到底有什么用呢？操作会不会更简单？答案是-----不会！

那TM要它有什么用？！

且听我慢慢说。


## 什么叫持续集成

网上很多解释，感兴趣的可以看看阮一峰的[这篇文章][2]。

概括起来讲，就是一个外包公司，博主只管写好文章，然后丢给它去处理，它就自动给你各种加工渲染，然后放到博客页面上去。你只需在有网络的地方即可开始写作，当然也要能无障碍访问 GitHub 才行啊（关于如何更快访问墙外网络，我会在之后一篇文章说明）。直接登录 GitHub 账号写东西，实在不够优雅，所以还要弄个 Markdown 编辑器，本地或者基于 Web 的都可以（Mac 推荐用 `Bear`，web的就用 `Cmd Markdown`），写完直接 push 到 GitHub 即可，其他各种库、主题什么的更新都不用管了。这才是新时代的方法----只要提出需求，然后交给“云”处理。


## Travis-CI 介绍

`Travis CI` 是目前新兴的开源持续集成构建项目，用来构建托管在GitHub上的代码。简单来说，`Travis CI`原理就是当你每次提交 `commit` 到在 `GitHub` 后，它会自动检测你的提交，同时根据的配置文件，生成一个 Linux 虚拟机来运行你的命令，通常这些命令用于测试，构建等。在我们的要求下，就可以用它运行一些`hexo g d`之类的命令来自动生成、部署我静态网页。好像不是很懂，没关系，做一遍就懂了。


[Travis-CI 官方文档][3]，有时间，懂英语的可以仔细看看。写得最好的还是官方文档，一般我们总是各种找教程什么的，其实最好的教程就是自带的文档。



## 自动部署

看到这里，应该都有了 `GitHub` 的 Pages了，不然也不会涉及到部署什么的。下面步骤前提是在你电脑上可以 `hexo g -d`成功的前提下进行的。


登陆`Travis CI`官网，使用GitHub账户登录，发现你的 `repos`都出现了吧。然后关键了。选择你要启动的项目，在这里也就是`yourname/yourname.GitHub.io`，你的博客项目地址。

然后点击设置按钮，进入设置选项，开启第一行的服务:

* `Build only if .travis.yml is present`：是只有在.travis.yml文件中配置的分支改变了才构建；
* `Build pushes`：当推送完这个分支后开始构建。


<center>![Snip20170223_2.png-54.3kB][4]</center>

到这一步， 我们已经开启了要构建的仓库。但此时Travis还没法帮助我们自动构建并部署，因为没有访问GitHub的权限。我们自己在本地计算机上push能成功是因为有了 SSH key，但是交给 travis去部署的话，它是没有这个key的，但是也可以给它，只要加密再解密就好了，配置文件配置可以参考文末的链接。不过我这里就用GitHub提供的token变量功能吧，比较适合新手。


在 `GitHub` 上为 `Travis CI` 配置 `Acess Token`，登陆GitHub，进入设置界面，点击到 `Personal access tokens` 页面。

点击右上角的`Generate new token` 按钮会生成新的token，点击后提示输入密码后继续，然后来到如下界面，取个名字（我这里取 `GH_token`，下面配置文件中要用到，牢记！），勾选一些权限（只要repo就行了，还有email），这个token很重要，如果别人也有了这个token，你`GitHub`账户的repo就可能被任意修改！！所以不能外泄这个token！重要的事情就不说三遍了。不过如果外泄了，登录你的GitHub，删除这个token即可。




![Snip20170223_3.png-148.2kB][5]

生成完后，拷贝下来，只有这时候才会显示，下载进来为了安全他就不会显示了，如果忘了只能重新生成一个了，拷贝完以后到`Travis CI`页面中 `Environment Variables` 进行配置。





现在 Travis CI 已获得 GitHub 访问权限，但它并不知道如何进行部署，所以还需要进一步设置。


在源代码分支（我这里即 blog-source 分支）下配置 `.travis.yml` 文件

```yml
language: node_js   #设置语言
node_js: stable     #设置相应的版本

cache:
    directories:
        - node_modules    #据说可以减少travis构建时间

before_install:
    - git clone https://github.com/zpike/hexo-theme-apollo.git themes/apollo
       #自己配置一个主题，也是乐趣之一，特效什么的，不过我还是偏好简洁（Next主题用的人真是多啊）

# S: Build Lifecycle
install:
  - npm install   #安装hexo及插件

before_script:
  - git clone --branch master https://github.com/zpike/zpike.github.io.git public
        #获取最新的commit信息

script:
  - hexo cl   #清除
  - hexo g   #生成

after_script:
  - cd ./public
  - git init
  - git config user.name "zpike"   #修改成自己的github用户名
  - git config user.email "conan401@126.com"   #修改成自己的GitHub邮箱
  - git add .
  - git commit -m "update"
  - git push --force --quiet "https://${GH_token}@${GH_REF}" master:master #GH_token就是在travis中设置的token

branches:
  only:
    - blog-source  #只监测这个分支，一有动静就开始构建（我可能发一篇文章会push好多次，每次看到它忙着构建，感觉好浪费）。
env:
    global:
        - GH_REF: github.com/zpike/zpike.github.io.git 
```


大致结构就是这样，根据自己的需求还需要一点修改。

`.travis.yml` 配置完成，这个时候就可以push文章到你的 `blog-source` 分支上去了。然后其他工作都由 Travis-CI 来完成。


登陆`Travis CI`，不出意外的话（我出了好多次意外，不过最终皇天不负有心人！哈哈）Travis已经检测到变化并进行构建部署，log记录了构建的过程，出什么问题里面都可以点开来看。




## 常见问题



#### 配置文件

如果出问题，基本都是配置文件的问题，yml文件的格式还是要求比较严格的，稍微不注意就不行。.travis.yml 文件的主要流程分三步，获取历史，生产静态文件，发布。

* `before_script`: 在所有动作之前，先把 master 分支clone到`./public`文件夹，保留之前的 commit记录，把最新结果提交上去。

* `script`: 就是 `hexo generate`的过程。

* `after_success`: 在成功之后，成功都到了public文件夹下，然后就是照着`git push`到`master`分支下。

当然还需要确认`travis build`都在 `blog-source`分支下工作。



#### 主题设置

网上很多hexo的主题，有的还在持续更新，这个就需要在配置文件中clone 一下，保持更新，或者在package.jason文件中配置一下也行吧（没试过），还是在travis配置文件中设置一下，方便换主题。设置在 `before_install` 后面。



## 总结

现在终于可以anywhere, anyplatform, anyeditor都可以开始写作了，还是很不错的。要做的就是打开editor，然后写完push即可，不用操心其它，多美好！


---


## 参考资料



[使用 Travis CI 自动部署 Hexo][6]

[使用 Travis CI 自動發布 hexo 到 GitHub pages][7]

[基于 Hexo 的全自动博客构建部署系统][8]

[用 Travis CI 自動部署網站到 GitHub(Hexo 作者写的)][9]


  [1]: http://spikezz.com/2016/07/07/how-to-create-a-hexo-blog
  [2]: http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html
  [3]: https://docs.travis-ci.com/user/getting-started
  [4]: http://static.zybuluo.com/spikett/53whszhrw6l15onttf8d20ol/Snip20170223_2.png
  [5]: http://static.zybuluo.com/spikett/yp8xndh33eha7yxsdwxhcb99/Snip20170223_3.png
  [6]: http://www.jianshu.com/p/5e74046e7a0f
  [7]: https://levirve.GitHub.io/2016/hexo-deploy-through-travisci
  [8]: http://kchen.cc/2016/11/12/hexo-instructions/
  [9]: https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/
