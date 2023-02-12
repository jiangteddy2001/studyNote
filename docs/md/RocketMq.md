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
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m"

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





### 8.3 indexFile



### 8.4 消息的消费



### 8.5 订阅关系一致性





## 9 RocketMQ应用



