---
title: 国内外常用VPS + 域名注册 + CDN，搭建属于自己的博客（新手推荐）
categories: 网站搭建
tags:
  - VPS
  - 域名
  - CDN
keywords: VPS、域名及CDN
abbrlink: d874e9c
date: 2019-10-09 16:35:32
description:
---

本文大概概括总结了自己学习前端技术以来各种实用的VPS、域名注册以及实用的CDN提供商。  
特别是对于**新手**来说，了解一些相关知识挺不容易的。
写一些总结希望会对大家有一定的帮助吧。**本文章会不定时更新**，分享更多的实用选择喔~  

喜欢的话就继续往下看看吧(*/ω＼*)  

<!-- more -->

## 建站VPS（虚拟专用服务器）

对于一个网站来说，首先需要的便是一台服务器（VPS）。可以有国内和境外两个选择。

### 国内

对于国内用户来说，常见VPS有如下几家提供商：  

+ [阿里云](https://www.aliyun.com/)：算是国内最为知名的服务器提供商了，安全且稳定。
+ [腾讯云](https://cloud.tencent.com/)：第二大云服务提供商，微信小程序使用较为普遍。
+ [华为云](https://www.huaweicloud.com/)：使用也比较普遍。
+ [UCLOUD](https://www.ucloud.cn/)

>几家服务商提供的VPS价格均相差不远，其中，阿里云和腾讯云对于在校大学生有优惠活动：  

**阿里云**：“云翼计划”，点击[这里](https://promotion.aliyun.com/ntms/act/campus2018.html)进入优惠网址。  

阿里云优惠需要通过学生认证，注册账号后，在后台账号管理界面可以申请认证：  

<fancybox>![学生优惠](https://i.loli.net/2019/10/10/YcAqefmvkDKTyn8.png)</fancybox>

认证后，对于学生机的量轻服务器和ECS仅**9.5 RMB/月**！！！！！

<fancybox>![aliyun](https://i.loli.net/2019/10/10/iRKEZmJ6A3hxjpn.png)</fancybox>

---

**腾讯云**：名称也类似，“云+校园”，优惠网址入口点击[这里](https://cloud.tencent.com/act/campus)。

腾讯云25岁以下或者通过学生认证直接优惠，不仅可以购买优惠价的云服务器，还可以购买优惠价的MySQL数据库：

<fancybox>![qcloud](https://i.loli.net/2019/10/10/BXTZnwVQNJWIfhD.png)</fancybox>

腾讯云也相当的很便宜，仅仅**10 RMB/月**！！！

### 境外

对于境外的可选择的提供商就比较多了。如亚马逊的AWS，微软的Azure，谷歌的Google Cloud，IBM公司的Cloud等等...但是国外的VPS换算为RMB后普遍偏贵，博主实在买不起呜呜呜(T＿T)  

---

这里，博主只能选择境外廉价的VPS提供商了。如[VirMarch](https://virmach.com)、[Vultr](https://vultr.com)、[linode](https://www.linode.com/)和[搬瓦工Bandwagon](https://bwh88.net)。  

对于此类廉价VPS，官方常常有一些**优惠减价活动**，可以国内相关的中文社区看看：

+ [VirMach中文网](https://virmach.net/)
+ [Vultr中文网](https://www.cnvultr.com/)
+ [搬瓦工VPS中文网1](https://banwagong.cn/)

**PS**：由于这类VPS廉价，最低只需2-4美元/月，因此，其提供的相关服务支持有限，有些服务器访问速度较慢（**但这并不是问题，可以配合Cloudflare的CDN加速使用**），最好不要用于重要用途。主要推荐国内的阿里云和腾讯云。

**PS：这里不介绍如何购买VPS，自己可以找找度娘，网上有很多教程~**

## 域名

有了VPS之后，便需要注册一个域名来来指向自己的VPS，使得大家都方便访问。

### 域名注册商

对于域名注册商来说，也有挺多的选择，**国内**可以选择：  

+ [万网（被阿里云收购）](http://www.net.cn)
+ [新网（与腾讯云有合作关系）](http://www.xinnet.com/)
+ [易名](https://www.ename.net/)
+ ...

**PS**：对于国内的域名注册来说，其域名后缀类型有限（`.com`，`.cn`，`.net`，`.top`，`.xyz`等常见域名），如果域名需要**备案**，其支持的格式也相当有限制了，只有部分少量域名类型才能备案。

---

如果对其他类型的域名，如`.me`，`.io`, `.cc`，`.design`或者其他顶级国家域名的时候，就需要找找**国外**老牌的注册商了，如：

+ Namecheap
+ Godaddy
+ NameSilo
+ Name.com
+ ...

### NameBeta.com

国外的域名注册商十分繁多，网站地址也很难记忆，不同注册商价格不一，英文界面对于国人来说不太友好。因此，这里推荐一个域名统一搜索平台——**[NameBeta.com](https://namebeta.com/)**

---

此平台是国内知名博客程序[Typecho](http://typecho.org/)创始人joyqi又一个作品~~~~

NameBeta.com汇集了国内外大量知名注册商的数据，并列出各个域名和注册商的最近优惠，我们只需要在搜索框输入自己想注册的域名后，便会自动得出注册**最为划算**的域名提供商，**点击后会自动跳转到对应注册商的付款页面！！！！**，相当方便有木有！！！！

<fancybox>![namebeta](https://i.loli.net/2019/10/10/m1xnN8Ewslo3T9L.png)</fancybox>

---

此外，对于Chrome浏览器来说，还支持安装插件！！！在页面最下方可进入安装：

<fancybox>![插件](https://i.loli.net/2019/10/10/SgA1aVEZIxyf6nQ.png)</fancybox>

这样，可以不用每次进入网站直接在浏览器插件寻找自己心仪的域名啦！

**例如：**  
我的博客域名[sublimerui.top](https://sublimerui.top/)和[cwxyr.me](https://cwxyr.me/)便是通过NameBeta.com检索后，分别在万网和Godaddy注册的。

**最后，在此感谢joyqi大佬~**

**PS：域名的注册方法也可以自行找找度娘滴~**

## CDN加速

对于个人博客来说，使用CDN可以说是一个非常常见的配备了。  
CDN（内容分发网络）是一种用来减轻服务器压力的常见方法，利用附近节点就近推送下载文件的原则，可以缩短网页访问速度。  

### 国内

CDN可以分为多种：  

+ 用于储存图片的CDN（图床，自己经常使用）：`sm.ms`，`img.vim-cn.com`等等...
+ 用于储存大型文件的OSS加CDN：[七牛云](https://www.qiniu.com)，[又拍云](https://www.upyun.com/)（**提供免费OSS储存空间**），阿里云，腾讯云（OSS与CDN均需要花钱）等等...

其中，又拍云推出的开发者帮助计划 **[又拍云联盟](https://www.upyun.com/league)**，可以在线申请**10GB免费储存空间和15GB免费CDN流量**，有兴趣的朋友可以去看看呀~

![upyun](https://i.loli.net/2019/10/14/yUkfSX1mVGHq7dl.png)

### 境外

对于个人博客来说，境外的服务器确实也不便宜，但唯一的优点便是**不用对域名进行备案**，直接在域名解析处使用`A记录`将域名指向境外的服务器的IP即可。这类极力推荐挂一个CDN，不然会慢死（~~由于中国的GFW存在，会对一些内容进行过滤和封锁，这也是为什么有科学上网的说法~~）

这里，可以使用`Cloudflare`进行网站的加速，国人很多都是用Cloudflare加速境外的服务器的访问。（**真的挺好用的！**）

![cloudflare](https://i.loli.net/2019/10/10/WMB9GpyjRL5lvXr.png)

这里放几篇自己认为比较好用的Cloudflare使用教程，讲得比较详细：

1. [十个你可能不知道的CloudFlare免费CDN加速技巧-SSL\DDOS\Cache](https://cloud.tencent.com/developer/news/135302)
2. [使用CloudFlare加速和保护你的境外网站](http://wenda.dreamshare.in/article_12.html)
3. [Cloudflare 免费SSL证书使用](https://www.jianshu.com/p/24d3800f597a)
4. [CloudFlare CDN折腾记－优化设置](https://www.cnblogs.com/waw/p/5729722.html)

>未完待续，关于这方面的总结有新的内容会持续更新滴~

---

很久都没更新文章了(╥╯^╰╥)鸽了一个月了吧...最近确实有点小忙，呜呜呜... 

**感谢你的耐心阅读呀(๑*◡*๑)，总结不易，加个收藏点点赞呗~**

**你的支持就是我前进的动力呀~**
