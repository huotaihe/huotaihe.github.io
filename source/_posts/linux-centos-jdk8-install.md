---
title: 【服务器环境搭建】之Centos安装jdk8
toc: true
categories:
  - 服务器环境搭建
tags:
  - 环境搭建
  - jdk
  - Linux
date: 2020-01-20 18:05:03
updated: 2020-01-20 18:05:03
---

## 环境信息
- CentOS Linux release 7.7.1908 (Core)
- Iterm2
- jdk-8u241-linux-x64

## 安装步骤
## 下载安装包

安装包下载地址：https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

<!-- more -->
```bash
wget https://download.oracle.com/otn/java/jdk/8u241-b07/1f5b5a70bf22433b84d0e960903adac8/jdk-8u241-linux-x64.tar.gz?AuthParam=1579514658_64aacc4c1cc8053ac1d4b3a981c01029
```

## 解压安装包
```bash
[root@baimaduhe src]# tar -xzvf jdk-8u241-linux-x64.tar.gz
```

## 把解压文件移动到指定目录
```bash
[root@baimaduhe src]# mv jdk1.8.0_241 /usr/local/java
```

## 修改环境变量
使用VIM打开.bash_profile文件
```bash
[root@baimaduhe java]# vim ~/.bash_profile
```

## 添加java信息
```vim
export PATH
export JAVA_HOME=/usr/local/java
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}
```

## 刷新配置文件
```bash
[root@baimaduhe java]# source ~/.bash_profile
```
## 检查安装是否成功

```bash
[root@baimaduhe java]# java -version
## 出现如下内容说明安装成功
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
```
---
## 参考资料
- https://www.cnblogs.com/kingsonfu/p/9801556.html