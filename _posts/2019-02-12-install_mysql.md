---
layout: post
tags: mysql
title: centos上安装mysql
---

在安装mysql之前，先将原来如果有下载过了的mysql 删除完全:
~~~python
#快速删除
yum remove  mysql mysql-server mysql-libs mysql-server
#查找残留文件
rpm -qa | grep -i mysql
#将查询出来的文件逐个删除，如还残留这个文件mysql-community-common-5.7.20-1.el6.x86_64：
yum remove mysql-community-common-5.7.20-1.el6.x86_64
#查找残留目录
whereis mysql
#逐条删除目录
rm –rf /usr/lib64/mysql
~~~

~~~python
#检测系统是否存在mysql
yum list installed | grep mysql
#删除mysql依赖项
yum -y remove mysql-libs.x86_64
~~~

~~~python
#给CentOS添加rpm源，并且选择较新的源
wget dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
yum localinstall mysql-community-release-el6-5.noarch.rpm
yum repolist all | grep mysql
yum-config-manager --disable mysql55-community
yum-config-manager --disable mysql56-community
yum-config-manager --enable mysql57-community-dmr
yum repolist enabled | grep mysql
~~~

~~~python
#安装mysql 服务器
yum install mysql-community-server
~~~

下面登录mysql
直接输入命令
~~~
mysql -u root -p
~~~
但是你不知道密码，这个密码是随机的，我们不知道，接下来我们修改一个文件
~~~
vi /etc/my.cnf
~~~
在这个里面的[mysqld]下面添加一行
~~~
skip-grant-tables
~~~

接下来直接输入命令
~~~
mysql
~~~
就直接进去了

下面是修改密码
~~~
mysql> set password for root@localhost = '123456';
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
mysql> flush privileges;
Query OK, 0 rows affected (0.03 sec)
mysql> set password for root@localhost = '123456';
Query OK, 0 rows affected (0.03 sec)
~~~
运行这三条命令即可，第一条运行后会报错，	
再输入二条，	
第三次同样输入第一次的那个命令，成功	
如果尝试普通修改密码的那些命令，会失败	
接下来，退出mysql
~~~
mysql> exit;
~~~
再次用密码(123456)登录
~~~
mysql -u root -p
~~~
成功!!!

下面如果想在本地连接服务器上的mysql,先要在服务器上，进入mysql
~~~
#赋予权限，可以进行远程登录
grant all privileges on *.* to 'root'@'%' identified by 'password';
flush privileges;
~~~
这个password自己设置，可以和服务器上的mysql登录密码不同，用于本地登录
~~~
mysql -h IP -u username -p
~~~
成功！！！

refference:https://www.jianshu.com/p/7b8c4dea6829

