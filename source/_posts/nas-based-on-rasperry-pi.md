---
title: 基于 Rasperry Pi 3 的家用 NAS 搭建
date: 2017-03-08
tags: nas
---


![mark](http://oc1hnrd8p.bkt.clouddn.com/photos/20170309/164143432.jpg)

最近感觉对于 `NAS` 的需求越来越多了，主要是因为电子设备的增多，想要将所有媒体文件以及文档都放到一个地方统一管理，然后在世界任何地方可以方便存取，查阅。这样的场景想想就诱人。

但是无奈群晖的 NAS 价格较高，个人用户还是比较难以承受的。不过很多其他方案可以实现，只是没有那么炫酷罢了。比如路由器自带的云存储，或者本篇文章要介绍的基于树莓派的家用 NAS 构建。


<!-- more -->


首先要用 NAS 当然要准备一个移动硬盘。目前可以选择的比较好的移动硬盘就那么几家，个人推荐WD的，或者希捷的，当然高富帅也可以选择Lacie，不过也就是样子货，还是简单实用就行了。

说道移动硬盘，最近出了SSD的移动硬盘，那玩意儿短期之内价格不会让人满意的，还是用传统的就好，用最新的Pi 3 速度已经够一般使用了。

然后就是拿出你尘封已久的 Rasperry Pi 了，到[这个网站][1]下载镜像，然后就是安装系统了。关于镜像的选择，貌似网上很多，不过还是建议找最热门的，就是 OpenMediaVault，但是也有点问题，比如对名字中包含中文的文件的支持。总体上还是不错的，操作简单，方便。

## OpenMediaVault 系统安装

安装比较简单，主要步骤如下：

* 格式化树莓派的SD卡，格式化成FAT32格式；
* 下载镜像文件，解压，镜像格式img；
* 把精选写到SD卡上；
* 树莓派放到路由器旁，插上一根网线，然后上电；
* 等系统启动完毕，在你的通网络的电脑中看到树莓派的ip地址，然后复制ip到你的浏览器中，默认登录用户名是：admin，密码：openmediavault。

然后开始配置你的 NAS 即可。



## OpenMediaVault 的使用 


配置只需要将移动硬盘挂在一下，在 web 界面下的 `File Systems` 中设置。


需要注意的是，每次配置好移动硬盘都需要 `Apply` 一下，其他操作也是如此。


然后是配置一下要分享的文件夹，在左边的 `Shared Folders` 中设置，选择好要分享的硬盘，就是刚刚挂载的移动硬盘。

要注意的是权限问题，管理挺麻烦的。自己家里用你就添加一个root帐号即可，所有权限都给。但是想要安全一点，还是需要多加限制。



最后就是使能 `SMB/CFIS` 分享功能，用过 ios上的 nplayer的应该比较熟悉。添加刚刚设置好的分享的文件夹，结束。

然后就可以在电脑上访问到nas中的资料了。其他设备访问也比较容易，手机上可以用 `Solid Explorer` （需付费）或者 ES文件浏览器。电视上应该也有ES浏览器可以安装。 ios设备推荐使用 `nplayer`，非常方便。


`参考文章：`

[Turn any hard drive into networked storage with Raspberry Pi][2]

[Openmediavault入门常见问题及解决方法][3]

[1]: https://sourceforge.net/projects/openmediavault/files/Raspberry%20Pi%20images/
[2]: https://www.cnet.com/how-to/raspberry-pi-as-cheap-nas-solution/
[3]: http://www.songming.me/general-discussion.html