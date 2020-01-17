---
title: CentOS 安装 Mysql
toc: true
categories:
  - Mysql 
tags:
  - Linux
  - Mysql
  - 环境搭建
date: 2020-01-17 22:55:39
updated: 2020-01-17 22:55:39
---
# 卸载原有mysql
## 检查是否已经安装Mysql
```bash
rpm -qa | grep mysql
```

## 卸载已经安装的Mysql
```bash
## 删除mysql相关目录
find / -name mysql |xargs rm -rf
```

<!-- more -->

# 安装新的mysql
## 安装yum源
```bash
yum localinstall mysql57-community-release-el7-8.noarch.rpm
```

## 安装mysql服务
```bash
yum install -y mysql-community-server
```

## 检查mysql安装结果
```bash
[root@baimaduhe src]# mysql -V
mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper
```

## 启动Mysql 服务
```bash
systemctl start mysqld
```

## 设置Mysql 开机启动
```bash
systemctl start mysqld
```

## 获取Mysql 初始密码
```bash
[root@baimaduhe src]# grep 'temporary password' /var/log/mysqld.log
2020-01-17T15:12:08.755778Z 1 [Note] A temporary password is generated for root@localhost: ;QsQkdLBl6kR
```

## 连接到mysql
```bash
[root@baimaduhe src]# mysql -uroot -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.29

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

# 配置mysql

## 将密码强度设置为低
**学习用为了方便才这样设置，生产环境请勿这样设置**
```bash
 set global validate_password_policy=LOW;
```

## 修改mysql
```bash
mysql> ALTER USER USER() IDENTIFIED BY 'new_password';
```

## 允许远程访问
```bash
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```
## 刷新配置
```bash
FLUSH PRIVILEGES;
```

## 远程登录mysql
```bash
mycli -u root -p<your_password> -h <service ip> -P 3306
```

# 参考资料
- https://www.cnblogs.com/123hll/p/9633422.html
- https://blog.csdn.net/muziljx/article/details/81541896