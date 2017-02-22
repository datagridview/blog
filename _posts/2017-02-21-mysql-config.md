---
layout: page
title: Windows下配置MySql(ZIP)
---

现在的B/S模式，最热的应该算是php+MySql+前端，很多大型的网站就是这种架构。当然，也有很多是微软的各类MSSQL+服务器后端(ASP,ASPX)+前端。今天我就从mysql的安装配置这个最基本的点来叙述，毕竟我们是一个喜欢安装软件的专业（笑）

首先，要想从正规渠道获得mysql，不用在意什么破解什么的，因为正常的mysql就是开源的软件，所以我们不会有良心债（话说你们下盗版的时候良心就不会痛吗）正经链接点[dev.mysql.com](https://dev.mysql.com/downloads/mysql/)

## 下载

能够下载这个mysql的前提是要有一个oracle的账号，大家去注册一个吧也不需要付费.我们需要下载的就是GA Releases版的，按照电脑的系统位数来进行下载，我的电脑是win10 64位版本的，所以在这里我选择第二项355.3M这个版本。之后静静等待下载完毕~
![image](/public/source/mysql.png)

## 解压缩

下载完毕之后，就是对这个zip进行解压缩.在这里我是将文件直接解压缩到我的E:/，解压之后你的mysql文件夹下的结构应该是这样的：
```
->mysql-5.7.17-winx64
-->bin
-->docs
-->includes
-->lib
-->shar
-->COPYING
-->my-defult.ini
-->README
```



## 开始配置

### 系统变量的配置

现在我们先完成系统环境变量的配置，那为什么要完成系统环境变量的配置呢？简单说来，配置系统环境变量就是一种直接用命令行调用函数的方法，比如说我安装好了python，我想cmd.exe/python直接打开它的工作台，而不是要一步一步cd e:\python 3.5\python.exe 这样打开。环境变量配置成功，就相当于简化了这个寻找的过程。

配置的顺序是：**此电脑右键->属性->高级系统设置->高级（选项卡）->环境变量**。
当然，再下一层选择系统变量（而不是用户变量），找到path，点击新建，将mysql的地址复制到上面（我的是E:\mysql-5.7.17-winx64\bin），之后确认一次退出新建页面，再确认一次退出环境变量配置。

这时候win+r打开运行，如果输入`mysql -V`显示当前mysql版本，配置成功。
### 配置文件中的basedir、datadir

打开my-defult.ini，更改其中的basedir、datadir，base后等于mysql文件包地址
```
basedir=E:\mysql-5.7.17-winx64
datadir=E:\mysql-5.7.17-winx64\data
```

但是我们并没有data文件夹？没关系，我们接下来初始化mysql可以生成data文件夹
### mysql命令行配置

用管理员的权限打开终端，用命令行操作进入mysql的本地文件夹
#### 依次输入(进入bin文件夹)

```
cd ..\\..
e:
cd mysql-5.7.17-winx64\bin
```
接下来的操作都在这里进行
#### 初始化mysql

```
mysqld initialize --user=mysql --console
```

会在mysql目录下会生成一个data文件夹

#### 安装mysql服务

```
mysqld install mysql
```
成功后显示`Service successfully installed.`
#### 启动mysql服务

```
net start mysql
```

此处会可能产生一些问题，以后更新
正常情况先，会产生两行的提示，提示服务已经成功启动
### 登录数据库

```
mysql -u root -p
```

![error](/public/source/error.png)回车键进入之后输入密码（可是你并不知道密码呀~~），一般会产生错误error 1045
#### 解决方法

创建一个my.ini，在其中输入以下代码（需要更改一下扩展名）
```
[mysqld]
skip-grant-tables
```
记得保存。
#### 关闭服务并重新启动

```
net stop mysql
net start mysql
mysql -u root -p
```
这时候我们不用输入密码，就直接回车，提示`Welcome...`就代表已经成功了。
#### 更改登陆用户

首先进入mysql数据库：
```
use mysql
```
查询用户信息：
```
select * from user
```
![mima](/public/source/mima.png)
此处一般authentication_string是密码项。执行update操作：(我的密码是root)
```
update user set authentication_string = password("root") where user="root";
```
这里记得加分号，所有的sql语句命令都要加分号~
成功提示。
![tishi](/public/source/tishi.png)
用`exit`退出mysql console界面。

之后删除my.ini中的`skip-grant-tables`，再重新启动mysql服务，这次登陆需要刚才设置的密码了。

现在，到此为止你就可以使用mysql数据库了。








