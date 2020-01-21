---
title: ［Spring Boot］ 搭建源码调试环境
toc: true
categories:
  - [Spring, 深入spring boot]
tags:
    - Spring 
    - Springboot 
    - 源码
date: 2019-07-16 16:32:53
updated: 2019-07-16 16:32:53
---

**<center><font size="6">读源码是成长最快的学习方法。</font></center>**

> 这篇文件主要讲述了怎么在本地搭建Spring Boot 源码的调试环境
<!-- more -->
## 环境信息
- macOS Mojave 10.14.5
- jkd1.8+
- maven 3.5.2
- Idea 2019.1.2

## 事先准备
### 增加idea内存
Intellij IDEA 的默认Xmx是750， 由于Spring Boot 源码非常庞大，经常会导致IDEA内存溢出，所以我事先将IDEA的Xmx 内存调整为2048m。
IDEA
IDEA内存调整方法见文章：https://blog.csdn.net/qq_27093465/article/details/81947933
### 增加maven内存

添加环境变量“MAVEN_OPT=-Xmx512m” 

修改方法请自行Google

## 源码拉取
### fork 官网仓库到自己的空间
方便保存注释
### clone 源码到本地磁盘

```bash
## 请自行替换你自己的仓库地址
git clone git@github.com:huotaihe/spring-boot.git
```

### 查看最新稳定tag
```bash
## 执行命令
git tag |sort -r

## 输出结果如下
v2.2.0.M4
v2.2.0.M3
v2.2.0.M2
v2.2.0.M1
v2.1.6.RELEASE ## 当前发行版本
v2.1.5.RELEASE
v2.1.4.RELEASE
v2.1.3.RELEASE
v2.1.2.RELEASE
v2.1.1.RELEASE
...

```

### checkout 当前发行版本
```bash
git checkout -b 2.1.6.RELEASE v2.1.6.RELEASE
```

## 构建源码

```bash
./mvnw clean install -e -DskipTests
```

本步骤非必要，但是提前构建效率更高，不然后面导入源码到IDEA或下载依赖，需要很长时间。

## 将源码导入IDEA
通过菜单
> File > Open

打开源码目录，IDEA会开始构建工程结构（时间会比较长）

### 检验源码工程构建结果
等源码导入完成构建完成后，执行菜单
> Build > Build Project

构建工程源码，如果没有报错信息
恭喜你已经成功搭建了源码调试环境

### 异常问题解决
现实总是残酷的，编译工程往往会报一些类解析不到。
这是因为一些module的工程结构idea解析的不正确。
解决方法:
  找到对应module，在module的pom.xml文件上执行右键菜单命令
  > Maven > reimport
  idea 会重现构建module的工程结构

  安全起见可以把每个module都遍历一下

## 验证

运行spring-boot-hibernate52-tests 如果启动成功
说明环境已经搭建成功了
