---
title: 学做图书管理系统--使用SQL Server创建并插入数据，建立触发器和存储过程
abbrlink: '6e81849'
date: 2019-10-31 00:20:05
categories:
    - [学习笔记, Python]
    - [学习笔记, 数据库]
tags: [SQL Server, pyodbc]
keywords:
description:
---


数据库（Database）可以说是前端网站搭建必不可少的部分了。  
常见的数据库软件有微软的SQL Server和Linux服务器中常见的MySQL两种。

|ू･ω･` )  

<!-- more -->

在连接数据库之前，需要确保电脑上有以下环境和软件：

+ （必备）任意一种数据库（这里选择SQL Server）
+ （必备）Python 3，可以在Python的[官网下载](https://www.python.org/)对应的版本
+ （必备）任意一种代码编辑器或IDE（本教程使用的**VS Code**和**SSMS**两种，也可使用Python自带的IDLE或者Pycharm）
+ （可选）使用Python包管理器Anaconda，或者使用Python自带的pip

## 安装SQL Server

>SQL Server可以从微软的官方网站上面下载获取：  
>链接：<https://www.microsoft.com/zh-cn/sql-server/sql-server-downloads>

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

>当我们安装了SSMS之后，便可以**可视化**地方便管理我们的数据库啦~  

>其实，SSMS软件与Linux环境下管理MySQL数据库的phpMyAdmin功能挺类似的呐~  

1. 首先，在SQL Server安装中心，选择第三项，安装**SQL Server管理工具**：

<fancybox>![10](https://i.loli.net/2019/10/30/DgYQbX1LaKVqk6M.png)</fancybox>

2. 跳转到网页页面，选择最新的版本下载：

<fancybox>![11](https://i.loli.net/2019/10/30/FsNt7HIJjb3yRMv.png)</fancybox>

3. 选择D盘安装，按照要求重启电脑：

<fancybox>![12](https://i.loli.net/2019/10/30/fT1BK3SM6Ro5eJw.png)</fancybox>

<fancybox>![13](https://i.loli.net/2019/10/30/59AgyjPEVKDp7mZ.png)</fancybox>

4. 重启后，在开始菜单找到**SQL Server Management Studio**打开，并登陆就可以啦~

<fancybox>![14](https://i.loli.net/2019/10/31/uOH563TFWLjsq27.png)</fancybox>

## 创建一个数据库

我们可以新建一个查询（数据库脚本）来创建一个数据库。  

登录进入SSMS后，选择菜单栏中的`新建查询`：

![新建](https://i.loli.net/2019/11/08/SQcu73Y8CVfF2LH.png)

在SQL脚本中可以输入如下代码，例如我创建的存放数据的数据库和日志的数据库：

```sql
CREATE DATABASE BookManageSys
ON
(
    NAME = 'BookManageSys_Data',
    FILENAME = 'E:\Common_things\Programs\数据库SQL\BookManageSystem\BookManageSys.mdf',
    SIZE = 10 MB,
    MAXSIZE = 50 MB,
    FILEGROWTH = 5%
)
LOG ON
(
    NAME = 'BookManageSys_Log',
    FILENAME = 'E:\Common_things\Programs\数据库SQL\BookManageSystem\BookManageSys_Log.ldf',
    SIZE = 2 MB,
    MAXSIZE = 5 MB,
    FILEGROWTH = 1 MB
)
GO
```

在T-SQL语法中，可以指定数据库的存放位置和其他约束和规则限制，只要在后面加上`NAME`和`FILENAME`参数即可。

---

或者直接在数据库默认目录下创建：  

```sql
CREATE DATABASE BookManageSys
GO
```

然后在菜单栏点击`执行`，便可以创建一个数据库啦。

![执行脚本](https://i.loli.net/2019/11/08/7zyY5H2GShBrUdM.png)

## 创建表、插入数据到表中

创建读者表、图书表和借阅表：  

```sql
create table TReader
(
    ReaderID char(8) NOT NULL PRIMARY KEY,
    Name char(8) NOT NULL,
    Sex   bit    not null,
    Born   date  not null,
    Spec  char(12)  not null,
    Num  int not null default 0,
    Photo  varbinary(MAX)
)

create table TBook
(
    ISBN char(18)  not null primary key,
    BookName char(40)  not null,
    Author char(16) not null,
    Publisher char(30) not null,
    Price float not null,
    CNum  int   not null,
    Snum  int   not null,
    Summary varchar(200),
    Photo varbinary(MAX)
)

create table TLend
(
    BookID  char(10)  not null primary key,
    ReaderID char(8)   not null,
    ISBN  char(18)   not null,
    Ltime datetime not null
)
```

向三个表中插入一些数据：  

```sql
INSERT INTO TReader
VALUES('131101','王林',1,'1996-2-10','计算机',4,null),
('131102','程明',1,'1997-2-1','计算机',2,null),
('131103','王燕',0,'1995-10-6','计算机',1,null),
('131104','韦严平',1,'1996-8-26','计算机',4,null),
('131106','李方方',1,'1996-11-20','计算机',1,null),
('131107','李明',1,'1996-5-1','计算机',0,null),
('131108','林一帆',1,'1995-8-5','计算机',0,null),
('131109','张强民',1,'1995-8-11','计算机',0,null),
('131110','张蔚',0,'1997-7-22','计算机',0,null),
('131111','赵琳',0,'1996-3-18','计算机',0,null),
('131113','严红',0,'1995-8-11','计算机',0,null),
('131201','王敏',1,'1995-6-10','通信工程',1,null),
('131202','王林',1,'1995-1-29','通信工程',1,null),
('131203','王玉民',1,'1996-3-26','通信工程',1,null),
('131204','马琳琳',0,'1995-2-10','通信工程',1,null),
('131206','李计',1,'1995-9-20','通信工程',1,null),
('131210','李红庆',1,'1995-5-1','通信工程',1,null),
('131216','孙祥欣',1,'1995-3-19','通信工程',0,null),
('131218','孙研',1,'1996-10-9','通信工程',1,null),
('131220','吴薇华',0,'1996-3-18','通信工程',1,null),
('131221','刘燕敏',0,'1995-11-12','通信工程',1,null),
('131241','罗林琳',0,'1996-1-30','通信工程',0,null)

INSERT INTO TBook
Values('978-7-121-23270-1','MySQL实用教程（第2版）','郑阿奇','电子工业出版社','53',8,1,null,null),
('978-7-81124-476-2','S7-300/400可编程控制器原理与应用','崔维群 孙启法','北京航空航天大学出版社','59',4,1,null,null),
('978-7-111-21382-6','Java编程思想','Bruce Eckel','机械工业出版社','108',3,1,null,null),
('978-7-121-23402-6','SQL Server实用教程（第4版）','郑阿奇','电子工业出版社','59',8,5,null,null),
('978-7-302-10853-6','C程序设计（第三版）','谭浩强','清华大学出版社','26',10,7,null,null),
('978-7-121-20907-9','C#实用教程（第2版）','郑阿奇','电子工业出版社','49',6,3,null,null)

insert into TLend 
values ('1200001','131101','978-7-121-23270-1','2014-02-18'),
('1300001','131101','978-7-81124-476-2','2014-02-18'),
('1200002','131102','978-7-121-23270-1','2014-02-18'),
('1400030','131104','978-7-121-23402-6','2014-02-18'),
('1600011','131101','978-7-302-10853-6','2014-02-18'),
('1700062','131104','978-7-121-20907-9','2014-02-19'),
('1200004','131103','978-7-121-23270-1','2014-02-20'),
('1200003','131201','978-7-121-23270-1','2014-03-10'),
('1300002','131202','978-7-81124-476-2','2014-03-11'),
('1200005','131204','978-7-121-23270-1','2014-03-11'),
('1400031','131206','978-7-121-23402-6','2014-03-13'),
('1600013','131203','978-7-302-10853-6','2014-03-13'),
('1700064','131210','978-7-121-20907-9','2014-03-13'),
('1300003','131216','978-7-81124-476-2','2014-03-13'),
('1200007','131218','978-7-121-23270-1','2014-04-08'),
('1800001','131220','978-7-111-21382-6','2014-04-08'),
('1200008','131221','978-7-121-23270-1','2014-04-08'),
('1400032','131101','978-7-121-23402-6','2014-04-08'),
('1700065','131102','978-7-121-20907-9','2014-04-08'),
('1600014','131104','978-7-302-10853-6','2014-07-22'),
('1800002','131104','978-7-111-21382-6','2014-07-22')
```
插入完成后，可以使用`SELECT`语句查询一下我们插入的数据是否成功：  

```sql
select * from TReader
select * from TBook
select * from TLend
```

>**PS：**如果使用VS Code编辑T-SQL脚本，可以参考微软的官方文档：[使用 Visual Studio Code 创建并运行 Transact SQL 脚本](https://docs.microsoft.com/zh-cn/sql/visual-studio-code/sql-server-develop-use-vscode?view=sql-server-ver15)

## 建立存储过程和触发器



## 参考资料

1. [在Windows上使用SQL Server创建Python应用](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/windows)
2. [pyodbc Github 官网文档](https://github.com/mkleehammer/pyodbc/wiki)
3. [使用 Visual Studio Code 创建并运行 Transact SQL 脚本](https://docs.microsoft.com/zh-cn/sql/visual-studio-code/sql-server-develop-use-vscode?view=sql-server-ver15)

