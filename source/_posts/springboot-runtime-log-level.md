---
title: springboot 运行时修改日志打印级别
date: 2018-12-17 14:51:39
categories: [Spring, 深入spring boot]
toc: true
tags: 
    - Spring 
    - Springboot 
    - log
---
## 序言

 Spring Boot 在 spring-boot-starter-actuator 模块中提供了日志相关的 EndPoint，通过该 EndPoint 可以在运行时不需要重启服务就可以修改日志的打印级别。
 
 解决了以前修改日志打印级别必须要重启服务的烦恼。

 <!-- more -->

## 环境信息
- spring boot 2.0.4.RELEASE(1.5以前的版本不适用该博文)
- JDK 1.8

## POM 信息

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-devtools</artifactId>
	<scope>runtime</scope>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

## 启用loggers Endpoint
出于安全因素考虑，依赖spring-boot-starter-actuator后，默认值启用了“/health”和“/info”两个 Endpoint
可以通过如下配置启用所有的 endpoint

```properties
management.endpoints.web.exposure.include=*
```
也可以通过该配置启用指定的 endpoint
```properties
management.endpoints.web.exposure.include=loggers,sessions
```
## 查看日志级别
我们可以通过浏览器请求到 http://127.0.0.1:8080/actuator/loggers 来获取支持的日志等级，以及系统默认的日志等和各个包路径对应的日志级别。
ps: 如下结果是精简过的，实际返回值很多
```json
{
    "levels":[
        "OFF",
        "ERROR",
        "WARN",
        "INFO",
        "DEBUG",
        "TRACE"
    ],
    "loggers":{
        "ROOT":{
            "configuredLevel":"INFO",
            "effectiveLevel":"INFO"
        },
        "com":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github.huotaihe":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github.huotaihe.spring":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github.huotaihe.spring.boot":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github.huotaihe.spring.boot.log":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        },
        "com.github.huotaihe.spring.boot.log.Application":{
            "configuredLevel":null,
            "effectiveLevel":"INFO"
        }
    }
}
```

## 配置日志级别
### 编写日志输出类
编写一个controller 输出各个级别的日志：

> 注意：日志门面类要使用 commons-log，而不能使用 sl4j；
> 使用 sl4j 会导致日志级别不生效，具体原因会继续跟进。

```java
package com.github.huotaihe.spring.boot.log;

import java.util.HashMap;
import java.util.Map;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class LogController {

  private final static Logger LOG = LoggerFactory.getLogger(LogController.class);

  @GetMapping("/log")
  public Map<String, Object> home() {
    if(LOG.isTraceEnabled()){
      LOG.trace("trace level log");
    }

    if(LOG.isDebugEnabled()){
      LOG.debug("debug level log");
    }

    if(LOG.isInfoEnabled()){
      LOG.info("info level log");
    }

    if(LOG.isWarnEnabled()){
      LOG.warn("warn level log");
    }

    if(LOG.isErrorEnabled()){
      LOG.error("error level log");
    }
    Map<String, Object> result = new HashMap<>();
    result.put("status", "good");
    result.put("name", "abc");
    result.put("password", "abc");
    return result;
  }
}

```
### 测试日志级别
启动应用并访问http://127.0.0.1:8080/log 
控制台打印如图，Spring Boot 默认的 ROOT 日志级别是INFO。
![](http://images.huotaihe.com/2019-01-17-11-13-04.png)

### 修改指定包的日志级别
通过/actuator/loggers端点提供的 POST 请求，修改包路径com.github.huotaihe.spring.boot.log 的日志级别为DEBUG。

- 发送 post 请求到
 http://127.0.0.1:8080/actuator/loggers/com.github.huotaihe.spring.boot.log
![](http://images.huotaihe.com/2019-01-17-11-16-15.png)

> header 信息必须添加
> Content-Type:application/json

- 浏览器通过 GET 方式访问如下地址
http://127.0.0.1:8080/actuator/loggers/com.github.huotaihe.spring.boot.log 
查看当前包的目前日志级别
```json
{
"configuredLevel": "DEBUG",
"effectiveLevel": "DEBUG"
}
```
- 再次访问http://127.0.0.1:8080/log 得到

![](http://images.huotaihe.com/2019-01-17-11-17-40.png)

## 源代码
[github](https://github.com/huotaihe/spring-boot-learn/tree/master/runtime-log-level)