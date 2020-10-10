---
title: 学做图书管理系统--利用Python的PyQt5编写简单的GUI图形界面
abbrlink: 3b91e5f
date: 2019-10-31 00:19:06
categories: 
    - [学习笔记, Python]
    - [学习笔记, 图形界面]
tags: [PyQt, GUI, Qt Designer]
keywords:
description:
---


由于学校课程的项目，最近最近在学习如何利用Python语言和SQL Server编写一个读者图书借阅、查询管理的小程序。以此为契机吧，自己便开始了学习之路~
这篇文章主要介绍自己如何使用PyQt5编写GUI界面。  

<!-- more -->

## 自己使用到的工具

+ VS Code
+ PyQt5依赖包
+ Qt Designer

## Python几种常见的GUI库

### Tkinter

Tkinter是Python自带的量轻级的简单GUI库，使用也较为简单。但是Tkinter之适用于基础和小型程序的开发，界面的美观程度也相对有限。

**在网上找到了比较全面介绍Tkinter的使用教程**：

1. <https://www.cnblogs.com/shwee/p/9427975.html>
2. <https://www.runoob.com/python/python-gui-tkinter.html>

### PyQt5

PyQt5算是比较成熟的GUI库了，PyQt从著名的由C++编写的Qt库移植封装到Python而来。同时，利用PyQt5开发GUI比较美观，同时利用**Qt Designer**可以拖动一些控件，实现GUI可视化的快速开发，可通过pyuic5工具转化为`.py`文件。在这里，自己选择了PyQt5开发前端界面。

## 安装PyQt5和Qt Designer

安装过程较为简单，只需在命令行窗口输入两行命令（在国内可以使用清华大学tuna镜像站，下载依赖包更快）：

```bash
pip install PyQt5 -i https://pypi.tuna.tsinghua.edu.cn/simple  #安装PyQt5
pip install PyQt5-tools -i https://pypi.tuna.tsinghua.edu.cn/simple # 安装Qt Designer
```

如图：

<fancybox>![安装pyqt5-tools](https://i.loli.net/2019/10/24/PFBobrTWEOkzQXK.png)</fancybox>

**PS：若使用Anaconda，会省去很多环境配置步骤，这里只需安装PyQt5-tools即可，命令同上。**如果想使用Anaconda，这里有详尽的配置及使用方法：[Anaconda详细安装及使用教程（带图文）](https://blog.csdn.net/ITLearnHall/article/details/81708148)

---

安装成功后，Qt Designer会放在Python（或Anaconda）安装路径，即`安装路径\Python37\Lib\site-packages\pyqt5-tools\Qt\bin`或者`安装路径\Anaconda3\Lib\site-packages\pyqt5-tools\Qt\bin`中，找到`designer.exe`运行即可。

**PS：为了今后打开方便，可以创建一个快捷方式哦~**

<fancybox>![找到designer](https://i.loli.net/2019/10/24/XZRrHEde2uYvFhn.png)</fancybox>

Qt Designer界面如下：

<fancybox>![打开designer](https://i.loli.net/2019/10/24/pu2IiGzkDenbyCJ.png)</fancybox>

**其中：** 左栏为一些常用的一些控件，可以拖放到窗口里面；右栏为不同的控件的值的设定，选中对应的控件便可以编辑。

<fancybox>![控件](https://i.loli.net/2019/10/31/guvYLZ6CyVxdswq.png)</fancybox>

>**关于Qt Designer的使用方法，可以自行找找度娘，使用方法很多啦~~这里放一个教程：**[PyQt5速成教程](https://www.jianshu.com/nb/26159952)

## 导出为.py文件

>当我们按照自己的需求，设计好了相关的界面后，便可以导入成`.py`文件啦~

设计保存后，桌面上会有一个`.ui`文件，这个就是Qt Designer生成的XML格式的文件。

![.ui](https://i.loli.net/2019/10/31/MLTOtmrXivhP3GH.png)

这里，我们使用`pyuic5`这个插件转换文件格式。转到这个文件夹目录后，在命令行窗口输入如下格式命令：

```bash
pyuic5 - o 目标文件名.py 源文件名.ui
```

如我的这样做的：

![cmd](https://i.loli.net/2019/10/31/k4U1hIdCz9sNBVD.png)

此时，会发现，文件夹多了一个转换后的同名`.py`文件：

![同名文件](https://i.loli.net/2019/10/31/uFjVr2PlYImLR3N.png)

打开`SetupUi.py`文件，发现里面代码很长，但是已经**自动生成**了，这样少了很多敲代码的步骤呢~

```python
# -*- coding: utf-8 -*-

# Form implementation generated from reading ui file 'SetupUi.ui'
#
# Created by: PyQt5 UI code generator 5.13.0
#
# WARNING! All changes made in this file will be lost!


from PyQt5 import QtCore, QtGui, QtWidgets


class Ui_mainWindow(object):
    def setupUi(self, mainWindow):
        mainWindow.setObjectName("mainWindow")
        mainWindow.resize(561, 491)
        mainWindow.setAnimated(False)
        self.centralwidget = QtWidgets.QWidget(mainWindow)
        self.centralwidget.setObjectName("centralwidget")
        self.verticalLayout = QtWidgets.QVBoxLayout(self.centralwidget)
        self.verticalLayout.setObjectName("verticalLayout")
        self.tabWidget = QtWidgets.QTabWidget(self.centralwidget)
        self.tabWidget.setObjectName("tabWidget")
        self.tab = QtWidgets.QWidget()
        self.tab.setObjectName("tab")
        self.formLayout = QtWidgets.QFormLayout(self.tab)
        self.formLayout.setObjectName("formLayout")
        self.label1 = QtWidgets.QLabel(self.tab)
        self.label1.setObjectName("label1")
        self.formLayout.setWidget(0, QtWidgets.QFormLayout.FieldRole, self.label1)
        self.graphicsView = QtWidgets.QGraphicsView(self.tab)
        brush = QtGui.QBrush(QtGui.QColor(0, 0, 0))
        brush.setStyle(QtCore.Qt.NoBrush)
        self.graphicsView.setBackgroundBrush(brush)
        self.graphicsView.setObjectName("graphicsView")
        self.formLayout.setWidget(3, QtWidgets.QFormLayout.FieldRole, self.graphicsView)
        self.buttonBox = QtWidgets.QDialogButtonBox(self.tab)
        self.buttonBox.setStatusTip("")
        self.buttonBox.setAccessibleName("")
        self.buttonBox.setOrientation(QtCore.Qt.Horizontal)
        self.buttonBox.setStandardButtons(QtWidgets.QDialogButtonBox.Close|QtWidgets.QDialogButtonBox.Ok)
        self.buttonBox.setCenterButtons(False)
        self.buttonBox.setObjectName("buttonBox")
        self.formLayout.setWidget(4, QtWidgets.QFormLayout.FieldRole, self.buttonBox)
        self.frame = QtWidgets.QFrame(self.tab)
        self.frame.setFrameShape(QtWidgets.QFrame.StyledPanel)
        self.frame.setFrameShadow(QtWidgets.QFrame.Raised)
        self.frame.setObjectName("frame")
        self.formLayout.setWidget(5, QtWidgets.QFormLayout.LabelRole, self.frame)
        self.tabWidget.addTab(self.tab, "")
        self.tab_2 = QtWidgets.QWidget()
        self.tab_2.setObjectName("tab_2")
        self.tabWidget.addTab(self.tab_2, "")
        self.verticalLayout.addWidget(self.tabWidget)
        mainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QtWidgets.QMenuBar(mainWindow)
        self.menubar.setGeometry(QtCore.QRect(0, 0, 561, 23))
        self.menubar.setObjectName("menubar")
        self.menu_options = QtWidgets.QMenu(self.menubar)
        self.menu_options.setObjectName("menu_options")
        self.menu_about = QtWidgets.QMenu(self.menubar)
        self.menu_about.setObjectName("menu_about")
        mainWindow.setMenuBar(self.menubar)
        self.statusbar = QtWidgets.QStatusBar(mainWindow)
        self.statusbar.setObjectName("statusbar")
        mainWindow.setStatusBar(self.statusbar)
        self.action = QtWidgets.QAction(mainWindow)
        self.action.setCheckable(False)
        self.action.setWhatsThis("")
        self.action.setShortcutContext(QtCore.Qt.WindowShortcut)
        self.action.setObjectName("action")
        self.action_2 = QtWidgets.QAction(mainWindow)
        self.action_2.setShortcutContext(QtCore.Qt.ApplicationShortcut)
        self.action_2.setMenuRole(QtWidgets.QAction.QuitRole)
        self.action_2.setObjectName("action_2")
        self.menu_options.addAction(self.action_2)
        self.menu_about.addAction(self.action)
        self.menubar.addAction(self.menu_options.menuAction())
        self.menubar.addAction(self.menu_about.menuAction())

        self.retranslateUi(mainWindow)
        self.tabWidget.setCurrentIndex(0)
        QtCore.QMetaObject.connectSlotsByName(mainWindow)

    def retranslateUi(self, mainWindow):
        _translate = QtCore.QCoreApplication.translate
        mainWindow.setWindowTitle(_translate("mainWindow", "MainWindow"))
        self.tabWidget.setToolTip(_translate("mainWindow", "<html><head/><body><p><br/></p></body></html>"))
        self.label1.setText(_translate("mainWindow", "学生图书管理系统"))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab), _translate("mainWindow", "Tab 1"))
        self.tabWidget.setTabText(self.tabWidget.indexOf(self.tab_2), _translate("mainWindow", "Tab 2"))
        self.menu_options.setTitle(_translate("mainWindow", "选项"))
        self.menu_about.setTitle(_translate("mainWindow", "关于"))
        self.action.setText(_translate("mainWindow", "关于作者"))
        self.action_2.setText(_translate("mainWindow", "退出"))

```

## 使用VS Code编写基础程序

使用VS Code打开这个文件夹，秉承界面和其他操作分离的原则，我们可以新建一个`.py`文件，如命名为`ConnetDatabase.py`，从而调用刚才生成的界面模块，新建一个`MyWindow`的子类，继承于`QMainWindow`和`Ui_mainWindow`父类，并初始化，编写构造函数即可。

最后，创建一个名为`myWin`的对象，启动即可。

```python
import sys
from PyQt5.QtWidgets import QApplication, QMainWindow  #导入PyQt相关模块

from  SetupUi import *  #导入之前新生成的窗口模块

class MyWindow(QMainWindow, Ui_mainWindow):
    def __init__(self, parent=None):
        super(MyWindow, self).__init__(parent)
        self.setupUi(self)


if __name__ == '__main__':
    app = QApplication(sys.argv)
    myWin = MyWindow()  #创建对象
    myWin.show()    #显示窗口
    sys.exit(app.exec_())

```

运行后的效果如图：

![运行](https://i.loli.net/2019/10/31/cjeohUJV6giNySr.png)

>**至此，基本的窗口创建成功，便可以开始写后端的具体代码实现了~**

## 参考资料

1. [八款常用的 Python GUI 开发框架推荐](https://blog.csdn.net/weixin_37773766/article/details/81535876)
2. [Python制作小软件——1. 安装并使用PyQt5进行界面设计](https://blog.csdn.net/weixin_41929524/article/details/81456308)
3. [Qt官网——Getting to Know Qt Designer](https://doc-snapshots.qt.io/qt5-dev/designer-to-know.html#)
4. [Qt官网——Qt Designer Manual](https://doc-snapshots.qt.io/qt5-dev/qtdesigner-manual.html)
5. [PyQt5快速入门（二）PyQt5信号槽机制](https://blog.51cto.com/9291927/2422187)
6. [【第一节】PyQt5简介](http://code.py40.com/1948.html)
