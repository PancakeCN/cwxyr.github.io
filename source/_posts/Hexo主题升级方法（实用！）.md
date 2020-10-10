---
title: Hexo主题升级方法（实用！）
categories: 学习笔记
tags: Hexo
abbrlink: dfb45bc4
date: 2019-08-30 00:01:49
keywords:
description:
---

最近，自己使用的Hexo博客主题Material-X有新的版本更新，使用普通的方法`git clone`会覆盖自己修改后的文件，使得DIY的主题丢失。  
通过查阅有关资料，总结出了以合并文件的形式更新当前的主题文件的方法喔~  

<!-- more -->

**PS**：

1. `git pull`用法：<https://blog.csdn.net/chaiyu2002/article/details/82120256>  

2. 最开始更新主题的时候，直接使用`git pull`命令，然而，出现了报错：  

>error: Your local changes to the following files would be overwritten by merge:  
_config.yml  
Please commit your changes or stash them before you merge.
Aborting

原来，pull新版本主题文件的时候，本地的主题文件也DIY修改过，两者文件内容不同，导致发生冲突（即报错中的 _config.yml 文件）。  

为了解决问题，同时本着无损合并代码的方式更新，在网络上找到了一篇文章。  
原文出自：<https://www.jianshu.com/p/920ad324fe64>

## 主题更新步骤

1. 使用[**Git Bash**](https://git-scm.com/)工具跳转到主题文件对应的目录中，如我的是：

>D:\Program Files\Hexo\themes\material-x  

2. 首先，更新工作的状态树：  

```bash
git add .
```

3. 之后，输入如下命令，将文件放在堆栈中，获取更新后，再弹栈：

```bash
git stash
git pull
git stash pop
```

执行这几条简单的命令之后，Hexo主题便可以无损更新。  

**操作截图如下：**  

![示意图](https://i.loli.net/2019/08/30/qviIATxeE8OFMPd.png)

## 附：相关Git命令知识

**git add .**  
他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。

**git stash**  
能够将所有未提交的修改（工作区和暂存区）保存至堆栈中，用于后续恢复当前工作目录。  

**git stash save**  
作用等同于git stash，区别是可以加一些注释  

**git stash list**  
查看当前stash中的内容  

**git stash pop**  
将当前stash中的内容弹出，并应用到当前分支对应的工作目录上。  
注：该命令将堆栈中最近保存的内容删除（栈是先进后出）  
如果从stash中恢复的内容和当前目录中的内容发生了冲突，也就是说，恢复的内容和当前目录修改了同一行的数据，那么会提示报错，需要解决冲突，可以通过创建新的分支来解决冲突。  

**git stash apply**  
将堆栈中的内容应用到当前目录，不同于git stash pop，该命令不会将内容从堆栈中删除，也就说该命令能够将堆栈的内容多次应用到工作目录中，适应于多个分支的情况。  
堆栈中的内容并没有删除。  
可以使用git stash apply + stash名字（如stash@{1}）指定恢复哪个stash到当前的工作目录。  

**git stash clear**  
清除堆栈中的所有内容  

**git stash show**  
查看堆栈中最新保存的stash和当前目录的差异。  

>通过`git stash`将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常`git pull`了，`git pull`完成后，执行`git stash pop`将之前本地做的修改应用到当前工作区。

-----

感谢你的耐心阅读呀(๑*◡*๑)，原创总结不易，收藏并评论一下呗~

你的支持就是我前进的动力呀~
