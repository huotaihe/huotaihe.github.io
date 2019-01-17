---
title: springboot 打包时跳过单元测试
date: 2018-12-18 10:14:49
categories: [Spring, 深入spring boot]
toc: true
tags: 
    - Spring 
    - Springboot 
    - 打包
---

## 通过修改pom.xml
> 在项目的pom.xml 文件的 &lt;properties&gt; 节点中增加如下节点
```xml
<!-- maven方式跳过maven test, 等同 mvn package -Dmaven.test.skip=true -->
<maven.test.skip>true</maven.test.skip>
```
<!-- more -->
## 通过命令行参数
> mvn命令后添加参数 -Dmaven.test.skip 或者 -DskipTests
```bash
mvn package -Dmaven.test.skip=true
# 或者
mvn install -DskipTests
```
