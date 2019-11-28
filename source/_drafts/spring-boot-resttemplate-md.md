---
title: SpringBoot RestTemplate的使用 
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-09-27 14:32:07
updated: 2019-09-27 14:32:07
---
## 什么是RestTemplate

RestTemplate 带有同步模板方法API的原始Spring REST客户端。

> RestTemplate 在http client 工具包的基础上提供了统一的接口模板。它使开发者可以非常容易的在一行代码中完成rest端点的调用。

> Spring 从5.0开始增加了WebClient，它是一个无阻塞反应式的，并且有效支持同步，以及流方案。
WebClient是RestTemplate的现代替代方案，RestTemplate在将来的版本中将会被弃用，并且以后也不会再添加新功能。

## 获取RestTemplate 实例

```java
因为RestTemplate 实例在使用前往往需要一些自定义配置，所以


/**
 * RestTemplateDemo
 *
 * @author: huotaihe
 * @Date: 15:05 2019-09-27
 **/
@Service
public class RestTemplateDemo {

    @Autowired
    private RestTemplateBuilder restTemplateBuilder;

    /**
     * 通过RestTemplateBuilder 构建
     */
    public void byBuilder(){
        RestTemplate restTemplate = restTemplateBuilder.build();
    }

    /**
     * 通过 new 创建默认的RestTemplate
     */
    public void bynew(){
        RestTemplate restTemplate = new RestTemplate();
    }

}
```