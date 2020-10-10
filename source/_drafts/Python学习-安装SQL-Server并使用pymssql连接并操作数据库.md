---
title: Python学习--安装SQL Server并使用pymssql连接并操作数据库
abbrlink: '6e81849'
tags:
---

数据库（Database）可以说是前端网站搭建必不可少的部分了。  
常见的数据库软件有微软的SQL Server和Linux服务器中常见的MySQL两种。  
这里主要记录了使用Python语言连接SQL Server的主要步骤和方法。  

|ू･ω･` )  

<!-- more -->

在连接数据库之前，需要确保电脑上有以下环境和软件：

+ （必备）任意一种数据库（这里选择SQL Server）
+ （必备）Python 3，可以在Python的[官网下载](https://www.python.org/)对应的版本
+ （必备）任意一种代码编辑器或IDE（本教程使用的VS Code，也可使用Python自带的IDLE或者Pycharm）
+ （可选）使用Python包管理器Anaconda，或者使用Python自带的pip

## 安装SQL Server

SQL Server可以从微软的官方网站上面下载获取：  
链接：<https://www.microsoft.com/zh-cn/sql-server/sql-server-downloads>

1. 我们可以选择免费的Express版本下载即可：

![express](https://i.loli.net/2019/10/30/Xto8hRayGF6TVkW.png)

2. 选择自定义安装：

<fancybox>![自定义](https://i.loli.net/2019/10/30/yufGT1ZUdXVcDBF.png)</fancybox>

3. 选择下载到D盘，安装：

<fancybox>![D盘](https://i.loli.net/2019/10/30/qnxXTp9ifGswRUY.png)</fancybox>

这里需要稍等片刻：

<fancybox>![wait](https://i.loli.net/2019/10/30/v9YqAI4nbxoGZ5H.png)</fancybox>

4. 下载后安装，选择**全新的SQL Server安装**：

<fancybox>![安装](https://i.loli.net/2019/10/30/pi5h4zGfIuEjCOT.png)

5. 然后依次按照图片所示一步步进行下去：

<fancybox>![1](https://i.loli.net/2019/10/30/ISheOuMWU18PHic.png)</fancybox>

<fancybox>![2](https://i.loli.net/2019/10/30/uDNizGQ7Mm4UfEt.png)</fancybox>

<fancybox>![3](https://i.loli.net/2019/10/30/yVPdNLTht2cfk7o.png)</fancybox>

<fancybox>![4](https://i.loli.net/2019/10/30/G9DhC7K3JEWfbpg.png)</fancybox>

<fancybox>![5](https://i.loli.net/2019/10/30/PpKh5bxWw6H3TcC.png)</fancybox>

<fancybox>![6](https://i.loli.net/2019/10/30/u9EwZDAJ1jaKO6m.png)</fancybox>

<fancybox>![7](https://i.loli.net/2019/10/30/dCWyU2FpiarwZeE.png)</fancybox>

稍微等待一会儿....

<fancybox>![8](https://i.loli.net/2019/10/30/riXoEmYhDxcFLuN.png)</fancybox>

安装完成啦~

<fancybox>![9](https://i.loli.net/2019/10/30/8hu1dGTQB9i7MjP.png)</fancybox>

### 安装SQL Server管理工具（SSMS）

<fancybox>![10](https://i.loli.net/2019/10/30/DgYQbX1LaKVqk6M.png)</fancybox>

跳转到网页页面，选择最新的版本下载：

<fancybox>![11](https://i.loli.net/2019/10/30/FsNt7HIJjb3yRMv.png)</fancybox>

选择D盘安装，按照要求重启电脑：

<fancybox>![12](https://i.loli.net/2019/10/30/fT1BK3SM6Ro5eJw.png)</fancybox>

<fancybox>![13](https://i.loli.net/2019/10/30/59AgyjPEVKDp7mZ.png)</fancybox>

之后，在开始菜单找到Management Studio，并登录就完成啦：

<fancybox>![14](https://i.loli.net/2019/10/31/WwLnN37zujvJdi8.png)</fancybox>


## 安装pymssql

1. 首先，使用组合键`Win + R`调出**运行**窗口，输入`cmd`，打开命令行。

2. 在命令行（CMD）界面，如使用了Anaconda，则需要激活conda环境（如没有Anaconda，则可以跳过这一步）：

```shell
activate
```
当光标前面出现了`(base) C:\Users\用户名>`，则说明进入了conda的base环境。

3. 输入如下任意其中一种命令：

```shell
pip install pymssql #常规方法，pip安装
conda install pymssql #如果安装了Anaconda，则可以使用conda命令
```
当出现提示确认是否安装，输入`y`，则可以等待安装完成。

<fancybox>![install](https://i.loli.net/2019/10/30/7kMy9aHDv43KGdV.png)</fancybox>

## 连接数据库

