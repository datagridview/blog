---
layout: page
title: WhatsNext TODO工具
---

由个人编写的基于shell/cmd的命令行 todo 任务工具 [项目地址](https://github.com/datagridview/WhatsNext)

## 前提

* 已安装redis
* 已安装python
* 已安装argparse

## 配置

```python
r = redis.StrictRedis(host='127.0.0.1', port=6379, db=0)
```

远端数据库填写远端ip，本机填写127.0.0.1。具体redis python配置有时间出一篇。redis操作见我的[wiki](http://wiki.heyunfan.com)。

## 安装

把whatsnext克隆到本地

```powershell
git clone git@github.com:datagridview/whatsnext
```

linux下修改shell配置文件

```powershell
alias wn='python todo.py'
```

windows下进入master文件夹，执行以下命令查看帮助（此处是我的路径）

```powershell
C:\Users\**\Desktop\whatsnext>python todo.py -h
usage: todo.py [options] [TEXT]

Command-line todo - t Help

optional arguments:
  -h, --help            show this help message and exit

Action:
  -a ADD [ADD ...], --add ADD [ADD ...]
                        add a task
  -u UPDATE [UPDATE ...], --update UPDATE [UPDATE ...]
                        update a task in aspect of time-to-finish field
  -f FINISH [FINISH ...], --finish FINISH [FINISH ...]
                        finish a task
  -d DELETE [DELETE ...], --del DELETE [DELETE ...]
                        delete a task

Show:
  --done                List all the done tasks
  --undo                List all the undo tasks
  --deleted             List all the deleted tasks
  --all                 List all done/undo tasks
```


# 使用方法 #
## 查看todo/done任务 ##
```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py --all
-----------------------------------
here is all the done/undo tasks.
-----------------------------------
[UNDO]20170830:操作系统
[UNDO]20170813:写一篇js新一章的wiki
[UNDO]20170101:复习docker
[UNDO]20170815:上学
[UNDO]20170903:caffeRelatedPresentation
[UNDO]20170814:自强学堂django
-----------------------------------
[DONE]20170101:finshThePaper
[DONE]20170811:undoPython实现
-----------------------------------
    
```
`[DELE]` `[DONE]` `[UNDO]`三种标签，日期格式将会优化。

## 查看任务 ##
使用 `--done` 命令可以查看已完成任务
```  powershell
C:\Users\云帆\Desktop\myTODO>python todo.py --done
[DONE]20170811:undoPython实现
[DONE]20170101:finshThePaper
```
使用 `--undo` 命令可以查看未完成任务
```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py --undo
[UNDO]20170815:上学
[UNDO]20170101:复习docker
[UNDO]20170813:写一篇js新一章的wiki
[UNDO]20170830:操作系统
[UNDO]20170903:caffeRelatedPresentation
[UNDO]20170814:自强学堂django
```
使用 `--deleted` 命令可以查看已删除任务
```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py --deleted
[DELE]20170812:ni
[DELE]20180911:wiki
[DELE]20170811:javascript
```

## 新增任务 ##
使用命令 `-a [task_name] [time_to_finish]  `  可以新增任务

```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py -a 努力工作 20180101
[UNDO]20170814:自强学堂django
[UNDO]20180101:努力工作
[UNDO]20170830:操作系统
[UNDO]20170813:写一篇js新一章的wiki
[UNDO]20170903:caffeRelatedPresentation
[UNDO]20170101:复习docker
[UNDO]20170815:上学
```

## 修改任务 ##
使用命令 `-u [task_name] [time_to_refresh]`可以修改deadline

```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py -u 上学 20191010
[UNDO]20191010:上学
[UNDO]20170813:写一篇js新一章的wiki
[UNDO]20170814:自强学堂django
[UNDO]20170101:复习docker
[UNDO]20180101:努力工作
[UNDO]20170830:操作系统
[UNDO]20170903:caffeRelatedPresentation
```

## 完成任务 ##
使用命令`-f [task_name]`可以将其作为已完成任务

```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py -f 写一篇js新一章的wiki
[DONE]20170811:undoPython实现
[DONE]20170813:写一篇js新一章的wiki
[DONE]20170101:finshThePaper
```

## 删除任务 ##
使用 `t -d [task_name]` 可以直接删除一个任务

```powershell
C:\Users\云帆\Desktop\myTODO>python todo.py -d 上学
[DELE]20180911:wiki
[DELE]20170812:ni
[DELE]20191010:上学
[DELE]20170811:javascript
```






# 版本 #

## v1.0功能 ##
* 增加任务

* 完成任务

* 删除任务

* 修改任务

* 查看DONE任务

* 查看UNDO任务

* 查看删除区任务

  ​

# TODO

* 日期的标准显示
* 加入ID使操作更方便
* 按deadline排序
* js实现
* 删除区恢复
* 按日期filter

**datagridview**