---
title: 使用阿里云ECS安装mysql及远程登录的一些小问题
date: 2018-10-15 22:52:15
categories:
- 数据库
tags:
- Mysql
- Bug
---

**1.Unable to locate package xxx** 

![在这里插入图片描述](使用阿里云ECS安装mysql及远程登录的一些小问题/20181015222129131.png)

解决方法:

    sudo apt-get update

**2.远程登录**
		选择创建一个用于远程登录的用户:

```shell
	GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```
生效:

```shell
flush privileges;
```
​		接下来一步,看了网上许多说的,都是vim /etc/mysql/my.cnf 将里面的bind-address = 127.0.0.1改为0.0.0.0,我试了下 然后进行重启服务(/etc/init.d/mysql restart),最后遇到报错:
mysql.serviceJob for mysql.service failed because the control process exited with error code. See "systemctl status mysql.service" and "journalctl -xe" for details.failed!

![在这里插入图片描述](使用阿里云ECS安装mysql及远程登录的一些小问题/20181015222017843.png)

???
![在这里插入图片描述](使用阿里云ECS安装mysql及远程登录的一些小问题/20181015222342177.png)
回去 仔细看/etc/mysql/my.cnf
我去!!!
![在这里插入图片描述](使用阿里云ECS安装mysql及远程登录的一些小问题/20181015222729516.png)
emm他的意思是说配置文件在那俩目录下...果然在操作的时候还是要仔细看下的- -
两个我都进去看了下 结果是在

```shell
vim  /etc/mysql/mysql.conf.d/mysqld.cnf
```
找到bind-address改为0.0.0.0(任意ip可访问),也可以设成自己的,在第一次那个文件里我还好奇咋没有bind-address,虎得我直接加了一行上去...

![在这里插入图片描述](使用阿里云ECS安装mysql及远程登录的一些小问题/20181015223338455.png)

重启服务  /etc/init.d/mysql restart  ,ok
Navicat连接测试成功

<img src="使用阿里云ECS安装mysql及远程登录的一些小问题/20181015224839569.png" alt="在这里插入图片描述" style="zoom:50%;" />



**重要**
netstat -anp|grep 3306
查看3306端口有没有开启,没有的话去阿里云那添加安全规则,不细写了

