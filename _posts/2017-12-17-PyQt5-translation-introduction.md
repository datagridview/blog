---
layout: page
title: PyQt5-简介
---

最近在研究GUI，主要用于展示自己一些比较不错的应用。不然别人不知道自己做的东西有多有趣。

这是由何翻译的PyQt5的使用说明这次是（一），简介，用于让你学会使用PyQt5套件。所有的案例已经在Linux上进行过测试。PyQt4涵盖了Python2.x和Python3.x。

## 关于PyQt5

PyQt5是一个连结Digia公司Qt5产品的Python使用套件，支持Python2和Python3。本教程采用Python3讲述。Qt框架是最具功能性的GUI框架。PyQt5由RiverBank公司开发。

PyQt5实现了一套Python模块。他拥有超过630个类和超过6000个方法。它可以工作在多平台，包括Unix，Windows和MacOS。PyQt5被双重授权。你可以选择GPL，也可以选择商业用许可。

## 安装PyQt5

```shell
$ sudo pip3 install pyqt5
```

## 模块

PyQt5被分为多个模块，包含以下：

- QtCore
- QtGui
- QtWidgets
- QtMultimedia
- QtBluetooth
- QtNetwork
- QtPositioning
- Enginio
- QtWebSockets
- QtWebKit
- QtWebKitWidgets
- QtXml
- QtSvg
- QtSql
- QtTest

QtCore模块没有GUI功能。该模块被用来控制时间，文件及目录，多数据类型，流，URL，媒体格式，线程和进程。QtGui包含窗口系统集成，事件处理，2D图形，基本图像，字体和文本类。QtWidgets模块包含提供用来创建一系列经典桌面风格的UI对象的类。QtMultimedia包含用来控制多媒体内容和照相机等多媒体设备的接口的类。QtBluetooth模块包含用于扫描和连接外部设备的类。QtNetwork包含用于网络编程的类。这些集成了TCP/IP和UDP的类使得网络编程更加的简单、便携。QtPositioning包含通过一系列类似卫星、WIFI、文本文件等源来确定位置。Enginio模块实现了用于访问Qt云服务托管应用程序运行时的客户端库。QtWebSockets模块包含了实现了WeobSocket协议的类。QtWebKit包含了基于WebKit2库实现的网络浏览器的类。 QtWebKitWidgets包含了基于Web QtWidgets应用程序的基于WebKit1的Web浏览器实现的类。QtXml包含了用于与Xml文件的类。这个模块实现了SAX和DOM APIS两种方式。QtSvg提供了用于展示svg内容的类。SVG是一种用xml用来表示2D图像和应用的文件格式。QtSql提供了用于操作数据库的类。QtTest用于提供Qt的应用测试。

## PyQt4和PyQt5的差异

PyQt5不与PyQt4向后兼容; PyQt5有几个重大的变化。但是，将旧代码调整到新库不是很困难。其中的差异包括以下几点：

- Python模块已经被重新组织。一些模块已被删除（QtScript），其他模块已拆分成子模块（QtGui，QtWebKit）。
- 新的模块已经出台，包括QtBluetooth，QtPositioning或Enginio。
- PyQt5只支持新式信号和插槽handlig。呼叫SIGNAL() 或SLOT()不再支持。
- PyQt5不支持在Qt v5.0中被标记为废弃或废弃的Qt API的任何部分。



This chapter was an introduction to PyQt5 toolkit.

Source: [pyqt5-introduction](http://zetcode.com/gui/pyqt5/introduction/)

translated by: **datagridview**