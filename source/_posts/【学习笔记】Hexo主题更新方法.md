---
title: 【学习笔记】Hexo主题更新方法
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

2. 之后，输入命令：  

```bash
git stash
git pull
git stash pop
```

执行这三条简单的命令之后，Hexo主题便可以无损更新。  

**操作截图如下：**  

![示意图](https://i.loli.net/2019/08/30/qviIATxeE8OFMPd.png)

## 附：相关Git命令知识

+ **git stash**: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。  
+ **git pull**:拉取服务器上的代码；  
+ **git stash pop**: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。  
+ **git stash list**: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。  
+ **git stash clear**: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。  

>通过`git stash`将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常`git pull`了，`git pull`完成后，执行`git stash pop`将之前本地做的修改应用到当前工作区。

-----

感谢你的耐心阅读呀(๑*◡*๑)，原创总结不易，收藏并评论一下呗~

你的支持就是我前进的动力呀~
