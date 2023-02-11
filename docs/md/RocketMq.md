# RocketMq

## 1 概述

### 1.1 MQ概述

MQ，Message Queue，是一种提供消息队列服务的中间件，也称为消息中间件，是一套提供了消息生产、存储、消费全过程API的软件系统。消息即数据。一般消息的体量不会很大。

### 1.2 MQ用途

限流削峰

![image-20230211110226124](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111102249.png)

异步解耦

![image-20230211110254737](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111102773.png)

数据收集

分布式系统会产生海量级数据流，如：业务日志、监控数据、用户行为等。针对这些数据流进行实时或批量采集汇总，然后对这些数据流进行大数据分析，这是当前互联网平台的必备技术。通过MQ完成此类数据收集是最好的选择。

### 1.3 MQ常见协议

JMS
JMS，Java Messaging Service（Java消息服务）。是Java平台上有关MOM（Message OrientedMiddleware，面向消息的中间件 PO/OO/AO）的技术规范，它便于消息系统中的Java应用程序进行消息交换，并且通过提供标准的产生、发送、接收消息的接口，简化企业应用的开发。ActiveMQ是该协议的典型实现。

STOMP
STOMP，Streaming Text Orientated Message Protocol（面向流文本的消息协议），是一种MOM设计的简单文本协议。STOMP提供一个可互操作的连接格式，允许客户端与任意STOMP消息代理（Broker）进行交互。ActiveMQ是该协议的典型实现，RabbitMQ通过插件可以支持该协议。

AMQP
AMQP，Advanced Message Queuing Protocol（高级消息队列协议），一个提供统一消息服务的应用层标准，是应用层协议的一个开放标准，是一种MOM设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言等条件的限制。 RabbitMQ是该协议的典型实现。

MQTT
MQTT，Message Queuing Telemetry Transport（消息队列遥测传输），是IBM开发的一个即时通讯协议，是一种二进制协议，主要用于服务器和低功耗IoT（物联网）设备间的通信。该协议支持所有平台，几乎可以把所有联网物品和外部连接起来，被用来当做传感器和致动器的通信协议。 RabbitMQ通过插件可以支持该协议。



## 2 RocketMQ基本概念

官网访问：https://rocketmq.apache.org/

领域模型

![image-20230211112231720](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111122765.png)

发布-订阅（Pub/Sub）是一种消息范式，消息的发送者（称为发布者、生产者、Producer）会将消息直接发送给特定的接收者（称为订阅者、消费者、Comsumer）。而RocketMQ的基础消息模型就是一个简单的Pub/Sub模型。

![image-20230211112814035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111128076.png)

在**基于主题**的系统中，消息被发布到主题或命名通道上。消费者将收到其订阅主题上的所有消息，生产者负责定义订阅者所订阅的消息类别。这是一个基础的概念模型，而在实际的应用中，结构会更复杂。例如为了支持高并发和水平扩展，中间的消息主题需要进行分区，同一个Topic会有多个生产者，同一个信息会有多个消费者，消费者之间要进行负载均衡等。

![image-20230211112844889](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111128935.png)



### 2.1 消息（Message）

消息是指，消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题

在RocketMQ中消息具有以下两个特征：

- 消息不可变性消息本质上是已经产生并确定的事件，一旦产生后，消息的内容不会发生改变。即使经过传输链路的控制也不会发生变化，消费端获取的消息都是只读消息视图。

- 消息持久化Apache RocketMQ 会默认对消息进行持久化，即将接收到的消息存储到 Apache RocketMQ 服务端的存储文件中，保证消息的可回溯性和系统故障场景下的可恢复性。
  

### 2.2 主题（Topic）

Topic表示一类消息的集合，每个主题包含若干条消息，每条消息只属于一个主题，是RocketMQ进行消费订阅的基本单位。
一个生产者可以同时发送多种Topic的消息；而一个消费者只对某种特定的Topic感兴趣，即只可以订阅和消费一种Topic的消息

### 2.3 标签（tag）

为消息设置的标签，用与同一主题下区分不同类型的消息。来自一业务单元的消息，可以根据不同的业务目的在同一主题下设置不同的标签。标签能够有效保持代码的清晰度和连贯性，并优化RocketMQ提供的查询系统。消息者可以根据Tag实现对不同子主题的不同消费逻辑，实现更好的扩展性。

```
Topic是消息的一级分类，Tag是消息的二级分类
```

### 2.4 队列（queue）

储存消息的物理实体，一个Topic中包含多个Queue，每个Queue中存放的就是该Topic的消息，一个Topic的Queue也称为一个Topic中消息的分区。

一个分区只能被一个消费者组里的一个消费者消费

一个Topic的Queue中的消息只能被一个消费者组中的一个消费者消费。一个Queue中的消息不允许同一个消费者组中的多个消费者同时消费

**分片(sharding)**
 分片不同于分区。在RocketMQ中，分片指的是存放相应Topic的Broker。每个分片中会创建出相应数量的分区，即Queue，每个Queue的大小是相同的。

![image-20230211113118425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302111131487.png)



### 2.5 消息标识

RocketMQ中每个消息拥有唯一的MessageId，可以携带具有业务标识的key，以方便对消息的查询.

不过需要注意的是，MessageId有两个：在生产者send（）消息时会自动生成MessageId（msgId），当消费者到达Broker后，Broker也会生成一个MessageId(offsetMsgId）。msgId，offsetMsgId与key都称为消息标识。

- msgId：右producer端生成，规则为：producerIp+进程pid+MessageClientIDSetter类的ClassLoader的hashCode+当前时间+AutomicInteger自增计数器

- offsetMsgId：右broker端生成，其规则为：brokerIp+物理分区的offset

- key：由用户指定的业务相关的唯一标识

## 3、系统架构















## 4 安装

### 4.1 windows本地安装

注意：要先安装JDK。**unzip解压缩rocketmq到指定位置**

配置环境变量

如果是Windows进入bin 目录

打开runserver.cmd,把Xms、Xmx、-Xmn都设置成512m



**同理设置 runbroker**

启动nameserver 

鼠标双击执行bin目录下的mqnamesrv.cmd 文件 或者 cmd 打开控制台，切换目录到rocket的bin目录下，执行命令：start mqnamesrv.cmd

启动broker  - 执行bin目录下的 mqbroker.cmd 文件 或者 cmd 打开控制台，切换目录到rocket的bin目录下，执行命令：start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true（默认端口是9876 autoCreateTopicEnable=true表示允许自动创建topic）

RocketMQ控制台安装

下载地址源码
 [https://github.com/apache/rocketmq-externals/tags](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fapache%2Frocketmq-externals%2Ftags)
 下载 rocketmq-externals-rocketmq-console-1.0.0 这个压缩包就行
 解压压缩包备用

改配置文件application.properties

打JAR

执行命令
mvn clean package -Dmaven.test.skip=true （跳过测试文件）

启动jar包
java -jar jar包路径

web访问地址：http://127.0.0.1:8088/#/