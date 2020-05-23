---
title: Mysql Err1055 ONLY_FULL_GROUP_BY 存储过程
date: 2019-01-21 22:05:30
categories:
- 数据库
tags:
- Mysql
- Bug
---

## 环境
Ubuntu16.04
Mysql5.7
**1.背景**
Err 1055 报错 sql_moede为ONLY_FULL_GROUP_BY,具体错误原因不介绍了.按提示改变sql_mode后存储过程中仍报改错.
**2.解决方法**
修改/etc/mysql/my.cnf 可以在最后加上如下
```mysql
[mysqld]
sql_mode ="STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"
```
然后 service mysql restart 重启服务
还有一些其他的设置sql_mode的方法,可自行了解
但是按照上面方法做完后在查询中不会再报1055,但是使用存储过程仍旧有问题,解决方法就是
...
...
...
复制,删除,新建,粘贴!!!
新建一个存储过程就OK,具体原因不了解,总之是个大坑