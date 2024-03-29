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



### 1.4 常见的MQ产品

- ActiveMQ
  - ActiveMQ是使用Java语言开发一款MQ产品。早期很多公司与项目中都在使用。但现在的社区活跃度已经很低。现在的项目中已经很少使用了。
- RabbitMQ
  - RabbitMQ是使用ErLang语言开发的一款MQ产品。其吞吐量较Kafka与RocketMQ要低，且由于其不是Java语言开发，所以公司内部对其实现定制化开发难度较大。
- Kafka
  - Kafka是使用Scala/Java语言开发的一款MQ产品。其最大的特点就是高吞吐率，常用于大数据领域的实时计算、日志采集等场景。其没有遵循任何常见的MQ协议，而是使用自研协议。对于Spring CloudNetçix，其仅支持RabbitMQ与Kafka。
- RocketMQ
  - RocketMQ是使用Java语言开发的一款MQ产品。经过数年阿里双 11 的考验，性能与稳定性非常高。其没有遵循任何常见的MQ协议，而是使用自研协议。对于Spring Cloud Alibaba，其支持RabbitMQ、Kafka，但提倡使用RocketMQ。



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

![image-20230211142415123](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211142415123.png)

![image-20230211144101861](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211144101861.png)

消息生产者比喻成寄件人，消息消费者对应的是收件人，中间 Broker相当于邮递员，上面相当于邮局管理邮递员，需要从邮局获取邮递员具体信息，NameServer 相当于邮递员的邮箱，topic 相当于是地区的划分比如北京的、山东邮件地址不一样，每个 massage queue 相当于是一封封的邮件，在 message 里面还有更加详细的概念叫message，是具体的消息对象可以认为是邮件里面的文字内容。

![image-20230211144158725](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211144158725.png)

```
如上图所示，整体可以分成4个角色，分别是:NameServer，Broker，Producer，Consumer.
（1）Broker（邮递员）
Broker 是 RocketMQ 的核心，负责消息的接收，存储，投递等功能
（2）NameServer（邮局）
消息队列的协调者，Broker 向它注册路由信息，同时 Producer 和Consumer 向其获取路由信息
（3）Producer（寄件人）
消息的生产者，需要从 NameServer 获取 Broker 信息，然后与Broker 建立连接，向 Broker 发送消息
（4）Consumer（收件人）
消息的消费者，需要从 NameServer 获取 Broker 信息，然后与Broker 建立连接，从 Broker 获取消息
（5）Topic（地区）
用来区分不同类型的消息，发送和接收消息前都需要先创建 Topic，针对 Topic 来发送和接收消息
（6）Message Queue（邮件）
为了提高性能和吞吐量，引入了 MessageQueue，一个 Topic 可以设置一个或多个 Message Queue，这样消息就可以并行往各个Message Queue 发送消息，消费者也可以并行的从多个MessageQueue 读取消息
（7）Message（具体文字）
Message 是消息的载体。
（8）Producer Group
生产者组，简单来说就是多个发送同一类消息的生产者称之为一个生产者组。
（9）Consumer Group
消费者组，和生产者类似，消费同一类消息的多个 consumer 实例组成一个消费者组。
```



### 3.1 生产者（producer)

发布消息的角色。Producer通过 MQ 的负载均衡模块选择相应的 Broker 集群队列进行消息投递，投递的过程支持快速失败和重试。

```
例如，业务系统产生的日志写入到MQ的过程，就是消息生产的过程
再如，电商平台中用户提交的秒杀请求写入到MQ的过程，就是消息生产的过程
```

RockerMQ中的消息生产者都是以生产者组（producer Group）的形式出现的。生产者组是同一类生产者的集合，这类Producer发送相同数量和种类Topic类型的信息，但是，一个生产者组可以同时发送多个主题的消息，此时生产者组中的每个生产者都生产多个相同类型的主题消息。


### 3.2 消费者（consumer)

消息消费者，负责消费消息，一个消息消费者会从broker服务器中获取到消息，并对消息进行相关业务处理。

```
例如，Qos系统从MQ中读取消息，并对日志进行解析处理的过程就是消息消费的过程
再入，电商平台的业务系统从MQ中读取到秒杀请求，并对请求进行处理的过程就是消息消费的过程
```

RockerMQ中消息消费者都是以消费者组（consumer Group）的形式出现的，

消息系统的重要作用之一是削峰填谷，但比如在电商大促的场景中，如果下游的消费者消费能力不足的话，大量的瞬时流量进入会后堆积在服务端。此时，消息的端到端延迟（从发送到被消费的时间）就会增加，对服务端而言，一直消费历史数据也会产生冷读。因此需要增加消费能力来解决这个问题，除了去优化消息消费的时间，最简单的方式就是扩容消费者。

但是否随意增加消费者就能提升消费能力？ 首先需要了解消费组的概念。在消费者中消费组的有非常重要的作用，如果多个消费者设置了相同的Consumer Group，我们认为这些消费者在同一个消费组内。



消费者组是同一类消费者的集合，这类consumer消费者是同一Topic类型的消息。

消费者组使得在消息消费方面，实现负载均衡(将一个Topic中的Queue平均分给一个Consumer Group中的Consumer）和容错（一个Comsumer挂了，该Consumer Group中的其他Consumer可以接着消费原Consumer中的Queue）的目标变得非常容易。

消费者组中Consumer的数量应该小于或等于一个Topic中Queue的数量，如果大于，多余的Consumer将不再消费消息。

```
注意：
消费者组只能一个Topic的消息，不能消费多个Topic的消息。
一个消费者组中的消费者只能订阅完全相同的topic。
一个Topic类型的消息可以被多个消费者组同时消费。
但是一个Queue不能被同一消费者组多个消费者消费，一个消费者可以消费多个Queue。
```

### 3.3 命名服务中心（NameServer）

NameServer是一个简单的 Topic 路由注册中心，支持 Topic、Broker 的动态注册与发现。

NameServer通常会有多个实例部署，各实例间相互不进行信息通讯。Broker是向每一台NameServer注册自己的路由信息，所以每一个NameServer实例上面都保存一份完整的路由信息。当某个NameServer因某种原因下线了，客户端仍然可以向其它NameServer获取路由信息。

主要包括两个功能：

- **Broker管理**，NameServer接受Broker集群的注册信息并且保存下来作为路由信息的基本数据。然后提供心跳检测机制，检查Broker是否还存活；
- **路由信息管理**，每个NameServer将保存关于 Broker 集群的整个路由信息和用于客户端查询的队列信息。Producer和Consumer通过NameServer就可以知道整个Broker集群的路由信息，从而进行消息的投递和消费。



#### 3.3.1 路由注册

NameServer通常也是以集群的方式部署，不过，NameServer是无状态的，即NameServer集群中的各个节点间是无差异的，各节点间相互不进行信息通讯。那各节点中的数据是如何进行数据同步的呢？在Broker节点启动时，轮询NameServer列表，与每个NameServer节点建立长连接，发起注册请求。在NameServer内部维护着一个Broker列表，用来动态存储Broker的信息。

```
注意，这是与其它像zk、Eureka、Nacos等注册中心不同的地方。
这种NameServer的无状态方式，有什么优缺点：
优点：NameServer集群搭建简单，扩容简单。
缺点：对于Broker，必须明确指出所有NameServer地址。否则未指出的将不会去注册。也正因为如此，NameServer并不能随便扩容。因为，若Broker不重新配置，新增的NameServer对于Broker来说是不可见的，其不会向这个NameServer进行注册。
```

#### 3.3.2 路由剔除

由于Broker关机、宕机或网络抖动等原因，NameServer没有收到Broker的心跳，NameServer可能会将其从Broker列表中剔除。

NameServer中有一个定时任务，每隔 10 秒就会扫描一次Broker表，查看每一个Broker的最新心跳时间戳距离当前时间是否超过 120 秒，如果超过，则会判定Broker失效，然后将其从Broker列表中剔除。

```
扩展：对于RocketMQ日常运维工作，例如Broker升级，需要停掉Broker的工作。OP需要怎么做？
OP需要将Broker的读写权限禁掉。一旦client(Consumer或Producer)向broker发送请求，都会收到broker的NO_PERMISSION响应，然后client会进行对其它Broker的重试。
当OP观察到这个Broker没有流量后，再关闭它，实现Broker从NameServer的移除。
OP：运维工程师
SRE：Site Reliability Engineer，现场可靠性工程师
```

#### 3.3.3. 路由发现

推送给客户端，而是客户端定时拉取主题最新的路由。默认客户端每 30 秒会拉取一次最新的路由。

```
扩展：
1 ）Push模型：推送模型。其实时性较好，是一个“发布-订阅”模型，需要维护一个长连接。而长连接的维护是需要资源成本的。该模型适合于的场景：
* 实时性要求较高
* Client数量不多，Server数据变化较频繁
2 ）Pull模型：拉取模型。存在的问题是，实时性较差。
3 ）Long Polling模型：长轮询模型。其是对Push与Pull模型的整合，充分利用了这两种模型的优势，屏蔽了它们的劣势。
```

#### 3.3.4 客户端NameServer选择策略

这里的客户端指的是Producer与Consumer

客户端在配置时必须要写上NameServer集群的地址，那么客户端到底连接的是哪个NameServer节点呢？客户端首先会生产一个随机数，然后再与NameServer节点数量取模，此时得到的就是所要连接的节点索引，然后就会进行连接。如果连接失败，则会采用round-robin策略，逐个尝试着去连接其它节点。

首先采用的是`随机策略`进行的选择，失败后采用的是`轮询策略`

```
扩展：Zookeeper Client是如何选择Zookeeper Server的？
简单来说就是，经过两次Shufæe，然后选择第一台Zookeeper Server。
详细说就是，将配置文件中的zk server地址进行第一次shufæe，然后随机选择一个。这个选择出的一般都是一个hostname。然后获取到该hostname对应的所有ip，再对这些ip进行第二次shufæe，从shufæe过的结果中取第一个server地址进行连接。
```



### 3.4 代理服务器 Broker

Broker主要负责消息的存储、投递和查询以及服务高可用保证。

在 Master-Slave 架构中，Broker 分为 Master 与 Slave。一个Master可以对应多个Slave，但是一个Slave只能对应一个Master。Master 与 Slave 的对应关系通过指定相同的BrokerName，不同的BrokerId 来定义，BrokerId为0表示Master，非0表示Slave。Master也可以部署多个。

小结：

- 每个 **Broker** 与 **NameServer** 集群中的所有节点建立长连接，定时注册 Topic 信息到所有 NameServer。
- **Producer** 与 **NameServer** 集群中的其中一个节点建立长连接，定期从 NameServer 获取Topic路由信息，并向提供 Topic 服务的 Master 建立长连接，且定时向 Master 发送心跳。Producer 完全无状态。
- **Consumer** 与 **NameServer** 集群中的其中一个节点建立长连接，定期从 NameServer 获取 Topic 路由信息，并向提供 Topic 服务的 Master、Slave 建立长连接，且定时向 Master、Slave发送心跳。Consumer 既可以从 Master 订阅消息，也可以从Slave订阅消息。

## 4 RocketMQ工作流程

### 4.1 启动NameServer

启动NameServer。NameServer启动后监听端口，等待Broker、Producer、Consumer连接，相当于一个路由控制中心。

### 4.2  启动 Broker

启动 Broker。与所有 NameServer 保持长连接，定时发送心跳包。心跳包中包含当前 Broker 信息以及存储所有 Topic 信息。注册成功后，NameServer 集群中就有 Topic跟Broker 的映射关系。

### 4.3 创建 Topic

创建 Topic 时需要指定该 Topic 要存储在哪些 Broker 上，也可以在发送消息时自动创建Topic。

手动创建Topic时，有两种模式：

- 集群模式：该模式下创建的Topic在该集群中，所有Broker中的Queue数量是相同的。
- Broker模式：该模式下创建的Topic在该集群中，每个Broker中的Queue数量可以不同。

自动创建Topic时，默认采用的是Broker模式，会为每个Broker默认创建 4 个Queue。



### 4.4 生产者发送消息

生产者发送消息。启动时先跟 NameServer 集群中的其中一台建立长连接，并从 NameServer 中获取当前发送的 Topic存在于哪些 Broker 上，轮询从队列列表中选择一个队列，然后与队列所在的 Broker建立长连接从而向 Broker发消息。

### 4.5 消费者接受消息

消费者接受消息。跟其中一台NameServer建立长连接，获取当前订阅Topic存在哪些Broker上，然后直接跟Broker建立连接通道，然后开始消费消息。

### 4.6 读写队列

从物理上来讲，读/写队列是同一个队列。所以，不存在读/写队列数据同步问题。读/写队列是逻辑上进行区分的概念。一般情况下，读/写队列数量是相同的。

例如，创建Topic时设置的写队列数量为 8 ，读队列数量为 4 ，此时系统会创建 8 个Queue，分别是0 1 2 3 4 5 6 7。Producer会将消息写入到这 8 个队列，但Consumer只会消费0 1 2 3这 4 个队列中的消息，4 5 6 7 中的消息是不会被消费到的。

再如，创建Topic时设置的写队列数量为 4 ，读队列数量为 8 ，此时系统会创建 8 个Queue，分别是0 1 2 3 4 5 6 7。Producer会将消息写入到0 1 2 3 这 4 个队列，但Consumer只会消费0 1 2 3 4 5 6 7这 8 个队列中的消息，但是4 5 6 7中是没有消息的。此时假设Consumer Group中包含两个Consumer，Consumer1消费0 1 2 3，而Consumer2消费4 5 6 7。但实际情况是，Consumer2是没有消息可消费的。

也就是说，当读/写队列数量设置不同时，总是有问题的。那么，为什么要这样设计呢？

其这样设计的目的是为了，方便Topic的Queue的缩容。

例如，原来创建的Topic中包含 16 个Queue，如何能够使其Queue缩容为 8 个，还不会丢失消息？可以动态修改写队列数量为 8 ，读队列数量不变。此时新的消息只能写入到前 8 个队列，而消费都消费的却是16 个队列中的数据。当发现后 8 个Queue中的消息消费完毕后，就可以再将读队列数量动态设置为 8 。整个缩容过程，没有丢失任何消息。

perm用于设置对当前创建Topic的操作权限： 2 表示只写， 4 表示只读， 6 表示读写。



## 5 安装

### 5.1 windows本地安装

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

### 5.2 linux单机安装

下载 Rocketmq安装包，上传到服务器

安装JDK1.8

```
cd /home/jiang/jdk
rpm -Uvh --force --nodeps *.rpm
```

解压缩

```
unzip rocketmq-all-4.9.0-bin-release.zip
mkdir /usr/loca/rocketmq
mv rocketmq-all-4.9.0-bin-release /usr/local/rocketmq/

cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release
```

![image-20230211153908830](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211153908830.png)

修改内存大小

修改runbroker.sh

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/bin
vi runbroker.sh
```

修改

```
# 原来的配置如下
JAVA_OPT="${JAVA_OPT} -server -Xms8g -Xmx8g -Xmn4g"

# 修改后
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"

```

编辑 runserver.sh 文件

```
vi runserver.sh
```

```
#原来的
JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"

# 改后
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
```

```
#  设置 tools.sh 中此项配置
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn256m -XX:PermSize=128m -XX:MaxPermSize=128m"
```



### 5.3 单机启动

#### 5.3.1 启动

在 bin 目录下，执行下面命令

启动nameserver

```
cd /bin
nohup sh mqnamesrv &
tail -f ~/logs/rocketmqlogs/namesrv.log

```

![image-20230211163536394](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211163536394.png)

可以用命令检查

```
jps
```

![image-20230211163640459](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211163640459.png)

启动 broker

```
nohup sh mqbroker -n localhost:9876 &
tail -f ~/logs/rocketmqlogs/broker.log

```

![image-20230211164742384](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211164742384.png)



#### 5.3.2 测试发送和接受消息

发送消息

```
# 设置环境变量
export NAMESRV_ADDR=localhost:9876
# 测试批量创建消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
```

![image-20230211180153280](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211180153280.png)

接受消息

```
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```



#### 5.3.3 关闭server

无论是关闭name server还是broker，都是使用mqshutdown命令

```
cd /bin

#先停止broker
sh mqshutdown broker
sh mqshutdown namesrv
```

![image-20230211180717369](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211180717369.png)

### 5.4 RocketMQ-admin的安装

#### 5.4.1 下载

下载地址：https://github.com/apache/rocketmq-externals/releases

https://github.com/apache/rocketmq-externals/archive/refs/tags/rocketmq-console-1.0.0.zip

![image-20230211165201533](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211165201533.png)

#### 5.4.2 修改配置

修改其src/main/resources中的application.properties配置文件。

- 原来的端口号为 8080 ，修改为一个不常用的8081
- 指定RocketMQ的name server地址

![image-20230211174647574](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211174647574.png)

#### 5.4.3 修改依赖

在解压目录rocketmq-console的pom.xml中添加如下JAXB依赖。

```
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>

```



#### 5.4.4 打包

在rocketmq-console目录下运行maven的打包命令

```
mvn clean package -Dmaven.test.skip=true
```

![image-20230211173535974](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211173535974.png)

![image-20230211173620632](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211173620632.png)



#### 5.4.5 启动和访问

运行JAR

```
# 命令格式nohup java -jar jar包名 &

cd D:\dev\rocketmq-console
java -jar rocketmq-console-ng-1.0.0.jar &
```

访问地址

```
http://localhost:8081
```

![image-20230211174928795](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230211174928795.png)

### 5.5 RocketMQ-Dashboard

`RocketMQ Dashboard` 是 RocketMQ 的管控利器，为用户提供客户端和应用程序的各种事件、性能的统计信息，支持以可视化工具代替 Topic 配置、Broker 管理等命令行操作。

#### 5.5.1 功能概述

![image-20230215104208447](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151042596.png)



#### 5.5.2 编译和配置

1. 下载工程文件后编译

```
mvn clean package -Dmaven.test.skip=true
```

修改rocketmq` 配置文件 `application.yml 设置 nameserver 地址和端口号

![image-20230215105614105](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151056174.png)



```
java -jar rocketmq-dashboard-1.0.1-SNAPSHOT.jar
```



启动后访问

![image-20230215105517849](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151055925.png)



#### 5.5.3 具体操作

创建主题：

主题 `>` 新增/更新

![image-20230215110637667](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151106748.png)

![image-20230215110651645](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151106699.png)



创建消费组

消费者 `>` 新增/更新

![image-20230215111000820](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151110881.png)

![image-20230215111134575](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151111643.png)

重置消费位点

主题 `>` 重置消费位点

![image-20230215111224968](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151112024.png)

#### 5.5.4 运维

![image-20230215111550284](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151115346.png)

运维这块儿就两个功能:

- 设置Nameserver
- 打开/关闭vipchannnel

设置nameserver：可以添加多个nameserver地址到输入框内，默认读取的是DashBoard这个springboot启动配置里面的nameserver配置。如果rockermq集群里有加入新的nameserver节点，可以在这里动态配置后更新生效。

打开/关闭vipchannel: 这里默认为false就好，vipchannnel针对的是topic的优先级，相当于在消息处理的时候，有些topic可以走vipchannel，可以优先被处理，这个除了电商场景用的一般不多。

#### 5.5.5 驾驶舱

![image-20230215111713532](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151117629.png)

1）按broker实例为类目（比如说集群中有3个broker实例）展示当前的消息数

2）按topic为类目（比如说当前所有broker中存在10个topic）展示当前的消息数

3）指定某天和时间段，查询不同broker实例的消息数的趋势

4）指定某天和时间段，查询某topic下消息数的趋势

#### 5.5.6 集群

![image-20230215111809377](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151118431.png)



分片：指的是数据分片（或者broker），当前rocketmq集群的只有一个数据分片，id为RaftNode00，即所有数据都在这个分片上；rocketmq的消息数据可以分布在多个数据分片上（一般都是多broker集群），后面搭建集群化环境的时候会讲到。

编号：标识了哪些是master（0是master)，哪些是slave，master负责直接读写；slave相当于master的副本，定期从master同步数据，如果master挂掉，slave会自动内部选举一个master节点。

地址：即broker的实际ip端口。

版本：rocketmq的版本

生产消息TPS：即broker中处理消息的TPS（每秒落盘的消息数）。

消费消息TPS：即consumer从broker中收取消息的TPS（每秒接收的消息数） 。

昨日生产总数：昨天落盘的总消息数。

昨日消费总数：昨天消费的总消息数。

今天生产总数：今天落盘的总消息数。

今天消费总数：今天消费的总消息数。

#### 5.6.7 主题

主题里面有三大类型

普通主题：这里是rocketmq自动创建的一些系统topic，然后用户创建的topic也展示在这里。

重试主题：这里是发送失败时候系统为之创建的topic。

死信主题：这里的topic类似垃圾箱，无法从中生产或者消费消息。

![image-20230215111917499](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151119559.png)



路由：

![image-20230215112042363](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151120432.png)

最上面的broker：RaftNode00指的是分片，brokerAddrs指的是分片里的几个broker的地址信息，即该topic存在于这几个broker中。

下面比较有用的是perm，通过修改perm可以使当前broker分别置为只读，只写，和读写状态。当用于运维的时候可以将broker置为只读状态

![image-20230215112623672](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151126738.png)

### 5.6 RocketMQ Promethus Exporter

`Rocketmq-exporter` 是用于监控 RocketMQ broker 端和客户端所有相关指标的系统，通过 `mqAdmin` 从 broker 端获取指标值后封装成 87 个 cache。

`Rocketmq-expoter` 获取监控指标的流程如下图所示，Expoter 通过 MQAdminExt 向 MQ 集群请求数据，请求到的数据通过 MetricService 规范化成 Prometheus 需要的格式，然后通过 /metics 接口暴露给 Promethus。

#### 5.6.1 Metric结构

`Metric` 类位于 `org.apache.rocketmq.expoter.model.metrics` 包下，实质上是一些实体类，每个实体类代表一类指标, 总共 14 个 Metric 类。这些类作为 87 个 Cache 的 key， 用不同的 label 值进行区分。

实体类中包含了 LABEL 的三个维度：BROKER、CONSUMER、PRODUCER

- **broker 相关 metric 类有**: BrokerRuntimeMetric、BrokerMetric、DLQTopicOffsetMetric、TopicPutNumMetric
- **消费者相关类有**: ConsumerRuntimeConsumeFailedMsgsMetric 、ConsumerRuntimeConsumeFailedTPSMetric 、ConsumerRuntimeConsumeOKTPSMetric、ConsumerRuntimeConsumeRTMetric、ConsumerRuntimePullRTMetric、ConsumerRuntimePullTPSMetric、ConsumerCountMetric、ConsumerMetric、ConsumerTopicDiffMetric
- **生产者相关 metric 类有**: ProducerMetric



#### 5.6.2 Prometheus 拉取 metrics 的过程

`RocketMQ-exporter` 项目和 `Prometheus` 相当于服务器和客户端的关系，RocketMQ-exporter 项目引入了 Prometheus 的 client 包，该包中规定了需要获取的信息的类型即项目中的 MetricFamilySamples 类，Prometheus 向 expoter 请求 metrics，expoter 将信息封装成相应的类型之后返回给 Prometheus。

rocketmq-expoter 项目启动后，会获取 rocketmq 的各项 metrics 收集到 mfs 对象中，当浏览器或 Prometheus 访问相应的接口时，会通过 service 将 mfs 对象中的 samples 生成 Prometheus 所支持的格式化数据。主要包含以下步骤：

浏览器通过访问 ip:5557/metrics，会调用 RMQMetricsController 类下的 metrics 方法，其中 ip 为 rocketmq-expoter 项目运行的主机 ip

```java
private void metrics(HttpServletResponse response) throws IOException {
    StringWriter writer = new StringWriter();
    metricsService.metrics(writer);
    response.setHeader("Content-Type", "text/plain; version=0.0.4; charset=utf-8");
    response.getOutputStream().print(writer.toString());
}
```



通过新建 StringWriter 对象用于收集 metrics 指标，调用 MetricsService 类中的方法 metrics 将 expoter 中提取到的指标收集到 writer 对象中，最后将收集到的指标输出到网页上。

收集到的指标格式为:

```javascript
<metric name>{<label name>=<label value>, ...} <metric value>
```

#### 5.6.3 快速开始

配置 application.yml

`application.yml` 中重要的配置主要有:

- server.port 设置 promethus 监听 rocketmq-exporter 的端口, 默认为 5557
- rocketmq.config.webTelemetryPath 配置 promethus 获取指标的路径,默认为 /metrics ，使用默认值即可.
- rocketmq.config.enableACL 如果 RocketMQ 集群开启了 ACL 验证,需要配置为 true, 并在 accessKey 和 secretKey 中配置相应的 ak, sk.
- rocketmq.config.outOfTimeSeconds 用于配置存储指标和相应的值的过期时间,若超过该时间,cache 中的 key 对应的节点没有发生写更改,则会进行删除.一般配置为 60s 即可(根据 promethus 获取指标的时间间隔进行合理配置,只要保证过期时间大于等于 promethus 收集指标的时间间隔即可)
- task.*.cron 配置 exporter 从 broker 拉取指标的定时任务的时间间隔,默认值为"15 0/1* * * ?" 每分钟的 15s 拉取一次指标.

启动 exporter 项目

按照 promethus 官网配置启动

配置 promethus 的 static_config: -targets 为 exporter 的启动 IP 和端口,如: localhost:5557

访问 promethus 页面

本地启动默认为: localhost:9090



## 6 集群部署

### 6.1 集群架构理论

![image-20230212150126231](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212150126231.png)

#### 6.1.1 数据复制和刷盘策略

![image-20230212150215161](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212150215161.png)

复制策略是Broker的Master与Slave间的数据同步方式。分为同步复制与异步复制：

- 同步复制：消息写入master后，master会等待slave同步数据成功后才向producer返回成功ACK
- 异步复制：消息写入master后，master立即向producer返回成功ACK，无需等待slave同步数据成功

```
异步复制策略会降低系统的写入延迟，RT变小，提高了系统的吞吐量
```



刷盘策略指的是broker中消息的**落盘方式**，

即消息发送到broker内存后消息持久化到磁盘的方式。分为同步刷盘与异步刷盘.

- 同步刷盘：当消息持久化到broker的磁盘后才算是消息写入成功。
- 异步刷盘：当消息写入到broker的内存后即表示消息写入成功，无需等待消息持久化到磁盘。

```
1 ）异步刷盘策略会降低系统的写入延迟，RT变小，提高了系统的吞吐量
2 ）消息写入到Broker的内存，一般是写入到了PageCache
3 ）对于异步 刷盘策略，消息会写入到PageCache后立即返回成功ACK。但并不会立即做落盘操作，而是当PageCache到达一定量时会自动进行落盘。
```



#### 6.1.2 Broker集群模式

根据Broker集群中各个节点间关系的不同，Broker集群可以分为以下几类：

1 **单Master**

只有一个broker（其本质上就不能称为集群）。这种方式也只能是在测试时使用，生产环境下不能使用，因为存在单点问题。

**2 多Master**

broker集群仅由多个master构成，不存在Slave。同一Topic的各个Queue会平均分布在各个master节点上。

- 优点：配置简单，单个Master宕机或重启维护对应用无影响，在磁盘配置为RAID10时，即使机器宕机不可恢复情况下，由于RAID10磁盘非常可靠，消息也不会丢（异步刷盘丢失少量消息，同步刷盘一条不丢），性能最高；
- 缺点：单台机器宕机期间，这台机器上未被消费的消息在机器恢复之前不可订阅（不可消费），消息实时性会受到影响。

```
以上优点的前提是，这些Master都配置了RAID磁盘阵列。如果没有配置，一旦出现某Master宕机，则会发生大量消息丢失的情况。
```



**3  多Master多Slave模式-异步复制**

broker集群由多个master构成，每个master又配置了多个slave（在配置了RAID磁盘阵列的情况下，一个master一般配置一个slave即可）。master与slave的关系是主备关系，即master负责处理消息的读写请求，而slave仅负责消息的备份与master宕机后的角色切换。

异步复制即前面所讲的`复制策略`中的`异步复制策略`，即消息写入master成功后，master立即向producer返回成功ACK，无需等待slave同步数据成功。

该模式的最大特点之一是，当master宕机后slave能够`自动切换`为master。不过由于slave从master的同步具有短暂的延迟（毫秒级），所以当master宕机后，这种异步复制方式可能会存在少量消息的丢失问题。

```
Slave从Master同步的延迟越短，其可能丢失的消息就越少

对于Master的RAID磁盘阵列，若使用的也是异步复制策略，同样也存在延迟问题，同样也可能会丢失消息。但RAID阵列的秘诀是微秒级的（因为是由硬盘支持的），所以其丢失的数据量会更少。
```



**4  多Master多Slave模式-同步双写**

该模式是多Master多Slave模式的同步复制实现。所谓同步双写，指的是消息写入master成功后，master会等待slave同步数据成功后才向producer返回成功ACK，即master与slave都要写入成功后才会返回成功ACK，也即双写。该模式与异步复制模式相比，优点是消息的安全性更高，不存在消息丢失的情况。但单个消息的RT略高，从而导致性能要略低（大约低10%）。

该模式存在一个大的问题：对于目前的版本，Master宕机后，Slave不会自动切换到Master。

**5 最佳实践**

一般会为Master配置RAID10磁盘阵列，然后再为其配置一个Slave。即利用了RAID10磁盘阵列的高效、安全性，又解决了可能会影响订阅的问题

```
1 ）RAID磁盘阵列的效率要高于Master-Slave集群。因为RAID是硬件支持的。也正因为如此，所以RAID阵列的搭建成本较高。

2 ）多Master+RAID阵列，与多Master多Slave集群的区别是什么？
1.多Master+RAID阵列，其仅仅可以保证数据不丢失，即不影响消息写入，但其可能会影响到消息的订阅。但其执行效率要远高于多Master多Slave集群
2.多Master多Slave集群，其不仅可以保证数据不丢失，也不会影响消息写入。其运行效率要低于多Master+RAID阵列
```



### 6.2 集群搭建实践

#### 6.2.1 环境配置

这里要搭建一个双主双从异步复制的Broker集群。为了方便，这里使用了两台主机来完成集群的搭建。这两台主机的功能与broker角色分配如下表。

| 序号 | 主机名/IP   | IP              | 功能                | BROKER角色       |
| ---- | ----------- | --------------- | ------------------- | ---------------- |
| 1    | rocketmqOS1 | 192.168.204.104 | NameServer + Broker | Master1 + Slave2 |
| 2    | rocketmqOS2 | 192.168.204.105 | NameServer + Broker | Master2 + Slave1 |

```
hostnamectl set-hostname rocketmqOS1
hostnamectl set-hostname rocketmqOS2

```

![image-20230212155707908](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212155707908.png)

#### 6.2.2 修改配置文件

进入conf目录下，可以看到配置的例子

![image-20230212155936178](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212155936178.png)

```
# 要修改的配置文件在rocketMQ解压目录的conf/2m-2s-async目录中。
cd 2m-2s-async
```

![image-20230212160140700](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212160140700.png)

```
-rw-r--r-- 1 root root 929 Jun  9  2021 broker-a.properties # a机器master的配置文件
-rw-r--r-- 1 root root 922 Jun  9  2021 broker-a-s.properties # a机器slave的配置文件
-rw-r--r-- 1 root root 929 Jun  9  2021 broker-b.properties # b机器master的配置文件
-rw-r--r-- 1 root root 922 Jun  9  2021 broker-b-s.properties # b机器slave的配置文件

```

修改broker-a.properties

```
# 指定整个broker集群的名称，或者说是RocketMQ集群的名称
brokerClusterName=  
# 指定master-slave集群的名称。一个RocketMQ集群可以包含多个master-slave集群
brokerName=broker-a
# master的brokerId为 0
brokerId= 0
# 指定删除消息存储过期文件的时间为凌晨 4 点
deleteWhen= 04
# 指定未发生更新的消息存储文件的保留时长为 48 小时， 48 小时后过期，将会被删除
fileReservedTime= 48
# 指定当前broker为异步复制master
brokerRole=ASYNC_MASTER
# 指定刷盘策略为异步刷盘
flushDiskType=ASYNC_FLUSH
# 指定Name Server的地址
namesrvAddr=192.168.204.104:9876;192.168.204.105:9876

```

增加namesrvAddr

![image-20230212164326320](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212164326320.png)

修改broker-b-s.properties

```
brokerClusterName=DefaultCluster
# 指定这是另外一个master-slave集群
brokerName=broker-b
# slave的brokerId为非 0
brokerId=1
deleteWhen=04
fileReservedTime=48
# 指定当前broker为slave
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
namesrvAddr=192.168.294.104:9876;192.168.204.105:9876
# 指定Broker对外提供服务的端口，即Broker与producer与consumer通信的端口。默认10911 。由于当前主机同时充当着master1与slave2，而前面的master1使用的是默认端口。这里需要将这两个端口加以区分，以区分出master1与slave2
listenPort= 11911
# 指定消息存储相关的路径。默认路径为~/store目录。由于当前主机同时充当着master1与slave2，master1使用的是默认路径，这里就需要再指定一个不同路径
storePathRootDir=~/store-s
storePathCommitLog=~/store-s/commitlog
storePathConsumeQueue=~/store-s/consumequeue
storePathIndex=~/store-s/index
storeCheckpoint=~/store-s/checkpoint
abortFile=~/store-s/abort

```

![image-20230212165419605](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212165419605.png)

这些配置文件，还可以设置如下

```
#指定整个broker集群的名称，或者说是RocketMQ集群的名称
brokerClusterName=rocket-MS
#指定master-slave集群的名称。一个RocketMQ集群可以包含多个master-slave集群
brokerName=broker-a
#0 表示 Master，>0 表示 Slave
brokerId=0
#nameServer地址，分号分割
namesrvAddr=nameserver1:9876;nameserver2:9876
#默认为新建Topic所创建的队列数
defaultTopicQueueNums=4
#是否允许 Broker 自动创建Topic，建议生产环境中关闭
autoCreateTopicEnable=true
#是否允许 Broker 自动创建订阅组，建议生产环境中关闭
autoCreateSubscriptionGroup=true
#Broker对外提供服务的端口，即Broker与producer与consumer通信的端口
listenPort=10911
#HA高可用监听端口，即Master与Slave间通信的端口，默认值为listenPort+1
haListenPort=10912
#指定删除消息存储过期文件的时间为凌晨 4 点
deleteWhen=04
#指定未发生更新的消息存储文件的保留时长为 48 小时， 48 小时后过期，将会被删除
fileReservedTime=48
#指定commitLog目录中每个文件的大小，默认1G
mapedFileSizeCommitLog=1073741824
#指定ConsumeQueue的每个Topic的每个Queue文件中可以存放的消息数量，默认30w条
mapedFileSizeConsumeQueue=300000
#在清除过期文件时，如果该文件被其他线程所占用（引用数大于 0 ，比如读取消息），此时会阻止此次删除任务，同时在第一次试图删除该文件时记录当前时间戳。该属性则表示从第一次拒绝删除后开始计时，该文件最多可以保留的时长。在此时间内若引用数仍不为 0 ，则删除仍会被拒绝。不过时间到后，文件将被强制删除
destroyMapedFileIntervalForcibly=120000
#指定commitlog、consumequeue所在磁盘分区的最大使用率，超过该值，则需立即清除过期文件
diskMaxUsedSpaceRatio=88
#指定store目录的路径，默认在当前用户主目录中
storePathRootDir=/usr/local/rocketmq-all-4.5.0/store
#commitLog目录路径
storePathCommitLog=/usr/local/rocketmq-all-4.5.0/store/commitlog
#consumeueue目录路径
storePathConsumeQueue=/usr/local/rocketmq-all-4.5.0/store/consumequeue
#index目录路径
storePathIndex=/usr/local/rocketmq-all-4.5.0/store/index
#checkpoint文件路径
storeCheckpoint=/usr/local/rocketmq-all-4.5.0/store/checkpoint
#abort文件路径
abortFile=/usr/local/rocketmq-all-4.5.0/store/abort
#指定消息的最大大小
maxMessageSize= 65536
#Broker的角色
# - ASYNC_MASTER 异步复制Master
# - SYNC_MASTER 同步双写Master
# - SLAVE
brokerRole=SYNC_MASTER
#刷盘策略
# - ASYNC_FLUSH 异步刷盘
# - SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
#发消息线程池数量
sendMessageThreadPoolNums=128
#拉消息线程池数量
pullMessageThreadPoolNums=128
#强制指定本机IP，需要根据每台机器进行修改。官方介绍可为空，系统默认自动识别，但多网卡时IP地址可能读取错误
brokerIP1=192.168.3.105

```

#### 6.2.3 修改另一个机器

修改rocketmqOS2配置文件

修改broker-b.properties

```
brokerClusterName=DefaultCluster
brokerName=broker-b
brokerId=0
deleteWhen=04
fileReservedTime=48
brokerRole=ASYNC_MASTER
flushDiskType=ASYNC_FLUSH
namesrvAddr=192.168.204.104:9876;192.168.204.105:9876
```

![image-20230212165724775](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212165724775.png)

修改broker-a-s.properties

```
brokerClusterName=DefaultCluster
brokerName=broker-a
brokerId=1
deleteWhen=04
fileReservedTime=48
brokerRole=SLAVE
flushDiskType=ASYNC_FLUSH
namesrvAddr=192.168.204.104:9876;192.168.204.105:9876
listenPort=11911
storePathRootDir=~/store-s
storePathCommitLog=~/store-s/commitlog
storePathConsumeQueue=~/store-s/consumequeue
storePathIndex=~/store-s/index
storeCheckpoint=~/store-s/checkpoint
abortFile=~/store-s/abort

```

![image-20230212170205797](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212170205797.png)



### 6.3 启动集群

#### 6.3.1 启动NameServer集群

分别启动rocketmqOS1与rocketmqOS2两个主机中的NameServer。启动命令完全相同。

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/

nohup sh bin/mqnamesrv &

tail -f ~/logs/rocketmqlogs/namesrv.log

```

#### 6.3.2 启动两个Master

分别启动rocketmqOS1与rocketmqOS2两个主机中的broker master。注意，它们指定所要加载的配置文件是不同的。

104服务器执行

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/

nohup sh bin/mqbroker -c conf/2m-2s-async/broker-a.properties &

tail -f ~/logs/rocketmqlogs/broker.log

```

105服务器执行

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/

nohup sh bin/mqbroker -c conf/2m-2s-async/broker-b.properties &

tail -f ~/logs/rocketmqlogs/broker.log

```



#### 6.3.3 启动两个Slave

分别启动rocketmqOS1与rocketmqOS2两个主机中的broker slave。注意，它们指定所要加载的配置文件是不同的。

104服务器执行

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/

nohup sh bin/mqbroker -c conf/2m-2s-async/broker-b-s.properties &

tail -f ~/logs/rocketmqlogs/broker.log

```

105服务器执行

```
cd /usr/local/rocketmq/rocketmq-all-4.9.0-bin-release/

nohup sh bin/mqbroker -c conf/2m-2s-async/broker-a-s.properties &

tail -f ~/logs/rocketmqlogs/broker.log

```

检查是否成功启动

![image-20230212172149718](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212172149718.png)

#### 6.3.4 控制台查看

如果要控制台查看，需要修改一下配置

![image-20230212172600484](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212172600484.png)

![image-20230212173443000](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212173443000.png)



然后重新编译

```
cd D:\soft\rocketmq\rocketmq-console-1.0.0\rocketmq-externals-rocketmq-console-1.0.0\rocketmq-console

mvn clean package -Dmaven.test.skip=true
```

启动

```
java -jar rocketmq-console-ng-1.0.0.jar &
```

访问http://localhost:8081

![image-20230212174019696](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212174019696.png)



### 6.4 mqadmin命令

在mq解压目录的bin目录下有一个mqadmin命令，该命令是一个运维指令，用于对mq的主题，集群，broker 等信息进行管理。

![image-20230212174509708](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212174509708.png)

在运行mqadmin命令之前，先要修改mq解压目录下bin/tools.sh配置的JDK的ext目录位置。本机的ext目录在`/usr/java/jdk1.8.0_161/jre/lib/ext`。

使用vi命令打开tools.sh文件，并在JAVA_OPT配置的-Djava.ext.dirs这一行的后面添加ext的路径。

```
JAVA_OPT="${JAVA_OPT} -server -Xms1g -Xmx1g -Xmn256m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=128m"
JAVA_OPT="${JAVA_OPT} -Djava.ext.dirs=${BASE_DIR}/lib:${JAVA_HOME}/jre/lib/ext:${JAVA_HOME}/lib/ext:/usr/java/jdk1.8.0_161/jre/lib/ext"
JAVA_OPT="${JAVA_OPT} -cp ${CLASSPATH}"

```

运行命令

直接运行该命令，可以看到其可以添加的commands。通过这些commands可以完成很多的功能。

```
[root@rocketmqos1 rocketmq-all-4.9.0-bin-release]# ./bin/mqadmin

```

参考资料

https://github.com/apache/rocketmq/blob/master/docs/cn/operation.md

![image-20230212174759078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212174759078.png)



## 7 Springboot集成

### 7.1 配置依赖

```
      <dependency>
            <groupId>com.alibaba.rocketmq</groupId>
            <artifactId>rocketmq-client</artifactId>
            <version>3.2.6</version>
        </dependency>
```

### 7.2 配置文件

```
rocketmq:
  producer:
    # 发送同一类消息的设置为同一个group，保证唯一,默认不需要设置，rocketmq会使用ip@pid(pid代表jvm名字)作为唯一标示
    groupName: vehicleProducerGroup
    #mq的nameserver地址
    #namesrvAddr: 123.206.175.47:9876;182.254.210.72:9876
    namesrvAddr: 192.168.133.103:9876
    #如果需要同一个jvm中不同的producer往不同的mq集群发送消息，需要设置不同的instanceName
    instanceName: vehicleProducer
    #topic名称
    topic: TEST
    #根据实际情况设置消息的tag
    tag: TEST
    #消息最大长度
    maxMessageSize: 131072 # 1024*128
    #发送消息超时时间
    sendMsgTimeout: 10000
  consumer:
    namesrvAddr: 192.168.133.103:9876
    groupName: vehicleProducerGroup
    topic: sms
    tag: verifycode
    consumeThreadMin: 20
    consumeThreadMax: 64
```

### 7.3 代码

配置类

```
package com.jiang.config;

import com.alibaba.rocketmq.client.consumer.DefaultMQPushConsumer;
import com.alibaba.rocketmq.client.exception.MQClientException;
import com.jiang.exception.RocketMQException;
import com.jiang.listener.MessageListener;
import org.apache.commons.lang.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.context.annotation.Bean;
/**
 * [消费者]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/15 12:57]
 */
@SpringBootConfiguration
public class RocketMQConsumerConfiguration {
    public static final Logger LOGGER = LoggerFactory.getLogger(RocketMQConsumerConfiguration.class);
    @Value("${rocketmq.consumer.namesrvAddr}")
    private String namesrvAddr;
    @Value("${rocketmq.consumer.groupName}")
    private String groupName;
    @Value("${rocketmq.consumer.topic}")
    private String topic;
    @Value("${rocketmq.consumer.tag}")
    private String tag;
    @Value("${rocketmq.consumer.consumeThreadMin}")
    private int consumeThreadMin;
    @Value("${rocketmq.consumer.consumeThreadMax}")
    private int consumeThreadMax;

    @Autowired
    @Qualifier("messageProcessorImplTest")
    private MessageProcessor messageProcessor;

    @Bean
    public DefaultMQPushConsumer getRocketMQConsumer() throws RocketMQException {
        if (StringUtils.isBlank(groupName)){
            throw new RocketMQException("groupName is null !!!");
        }
        if (StringUtils.isBlank(namesrvAddr)){
            throw new RocketMQException("namesrvAddr is null !!!");
        }
        if (StringUtils.isBlank(topic)){
            throw new RocketMQException("topic is null !!!");
        }
        if (StringUtils.isBlank(tag)){
            throw new RocketMQException("tag is null !!!");
        }
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer(groupName);
        consumer.setNamesrvAddr(namesrvAddr);
        consumer.setConsumeThreadMin(consumeThreadMin);
        consumer.setConsumeThreadMax(consumeThreadMax);
        MessageListener messageListener = new MessageListener();
        messageListener.setMessageProcessor(messageProcessor);
        consumer.registerMessageListener(messageListener);
        try {
            consumer.subscribe(topic,this.tag);
            consumer.start();
            LOGGER.info("consumer is start !!! groupName:{},topic:{},namesrvAddr:{}",groupName,topic,namesrvAddr);
        }catch (MQClientException e){
            LOGGER.error("consumer is start !!! groupName:{},topic:{},namesrvAddr:{}",groupName,topic,namesrvAddr,e);
            throw new RocketMQException(e);
        }
        return consumer;
    }
}

```

```
package com.jiang.config;

import com.alibaba.rocketmq.client.exception.MQClientException;
import com.alibaba.rocketmq.client.producer.DefaultMQProducer;
import com.jiang.exception.RocketMQException;
import org.apache.commons.lang.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.context.annotation.Bean;
/**
 * [生产者]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/15 12:56]
 */
@SpringBootConfiguration
public class RocketMQProducerConfiguration {
    public static final Logger LOGGER = LoggerFactory.getLogger(RocketMQProducerConfiguration.class);
    @Value("${rocketmq.producer.groupName}")
    private String groupName;
    @Value("${rocketmq.producer.namesrvAddr}")
    private String namesrvAddr;
    @Value("${rocketmq.producer.instanceName}")
    private String instanceName;
    @Value("${rocketmq.producer.maxMessageSize}")
    private int maxMessageSize ; //4M
    @Value("${rocketmq.producer.sendMsgTimeout}")
    private int sendMsgTimeout ;

    @Bean
    public DefaultMQProducer getRocketMQProducer() throws RocketMQException {
        if (StringUtils.isBlank(this.groupName)) {
            throw new RocketMQException("groupName is blank");
        }
        if (StringUtils.isBlank(this.namesrvAddr)) {
            throw new RocketMQException("nameServerAddr is blank");
        }
        if (StringUtils.isBlank(this.instanceName)){
            throw new RocketMQException("instanceName is blank");
        }
        DefaultMQProducer producer;
        producer = new DefaultMQProducer(this.groupName);
        producer.setNamesrvAddr(this.namesrvAddr);
        producer.setInstanceName(instanceName);
        producer.setMaxMessageSize(this.maxMessageSize);
        producer.setSendMsgTimeout(this.sendMsgTimeout);
        try {
            producer.start();
            LOGGER.info(String.format("producer is start ! groupName:[%s],namesrvAddr:[%s]"
                    , this.groupName, this.namesrvAddr));
        } catch (MQClientException e) {
            LOGGER.error(String.format("producer is error {}"
                    , e.getMessage(),e));
            throw new RocketMQException(e);
        }
        return producer;
    }
}

```



启动类

```
package com.jiang;

import com.alibaba.rocketmq.client.consumer.DefaultMQPushConsumer;
import com.alibaba.rocketmq.client.exception.MQBrokerException;
import com.alibaba.rocketmq.client.exception.MQClientException;
import com.alibaba.rocketmq.client.producer.DefaultMQProducer;
import com.alibaba.rocketmq.client.producer.SendResult;
import com.alibaba.rocketmq.common.message.Message;
import com.alibaba.rocketmq.remoting.exception.RemotingException;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

/**
 * 启动
 */
@SpringBootApplication
public class RocketMQApp {
    public static void main(String[] args)  throws InterruptedException, RemotingException, MQClientException, MQBrokerException {
        ApplicationContext context = SpringApplication.run(RocketMQApp.class,args);
        DefaultMQProducer defaultMQProducer = context.getBean(DefaultMQProducer.class);
        Message msg = new Message("TEST",// topic
                "TEST",// tag
                "KKK",//key用于标识业务的唯一性
                ("Hello RocketMQ !!!!!!!!!!" ).getBytes()// body 二进制字节数组
        );
        SendResult result = defaultMQProducer.send(msg);
        System.out.println(result);
        DefaultMQPushConsumer consumer = context.getBean(DefaultMQPushConsumer.class);
    }
}
```

自定义异常

```
package com.jiang.exception;

/**
 * [自定义异常]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/15 12:45]
 */
public class RocketMQException extends Exception{
    public RocketMQException() {
        super();
    }

    public RocketMQException(String message) {
        super(message);
    }

    public RocketMQException(String message, Throwable cause) {
        super(message, cause);
    }

    public RocketMQException(Throwable cause) {
        super(cause);
    }

    protected RocketMQException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}

```

监听器

```
package com.jiang.listener;

import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import com.alibaba.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import com.alibaba.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import com.alibaba.rocketmq.common.message.MessageExt;
import com.jiang.config.MessageProcessor;

import java.util.List;

/**
 * [监听器]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/15 12:48]
 */
public class MessageListener implements MessageListenerConcurrently {
    private MessageProcessor messageProcessor;

    public void setMessageProcessor(MessageProcessor messageProcessor) {
        this.messageProcessor = messageProcessor;
    }

    @Override
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext consumeConcurrentlyContext) {
        for (MessageExt msg : msgs){
            boolean result = messageProcessor.handleMessage(msg);
            if (!result){
                return ConsumeConcurrentlyStatus.RECONSUME_LATER;
            }
        }
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
    }
}

```

接口

```
package com.jiang.config;

import com.alibaba.rocketmq.common.message.MessageExt;

public interface MessageProcessor {

    /**
     * 处理消息的接口
     * @param messageExt
     * @return
     */
    public boolean handleMessage(MessageExt messageExt);
}

```



实现类

```
package com.jiang.config;

import com.alibaba.rocketmq.common.message.MessageExt;
import org.springframework.stereotype.Component;

/**
 * [核心操作]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/2/15 12:50]
 */
@Component
public class MessageProcessorImplTest implements MessageProcessor{
    @Override
    public boolean handleMessage(MessageExt messageExt) {
        System.out.println("receive : " + messageExt.toString());
        return true;
    }
}

```

### 7.4 测试结果

![image-20230215132650337](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151326420.png)

![image-20230215132714883](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151327939.png)

![image-20230215132803918](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151328990.png)

## 8 RocketMQ工作原理

### 8.1 消息的生产

Producer可以将消息写入到某Broker中的某Queue中，其经历了如下过程：

- Producer发送消息之前，会先向NameServer发出获取消息Topic的路由信息的请求
- NameServer返回该Topic的路由表及Broker列表
- Producer根据代码中指定的Queue选择策略，从Queue列表中选出一个队列，用于后续存储消息
- Produer对消息做一些特殊处理，例如，消息本身超过4M，则会对其进行压缩
- Producer向选择出的Queue所在的Broker发出RPC请求，将消息发送到选择出的Queue

```
路由表：实际是一个Map，key为Topic名称，value是一个QueueData实例列表。QueueData并不是一个Queue对应一个QueueData，而是一个Broker中该Topic的所有Queue对应一个QueueData。即，只要涉及到该Topic的Broker，一个Broker对应一个QueueData。QueueData中包含brokerName。简单来说，路由表的key为Topic名称，value则为所有涉及该Topic的BrokerName列表。

Broker列表：其实际也是一个Map。key为brokerName，value为BrokerData。一个Broker对应一个BrokerData实例，对吗？不对。一套brokerName名称相同的Master-Slave小集群对应一个BrokerData。BrokerData中包含brokerName及一个map。该map的key为brokerId，value为该broker对应的地址。brokerId为 0 表示该broker为Master，非 0 表示Slave。
```



Queue选择算法：

对于无序消息，其Queue选择算法，也称为消息投递算法，常见的有两种：

轮询算法

默认选择算法。该算法保证了每个Queue中可以均匀的获取到消息。

```
该算法存在一个问题：由于某些原因，在某些Broker上的Queue可能投递延迟较严重。从而导致Producer的缓存队列中出现较大的消息积压，影响消息的投递性能。
```

最小投递延迟算法

该算法会统计每次消息投递的时间延迟，然后根据统计出的结果将消息投递到时间延迟最小的Queue。如果延迟相同，则采用轮询算法投递。该算法可以有效提升消息的投递性能。

```
该算法也存在一个问题：消息在Queue上的分配不均匀。投递延迟小的Queue其可能会存在大量的消息。而对该Queue的消费者压力会增大，降低消息的消费能力，可能会导致MQ中消息的堆积
```



### 8.2 消息的存储

RocketMQ中的消息存储在本地文件系统中，这些相关文件默认在当前用户主目录下的store目录中

![image-20230212175702655](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230212175702655.png)

- abort：该文件在Broker启动后会自动创建，正常关闭Broker，该文件会自动消失。若在没有启动Broker的情况下，发现这个文件是存在的，则说明之前Broker的关闭是非正常关闭。
- checkpoint：其中存储着commitlog、consumequeue、index文件的最后刷盘时间戳
- commitlog：其中存放着commitlog文件，而消息是写在commitlog文件中的
- conæg：存放着Broker运行期间的一些配置数据
- consumequeue：其中存放着consumequeue文件，队列就存放在这个目录中
- index：其中存放着消息索引文件indexFile
- lock：运行期间使用到的全局资源锁



#### 8.2.1Commitlog文件

说明：在很多资料中commitlog目录中的文件简单就称为commitlog文件。但在源码中，该文件被命名为mappedFile。

**目录与文件**

commitlog目录中存放着很多的mappedFile文件，当前Broker中的所有消息都是落盘到这些mappedFile文件中的。mappedFile文件大小为1G（小于等于1G），文件名由 20 位十进制数构成，表示当前文件的第一条消息的起始位移偏移量。

需要注意的是，一个Broker中仅包含一个commitlog目录，所有的mappedFile文件都是存放在该目录中的。即无论当前Broker中存放着多少Topic的消息，这些消息都是被顺序写入到了mappedFile文件中的。也就是说，这些消息在Broker中存放时并没有被按照Topic进行分类存放。

```
mappedFile文件是顺序读写的文件，所有其访问效率很高

无论是SSD磁盘还是SATA磁盘，通常情况下，顺序存取效率都会高于随机存取。
```

**消息单元**

![image-20230213125525316](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131255489.png)

mappedFile文件内容由一个个的`消息单元`构成。每个消息单元中包含消息总长度MsgLen、消息的物理位置physicalOffset、消息体内容Body、消息体长度BodyLength、消息主题Topic、Topic长度 TopicLength、消息生产者BornHost、消息发送时间戳BornTimestamp、消息所在的队列QueueId、消息在Queue中存储的偏移量QueueOffset等近 20 余项消息相关属性

#### 8.2.2 consumequeue

![image-20230213125653038](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131256094.png)

![image-20230213125709420](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131257482.png)

为了提高效率，会为每个Topic在~/store/consumequeue中创建一个目录，目录名为Topic名称。在该Topic目录下，会再为每个该Topic的Queue建立一个目录，目录名为queueId。每个目录中存放着若干consumequeue文件，consumequeue文件是commitlog的索引文件，可以根据consumequeue定位到具体的消息。

consumequeue文件名也由 20 位数字构成，表示当前文件的第一个索引条目的起始位移偏移量。与mappedFile文件名不同的是，其后续文件名是固定的。因为consumequeue文件大小是固定不变的。



#### 8.2.3 对文件的读写

![image-20230213125752290](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131257346.png)

**消息的写入**

一条消息进入到Broker后经历了以下几个过程才最终被持久化。

- Broker根据queueId，获取到该消息对应索引条目要在consumequeue目录中的写入偏移量，即QueueOffset
- 将queueId、queueOffset等数据，与消息一起封装为消息单元
- 将消息单元写入到commitlog
- 同时，形成消息索引条目
- 将消息索引条目分发到相应的consumequeue

**消息的拉取**

- 当Consumer来拉取消息时会经历以下几个步骤：

  - Consumer获取到其要消费消息所在Queue的消费偏移量offset，计算出其要消费消息的消息offset

    > 消费offset即消费进度，consumer对某个Queue的消费offset，即消费到了该Queue的第几条消息
    > 消息offset = 消费offset + 1

- Consumer向Broker发送拉取请求，其中会包含其要拉取消息的Queue、消息offset及消息Tag。

- Broker计算在该consumequeue中的queueOffset。

  > queueOffset = 消息offset * 20字节

- 从该queueOffset处开始向后查找第一个指定Tag的索引条目。

- 解析该索引条目的前 8 个字节，即可定位到该消息在commitlog中的commitlog offset

- 从对应commitlog offset中读取消息单元，并发送给Consumer

**性能提升**

RocketMQ中，无论是消息本身还是消息索引，都是存储在磁盘上的。其不会影响消息的消费吗？当然不会。其实RocketMQ的性能在目前的MQ产品中性能是非常高的。因为系统通过一系列相关机制大大提升了性能。

首先，RocketMQ对文件的读写操作是通过`mmap零拷贝`进行的，将对文件的操作转化为直接对内存地址进行操作，从而极大地提高了文件的读写效率。

其次，consumequeue中的数据是顺序存放的，还引入了`PageCache的预读取机制`，使得对consumequeue文件的读取几乎接近于内存读取，即使在有消息堆积情况下也不会影响性能。

> PageCache机制，页缓存机制，是OS对文件的缓存机制，用于加速对文件的读写操作。一般来说，程序对文件进行顺序读写的速度几乎接近于内存读写速度，主要原因是由于OS使用PageCache机制对读写访问操作进行性能优化，将一部分的内存用作PageCache。
>
> 1)写操作：OS会先将数据写入到PageCache中，随后会以异步方式由pdæush（page dirty æush)内核线程将Cache中的数据刷盘到物理磁盘
> 2)读操作：若用户要读取数据，其首先会从PageCache中读取，若没有命中，则OS在从物理磁盘上加载该数据到PageCache的同时，也会顺序 对其相邻数据块中的数据进行预读取。

RocketMQ中可能会影响性能的是对commitlog文件的读取。因为对commitlog文件来说，读取消息时会产生大量的随机访问，而随机访问会严重影响性能。不过，如果选择合适的系统IO调度算法，比如设置调度算法为Deadline（采用SSD固态硬盘的话），随机读的性能也会有所提升。



#### 8.2.4 与Kafka对比

RocketMQ的很多思想来源于Kafka，其中commitlog与consumequeue就是。

RocketMQ中的commitlog目录与consumequeue的结合就类似于Kafka中的partition分区目录。mappedFile文件就类似于Kafka中的segment段。

> Kafka中的Topic的消息被分割为一个或多个partition。partition是一个物理概念，对应到系统上就是topic目录下的一个或多个目录。每个partition中包含的文件称为segment，是具体存放消息的文件。
>
> Kafka中消息存放的目录结构是：topic目录下有partition目录，partition目录下有segment文件
>
> Kafka中没有二级分类标签Tag这个概念
>
> Kafka中无需索引文件。因为生产者是将消息直接写在了partition中的，消费者也是直接从partition中读取数据的



### 8.3 indexFile

除了通过通常的指定Topic进行消息消费外，RocketMQ还提供了根据key进行消息查询的功能。该查询是通过store目录中的index子目录中的indexFile进行索引实现的快速查询。当然，这个indexFile中的索引数据是在`包含了key的消息`被发送到Broker时写入的。如果消息中没有包含key，则不会写入。

#### 8.3.1 索引条目

每个Broker中会包含一组indexFile，每个indexFile都是以一个`时间戳`命名的（这个indexFile被创建时的时间戳）。

![image-20230213130108790](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131301839.png)

每个indexFile文件由三部分构成：indexHeader，slots槽位，indexes索引数据。每个 indexFile文件中包含500w个slot槽。而每个slot槽又可能会挂载很多的index索引单元。

indexHeader固定 40 个字节，其中存放着如下数据：

![image-20230213130137349](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131301399.png)

- beginTimestamp：该indexFile中第一条消息的存储时间
- endTimestamp：该indexFile中最后一条消息存储时间
- beginPhyoffset：该indexFile中第一条消息在commitlog中的偏移量commitlog offset
- endPhyoffset：该indexFile中最后一条消息在commitlog中的偏移量commitlog offset
- hashSlotCount：已经填充有index的slot数量（并不是每个slot槽下都挂载有index索引单元，这里统计的是所有挂载了index索引单元的slot槽的数量）
- indexCount：该indexFile中包含的索引单元个数（统计出当前indexFile中所有slot槽下挂载的所有index索引单元的数量之和）

indexFile中最复杂的是Slots与Indexes间的关系。在实际存储时，Indexes是在Slots后面的，但为了便于理解，将它们的关系展示为如下形式：

![image-20230213130205108](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131302161.png)

`key的hash值 % 500w`的结果即为slot槽位，然后将该slot值修改为该index索引单元的indexNo，根据这个indexNo可以计算出该index单元在indexFile中的位置。不过，该取模结果的重复率是很高的，为了解决该问题，在每个index索引单元中增加了preIndexNo，用于指定该slot中当前index索引单元的前一个index索引单元。而slot中始终存放的是其下最新的index索引单元的indexNo，这样的话，只要找到了slot就可以找到其最新的index索引单元，而通过这个index索引单元就可以找到其之前的所有index索引单元。

> indexNo是一个在indexFile中的流水号，从 0 开始依次递增。即在一个indexFile中所有indexNo是以此递增的。indexNo在index索引单元中是没有体现的，其是通过indexes中依次数出来的。

index索引单元默写 20 个字节，其中存放着以下四个属性：

![image-20230213130226349](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131302402.png)

- keyHash：消息中指定的业务key的hash值
- phyOffset：当前key对应的消息在commitlog中的偏移量commitlog offset
- timeDiff：当前key对应消息的存储时间与当前indexFile创建时间的时间差
- preIndexNo：当前slot下当前index索引单元的前一个index索引单元的indexNo

#### 8.3.2 indexfile创建

indexFile的文件名为当前文件被创建时的时间戳。这个时间戳有什么用处呢？

根据业务key进行查询时，查询条件除了key之外，还需要指定一个要查询的时间戳，表示要查询不大于该时间戳的最新的消息，即查询指定时间戳之前存储的最新消息。这个时间戳文件名可以简化查询，提高查询效率。具体后面会详细讲解。

indexFile文件是何时创建的？其创建的条件（时机）有两个：

- 当第一条带key的消息发送来后，系统发现没有indexFile，此时会创建第一个indexFile文件
- 当一个indexFile中挂载的index索引单元数量超出2000w个时，会创建新的indexFile。当带key的消息发送到来后，系统会找到最新的indexFile，并从其indexHeader的最后 4 字节中读取到indexCount。若indexCount >= 2000w时，会创建新的indexFile。

> 由于可以推算出，一个indexFile的最大大小是：(40 + 500w * 4 + 2000w * 20)字节

#### 8.3.3 查询流程

当消费者通过业务key来查询相应的消息时，其需要经过一个相对较复杂的查询流程。不过，在分析查询流程之前，首先要清楚几个定位计算式子：

```shell
计算指定消息key的slot槽位序号：
slot槽位序号 = key的hash % 500w (式子1)Copy to clipboardErrorCopied
计算槽位序号为n的slot在indexFile中的起始位置：
slot(n)位置 = 40 + (n - 1) * 4 (式子2)Copy to clipboardErrorCopied
计算indexNo为m的index在indexFile中的位置：
index(m)位置 = 40 + 500w * 4 + (m - 1) * 20 (式子3)Copy to clipboardErrorCopied
```

> 40 为indexFile中indexHeader的字节数
> 500w * 4 是所有slots所占的字节数





### 8.4 消息的消费

消费者从Broker中获取消息的方式有两种：pull拉取方式和push推动方式。

消费者组对于消息消费的模式又分为两种：集群消费Clustering和广播消费Broadcasting。

#### 8.4.1 消费类型

**拉取式消费**

Consumer主动从Broker中拉取消息，主动权由Consumer控制。一旦获取了批量消息，就会启动消费过程。不过，该方式的实时性较弱，即Broker中有了新的消息时消费者并不能及时发现并消费。

> 由于拉取时间间隔是由用户指定的，所以在设置该间隔时需要注意平稳：间隔太短，空请求比例会增加；间隔太长，消息的实时性太差

**推送式消费**

该模式下Broker收到数据后会主动推送给Consumer。该获取方式一般实时性较高。

该获取方式是典型的`发布-订阅`模式，即Consumer向其关联的Queue注册了监听器，一旦发现有新的消息到来就会触发回调的执行，回调方法是Consumer去Queue中拉取消息。而这些都是基于Consumer与Broker间的长连接的。长连接的维护是需要消耗系统资源的。



对比

- pull：需要应用去实现对关联Queue的遍历，实时性差；但便于应用控制消息的拉取
- push：封装了对关联Queue的遍历，实时性强，但会占用较多的系统资源

#### 8.4.2 消费模式

在 Apache RocketMQ 有两种消费模式，分别是：

- 集群消费模式：当使用集群消费模式时，RocketMQ 认为任意一条消息只需要被消费组内的任意一个消费者处理即可。
- 广播消费模式：当使用广播消费模式时，RocketMQ 会将每条消息推送给消费组所有的消费者，保证消息至少被每个消费者消费一次。



![image-20230215183657142](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151836220.png)

广播消费模式下，相同Consumer Group的每个Consumer实例都接收同一个Topic的全量消息。即每条消息都会被发送到Consumer Group中的每个Consumer。



![image-20230215183634646](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151836785.png)

集群消费模式下，相同Consumer Group的每个Consumer实例`平均分摊`同一个Topic的消息。即每条消息只会被发送到Consumer Group中的`某个`Consumer。

消费进度保存

- 广播模式：消费进度保存在consumer端。因为广播模式下consumer group中每个consumer都会消费所有消息，但它们的消费进度是不同。所以consumer各自保存各自的消费进度。
- 集群模式：消费进度保存在broker中。consumer group中的所有consumer共同消费同一个Topic中的消息，同一条消息只会被消费一次。消费进度会参与到了消费的负载均衡中，故消费进度是需要共享的。下图是broker中存放的各个Topic的各个Queue的消费进度。

#### 8.4.3 Rebalance机制

Rebalance机制讨论的前提是：集群消费。

什么是Rebalance

Rebalance即再均衡，指的是，将一个Topic下的多个Queue在同一个Consumer Group中的多个Consumer间进行重新分配的过程。

![image-20230213130955653](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131309703.png)

Rebalance机制的本意是为了提升消息的并行消费能力。例如，一个Topic下 5 个队列，在只有 1 个消费者的情况下，这个消费者将负责消费这 5 个队列的消息。如果此时我们增加一个消费者，那么就可以给其中一个消费者分配 2 个队列，给另一个分配 3 个队列，从而提升消息的并行消费能力。

Rebalance限制：

由于一个队列最多分配给一个消费者，因此当某个消费者组下的消费者实例数量大于队列的数量时，多余的消费者实例将分配不到任何队列。



Rebalance危害：

Rebalance的在提升消费能力的同时，也带来一些问题：

`消费暂停：`在只有一个Consumer时，其负责消费所有队列；在新增了一个Consumer后会触发Rebalance的发生。此时原Consumer就需要暂停部分队列的消费，等到这些队列分配给新的Consumer后，这些暂停消费的队列才能继续被消费。

`消费重复：`Consumer 在消费新分配给自己的队列时，必须接着之前Consumer 提交的消费进度的offset继续消费。然而默认情况下，offset是异步提交的，这个异步性导致提交到Broker的offset与Consumer实际消费的消息并不一致。这个不一致的差值就是可能会重复消费的消息。

> 同步提交：consumer提交了其消费完毕的一批消息的offset给broker后，需要等待broker的成功ACK。当收到ACK后，consumer才会继续获取并消费下一批消息。在等待ACK期间，consumer是阻塞的。
>
> 异步提交：consumer提交了其消费完毕的一批消息的offset给broker后，不需要等待broker的成功ACK。consumer可以直接获取并消费下一批消息。
>
> 对于一次性读取消息的数量，需要根据具体业务场景选择一个相对均衡的是很有必要的。因为数量过大，系统性能提升了，但产生重复消费的消息数量可能会增加；数量过小，系统性能会下降，但被重复消费的消息数量可能会减少。

`消费突刺：`由于Rebalance可能导致重复消费，如果需要重复消费的消息过多，或者因为Rebalance暂停时间过长从而导致积压了部分消息。那么有可能会导致在Rebalance结束之后瞬间需要消费很多消息。

导致的原因：

导致Rebalance产生的原因，无非就两个：消费者所订阅Topic的Queue数量发生变化，或消费者组中消费者的数量发生变化。

> 1 ）Queue数量发生变化的场景：
> Broker扩容或缩容
> Broker升级运维
> Broker与NameServer间的网络异常
> Queue扩容或缩容
> 2 ）消费者数量发生变化的场景：
> Consumer Group扩容或缩容
> Consumer升级运维
> Consumer与NameServer间网络异常

Rebalance过程：

在Broker中维护着多个Map集合，这些集合中动态存放着当前Topic中Queue的信息、Consumer Group中Consumer实例的信息。一旦发现消费者所订阅的Queue数量发生变化，或消费者组中消费者的数量发生变化，立即向Consumer Group中的每个实例发出Rebalance通知。

> TopicConågManager：key是topic名称，value是TopicConåg。TopicConåg中维护着该Topic中所有Queue的数据。
>
> ConsumerManager：key是Consumser Group Id，value是ConsumerGroupInfo。
> ConsumerGroupInfo中维护着该Group中所有Consumer实例数据。
>
> ConsumerOffsetManager：key为`Topic与订阅该Topic的Group的组合,即topic@group`，value是一个内层Map。内层Map的key为QueueId，内层Map的value为该Queue的消费进度offset。

Consumer实例在接收到通知后会采用Queue分配算法自己获取到相应的Queue，即由Consumer实例自主进行Rebalance。

#### 8.4.4 与Kafka对比

在Kafka中，一旦发现出现了Rebalance条件，Broker会调用Group Coordinator来完成Rebalance。Coordinator是Broker中的一个进程。Coordinator会在Consumer Group中选出一个Group Leader。由这个Leader根据自己本身组情况完成Partition分区的再分配。这个再分配结果会上报给Coordinator，并由Coordinator同步给Group中的所有Consumer实例。

Kafka中的Rebalance是由Consumer Leader完成的。而RocketMQ中的Rebalance是由每个Consumer自身完成的，Group中不存在Leader。



#### 8.4.5 Queue分配算法

一个Topic中的Queue只能由Consumer Group中的一个Consumer进行消费，而一个Consumer可以同时消费多个Queue中的消息。那么Queue与Consumer间的配对关系是如何确定的，即Queue要分配给哪个Consumer进行消费，也是有算法策略的。常见的有四种策略。这些策略是通过在创建Consumer时的构造器传进去的。

4种策略如下：

1、平均分配策略

![image-20230213132736201](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131327246.png)

该算法是要根据`avg = QueueCount / ConsumerCount`的计算结果进行分配的。如果能够整除，则按顺序将avg个Queue逐个分配Consumer；如果不能整除，则将多余出的Queue按照Consumer顺序逐个分配。

> 该算法即，先计算好每个Consumer应该分得几 个Queue，然后再依次将这些数量的Queue逐个分配个Consumer。



2、环形平均策略

![image-20230213132813784](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131328833.png)

环形平均算法是指，根据消费者的顺序，依次在由queue队列组成的环形图中逐个分配。

> 该算法不用事先计算每个Consumer需要分配几 个Queue，直接一个一个分即可。

3、一致性hash策略

![image-20230213132850583](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131328631.png)

该算法会将consumer的hash值作为Node节点存放到hash环上，然后将queue的hash值也放到hash环上，通过顺时针方向，距离queue最近的那个consumer就是该queue要分配的consumer。

> 该算法存在的问题：分配不均。

4、同机房策略

![image-20230213132915820](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131329868.png)

该算法会根据queue的部署机房位置和consumer的位置，过滤出当前consumer相同机房的queue。然后按照平均分配策略或环形平均策略对同机房queue进行分配。如果没有同机房queue，则按照平均分配策略或环形平均策略对所有queue进行分配。

对比：

一致性hash算法存在的问题：

两种平均分配策略的分配效率较高，一致性hash策略的较低。因为一致性hash算法较复杂。另外，一致性hash策略分配的结果也很大可能上存在不平均的情况。

一致性hash算法存在的意义：

其可以有效减少由于消费者组扩容或缩容所带来的大量的Rebalance。

一致性hash算法的应用场景：

Consumer数量变化较频繁的场景。



#### 8.4.6 至少一次原则

RocketMQ有一个原则：每条消息必须要被`成功消费`一次。

那么什么是成功消费呢？Consumer在消费完消息后会向其`消费进度记录器`提交其消费消息的offset，offset被成功记录到记录器中，那么这条消费就被成功消费了。

> 什么是消费进度记录器？
> 对于广播消费模式来说，Consumer本身就是消费进度记录器。
> 对于集群消费模式来说，Broker是消费进度记录器。



### 8.5 订阅关系一致性

订阅关系的一致性指的是，同一个消费者组（Group ID相同）下所有Consumer实例所订阅的Topic与Tag及对消息的处理逻辑必须完全一致。否则，消息消费的逻辑就会混乱，甚至导致消息丢失。



### 8.6 offset管理

> 这里的offset指的是Consumer的消费进度offset。

消费进度offset是用来记录每个Queue的不同消费组的消费进度的。根据消费进度记录器的不同，可以分为两种模式：本地模式和远程模式。

#### 8.6.1 本地管理模式

当消费模式为`广播消费`时，offset使用本地模式存储。因为每条消息会被所有的消费者消费，每个消费者管理自己的消费进度，各个消费者之间不存在消费进度的交集。

Consumer在广播消费模式下offset相关数据以json的形式持久化到Consumer本地磁盘文件中，默认文件路径为当前用户主目录下的`.rocketmq_offsets/${clientId}/${group}/Offsets.json`。其中${clientId}为当前消费者id，默认为ip@DEFAULT；${group}为消费者组名称。



#### 8.6.2 远程管理模式

消费模式为`集群消费`时，offset使用远程模式管理。因为所有Cosnumer实例对消息采用的是均衡消费，所有Consumer共享Queue的消费进度。

Consumer在集群消费模式下offset相关数据以json的形式持久化到Broker磁盘文件中，文件路径为当前用户主目录下的`store/config/consumerOffset.json`。

Broker启动时会加载这个文件，并写入到一个双层Map（ConsumerOffsetManager）。外层map的key为topic@group，value为内层map。内层map的key为queueId，value为offset。当发生Rebalance时，新的Consumer会从该Map中获取到相应的数据来继续消费。

集群模式下offset采用远程管理模式，主要是为了保证Rebalance机制。



#### 8.6.3 用途

消费者是如何从最开始持续消费消息的？消费者要消费的第一条消息的起始位置是用户自己通过consumer.setConsumeFromWhere()方法指定的。

在Consumer启动后，其要消费的第一条消息的起始位置常用的有三种，这三种位置可以通过枚举类型常量设置。这个枚举类型为ConsumeFromWhere。

![image-20230213133311152](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131333210.png)

当消费完一批消息后，Consumer会提交其消费进度offset给Broker，Broker在收到消费进度后会将其更新到那个双层Map（ConsumerOffsetManager）及consumerOffset.json文件中，然后向该Consumer进行ACK，而ACK内容中包含三项数据：当前消费队列的最小offset（minOffset）、最大offset（maxOffset）、及下次消费的起始offset（nextBeginOffset）。

#### 8.6.4 重试队列

当rocketMQ对消息的消费出现异常时，会将发生异常的消息的offset提交到Broker中的重试队列。系统在发生消息消费异常时会为当前的topic@group创建一个重试队列，该队列以%RETRY%开头，到达重试时间后进行消费重试

#### 8.6.5 同步提交与异步提交

集群消费模式下，Consumer消费完消息后会向Broker提交消费进度offset，其提交方式分为两种：

`同步提交`：消费者在消费完一批消息后会向broker提交这些消息的offset，然后等待broker的成功响应。若在等待超时之前收到了成功响应，则继续读取下一批消息进行消费（从ACK中获取nextBeginOffset）。若没有收到响应，则会重新提交，直到获取到响应。而在这个等待过程中，消费者是阻塞的。其严重影响了消费者的吞吐量。

`异步提交`：消费者在消费完一批消息后向broker提交offset，但无需等待Broker的成功响应，可以继续读取并消费下一批消息。这种方式增加了消费者的吞吐量。但需要注意，broker在收到提交的offset后，还是会向消费者进行响应的。可能还没有收到ACK，此时Consumer会从Broker中直接获取nextBeginOffset。



### 8.7 消费幂等

#### 8.7.1 什么是消费幂等

当出现消费者对某条消息重复消费的情况时，重复消费的结果与消费一次的结果是相同的，并且多次消费并未对业务系统产生任何负面影响，那么这个消费过程就是消费幂等的。

> 幂等：若某操作执行多次与执行一次对系统产生的影响是相同的，则称该操作是幂等的。

在互联网应用中，尤其在网络不稳定的情况下，消息很有可能会出现重复发送或重复消费。如果重复的消息可能会影响业务处理，那么就应该对消息做幂等处理。

#### 8.7.2 场景分析

什么情况下可能会出现消息被重复消费呢？最常见的有以下三种情况：

1、发送时消息重复

当一条消息已被成功发送到Broker并完成持久化，此时出现了网络闪断，从而导致Broker对Producer应答失败。 如果此时Producer意识到消息发送失败并尝试再次发送消息，此时Broker中就可能会出现两条内容相同并且Message ID也相同的消息，那么后续Consumer就一定会消费两次该消息。



2、消费时消息重复

消息已投递到Consumer并完成业务处理，当Consumer给Broker反馈应答时网络闪断，Broker没有接收到消费成功响应。为了保证消息`至少被消费一次`的原则，Broker将在网络恢复后再次尝试投递之前已被处理过的消息。此时消费者就会收到与之前处理过的内容相同、Message ID也相同的消息。

3、Rebalance重复

当Consumer Group中的Consumer数量发生变化时，或其订阅的Topic的Queue数量发生变化时，会触发Rebalance，此时Consumer可能会收到曾经被消费过的消息。

#### 8.7.3 通用解决方案

对于常见的系统，幂等性操作的通用性解决方案是：

- 1. 首先通过缓存去重。在缓存中如果已经存在了某幂等令牌，则说明本次操作是重复性操作；若缓存没有命中，则进入下一步。
- 1. 在唯一性处理之前，先在数据库中查询幂等令牌作为索引的数据是否存在。若存在，则说明本次操作为重复性操作；若不存在，则进入下一步。
- 1. 在同一事务中完成三项操作：唯一性处理后，将幂等令牌写入到缓存，并将幂等令牌作为唯一索引的数据写入到DB中。

> 第 1 步已经判断过是否是重复性操作了，为什么第 2 步还要再次判断？能够进入第 2 步，说明已经不是重复操作了，第 2 次判断是否重复？
>
> 当然不重复。一般缓存中的数据是具有有效期的。缓存中数据的有效期一旦过期，就是发生缓存穿透，使请求直接就到达了DBMS。

以支付场景为例：

- 1. 当支付请求到达后，首先在Redis缓存中却获取key为支付流水号的缓存value。若value不空，则说明本次支付是重复操作，业务系统直接返回调用侧重复支付标识；若value为空，则进入下一步操作

- 1. 到DBMS中根据支付流水号查询是否存在相应实例。若存在，则说明本次支付是重复操作，业务系统直接返回调用侧重复支付标识；若不存在，则说明本次操作是首次操作，进入下一步完成唯一性处理

- 1. 在分布式事务中完成三项操作：

  - 完成支付任务
  - 将当前支付流水号作为key，任意字符串作为value，通过set(key, value, expireTime)将数据写入到Redis缓存
  - 将当前支付流水号作为主键，与其它相关数据共同写入到DBMS

#### 8.7.4 消费幂等的实现

费幂等的解决方案很简单：为消息指定不会重复的唯一标识。因为Message ID有可能出现重复的情况，所以真正安全的幂等处理，不建议以Message ID作为处理依据。最好的方式是以业务唯一标识作为幂等处理的关键依据，而业务的唯一标识可以通过消息Key设置。

以支付场景为例，可以将消息的Key设置为订单号，作为幂等处理的依据。具体代码示例如下：

```java
Message message = new Message();
message.setKey("ORDERID_100");
SendResult sendResult = producer.send(message);Copy to clipboardErrorCopied
```

消费者收到消息时可以根据消息的Key即订单号来实现消费幂等：

```java
consumer.registerMessageListener(new MessageListenerConcurrently() {
    @Override
    public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt>msgs,ConsumeConcurrentlyContext context) {
        for(MessageExt msg:msgs){
            String key = msg.getKeys();
            // 根据业务唯一标识Key做幂等处理
            // ......
            }
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
    }
});Copy to clipboardErrorCopied
```

> RocketMQ能够保证消息不丢失，但不能保证消息不重复。



### 8.8 消息堆积和消息延迟

#### 8.8.1 概念

消息处理流程中，如果Consumer的消费速度跟不上Producer的发送速度，MQ中未处理的消息会越来越多（进的多出的少），这部分消息就被称为`堆积消息`。消息出现堆积进而会造成消息的`消费延迟`。
以下场景需要重点关注消息堆积和消费延迟问题：

- 业务系统上下游能力不匹配造成的持续堆积，且无法自行恢复。
- 业务系统对消息的消费实时性要求较高，即使是短暂的堆积造成的消费延迟也无法接受。

#### 8.8.2 产生原因分析

![image-20230213134338316](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131343368.png)

**消息拉取**

Consumer通过长轮询Pull模式批量拉取的方式从服务端获取消息，将拉取到的消息缓存到本地缓冲队列中。对于拉取式消费，在内网环境下会有很高的吞吐量，所以这一阶段一般不会成为消息堆积的瓶颈。

> 一个单线程单分区的低规格主机(Consumer，4C8G)，其可达到几万的TPS。如果是多个分区多个线程，则可以轻松达到几十万的TPS。

**消息消费**

Consumer将本地缓存的消息提交到消费线程中，使用业务消费逻辑对消息进行处理，处理完毕后获取到一个结果。这是真正的消息消费过程。此时Consumer的消费能力就完全依赖于消息的`消费耗时`和`消费并发度`了。如果由于业务处理逻辑复杂等原因，导致处理单条消息的耗时较长，则整体的消息吞吐量肯定不会高，此时就会导致Consumer本地缓冲队列达到上限，停止从服务端拉取消息。

结论：

消息堆积的主要瓶颈在于客户端的消费能力，而消费能力由`消费耗时`和`消费并发度`决定。注意，消费耗时的优先级要高于消费并发度。即在保证了消费耗时的合理性前提下，再考虑消费并发度问题。

#### 8.8.3 消费耗时

影响消息处理时长的主要因素是代码逻辑。而代码逻辑中可能会影响处理时长代码主要有两种类型：`CPU内部计算型代码`和`外部I/O操作型代码`。

通常情况下代码中如果没有复杂的递归和循环的话，内部计算耗时相对外部I/O操作来说几乎可以忽略。所以外部IO型代码是影响消息处理时长的主要症结所在。

> 外部IO操作型代码举例：
>
> 1)读写外部数据库，例如对远程MySQL的访问
> 2)读写外部缓存系统，例如对远程Redis的访问
> 3)下游系统调用，例如Dubbo的RPC远程调用，Spring Cloud的对下游系统的Http接口调用
>
> 关于下游系统调用逻辑需要进行提前梳理，掌握每个调用操作预期的耗时，这样做是为了能够判断消费逻辑中IO操作的耗时是否合理。通常消息堆积是由于下游系统出现了`服务异常`或`达到了DBMS容量限制`，导致消费耗时增加。
>
> 服务异常，并不仅仅是系统中出现的类似 500 这样的代码错误，而可能是更加隐蔽的问题。例如，网络带宽问题。
>
> 达到了DBMS容量限制，其也会引发消息的消费耗时增加。

#### 8.8.4 消费并发度

一般情况下，消费者端的消费并发度由单节点线程数和节点数量共同决定，其值为单节点线程数*节点数量。不过，通常需要优先调整单节点的线程数，若单机硬件资源达到了上限，则需要通过横向扩展来提高消费并发度。

> 单节点线程数，即单个Consumer所包含的线程数量
>
> 节点数量，即Consumer Group所包含的Consumer数量
>
> 对于普通消息、延时消息及事务消息，并发度计算都是单节点线程数*节点数量。但对于顺序消息则是不同的。顺序消息的消费并发度等于Topic的Queue分区数量。
>
> 1 ）全局顺序消息：该类型消息的Topic只有一个Queue分区。其可以保证该Topic的所有消息被顺序消费。为了保证这个全局顺序性，Consumer Group中在同一时刻只能有一个Consumer的一个线程进行消费。所以其并发度为 1 。
>
> 2 ）分区顺序消息：该类型消息的Topic有多个Queue分区。其仅可以保证该Topic的每个Queue分区中的消息被顺序消费，不能保证整个Topic中消息的顺序消费。为了保证这个分区顺序性，每个Queue分区中的消息在Consumer Group中的同一时刻只能有一个Consumer的一个线程进行消费。即，在同一时刻最多会出现多个Queue分蘖有多个Consumer的多个线程并行消费。所以其并发度为Topic的分区数量。



#### 8.8.5 单机线程计算

对于一台主机中线程池中线程数的设置需要谨慎，不能盲目直接调大线程数，设置过大的线程数反而会带来大量的线程切换的开销。理想环境下单节点的最优线程数计算模型为：C *（T1 + T2）/ T1。

- C：CPU内核数
- T1：CPU内部逻辑计算耗时
- T2：外部IO操作耗时

> 最优线程数 = C *（T1 + T2）/ T1 = C * T1/T1 + C * T2/T1 = C + C * T2/T1

> 注意，该计算出的数值是理想状态下的理论数据，在生产环境中，不建议直接使用。而是根据当前环境，先设置一个比该值小的数值然后观察其压测效果，然后再根据效果逐步调大线程数，直至找到在该环境中性能最佳时的值。

#### 8.5.6 如何避免

为了避免在业务使用时出现非预期的消息堆积和消费延迟问题，需要在前期设计阶段对整个业务逻辑进行完善的排查和梳理。其中最重要的就是`梳理消息的消费耗时`和`设置消息消费的并发度`。

**梳理消息的消费耗时**

通过压测获取消息的消费耗时，并对耗时较高的操作的代码逻辑进行分析。梳理消息的消费耗时需要关注以下信息：

- 消息消费逻辑的计算复杂度是否过高，代码是否存在无限循环和递归等缺陷。
- 消息消费逻辑中的I/O操作是否是必须的，能否用本地缓存等方案规避。
- 消费逻辑中的复杂耗时的操作是否可以做异步化处理。如果可以，是否会造成逻辑错乱。

**设置消费并发度**

对于消息消费并发度的计算，可以通过以下两步实施：

- 逐步调大单个Consumer节点的线程数，并观测节点的系统指标，得到单个节点最优的消费线程数和消息吞吐量。
- 根据上下游链路的流量峰值计算出需要设置的节点数

> 节点数 = 流量峰值 / 单个节点消息吞吐量



### 8.9 消息的清理

消息被消费过后会被清理掉吗？不会的。

消息是被顺序存储在commitlog文件的，且消息大小不定长，所以消息的清理是不可能以消息为单位进行清理的，而是以commitlog文件为单位进行清理的。否则会急剧下降清理效率，并实现逻辑复杂。

commitlog文件存在一个过期时间，默认为 72 小时，即三天。除了用户手动清理外，在以下情况下也会被自动清理，无论文件中的消息是否被消费过：

- 文件过期，且到达清理时间点（默认为凌晨 4 点）后，自动清理过期文件
- 文件过期，且磁盘空间占用率已达过期清理警戒线（默认75%）后，无论是否达到清理时间点，都会自动清理过期文件
- 磁盘占用率达到清理警戒线（默认85%）后，开始按照设定好的规则清理文件，无论是否过期。默认会从最老的文件开始清理
- 磁盘占用率达到系统危险警戒线（默认90%）后，Broker将拒绝消息写入

> 需要注意以下几点：
> 1 ）对于RocketMQ系统来说，删除一个1G大小的文件，是一个压力巨大的IO操作。在删除过程中，系统性能会骤然下降。所以，其默认清理时间点为凌晨 4 点，访问量最小的时间。也正因如果，我们要保障磁盘空间的空闲率，不要使系统出现在其它时间点删除commitlog文件的情况。
> 2 ）官方建议RocketMQ服务的Linux文件系统采用ext4。因为对于文件删除操作，ext4要比ext3性能更好



## 9 RocketMQ应用

### 9.1 普通消息

#### 9.1.1 消息发送分类

Producer对于消息的发送方式也有多种选择，不同的方式会产生不同的系统效果。

1、同步发送

同步发送消息是指，Producer发出一条消息后，会在收到MQ返回的ACK之后才发下一条消息。该方式的消息可靠性最高，但消息发送效率太低。

![image-20230213134857295](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131348345.png)



2、异步发送

异步发送消息是指，Producer发出消息后无需等待MQ返回ACK，直接发送下一条消息。该方式的消息可靠性可以得到保障，消息发送效率也可以。

![image-20230213134913822](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131349873.png)

3、单向发送消息

单向发送消息是指，Producer仅负责发送消息，不等待、不处理MQ的ACK。该发送方式时MQ也不返回ACK。该方式的消息发送效率最高，但消息可靠性较差。

![image-20230213134944669](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302131349720.png)

#### 9.1.2 代码举例



同步发送

```
package producer;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午2:42:09
* @DESCRIPTION :同步发送者
*/
public class SyncProducer {
	public static void main(String[] args) throws Exception {
    // 创建一个producer，参数为Producer Group名称
    DefaultMQProducer producer = new DefaultMQProducer("pg");
    // 指定nameServer地址
    producer.setNamesrvAddr("192.168.133.103:9876");
    // 设置当发送失败时重试发送的次数，默认为 2 次
    producer.setRetryTimesWhenSendFailed( 3 );
    // 设置发送超时时限为5s，默认3s
    producer.setSendMsgTimeout( 5000 );
    // 开启生产者
    producer.start();
    // 生产并发送 100 条消息
    for (int i = 0 ; i < 100 ; i++) {
        byte[] body = ("Hi," + i).getBytes();
        // someTopic是主题
        Message msg = new Message("someTopic", "someTag", body);
        // 为消息指定key
        msg.setKeys("key-" + i);
        // 发送消息
        SendResult sendResult = producer.send(msg);
        System.out.println(sendResult);
    }
    // 关闭producer
    producer.shutdown();
}
}

```

![image-20230215154254910](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151542997.png)

```
// 消息发送的状态
public enum SendStatus {
    SEND_OK, // 发送成功
    FLUSH_DISK_TIMEOUT,  // 刷盘超时。当Broker设置的刷盘策略为同步刷盘时才可能出现这种异常状态。异步刷盘不会出现
    FLUSH_SLAVE_TIMEOUT, // Slave同步超时。当Broker集群设置的Master-Slave的复制方式为同步复制时才可能出现这种异常状态。异步复制不会出现
    SLAVE_NOT_AVAILABLE, // 没有可用的Slave。当Broker集群设置为Master-Slave的复制方式为同步复制时才可能出现这种异常状态。异步复制不会出现
}

```

异步发送

```
package producer;

import java.util.concurrent.TimeUnit;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.client.producer.SendCallback;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午2:44:58
* @DESCRIPTION :异步生产者
*/
public class AsyncProducer {
	public static void main(String[] args) throws Exception {
    DefaultMQProducer producer = new DefaultMQProducer("pg");
    producer.setNamesrvAddr("192.168.133.103:9876");
    // 指定异步发送失败后不进行重试发送
    producer.setRetryTimesWhenSendAsyncFailed( 0 );
    // 指定新创建的Topic的Queue数量为 2 ，默认为 4
    producer.setDefaultTopicQueueNums( 2 );

    producer.start();

    for (int i = 0 ; i < 100 ; i++) {
        byte[] body = ("Hi," + i).getBytes();
        try {
				Message msg = new Message("myTopicA", "myTag", body);
        // 异步发送。指定回调
        producer.send(msg, new SendCallback() {
                // 当producer接收到MQ发送来的ACK后就会触发该回调方法的执行
                
                public void onSuccess(SendResult sendResult) {
                System.out.println(sendResult);
                }

                
                public void onException(Throwable e) {
                e.printStackTrace();
                }
            });
        } catch (Exception e) {
            e.printStackTrace();
        }
    } // end-for
    // sleep一会儿
    // 由于采用的是异步发送，所以若这里不sleep，
    // 则消息还未发送就会将producer给关闭，报错
    TimeUnit.SECONDS.sleep( 3 );
    producer.shutdown();
}
}

```

![image-20230215154526320](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151545411.png)

单向生产者

```
package general;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午2:46:19
* @DESCRIPTION :单向生产者
*/
public class OnewayProducer {
	
	public static void main(String[] args) throws Exception{
    DefaultMQProducer producer = new DefaultMQProducer("pg");
    producer.setNamesrvAddr("192.168.133.103:9876");
    producer.start();

    for (int i = 0 ; i < 10 ; i++) {
        byte[] body = ("Hi," + i).getBytes();
        Message msg = new Message("single", "someTag", body);
        // 单向发送
        producer.sendOneway(msg);
    }
    producer.shutdown();
    System.out.println("producer shutdown");
}

}

```

![image-20230215154954086](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151549169.png)

消费者

```
package general;

import java.util.List;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;
import org.apache.rocketmq.common.message.MessageExt;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午3:51:13
* @DESCRIPTION :
*/
public class SomeConsumer {
	 public static void main(String[] args) throws MQClientException {
     // 定义一个pull消费者
     // DefaultLitePullConsumer consumer = new DefaultLitePullConsumer("cg");
     // 定义一个push消费者
     DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
     // 指定nameServer
     consumer.setNamesrvAddr("192.168.133.103:9876");
     // 指定从第一条消息开始消费
     consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
     // 指定消费topic与tag
     consumer.subscribe("someTopic", "*");
     // 指定采用“广播模式”进行消费，默认为“集群模式”
     // consumer.setMessageModel(MessageModel.BROADCASTING);
     // 注册消息监听器
     consumer.registerMessageListener(new MessageListenerConcurrently() {
         // 一旦broker中有了其订阅的消息就会触发该方法的执行，
         // 其返回值为当前consumer消费的状态
         public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context) {
             // 逐条消费消息
             for (MessageExt msg : msgs) {
                 System.out.println(msg);
             }
             // 返回消费状态：消费成功
             return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
         }
     });
     // 开启消费者消费
     consumer.start();
     System.out.println("Consumer Started");
 }
}

```

![image-20230215155405770](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151554871.png)

### 9.2 顺序消息

#### 9.2.1 概念

顺序消息指的是，严格按照消息的`发送顺序`进行`消费`的消息(FIFO)。

默认情况下生产者会把消息以Round Robin轮询方式发送到不同的Queue分区队列；而消费消息时会从多个Queue上拉取消息，这种情况下的发送和消费是不能保证顺序的。如果将消息仅发送到同一个Queue中，消费时也只从这个Queue上拉取消息，就严格保证了消息的顺序性。

#### 9.2.2 为什么需要顺序消息

例如，现在有TOPIC `ORDER_STATUS`(订单状态)，其下有 4 个Queue队列，该Topic中的不同消息用于描述当前订单的不同状态。假设订单有状态：未支付、已支付、发货中、发货成功、发货失败。

根据以上订单状态，生产者从时序上可以生成如下几个消息：

`订单T0000001:未支付 --> 订单T0000001:已支付 --> 订单T0000001:发货中 --> 订单T0000001:发货失败

消息发送到MQ中之后，Queue的选择如果采用轮询策略，消息在MQ的存储可能如下：

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151555956.png)

这种情况下，我们希望Consumer消费消息的顺序和我们发送是一致的，然而上述MQ的投递和消费方式，我们无法保证顺序是正确的。对于顺序异常的消息，Consumer即使设置有一定的状态容错，也不能完全处理好这么多种随机出现组合情况。

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151555636.png)

基于上述的情况，可以设计如下方案：对于相同订单号的消息，通过一定的策略，将其放置在一个Queue中，然后消费者再采用一定的策略（例如，一个线程独立处理一个queue，保证处理消息的顺序性），能够保证消费的顺序性。

#### 9.2.3 有序分类

根据有序范围的不同，RocketMQ可以严格地保证两种消息的有序性：分区有序与全局有序。

全局有序：

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151556798.png)

当发送和消费参与的Queue只有一个时所保证的有序是整个Topic中消息的顺序， 称为`全局有序`。

> 在创建Topic时指定Queue的数量。有三种指定方式：
>
> 1 ）在代码中创建Producer时，可以指定其自动创建的Topic的Queue数量
>
> 2 ）在RocketMQ可视化控制台中手动创建Topic时指定Queue数量
>
> 3 ）使用mqadmin命令手动创建Topic时指定Queue数量

分区有序：

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151557869.png)

如果有多个Queue参与，其仅可保证在该Queue分区队列上的消息顺序，则称为`分区有序`。

> 如何实现Queue的选择？在定义Producer时我们可以指定消息队列选择器，而这个选择器是我们自己实现了MessageQueueSelector接口定义的。
>
> 在定义选择器的选择算法时，一般需要使用选择key。这个选择key可以是消息key也可以是其它数据。但无论谁做选择key，都不能重复，都是唯一的。
>
> 一般性的选择算法是，让选择key（或其hash值）与该Topic所包含的Queue的数量取模，其结果即为选择出的Queue的QueueId。
>
> 取模算法存在一个问题：不同选择key与Queue数量取模结果可能会是相同的，即不同选择key的消息可能会出现在相同的Queue，即同一个Consuemr可能会消费到不同选择key的消息。这个问题如何解决？一般性的作法是，从消息中获取到选择key，对其进行判断。若是当前Consumer需要消费的消息，则直接消费，否则，什么也不做。这种做法要求选择key要能够随着消息一起被Consumer获取到。此时使用消息key作为选择key是比较好的做法。
>
> 以上做法会不会出现如下新的问题呢？不属于那个Consumer的消息被拉取走了，那么应该消费该消息的Consumer是否还能再消费该消息呢？同一个Queue中的消息不可能被同一个Group中的不同Consumer同时消费。所以，消费现一个Queue的不同选择key的消息的Consumer一定属于不同的Group。而不同的Group中的Consumer间的消费是相互隔离的，互不影响的

#### 9.2.4 代码

```
package order;

import java.util.List;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.client.producer.MessageQueueSelector;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.common.message.Message;
import org.apache.rocketmq.common.message.MessageQueue;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:08:17
* @DESCRIPTION : 顺序一致性消息
*/
public class OrderedProducer {
	 public static void main(String[] args) throws Exception {
     DefaultMQProducer producer = new DefaultMQProducer("pg");
     producer.setNamesrvAddr("192.168.133.103:9876");
     // 若全局有序，需要设置1
     //producer.setDefaultTopicQueueNums(1);
     producer.start();
     for (int i = 0 ; i < 100 ; i++) {
         Integer orderId = i;
         byte[] body = ("Hi," + i).getBytes();
         Message msg = new Message("TopicA", "TagA", body);
         SendResult sendResult = producer.send(msg, new MessageQueueSelector() {
             
             public MessageQueue select(List<MessageQueue> mqs,Message msg, Object arg) {
                     Integer id = (Integer) arg;
                     int index = id % mqs.size();
                     return mqs.get(index);
                 }
             }, orderId);
         System.out.println(sendResult);
     }
     producer.shutdown();
 }
}

```



```
MessageQueueSelector的接口如下：

public interface MessageQueueSelector {
    MessageQueue select(final List<MessageQueue> mqs, final Message msg, final Object arg);
}

其中 mqs 是可以发送的队列，msg是消息，arg是上述send接口中传入的Object对象，返回的是该消息需要发送到的队列。上述例子里，是以orderId作为分区分类标准，对所有队列个数取余，来对将相同orderId的消息发送到同一个队列中。

生产环境中建议选择最细粒度的分区键进行拆分，例如，将订单ID、用户ID作为分区键关键字，可实现同一终端用户的消息按照顺序处理，不同用户的消息无需保证顺序。
```



### 9.3 延时消息

#### 9.3.1 概念

当消息写入到Broker后，在指定的时长后才可被消费处理的消息，称为延时消息。

采用RocketMQ的延时消息可以实现`定时任务`的功能，而无需使用定时器。典型的应用场景是，电商交易中超时未支付关闭订单的场景， 12306 平台订票超时未支付取消订票的场景。

> 在电商平台中，订单创建时会发送一条延迟消息。这条消息将会在 30 分钟后投递给后台业务系统（Consumer），后台业务系统收到该消息后会判断对应的订单是否已经完成支付。如果未完成，则取消订单，将商品再次放回到库存；如果完成支付，则忽略。
>
> 在 12306 平台中，车票预订成功后就会发送一条延迟消息。这条消息将会在 45 分钟后投递给后台业务系统（Consumer），后台业务系统收到该消息后会判断对应的订单是否已经完成支付。如果未完成，则取消预订，将车票再次放回到票池；如果完成支付，则忽略。

#### 9.3.2 延时等级

延时消息的延迟时长`不支持随意时长`的延迟，是通过特定的延迟等级来指定的。延时等级定义在RocketMQ服务端的MessageStoreConfig类中的如下变量中：

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151619399.png)

即，若指定的延时等级为 3 ，则表示延迟时长为10s，即延迟等级是从 1 开始计数的。

当然，如果需要自定义的延时等级，可以通过在broker加载的配置中新增如下配置（例如下面增加了 1天这个等级1d）。配置文件在RocketMQ安装目录下的conf目录中。

```
messageDelayLevel = 1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h 1d
```



#### 9.3.3 延时原理

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151619857.png)

修改消息

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151619836.png)

Producer将消息发送到Broker后，Broker会首先将消息写入到commitlog文件，然后需要将其分发到相应的consumequeue。不过，在分发之前，系统会先判断消息中是否带有延时等级。若没有，则直接正常分发；若有则需要经历一个复杂的过程：

- 修改消息的Topic为SCHEDULE_TOPIC_XXXX
- 根据延时等级，在consumequeue目录中SCHEDULE_TOPIC_XXXX主题下创建出相应的queueId目录与consumequeue文件（如果没有这些目录与文件的话）。

> 延迟等级delayLevel与queueId的对应关系为queueId = delayLevel -1
> 需要注意，在创建queueId目录时，并不是一次性地将所有延迟等级对应的目录全部创建完毕，而是用到哪个延迟等级创建哪个目录

- 修改消息索引单元内容。索引单元中的Message Tag HashCode部分原本存放的是消息的Tag的Hash值。现修改为消息的`投递时间`。投递时间是指该消息被重新修改为原Topic后再次被写入到commitlog中的时间。`投递时间 = 消息存储时间 + 延时等级时间`。消息存储时间指的是消息被发送到Broker时的时间戳。
- 将消息索引写入到SCHEDULE_TOPIC_XXXX主题下相应的consumequeue中

SCHEDULE_TOPIC_XXXX目录中各个延时等级Queue中的消息是如何排序的？

是按照消息投递时间排序的。一个Broker中同一等级的所有延时消息会被写入到consumequeue目录中SCHEDULE_TOPIC_XXXX目录下相同Queue中。即一个Queue中消息投递时间的延迟等级时间是相同的。那么投递时间就取决于于`消息存储时间`了。即按照消息被发送到Broker的时间进行排序的。

#### 9.3.4 投递延时消息

Broker内部有一个延迟消息服务类ScheuleMessageService，其会消费SCHEDULE_TOPIC_XXXX中的消息，即按照每条消息的投递时间，将延时消息投递到目标Topic中。不过，在投递之前会从commitlog中将原来写入的消息再次读出，并将其原来的延时等级设置为 0 ，即原消息变为了一条不延迟的普通消息。然后再次将消息投递到目标Topic中。

> ScheuleMessageService在Broker启动时，会创建并启动一个定时器TImer，用于执行相应的定时任务。系统会根据延时等级的个数，定义相应数量的TimerTask，每个TimerTask负责一个延迟等级消息的消费与投递。每个TimerTask都会检 测相应Queue队列的第一条消息是否到期。若第一条消息未到期，则后面的所有消息更不会到期（消息是按照投递时间排序的）；若第一条消息到期了，则将该消息投递到目标Topic，即消费该消息。

延迟消息服务类ScheuleMessageService将延迟消息再次发送给了commitlog，并再次形成新的消息索引条目，分发到相应Queue。

> 这其实就是一次普通消息发送。只不过这次的消息Producer是延迟消息服务类ScheuleMessageService。

#### 9.3.5 代码举例

```
package delay;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;
import org.apache.rocketmq.common.message.MessageExt;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:23:31
* @DESCRIPTION :延时消费者
*/
public class OtherConsumer {
	public static void main(String[] args) throws MQClientException {
    DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
    consumer.setNamesrvAddr("192.168.133.103:9876");
    consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET );
    consumer.subscribe("TopicB", "*");
    consumer.registerMessageListener(new MessageListenerConcurrently() {
       
        public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context) {
        for (MessageExt msg : msgs) {
                // 输出消息被消费的时间
                System.out.print(new SimpleDateFormat("mm:ss").format(new Date()));
                System.out.println(" ," + msg);
            }
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        }
    });
    consumer.start();
    System.out.println("Consumer Started");
}

}

```

```
package delay;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:22:18
* @DESCRIPTION :延时消息
*/
public class DelayProducer {

	 public static void main(String[] args) throws Exception {
     DefaultMQProducer producer = new DefaultMQProducer("pg");
     producer.setNamesrvAddr("192.168.133.103:9876");
     producer.start();
     for (int i = 0 ; i < 10 ; i++) {
         byte[] body = ("Hi," + i).getBytes();
         Message msg = new Message("TopicB", "someTag", body);
         // 指定消息延迟等级为 3 级，即延迟10s
         msg.setDelayTimeLevel(3);
         SendResult sendResult = producer.send(msg);
         // 输出消息被发送的时间
         System.out.print(new SimpleDateFormat("mm:ss").format(new Date()));
         System.out.println(" ," + sendResult);
     }
     producer.shutdown();
 }
	
}

```

先启动消费者，然后执行发送消息。等待10秒看消费者。



### 9.4 事务消息

在一些对数据一致性有强需求的场景，可以用 Apache RocketMQ 事务消息来解决，从而保证上下游数据的一致性。

这里的一个需求场景是：工行用户A向建行用户B转账 1 万元。

我们可以使用同步消息来处理该需求场景：

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151627567.png)

- 1. 工行系统发送一个给B增款 1 万元的同步消息M给Broker
- 1. 消息被Broker成功接收后，向工行系统发送成功ACK
- 1. 工行系统收到成功ACK后从用户A中扣款 1 万元
- 1. 建行系统从Broker中获取到消息M
- 1. 建行系统消费消息M，即向用户B中增加 1 万元

> 这其中是有问题的：若第 3 步中的扣款操作失败，但消息已经成功发送到了Broker。对于MQ来说，只要消息写入成功，那么这个消息就可以被消费。此时建行系统中用户B增加了 1 万元。出现了数据不一致问题。

#### 9.4.1 解决思路

解决思路是，让第 1 、 2 、 3 步具有原子性，要么全部成功，要么全部失败。即消息发送成功后，必须要保证扣款成功。如果扣款失败，则回滚发送成功的消息。而该思路即使用`事务消息`。这里要使用`分布式事务`解决方案。

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151628000.png)

场景

- 1. 事务管理器TM向事务协调器TC发起指令，开启全局事务
- 1. 工行系统发一个给B增款 1 万元的事务消息M给TC
- 1. TC会向Broker发送半事务消息prepareHalf，将消息M预提交到Broker。此时的建行系统是看不到Broker中的消息M的
- 1. Broker会将预提交执行结果Report给TC。
- 1. 如果预提交失败，则TC会向TM上报预提交失败的响应，全局事务结束；如果预提交成功，TC会调用工行系统的回调操作，去完成工行用户A的预扣款 1 万元的操作
- 1. 工行系统会向TC发送预扣款执行结果，即本地事务的执行状态
- 1. TC收到预扣款执行结果后，会将结果上报给TM。

> 预扣款执行结果存在三种可能性：

```java
// 描述本地事务执行状态
public enum LocalTransactionState {
    COMMIT_MESSAGE,  // 本地事务执行成功
    ROLLBACK_MESSAGE,  // 本地事务执行失败
    UNKNOW,  // 不确定，表示需要进行回查以确定本地事务的执行结果
}Copy to clipboardErrorCopied
```

- 1. TM会根据上报结果向TC发出不同的确认指令

  - 若预扣款成功（本地事务状态为COMMIT_MESSAGE），则TM向TC发送Global Commit指令
  - 若预扣款失败（本地事务状态为ROLLBACK_MESSAGE），则TM向TC发送Global Rollback指令
  - 若现未知状态（本地事务状态为UNKNOW），则会触发工行系统的本地事务状态`回查操作`。回查操作会将回查结果，即COMMIT_MESSAGE或ROLLBACK_MESSAGE Report给TC。TC将结果上报给TM，TM会再向TC发送最终确认指令Global Commit或Global Rollback

- 1. TC在接收到指令后会向Broker与工行系统发出确认指令

  - TC接收的若是Global Commit指令，则向Broker与工行系统发送Branch Commit指令。此时Broker中的消息M才可被建行系统看到；此时的工行用户A中的扣款操作才真正被确认
  - TC接收到的若是Global Rollback指令，则向Broker与工行系统发送Branch Rollback指令。此时Broker中的消息M将被撤销；工行用户A中的扣款操作将被回滚



#### 9.4.2 事务的概念

分布式事务：

对于分布式事务，通俗地说就是，一次操作由若干分支操作组成，这些分支操作分属不同应用，分布在不同服务器上。分布式事务需要保证这些分支操作要么全部成功，要么全部失败。分布式事务与普通事务一样，就是为了保证操作结果的一致性。

事务消息：

RocketMQ提供了类似X/Open XA的分布式事务功能，通过事务消息能达到分布式事务的最终一致。XA是一种分布式事务解决方案，一种分布式事务处理模式。

半事务消息：

暂不能投递的消息，发送方已经成功地将消息发送到了Broker，但是Broker未收到最终确认指令，此时该消息被标记成“暂不能投递”状态，即不能被消费者看到。处于该种状态下的消息即半事务消息。

本地事务状态：

Producer`回调操作`执行的结果为本地事务状态，其会发送给TC，而TC会再发送给TM。TM会根据TC发送来的本地事务状态来决定全局事务确认指令。

```
// 描述本地事务执行状态
public enum LocalTransactionState {
    COMMIT_MESSAGE,  // 本地事务执行成功
    ROLLBACK_MESSAGE,  // 本地事务执行失败
    UNKNOW,  // 不确定，表示需要进行回查以确定本地事务的执行结果
}

```

消息回查

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151631259.png)

消息回查，即重新查询本地事务的执行状态。本例就是重新到DB中查看预扣款操作是否执行成功。

> 注意，消息回查不是重新执行回调操作。回调操作是进行预扣款操作，而消息回查则是查看预扣款操作执行的结果。
>
> 引发消息回查的原因最常见的有两个：
> 1)回调操作返回UNKNWON
> 2)TC没有接收到TM的最终全局事务确认指令

#### 9.4.3 RocketMQ的消息回查设置

关于消息回查，有三个常见的属性设置。它们都在broker加载的配置文件中设置，例如：

- transactionTimeout=20，指定TM在 20 秒内应将最终确认状态发送给TC，否则引发消息回查。默认为 60 秒
- transactionCheckMax=5，指定最多回查 5 次，超过后将丢弃消息并记录错误日志。默认 15 次。
- transactionCheckInterval=10，指定设置的多次消息回查的时间间隔为 10 秒。默认为 60 秒。

#### 9.4.4 XA协议

XA（Unix Transaction）是一种分布式事务解决方案，一种分布式事务处理模式，是基于XA协议的。
XA协议由Tuxedo（Transaction for Unix has been Extended for Distributed Operation，分布式操作扩展之后的Unix事务系统）首先提出的，并交给X/Open组织，作为资源管理器与事务管理器的接口标准。

```
XA模式中有三个重要组件：TC、TM、RM。

Transaction Coordinator，事务协调者。维护全局和分支事务的状态，驱动全局事务提交或回滚。
RocketMQ中Broker充当着TC。

Transaction Manager，事务管理器。定义全局事务的范围：开始全局事务、提交或回滚全局事务。它实际是全局事务的发起者。
RocketMQ中事务消息的Producer充当着TM。

Resource Manager，资源管理器。管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。
RocketMQ中事务消息的Producer及Broker均是RM。
```

#### 9.4.5 XA协议架构

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151634356.png)

XA模式是一个典型的2PC，其执行原理如下：

- 1. TM向TC发起指令，开启一个全局事务。

- 1. 根据业务要求，各个RM会逐个向TC注册分支事务，然后TC会逐个向RM发出预执行指令。

- 1. 各个RM在接收到指令后会在进行本地事务预执行。

- 1. RM将预执行结果Report给TC。当然，这个结果可能是成功，也可能是失败。

- 1. TC在接收到各个RM的Report后会将汇总结果上报给TM，根据汇总结果TM会向TC发出确认指令。

  - 若所有结果都是成功响应，则向TC发送Global Commit指令。
  - 只要有结果是失败响应，则向TC发送Global Rollback指令。

- 1. TC在接收到指令后再次向RM发送确认指令。

> 事务消息方案并不是一个典型的XA模式。因为XA模式中的分支事务是异步的，而事务消息方案中的消息预提交与预扣款操作间是同步的。

```
事务消息不支持延时消息
对于事务消息要做好幂等性检查，因为事务消息可能不止一次被消费（因为存在回滚后再提交的情况）
```

#### 9.4.6 代码举例

事务监听器

```
package transaction;

import org.apache.commons.lang3.StringUtils;
import org.apache.rocketmq.client.producer.LocalTransactionState;
import org.apache.rocketmq.client.producer.TransactionListener;
import org.apache.rocketmq.common.message.Message;
import org.apache.rocketmq.common.message.MessageExt;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:40:23
* @DESCRIPTION :事务消息监听器
*/
public class ICBCTransactionListener implements TransactionListener{
//回调操作方法
  // 消息预提交成功就会触发该方法的执行，用于完成本地事务
  public LocalTransactionState executeLocalTransaction(Message msg,Object arg) {
      System.out.println("预提交消息成功：" + msg);
      // 假设接收到TAGA的消息就表示扣款操作成功，TAGB的消息表示扣款失败，
      // TAGC表示扣款结果不清楚，需要执行消息回查
      if (StringUtils.equals("TAGA", msg.getTags())) {
          return LocalTransactionState.COMMIT_MESSAGE;
      } else if (StringUtils.equals("TAGB", msg.getTags())) {
          return LocalTransactionState.ROLLBACK_MESSAGE;
      } else if (StringUtils.equals("TAGC", msg.getTags())) {
          return LocalTransactionState.UNKNOW;
      }
          return LocalTransactionState.UNKNOW;
  }

  // 消息回查方法
  // 引发消息回查的原因最常见的有两个：
  // 1)回调操作返回UNKNWON
  // 2)TC没有接收到TM的最终全局事务确认指令
  public LocalTransactionState checkLocalTransaction(MessageExt msg) {
      System.out.println("执行消息回查" + msg.getTags());
      return LocalTransactionState.COMMIT_MESSAGE;
  }
}

```

事务消息生产者

```
package transaction;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

import org.apache.rocketmq.client.producer.SendResult;
import org.apache.rocketmq.client.producer.TransactionMQProducer;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:41:56
* @DESCRIPTION :事务消息生产者
*/
public class TransactionProducer {
	 public static void main(String[] args) throws Exception {
     TransactionMQProducer producer = new TransactionMQProducer("tpg");
     producer.setNamesrvAddr("192.168.133.103:9876");
     /**
     * 定义一个线程池
     * @param corePoolSize 线程池中核心线程数量
     * @param maximumPoolSize 线程池中最多线程数
     * @param keepAliveTime 这是一个时间。当线程池中线程数量大于核心线程数量是，多余空闲线程的存活时长
     * @param unit 时间单位
     * @param workQueue 临时存放任务的队列，其参数就是队列的长度
     * @param threadFactory 线程工厂
     */
     ExecutorService executorService = new ThreadPoolExecutor( 2 , 5 ,100 , TimeUnit.SECONDS,new ArrayBlockingQueue<Runnable>( 2000 ), new ThreadFactory() {
         
         public Thread newThread(Runnable r) {
             Thread thread = new Thread(r);
             thread.setName("client-transaction-msg-check-thread");
             return thread;
         }
     });
     // 为生产者指定一个线程池
     producer.setExecutorService(executorService);
     // 为生产者添加事务监听器
     producer.setTransactionListener(new ICBCTransactionListener());
     producer.start();
     String[] tags = {"TAGA","TAGB","TAGC"};
     for (int i = 0 ; i < 3 ; i++) {
         byte[] body = ("Hi," + i).getBytes();
         Message msg = new Message("TTopic", tags[i], body);
         // 发送事务消息
         // 第二个参数用于指定在执行本地事务时要使用的业务参数
         SendResult sendResult =producer.sendMessageInTransaction(msg,null);
         System.out.println("发送结果为：" +sendResult.getSendStatus());
     }
 }
}

```

消费者

```
package transaction;

import java.util.List;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;
import org.apache.rocketmq.common.message.MessageExt;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午4:44:01
* @DESCRIPTION :事务消息消费者
*/
public class TransactionCosumer {
	public static void main(String[] args) throws MQClientException {
    // DefaultLitePullConsumer consumer = new DefaultLitePullConsumer("cg");
    // 定义一个push消费者
    DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
    // 指定nameServer
    consumer.setNamesrvAddr("192.168.133.103:9876");
    // 指定从第一条消息开始消费
    consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
    // 指定消费topic与tag
    consumer.subscribe("TTopic", "*");
    // 指定采用“广播模式”进行消费，默认为“集群模式”
    // consumer.setMessageModel(MessageModel.BROADCASTING);

    // 注册消息监听器
    consumer.registerMessageListener(new MessageListenerConcurrently() {
        // 一旦broker中有了其订阅的消息就会触发该方法的执行，
        // 其返回值为当前consumer消费的状态
        public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context) {
            // 逐条消费消息
            for (MessageExt msg : msgs) {
                System.out.println(msg);
            }
        // 返回消费状态：消费成功
        return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        }
    });
    // 开启消费者消费
    consumer.start();
    System.out.println("Consumer Started");
}
}

```

![image-20230215165347239](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151653327.png)

### 9.5 批量消息

#### 9.5.1 批量发送消息

发送限制：

生产者进行消息发送时可以一次发送多条消息，这可以大大提升Producer的发送效率。不过需要注意以下几点：

- 批量发送的消息必须具有相同的Topic
- 批量发送的消息必须具有相同的刷盘策略
- 批量发送的消息不能是延时消息与事务消息

批量发送大小：

默认情况下，一批发送的消息总大小不能超过4MB字节。如果想超出该值，有两种解决方案：

- 方案一：将批量消息进行拆分，拆分为若干不大于4M的消息集合分多次批量发送
- 方案二：在Producer端与Broker端修改属性

** Producer端需要在发送之前设置Producer的maxMessageSize属性

** Broker端需要修改其加载的配置文件中的maxMessageSize属性

**生产者发送消息的大小**

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151853760.png)

生产者通过send()方法发送的Message，并不是直接将Message序列化后发送到网络上的，而是通过这个Message生成了一个字符串发送出去的。这个字符串由四部分构成：Topic、消息Body、消息日志（占 20 字节），及用于描述消息的一堆属性key-value。这些属性中包含例如生产者地址、生产时间、要发送的QueueId等。最终写入到Broker中消息单元中的数据都是来自于这些属性

#### 9.5.2 批量消费消息

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151854486.png)

Consumer的MessageListenerConcurrently监听接口的consumeMessage()方法的第一个参数为消息列表，但默认情况下每次只能消费一条消息。

若要使其一次可以消费多条消息，则可以通过修改Consumer的consumeMessageBatchMaxSize属性来指定。不过，该值不能超过 32 。因为默认情况下消费者每次可以拉取的消息最多是 32 条。若要修改一次拉取的最大值，则可通过修改Consumer的pullBatchSize属性来指定。

存在的问题：

Consumer的pullBatchSize属性与consumeMessageBatchMaxSize属性是否设置的越大越好？当然不是。

- pullBatchSize值设置的越大，Consumer每拉取一次需要的时间就会越长，且在网络上传输出现问题的可能性就越高。若在拉取过程中若出现了问题，那么本批次所有消息都需要全部重新拉取。
- consumeMessageBatchMaxSize值设置的越大，Consumer的消息并发消费能力越低，且这批被消费的消息具有相同的消费结果。因为consumeMessageBatchMaxSize指定的一批消息只会使用一个线程进行处理，且在处理过程中只要有一个消息处理异常，则这批消息需要全部重新再次消费处理。

#### 9.5.3 代码举例

该批量发送的需求是，不修改最大发送4M的默认值，但要防止发送的批量消息超出4M的限制。

定义消息列表分割器

```
package batch;

import java.util.Iterator;
import java.util.List;
import java.util.Map;

import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午7:09:05
* @DESCRIPTION :消息列表分割器
*/
// 消息列表分割器：其只会处理每条消息的大小不超4M的情况。
// 若存在某条消息，其本身大小大于4M，这个分割器无法处理，
// 其直接将这条消息构成一个子列表返回。并没有再进行分割
public class MessageListSplitter implements Iterator<List<Message>> {
	 // 指定极限值为4M
  private final int SIZE_LIMIT =  4 * 1024 * 1024 ;
  // 存放所有要发送的消息
  private final List<Message> messages;
  // 要进行批量发送消息的小集合起始索引
  private int currIndex;
  public MessageListSplitter(List<Message> messages) {
      this.messages = messages;
  }

	public boolean hasNext() {
	  // 判断当前开始遍历的消息索引要小于消息总数
    return currIndex < messages.size();
	}

	public List<Message> next() {
		int nextIndex = currIndex;
    // 记录当前要发送的这一小批次消息列表的大小
    int totalSize = 0 ;
    for (; nextIndex < messages.size(); nextIndex++) {
        // 获取当前遍历的消息
        Message message = messages.get(nextIndex);
        // 统计当前遍历的message的大小
        int tmpSize = message.getTopic().length() + message.getBody().length;
        Map<String, String> properties = message.getProperties();
        for (Map.Entry<String, String> entry :properties.entrySet()) {
            tmpSize += entry.getKey().length() +
            entry.getValue().length();
        }
        tmpSize = tmpSize + 20 ;
        // 判断当前消息本身是否大于4M
        if (tmpSize > SIZE_LIMIT) {
            if (nextIndex - currIndex == 0 ) {
                nextIndex++;
            }
            break;
        }

        if (tmpSize + totalSize > SIZE_LIMIT) {
            break;
        } else {
            totalSize += tmpSize;
        }

    } 
    // 获取当前messages列表的子集合[currIndex, nextIndex)
    List<Message> subList = messages.subList(currIndex, nextIndex);
    // 下次遍历的开始索引
    currIndex = nextIndex;
    return subList;
	}

}

```

定义批量消息生产者

```
package batch;

import java.util.ArrayList;
import java.util.List;

import org.apache.rocketmq.client.producer.DefaultMQProducer;
import org.apache.rocketmq.common.message.Message;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午7:10:57
* @DESCRIPTION :批量消息生产者
*/
public class BatchProducer {
	 public static void main(String[] args) throws Exception {
     DefaultMQProducer producer = new DefaultMQProducer("pg");
     producer.setNamesrvAddr("192.168.133.103:9876");
     // 指定要发送的消息的最大大小，默认是4M
     // 不过，仅修改该属性是不行的，还需要同时修改broker加载的配置文件中的
     // maxMessageSize属性
     // producer.setMaxMessageSize(8 * 1024 * 1024);
     producer.start();

     // 定义要发送的消息集合
     List<Message> messages = new ArrayList<Message>();
     for (int i = 0 ; i < 100 ; i++) {
         byte[] body = ("Hi," + i).getBytes();
         Message msg = new Message("someTopic", "someTag", body);
         messages.add(msg);
     }

     // 定义消息列表分割器，将消息列表分割为多个不超出4M大小的小列表
     MessageListSplitter splitter = new MessageListSplitter(messages);
     while (splitter.hasNext()) {
         try {
             List<Message> listItem = splitter.next();
             producer.send(listItem);
         } catch (Exception e) {
             e.printStackTrace();
         }
     }
     producer.shutdown();
 }
}

```

定义批量消息消费者

```
package batch;

import java.util.List;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.client.exception.MQClientException;
import org.apache.rocketmq.common.consumer.ConsumeFromWhere;
import org.apache.rocketmq.common.message.MessageExt;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月15日 下午7:12:21
* @DESCRIPTION :批量消费者
*/
public class BatchConsumer {
	 public static void main(String[] args) throws MQClientException {
     DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
     consumer.setNamesrvAddr("192.168.133.103:9876");
     consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
     consumer.subscribe("someTopicA", "*");

     // 指定每次可以消费 10 条消息，默认为 1
     consumer.setConsumeMessageBatchMaxSize( 10 );
     // 指定每次可以从Broker拉取 40 条消息，默认为 32
     consumer.setPullBatchSize( 40 );

     consumer.registerMessageListener(new MessageListenerConcurrently() {
        
         public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context) {
             for (MessageExt msg : msgs) {
                 System.out.println(msg);
             }
             // 消费成功的返回结果
             return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
             // 消费异常时的返回结果
             // return ConsumeConcurrentlyStatus.RECONSUME_LATER;
         }
     });

     consumer.start();
     System.out.println("Consumer Started");
 }
}

```



### 9.6 消息过滤

消息者在进行消息订阅时，除了可以指定要订阅消息的Topic外，还可以对指定Topic中的消息根据指定条件进行过滤，即可以订阅比Topic更加细粒度的消息类型。

对于指定Topic消息的过滤有两种过滤方式：Tag过滤与SQL过滤。

#### 9.6.1 tag过滤

通过consumer的subscribe()方法指定要订阅消息的Tag。如果订阅多个Tag的消息，Tag间使用或运算符(双竖线||)连接。

```java
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("CID_EXAMPLE");
consumer.subscribe("TOPIC", "TAGA || TAGB || TAGC");
```

#### 9.6.2 SQL过滤

SQL过滤是一种通过特定表达式对事先埋入到消息中的`用户属性`进行筛选过滤的方式。通过SQL过滤，可以实现对消息的复杂过滤。不过，只有使用`PUSH模式`的消费者才能使用SQL过滤。

SQL过滤表达式中支持多种常量类型与运算符。

支持的常量类型：

- 数值：比如： 123 ，3.1415
- 字符：必须用单引号包裹起来，比如：'abc'
- 布尔：TRUE 或 FALSE
- NULL：特殊的常量，表示空

支持的运算符有：

- 数值比较：>，>=，<，<=，BETWEEN，=
- 字符比较：=，<>，IN
- 逻辑运算 ：AND，OR，NOT
- NULL判断：IS NULL 或者 IS NOT NULL

默认情况下Broker没有开启消息的SQL过滤功能，需要在Broker加载的配置文件中添加如下属性，以开启该功能：

```shell
enablePropertyFilter = trueCopy to clipboardErrorCopied
```

在启动Broker时需要指定这个修改过的配置文件。例如对于单机Broker的启动，其修改的配置文件是conf/broker.conf，启动时使用如下命令：

```shell
sh bin/mqbroker -n localhost:9876 -c conf/broker.conf &
```

#### 9.6.3 代码举例

定义Tag过滤Producer

```java
public class FilterByTagProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        producer.setNamesrvAddr("192.168.133.103:9876");
        producer.start();
        String[] tags = {"myTagA","myTagB","myTagC"};
        for (int i = 0 ; i < 10 ; i++) {
            byte[] body = ("Hi," + i).getBytes();
            String tag = tags[i%tags.length];
            Message msg = new Message("myTopic",tag,body);
            SendResult sendResult = producer.send(msg);
            System.out.println(sendResult);
        }
        producer.shutdown();
    }
}
```

定义Tag过滤Consumer

```java
public class FilterByTagConsumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("pg");
        consumer.setNamesrvAddr("192.168.133.103:9876");
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);

        consumer.subscribe("myTopic", "myTagA || myTagB");
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs,ConsumeConcurrentlyContext context) {
                for (MessageExt me:msgs){
                    System.out.println(me);
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
        System.out.println("Consumer Started");
    }
}
```

定义SQL过滤Producer

```java
public class FilterBySQLProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("pg");
        producer.setNamesrvAddr("192.168.133.103:9876");
        producer.start();
        for (int i = 0 ; i < 10 ; i++) {
            try {
                byte[] body = ("Hi," + i).getBytes();
                Message msg = new Message("myTopic", "myTag", body);
                msg.putUserProperty("age", i + "");
                SendResult sendResult = producer.send(msg);
                System.out.println(sendResult);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        producer.shutdown();
    }
}
```

定义SQL过滤Consumer

```java
public class FilterBySQLConsumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("pg");
        consumer.setNamesrvAddr("192.168.133.103:9876");
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
        consumer.subscribe("myTopic", MessageSelector.bySql("age between 0 and 6"));
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                for (MessageExt me:msgs){
                    System.out.println(me);
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
        System.out.println("Consumer Started");
    }
}
```







### 9.7 消息发送重试机制

#### 9.7.1 说明

Producer对发送失败的消息进行重新发送的机制，称为消息发送重试机制，也称为消息重投机制。

注意点

- 生产者在发送消息时，若采用同步或异步发送方式，发送失败会重试，但oneway消息发送方式发送失败是没有重试机制的
- 只有普通消息具有发送重试机制，顺序消息是没有的
- 消息重投机制可以保证消息尽可能发送成功、不丢失，但可能会造成消息重复。消息重复在RocketMQ中是无法避免的问题
- 消息重复在一般情况下不会发生，当出现消息量大、网络抖动，消息重复就会成为大概率事件
- producer主动重发、consumer负载变化（发生Rebalance，不会导致消息重复，但可能出现重复消费）也会导致重复消息
- 消息重复无法避免，但要避免消息的重复消费。
- 避免消息重复消费的解决方案是，为消息添加唯一标识（例如消息key），使消费者对消息进行消费判断来避免重复消费
- 消息发送重试有三种策略可以选择：同步发送失败策略、异步发送失败策略、消息刷盘失败策略

#### 9.7.2 同步发送失败策略

对于普通消息，消息发送默认采用round-robin策略来选择所发送到的队列。如果发送失败，默认重试 2次。但在重试时是不会选择上次发送失败的Broker，而是选择其它Broker。当然，若只有一个Broker其也只能发送到该Broker，但其会尽量发送到该Broker上的其它Queue。

```java
// 创建一个producer，参数为Producer Group名称
DefaultMQProducer producer = new DefaultMQProducer("pg");
// 指定nameServer地址
producer.setNamesrvAddr("192.168.133.103:9876");
// 设置同步发送失败时重试发送的次数，默认为 2 次
producer.setRetryTimesWhenSendFailed( 3 );
// 设置发送超时时限为5s，默认3s
producer.setSendMsgTimeout( 5000 );Copy to clipboardErrorCopied
```

同时，Broker还具有`失败隔离`功能，使Producer尽量选择未发生过发送失败的Broker作为目标Broker。其可以保证其它消息尽量不发送到问题Broker，为了提升消息发送效率，降低消息发送耗时。

> 思考：让我们自己实现`失败隔离`功能，如何来做？
>
> 1 ）方案一：Producer中维护某JUC的Map集合，其key是发生失败的时间戳，value为Broker实例。Producer中还维护着一个Set集合，其中存放着所有未发生发送异常的Broker实例。选择目标Broker是从该Set集合中选择的。再定义一个定时任务，定期从Map集合中将长期未发生发送异常的Broker清理出去，并添加到Set集合。
>
> 2 ）方案二：为Producer中的Broker实例添加一个标识，例如是一个AtomicBoolean属性。只要该Broker上发生过发送异常，就将其置为true。选择目标Broker就是选择该属性值为false的Broker。再定义一个定时任务，定期将Broker的该属性置为false。
>
> 3 ）方案三：为Producer中的Broker实例添加一个标识，例如是一个AtomicLong属性。只要该Broker上发生过发送异常，就使其值增一。选择目标Broker就是选择该属性值最小的Broker。若该值相同，采用轮询方式选择。

如果超过重试次数，则抛出异常，由Producer去保证消息不丢。当然当生产者出现RemotingException、MQClientException和MQBrokerException时，Producer会自动重投消息。



#### 9.7.3 异步发送失败策略

异步发送失败重试时，异步重试不会选择其他broker，仅在同一个broker上做重试，所以该策略无法保证消息不丢。

```java
DefaultMQProducer producer = new DefaultMQProducer("pg");
producer.setNamesrvAddr("192.168.133.103:9876");
// 指定异步发送失败后不进行重试发送
producer.setRetryTimesWhenSendAsyncFailed( 0 );
```

#### 9.7.4 消息刷盘失败策略

消息刷盘超时（Master或Slave）或slave不可用（slave在做数据同步时向master返回状态不是SEND_OK）时，默认是不会将消息尝试发送到其他Broker的。

不过，对于重要消息可以通过在Broker的配置文件设置retryAnotherBrokerWhenNotStoreOK属性为true来开启。



### 9.8 消息消费重试机制

#### 9.8.1 顺序消息的重试机制

对于顺序消息，当Consumer消费消息失败后，为了保证消息的顺序性，其会自动不断地进行消息重试，直到消费成功。消费重试默认间隔时间为 1000 毫秒。重试期间应用会出现消息消费被阻塞的情况。

```java
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
// 顺序消息消费失败的消费重试时间间隔，单位毫秒，默认为 1000 ，其取值范围为[10,30000]
consumer.setSuspendCurrentQueueTimeMillis( 100 );Copy to clipboardErrorCopied
```

> 由于对顺序消息的重试是无休止的，不间断的，直至消费成功，所以，对于顺序消息的消费，务必要保证应用能够及时监控并处理消费失败的情况，避免消费被永久性阻塞。
>
> 注意，顺序消息没有发送失败重试机制，但具有消费失败重试机制



#### 9.8.2 无序消息的重试机制

对于无序消息（普通消息、延时消息、事务消息），当Consumer消费消息失败时，可以通过设置返回状态达到消息重试的效果。不过需要注意，无序消息的重试`只对集群消费方式生效`，广播消费方式不提供失败重试特性。即对于广播消费，消费失败后，失败消息不再重试，继续消费后续消息。

#### 9.8.3 消费重试次数与间隔

对于`无序消息集群`消费下的重试消费，每条消息默认最多重试 16 次，但每次重试的间隔时间是不同的，会逐渐变长。每次重试的间隔时间如下表。

| 重试次数 | 与上次重试的间隔时间 | 重试次数 | 与上次重试的间隔时间 |
| -------- | -------------------- | -------- | -------------------- |
| 1        | 10秒                 | 9        | 7分钟                |
| 2        | 30                   | 10       | 8 分钟               |
| 3        | 1分钟                | 11       | 9 分钟               |
| 4        | 2分钟                | 12       | 10分钟               |
| 5        | 3分钟                | 13       | 20分钟               |
| 6        | 4分钟                | 14       | 30分钟               |
| 7        | 5分钟                | 15       | 1小时                |
| 8        | 6分钟                | 16       | 2 小时               |

> 若一条消息在一直消费失败的前提下，将会在正常消费后的第 `4 小时 46 分`后进行第 16 次重试。
> 若仍然失败，则将消息投递到`死信队列`
>
> 修改消费重试次数

```
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("cg");
// 修改消费重试次数
consumer.setMaxReconsumeTimes( 10 );
```

对于修改过的重试次数，将按照以下策略执行：
1)若修改值小于 16 ，则按照指定间隔进行重试
2)若修改值大于 16 ，则超过 16 次的重试时间间隔均为 2 小时

对于Consumer Group，若仅修改了一个Consumer的消费重试次数，则会应用到该Group中所有其它Consumer实例。若出现多个Consumer均做了修改的情况，则采用覆盖方式生效。即最后被修改的值会覆盖前面设置的值。

#### 9.8.4 重试队列

对于需要重试消费的消息，并不是Consumer在等待了指定时长后再次去拉取原来的消息进行消费，而是将这些需要重试消费的消息放入到了一个特殊Topic的队列中，而后进行再次消费的。这个特殊的队列就是重试队列。

当出现需要进行重试消费的消息时，Broker会为每个消费组都设置一个Topic名称为`%RETRY%consumerGroup@consumerGroup`的重试队列。

> 1 ）这个重试队列是针对消息才组的，而不是针对每个Topic设置的（一个Topic的消息可以让多个消费者组进行消费，所以会为这些消费者组各创建一个重试队列）
> 2 ）只有当出现需要进行重试消费的消息时，才会为该消费者组创建重试队列

> 注意，消费重试的时间间隔与`延时消费`的`延时等级`十分相似，除了没有延时等级的前两个时间外，其它的时间都是相同的

Broker对于重试消息的处理是通过`延时消息`实现的。先将消息保存到SCHEDULE_TOPIC_XXXX延迟队列中，延迟时间到后，会将消息投递到%RETRY%consumerGroup@consumerGroup重试队列中。

#### 9.8.5 消费重试的配置方式

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151931064.png)

集群消费方式下，消息消费失败后若希望消费重试，则需要在消息监听器接口的实现中明确进行如下三种方式之一的配置：

- 方式 1 ：返回ConsumeConcurrentlyStatus.RECONSUME_LATER（推荐）
- 方式 2 ：返回Null
- 方式 3 ：抛出异常

#### 9.8.6 消费不重试配置方式

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302151932235.png)

集群消费方式下，消息消费失败后若不希望消费重试，则在捕获到异常后同样也返回与消费成功后的相同的结果，即ConsumeConcurrentlyStatus.CONSUME_SUCCESS，则不进行消费重试。





### 9.9 死信队列

#### 9.9.1 概念

当一条消息初次消费失败，消息队列会自动进行消费重试；达到最大重试次数后，若消费依然失败，则表明消费者在正常情况下无法正确地消费该消息，此时，消息队列不会立刻将消息丢弃，而是将其发送到该消费者对应的特殊队列中。这个队列就是死信队列（Dead-Letter Queue，DLQ），而其中的消息 则称为死信消息（Dead-Letter Message，DLM）。

> 死信队列是用于处理无法被正常消费的消息的。

#### 9.9.2 特征

- 死信队列中的消息不会再被消费者正常消费，即DLQ对于消费者是不可见的
- 死信存储有效期与正常消息相同，均为 3 天（commitlog文件的过期时间）， 3 天后会被自动删除
- 死信队列就是一个特殊的Topic，名称为%DLQ%consumerGroup@consumerGroup，即每个消费者组都有一个死信队列
- 如果一个消费者组未产生死信消息，则不会为其创建相应的死信队列

#### 9.9.3 如何处理

实际上，当一条消息进入死信队列，就意味着系统中某些地方出现了问题，从而导致消费者无法正常消费该消息，比如代码中原本就存在Bug。因此，对于死信消息，通常需要开发人员进行特殊处理。最关键的步骤是要排查可疑因素，解决代码中可能存在的Bug，然后再将原来的死信消息再次进行投递消费。
