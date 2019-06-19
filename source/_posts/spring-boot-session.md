---
title: Spring Boot 集成Spring Session 实现全局Session
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-06-18 11:11:00
updated: 2019-06-18 11:11:00
#thumbnail: http://images.huotaihe.com/icon-spring-boot.png
---


## Spring Session 简介
Spring Session 为管理用户Session 信息提供了API和实现
> Spring Session provides an API and implementations for managing a user’s session information.

  ### 特点
  Spring Session 使应用支持集群回话变得更容易，而且不需要依赖于特定应用容器的解决方案。还提供如下透明集成：
<!-- more -->  
  - HttpSession - 可以独立于应用容器(i.e. tomcat)的方式 替换HttpSession，使用RESTful API的时候，支持在Headers中携带回话ID
  - WebSocket - 提供在接收WebSocket消息时保持HttpSession活动的能力
  - WebSession 以独立于应用容器的方式替换 Spring WebFlux 的 web Session


 ### 模块

  Spring Session 有一下几个模块组成：
  - Spring Session Core - 提供Spring Session的核心API和能力
  - Spring Session Data Redis - 提供基于redis 的SessionRepository 和 ReactiveSessionRepository的实现，还有配置支持
  - Spring Session JDBC 提供基于数据库 的SessionRepository 和 ReactiveSessionRepository 的实现，还有配置支持
  - Spring Session Hazelcast  提供基于Hazelcast的SessionRepository 和 ReactiveSessionRepository 的实现，还有配置支持 

## Spring Boot 使用redis集中存储Spring Session 实战
### 环境信息
- Spring Boot - 2.1.3.RELEASE
- Redis - 4.0.11

### 引入pom依赖
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.session</groupId>
  <artifactId>spring-session-data-redis</artifactId>
</dependency>
```

### 配置redis 数据源
```yml
spring:
  redis:
    database: 0
    password: qweqwe123
    host: 127.0.0.1
    port: 6379
```

### 创建测试Controller
```java
package com.github.huotaihe.springboot.learn.session;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpSession;

@RestController
@RequestMapping("/session")
public class SessionTestController {

    @GetMapping("/setAtt")
    public String test(HttpSession session){
        session.setAttribute("age", "1");

        return "1";
    }

    @GetMapping("/getAtt")
    public String test2(HttpSession session) {
        String age = (String) session.getAttribute("age");

        return age;
    }
}
```

### 启动Srping Boot 并请求 http://127.0.0.1:8080/session/setAtt
![](http://images.huotaihe.com/2019-06-19-11-33-20.png)

### 查看redis内容
会发现redis 中已经存入了Session信息
```bash
127.0.0.1:6379> keys *
 1) "spring:session:sessions:10d169c5-cbad-4308-81c5-ef87b3e5c10b"
 2) "spring:session:sessions:expires:10d169c5-cbad-4308-81c5-ef87b3e5c10b"
 3) "spring:session:expirations:1560916620000"
```

### 重启Spring Boot 并请求 http://127.0.0.1:8080/session/getAtt
尝试获取上一步骤存入Seseion的属性，并且可以成功获取
![](http://images.huotaihe.com/2019-06-19-11-38-38.png)

至此整个实战过程已经完成了
---

### Spring Session 常用配置项说明
```properties
# SPRING SESSION (SessionProperties)
## spring session 存储类型，如果只有一个Spring Session module 出现在classpath， Spring Boot 会自动使用存储实现，不需要制定该配置项；但是如果有多个实现存在，你必须通过该配置项选在使用哪个StoreType。
spring.session.store-type= redis
## Session 过期时间。如果时间后缀没有制定，默认单位是秒；如果没有配置，默认使用server.servlet.session.timeout的自动配置的值
spring.session.timeout= 10000
## Session repository 的过滤器执行顺序
spring.session.servlet.filter-order=-2147483598 
## Session repository filter 分发类型
spring.session.servlet.filter-dispatcher-types=async,error,request 

# SPRING SESSION REDIS (RedisSessionProperties)
## session 过期表达式
spring.session.redis.cleanup-cron=0 * * * * *
# Sessions flush mode.
spring.session.redis.flush-mode=on-save
## session 在redis 中保存的命名空间 
spring.session.redis.namespace=spring:session
```
---
工程源码：
- github： https://github.com/huotaihe/spring-boot-learn/tree/master/spring-redis-session

参考资料：
- https://docs.spring.io/spring-boot/docs/2.1.5.RELEASE/reference/htmlsingle/#boot-features-session
- https://docs.spring.io/spring-session/docs/2.1.5.RELEASE/reference/html5/guides/boot-redis.html
- https://spring.io/projects/spring-session