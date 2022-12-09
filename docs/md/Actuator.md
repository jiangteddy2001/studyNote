# Actuator

## 1 使用目的

- Actuator是Springboot提供的用来对应用系统进行自省和监控的功能模块，借助于Actuator开发者可以很方便地对应用系统某些监控指标进行查看、统计等。
- 管理员可以对运行时各个参数、状态进行监控、管理。

## 2 Actuator配置

![image-20221209144133197](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091441227.png)

![image-20221209144148551](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091441588.png)

配置结束后访问 localhost:8081/actuator/health

![image-20221209144229674](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091442709.png)

## 3 配置说明

![image-20221209144316255](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091443301.png)

## 4 自定义Endpoint

我们可以通过

@Endpoint注解 + @ReadOperation、@WriteOperation、@DeleteOperation注解来实现自定义端点

![image-20221209144357418](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091443449.png)

然后用Postman访问

![image-20221209144430510](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091444543.png)

## 5、heapdump信息

SpringBoot程序运行在JVM上，是JVM的一个执行线程，Actuator提供了heapdump端点可以获取到Springboot所运行JVM的实时情况，访问localhost:8080/actuator/heapdump，可以进行dump文件的下载。可以使用visualvm打开dump文件

![image-20221209144548485](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091445518.png)

## 6、远程结束服务

actuator提供有shutdown端点可以在SpringBoot程序运行时，调用`/actuator/shutdown`接口远程结束程序，此功能默认是关闭的，需要配置application.yml文件开启。shutdown端点只能通过POST请求的方式调用

server:
  port: 8080
management:
  server:
    port: 8081 # actuator监听8081端口
  endpoint:
    shutdown:
      enabled: true # 开启shutdown功能