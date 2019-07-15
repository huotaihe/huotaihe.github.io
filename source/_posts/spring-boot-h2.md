---
title: Spring Boot 快速集成H2 内存数据库
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-07-09 17:47:01
updated: 2019-07-09 17:47:01
---

Spring Boot 为嵌入式Java数据库引擎提供了自动化配置支持，目前提供了对HSQL，H2和Derby的支持，当然我们也通过可扩展API来支持新的嵌入式数据库类型和DataSource实现。
## 为什么使用嵌入式内存数据库
嵌入式数据库在项目的开发阶段非常有用，因为它具有轻量级特性。 优点包括易于配置，快速启动时间，可测试性以及在开发过程中快速开发SQL的能力。但是它不提供数据持久化，我们需要在应用启动的时候通过SQL来初始化数据库，在应用程序结束的时候运行时生成的数据会被丢弃

<!-- more -->

## 关于DB2

H2是个基于JAVA编写的轻量级关系型数据库。具有以下特点：
- 快速、开源、基于JDBC API
- 支持嵌入和独立服务器两种模式；内存型数据库
- 提供了网页管理控制台
- 体量小，jar包值有2MB大小

## 使用Spring Boot 集成H2
Spring Boot 为H2提供了自动化配置，集成起来非常方便。我们不需要提供任何的连接URL信息，我们只需要简单的在环境变量里面(或POM文件)引入H2的jar包即可。

### pom 依赖
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!-- devtools 和 web 非必要 演示console的时候用到 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

### 打印数据源信息
```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.sql.Connection;

@Component
@Slf4j
public class DataSourceInfo implements CommandLineRunner {

    @Autowired
    private DataSource dataSource;

    @Override
    public void run(String... args) throws Exception {
        Connection connection = dataSource.getConnection();
        log.info("Connection info: " + connection.toString());
    }
}
```
### 初始化sql
src/main/resources/schema.sql
```sql
create table `t`(`id` int(11) not null AUTO_INCREMENT, `c` int (11) default null, `d` int(11) default null, primary key (`id`));
```
src/main/resources/data.sql
```sql
INSERT INTO `t` (`c`, `d`) VALUES (1, 1);
```
### 数据源信息
从日志中看到到Spring Boot 为H2自动配置的数据源信息为：
- url: jdbc:h2:mem:testdb 
- user: SA
- password: <空>

### 登录控H2制台
用浏览器打开网址：http://127.0.0.1:8080/h2-console
输入数据源信息并登录
![](http://images.huotaihe.com/2019-07-15-17-15-50.png)
可以看到初始化过得数据源信息：
![](http://images.huotaihe.com/2019-07-15-17-17-48.png)

## 关于H2 Web Console 说明
H2 提供了基于浏览器的web控制台，而且Spring Boot 对其进行了自动化配置。当满足一下条件时web控制台才会自动化配置：
- 你的工程师一个基于Servlet 的web应用
- com.h2database:h2 在环境变量里
- 你正在使用Spring Boot 的devtools 开发者工具

> 如果你没有使用Spring Boot的开发者工具的时候，仍然想要使用Web console， 我们可以通过修改配置项spring.h2.console.enabled为true来启用它

> H2 Web 控制台只适合在开发阶段使用，生产环境应通过设置spring.h2.console.enabled配置项为false来禁用该功能

### 修改Web 控制台的URI
H2 的Web 控制台的默认路径是：/h2-console，我们可以通过application.properties(或者application.yml) 的配置项spring.h2.console.path 来修改路径。

## 关于初始化DB 脚本的说明
Spring Boot 可以自动的创建表结构，并对表数据进行初始化。Spring Boot 从环境变量根路径下分别加载schema.sql 和 data.sql文件，来完成数据源的填充。其中schema.sql 中为dml脚本，data.sql 中为ddl脚本。

## 结语
我们将在下一篇文章通过源代码讲解Spring Boot 是怎么完成H2的自动化配置的
---
工程源码：
- github： https://github.com/huotaihe/spring-boot-learn/tree/master/spring-redis-h2

## 参考资料
- https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-sql.html#boot-features-sql-h2-console
- https://docs.spring.io/spring/docs/5.1.8.RELEASE/spring-framework-reference/data-access.html#jdbc-embedded-database-support
- https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/howto-database-initialization.html