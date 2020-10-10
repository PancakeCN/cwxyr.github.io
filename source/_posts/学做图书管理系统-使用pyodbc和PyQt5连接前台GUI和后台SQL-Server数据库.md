---
title: 学做图书管理系统--使用pyodbc和PyQt5连接前台GUI和后台SQL Server数据库
abbrlink: ca06d6a5
date: 2019-12-06 03:21:45
categories:
tags:
keywords:
description:
---

这里主要记录了使用Python语言连接SQL Server的主要步骤和方法。 

<!-- more -->

在连接数据库之前，需要确保电脑上有以下环境和软件：

+ （必备）任意一种数据库（这里选择SQL Server）
+ （必备）Python 3，可以在Python的[官网下载](https://www.python.org/)对应的版本
+ （必备）任意一种代码编辑器或IDE（本教程使用的**VS Code**和**SSMS**两种，也可使用Python自带的IDLE或者Pycharm）
+ （可选）使用Python包管理器Anaconda，或者使用Python自带的pip

## 安装pyodbc

1. 首先，使用组合键`Win + R`调出**运行**窗口，输入`cmd`，打开命令行。

2. 在命令行（CMD）界面，如使用了Anaconda，则需要激活conda环境（如没有Anaconda，则可以跳过这一步）：

```shell
activate
```
当光标前面出现了`(base) C:\Users\用户名>`，则说明进入了conda的base环境。

3. 输入如下任意其中一种命令：

```shell
pip install pyodbc #常规方法，pip安装
conda install pyodbc #如果安装了Anaconda，则可以使用conda命令
```
当出现提示确认是否安装，输入`y`，则可以等待安装完成。

<fancybox>![install](https://i.loli.net/2019/11/09/V9UckmeExLywRTr.png)</fancybox>


### 安装SQL Server的ODBC驱动程序和命令行工具

1. 进入微软的此页面下载安装[ODBC驱动程序](https://www.microsoft.com/zh-CN/download/details.aspx?id=56567)
2. 进入微软的此页面下载安装[命令行工具](https://www.microsoft.com/en-us/download/details.aspx?id=53591)

在命令行输入如下命令登录SQL Server：

```powershell
sqlcmd -S localhost -U sa -P your_password
1>    #已登录成功
```