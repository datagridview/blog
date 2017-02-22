---
layout: page
title: Sublime Text 3+Python简易IDE
---
如果你厌倦了安装各种各样的IDE，同时你的电脑的性能并不怎么样，你可以选择使用Sublime Text。选择它的原因有很多：
>1、轻量级，最大的版本不超过10M；
>2、插件机制，安装之后可以直接编译程序；
>3、代码高亮，对象清晰，思路清晰；
>4、支持的语言代码非常之多，从你听说过的Java、html、c、c++，到我们不太熟悉的ruby、Go、Erlang，应有尽有（但并不代表着它每一个都能编译）；
>5、好看。

官网对此有十分简单的介绍：
>Sublime Text is a sophisticated text editor for code, markup and prose.
You'll love the slick user interface, extraordinary features and amazing performance.
它是一个可以用来写代码，标记和散文的复杂文本编辑器。
你会喜欢光滑的用户界面，非凡的功能和惊人的性能。

很久之前我就开始使用了这款编辑器，原因是受不了vs打开的速度相对较慢；后来接触了python之后，安装了pycharm之后发现这个基于IntelliJ IDEA的IDE平台在windows上，都十分的卡。于是我钻研了一下这个编译器的插件，阅读了很多的资料，完成了Sublime Text 3+c、+python，+markdown，+textile（后两个是写作语法）的环境，那我们今天就来说一下Sublime Text 3+python 的配置过程。
## 下载
现在，我默认你已经装好了python，无论是python2.7、python3.5，还是两者都共存都行。安装好的标志是，在终端中输入`python -V`，会输出python的版本号，这样我们可以认为既安装好了python，也配置好了环境变量（环境变量的配置基本上和我上一篇的系统变量的配置类似）
官方网站安装地址（略慢）：[Sublime Text 3官网](https://download.sublimetext.com/Sublime%20Text%20Build%203126%20x64%20Setup.exe) 
国内第三方软件安装地址：[Sublime Text 3国内](http://soft2.xzstatic.com/2016/02/Sublime%20Text%20Build%203083.zip)
接下来的应该都不难了，我这里就不再啰嗦了~
安装完毕之后，现在应该是一个纯净版的sublime，所以里面不会有很多的功能，但是几乎支持了所有的代码语法标注和高亮。

![pure](/public/source/pure.png)
## 安装python插件
### 安装package control 插件控制包
使用快捷键ctrl+`（这里的点是Tab上面的点）打开console控制栏，复制以下代码。这个是用python写的一段代码，远端下载package control。
```
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by) 
```
重启之后preference->package control，代表安装成功。
### 下载SublimeCodeIntel
[SublimeCodeIntel 的github下载地址](https://github.com/SublimeCodeIntel/SublimeCodeIntel)在这个页面里看见绿色按钮，点击其中的download zip按钮，下载文件包。解压到Packages文件夹中。
当然，可能你们会问Packages目录在哪里？按照以下的思路，点击上面的菜单栏：preference->page settings->package control->Setting-Default你会在Sublime中打开一个文件，右键标签，open file之后你就可以拿到你想要的地址了。我的地址是：
```
C:\Users\云帆\AppData\Roaming\Sublime Text 3\Packages
```
之后重启sublime text再打开，就会发现preference->page settings中有SublimeCodeIntel的选项。

![codeintel](/public/source/codeintel.png)
### 将python库之间关联到sublime 的codeintel中
依次点击preference->page settings->SublimeCodeIntel->Settings-Default，会打开一个SublimeCodeIntel.sublime-settings的文件，在最后你可以找到这样的一行代码，它存储了建立联系的程序语言内核：
```
    "codeintel_language_settings": {
        "JavaScript": {
            "codeintel_scan_extra_dir": [],
            "codeintel_scan_exclude_dir":["/build/", "/min/"],
            "codeintel_scan_files_in_project": false,
            "codeintel_max_recursive_dir_depth": 2,
            "codeintel_selected_catalogs": ["jQuery"]
        },
        "PHP": {
            "php": "/Applications/MAMP/bin/php/php5.5.3/bin/php",
            "codeintel_scan_extra_dir": [],
            "codeintel_scan_files_in_project": true,
            "codeintel_max_recursive_dir_depth": 15,
            "codeintel_scan_exclude_dir":["/Applications/MAMP/bin/php/php5.5.3/"]
        }
    }
```
你要在这一个字典里插入与python基本包的信息：
```
/* 这里是我的python 3.5基本包的地址，大家酌情哈 */
        "Python3": {
           "python":"E:/python 3.5/python.exe",    
             "pythonExtraPaths":    
                [    
                    "E:/python 3.5",  
                    "E:/Python 3.5/DLLs",  
                    "E:/Python 3.5/Lib",   
                    "E:/Python 3.5/Lib/site-packages"    
                ]
            },
```
## 通过Ctrl+B直接编译python.py
### 构建system执行命令
打开选项卡中tools->Build System->New Build System
这时候会打开一个新的页面，输入以下的代码：
```
/* 这里是我的python 3.5基本包的地址
   注意两个\是为了屏蔽转义字符
   file_regex是一个正则表达式，用来匹配命令地址
 */
{
"cmd": ["E:\\python 3.5\\python.exe","-u","$file"],
"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
"selector": "source.python",
"encoding": "cp936",
}
```
保存到packages\users包中，文档名为Python.sublime-build。
重启sublime text。选择tools->Build System->python，这是选择了已经定义好的编译环境。
#### 测试
右下角默认是plain text文档格式，点击选择python。写一段python的编码：
```
#这里是python 3.5,如果是python 2.7，则测试代码为print 'Hello Heyunfan'酌情选择
print('Hello Heyunfan')
```
##### 运行错误
有时候会显示如下错误：

![python error](/public/source/python error.png)

这是因为你没有保存，保存到任意路径 XXX.py文件，再ctrl+b运行，即可成功。

![python sucess](/public/source/python sucess.png)



<br>
## 总结
到这里，基本的python的配置已经完成了，大家可以尝试一下~当然，这个简易的IDE也有很多的问题，比如调试没有pycharm简单，不能设置断点；不能实现输入raw_input,也就是不能手动的交互。当然，对于那些简单的，输入可以代码设置的，运行就完全都没有问题，甚至可以编写一个UI界面、编写一个服务器端的逻辑结构（当然也就不好维护了）。

如果有问题，可以在微博里私信给我~我会改进这篇文章的=.=

##### datagridview