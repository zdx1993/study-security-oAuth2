# 简介

## [#](https://www.funtl.com/zh/spring-security-oauth2/#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-简介](https://www.bilibili.com/video/av48590637/?p=1)

## [#](https://www.funtl.com/zh/spring-security-oauth2/#%E6%A6%82%E8%BF%B0)概述

本章节的目的是帮助大家快速上手使用 Spring 提供的 Spring Security oAuth2 搭建一套验证授权及资源访问服务，帮助大家在实现企业微服务架构时能够有效的控制多个服务的统一登录、授权及资源保护工作

[**GitHub**](https://github.com/topsale/spring-boot-samples/tree/master/spring-security-oauth2)

## [#](https://www.funtl.com/zh/spring-security-oauth2/#%E4%BB%80%E4%B9%88%E6%98%AF-oauth)什么是 oAuth

oAuth 协议为用户资源的授权提供了一个安全的、开放而又简易的标准。与以往的授权方式不同之处是 oAuth 的授权不会使第三方触及到用户的帐号信息（如用户名与密码），即第三方无需使用用户的用户名与密码就可以申请获得该用户资源的授权，因此 oAuth 是安全的。

## [#](https://www.funtl.com/zh/spring-security-oauth2/#%E4%BB%80%E4%B9%88%E6%98%AF-spring-security)什么是 Spring Security

Spring Security 是一个安全框架，前身是 Acegi Security，能够为 Spring 企业应用系统提供声明式的安全访问控制。Spring Security 基于 Servlet 过滤器、IoC 和 AOP，为 Web 请求和方法调用提供身份确认和授权处理，避免了代码耦合，减少了大量重复代码工作。

# 为什么需要 oAuth2

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-oAuth2.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-为什么需要 oAuth2](https://www.bilibili.com/video/av48590637/?p=2)
- [Spring Security oAuth2-交互过程](https://www.bilibili.com/video/av48590637/?p=3)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-oAuth2.html#%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)应用场景

我们假设你有一个“云笔记”产品，并提供了“云笔记服务”和“云相册服务”，此时用户需要在不同的设备（PC、Android、iPhone、TV、Watch）上去访问这些“资源”（笔记，图片）

那么用户如何才能访问属于自己的那部分资源呢？此时传统的做法就是提供自己的账号和密码给我们的“云笔记”，登录成功后就可以获取资源了。但这样的做法会有以下几个问题：

- “云笔记服务”和“云相册服务”会分别部署，难道我们要分别登录吗？
- 如果有第三方应用程序想要接入我们的“云笔记”，难道需要用户提供账号和密码给第三方应用程序，让他记录后再访问我们的资源吗？
- 用户如何限制第三方应用程序在我们“云笔记”的授权范围和使用期限？难道把所有资料都永久暴露给它吗？
- 如果用户修改了密码收回了权限，那么所有第三方应用程序会全部失效。
- 只要有一个接入的第三方应用程序遭到破解，那么用户的密码就会泄露，后果不堪设想。

为了解决如上问题，oAuth 应用而生。

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-oAuth2.html#%E5%90%8D%E8%AF%8D%E8%A7%A3%E9%87%8A)名词解释

- **第三方应用程序（Third-party application）：** 又称之为客户端（client），比如上节中提到的设备（PC、Android、iPhone、TV、Watch），我们会在这些设备中安装我们自己研发的 APP。又比如我们的产品想要使用 QQ、微信等第三方登录。对我们的产品来说，QQ、微信登录是第三方登录系统。我们又需要第三方登录系统的资源（头像、昵称等）。对于 QQ、微信等系统我们又是第三方应用程序。
- **HTTP 服务提供商（HTTP service）：** 我们的云笔记产品以及 QQ、微信等都可以称之为“服务提供商”。
- **资源所有者（Resource Owner）：** 又称之为用户（user）。
- **用户代理（User Agent）：** 比如浏览器，代替用户去访问这些资源。
- **认证服务器（Authorization server）：** 即服务提供商专门用来处理认证的服务器，简单点说就是登录功能（验证用户的账号密码是否正确以及分配相应的权限）
- **资源服务器（Resource server）：** 即服务提供商存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。简单点说就是资源的访问入口，比如上节中提到的“云笔记服务”和“云相册服务”都可以称之为资源服务器。

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-oAuth2.html#%E4%BA%A4%E4%BA%92%E8%BF%87%E7%A8%8B)交互过程

oAuth 在 "客户端" 与 "服务提供商" 之间，设置了一个授权层（authorization layer）。"客户端" 不能直接登录 "服务提供商"，只能登录授权层，以此将用户与客户端区分开来。"客户端" 登录授权层所用的令牌（token），与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。"客户端" 登录授权层以后，"服务提供商" 根据令牌的权限范围和有效期，向 "客户端" 开放用户储存的资料。

![1562118023629](assets/1562118023629.png)

# 开放平台

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-开放平台](https://www.bilibili.com/video/av48590637/?p=4)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0.html#%E4%BA%A4%E4%BA%92%E6%A8%A1%E5%9E%8B)交互模型

交互模型涉及三方：

- 资源拥有者：用户
- 客户端：APP
- 服务提供方：包含两个角色
  - 认证服务器
  - 资源服务器

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0.html#%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8)认证服务器

认证服务器负责对用户进行认证，并授权给客户端权限。认证很容易实现（验证账号密码即可），问题在于如何授权。比如我们使用第三方登录 "有道云笔记"，你可以看到如使用 QQ 登录的授权页面上有 "有道云笔记将获得以下权限" 的字样以及权限信息

![1562118036796](assets/1562118036796.png)

认证服务器需要知道请求授权的客户端的身份以及该客户端请求的权限。我们可以为每一个客户端预先分配一个 id，并给每个 id 对应一个名称以及权限信息。这些信息可以写在认证服务器上的配置文件里。然后，客户端每次打开授权页面的时候，把属于自己的 id 传过来，如：

```text
http://www.funtl.com/login?client_id=yourClientId
```

1

随着时间的推移和业务的增长，会发现，修改配置的工作消耗了太多的人力。有没有办法把这个过程自动化起来，把人工从这些繁琐的操作中解放出来？当开始考虑这一步，开放平台的成型也就是水到渠成的事情了。

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0.html#oauth2-%E5%BC%80%E6%94%BE%E5%B9%B3%E5%8F%B0)oAuth2 开放平台

开放平台是由 oAuth2.0 协议衍生出来的一个产品。它的作用是让客户端自己去这上面进行注册、申请，通过之后系统自动分配 `client_id` ，并完成配置的自动更新（通常是写进数据库）。

客户端要完成申请，通常需要填写客户端程序的类型（Web、App 等）、企业介绍、执照、想要获取的权限等等信息。这些信息在得到服务提供方的人工审核通过后，开发平台就会自动分配一个 `client_id` 给客户端了。

到这里，已经实现了登录认证、授权页的信息展示。那么接下来，当用户成功进行授权之后，认证服务器需要把产生的 `access_token` 发送给客户端，方案如下：

- 让客户端在开放平台申请的时候，填写一个 URL，例如：http://www.funtl.com
- 每次当有用户授权成功之后，认证服务器将页面重定向到这个 URL（回调），并带上 `access_token`，例如：http://www.funtl.com?access_token=123456789
- 客户端接收到了这个 `access_token`，而且认证服务器的授权动作已经完成，刚好可以把程序的控制权转交回客户端，由客户端决定接下来向用户展示什么内容

# 令牌的访问与刷新

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%BB%A4%E7%89%8C%E7%9A%84%E8%AE%BF%E9%97%AE%E4%B8%8E%E5%88%B7%E6%96%B0.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-令牌的访问与刷新](https://www.bilibili.com/video/av48590637/?p=5)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%BB%A4%E7%89%8C%E7%9A%84%E8%AE%BF%E9%97%AE%E4%B8%8E%E5%88%B7%E6%96%B0.html#access-token)Access Token

Access Token 是客户端访问资源服务器的令牌。拥有这个令牌代表着得到用户的授权。然而，这个授权应该是 **临时** 的，有一定有效期。这是因为，Access Token 在使用的过程中 **可能会泄露**。给 Access Token 限定一个 **较短的有效期** 可以降低因 Access Token 泄露而带来的风险。

然而引入了有效期之后，客户端使用起来就不那么方便了。每当 Access Token 过期，客户端就必须重新向用户索要授权。这样用户可能每隔几天，甚至每天都需要进行授权操作。这是一件非常影响用户体验的事情。希望有一种方法，可以避免这种情况。

于是 oAuth2.0 引入了 Refresh Token 机制

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E4%BB%A4%E7%89%8C%E7%9A%84%E8%AE%BF%E9%97%AE%E4%B8%8E%E5%88%B7%E6%96%B0.html#refresh-token)Refresh Token

Refresh Token 的作用是用来刷新 Access Token。认证服务器提供一个刷新接口，例如：

```text
http://www.funtl.com/refresh?refresh_token=&client_id=
```

1

传入 `refresh_token` 和 `client_id`，认证服务器验证通过后，返回一个新的 Access Token。为了安全，oAuth2.0 引入了两个措施：

- oAuth2.0 要求，Refresh Token **一定是保存在客户端的服务器上** ，而绝不能存放在狭义的客户端（例如 App、PC 端软件）上。调用 `refresh` 接口的时候，一定是从服务器到服务器的访问。
- oAuth2.0 引入了 `client_secret` 机制。即每一个 `client_id` 都对应一个 `client_secret`。这个 `client_secret` 会在客户端申请 `client_id` 时，随 `client_id` 一起分配给客户端。**客户端必须把 client_secret 妥善保管在服务器上**，决不能泄露。刷新 Access Token 时，需要验证这个 `client_secret`。

实际上的刷新接口类似于：

```text
http://www.funtl.com/refresh?refresh_token=&client_id=&client_secret=
```

1

以上就是 Refresh Token 机制。Refresh Token 的有效期非常长，会在用户授权时，随 Access Token 一起重定向到回调 URL，传递给客户端。

# 客户端授权模式

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-简单模式与授权码模式](https://www.bilibili.com/video/av48590637/?p=6)
- [Spring Security oAuth2-密码模式与客户端模式](https://www.bilibili.com/video/av48590637/?p=7)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E6%A6%82%E8%BF%B0)概述

客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。oAuth 2.0 定义了四种授权方式。

- implicit：简化模式，不推荐使用
- authorization code：授权码模式
- resource owner password credentials：密码模式
- client credentials：客户端模式

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E7%AE%80%E5%8C%96%E6%A8%A1%E5%BC%8F)简化模式

简化模式适用于纯静态页面应用。所谓纯静态页面应用，也就是应用没有在服务器上执行代码的权限（通常是把代码托管在别人的服务器上），只有前端 JS 代码的控制权。

这种场景下，应用是没有持久化存储的能力的。因此，按照 oAuth2.0 的规定，这种应用是拿不到 Refresh Token 的。其整个授权流程如下：

![1562118049714](assets/1562118049714.png)

该模式下，`access_token` 容易泄露且不可刷新

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E6%8E%88%E6%9D%83%E7%A0%81%E6%A8%A1%E5%BC%8F)授权码模式

授权码模式适用于有自己的服务器的应用，它是一个一次性的临时凭证，用来换取 `access_token` 和 `refresh_token`。认证服务器提供了一个类似这样的接口：

```text
https://www.funtl.com/exchange?code=&client_id=&client_secret=
```

1

需要传入 `code`、`client_id` 以及 `client_secret`。验证通过后，返回 `access_token` 和 `refresh_token`。一旦换取成功，`code` 立即作废，不能再使用第二次。流程图如下：

![1562118064376](assets/1562118064376.png)

这个 code 的作用是保护 token 的安全性。上一节说到，简单模式下，token 是不安全的。这是因为在第 4 步当中直接把 token 返回给应用。而这一步容易被拦截、窃听。引入了 code 之后，即使攻击者能够窃取到 code，但是由于他无法获得应用保存在服务器的 `client_secret`，因此也无法通过 code 换取 token。而第 5 步，为什么不容易被拦截、窃听呢？这是因为，首先，这是一个从服务器到服务器的访问，黑客比较难捕捉到；其次，这个请求通常要求是 https 的实现。即使能窃听到数据包也无法解析出内容。

有了这个 code，token 的安全性大大提高。因此，oAuth2.0 鼓励使用这种方式进行授权，而简单模式则是在不得已情况下才会使用。

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E5%AF%86%E7%A0%81%E6%A8%A1%E5%BC%8F)密码模式

密码模式中，用户向客户端提供自己的用户名和密码。客户端使用这些信息，向 "服务商提供商" 索要授权。在这种模式中，用户必须把自己的密码给客户端，但是客户端不得储存密码。这通常用在用户对客户端高度信任的情况下，比如客户端是操作系统的一部分。

一个典型的例子是同一个企业内部的不同产品要使用本企业的 oAuth2.0 体系。在有些情况下，产品希望能够定制化授权页面。由于是同个企业，不需要向用户展示“xxx将获取以下权限”等字样并询问用户的授权意向，而只需进行用户的身份认证即可。这个时候，由具体的产品团队开发定制化的授权界面，接收用户输入账号密码，并直接传递给鉴权服务器进行授权即可。

![1562118073604](assets/1562118073604.png)

有一点需要特别注意的是，在第 2 步中，认证服务器需要对客户端的身份进行验证，确保是受信任的客户端。

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%8E%88%E6%9D%83%E6%A8%A1%E5%BC%8F.html#%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%A8%A1%E5%BC%8F)客户端模式

如果信任关系再进一步，或者调用者是一个后端的模块，没有用户界面的时候，可以使用客户端模式。鉴权服务器直接对客户端进行身份验证，验证通过后，返回 token。

![1562118087294](assets/1562118087294.png)

# 创建案例工程项目

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E6%A1%88%E4%BE%8B%E5%B7%A5%E7%A8%8B%E9%A1%B9%E7%9B%AE.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-概念总结](https://www.bilibili.com/video/av48590637/?p=8)
- [Spring Security oAuth2-基于内存存储令牌](https://www.bilibili.com/video/av48590637/?p=9)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E6%A1%88%E4%BE%8B%E5%B7%A5%E7%A8%8B%E9%A1%B9%E7%9B%AE.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/>
    </parent>

    <groupId>com.funtl</groupId>
    <artifactId>spring-boot-samples</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <modules>
        <!-- 工程模块请随着项目的不断完善自行添加 -->
    </modules>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.funtl</groupId>
                <artifactId>spring-boot-samples-dependencies</artifactId>
                <version>${project.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <profiles>
        <profile>
            <id>default</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <spring-javaformat.version>0.0.7</spring-javaformat.version>
            </properties>
            <build>
                <plugins>
                    <plugin>
                        <groupId>io.spring.javaformat</groupId>
                        <artifactId>spring-javaformat-maven-plugin</artifactId>
                        <version>${spring-javaformat.version}</version>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-surefire-plugin</artifactId>
                        <configuration>
                            <includes>
                                <include>**/*Tests.java</include>
                            </includes>
                            <excludes>
                                <exclude>**/Abstract*.java</exclude>
                            </excludes>
                            <systemPropertyVariables>
                                <java.security.egd>file:/dev/./urandom</java.security.egd>
                                <java.awt.headless>true</java.awt.headless>
                            </systemPropertyVariables>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-enforcer-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>enforce-rules</id>
                                <goals>
                                    <goal>enforce</goal>
                                </goals>
                                <configuration>
                                    <rules>
                                        <bannedDependencies>
                                            <excludes>
                                                <exclude>commons-logging:*:*</exclude>
                                            </excludes>
                                            <searchTransitive>true</searchTransitive>
                                        </bannedDependencies>
                                    </rules>
                                    <fail>true</fail>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-install-plugin</artifactId>
                        <configuration>
                            <skip>true</skip>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <configuration>
                            <skip>true</skip>
                        </configuration>
                        <inherited>true</inherited>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>

    <repositories>
        <repository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
        <pluginRepository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
</project>
```

  创建统一的依赖管理模块

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E7%BB%9F%E4%B8%80%E7%9A%84%E4%BE%9D%E8%B5%96%E7%AE%A1%E7%90%86%E6%A8%A1%E5%9D%97.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.funtl</groupId>
    <artifactId>spring-boot-samples-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <properties>
        <spring-cloud.version>Greenwich.RELEASE</spring-cloud.version>
    </properties>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <repositories>
        <repository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
        <pluginRepository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>

</project>
```

# 创建 oAuth2 案例模块

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA-oAuth2-%E6%A1%88%E4%BE%8B%E6%A8%A1%E5%9D%97.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-boot-samples</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-security-oauth2</artifactId>
    <packaging>pom</packaging>
    <url>http://www.funtl.com</url>

    <modules>
        <!-- 工程模块请随着项目的不断完善自行添加 -->
    </modules>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

</project>
```

# 创建认证服务器模块

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-security-oauth2</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-security-oauth2-server</artifactId>
    <url>http://www.funtl.com</url>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!-- Spring Security -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-oauth2</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.oauth2.OAuth2ServerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#application)Application

```java
package com.funtl.oauth2;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 认证服务器，用于获取 Token
 * <p>
 * Description:
 * </p>
 *
 * @author Lusifer
 * @version v1.0.0
 * @date 2019-04-01 16:06:45
 * @see com.funtl.oauth2
 */
@SpringBootApplication
public class OAuth2ServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(OAuth2ServerApplication.class, args);
    }

}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24

# 基于内存存储令牌

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E6%A6%82%E8%BF%B0)概述

本章节基于 **内存存储令牌** 的模式用于演示最基本的操作，帮助大家快速理解 oAuth2 认证服务器中 "认证"、"授权"、"访问令牌” 的基本概念

**操作流程**

![img](https://www.funtl.com/assets1/Lusifer_201904030001.png)

- 配置认证服务器

  - 配置客户端信息：

    ```
    ClientDetailsServiceConfigurer
    ```

    - `inMemory`：内存配置
    - `withClient`：客户端标识
    - `secret`：客户端安全码
    - `authorizedGrantTypes`：客户端授权类型
    - `scopes`：客户端授权范围
    - `redirectUris`：注册回调地址

- 配置 Web 安全

- 通过 GET 请求访问认证服务器获取授权码

  - 端点：`/oauth/authorize`

- 通过 POST 请求利用授权码访问认证服务器获取令牌

  - 端点：`/oauth/token`

**附：默认的端点 URL**

- `/oauth/authorize`：授权端点
- `/oauth/token`：令牌端点
- `/oauth/confirm_access`：用户确认授权提交端点
- `/oauth/error`：授权服务错误信息端点
- `/oauth/check_token`：用于资源服务访问的令牌解析端点
- `/oauth/token_key`：提供公有密匙的端点，如果你使用 JWT 令牌的话

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E9%85%8D%E7%BD%AE%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8)配置认证服务器

创建一个类继承 `AuthorizationServerConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableAuthorizationServer`

```java
package com.funtl.oauth2.server.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 配置客户端
        clients
                // 使用内存设置
                .inMemory()
                // client_id
                .withClient("client")
                // client_secret
                .secret("secret")
                // 授权类型
                .authorizedGrantTypes("authorization_code")
                // 授权范围
                .scopes("app")
                // 注册回调地址
                .redirectUris("http://www.funtl.com");
    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)服务器安全配置

创建一个类继承 `WebSecurityConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableWebSecurity`
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

```java
package com.funtl.oauth2.server.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

}
```

1
2
3
4
5
6
7
8
9
10
11
12
13

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#application-yml)application.yml

```yaml
spring:
  application:
    name: oauth2-server
  security:
    user:
      # 账号
      name: root
      # 密码
      password: 123456

server:
  port: 8080
```

1
2
3
4
5
6
7
8
9
10
11
12

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E8%AE%BF%E9%97%AE%E8%8E%B7%E5%8F%96%E6%8E%88%E6%9D%83%E7%A0%81)访问获取授权码

打开浏览器，输入地址：

```text
http://localhost:8080/oauth/authorize?client_id=client&response_type=code
```

1

第一次访问会跳转到登录页面

![img](https://www.funtl.com/assets1/Lusifer_20190401195014.png)

验证成功后会询问用户是否授权客户端

![img](https://www.funtl.com/assets1/Lusifer_20190401195129.png)

选择授权后会跳转到我的博客，浏览器地址上还会包含一个授权码（`code=1JuO6V`），浏览器地址栏会显示如下地址：

```text
http://www.funtl.com/?code=1JuO6V
```

1

有了这个授权码就可以获取访问令牌了

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E9%80%9A%E8%BF%87%E6%8E%88%E6%9D%83%E7%A0%81%E5%90%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%94%B3%E8%AF%B7%E4%BB%A4%E7%89%8C)通过授权码向服务器申请令牌

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```

1

![img](https://www.funtl.com/assets1/Lusifer_20190402232952.png)

**注意：此时无法请求到令牌，访问服务器会报错 There is no PasswordEncoder mapped for the id “null”解决方案请移步 这里**

# 基于 JDBC 存储令牌

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-基于 JDBC 存储令牌](https://www.bilibili.com/video/av48590637/?p=10)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E6%A6%82%E8%BF%B0)概述

本章节 **基于 JDBC 存储令牌** 的模式用于演示最基本的操作，帮助大家快速理解 oAuth2 认证服务器中 "认证"、"授权"、"访问令牌” 的基本概念

**操作流程**

![img](https://www.funtl.com/assets1/Lusifer_201904030001.png)

- 初始化 oAuth2 相关表

- 在数据库中配置客户端

- 配置认证服务器

  - 配置数据源：`DataSource`

  - 配置令牌存储方式：`TokenStore` -> `JdbcTokenStore`

  - 配置客户端读取方式：`ClientDetailsService` -> `JdbcClientDetailsService`

  - 配置服务端点信息：

    ```
    AuthorizationServerEndpointsConfigurer
    ```

    - `tokenStore`：设置令牌存储方式

  - 配置客户端信息：

    ```
    ClientDetailsServiceConfigurer
    ```

    - `withClientDetails`：设置客户端配置读取方式

- 配置 Web 安全

  - 配置密码加密方式：`BCryptPasswordEncoder`
  - 配置认证信息：`AuthenticationManagerBuilder`

- 通过 GET 请求访问认证服务器获取授权码

  - 端点：`/oauth/authorize`

- 通过 POST 请求利用授权码访问认证服务器获取令牌

  - 端点：`/oauth/token`

**附：默认的端点 URL**

- `/oauth/authorize`：授权端点
- `/oauth/token`：令牌端点
- `/oauth/confirm_access`：用户确认授权提交端点
- `/oauth/error`：授权服务错误信息端点
- `/oauth/check_token`：用于资源服务访问的令牌解析端点
- `/oauth/token_key`：提供公有密匙的端点，如果你使用 JWT 令牌的话

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E5%88%9D%E5%A7%8B%E5%8C%96-oauth2-%E7%9B%B8%E5%85%B3%E8%A1%A8)初始化 oAuth2 相关表

使用官方提供的建表脚本初始化 oAuth2 相关表，地址如下：

```text
https://github.com/spring-projects/spring-security-oauth/blob/master/spring-security-oauth2/src/test/resources/schema.sql
```

1

由于我们使用的是 MySQL 数据库，默认建表语句中主键为 `VARCHAR(256)`，这超过了最大的主键长度，请手动修改为 `128`，并用 `BLOB` 替换语句中的 `LONGVARBINARY` 类型，修改后的建表脚本如下：

```sql
CREATE TABLE `clientdetails` (
  `appId` varchar(128) NOT NULL,
  `resourceIds` varchar(256) DEFAULT NULL,
  `appSecret` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `grantTypes` varchar(256) DEFAULT NULL,
  `redirectUrl` varchar(256) DEFAULT NULL,
  `authorities` varchar(256) DEFAULT NULL,
  `access_token_validity` int(11) DEFAULT NULL,
  `refresh_token_validity` int(11) DEFAULT NULL,
  `additionalInformation` varchar(4096) DEFAULT NULL,
  `autoApproveScopes` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`appId`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_access_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication_id` varchar(128) NOT NULL,
  `user_name` varchar(256) DEFAULT NULL,
  `client_id` varchar(256) DEFAULT NULL,
  `authentication` blob,
  `refresh_token` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`authentication_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_approvals` (
  `userId` varchar(256) DEFAULT NULL,
  `clientId` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `status` varchar(10) DEFAULT NULL,
  `expiresAt` timestamp NULL DEFAULT NULL,
  `lastModifiedAt` timestamp NULL DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_client_details` (
  `client_id` varchar(128) NOT NULL,
  `resource_ids` varchar(256) DEFAULT NULL,
  `client_secret` varchar(256) DEFAULT NULL,
  `scope` varchar(256) DEFAULT NULL,
  `authorized_grant_types` varchar(256) DEFAULT NULL,
  `web_server_redirect_uri` varchar(256) DEFAULT NULL,
  `authorities` varchar(256) DEFAULT NULL,
  `access_token_validity` int(11) DEFAULT NULL,
  `refresh_token_validity` int(11) DEFAULT NULL,
  `additional_information` varchar(4096) DEFAULT NULL,
  `autoapprove` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`client_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_client_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication_id` varchar(128) NOT NULL,
  `user_name` varchar(256) DEFAULT NULL,
  `client_id` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`authentication_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_code` (
  `code` varchar(256) DEFAULT NULL,
  `authentication` blob
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `oauth_refresh_token` (
  `token_id` varchar(256) DEFAULT NULL,
  `token` blob,
  `authentication` blob
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E5%9C%A8%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E9%85%8D%E7%BD%AE%E5%AE%A2%E6%88%B7%E7%AB%AF)在数据库中配置客户端

在表 `oauth_client_details` 中增加一条客户端配置记录，需要设置的字段如下：

- `client_id`：客户端标识
- `client_secret`：客户端安全码，**此处不能是明文，需要加密**
- `scope`：客户端授权范围
- `authorized_grant_types`：客户端授权类型
- `web_server_redirect_uri`：服务器回调地址

使用 `BCryptPasswordEncoder` 为客户端安全码加密，代码如下：

```java
System.out.println(new BCryptPasswordEncoder().encode("secret"));
```

1

数据库配置客户端效果图如下：

![img](https://www.funtl.com/assets1/Lusifer_20190403150110.png)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#pom)POM

由于使用了 JDBC 存储，我们需要增加相关依赖，数据库连接池部分弃用 `Druid` 改为 `HikariCP` （**号称全球最快连接池**）

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>${hikaricp.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <exclusions>
        <!-- 排除 tomcat-jdbc 以使用 HikariCP -->
        <exclusion>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-jdbc</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E9%85%8D%E7%BD%AE%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8)配置认证服务器

创建一个类继承 `AuthorizationServerConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableAuthorizationServer`

```java
package com.funtl.oauth2.server.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.jdbc.DataSourceBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;
import org.springframework.security.oauth2.config.annotation.web.configurers.AuthorizationServerEndpointsConfigurer;
import org.springframework.security.oauth2.provider.ClientDetailsService;
import org.springframework.security.oauth2.provider.client.JdbcClientDetailsService;
import org.springframework.security.oauth2.provider.token.TokenStore;
import org.springframework.security.oauth2.provider.token.store.JdbcTokenStore;

import javax.sql.DataSource;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {

    @Bean
    @Primary
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource() {
        // 配置数据源（注意，我使用的是 HikariCP 连接池），以上注解是指定数据源，否则会有冲突
        return DataSourceBuilder.create().build();
    }

    @Bean
    public TokenStore tokenStore() {
        // 基于 JDBC 实现，令牌保存到数据
        return new JdbcTokenStore(dataSource());
    }

    @Bean
    public ClientDetailsService jdbcClientDetails() {
        // 基于 JDBC 实现，需要事先在数据库配置客户端信息
        return new JdbcClientDetailsService(dataSource());
    }

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        // 设置令牌
        endpoints.tokenStore(tokenStore());
    }

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 读取客户端配置
        clients.withClientDetails(jdbcClientDetails());
    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)服务器安全配置

创建一个类继承 `WebSecurityConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableWebSecurity`
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

```java
package com.funtl.oauth2.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        // 设置默认的加密方式
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

        auth.inMemoryAuthentication()
                // 在内存中创建用户并为密码加密
                .withUser("user").password(passwordEncoder().encode("123456")).roles("USER")
                .and()
                .withUser("admin").password(passwordEncoder().encode("123456")).roles("ADMIN");

    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#application-yml)application.yml

```yaml
spring:
  application:
    name: oauth2-server
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    jdbc-url: jdbc:mysql://192.168.141.128:3307/oauth2?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
    hikari:
      minimum-idle: 5
      idle-timeout: 600000
      maximum-pool-size: 10
      auto-commit: true
      pool-name: MyHikariCP
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1

server:
  port: 8080
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E8%AE%BF%E9%97%AE%E8%8E%B7%E5%8F%96%E6%8E%88%E6%9D%83%E7%A0%81)访问获取授权码

打开浏览器，输入地址：

```text
http://localhost:8080/oauth/authorize?client_id=client&response_type=code
```

1

第一次访问会跳转到登录页面

![img](https://www.funtl.com/assets1/Lusifer_20190401195014.png)

验证成功后会询问用户是否授权客户端

![img](https://www.funtl.com/assets1/Lusifer_20190401195129.png)

选择授权后会跳转到我的博客，浏览器地址上还会包含一个授权码（`code=1JuO6V`），浏览器地址栏会显示如下地址：

```text
http://www.funtl.com/?code=1JuO6V
```

1

有了这个授权码就可以获取访问令牌了

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html#%E9%80%9A%E8%BF%87%E6%8E%88%E6%9D%83%E7%A0%81%E5%90%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%94%B3%E8%AF%B7%E4%BB%A4%E7%89%8C)通过授权码向服务器申请令牌

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```

1

![img](https://www.funtl.com/assets1/Lusifer_20190402232952.png)

得到响应结果如下：

```json
{
    "access_token": "016d8d4a-dd6e-4493-b590-5f072923c413",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "app"
}
```

1
2
3
4
5
6

操作成功后数据库 `oauth_access_token` 表中会增加一笔记录，效果图如下：

![img](https://www.funtl.com/assets1/Lusifer_20190403150529.png)    

# RBAC 基于角色的权限控制

## [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-RBAC 基于角色的权限控制1](https://www.bilibili.com/video/av48590637/?p=11)
- [Spring Security oAuth2-RBAC 基于角色的权限控制2](https://www.bilibili.com/video/av48590637/?p=12)

## [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E6%A6%82%E8%BF%B0)概述

RBAC（Role-Based Access Control，基于角色的访问控制），就是用户通过角色与权限进行关联。简单地说，一个用户拥有若干角色，每一个角色拥有若干权限。这样，就构造成“用户-角色-权限”的授权模型。在这种模型中，用户与角色之间，角色与权限之间，一般是多对多的关系。（如下图）

![img](https://www.funtl.com/assets1/Lusifer_2019040416220001.png)

## [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E7%9B%AE%E7%9A%84)目的

在我们的 oAuth2 系统中，我们需要对系统的所有资源进行权限控制，系统中的资源包括：

- 静态资源（对象资源）：功能操作、数据列
- 动态资源（数据资源）：数据

系统的目的就是对应用系统的所有对象资源和数据资源进行权限控制，比如：功能菜单、界面按钮、数据显示的列、各种行级数据进行权限的操控

## [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E5%AF%B9%E8%B1%A1%E5%85%B3%E7%B3%BB)对象关系

### [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E6%9D%83%E9%99%90)权限

系统的所有权限信息。权限具有上下级关系，是一个树状的结构。如：

- 系统管理
  - 用户管理
    - 查看用户
    - 新增用户
    - 修改用户
    - 删除用户

### [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E7%94%A8%E6%88%B7)用户

系统的具体操作者，可以归属于一个或多个角色，它与角色的关系是多对多的关系

### [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E8%A7%92%E8%89%B2)角色

为了对许多拥有相似权限的用户进行分类管理，定义了角色的概念，例如系统管理员、管理员、用户、访客等角色。角色具有上下级关系，可以形成树状视图，父级角色的权限是自身及它的所有子角色的权限的综合。父级角色的用户、父级角色的组同理可推。

### [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E5%85%B3%E7%B3%BB%E5%9B%BE)关系图

![img](https://www.funtl.com/assets1/Lusifer_2019040416220002.png)

### [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E6%A8%A1%E5%9D%97%E5%9B%BE)模块图

![img](https://www.funtl.com/assets1/Lusifer_2019040417150001.png)

## [#](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E8%A1%A8%E7%BB%93%E6%9E%84)表结构

```sql
CREATE TABLE `tb_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父权限',
  `name` varchar(64) NOT NULL COMMENT '权限名称',
  `enname` varchar(64) NOT NULL COMMENT '权限英文名称',
  `url` varchar(255) NOT NULL COMMENT '授权路径',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=37 DEFAULT CHARSET=utf8 COMMENT='权限表';

CREATE TABLE `tb_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父角色',
  `name` varchar(64) NOT NULL COMMENT '角色名称',
  `enname` varchar(64) NOT NULL COMMENT '角色英文名称',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=37 DEFAULT CHARSET=utf8 COMMENT='角色表';

CREATE TABLE `tb_role_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  `permission_id` bigint(20) NOT NULL COMMENT '权限 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=37 DEFAULT CHARSET=utf8 COMMENT='角色权限表';

CREATE TABLE `tb_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(64) NOT NULL COMMENT '密码，加密存储',
  `phone` varchar(20) DEFAULT NULL COMMENT '注册手机号',
  `email` varchar(50) DEFAULT NULL COMMENT '注册邮箱',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`) USING BTREE,
  UNIQUE KEY `phone` (`phone`) USING BTREE,
  UNIQUE KEY `email` (`email`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=37 DEFAULT CHARSET=utf8 COMMENT='用户表';

CREATE TABLE `tb_user_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `user_id` bigint(20) NOT NULL COMMENT '用户 ID',
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=37 DEFAULT CHARSET=utf8 COMMENT='用户角色表';
```

# 基于 RBAC 的自定义认证

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-基于 RBAC 的自定义认证1](https://www.bilibili.com/video/av48590637/?p=13)
- [Spring Security oAuth2-基于 RBAC 的自定义认证2](https://www.bilibili.com/video/av48590637/?p=14)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E6%A6%82%E8%BF%B0)概述

在实际开发中，我们的用户信息都是存在数据库里的，本章节基于 [RBAC 模型](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html) 将用户的认证信息与数据库对接，实现真正的用户认证与授权

**操作流程**

继续 [基于 JDBC 存储令牌](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-JDBC-%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html) 章节的代码开发

- 初始化 RBAC 相关表
- 在数据库中配置“用户”、“角色”、“权限”相关信息
- 数据库操作使用 `tk.mybatis` 框架，故需要增加相关依赖
- 配置 Web 安全
  - 配置使用自定义认证与授权
- 通过 GET 请求访问认证服务器获取授权码
  - 端点：`/oauth/authorize`
- 通过 POST 请求利用授权码访问认证服务器获取令牌
  - 端点：`/oauth/token`

**附：默认的端点 URL**

- `/oauth/authorize`：授权端点
- `/oauth/token`：令牌端点
- `/oauth/confirm_access`：用户确认授权提交端点
- `/oauth/error`：授权服务错误信息端点
- `/oauth/check_token`：用于资源服务访问的令牌解析端点
- `/oauth/token_key`：提供公有密匙的端点，如果你使用 JWT 令牌的话

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E5%88%9D%E5%A7%8B%E5%8C%96-rbac-%E7%9B%B8%E5%85%B3%E8%A1%A8)初始化 RBAC 相关表

```sql
CREATE TABLE `tb_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父权限',
  `name` varchar(64) NOT NULL COMMENT '权限名称',
  `enname` varchar(64) NOT NULL COMMENT '权限英文名称',
  `url` varchar(255) NOT NULL COMMENT '授权路径',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=44 DEFAULT CHARSET=utf8 COMMENT='权限表';
insert  into `tb_permission`(`id`,`parent_id`,`name`,`enname`,`url`,`description`,`created`,`updated`) values 
(37,0,'系统管理','System','/',NULL,'2019-04-04 23:22:54','2019-04-04 23:22:56'),
(38,37,'用户管理','SystemUser','/users/',NULL,'2019-04-04 23:25:31','2019-04-04 23:25:33'),
(39,38,'查看用户','SystemUserView','',NULL,'2019-04-04 15:30:30','2019-04-04 15:30:43'),
(40,38,'新增用户','SystemUserInsert','',NULL,'2019-04-04 15:30:31','2019-04-04 15:30:44'),
(41,38,'编辑用户','SystemUserUpdate','',NULL,'2019-04-04 15:30:32','2019-04-04 15:30:45'),
(42,38,'删除用户','SystemUserDelete','',NULL,'2019-04-04 15:30:48','2019-04-04 15:30:45');

CREATE TABLE `tb_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父角色',
  `name` varchar(64) NOT NULL COMMENT '角色名称',
  `enname` varchar(64) NOT NULL COMMENT '角色英文名称',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='角色表';
insert  into `tb_role`(`id`,`parent_id`,`name`,`enname`,`description`,`created`,`updated`) values 
(37,0,'超级管理员','admin',NULL,'2019-04-04 23:22:03','2019-04-04 23:22:05');

CREATE TABLE `tb_role_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  `permission_id` bigint(20) NOT NULL COMMENT '权限 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=43 DEFAULT CHARSET=utf8 COMMENT='角色权限表';
insert  into `tb_role_permission`(`id`,`role_id`,`permission_id`) values 
(37,37,37),
(38,37,38),
(39,37,39),
(40,37,40),
(41,37,41),
(42,37,42);

CREATE TABLE `tb_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(64) NOT NULL COMMENT '密码，加密存储',
  `phone` varchar(20) DEFAULT NULL COMMENT '注册手机号',
  `email` varchar(50) DEFAULT NULL COMMENT '注册邮箱',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`) USING BTREE,
  UNIQUE KEY `phone` (`phone`) USING BTREE,
  UNIQUE KEY `email` (`email`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户表';
insert  into `tb_user`(`id`,`username`,`password`,`phone`,`email`,`created`,`updated`) values 
(37,'admin','$2a$10$9ZhDOBp.sRKat4l14ygu/.LscxrMUcDAfeVOEPiYwbcRkoB09gCmi','15888888888','lee.lusifer@gmail.com','2019-04-04 23:21:27','2019-04-04 23:21:29');

CREATE TABLE `tb_user_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `user_id` bigint(20) NOT NULL COMMENT '用户 ID',
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户角色表';
insert  into `tb_user_role`(`id`,`user_id`,`role_id`) values 
(37,37,37);
```



由于使用了 `BCryptPasswordEncoder` 的加密方式，故用户密码需要加密，代码如下：

```java
System.out.println(new BCryptPasswordEncoder().encode("123456"));
```

1

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#pom)POM

数据库操作采用 `tk.mybatis` 框架，需增加相关依赖

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
</dependency>
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E5%85%B3%E9%94%AE%E6%AD%A5%E9%AA%A4)关键步骤

由于本次增加了 MyBatis 相关操作，代码增加较多，可以参考我 [**GitHub**](https://github.com/topsale/spring-boot-samples/tree/master/spring-security-oauth2) 上的源码，下面仅列出关键步骤及代码

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E8%8E%B7%E5%8F%96%E7%94%A8%E6%88%B7%E4%BF%A1%E6%81%AF)获取用户信息

目的是为了实现自定义认证授权时可以通过数据库查询用户信息，Spring Security oAuth2 要求使用 `username` 的方式查询，提供相关用户信息后，认证工作由框架自行完成

```java
package com.funtl.oauth2.server.service.impl;

import com.funtl.oauth2.server.domain.TbUser;
import com.funtl.oauth2.server.mapper.TbUserMapper;
import com.funtl.oauth2.server.service.TbUserService;
import org.springframework.stereotype.Service;
import tk.mybatis.mapper.entity.Example;

import javax.annotation.Resource;

@Service
public class TbUserServiceImpl implements TbUserService {

    @Resource
    private TbUserMapper tbUserMapper;

    @Override
    public TbUser getByUsername(String username) {
        Example example = new Example(TbUser.class);
        example.createCriteria().andEqualTo("username", username);
        return tbUserMapper.selectOneByExample(example);
    }
}
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E8%8E%B7%E5%8F%96%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E4%BF%A1%E6%81%AF)获取用户权限信息

认证成功后需要给用户授权，具体的权限已经存储在数据库里了，SQL 语句如下：

```sql
SELECT
  p.*
FROM
  tb_user AS u
  LEFT JOIN tb_user_role AS ur
    ON u.id = ur.user_id
  LEFT JOIN tb_role AS r
    ON r.id = ur.role_id
  LEFT JOIN tb_role_permission AS rp
    ON r.id = rp.role_id
  LEFT JOIN tb_permission AS p
    ON p.id = rp.permission_id
WHERE u.id = <yourUserId>
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81%E6%8E%88%E6%9D%83%E5%AE%9E%E7%8E%B0%E7%B1%BB)自定义认证授权实现类

创建一个类，实现 `UserDetailsService` 接口，代码如下：

```java
package com.funtl.oauth2.server.config.service;

import com.funtl.oauth2.server.domain.TbPermission;
import com.funtl.oauth2.server.domain.TbUser;
import com.funtl.oauth2.server.service.TbPermissionService;
import com.funtl.oauth2.server.service.TbUserService;
import org.assertj.core.util.Lists;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 自定义用户认证与授权
 * <p>
 * Description:
 * </p>
 *
 * @author Lusifer
 * @version v1.0.0
 * @date 2019-04-04 23:57:04
 * @see com.funtl.oauth2.server.config
 */
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private TbUserService tbUserService;

    @Autowired
    private TbPermissionService tbPermissionService;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 查询用户信息
        TbUser tbUser = tbUserService.getByUsername(username);
        List<GrantedAuthority> grantedAuthorities = Lists.newArrayList();
        if (tbUser != null) {
            // 获取用户授权
            List<TbPermission> tbPermissions = tbPermissionService.selectByUserId(tbUser.getId());

            // 声明用户授权
            tbPermissions.forEach(tbPermission -> {
                if (tbPermission != null && tbPermission.getEnname() != null) {
                    GrantedAuthority grantedAuthority = new SimpleGrantedAuthority(tbPermission.getEnname());
                    grantedAuthorities.add(grantedAuthority);
                }
            });
        }
        
        // 由框架完成认证工作
        return new User(tbUser.getUsername(), tbUser.getPassword(), grantedAuthorities);
    }
}
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)服务器安全配置

创建一个类继承 `WebSecurityConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableWebSecurity`
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

```java
package com.funtl.oauth2.server.config;

import com.funtl.oauth2.server.config.service.UserDetailsServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        // 设置默认的加密方式
        return new BCryptPasswordEncoder();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        return new UserDetailsServiceImpl();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 使用自定义认证与授权
        auth.userDetailsService(userDetailsService());
    }
}
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#application)Application

增加了 Mapper 的包扫描配置

```java
package com.funtl.oauth2;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

/**
 * 认证服务器，用于获取 Token
 * <p>
 * Description:
 * </p>
 *
 * @author Lusifer
 * @version v1.0.0
 * @date 2019-04-01 16:06:45
 * @see com.funtl.oauth2
 */
@SpringBootApplication
@MapperScan(basePackages = "com.funtl.oauth2.server.mapper")
public class OAuth2ServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(OAuth2ServerApplication.class, args);
    }

}
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#application-yml)application.yml

增加了 MyBatis 配置

```yaml
spring:
  application:
    name: oauth2-server
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    jdbc-url: jdbc:mysql://192.168.141.128:3307/oauth2?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
    hikari:
      minimum-idle: 5
      idle-timeout: 600000
      maximum-pool-size: 10
      auto-commit: true
      pool-name: MyHikariCP
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1

server:
  port: 8080

mybatis:
  type-aliases-package: com.funtl.oauth2.server.domain
  mapper-locations: classpath:mapper/*.xml
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E8%AE%BF%E9%97%AE%E8%8E%B7%E5%8F%96%E6%8E%88%E6%9D%83%E7%A0%81)访问获取授权码

打开浏览器，输入地址：

```text
http://localhost:8080/oauth/authorize?client_id=client&response_type=code
```



第一次访问会跳转到登录页面

![img](https://www.funtl.com/assets1/Lusifer_20190401195014.png)

验证成功后会询问用户是否授权客户端

![img](https://www.funtl.com/assets1/Lusifer_20190401195129.png)

选择授权后会跳转到我的博客，浏览器地址上还会包含一个授权码（`code=1JuO6V`），浏览器地址栏会显示如下地址：

```text
http://www.funtl.com/?code=1JuO6V
```



有了这个授权码就可以获取访问令牌了

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E-RBAC-%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AE%A4%E8%AF%81.html#%E9%80%9A%E8%BF%87%E6%8E%88%E6%9D%83%E7%A0%81%E5%90%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%94%B3%E8%AF%B7%E4%BB%A4%E7%89%8C)通过授权码向服务器申请令牌

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```



![img](https://www.funtl.com/assets1/Lusifer_20190402232952.png)

得到响应结果如下：

```json
{
    "access_token": "016d8d4a-dd6e-4493-b590-5f072923c413",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "app"
}
```



操作成功后数据库 `oauth_access_token` 表中会增加一笔记录，效果图如下：

![img](https://www.funtl.com/assets1/Lusifer_20190403150529.png)

# There is no PasswordEncoder mapped

## [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#%E9%97%AE%E9%A2%98%E6%8F%8F%E8%BF%B0)问题描述

按照 [基于内存存储令牌](https://www.funtl.com/zh/spring-security-oauth2/%E5%9F%BA%E4%BA%8E%E5%86%85%E5%AD%98%E5%AD%98%E5%82%A8%E4%BB%A4%E7%89%8C.html) 配置成功后，携授权码使用 POST 请求认证服务器时，服务器返回错误信息

**版本**

- Spring Boot: 2.1.3.RELEASE
- Spring Security: 5.1.4.RELEASE

**日志**

```text
java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)解决方案

Spring Security 5.0 之前版本的 `PasswordEncoder` 接口默认实现为 `NoOpPasswordEncoder` 此时是可以使用明文密码的，在 5.0 之后默认实现类改为 `DelegatingPasswordEncoder` 此时密码必须以加密形式存储。

### [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#application-yml)application.yml

删除 `spring.security` 相关配置，修改为

```yaml
spring:
  application:
    name: oauth2-server

server:
  port: 8080
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#websecurityconfiguration)WebSecurityConfiguration

```java
package com.funtl.oauth2.server.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        // 设置默认的加密方式
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

        auth.inMemoryAuthentication()
                // 在内存中创建用户并为密码加密
                .withUser("user").password(passwordEncoder().encode("123456")).roles("USER")
                .and()
                .withUser("admin").password(passwordEncoder().encode("123456")).roles("ADMIN");

    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32

### [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#authorizationserverconfiguration)AuthorizationServerConfiguration

```java
package com.funtl.oauth2.server.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.oauth2.config.annotation.configurers.ClientDetailsServiceConfigurer;
import org.springframework.security.oauth2.config.annotation.web.configuration.AuthorizationServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableAuthorizationServer;

@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {

    // 注入 WebSecurityConfiguration 中配置的 BCryptPasswordEncoder
    @Autowired
    private BCryptPasswordEncoder passwordEncoder;

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients
                .inMemory()
                .withClient("client")
                // 还需要为 secret 加密
                .secret(passwordEncoder.encode("secret"))
                .authorizedGrantTypes("authorization_code")
                .scopes("app")
                .redirectUris("http://www.funtl.com");

    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30

## [#](https://www.funtl.com/zh/spring-security-oauth2/PasswordEncoder.html#%E6%B5%8B%E8%AF%95%E8%AE%BF%E9%97%AE)测试访问

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```

1

![img](https://www.funtl.com/assets1/Lusifer_20190402232952.png)

得到响应结果如下：

```json
{
    "access_token": "016d8d4a-dd6e-4493-b590-5f072923c413",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "app"
}
```

# 对认证服务器的修改

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AF%B9%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E4%BF%AE%E6%94%B9.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-对认证服务器的修改](https://www.bilibili.com/video/av48590637/?p=15)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AF%B9%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E4%BF%AE%E6%94%B9.html#%E6%A6%82%E8%BF%B0)概述

在开发资源服务器之前，我们需要对 [创建认证服务器](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97) 章节的配置进行小量修改，需要修改的内容如下：

- 删除 `spring-boot-starter-security` 依赖，因为 `spring-cloud-starter-oauth2` 包含了该依赖
- 解决访问 `/oauth/check_token` 端点的 403 问题
- 优化 RBAC 模型数据，以便更好的演示资源服务器的概念

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AF%B9%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E4%BF%AE%E6%94%B9.html#pom)POM

修改 `spring-security-oauth2-server` 项目的 `pom.xml`，删除 `spring-boot-starter-security` 依赖，因为 `spring-cloud-starter-oauth2` 包含了该依赖，完整 POM 如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-security-oauth2</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-security-oauth2-server</artifactId>
    <url>http://www.funtl.com</url>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!-- Spring Security -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-oauth2</artifactId>
        </dependency>

        <!-- 数据库 -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <exclusions>
                <!-- 排除 tomcat-jdbc 以使用 HikariCP -->
                <exclusion>
                    <groupId>org.apache.tomcat</groupId>
                    <artifactId>tomcat-jdbc</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.oauth2.OAuth2ServerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AF%B9%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E4%BF%AE%E6%94%B9.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)服务器安全配置

资源服务器需要访问 `/oauth/check_token` 端点来检查 `access_token` 的有效性，此时该端点是受保护的资源，当我们访问该端点时会遇到 403 问题，将该端点暴露出来即可，暴露端点的关键代码为：

```java
@Override
public void configure(WebSecurity web) throws Exception {
    // 将 check_token 暴露出去，否则资源服务器访问时报 403 错误
    web.ignoring().antMatchers("/oauth/check_token");
}
```

1
2
3
4
5

完整代码如下：

```java
package com.funtl.oauth2.server.config;

import com.funtl.oauth2.server.config.service.UserDetailsServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        // 设置默认的加密方式
        return new BCryptPasswordEncoder();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        return new UserDetailsServiceImpl();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 使用自定义认证与授权
        auth.userDetailsService(userDetailsService());
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        // 将 check_token 暴露出去，否则资源服务器访问时报 403 错误
        web.ignoring().antMatchers("/oauth/check_token");
    }
}
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%AF%B9%E8%AE%A4%E8%AF%81%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E4%BF%AE%E6%94%B9.html#%E5%88%9D%E5%A7%8B%E5%8C%96-rbac-%E7%9B%B8%E5%85%B3%E8%A1%A8)初始化 RBAC 相关表

增加了内容管理权限的数据，以便于我演示资源服务器的用法，SQL 语句如下：

```sql
CREATE TABLE `tb_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父权限',
  `name` varchar(64) NOT NULL COMMENT '权限名称',
  `enname` varchar(64) NOT NULL COMMENT '权限英文名称',
  `url` varchar(255) NOT NULL COMMENT '授权路径',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=49 DEFAULT CHARSET=utf8 COMMENT='权限表';
insert  into `tb_permission`(`id`,`parent_id`,`name`,`enname`,`url`,`description`,`created`,`updated`) values 
(37,0,'系统管理','System','/',NULL,'2019-04-04 23:22:54','2019-04-04 23:22:56'),
(38,37,'用户管理','SystemUser','/users/',NULL,'2019-04-04 23:25:31','2019-04-04 23:25:33'),
(39,38,'查看用户','SystemUserView','/users/view/**',NULL,'2019-04-04 15:30:30','2019-04-04 15:30:43'),
(40,38,'新增用户','SystemUserInsert','/users/insert/**',NULL,'2019-04-04 15:30:31','2019-04-04 15:30:44'),
(41,38,'编辑用户','SystemUserUpdate','/users/update/**',NULL,'2019-04-04 15:30:32','2019-04-04 15:30:45'),
(42,38,'删除用户','SystemUserDelete','/users/delete/**',NULL,'2019-04-04 15:30:48','2019-04-04 15:30:45'),
(44,37,'内容管理','SystemContent','/contents/',NULL,'2019-04-06 18:23:58','2019-04-06 18:24:00'),
(45,44,'查看内容','SystemContentView','/contents/view/**',NULL,'2019-04-06 23:49:39','2019-04-06 23:49:41'),
(46,44,'新增内容','SystemContentInsert','/contents/insert/**',NULL,'2019-04-06 23:51:00','2019-04-06 23:51:02'),
(47,44,'编辑内容','SystemContentUpdate','/contents/update/**',NULL,'2019-04-06 23:51:04','2019-04-06 23:51:06'),
(48,44,'删除内容','SystemContentDelete','/contents/delete/**',NULL,'2019-04-06 23:51:08','2019-04-06 23:51:10');

CREATE TABLE `tb_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父角色',
  `name` varchar(64) NOT NULL COMMENT '角色名称',
  `enname` varchar(64) NOT NULL COMMENT '角色英文名称',
  `description` varchar(200) DEFAULT NULL COMMENT '备注',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='角色表';
insert  into `tb_role`(`id`,`parent_id`,`name`,`enname`,`description`,`created`,`updated`) values 
(37,0,'超级管理员','admin',NULL,'2019-04-04 23:22:03','2019-04-04 23:22:05');

CREATE TABLE `tb_role_permission` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  `permission_id` bigint(20) NOT NULL COMMENT '权限 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=48 DEFAULT CHARSET=utf8 COMMENT='角色权限表';
insert  into `tb_role_permission`(`id`,`role_id`,`permission_id`) values 
(37,37,37),
(38,37,38),
(39,37,39),
(40,37,40),
(41,37,41),
(42,37,42),
(43,37,44),
(44,37,45),
(45,37,46),
(46,37,47),
(47,37,48);

CREATE TABLE `tb_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(64) NOT NULL COMMENT '密码，加密存储',
  `phone` varchar(20) DEFAULT NULL COMMENT '注册手机号',
  `email` varchar(50) DEFAULT NULL COMMENT '注册邮箱',
  `created` datetime NOT NULL,
  `updated` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`) USING BTREE,
  UNIQUE KEY `phone` (`phone`) USING BTREE,
  UNIQUE KEY `email` (`email`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户表';
insert  into `tb_user`(`id`,`username`,`password`,`phone`,`email`,`created`,`updated`) values 
(37,'admin','$2a$10$9ZhDOBp.sRKat4l14ygu/.LscxrMUcDAfeVOEPiYwbcRkoB09gCmi','15888888888','lee.lusifer@gmail.com','2019-04-04 23:21:27','2019-04-04 23:21:29');

CREATE TABLE `tb_user_role` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `user_id` bigint(20) NOT NULL COMMENT '用户 ID',
  `role_id` bigint(20) NOT NULL COMMENT '角色 ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=38 DEFAULT CHARSET=utf8 COMMENT='用户角色表';
insert  into `tb_user_role`(`id`,`user_id`,`role_id`) values 
(37,37,37);
```

# 创建资源服务器模块

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E6%9C%AC%E8%8A%82%E8%A7%86%E9%A2%91)本节视频

- [Spring Security oAuth2-创建资源服务器模块1](https://www.bilibili.com/video/av48590637/?p=16)
- [Spring Security oAuth2-创建资源服务器模块2](https://www.bilibili.com/video/av48590637/?p=17)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E6%A6%82%E8%BF%B0)概述

在 [为什么需要 oAuth2](https://www.funtl.com/zh/spring-security-oauth2/%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81-oAuth2.html#%E5%90%8D%E8%AF%8D%E8%A7%A3%E9%87%8A) 和 [RBAC 基于角色的权限控制](https://www.funtl.com/zh/spring-security-oauth2/RBAC-%E5%9F%BA%E4%BA%8E%E8%A7%92%E8%89%B2%E7%9A%84%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6.html#%E7%9B%AE%E7%9A%84) 章节，我们介绍过资源的概念，简单点说就是需要被访问的业务数据或是静态资源文件都可以被称作资源。

为了让大家更好的理解资源服务器的概念，我们单独创建一个名为 `spring-security-oauth2-resource` 资源服务器的项目，该项目的主要目的就是对数据表的 CRUD 操作，而这些操作就是对资源的操作了。

**操作流程**

![img](https://www.funtl.com/assets1/Lusifer_2019040703090001.png)

- 初始化资源服务器数据库
- POM 所需依赖同认证服务器
- 配置资源服务器
- 配置资源(Controller)

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E5%88%9D%E5%A7%8B%E5%8C%96%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%B0%E6%8D%AE%E5%BA%93)初始化资源服务器数据库

```sql
CREATE TABLE `tb_content` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `category_id` bigint(20) NOT NULL COMMENT '内容类目ID',
  `title` varchar(200) DEFAULT NULL COMMENT '内容标题',
  `sub_title` varchar(100) DEFAULT NULL COMMENT '子标题',
  `title_desc` varchar(500) DEFAULT NULL COMMENT '标题描述',
  `url` varchar(500) DEFAULT NULL COMMENT '链接',
  `pic` varchar(300) DEFAULT NULL COMMENT '图片绝对路径',
  `pic2` varchar(300) DEFAULT NULL COMMENT '图片2',
  `content` text COMMENT '内容',
  `created` datetime DEFAULT NULL,
  `updated` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `category_id` (`category_id`),
  KEY `updated` (`updated`)
) ENGINE=InnoDB AUTO_INCREMENT=42 DEFAULT CHARSET=utf8;
insert  into `tb_content`(`id`,`category_id`,`title`,`sub_title`,`title_desc`,`url`,`pic`,`pic2`,`content`,`created`,`updated`) values 
(28,89,'标题','子标题','标题说明','http://www.jd.com',NULL,NULL,NULL,'2019-04-07 00:56:09','2019-04-07 00:56:11'),
(29,89,'ad2','ad2','ad2','http://www.baidu.com',NULL,NULL,NULL,'2019-04-07 00:56:13','2019-04-07 00:56:15'),
(30,89,'ad3','ad3','ad3','http://www.sina.com.cn',NULL,NULL,NULL,'2019-04-07 00:56:17','2019-04-07 00:56:19'),
(31,89,'ad4','ad4','ad4','http://www.funtl.com',NULL,NULL,NULL,'2019-04-07 00:56:22','2019-04-07 00:56:25');

CREATE TABLE `tb_content_category` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '类目ID',
  `parent_id` bigint(20) DEFAULT NULL COMMENT '父类目ID=0时，代表的是一级的类目',
  `name` varchar(50) DEFAULT NULL COMMENT '分类名称',
  `status` int(1) DEFAULT '1' COMMENT '状态。可选值:1(正常),2(删除)',
  `sort_order` int(4) DEFAULT NULL COMMENT '排列序号，表示同级类目的展现次序，如数值相等则按名称次序排列。取值范围:大于零的整数',
  `is_parent` tinyint(1) DEFAULT '1' COMMENT '该类目是否为父类目，1为true，0为false',
  `created` datetime DEFAULT NULL COMMENT '创建时间',
  `updated` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`),
  KEY `parent_id` (`parent_id`,`status`) USING BTREE,
  KEY `sort_order` (`sort_order`)
) ENGINE=InnoDB AUTO_INCREMENT=98 DEFAULT CHARSET=utf8 COMMENT='内容分类';
insert  into `tb_content_category`(`id`,`parent_id`,`name`,`status`,`sort_order`,`is_parent`,`created`,`updated`) values 
(30,0,'LeeShop',1,1,1,'2015-04-03 16:51:38','2015-04-03 16:51:40'),
(86,30,'首页',1,1,1,'2015-06-07 15:36:07','2015-06-07 15:36:07'),
(87,30,'列表页面',1,1,1,'2015-06-07 15:36:16','2015-06-07 15:36:16'),
(88,30,'详细页面',1,1,1,'2015-06-07 15:36:27','2015-06-07 15:36:27'),
(89,86,'大广告',1,1,0,'2015-06-07 15:36:38','2015-06-07 15:36:38'),
(90,86,'小广告',1,1,0,'2015-06-07 15:36:45','2015-06-07 15:36:45'),
(91,86,'商城快报',1,1,0,'2015-06-07 15:36:55','2015-06-07 15:36:55'),
(92,87,'边栏广告',1,1,0,'2015-06-07 15:37:07','2015-06-07 15:37:07'),
(93,87,'页头广告',1,1,0,'2015-06-07 15:37:17','2015-06-07 15:37:17'),
(94,87,'页脚广告',1,1,0,'2015-06-07 15:37:31','2015-06-07 15:37:31'),
(95,88,'边栏广告',1,1,0,'2015-06-07 15:37:56','2015-06-07 15:37:56'),
(96,86,'中广告',1,1,1,'2015-07-25 18:58:52','2015-07-25 18:58:52'),
(97,96,'中广告1',1,1,0,'2015-07-25 18:59:43','2015-07-25 18:59:43');
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#pom)POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>spring-security-oauth2</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>spring-security-oauth2-resource</artifactId>
    <url>http://www.funtl.com</url>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>liwemin</id>
            <name>Lusifer Lee</name>
            <email>lee.lusifer@gmail.com</email>
        </developer>
    </developers>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!-- Spring Security -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-oauth2</artifactId>
        </dependency>

        <!-- 数据库 -->
        <dependency>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <exclusions>
                <!-- 排除 tomcat-jdbc 以使用 HikariCP -->
                <exclusion>
                    <groupId>org.apache.tomcat</groupId>
                    <artifactId>tomcat-jdbc</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.oauth2.OAuth2ResourceApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```



## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E5%85%B3%E9%94%AE%E6%AD%A5%E9%AA%A4)关键步骤

由于代码较多，可以参考我 [**GitHub**](https://github.com/topsale/spring-boot-samples/tree/master/spring-security-oauth2) 上的源码，下面仅列出关键步骤及代码

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E9%85%8D%E7%BD%AE%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8)配置资源服务器

创建一个类继承 `ResourceServerConfigurerAdapter` 并添加相关注解：

- `@Configuration`
- `@EnableResourceServer`：资源服务器
- `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)`：全局方法拦截

```java
package com.funtl.oauth2.resource.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;
import org.springframework.security.oauth2.config.annotation.web.configuration.ResourceServerConfigurerAdapter;

@Configuration
@EnableResourceServer
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
public class ResourceServerConfiguration extends ResourceServerConfigurerAdapter {

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http
                .exceptionHandling()
                .and()
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 以下为配置所需保护的资源路径及权限，需要与认证服务器配置的授权部分对应
                .antMatchers("/").hasAuthority("SystemContent")
                .antMatchers("/view/**").hasAuthority("SystemContentView")
                .antMatchers("/insert/**").hasAuthority("SystemContentInsert")
                .antMatchers("/update/**").hasAuthority("SystemContentUpdate")
                .antMatchers("/delete/**").hasAuthority("SystemContentDelete");
    }

}
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#application)Application

```java
package com.funtl.oauth2;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import tk.mybatis.spring.annotation.MapperScan;

@SpringBootApplication
@MapperScan(basePackages = "com.funtl.oauth2.resource.mapper")
public class OAuth2ResourceApplication {

    public static void main(String[] args) {
        SpringApplication.run(OAuth2ResourceApplication.class, args);
    }
}
```



### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#application-yml)application.yml

```yaml
spring:
  application:
    name: oauth2-resource
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.141.128:3307/oauth2_resource?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
    hikari:
      minimum-idle: 5
      idle-timeout: 600000
      maximum-pool-size: 10
      auto-commit: true
      pool-name: MyHikariCP
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1

security:
  oauth2:
    client:
      client-id: client
      client-secret: secret
      access-token-uri: http://localhost:8080/oauth/token
      user-authorization-uri: http://localhost:8080/oauth/authorize
    resource:
      token-info-uri: http://localhost:8080/oauth/check_token

server:
  port: 8081
  servlet:
    context-path: /contents

mybatis:
  type-aliases-package: com.funtl.oauth2.resource.domain
  mapper-locations: classpath:mapper/*.xml

logging:
  level:
    root: INFO
    org.springframework.web: INFO
    org.springframework.security: INFO
    org.springframework.security.oauth2: INFO
```

## [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E8%AE%BF%E9%97%AE%E8%B5%84%E6%BA%90)访问资源

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E8%AE%BF%E9%97%AE%E8%8E%B7%E5%8F%96%E6%8E%88%E6%9D%83%E7%A0%81)访问获取授权码

打开浏览器，输入地址：

```text
http://localhost:8080/oauth/authorize?client_id=client&response_type=code
```

1

第一次访问会跳转到登录页面

![img](https://www.funtl.com/assets1/Lusifer_20190401195014.png)

验证成功后会询问用户是否授权客户端

![img](https://www.funtl.com/assets1/Lusifer_20190401195129.png)

选择授权后会跳转到我的博客，浏览器地址上还会包含一个授权码（`code=1JuO6V`），浏览器地址栏会显示如下地址：

```text
http://www.funtl.com/?code=1JuO6V
```

1

有了这个授权码就可以获取访问令牌了

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E9%80%9A%E8%BF%87%E6%8E%88%E6%9D%83%E7%A0%81%E5%90%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%94%B3%E8%AF%B7%E4%BB%A4%E7%89%8C)通过授权码向服务器申请令牌

通过 CURL 或是 Postman 请求

```bash
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'grant_type=authorization_code&code=1JuO6V' "http://client:secret@localhost:8080/oauth/token"
```

1

![img](https://www.funtl.com/assets1/Lusifer_20190402232952.png)

得到响应结果如下：

```json
{
    "access_token": "016d8d4a-dd6e-4493-b590-5f072923c413",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "app"
}
```

1
2
3
4
5
6

### [#](https://www.funtl.com/zh/spring-security-oauth2/%E5%88%9B%E5%BB%BA%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%9D%97.html#%E6%90%BA%E5%B8%A6%E4%BB%A4%E7%89%8C%E8%AE%BF%E9%97%AE%E8%B5%84%E6%BA%90%E6%9C%8D%E5%8A%A1%E5%99%A8)携带令牌访问资源服务器

此处以获取全部资源为例，其它请求方式一样，可以参考我源码中的单元测试代码。可以使用以下方式请求：

- 使用 Headers 方式：需要在请求头增加 `Authorization: Bearer yourAccessToken`
- 直接请求带参数方式：`http://localhost:8081/contents?access_token=yourAccessToken`

使用 Headers 方式，通过 CURL 或是 Postman 请求

```bash
curl --location --request GET "http://localhost:8081/contents" --header "Content-Type: application/json" --header "Authorization: Bearer yourAccessToken"
```

1

![img](https://www.funtl.com/assets1/Lusifer_20190407033854.png)