# SpringCloud

### 1 认证中心



登录请求后台接口，为了安全认证，所有请求都携带`token`信息进行安全认证，比如使用`vue`、`react`后者`h5`开发的`app`，用于控制可访问系统的资源。

`TokenController`控制器`login`方法会进行用户验证，如果验证通过会保存登录日志并返回`token`，同时缓存中会存入`login_tokens:xxxxxx`（包含用户、权限信息）。

`TokenController`控制器`refresh`方法会在用户调用时更新令牌有效期。

刷新令牌接口地址 `http://localhost:9200/refresh`

### 2 服务网关

`Spring Cloud Gateway`是基于`Spring`生态系统之上构建的`API`网关，包括：`Spring 5.x`，`Spring Boot 2.x`和`Project Reactor`。`Spring Cloud Gateway`旨在提供一种简单而有效的方法来路由到`API`，并为它们提供跨领域的关注点，例如：`安全性`，`监视/指标`，`限流`等。

- 核心概念

`路由（Route）`：路由是网关最基础的部分，路由信息由 ID、目标 URI、一组断言和一组过滤器组成。如果断言 路由为真，则说明请求的 URI 和配置匹配。
`断言（Predicate）`：Java8 中的断言函数。Spring Cloud Gateway 中的断言函数输入类型是 Spring 5.0 框架中 的 ServerWebExchange。Spring Cloud Gateway 中的断言函数允许开发者去定义匹配来自于 Http Request 中的任 何信息，比如请求头和参数等。
`过滤器（Filter）`：一个标准的 Spring Web Filter。Spring Cloud Gateway 中的 Filter 分为两种类型，分别是 Gateway Filter 和 Global Filter。过滤器将会对请求和响应进行处理。

### 3 注册中心

- 什么是注册中心

注册中心在微服务项目中扮演着非常重要的角色，是微服务架构中的纽带，类似于`通讯录`，它记录了服务和服务地址的映射关系。在分布式架构中，服务会注册到这里，当服务需要调用其它服务时，就到这里找到服务的地址，进行调用。

- 为什么要使用注册中心

注册中心解决了`服务发现`的问题。在没有注册中心时候，服务间调用需要知道被调方的地址或者代理地址。当服务更换部署地址，就不得不修改调用当中指定的地址或者修改代理配置。而有了注册中心之后，每个服务在调用别人的时候只需要知道服务名称就好，继续地址都会通过注册中心同步过来。

- Nacos 注册中心

`Nacos`是阿里巴巴开源的一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

![nacos](https://oscimg.oschina.net/oscnet/up-3b2499cb4616a7073db056095ff530c03c9.png)

### 4 配置中心

![config](https://oscimg.oschina.net/oscnet/up-e61a40fefffca8dd0ab215273af767f2961.png)

总得来说，配置中心就是一种统一管理各种应用配置的基础服务组件。

- 为什么要使用配置中心

配置中心将配置从各应用中剥离出来，对配置进行统一管理，应用自身不需要自己去管理配置。

- Nacos 配置中心

`Nacos`是阿里巴巴开源的一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

配置中心的服务流程如下：

1、用户在配置中心更新配置信息。
2、服务A和服务B及时得到配置更新通知，从配置中心获取配置。

![config](https://oscimg.oschina.net/oscnet/up-36349d06b1ac13ea40abd2f1666b73218aa.png)

### 5服务调用

Feign 旨在使编写 JAVA HTTP 客户端变得更加简单，Feign 简化了`RestTemplate`代码，实现了`Ribbon`负载均衡，使代码变得更加简洁，也少了客户端调用的代码，使用 Feign 实现负载均衡是首选方案，只需要你创建一个接口，然后在上面添加注解即可。
Feign 是声明式服务调用组件，其核心就是：像调用本地方法一样调用远程方法，无感知远程 HTTP 请求。让开发者调用远程接口就跟调用本地方法一样的体验，开发者完全无感知这是远程方法，无需关注与远程的交互细节，更无需关注分布式环境开发。

### 6服务监控

- spring boot actuator 服务监控接口

`actuator`是监控系统健康情况的工具。

- spring boot admin 服务监控管理

`Spring Boot Admin`是一个针对`spring-boot`的`actuator`接口进行UI美化封装的监控工具。他可以：在列表中浏览所有被监控`spring-boot`项目的基本信息，详细的Health信息、内存信息、JVM信息、垃圾回收信息、各种配置信息（比如数据源、缓存列表和命中率）等，还可以直接修改logger的level

### 7链路追踪(skywalking )

`SkyWalking`是一个可观测性分析平台（Observability Analysis Platform 简称OAP）和应用性能管理系统（Application Performance Management 简称 APM）。

提供分布式链路追踪，服务网格（Service Mesh）遥测分析，度量（Metric）聚合和可视化一体化解决方案。

SkyWalking 特点

- 多语言自动探针，java，.Net Code ,Node.Js
- 多监控手段，语言探针和Service Mesh
- 轻量高效，不需要额外搭建大数据平台
- 模块化架构，UI ，存储《集群管理多种机制可选
- 支持警告
- 优秀的可视化效果。

- Windows平台安装包下载

可以从`http://skywalking.apache.org/downloads`下载`apache-skywalking-apm-$version.tar.gz`包。

Windows下载解压后（.tar.gz），直接点击`bin/startup.bat`就可以了，这个时候实际上是启动了两个项目，一个收集器，一个web页面。

### 8 分库分表

分库：从单个数据库拆分成多个数据库的过程，将数据散落在多个数据库中。
分表：从单张表拆分成多张表的过程，将数据散落在多张表内。

`ShardingSphere`是一套开源的分布式数据库中间件解决方案组成的生态圈，它由`Sharding-JDBC`、`Sharding-Proxy`和`Sharding-Sidecar`这3款相互独立的产品组成。 他们均提供标准化的数据分片、读写分离、多数据副本、数据加密、影子库压测、分布式事务和数据库治理等功能

![shardingsphere](https://oscimg.oschina.net/oscnet/up-f1abffda350dfab3c2969f43057da342a23.png)

### 9 熔断讲解(Sentinel)

Sentinel是阿里开源的项目，提供了流量控制、熔断降级、系统负载保护等多个维度来保障服务之间的稳定性。
官网：https://github.com/alibaba/Sentinel/wiki

Hystrix常用的线程池隔离会造成线程上下切换的overhead比较大；Hystrix使用的信号量隔离对某个资源调用的并发数进行控制，效果不错，但是无法对慢调用进行自动降级；

Sentinel通过并发线程数的流量控制提供信号量隔离的功能；此外，Sentinel支持的熔断降级维度更多，可对多种指标进行流控、熔断，且提供了实时监控和控制面板，功能更为强大。



### 10 分布式事务(Seata)

`Seata`是阿里开源的一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。

`Seata`目标打造一站式的分布事务的解决方案，最终会提供四种事务模式：

### 11 分布式文件

目前支持三种存储方式，`本地存储`、`MinIO存储`、`FastDfs存储`，可以在`ruoyi-file-dev.yml`配置。

### 12 分布式日志（ELK）

实际上`ELK`是三款软件的简称，分别是`Elasticsearch`、 `Logstash`、`Kibana`组成。

**Elasticsearch** 基于`java`，是个开源分布式搜索引擎，它的特点有：分布式，零配置，自动发现，索引自动分片，索引副本机制，`restful`风格接口，多数据源，自动搜索负载等。

**Kibana** 基于`nodejs`，也是一个开源和免费的工具，`Kibana`可以为`Logstash`和`ElasticSearch`提供的日志分析友好的Web 界面，可以汇总、分析和搜索重要数据日志。

**Logstash** 基于`java`，是一个开源的用于收集,分析和存储日志的工具。

工作原理：

![elk](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212082158802.png)