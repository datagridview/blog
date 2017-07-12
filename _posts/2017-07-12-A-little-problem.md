---
layout: page
title: PyMySQL的commit事务的辨析
---
近来忙着考试，忙着参加夏令营，去浙大做presentation的时候才会觉得自己的经历和奖项有多么的稀少，博客也荒了好久，不过应该没什么人看（看的人都是真爱），现在回头看来我之前写的一些教程文章在别人看来是非常的简单甚至不值得提。还有，stackoverflow是个好网站，这世界上总有一些人比我们先遇到了一些问题。最近要勤于动笔，暑假是学东西的好时候！今天来和大家说一下刚才遇到的一个小问题。是有关PyMySQL的事务提交的。

## 问题的产生
```
#!/usr/bin/python3
import pymysql
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()
# 使用 execute() 方法执行 SQL，如果表存在则删除
cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
# 使用预处理语句创建表
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(20) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,  
         SEX CHAR(1),
         INCOME FLOAT )"""
cursor.execute(sql)
# 关闭数据库连接
db.close()
```
这里的重点在cursor.execute()，数据库就已经执行并且有了作用，表被创建了。
但是接下来：


```
#!/usr/bin/python3
import pymysql
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
# SQL 插入语句
sql = """INSERT INTO EMPLOYEE(FIRST_NAME,
         LAST_NAME, AGE, SEX, INCOME)
         VALUES ('Mac', 'Mohan', 20, 'M', 2000)"""
try:
   # 执行sql语句
   cursor.execute(sql)
   # 提交到数据库执行
   db.commit()
except:
   # 如果发生错误则回滚
   db.rollback()
# 关闭数据库连接
db.close()
```
为什么？？为什么这里一定要commit才生效？execute之后是不被执行的。

## 解答

找到了一篇文章（节选）:

>然后通过百度查找发现，其实MySQLdb库，自带了事务处理的功能，pymysql库也是一样。
conn = MySQLdb.connect()返回一个连接对象
cursor = conn.cursor()返回一个游标对象
当我们使用cursor.execute(SQL)执行一条Insert/Update/Delete语句后，我们需要使用conn.commit()提交事务，否则，语句不会生效。
其实我们常用的commit()，就是包含了一种事务处理的概念，如果你在commit()之前，执行了多条语句，只有当commit()之后，才会全部生效。
那么如果像文章开始提到的那样，执行两条插入语句，我们可以把conn.commit()语句放在两条插入语句执行之后，并放在try语句块内，如果差生异常，我们可以使用：
conn.rollback()
这个语句可以使事务回滚，直到上一条conn.commit()执行之后的位置。
所以，我的代码是这样写的，这里我就简单的写了，并没有用类来封装了：
try:
cursor = conn.cursor()
cursor.execute(insertSQL1)
cursor.execute(insertSQL2)
cursor.close()
conn.commit()
except Exception, e:
cursor.close()
conn.rollback()


重点在于“当我们使用cursor.execute(SQL)执行一条Insert/Update/Delete语句后，我们需要使用conn.commit()提交事务”
！！！所以除了以上几点别的可以直接execute()

快速的写完一篇，以后学到什么再在这篇基础上改。

##### datagridview








