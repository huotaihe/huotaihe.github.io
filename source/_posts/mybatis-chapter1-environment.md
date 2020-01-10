---
title: 【深入学习Mybatis】Chapter1源码调试环境搭建
toc: true
categories:
  - temp
tags:
  - temp
date: 2020-01-10 18:09:40
updated: 2020-01-10 18:09:40
---


## Mybatis简介
> Mybatis 是一个一流的持久化框架，支持自定义SQL，存储过程和高级映射。
Mybatis几乎消除了所有的JDBC代码以及参数的手动设置和结果检索。Mybatis可以使用简单的XML或注释进行配置，并将原函数，映射接口和Java Pojo 映射到数据库记录。
>> MyBatis is a first class persistence framework with support for custom SQL, stored procedures and advanced mappings. MyBatis eliminates almost all of the JDBC code and manual setting of parameters and retrieval of results. MyBatis can use simple XML or Annotations for configuration and map primitives, Map interfaces and Java POJOs (Plain Old Java Objects) to database records.

<!-- more -->

## 环境信息
- macOS Catalina
- Git
- JDK8
- Maven3
- Intellij

## 克隆源码到本地

```bash
git clone https://github.com/mybatis/mybatis-3.git
```

## 将源码导出本地


File > New > Project from Existing sources

选择源码目录将源码导入Intellij ，等待IDEA构建项目结构，时间可能会长一点儿

导入结构如图
![mybatis 源码包结构图](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlcy1vc3MuaHVvdGFpaGUuY29tL215YmF0aXMvbXliYXRpcy1pbnRlbGxpai1zdHJ1Y3R1cmUucG5n?x-oss-process=image/format,png)

## 运行测试用例

```java
org.apache.ibatis.autoconstructor.AutoConstructorTest.fullyPopulatedSubject()
```

控制台输出如下信息说明环境搭建成功
```bash

Process finished with exit code 0

```

## 源码包结构说明
```bash
.
└── org
    └── apache
        └── ibatis
            ├── annotations \\ 注解模块 包含所有在mapper接口用到的注解
            ├── binding \\Bings mapper interfaces with mapped statements
            ├── builder \\ Base package for the Configuration building code
            ├── cache \\ 缓存模块
            ├── cursor \\ 游标模块
            ├── datasource \\ 数据源模块
            ├── exceptions \\ 异常模块
            ├── executor \\ 执行模块
            ├── io \\ 读取资源的工具
            ├── jdbc \\ JDBC工具类
            ├── lang \\ 
            ├── logging \\ 日志模块
            ├── mapping \\ 映射模块
            ├── parsing \\ 解析工具
            ├── plugin \\ 插件模块
            ├── reflection \\ 反射模块
            ├── scripting \\ SQL 解析
            ├── session \\ SQL会话
            ├── transaction \\ 事务模块
            └── type \\ 类型处理器

```

## 参考资料
---
- https://github.com/mybatis/mybatis-3
- https://mybatis.org/mybatis-3/