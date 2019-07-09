---
title: Spring Boot 搭建Oauth2 认证系统
toc: true
categories:
  - temp
tags:
  - temp
date: 2019-06-21 14:44:51
updated: 2019-06-21 14:44:51
---


##  spring-security-oauth 简介
OAuth2是一种广泛使用的授权框架。

Spring Security OAuth 提供了OAuth的实现，为OAuth 的提供者和消费者提供了支持，并且支持OAuth1和OAth2.0。

在学习spring security oauth 之前，需要掌握并理解OAuth，并且理解它解决了哪些应用场景，不了的话可以阅读它的[官方文档](https://oauth.net/)；同时开发者也需要书序Spring和Spring Security 的工作原理。
<!-- more -->
## 环境信息
- Spring Boot - 2.1.3.RELEASE
- JDK - 8
- maven - 3.5.2

## 1. 搭建授权服务
目前，Spring Security 还没有为OAuth2.0认证服务器提供实现。但是Spring Security OAuth项目 为该功能提供了支持，Spring Security OAuth 将被Spring Security 完全取代。在此之前，我们可以用spring-security-oauth2-autoconfigure 模块很容易的配置出OAuth2.0 授权服务器。

详细说明可以参考它的说明文档: https://docs.spring.io/spring-security-oauth2-boot/docs/2.1.6.RELEASE/reference/htmlsingle/

### 利用spring-security-oauth2-boot 自动化配置搭建
#### 添加pom依赖
```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--启用 oauth2 需要引入security 和 oauth2-autoconfigure  -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.security.oauth.boot</groupId>
            <artifactId>spring-security-oauth2-autoconfigure</artifactId>
            <version>2.1.6.RELEASE</version>
        </dependency>
    </dependencies>
```

#### 在入口主类启用@EnableAuthorizationServer注解
```java
@SpringBootApplication
@EnableAuthorizationServer
public class SpringBootOauthServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootOauthServerApplication.class, args);
    }

}
```
#### 添加相关配置
```properties
spring.security.user.name=server
spring.security.user.password=qweqwe123
security.oauth2.client.client-id=oauth2-server
security.oauth2.client.client-secret=qweqwe123
## 官方说明文档并没有要求添加该项，但是 不加scope配置获取token会报错，
security.oauth2.client.scope=1
```

#### 启动配置oauth2 server 的springboot 工程
#### 测试授权服务器
```bash
## 发起获取token请求
curl oauth2-server:qweqwe123@localhost:8080/oauth/token -d grant_type=password -d 
username=server -d password=qweqwe123
```
    > /token endpoint 的认证凭据是配置文件中的client-id和client-secret
    > 用户的认证凭据是正常的spring security 用户
```bash
## 控制台打印如下信息说明授权服务器配置成功
{"access_token":"aed587ec-a3cb-4953-89c8-5399d41dd8cd","token_type":"bearer","refresh_token":"5267069b-fc9a-490e-befb-ed11564e05e1","expires_in":43199,"scope":"1"}%
```


## 2. 搭建资源服务
要使用Access Token 我们需要搭建一个资源服务器(Resource Server), 资源服务器可以是一个独立的服务，也可以和认证服务器搭建在同一个服务。
创建资源服务器非常简单，只需要在主类上添加@EnableResourceServer, 并且提供一些配置允许服务器解码access token。

如果你的资源服务器同时也是认证服务器，应用一直知道如何解码access token 了，这是什么都不用配置。如果你的应用是一个独立的资源服务，你需要为应用提供如下几个配置项：

- security.oauth2.resource.user-info-uri 使用 Pivotal Web Services   的/me 资源
- security.oauth2.resource.token-info-uri

如果你同时设置了user-info-uri和token-info-uri，你可以配置一个flag说明哪个配置优先(prefer-token-info=true 为默认配置)。

另外，如果你的tokens 是JWTS 可以替代user-info-uri 或者token-info-uri。我们可以配置一个<kbd>security.oauth2.resource.jwt.key-value</kbd> 用来对token进行本地解码(security.oauth2.resource.jwt.key-value 是验证秘钥)。校验秘钥可以是堆成秘钥也可以是PEM-encoded RSA 公钥。如果你没有key且它是公共的，
您可以使用security.oauth2.resource.jwt.key-uri提供可以下载的URI（作为具有“value”字段的JSON对象）。 例如。 在PWS上：
```bash
 curl https://uaa.run.pivotal.io/token_key
 ## 返回值
{"kty":"RSA","e":"AQAB","use":"sig","kid":"sha2-2017-01-20-key","alg":"RS256","value":"-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyH6kYCP29faDAUPKtei3\nV/Zh8eCHyHRDHrD0iosvgHuaakK1AFHjD19ojuPiTQm8r8nEeQtHb6mDi1LvZ03e\nEWxpvWwFfFVtCyBqWr5wn6IkY+ZFXfERLn2NCn6sMVxcFV12sUtuqD+jrW8MnTG7\nhofQqxmVVKKsZiXCvUSzfiKxDgoiRuD3MJSoZ0nQTHVmYxlFHuhTEETuTqSPmOXd\n/xJBVRi5WYCjt1aKRRZEz04zVEBVhVkr2H84qcVJHcfXFu4JM6dg0nmTjgd5cZUN\ncwA1KhK2/Qru9N0xlk9FGD2cvrVCCPWFPvZ1W7U7PBWOSBBH6GergA+dk2vQr7Ho\nlQIDAQAB\n-----END PUBLIC KEY-----","n":"AMh-pGAj9vX2gwFDyrXot1f2YfHgh8h0Qx6w9IqLL4B7mmpCtQBR4w9faI7j4k0JvK_JxHkLR2-pg4tS72dN3hFsab1sBXxVbQsgalq-cJ-iJGPmRV3xES59jQp-rDFcXBVddrFLbqg_o61vDJ0xu4aH0KsZlVSirGYlwr1Es34isQ4KIkbg9zCUqGdJ0Ex1ZmMZRR7oUxBE7k6kj5jl3f8SQVUYuVmAo7dWikUWRM9OM1RAVYVZK9h_OKnFSR3H1xbuCTOnYNJ5k44HeXGVDXMANSoStv0K7vTdMZZPRRg9nL61Qgj1hT72dVu1OzwVjkgQR-hnq4APnZNr0K-x6JU"}%
```

此外，如果您的授权服务器具有返回一组JSON Web密钥（JWK）的端点，则可以配置security.oauth2.resource.jwk.key-set-uri
```bash
curl https://uaa.run.pivotal.io/token_keys
## 返回值
{"keys":[{"kty":"RSA","e":"AQAB","use":"sig","kid":"sha2-2017-01-20-key","alg":"RS256","value":"-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyH6kYCP29faDAUPKtei3\nV/Zh8eCHyHRDHrD0iosvgHuaakK1AFHjD19ojuPiTQm8r8nEeQtHb6mDi1LvZ03e\nEWxpvWwFfFVtCyBqWr5wn6IkY+ZFXfERLn2NCn6sMVxcFV12sUtuqD+jrW8MnTG7\nhofQqxmVVKKsZiXCvUSzfiKxDgoiRuD3MJSoZ0nQTHVmYxlFHuhTEETuTqSPmOXd\n/xJBVRi5WYCjt1aKRRZEz04zVEBVhVkr2H84qcVJHcfXFu4JM6dg0nmTjgd5cZUN\ncwA1KhK2/Qru9N0xlk9FGD2cvrVCCPWFPvZ1W7U7PBWOSBBH6GergA+dk2vQr7Ho\nlQIDAQAB\n-----END PUBLIC KEY-----","n":"AMh-pGAj9vX2gwFDyrXot1f2YfHgh8h0Qx6w9IqLL4B7mmpCtQBR4w9faI7j4k0JvK_JxHkLR2-pg4tS72dN3hFsab1sBXxVbQsgalq-cJ-iJGPmRV3xES59jQp-rDFcXBVddrFLbqg_o61vDJ0xu4aH0KsZlVSirGYlwr1Es34isQ4KIkbg9zCUqGdJ0Ex1ZmMZRR7oUxBE7k6kj5jl3f8SQVUYuVmAo7dWikUWRM9OM1RAVYVZK9h_OKnFSR3H1xbuCTOnYNJ5k44HeXGVDXMANSoStv0K7vTdMZZPRRg9nL61Qgj1hT72dVu1OzwVjkgQR-hnq4APnZNr0K-x6JU"}]}
```

> 同时配置JWT和JWK 属性会引起报错。security.oauth2.resource.jwt.key-uri (或 security.oauth2.resource.jwt.key-value) 和 security.oauth2.resource.jwk.key-set-uri 应该只配置一个。

> 如果你是用security.oauth2.resource.jwt.key-uri 和security.oauth2.resource.jwk.key-set-uri，应用程序启动时，认证服务器需要保持运行。
如果找不到密钥，它会记录警告，并告诉您如何解决问题。

OAuth2 资源服务器被security.oauth2.resource.filter-order 过滤器链保护，默认情况下在actuator endpoints 的保护过滤器之后。（因此，除非您更改顺序，否则执行器端点将保留在HTTP Basic上）

## 3. 搭建客户端服务 

要让你的额web-app变为变为OAuth2 客户端，只需要简单的添加@EnableOAuth2Client注解，Spring Boot 将会创建一个OAuth2ClientContext和OAuth2ProtectedResourceDetails，这些都是创建OAuth2RestOperations所必需的。Spring Boot 没有自动创建这样的bean，但是你可以轻松的创建。
```java
@Bean
public OAuth2RestTemplate oauth2RestTemplate(OAuth2ClientContext oauth2ClientContext,
        OAuth2ProtectedResourceDetails details) {
    return new OAuth2RestTemplate(details, oauth2ClientContext);
}
```

> 您可能希望添加限定符并检查配置，因为可能在应用程序中定义了多个RestTemplate。

此配置中使用security.oauth2.client.*作为凭据(与在授权服务器中的配置相同)，此外还需要授权服务器的授权和令牌URI。如下面配置项：
```yml
security:
  oauth2:
    client:
      clientId: bd1c0a783ccdd1c9b9e4
      clientSecret: 1a9030fbca47a5b2c28e92f19050bb77824b5ad1
      accessTokenUri: https://github.com/login/oauth/access_token
      userAuthorizationUri: https://github.com/login/oauth/authorize
      clientAuthenticationScheme: form
```
当你尝试使用Oauth2RestTemplate的时候,包含该配置的client将会重定向到Github进行认证。如果你已经登录到了Github，你甚至不会注意到他已经经过认证。只有你的应用运行在8080端口的时候，这些指定的凭证才会工作。(在Github或其他提供程序中注册您自己的客户端应用程序以获得更大的灵活性)

要限制客户端在获取访问令牌时要求的范围，可以设置security.oauth2.client.scope（逗号分隔或YAML中的数组）。 默认情况下，作用域为空，由Authorization Server决定默认值应该是什么，通常取决于它所拥有的客户端注册中的设置。

> security.oauth2.client.client-authentication-scheme还有一个设置，默认是“header”(但是如果像Github一样，你的OAuth2 提供者不喜欢header 认证，你需要将其设置为“form”)。实际上，security.oauth2.client.* 属性绑定到了AuthorizationCodeResourceDetails实例，因此他的所有属性都可以指定。

> 在非web应用中你仍然可以创建一个OAuth2RestOperations，它仍然连接到security.oauth2.client.* 配置。在这种情况下，如果你使用它，它将是一个客户端凭证令牌授权(并且不再需要@EnableOAuth2Client和@EnableOAuth2SSo注解)。要组织该结构被定义，只需要从配置文件中删除security.oauth2.client.client-id 配置项(或者将值设为空字符串)

## 单点登录
OAuth2 客户端可以用于从提供程序获取用户详细信息(如果此类功能可用)，然后将其转换成Spring Security的身份令牌验证。上面的资源服务器通过user-info-uri 属性支持者一点。这是基于OAuth2的单点登录的协议挤出，Spring Boot 通过提供@EnableOauth2Sso 注解使其易于参与。上面的Github客户端可以保护其所有资源，并使用Github /user/ endpoint进行验证，方法是添加注解并声明在哪里找到端点(除了上面已经列出的security.oauth2.client.* 配置)

application.yml
```yml
security:
  oauth2:
# ...
  resource:
    userInfoUri: https://api.github.com/user
    preferTokenInfo: false
```

由于默认所有paths都是安全的，因此没有注解可以显示给未经身份验证的用户并邀请他们登录(通过访问/login路径或security.oauth2.sso.login-path指定的路径)。

自定义要保护的访问规则和路径，一遍可以添加home页面，可以将@EnableOAuth2Sso 添加到WebSecurityConfigureAdapter中，注释将使其被装饰和增强，并获得必要的部分以获取工作路径。例如，我们仅允许在“/”处对主页进行未经身份认证的访问，并保留其他所有内容的默认值。

```java
@Configuration
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .mvcMatchers("/").permitAll()
                .anyRequest().authenticated();
    }
}
```

另外请注意，由于默认请你赶快下所有endpoints都是安全的，因此这包括任何默认错误处理端点，例如端点"/error"。这意味着如果单点登录期间存在某些问题，需要重新定向到"/error"页面，这会导致身份提供程序和应用接收程序之间的无限重定向。

首先，仔细考虑使端点安全，因为您可能会发现行为只是不同问题的证据。 但是，可以通过将应用程序配置为允许“/ error”来解决此问题：

```java
@Configuration
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/error").permitAll()
                .anyRequest().authenticated();
    }
}
```

---
## 参考资料
- https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/htmlsingle/
- https://projects.spring.io/spring-security-oauth/docs/oauth2.html
- https://docs.spring.io/spring-security-oauth2-boot/docs/2.0.0.RELEASE/reference/htmlsingle/#boot-features-security-oauth2-authorization-server




