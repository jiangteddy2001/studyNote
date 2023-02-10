

# Dubbo

## 1、基础知识

### 1.1 分布式系统

分布式系统是若干独立 计算机的集合，这些计算机对于用户来说就像单个相关系统。

老式系统(单一应用架构)就是把一个系统，统一放到一个服务器当中然后每一个服务器上放一个系统，如果说要更新代码的话，每一个服务器上的系统都要重新去部署十分的麻烦。

而分布式系统就是将一个完整的系统拆分成多个不同的服务，然后在将每一个服务单独的放到一个服务器当中。


### 1.2 RPC简介

**分布式应用架构(远程过程调用)**：当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。

RPC [ Remote Procedure Call]是指远程过程调用，是一种进程问通信方式，他是一种技术的思想，而不是规范。它允许程序调用另一个地址空间(通常是共享网络的另一台机器上)的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。

RPC（Remote Procedure Call）—远程过程调用，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。也就是说两台服务器A，B，一个应用部署在A服务器上，想要调用B服务器上应用提供的方法，由于不在一个内存空间，不能直接调用，需要通过网络来表达调用的语义和传达调用的数据。


![image-20230208133035978](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081330116.png)

Client像调用本地服务似的调用远程服务；
Client stub接收到调用后，将方法、参数序列化
客户端通过sockets将消息发送到服务端
Server stub 收到消息后进行解码（将消息对象反序列化）
Server stub 根据解码结果调用本地的服务
本地服务执行(对于服务端来说是本地执行)并将结果返回给Server stub
Server stub将返回结果打包成消息（将结果消息对象序列化）
服务端通过sockets将消息发送到客户端
Client stub接收到结果消息，并进行解码（将结果消息发序列化）
客户端得到最终结果

**RPC 调用分以下两种：**
**同步调用**：客户方等待调用执行完成并返回结果。
**异步调用**：客户方调用后不用等待执行结果返回，但依然可以通过回调通知等方式获取返回结果。若客户方不关心调用返回结果，则变成单向异步调用，单向调用不用返回结果。

### 1.3 RPC框架

当前框架有：Dubbo、gRPC、Thrift、HSF

## 2、Dubbo基本概念

### 2.1 简单介绍

官网：[Apache Dubbo](https://dubbo.apache.org/zh/)

![image-20230208133839976](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081338049.png)

Apache Dubbo 是一款 RPC 服务开发框架，用于解决微服务架构下的服务治理与通信问题，官方提供了 Java、Golang 等多语言 SDK 实现。使用 Dubbo 开发的微服务原生具备相互之间的远程地址发现与通信能力， 利用 Dubbo 提供的丰富服务治理特性，可以实现诸如服务发现、负载均衡、流量调度等服务治理诉求。



### 2.2 框架设计

![image-20230208134740007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081347056.png)

在传统的部署架构下，服务发现涉及提供者、消费者和注册中心三个参与角色，其中，提供者注册 URL 地址到注册中心，注册中心负责对数据进行聚合，消费者从注册中心订阅 URL 地址更新

服务提供者（Provider）：暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。

服务消费者（Consumer）: 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

注册中心（Registry）：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。

监控中心（Monitor）：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。


## 3. 环境搭建

### 3.1 搭建注册中心

zookeeper搭建

![image-20230208142105664](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081421714.png)

在bin文件下，启动zkServer.cmd会有报错，处理需要在condif文件中将zoo_sample.cfg文件复制一份，将名字改为zoo.cfg。

![image-20230208143346266](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081433352.png)

![image-20230208155156141](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081551176.png)

在zookeeper的文件夹下创建data文件夹，打开zoo.cfg，修改datadir，将dataDir数据保存为我们自定义的文件中

![image-20230208154851380](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081548428.png)

![image-20230208155328553](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081553587.png)

配置完毕后，我们再次在conf下启动zkServer.cmd

```
cd D:\zookeeper-3.4.12\bin
zkServer.cmd
```

启动成功

![image-20230208155608071](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081556123.png)

用客户端连接zookeeper

```
cd D:\zookeeper-3.4.12\bin
zkCli.cmd
```

![image-20230208155920484](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081559526.png)

使用zookeeper命令

```
# 获取根目录下的值
get /

# 查询根目录下
ls /

# 创建一个节点
create -e  /jiangbx 123456

# 获取新建的节点下的值
get /jiangbx
```

![image-20230208160055296](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081600333.png)

![image-20230208160335716](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081603741.png)

![image-20230208160405206](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081604246.png)

### 3.2 管理控制台安装

下载dubbo-admin,注意版本的区别

![image-20230208161443792](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081614837.png)

配置文件修改

![image-20230208170551028](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081705068.png)

启动dubbo-admin项目，访问地址

也可以打包后启动，cmd命令 `java -jar dubbo-admin-0.0.1-SNAPSHOT.jar`运行打包好的jar包

```
http://localhost:7001/

用户名：root
密码： root
```

![image-20230208171000124](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302081710186.png)

注意：要访问到监控中心，一定要启动zookeeper

### 3.3 创建提供者、消费者项目

**创建Maven项目=> `user-service-provider` 服务提供者**

![image-20230208202422352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082024508.png)

**UserAddress**

```
public class UserAddress implements Serializable{
    private Integer id;
    private String userAddress; //用户地址
    private String userId; //用户id
    private String consignee; //收货人
    private String phoneNum; //电话号码
    private String isDefault; //是否为默认地址    Y-是     N-否
    //get     set 
    //有参构造  无参构造
  }

```

```
//用户服务
public interface UserService {
	/**
	 * 按照用户id返回所有的收货地址
	 * @param userId
	 * @return
	 */
	public List<UserAddress> getUserAddressList(String userId);
}

```



```
public class UserServiceImpl implements UserService {
	public List<UserAddress> getUserAddressList(String userId) {

		UserAddress address1 = new UserAddress(1, "河南省郑州巩义市宋陵大厦2F", "1", "安然", "150360313x", "Y");
		UserAddress address2 = new UserAddress(2, "北京市昌平区沙河镇沙阳路", "1", "情话", "1766666395x", "N");

		return Arrays.asList(address1,address2);
	}
}

```

**创建Maven项目=> `order-service-consumer` 服务消费者(订单服务)**

![image-20230208203547581](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082035640.png)

```
public interface OrderService {
    /**
     * 初始化订单
     * @param userID
     */
    public void initOrder(String userID);
}

```



```
public class OrderServiceImpl implements OrderService {
    public void initOrder(String userID) {
        //查询用户的收货地址

    }
}

```

如果我们两个项目工程都创建共同的实体类，太过于麻烦了，我们可以将服务接口，服务模型等单独放在一个项目中，更为方便调用。

**创建Maven项目=> gmail-interface 用于存放共同的服务接口**

![image-20230208213550678](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082135718.png)



将 提供者 和 消费者 项目中的所有实体类复制到当前相关的文件包下，删除原有的实体类包及service包，也就是将实体类及service放在了当前公共的项目中。

把服务提供者和消费者项目中引入以下依赖，引入后项目不在报错

```
	<dependency>
			<groupId>com.jiang.gmall</groupId>
			<artifactId>gmall-interface</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>

```

![image-20230208213610950](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082136989.png)

### 3.4 服务提供者

在 `user-service-provider` 服务提供者项目中引入依赖

```
	 <!--dubbo-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>dubbo</artifactId>
        <version>2.6.2</version>
    </dependency>
    <!--注册中心是 zookeeper，引入zookeeper客户端-->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>2.12.0</version>
    </dependency>

```

在`resource`文件中创建`provider.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <!--1、指定当前服务/应用的名字(同样的服务名字相同，不要和别的服务同名)-->
   <dubbo:application name="user-service-provider"></dubbo:application>
    <!--2、指定注册中心的位置-->
    <!--<dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>-->
    <dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>
    <!--3、指定通信规则（通信协议? 服务端口）-->
    <dubbo:protocol name="dubbo" port="20880"></dubbo:protocol>
    <!--4、暴露服务 让别人调用 ref指向服务的真正实现对象-->
    <dubbo:service interface="com.jiang.gmall.service.UserService" ref="userServiceImpl"></dubbo:service>

    <!--服务的实现-->
    <bean id="userServiceImpl" class="com.jiang.gmall.service.impl.UserServiceImpl"></bean>
</beans>

```

编写一个`ProviderApplication`启动类程序，运行测试配置

```
public class MallApplication {
    public static void main(String[] args) throws IOException {
        ClassPathXmlApplicationContext applicationContext= new ClassPathXmlApplicationContext("provider.xml");
        applicationContext.start();
        System.in.read();
    }
}

```

![image-20230208214743801](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082147833.png)

启动zookeeper注册中心的的zkServer.cmd、和zkCli.cmd服务

再次启动项目，我们可以看到在zookeeper中已经发现服务提供者。

![image-20230208214727193](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082147260.png)



### 3.5 服务消费者

在 `order-service-consumer` 服务消费者项目中引入依赖

```
  <!--dubbo-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.6.2</version>
        </dependency>
        <!--注册中心是 zookeeper，引入zookeeper客户端-->
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>2.12.0</version>
        </dependency>

```

创建consumer.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
   <!--包扫描-->
    <context:component-scan base-package="com.jiang.gmall.service.impl"/>

    <!--指定当前服务/应用的名字(同样的服务名字相同，不要和别的服务同名)-->
    <dubbo:application name="order-service-consumer"></dubbo:application>
    <!--指定注册中心的位置-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>

    <!--调用远程暴露的服务，生成远程服务代理-->
    <dubbo:reference interface="com.jiang.gmall.service.UserService" id="userService"></dubbo:reference>
</beans>

```

把当前OrderServiceImpl实现类中加上注解

```
@Service
public class OrderServiceImpl implements OrderService {
    @Autowired
    public UserService userService;
    public void initOrder(String userID) {
        //查询用户的收货地址
        List<UserAddress> userAddressList = userService.getUserAddressList(userID);
        
        //为了直观的看到得到的数据，以下内容也可不写
        System.out.println("当前接收到的userId=> "+userID);
        System.out.println("**********");
        System.out.println("查询到的所有地址为：");
        for (UserAddress userAddress : userAddressList) {
            //打印远程服务地址的信息
            System.out.println(userAddress.getUserAddress());
        }
        
    }
}

```

编写一个`ConsumerApplication`启动类程序，运行测试配置

```
public class ConsumerApplication {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("consumer.xml");
        OrderService orderService = applicationContext.getBean(OrderService.class);

        //调用方法查询出数据
        orderService.initOrder("1");
        System.out.println("调用完成...");
	    System.in.read();
    }
}

```

查看监控信息

![image-20230208215654663](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082156706.png)

查看提供者

![image-20230208215720623](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082157680.png)

![image-20230208215734424](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082157477.png)

![image-20230208220644091](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082206141.png)





### 3.6 监控中心搭建

进入dubbo-monitor-simple文件，执行cmd命令，mvn package打包成jar包

检查配置文件

![image-20230208220838506](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082208549.png)

访问地址

```
localhost:8080 
```

![image-20230208221044508](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082210561.png)

修改consumer.xml，增加

```
    <!--dubbo-monitor-simple监控中心发现的配置-->
    <dubbo:monitor protocol="registry"></dubbo:monitor>
    <!--<dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor>-->

```

也修改provider.xml，增加

```
    <!--dubbo-monitor-simple监控中心发现的配置-->
    <dubbo:monitor protocol="registry"></dubbo:monitor>
    <!--<dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor>-->
```

监控中心也捕获到了服务提供和消费者信息

![image-20230208221748383](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082217433.png)

### 3.7 Dubbo与SpringBoot整合

创建**`boot-user-service-provider`**

![image-20230208224643231](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082246278.png)

```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		 <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.2.0</version>
        </dependency>

		<dependency>
			<groupId>com.jiang.gmall</groupId>
			<artifactId>gmall-interface</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>
	</dependencies>
```

把 `user-service-provider` 中的service拿到此项目中

![image-20230208225406407](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082254441.png)

配置 `application.properties`

```
dubbo.application.name=boot-user-service-provider
dubbo.registry.address=127.0.0.1:2181
dubbo.registry.protocol=zookeeper

dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

#连接监控中心
dubbo.monitor.protocol=registry

```

BootProviderApplication 启动类配置

```
@EnableDubbo //开启基于注解的dubbo功能
@SpringBootApplication
public class BootProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(BootProviderApplication.class, args);
    }
}


```

再创建**`boot-order-service-consumer`**

把order-service-consumer项目中的service复制到当前项目。

```
@Service
public class OrderServiceImpl implements OrderService {

    @Reference//引用远程提供者服务
    UserService userService;

    public List<UserAddress> initOrder(String userID) {
        //查询用户的收货地址
        List<UserAddress> userAddressList = userService.getUserAddressList(userID);

        System.out.println("当前接收到的userId=> "+userID);
        System.out.println("**********");
        System.out.println("查询到的所有地址为：");
        for (UserAddress userAddress : userAddressList) {
            //打印远程服务地址的信息
            System.out.println(userAddress.getUserAddress());
        }
        return userAddressList;
    }
}

```



```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		 <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.2.0</version>
        </dependency>
		<dependency>
			<groupId>com.jiang.gmall</groupId>
			<artifactId>gmall-interface</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>
	</dependencies>
```

![image-20230208225605925](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082256960.png)

创建 OrderController 控制器

```
@Controller
public class OrderController {
    @Autowired
    OrderService orderService;

    @RequestMapping("/initOrder")
    @ResponseBody
    public List<UserAddress> initOrder(@RequestParam("uid")String userId) {
        return orderService.initOrder(userId);
    }
}

```



创建application.properties 配置

```
server.port=8081
dubbo.application.name=boot-order-service-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181

#连接监控中心 注册中心协议
dubbo.monitor.protocol=registry

```

**BootConsumerApplication** 启动类创建

```
@EnableDubbo //开启基于注解的dubbo功能
@SpringBootApplication
public class BootConsumerApplication {
    public static void main(String[] args){
        SpringApplication.run(BootConsumerApplication.class,args);
    }
}

```

看面板

![image-20230208231404334](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082314393.png)

测试：

```
http://localhost:8081/initOrder?uid=1
```

![image-20230208231745364](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302082317400.png)

成功。

## 4 Dubbo配置介绍

### 4.1 配置概述

参考文档：

https://cn.dubbo.apache.org/zh/docs3-v2/java-sdk/reference-manual/config/overview/



### 4.2 配置原则

![image-20230209140805683](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091408818.png)

JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。
XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。
Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。

### 4.3启动检查

Dubbo 缺省会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止 Spring 初始化完成，以便上线时，能及早发现问题，默认 check=“true”。

参考文档：https://cn.dubbo.apache.org/zh/docs3-v2/java-sdk/advanced-features-and-usage/service/preflight-check/

关闭某个服务的启动时检查

```xml
<dubbo:reference interface="com.foo.BarService" check="false" />
```

关闭所有服务的启动时检查

```xml
<dubbo:consumer check="false" />
```

关闭注册中心启动时检查

```xml
<dubbo:registry check="false" />
```

通过 dubbo.properties

```properties
dubbo.reference.com.foo.BarService.check=false
dubbo.consumer.check=false
dubbo.registry.check=false
```

通过 -D 参数

```sh
java -Ddubbo.reference.com.foo.BarService.check=false
java -Ddubbo.consumer.check=false 
java -Ddubbo.registry.check=false
```



以`order-service-consumer`消费者为例，在consumer.xml中添加配置

可以通过 check=“false” 关闭检查

![image-20230209142526355](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091425408.png)

也可以用下面的配置，统一配置所有消费-配置当前消费者的统一规则,当前所有的服务都不启动时检查

```
<!--配置当前消费者的统一规则,当前所有的服务都不启动时检查-->
 <dubbo:consumer check="false"></dubbo:consumer>
```

![image-20230209142813195](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091428247.png)



### 4.4 全局超时配置

![image-20230209143521936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091435995.png)

```
全局超时配置
<dubbo:provider timeout="5000" />

指定接口以及特定方法超时配置
<dubbo:provider interface="com.foo.BarService" timeout="2000">
    <dubbo:method name="sayHello" timeout="3000" />
</dubbo:provider>

```

配置的覆盖规则：

1. 方法级优先，接口次之，全局配置再次之（精确有限）

2. 如果级别一样，消费方有限，提供方次之（消费优先）

   

![image-20230209143644553](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091436612.png)

### 4.5 重试次数

![image-20230209144243837](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091442890.png)

1 重试次数，不包含第一次

2 幂等（设置重试次数）【查询、修改、删除】、非幂等（不能设置重试次数）【数据库新增】

![image-20230209144702173](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091447218.png)

### 4.6 多版本

类似于灰度发布

重新创建一个服务类

![image-20230209145214787](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091452825.png)

![image-20230209145228171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091452218.png)

![image-20230209145238207](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091452255.png)

修改provider.xml

```
    <!--4、暴露服务 让别人调用 ref指向服务的真正实现对象-->
    <dubbo:service interface="com.jiang.gmall.service.UserService" ref="userServiceImpl01" version="1.0"></dubbo:service>
    <dubbo:service interface="com.jiang.gmall.service.UserService" ref="userServiceImpl02" version="2.0"></dubbo:service>

    <!--服务的实现-->
    <bean id="userServiceImpl01" class="com.jiang.gmall.service.impl.UserServiceImpl"></bean>
    <bean id="userServiceImpl02" class="com.jiang.gmall.service.impl.UserServiceImpl2"></bean>
```

然后修改消费方的配置consumer.xml

服务消费者调用时，可自由配置版本

```
 <dubbo:reference interface="com.jiang.gmall.service.UserService" id="userService" retries="0" version="1.0.0"></dubbo:reference>
```

![image-20230209145823128](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091458176.png)

测试，调用的是1.0.0版本

![image-20230209150144430](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091501475.png)

### 4.7 本地存根

远程服务后，客户端通常只剩下接口，而实现全在服务器端，但提供方有些时候想在客户端也执行部分逻辑。

![image-20230209150531035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091505091.png)

使用场景

做 ThreadLocal 缓存，提前验证参数，调用失败后伪造容错数据等等，此时就需要在 API 中带上 Stub，客户端生成 Proxy 实例，会把 Proxy 通过构造函数传给 Stub [1](https://cn.dubbo.apache.org/zh/docs3-v2/java-sdk/advanced-features-and-usage/service/local-stub/#fn:1)，然后把 Stub 暴露给用户，Stub 可以决定要不要去调 Proxy。

注意：

1. Stub 必须有可传入 Proxy 的构造函数。
2. 在 interface 旁边放一个 Stub 实现，它实现 BarService 接口，并有一个传入远程 BarService 实例的构造函数

修改order-service-consumer

增加一个类UserServiceStub

```
public class UserServiceStub implements UserService{
	private final UserService userService;
	
	/**
	 * 传入userService远程的代理对象
	 * @param userService
	 */
	public UserServiceStub(UserService userService) {
		super();
		this.userService = userService;
	}

	public List<UserAddress> getUserAddressList(String userId) {
	     System.out.println("UserServiceStub...........");
		if(!StringUtils.isEmpty(userId)){
			return userService.getUserAddressList(userId); 
		}
		
		return null;
	}

}
```

修改配置consumer.xml

```
   <dubbo:reference interface="com.jiang.gmall.service.UserService" id="userService" 
    stub="com.jiang.gmall.service.impl.UserServiceStub" version="1.0.0"></dubbo:reference>
```

![image-20230209153821216](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091538263.png)

运行方法

![image-20230209154006907](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091540949.png)



### 4.8 与springboot整合的三种方式

4.8.1 导入dubbo-starter。在application.properties配置属性，使用@Service【暴露服务】，使用@Reference【引用服务】

![image-20230209154658853](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091546896.png)

```
        <dependency>
            <groupId>com.alibaba.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>0.2.0</version>
        </dependency>
```

4.8.2保留Dubbo 相关的xml配置文件
导入dubbo-starter，使用@ImportResource导入Dubbo的xml配置文件

![image-20230209154538306](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091545351.png)

![image-20230209154614781](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091546837.png)



4.8.3 使用 注解API的方式
将每一个组件手动创建到容器中，让dubbo来扫描其他的组件，@EnableDubbo(scanBasePackges="包地址") @Service暴露

写一个配置类

![image-20230209163734947](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302091637001.png)

```
@Configuration
public class MyDubboConfig {
	// 当前服务名
	@Bean
	public ApplicationConfig applicationConfig() {
		ApplicationConfig applicationConfig = new ApplicationConfig();
		applicationConfig.setName("boot-user-service-provider");
		return applicationConfig;
	}

	// 注册中心
	@Bean
	public RegistryConfig registryConfig() {
		RegistryConfig registryConfig = new RegistryConfig();
		registryConfig.setProtocol("zookeeper");
		registryConfig.setAddress("127.0.0.1:2181");
		return registryConfig;
	}

	// 通信规则
	@Bean
	public ProtocolConfig protocolConfig() {
		ProtocolConfig protocolConfig = new ProtocolConfig();
		protocolConfig.setName("dubbo");
		protocolConfig.setPort(20882);
		return protocolConfig;
	}

	// 暴露服务
	@Bean
	public ServiceConfig<UserService> userServiceConfig(UserService userService) {
		ServiceConfig<UserService> serviceConfig = new ServiceConfig<UserService>();
		serviceConfig.setInterface(UserService.class);
		serviceConfig.setRef(userService);
		serviceConfig.setVersion("1.0.0");

		MethodConfig methodConfig = new MethodConfig();
		methodConfig.setName("getUserAddressList");
		methodConfig.setTimeout(1000);

		ArrayList<MethodConfig> methods = new ArrayList<MethodConfig>();
		serviceConfig.setMethods(methods);

		return serviceConfig;
	}

}
```

使用 @EnableDubbo 并规定扫描包路径

```
@EnableDubbo(scanBasePackages="com.jiang.gmall") //开启基于注解的dubbo功能
@SpringBootApplication
public class BootUserServiceProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(BootUserServiceProviderApplication.class, args);
	}

}
```

## 5 、Dubbo高可用

### 5.1 zookeeper宕机与dubbo直连

正常情况

![image-20230209214015876](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092140064.png)

模拟如果注册中心zookeeper挂了

![image-20230209214108220](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092141290.png)



注册中心全部宕机后，服务提供者和服务消费者仍能通过本地缓存通信

消费者绕过注册中心直连

原因：

监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
**注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯**
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
高可用：通过设计，减少系统不能提供服务的时间；

我们可以设置消费者绕过注册中心直连

```
   @Reference(url="127.0.0.1:20882")//引用远程提供者服务
   public UserService userService;
```

![image-20230209214728513](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092147563.png)



### 5.2 负载均衡

四种负载均衡策略

1）Random LoadBalance
随机，按权重设置随机概率。（基于权重的负载均衡机制）（默认）

![image-20230209214936036](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092149088.png)

随机，按权重设置随机概率。 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。



2）RoundRobin LoadBalance
轮循，按公约后的权重设置轮循比率。（基于权重的轮询负载均衡机制）

![image-20230209215035639](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092150692.png)

轮循，按公约后的权重设置轮循比率。 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。



3）LeastActive LoadBalance
最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。（最少活跃数的负载均衡机制）

![image-20230209215343993](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092153041.png)

4）ConsistentHash LoadBalance
一致性 Hash，相同参数的请求总是发到同一提供者。（一致性 Hash负载均衡机制）

![image-20230209215357498](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092153547.png)

一致性 Hash，相同参数的请求总是发到同一提供者。 

当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。算法参见：http://en.wikipedia.org/wiki/Consistent_hashing 

缺省只对第一个参数 Hash，

如果要修改，请配置 <dubbo:parameter key="hash.arguments" value="0,1" /> 缺省用 160 份虚拟节点，如果要修改，请配置 <dubbo:parameter key="hash.nodes" value="320" />


分别启动三个提供者，每个分别修改端口和打印的数字。

![image-20230209220346533](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092203592.png)

![image-20230209220409200](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092204250.png)

![image-20230209220511292](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092205338.png)

修改消费者

![image-20230209220555005](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092205054.png)

启动消费者

访问http://localhost:8081/initOrder?uid=1，发现是2号提供者提供服务，接下来

![image-20230209222153822](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092221864.png)

得出结论，默认是随机策略。

在消费者端，设置策略--替换轮询机制

```
   @Reference(loadbalance="roundrobin")//引用远程提供者服务
   public UserService userService;
```

![image-20230209222615849](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092226904.png)

可以在控制界面调整

![image-20230209222918170](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092229224.png)

![image-20230209223018743](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092230803.png)

![image-20230209223038751](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092230814.png)

分别把20881倍权，把20880降权

![image-20230209223234914](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092232975.png)

对比

![image-20230209223344660](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092233701.png)

![image-20230209223406765](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092234806.png)



### 5.3 服务降级

服务降级是指服务在非正常情况下进行降级应急处理。

**当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证核心交易正常运作或高效运作。**

#### 5.3.1 屏蔽 （直接返回为空）

mock=force:return+null 表示消费方对该服务的方法调用都直接返回 null 值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响。



#### 5.3.2 容错 （调用失败后返回为空）

改为 mock=fail:return+null 表示消费方对该服务的方法调用在失败后，再返回 null 值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。



#### 5.3.3  dubbo-admin控制规则

启动 dubbo-admin，在服务 Mock-> 规则配置菜单下设置 Mock 规则

屏蔽

![image-20230209224304641](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092243703.png)

屏蔽之后，消费者无法访问。http://localhost:8081/initOrder?uid=1

![image-20230209224529916](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092245958.png)

可以点击恢复

![image-20230209224554640](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092245692.png)

容错

![image-20230209224351739](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092243803.png)

修改boot-user-service-provider

![image-20230209224926376](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092249426.png)

修改boot-order-service-consumer

![image-20230209224912613](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092249662.png)

点击规则的容错

![image-20230209225047832](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092250890.png)

![image-20230209225124972](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092251023.png)



### 5.4 服务容错

Dubbo 提供了多种容错方案，缺省为 failover 重试。

![image-20230209225427282](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092254336.png)

Failfast Cluster[ ](https://cn.dubbo.apache.org/zh-cn/docs3-v2/java-sdk/advanced-features-and-usage/service/fault-tolerent-strategy/#failfast-cluster)

快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

Failsafe Cluster

失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

Failback Cluster

失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

Forking Cluster

并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 `forks="2"` 来设置最大并行数。

Broadcast Cluster

广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

**集群模式配置**
按照以下示例在服务提供方和消费方配置集群模式
<dubbo:service cluster=“failsafe” />
或
<dubbo:reference cluster=“failsafe” />

整合hystrix

1 配置spring-cloud-starter-netflix-hystrix （消费者和提供者）

```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
			<version>1.4.4.RELEASE</version>
		</dependency>
```

2 在Application类上增加@EnableHystrix来启用hystrix starter（消费者和提供者）

![image-20230209230036539](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092300597.png)

3 在Dubbo的Provider上增加@HystrixCommand配置，这样子调用就会经过Hystrix代理。

```
@Service//dubbo的服务暴露
@Component
public class UserServiceImpl implements UserService{

	@HystrixCommand
	public List<UserAddress> getUserAddressList(String userId) {
		System.out.println("UserServiceImpl...3...");
		UserAddress address1 = new UserAddress(1, "苏州市吴中区金鸡湖大厦2F", "1", "米尼", "150360313x", "Y");
		UserAddress address2 = new UserAddress(2, "北京市昌平区沙河镇沙阳路", "1", "米奇", "17123666395x", "N");

//		try {
//			Thread.sleep(2000);
//		} catch (InterruptedException e) {
//			// TODO Auto-generated catch block
//			e.printStackTrace();
//		}
		if(Math.random()>0.5){
			throw new RuntimeException("");
		}
		
		return Arrays.asList(address1,address2);

	}

}
```

![image-20230209230326227](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092303281.png)

4 对于Consumer端，则可以增加一层method调用，并在method上配置@HystrixCommand。当调用出错时，会走到fallbackMethod = "reliable"的调用里

```
Service
public class OrderServiceImpl implements OrderService {

	@Reference(timeout = 1000) // 引用远程提供者服务
	public UserService userService;

	@HystrixCommand(fallbackMethod = "reliable")
	public List<UserAddress> initOrder(String userID) {
		// 查询用户的收货地址
		List<UserAddress> userAddressList = userService.getUserAddressList(userID);

		System.out.println("当前接收到的userId=> " + userID);
		System.out.println("**********");
		System.out.println("查询到的所有地址为：");
		for (UserAddress userAddress : userAddressList) {
			// 打印远程服务地址的信息
			System.out.println(userAddress.getUserAddress());
		}
		return userAddressList;

	}
	
	 public String reliable(String name) {
     return "hystrix fallback value";
 }

}

```

![image-20230209230539064](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302092305122.png)





## 7、RPC原理

一次完整的RPC调用流程（同步）：

服务消费方（client）调用以本地调用方式调用服务；
client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
client stub找到服务地址，并将消息发送到服务端；
server stub收到消息后进行解码；
server stub根据解码结果调用本地的服务；
本地服务执行并将结果返回给server stub；
server stub将返回结果打包成消息并发送至消费方；
client stub接收到消息，并进行解码；
服务消费方得到最终结果。

dubbo只用了两步1和8，中间的过程是透明的看不到的。RPC框架的目标就是要2~8这些步骤都封装起来，这些细节对用户来说是透明的，不可见的。

## 8、Netty通信原理

Netty是一个异步事件驱动的网络应用程序框架， 用于快速开发可维护的高性能协议服务器和客户端。

它极大地简化并简化了TCP和UDP套接字服务器等网络编程。

![image-20230210134447168](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101344366.png)

Netty 的非阻塞 I/O 的实现关键是基于 I/O 复用模型，这里用 Selector 对象表示：

![image-20230210134518166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101345229.png)

Netty 的 IO 线程 NioEventLoop 由于聚合了多路复用器 Selector，可以同时并发处理成百上千个客户端连接。

当线程从某客户端 Socket 通道进行读写数据时，若没有数据可用时，该线程可以进行其他任务。

线程通常将非阻塞 IO 的空闲时间用于在其他通道上执行 IO 操作，所以单独的线程可以管理多个输入和输出通道。

由于读写操作都是非阻塞的，这就可以充分提升 IO 线程的运行效率，避免由于频繁 I/O 阻塞导致的线程挂起。





## 9、Dubbo原理

### 9.1 框架设计

![image-20230210134717774](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101347894.png)

图例说明：

- 图中左边淡蓝背景的为服务消费方使用的接口，右边淡绿色背景的为服务提供方使用的接口，位于中轴线上的为双方都用到的接口。
- 图中从下至上分为十层，各层均为单向依赖，右边的黑色箭头代表层之间的依赖关系，每一层都可以剥离上层被复用，其中，Service 和 Config 层为 API，其它各层均为 SPI。
- 图中绿色小块的为扩展接口，蓝色小块为实现类，图中只显示用于关联各层的实现类。
- 图中蓝色虚线为初始化过程，即启动时组装链，红色实线为方法调用过程，即运行时调用链，紫色三角箭头为继承，可以把子类看作父类的同一个节点，线上的文字为调用的方法。

各层说明

- **Config 配置层**：对外配置接口，以 `ServiceConfig`, `ReferenceConfig` 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
- **Proxy 服务代理层**：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 `ServiceProxy` 为中心，扩展接口为 `ProxyFactory`
- **Registry 注册中心层**：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 `RegistryFactory`, `Registry`, `RegistryService`
- **Cluster 路由层**：封装多个提供者的路由及负载均衡，并桥接注册中心，以 `Invoker` 为中心，扩展接口为 `Cluster`, `Directory`, `Router`, `LoadBalance`
- **Monitor 监控层**：RPC 调用次数和调用时间监控，以 `Statistics` 为中心，扩展接口为 `MonitorFactory`, `Monitor`, `MonitorService`
- **Protocol 远程调用层**：封装 RPC 调用，以 `Invocation`, `Result` 为中心，扩展接口为 `Protocol`, `Invoker`, `Exporter`
- **Exchange 信息交换层**：封装请求响应模式，同步转异步，以 `Request`, `Response` 为中心，扩展接口为 `Exchanger`, `ExchangeChannel`, `ExchangeClient`, `ExchangeServer`
- **Transport 网络传输层**：抽象 mina 和 netty 为统一接口，以 `Message` 为中心，扩展接口为 `Channel`, `Transporter`, `Client`, `Server`, `Codec`
- **Serialize 数据序列化层**：可复用的一些工具，扩展接口为 `Serialization`, `ObjectInput`, `ObjectOutput`, `ThreadPool`





### 9.2 启动标签解析

spring解析配置文件的入口--BeanDefinitionParser 

![image-20230210140724484](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101407541.png)

![image-20230210140815395](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101408445.png)

打开它的继承树

![image-20230210141112667](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101411725.png)

![image-20230210141144401](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101411461.png)

![image-20230210141241399](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101412471.png)

![image-20230210141314282](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101413335.png)

![image-20230210141559336](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101415373.png)

根据构造器，找到这个类的调用者---DubboNamespaceHandler

![image-20230210142203684](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101422739.png)

例如DubboBeanDefinitionParser ,它对应的就是service这个关键字标签

![image-20230210142353932](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101423985.png)

![image-20230210142301623](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101423676.png)

![image-20230210142440240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101424301.png)



### 9.3 dubbo原理 -服务暴露

![image-20230210143038619](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101430704.png)

![image-20230210143818050](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101438097.png)

ServiceBean2个实现方法![image-20230210143923088](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101439138.png)

1 afterPropertiesSet方法

![image-20230210144043088](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101440134.png)

![image-20230210144100240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101441291.png)

2 ContextRefreshedEvent

![image-20230210144224760](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101442804.png)

这个export方法暴露服务--->ServiceConfig

![image-20230210144342082](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101443139.png)

![image-20230210144425860](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101444906.png)

doExportUrlsFor1Protocol方法里有个proxyFactory

![image-20230210144712780](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101447843.png)

![image-20230210144929282](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101449332.png)

![image-20230210145009512](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101450555.png)

进入类----DubboProtocol

![image-20230210145850306](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101458366.png)

调用方法----createServer(url)

![image-20230210150017171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101500217.png)

![image-20230210152958954](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101529006.png)

走到这个步骤，启动NETTY，监听20880端口

RegistryProtocol继续执行export

![image-20230210153555293](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101535349.png)

```
# 注册提供方
ProviderConsumerRegTable.registerProvider(originInvoker, registryUrl, registedProviderUrl);
```

![image-20230210153726303](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101537358.png)

维护了提供方和消费方的关联关系

![image-20230210153815957](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101538009.png)



### 9.4 dubbo原理 -服务引用

![image-20230210154041026](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101540088.png)

分析消费者---order-service-consumer

![image-20230210154406704](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101544759.png)

分析ReferenceBean，这是一个工厂bean

![image-20230210154522915](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101545959.png)



![image-20230210154608170](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101546213.png)

init方法里，有一个createProxy 创建代理对象

![image-20230210154804080](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101548132.png)

createProxy 方法里有个远程引用方法，

![image-20230210154915222](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101549278.png)



进入注册服务

![image-20230210155242113](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101552166.png)



![image-20230210155430580](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101554642.png)

发起订阅，进入DubboProtocol

![image-20230210155612047](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101556090.png)

![image-20230210155705731](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101557785.png)

调用getSharedClient(url);

![image-20230210155738234](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101557285.png)

![image-20230210155829393](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101558444.png)

调用Exchangers.connect(url, requestHandler)

![image-20230210155915759](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101559824.png)

创建NETTY的客户端，去连接。

完成订阅后，就返回

![image-20230210160109125](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101601189.png)

invoker封装了调用的地址等信息

然后把信息注册

```
 ProviderConsumerRegTable.registerConsumer(invoker, url, subscribeUrl, directory);
```



### 9.5 dubbo原理 -服务调用

![image-20230210154102819](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302101541900.png)

在 Dubbo 的核心领域模型中：

- Protocol 是服务域，它是 Invoker 暴露和引用的主功能入口，它负责 Invoker 的生命周期管理。
- Invoker 是实体域，它是 Dubbo 的核心模型，其它模型都向它靠拢，或转换成它，它代表一个可执行体，可向它发起 invoke 调用，它有可能是一个本地的实现，也可能是一个远程的实现，也可能一个集群实现。
- Invocation 是会话域，它持有调用过程中的变量，比如方法名，参数等。



