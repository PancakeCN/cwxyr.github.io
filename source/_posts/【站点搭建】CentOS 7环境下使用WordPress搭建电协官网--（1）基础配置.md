---
title: 【站点搭建】CenOS 7环境下使用WordPress搭建电协官网--（1）基础配置
categories: 网站搭建
tags:
  - WordPress
  - 电协官网
abbrlink: 826b26c7
date: 2019-07-31 15:47:14
keywords:
description:
---

因下学期电协招新及20周年纪念的需要，需要搭建一个官方网站。我们以WordPress为例，介绍从购买一个服务器开始，到安装博客程序以及最后细节优化及注意事项。  

![cover](https://i.loli.net/2019/07/31/5d414ae98e34945456.jpg)

<!-- more -->

# 环境准备

1. 一个阿里云或者腾讯云服务器 

2. 宝塔面板

3. CenOS 7+ 系统

4. LAMP/LNMP环境

5. WordPress安装包

6. 远程连接工具Xshell和Xftp

------------------

# 购买并配置服务器

对于大多数国内用户来说，我们可以选着阿里云或者腾讯云的服务器，这里以选择腾讯云服务器为例，进入[腾讯云](https://cloud.tencent.com/)，注册并登录后进入管理后台。在`云产品`中选择`云服务器`：  

![腾讯云1](https://i.loli.net/2019/07/31/5d419d3f30d7127999.png)

之后，在云服务器里面选择`创建`：  

![腾讯云2](https://i.loli.net/2019/07/31/5d419dd53cadd16616.png)

选择适当的配置，我们可以选择`快速配置`，可以按照如下配置购买，其中推荐使用CentOS系统，相对来说较为稳定。

![腾讯云3](https://i.loli.net/2019/07/31/5d41a02986fd693847.png)

购买后，选择左边的`SSH密钥`，新建后并下载（如我命名为xshell）

![SSH密钥1](https://i.loli.net/2019/07/31/5d41a5462cda531468.png)

返回`实例`页面，点击右侧`更多`->`密码/密钥`->`加载密钥`，选择之前创建的密钥后，将会强制关机以绑定密钥。

![SSH密钥2](https://i.loli.net/2019/07/31/5d41a660dfdf127999.png)

------

# 下载Xshell和Xftp并远程连接

1. Xshell[下载地址](https://www.netsarang.com/zh/xshell-download/)
2. Xftp[下载地址](https://www.netsarang.com/zh/xftp-download/)

## 配置Xshell

以下载Xshell为例，进入官网后右边有`免费授权页面`，填写相关信息后，收到回复邮件，打开链接即可免费下载（Xftp同理）  

![Xshell免费下载](https://i.loli.net/2019/08/04/m6erVNJikGXWtA3.png)  

下载并安装Xshell和Xftp后，点击`文件`->`新建`，在“连接”窗口，填写的`主机`为腾讯云服务器外网IP地址，在“用户身份验证”窗口，选择`方法`为“Public Key”,`用户名`为root，用户密钥选择之前下载的SSH密钥，点击`连接`即可成功连接至腾讯云服务器：

![shell配置1](https://i.loli.net/2019/08/04/69ZH51GmLwBqsgj.png)  

![shell配置2](https://i.loli.net/2019/08/04/rMlsa57UfcbCTWO.png)

连接成功后的界面如下：  

![shell连接成功](https://i.loli.net/2019/08/04/qUBsvOgzDV1a5Tr.png)  

## 配置Xftp

配置方法与Xshell类似，这里放上几张配置截图：  

![ftp配置](https://i.loli.net/2019/08/04/qhubmzMlQOISoE8.png)  

![ftp连接成功](https://i.loli.net/2019/08/04/fNnRMyYxqu8BWbC.png)  

**如果你已经实现了上述结果，恭喜你，远程连接已配置成功。**

**PS:  
使用Xshell需要涉及Linux命令相关的知识。如不是很了解，建议点击这里的[传送门](https://www.runoob.com/linux/linux-command-manual.html)了解基本命令。**

------

# 安装宝塔面板

安装宝塔面板主要是为了方便后期维护的方便，使得服务器配置可视化同时加快配置效率。  

宝塔面板[官方网站](https://www.bt.cn/) 

**PS：安装宝塔面板时，一定要保证系统纯净，即服务器中没有安装其他的任何软件，否则可能会安装失败！！！**   

## 放行出站入站端口

在安装面板之前，需要放行特定的入站和出站端口号。回到腾讯云管理界面，选择`安全组`->`新建`： 

![安全组1](https://i.loli.net/2019/08/04/ptxE3DG1CuNWMzO.png)

然后下载规则配置文件：  

[sg_input_rules.xlsx](https://www.bt.cn/bbs/forum.php?mod=attachment&aid=NDExOHwyYTI5ZWM1NnwxNTY0ODUwMzQ4fDB8MTIyOQ%3D%3D)

[sg_output_rules.xlsx](https://www.bt.cn/bbs/forum.php?mod=attachment&aid=NDExOXxkZGE5M2NlNHwxNTY0ODUwMzQ4fDB8MTIyOQ%3D%3D) 

点击`导入规则`，将规则配置文件添加到入站和出站规则中：

![入站和出站规则1](https://i.loli.net/2019/08/04/kPi3QZyUNjYF5XM.png)

之后，点击`关联实例`->`新增关联`，将规则和服务器相互关联。

![关联实例](https://i.loli.net/2019/08/04/h2AovOxpckI5RLz.png)

## 正式安装

打开之前配置好的Xshell，对于CenOS 7+的系统，输入如下命令安装:

```bash
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

出现提示信息，询问是否安装宝塔面板，输入`y`并回车：

![安装](https://i.loli.net/2019/08/04/I7OYQWmrZpB3lgA.png)

耐心等待片刻后，出现如下界面，则安装成功。

![成功](https://i.loli.net/2019/08/04/PADRNi4qGy5ml1g.png)

其中，给出了登录名和密码，登录地址为`http://你的IP或域名地址:8888/安全入口地址`的格式。浏览器输入登录地址并登录。如出现如下后台界面，则安装成功！！！

![宝塔面板](https://i.loli.net/2019/08/04/YMI4SZxPqzpRneL.png)

至此，**服务器的基础配置已基本完成**。建议到`面板设置`修改登录名和密码，便于记忆。

---------------

感谢你的耐心阅读呀(๑*◡*๑)，原创不易，收藏并评论一下呗~

接下来，我们可以配置服务器和安装WordPress等后续操作啦~

