# Kafka

## 1 概念

### 1.1 MQ概念

Message Queue（MQ），消息队列中间件。很多人都说：MQ 通过将消息的发送和接收分离来实现应用程序的异步和解偶，这个给人的直觉是——MQ 是异步的，用来解耦的，但是这个只是 MQ 的效果而不是目的。MQ 真正的目的是为了通讯，屏蔽底层复杂的通讯协议，定义了一套应用层的、更加简单的通讯协议。一个分布式系统中两个模块之间通讯要么是HTTP，要么是自己开发的（rpc） TCP，但是这两种协议其实都是原始的协议。

HTTP 协议很难实现两端通讯——模块 A 可以调用 B，B 也可以主动调用 A，如果要做到这个两端都要背上WebServer，而且还不支持⻓连接（HTTP 2.0 的库根本找不到）。TCP 就更加原始了，粘包、心跳、私有的协议，想一想头皮就发麻。

MQ 所要做的就是在这些协议之上构建一个简单的“协议”——生产者/消费者模型。MQ 带给我的“协议”不是具体的通讯协议，而是更高层次通讯模型。它定义了两个对象——发送数据的叫生产者；接收数据的叫消费者， 提供一个SDK 让我们可以定义自己的生产者和消费者实现消息通讯而无视底层通讯协议

### 1.2 消息队列流派

分为有Broker的MQ和无Broker的MQ

#### 1.2.1 有Broker的MQ

这个流派通常有一台服务器作为 Broker，所有的消息都通过它中转。生产者把消息发送给它就结束自己的任务了，Broker 则把消息主动推送给消费者（或者消费者主动轮询）

1重topic 

> kafka、JMS（ActiveMQ）就属于这个流派，生产者会发送 key 和数据到 Broker，由 Broker比较 key 之后决定给哪个消费者。这种模式是我们最常⻅的模式，是我们对 MQ 最多的印象。在这种模式下一个 topic 往往是一个比较大的概念，甚至一个系统中就可能只有一个topic，topic 某种意义上就是 queue，生产者发送 key 相当于说：“hi，把数据放到 key 的队列中”

> 如上图所示，Broker 定义了三个队列，key1，key2，key3，生产者发送数据的时候会发送key1 和 data，Broker 在推送数据的时候则推送 data（也可能把 key 带上）。

> 虽然架构一样但是 kafka 的性能要比 jms 的性能不知道高到多少倍，所以基本这种类型的MQ 只有 kafka 一种备选方案。如果你需要一条暴力的数据流（在乎性能而非灵活性）那么kafka 是最好的选择

2 轻topic 

> 这种的代表是 RabbitMQ（或者说是 AMQP）。生产者发送 key 和数据，消费者定义订阅的队列，Broker 收到数据之后会通过一定的逻辑计算出 key 对应的队列，然后把数据交给队列

> 这种模式下解耦了 key 和 queue，在这种架构中 queue 是非常轻量级的（在 RabbitMQ 中它的上限取决于你的内存），消费者关心的只是自己的 queue；生产者不必关心数据最终给谁只要指定 key 就行了，中间的那层映射在 AMQP 中叫 exchange（交换机）。

AMQP 中有四种 exchange

- Direct exchange：key 就等于 queue
- Fanout exchange：无视 key，给所有的 queue 都来一份
- Topic exchange：key 可以用“宽字符”模糊匹配 queue
- Headers exchange：无视 key，通过查看消息的头部元数据来决定发给那个
- queue（AMQP 头部元数据非常丰富而且可以自定义）

这种结构的架构给通讯带来了很大的灵活性，我们能想到的通讯方式都可以用这四种exchange 表达出来。如果你需要一个企业数据总线（在乎灵活性）那么 RabbitMQ 绝对的值得一用

追求可用性：Kafka、 RocketMQ 、RabbitMQ

追求可靠性：RabbitMQ、RocketMQ

追求吞吐能力：RocketMQ、Kafka

追求消息低延迟：RabbitMQ、Kafka



#### 1.2.2 无Broker的MQ

> 无 Broker 的 MQ 的代表是 ZeroMQ。该作者非常睿智，他非常敏锐的意识到——MQ 是更高级的 Socket，它是解决通讯问题的。所以 ZeroMQ 被设计成了一个“库”而不是一个中间件，这种实现也可以达到——没有 Broker 的目的

> 节点之间通讯的消息都是发送到彼此的队列中，每个节点都既是生产者又是消费者。ZeroMQ做的事情就是封装出一套类似于 Socket 的 API 可以完成发送数据，读取数据

> ZeroMQ 其实就是一个跨语言的、重量级的 Actor 模型邮箱库。你可以把自己的程序想象成一个 Actor，ZeroMQ 就是提供邮箱功能的库；ZeroMQ 可以实现同一台机器的 RPC 通讯也可以实现不同机器的 TCP、UDP 通讯，如果你需要一个强大的、灵活、野蛮的通讯能力，别犹豫 ZeroMQ

### 1.3 Kafka介绍

![Kafka](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171125084.png)

Kafka是最初由Linkedin公司开发，是一个分布式、支持分区的（partition）、多副本的 （replica），基于zookeeper协调的分布式消息系统，它的最大的特性就是可以实时的处理 大量数据以满足各种需求场景：比如基于hadoop的批处理系统、低延迟的实时系统、 Storm/Spark流式处理引擎，web/nginx日志、访问日志，消息服务等等，用scala语言编 写，Linkedin于 2010 年贡献给了Apache基金会并成为顶级开源 项目。

使用场景：

日志收集：一个公司可以用Kafka收集各种服务的log，通过kafka以统一接口服务的方式 开放给各种consumer，例如hadoop、Hbase、Solr等。 消息系统：解耦和生产者和消费者、缓存消息等。 用户活动跟踪：Kafka经常被用来记录web用户或者app用户的各种活动，如浏览网⻚、 搜索、点击等活动，这些活动信息被各个服务器发布到kafka的topic中，然后订阅者通过 订阅这些topic来做实时的监控分析，或者装载到hadoop、数据仓库中做离线分析和挖 掘。 运营指标：Kafka也经常用来记录运营监控数据。包括收集各种分布式应用的数据，生产 各种操作的集中反馈，比如报警和报告。



### 1.4 Kafka基本概念

kafka是一个分布式的，分区的消息(官方称之为commit log)服务。它提供一个消息系统应该 具备的功能，但是确有着独特的设计。可以这样来说，Kafka借鉴了JMS规范的思想，但是确 并 `没有完全遵循JMS规范。`

首先，让我们来看一下基础的消息(Message)相关术语：

| 名称          | 解释                                                         |
| ------------- | ------------------------------------------------------------ |
| Broker        | 消息中间件处理节点，⼀个Kafka节点就是⼀个broker，⼀个或者多个Broker可以组成⼀个Kafka集群 |
| Topic         | Kafka根据topic对消息进⾏归类，发布到Kafka集群的每条消息都需要指定⼀个topic |
| Producer      | 消息⽣产者，向Broker发送消息的客户端                         |
| Consumer      | 消息消费者，从Broker读取消息的客户端                         |
| ConsumerGroup | 每个Consumer属于⼀个特定的Consumer Group，⼀条消息可以被多个不同的Consumer Group消费，但是⼀个Consumer Group中只能有⼀个Consumer能够消费该消息 |
| Partition     | 物理上的概念，⼀个topic可以分为多个partition，每个partition内部消息是有序的 |

因此，从一个较高的层面上来看，producer通过网络发送消息到Kafka集群，然后consumer 来进行消费，如下图： ![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302161600667.png)

服务端(brokers)和客户端(producer、consumer)之间通信通过 **TCP协议** 来完成。

![image-20221023160845899](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171127737.png)

## 2 Kafka的基本使用

### 2.1 环境安装

- 安装jdk
- 安装zk

官网 https://zookeeper.apache.org/releases.html

下载3.7.1版本。上传到服务器

```
cd /usr/local/
mkdir zookeeper

tar -zxvf apache-zookeeper-3.7.1-bin.tar.gz
mv apache-zookeeper-3.7.1-bin/ /usr/local/zookeeper/
cd /usr/local/zookeeper/apache-zookeeper-3.7.1-bin/

# 修改配置文件
cd conf
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg

```

![image-20230216172640177](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302161726219.png)

配置环境变量

```
vi  ~/.bashrc 


#编写内容
export ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.7.1-bin
export PATH=$ZOOKEEPER_HOME/bin:$PATH

# 环境变量生效
source  ~/.bashrc 

```

启动并查看状态

```
# 开启服务端
zkServer.sh start
zkServer.sh  status

#开启客户端
zkCli.sh
```

```
[root@localhost conf]# zkServer.sh start
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/apache-zookeeper-3.7.1-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@localhost conf]# zkServer.sh  status
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/apache-zookeeper-3.7.1-bin/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone

```

- 官网下载kafka的压缩包:http://kafka.apache.org/downloads

下载版本2.4.1

```
cd /usr/local
mkdir kafka

# 上传压缩包到kafka目录
tar -zxvf kafka_2.11-2.4.1.tgz

```

![image-20230216201546474](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162015636.png)

修改配置文件：/usr/local/kafka/kafka2.11-2.4/config/server.properties

```
#broker.id属性在kafka集群中必须要是唯一
broker.id= 0
#kafka部署的机器ip和提供服务的端口号
listeners=PLAINTEXT://192.168.133.103:9092
#kafka的消息存储文件
log.dir=/usr/local/data/kafka-logs
#kafka连接zookeeper的地址
zookeeper.connect= 192.168.133.103:2181

```

![image-20230216204738640](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162047700.png)

![image-20230216204821360](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162048399.png)

### 2.2 启动服务

进入到bin目录下。使用命令来启动

```
./kafka-server-start.sh -daemon ../config/server.properties
```

验证是否启动成功：

```
[root@localhost bin]# jps
9058 Jps
8979 Kafka
8183 QuorumPeerMain

```

进入到zk中的节点看id是 0 的broker有没有存在（上线）

```
zkCli.sh

ls /brokers/ids
```

![image-20230216205807835](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162058871.png)



**server.properties核心配置详解：**

| Property                   | Default                          | Description                                                  |
| -------------------------- | -------------------------------- | ------------------------------------------------------------ |
| broker.id                  | 0                                | 每个broker都可以⽤⼀个唯⼀的⾮负整数id进⾏标识；这个id可以作为broker的“名字”，你可以选择任意你喜欢的数字作为id，只要id是唯⼀的即可。 |
| log.dirs                   | /tmp/kafka-logs                  | kafka存放数据的路径。这个路径并不是唯⼀的，可以是多个，路径之间只需要使⽤逗号分隔即可；每当创建新partition时，都会选择在包含最少partitions的路径下进⾏。 |
| listeners                  | PLAINTEXT://192.168.133.103:9092 | server接受客户端连接的端⼝，ip配置kafka本机ip即可            |
| zookeeper.connect          | localhost:2181                   | zooKeeper连接字符串的格式为：hostname:port，此处hostname和port分别是ZooKeeper集群中某个节点的host和port；zookeeper如果是集群，连接⽅式为hostname1:port1, hostname2:port2,hostname3:port3 |
| log.retention.hours        | 168                              | 每个⽇志⽂件删除之前保存的时间。默认数据保存时间对所有topic都⼀样。 |
| num.partitions             | 1                                | 创建topic的默认分区数                                        |
| default.replication.factor | 1                                | ⾃动创建topic的默认副本数量，建议设置为⼤于等于2             |
| min.insync.replicas        | 1                                | 当producer设置acks为-1时，min.insync.replicas指定replicas的最⼩数⽬（必须确认每⼀个repica的写数据都是成功的），如果这个数⽬没有达到，producer发送消息会产⽣异常 |
| delete.topic.enable        | false                            | 是否允许删除主题                                             |



### 2.3 创建主题

topic是什么概念？topic可以实现消息的分类，不同消费者订阅不同的topic。

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162058264.png)

执行以下命令创建名为“test”的topic，这个topic只有一个partition，并且备份因子也设置为1

```shell
# 创建一个分区一个副本的主题，主题名称是test
./kafka-topics.sh --create --zookeeper 192.168.133.103:2181 --replication-factor 1 --partitions 1 --topic test
```

查看当前kafka内有哪些topic

```shell
./kafka-topics.sh --list --zookeeper 192.168.133.103:2181
```

### 2.4 发送消息

kafka自带了一个producer命令客户端，可以从本地文件中读取内容，或者我们也可以以命令行中直接输入内容，并将这些内容以消息的形式发送到kafka集群中。在默认情况下，每一个行会被当做成一个独立的消息。使用kafka的发送消息的客户端，指定发送到的kafka服务器地址和topic

```
./kafka-console-producer.sh --broker-list 192.168.133.103:9092 --topic test
```



### 2.5 消费消息

对于consumer，kafka同样也携带了一个命令行客户端，会将获取到内容在命令中进行输 出， **默认是消费最新的消息** 。使用kafka的消费者消息的客户端，从指定kafka服务器的指定 topic中消费消息

方式一：从最后一条消息的偏移量+1开始消费

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092 --topic test
```

![image-20230216212236911](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162122942.png)



![image-20230216212226440](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162122476.png)

方式二：从头开始消费

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092 --from-beginning --topic test
```

![image-20230216212336141](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162123178.png)



几个注意点：

- 消息会被存储

- 消息是顺序存储

- 消息是有偏移量的

- 消费时可以指明偏移量进行消费

  

## 3 Kafka的关键细节

### 3.1 消息的顺序存储

消息的发送方会把消息发送到broker中，broker会存储消息，消息是按照发送的顺序进行存储。因此消费者在消费消息时可以指明主题中消息的偏移量。默认情况下，是从最后一个消息的下一个偏移量开始消费。

生产者将消息发送给broker，broker会将消息保存在本地的日志文件中

```
/usr/local/kafka/kafka-logs/主题-分区/00000000.log
```

消息的保存是有序的，通过offset偏移量来描述消息的有序性

消费者消费消息时也是通过offset来描述当前要消费的那条消息的位置


![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171130323.png)



### 3.2 单播消息的实现

单播消息：一个消费组里 只会有一个消费者能消费到某一个topic中的消息。于是可以创建多个消费者，这些消费者在同一个消费组中。

两个消费者在同一个消费组，只有1个能收到消息，两个在不同组或未分组都可以收到

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092 --consumer-property group.id=testGroup --topic test

```

![image-20230216215736058](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162157126.png)

小结一下：**两个消费者在同一个组，只有一个能接到消息，两个在不同组或者未指定组则都能收到**



### 3.3 多播消息的实现

kafka实现多播，只需要让不同的消费者处于不同的消费组即可。

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092 --consumer-property group.id=testGroup1 --topic test

./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092 --consumer-property group.id=testGroup2 --topic test

```

![image-20230216221022094](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162210155.png)

![image-20230216224544519](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162245573.png)





### 3.4 查看消费组的信息

```
# 查看当前主题下有哪些消费组
./kafka-consumer-groups.sh --bootstrap-server 192.168.133.103:9092 --list
# 查看消费组中的具体信息：比如当前偏移量、最后一条消息的偏移量、堆积的消息数量
./kafka-consumer-groups.sh --bootstrap-server 192.168.133.103:9092 --describe --group testGroup

```

- Currennt-offset: 当前消费组的已消费偏移量
- Log-end-offset: 主题对应分区消息的结束偏移量(HW)
- Lag: 当前消费组未消费的消息数

![image-20230216221054575](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162210612.png)

![image-20230216221122639](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162211683.png)

![image-20230216224605116](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162246226.png)





## 4 主题、分区的概念

### 4.1 主题Topic

主题Topic可以理解成是一个类别的名称。



### 4.2 partition分区

![输入图片说明](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162126821.png)

> 一个主题中的消息量是非常大的，因此可以通过分区的设置，来分布式存储这些消息。比如一个topic创建了 3 个分区。那么topic中的消息就会分别存放在这三个分区中

分区的作用：

- 可以分布式存储
- 可以并行写

实际上是存在data/kafka-logs/test-0 和 test-1中的0000000.log文件中

小细节：

> 定期将自己消费分区的offset提交给kafka内部topic：__consumer_offsets，提交过去的 时候，key是consumerGroupId+topic+分区号，value就是当前offset的值，kafka会定 期清理topic里的消息，最后就保留最新的那条数据 因为__consumer_offsets可能会接收高并发的请求，kafka默认给其分配 50 个分区(可以 通过offsets.topic.num.partitions设置)，这样可以通过加机器的方式抗大并发。 通过如下公式可以选出consumer消费的offset要提交到__consumer_offsets的哪个分区 公式：hash(consumerGroupId) % __consumer_offsets主题的分区数

为一个主题创建多个分区

```
./kafka-topics.sh --create --zookeeper 192.168.133.103:2181 --replication-factor 1 --partitions 2 --topic test1
```

**可以通过这样的命令查看topic的分区信息**

```
./kafka-topics.sh --describe --zookeeper localhost:2181 --topic test1
```

![image-20230216222655714](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162226757.png)

现在我们再进到日志文件中看一眼，可以看到日志是以分区来命名的

```
[root@localhost logs]# cd /usr/local/data/kafka-logs
[root@localhost kafka-logs]# ls

```

![image-20230216225229757](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302162252806.png)



### 4.3 Kafka命令

#### 4.3.1 Topic相关命令

在上面我们简单使用kafka后，我们来小结一下kafka中的命令，其实主要有三类：

topic命令：对应脚本kafka-topics.sh
生产者命令：对应脚本kafka-console-producer.sh
消费者命令：对应脚本kafka-console-consumer.sh
首先我们想要的所有命令都可以通过sh kafka-topics.sh看到，主要的命令有：

参数	描述
–bootstrap-server <String: server to connect to>	连接的 Kafka Broker 主机名称和端口号
–topic <String: topic>	操作的 topic 名称
–create	创建主题
–delete	删除主题
–alter	修改主题
–list	查看所有主题
–describe	查看主题详细描述
–partitions <Integer: # of partitions>	设置分区数
–replication-factor <Integer: replication factor>	设置分区副本
–config <String: name=value>	更新系统默认的配置

#### 4.3.2 producer相关命令

| 参数                                             | 描述                                 |
| ------------------------------------------------ | ------------------------------------ |
| –bootstrap-server <String: server to connect to> | 连接的 Kafka Broker 主机名称和端口号 |
| –topic <String: topic>                           | 操作的 topic 名称                    |

#### 4.3.3 consumer相关命令

参数	描述
–bootstrap-server <String: server to connect to>	连接的 Kafka Broker 主机名称和端口号
–topic <String: topic>	操作的 topic 名称
–from-beginning	从头开始消费
–group <String: consumer group id>	指定消费者组名称



## 5 集群和副本

### 5.1 伪分布式搭建

准备 3 个server.properties文件

```
cd /usr/local/kafka/kafka_2.11-2.4.1/config

[root@localhost config]# cp server.properties server1.properties 
[root@localhost config]# cp server.properties server2.properties 
[root@localhost config]# vi server1.properties
[root@localhost config]# vi server2.properties

```

![image-20230217113601166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171136222.png)

每个文件中的这些内容要调整

- server.properties

  ```shell
  broker.id= 0
  listeners=PLAINTEXT://192.168.133.103:9092
  log.dir=/usr/local/data/kafka-logs
  ```

- server1.properties

  ```shell
  broker.id= 1
  listeners=PLAINTEXT://192.168.133.103:9093
  log.dir=/usr/local/data/kafka-logs-1
  ```

- server2.properties

  ```shell
  broker.id= 2
  listeners=PLAINTEXT://192.168.133.103:9094
  log.dir=/usr/local/data/kafka-logs-2
  ```

![image-20230217113919759](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171139811.png)



![image-20230217114047457](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171140507.png)

然后，使用如下命令启动集群

```shell
./kafka-server-start.sh -daemon ../config/server.properties
./kafka-server-start.sh -daemon ../config/server1.properties
./kafka-server-start.sh -daemon ../config/server2.properties
```

搭建完后通过查看zk中的/brokers/ids 看是否启动成功

```
zkCli.sh
```

![image-20230217133852081](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171338151.png)

### 5.2 副本的概念

副本是对分区的备份。在集群中，不同的副本会被部署在不同的broker上。

我们现在创建`一个主题、两个分区、三个副本`的topic（注意：副本只有在集群下才有意义）

```
./kafka-topics.sh --create --zookeeper 192.168.133.103:2181 --replication-factor 3 --partitions 2 --topic my-replicated-topic

```

描述

```
sh kafka-topics.sh \
# 指定启动的机器
--bootstrap-server localhost:9092 \ 
# 创建一个topic
--create --topic my-replicated-topic \   
# 设置分区数为1
--partitions 2         \   
# 设置副本数为3
--replication-factor 3    

```

我们查看一下分区的详细信息

```
# 查看topic情况
./kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
```

```
[root@localhost bin]# ./kafka-topics.sh --create --zookeeper 192.168.133.103:2181 --replication-factor 3 --partitions 2 --topic my-replicated-topic
Created topic my-replicated-topic.
[root@localhost bin]# ./kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
Topic: my-replicated-topic	PartitionCount: 2	ReplicationFactor: 3	Configs: 
	Topic: my-replicated-topic	Partition: 0	Leader: 1	Replicas: 1,2,0	Isr: 1,2,0
	Topic: my-replicated-topic	Partition: 1	Leader: 2	Replicas: 2,0,1	Isr: 2,0,1

```

![image-20230217140242031](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171402114.png)

![image-20230217140647532](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171406580.png)

通过查看主题信息，其中的关键数据：

leader：kafka的写和读的操作，都发生在leader上。leader负责把数据同步给follower。当leader挂了，经过主从选举，从多个follower中选举产生一个新的leader

follower： 接收leader的同步的数据.

isr：可以同步和已同步的节点会被存入到isr集合中。这里有一个细节：如果isr中的节点性能较差，会被提出isr集合。

```
集群中有多个broker，创建主题时可以指明主题有多个分区（把消息拆分到不同的分区中存储），可以为分区创建多个副本，不同的副本存放在不同的broker里。

```

观察3个日志文件夹

![image-20230217155150628](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171551706.png)



通过kill掉leader后再查看主题情况

```
# kill掉leader
ps -aux | grep server.properties

kill 进程号
# 查看topic情况
./kafka-topics.sh --describe --zookeeper 192.168.133.103:2181 --topic my-replicated-topic

```



### 5.3 broker、主题、分区、副本

- kafka集群中由多个broker组成
- 一个broker中存放一个topic的不同partition——副本

![输入图片说明](https://bright-boy.gitee.io/technical-notes/kafka/images/QQ截图20220110134554.png)



### 5.4 集群消费

#### 5.4.1 向集群发送消息

```
./kafka-console-producer.sh --broker-list 192.168.133.103:9092,192.168.133.103:9093,192.168.133.103:9094 --topic my-replicated-topic
```



#### 5.4.2 从集群中消费消息

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092,192.168.133.103:9093,192.168.133.103:9094 --from-beginning --topic my-replicated-topic

```

![image-20230217175111744](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171751834.png)

#### 5.4.3 指定消费组来消费消息

```
./kafka-console-consumer.sh --bootstrap-server 192.168.133.103:9092,192.168.133.103:9093,192.168.133.103:9094 --from-beginning --consumer-property group.id=testGroup1 --topic my-replicated-topic
```

![image-20230217175734203](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171757279.png)

如上图，同一个消费组的2个消费者，一个收到另一个就不会收到消息

这里有一个细节，结合上面的单播消息我们很容易可以想到下面的这种情况，因为一个Partition只能被一个consumer Group里面的一个consumer，所有很容易就可以形成组内单播的现象，即：

多Partition与多consumer一一对应
这样的好处是：

分区存储，可以解决一个topic中文件过大无法存储的问题
提高了读写的吞吐量，读写可以在多个分区中同时进行



![kafka集群消费](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171555205.jpeg)

```
图中Kafka集群有两个broker，每个broker中有多个partition。一个partition只能被一个消费组里的某一个消费者消费，从而保证消费顺序。Kafka只在partition的范围内保证消息消费的局部顺序性，不能在同一个topic中的多个partition中保证总的消费顺序性。一个消费者可以消费多个partition。

Kafka这种通过分区与分组进行并行消费的方式，让kafka拥有极大的吞吐量
```

![image-20230217155824905](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171558967.png)

小结一下：

- 一个partition只能被一个消费组中的一个消费者消费，目的是为了保证消费的顺序性，但是多个partion的多个消费者消费的总的顺序性是得不到保证的，那怎么做到消费的总顺序性呢？这个后面揭晓答案

- partition的数量决定了消费组中消费者的数量，建议同一个消费组中消费者的数量不要超过partition的数量，否则多的消费者消费不到消息

- 如果消费者挂了，那么会触发rebalance机制（后面介绍），会让其他消费者来消费该分区

- kafka通过partition 可以保证每条消息的原子性，但是不会保证每条消息的顺序性
  

## 6 Java客户端-生产者

### 6.1 引入依赖

```
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>2.4.1</version>
</dependency>
```

### 6.2 生产者核心概念

在消息发送的过程中，涉及到了两个线程

```
main 线程
Sender 线程
```

在 main 线程中创建了一个双端队列 RecordAccumulator。main 线程将消息发送给 RecordAccumulator；

Sender 线程不断从 RecordAccumulator 中拉取消息发送到 Kafka Broker
在main线程中，消息的生产，要经历拦截器、序列化器和分区器，其中一个分区就会创建一个队列，这样方便数据的管理

其中队列默认是32M，而存放到队列里面的数据也会经过压缩为16k再发往send线程进行发送，但是这样也会有问题，就是如果只有一条消息，难道就不发送了吗？其实还有一个参数linger.ms，用来表示一条消息如果超过这个时间就会直接发送，不用管大小，其实可以类比坐车的场景，人满或者时间到了 都发车
![image-20221023235243343](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171804504.png)

```
send线程发送给kafka集群的时候，我们需要联系到上面的Topic与Partition已经消费组，形成一个Partition对应consumer Group里面的一个consumer这种组内单播的效果，进行并发读写
```

### 6.3 生产者代码编写

```
package com.jiang.kafka.genenal;

import java.util.Properties;
import java.util.concurrent.ExecutionException;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.apache.kafka.clients.producer.RecordMetadata;
import org.apache.kafka.common.serialization.StringSerializer;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月17日 下午7:43:57
* @DESCRIPTION :
*/
public class MySimpleProducer {
	private final static String TOPIC_NAME = "my-replicated-topic";
	
	public static void main(String[] args) throws ExecutionException, InterruptedException{
		 // 1. 设置参数
    Properties props = new Properties();
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.133.103:9092,192.168.133.103:9093,192.168.133.103:9094");
    // 把发送的key从字符串序列化为字节数组，这里不采用jdk的序列化，而是自定义序列化方式
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    //把发送消息value从字符串序列化为字节数组
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
    // 2. 创建生产消息的客户端，传入参数
    Producer<String, String> producer = new KafkaProducer<String, String>(props);
    // 3.创建消息
    // key：作用是决定了往哪个分区上发，value：具体要发送的消息内容
    ProducerRecord<String, String> producerRecord = new ProducerRecord<String, String>(TOPIC_NAME, "mykeyvalue", "hellokafka");
    //4. 发送消息,得到消息发送的元数据并输出
    RecordMetadata metadata = producer.send(producerRecord).get();
    System.out.println("同步方式发送消息结果：" + "topic-" + metadata.topic() + "|partition-"
            + metadata.partition() + "|offset-" + metadata.offset());

	}

}
```

可以看到消息发出后有一个get()，其实这里有一个过程，就是Broker需要在收到消息后回复一个ACK表示确认收到

如果生产者发送消息没有收到ack，生产者会阻塞，阻塞到3s的时间，如果还没有收到消息，会进行重试。重试的次数3次

这里的应答ack有三个取值

0：生产者发送过来的数据，不需要应答
1：生产者发送过来的数据，Leader收到数据后会应答
-1（all），生产者发送过来的数据，Leader和ISR队列里面所有的结点收齐数据后应答，-1与

![image-20230217195043153](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171950218.png)



### 6.4 同步发送

```
RecordMetadata metadata = producer.send(producerRecord).get();
System.out.println("同步方式发送消息结果：" + "topic-" + metadata.topic() + "|partition-"
                   + metadata.partition() + "|offset-" + metadata.offset());

```

如果生产者发送消息没有收到ack，**生产者会阻塞，阻塞到3s的时间**，如果还没有收到消息，会进行重试。**重试的次数3次**

![image-20230218100905271](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20230218100905271.png)

### 6.5 异步发送

生产者发消息，发送完后不用等待broker给回复，直接执行下面的业务逻辑。可以提供callback，让broker异步的调用callback，告知生产者，消息发送的结果


```
//要发送 5 条消息
Order order = new Order((long) i, i);
//指定发送分区
ProducerRecord<String, String> producerRecord = new ProducerRecord<String, String>(TOPIC_NAME, 0 , order.getOrderId().toString(),JSON.toJSONString(order));
//异步回调方式发送消息
producer.send(producerRecord, new Callback() {
public void onCompletion(RecordMetadata metadata, Exception exception) {
if (exception != null) {
    System.err.println("发送消息失败：" +
    exception.getStackTrace());
}
if (metadata != null) {
System.out.println("异步方式发送消息结果：" + "topic-" +metadata.topic() + "|partition-"+ metadata.partition() + "|offset-" + metadata.offset());
         }
    }
});

```



### 6.6 生产者ACK配置

在同步发送的前提下，生产者在获得集群返回的ack之前会一直阻塞。那么集群什么时候返回ack呢？此时ack有3个配置：

ack = 0 kafka-cluster不需要任何的broker收到消息，就立即返回ack给生产者，最容易丢消息的，效率是最高的

ack=1（默认）： 多副本之间的leader已经收到消息，并把消息写入到本地的log中，才会返回ack给生产者，性能和安全性是最均衡的s

ack=-1/all。里面有默认的配置min.insync.replicas=2(默认为1，推荐配置大于等于2)，此时就需要leader和一个follower同步完后，才会返回ack给生产者（此时集群中有2个broker已完成数据的接收），这种方式最安全，但性能最差

下面是关于ack和重试（如果没有收到ack，就开启重试）的配置

```
props.put(ProducerConfig.ACKS_CONFIG, "1");
/*
 发送失败会重试，默认重试间隔100ms，重试能保证消息发送的可靠性，但是也可能造成消息重复发送，比如网络抖动，所以需要在
 接收者那边做好消息接收的幂等性处理
*/
props.put(ProducerConfig.RETRIES_CONFIG, 3); 
//重试间隔设置
props.put(ProducerConfig.RETRY_BACKOFF_MS_CONFIG, 300);

```



### 6.7发送消息的缓冲机制

![image-20230217195317858](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171953920.png)

kafka默认会创建一个消息缓冲区，用来存放要发送的消息，缓冲区是32m

```
props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432);
```

kafka本地线程会去缓冲区中一次拉16k的数据，发送到broker

```
props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
```



如果线程拉不到16k的数据，间隔10ms也会将已拉到的数据发到broker

```
props.put(ProducerConfig.LINGER_MS_CONFIG, 10);
```

- 发送会默认会重试 3 次，每次间隔100ms
- 发送的消息会先进入到本地缓冲区（32mb），kakfa会跑一个线程，该线程去缓冲区中取16k的数据，发送到kafka，如果到 10 毫秒数据没取满16k，也会发送一次。



## 7 Java消费者

### 7.1 消费者基本实现

```
package com.jiang.kafka.genenal;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.serialization.StringDeserializer;

import java.time.Duration;
import java.util.Arrays;
import java.util.Properties;

/**
 * @author 作者 jiangbaixiong
 * @version 创建时间：2023年2月18日 上午10:10:53
 * @DESCRIPTION :
 */
public class MySimpleConsumer {
	private final static String TOPIC_NAME = "my-replicated-topic";
	private final static String CONSUMER_GROUP_NAME = "testGroup";

	public static void main(String[] args) {
		Properties props = new Properties();
		props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "192.168.133.103:9092,192.168.133.103:9093,192.168.133.103:9094");
		// 消费分组名
		props.put(ConsumerConfig.GROUP_ID_CONFIG, CONSUMER_GROUP_NAME);
		props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
		props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
		// 1.创建一个消费者的客户端
		KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);
		// 2. 消费者订阅主题列表
		consumer.subscribe(Arrays.asList(TOPIC_NAME));
		while (true) {
			/*
			 * 3.poll() API 是拉取消息的长轮询
			 */
			ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
			for (ConsumerRecord<String, String> record : records) {
				// 4.打印消息
				System.out.printf("收到消息：partition = %d,offset = %d, key = %s, value = %s%n", record.partition(), record.offset(), record.key(), record.value());
			}

		}
	}
}

```

### 7.2 关于消费者自动提交和手动提交offset

#### 7.2.1 提交内容

消费者无论是自动提交还是手动提交，都需要把所属的消费组+消费的某个主题+消费的某个分区及消费的偏移量，这样的信息提交到集群的_consumer_offsets主题里面

#### 7.2.2 自动提交

消费者poll消息下来以后就会自动提交offset

```
// 是否自动提交offset，默认就是true
props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "true");
// 自动提交offset的间隔时间
props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
```

#### 7.2.3 手动提交

需要把自动提交的配置改成false

```
props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "false");
```

手动提交又分成了两种：

- 手动同步提交

  在消费完消息后调用同步提交的方法，当集群返回ack前一直阻塞，返回ack后表示提交成功，执行之后的逻辑

```
 while (true) {
      /*
       * poll() API 是拉取消息的长轮询
       */
      ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
      for (ConsumerRecord<String, String> record : records) {
        System.out.printf("收到消息：partition = %d,offset = %d, key = %s, value = %s%n", record.partition(),
          record.offset(), record.key(), record.value());
      }
      //所有的消息已消费完
      if (records.count() > 0) {//有消息
        // 手动同步提交offset，当前线程会阻塞直到offset提交成功
        // 一般使用同步提交，因为提交之后一般也没有什么逻辑代码了
        consumer.commitSync();//=======阻塞=== 提交成功
      }
    }
  }

```

- 手动异步提交

在消息消费完后提交，不需要等到集群ack，直接执行之后的逻辑，可以设置一个回调方法，供集群调用

```
 while (true) {
      /*
       * poll() API 是拉取消息的长轮询
       */
      ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
      for (ConsumerRecord<String, String> record : records) {
        System.out.printf("收到消息：partition = %d,offset = %d, key = %s, value = %s%n", record.partition(),
          record.offset(), record.key(), record.value());
      }
      //所有的消息已消费完
      if (records.count() > 0) { 
        // 手动异步提交offset，当前线程提交offset不会阻塞，可以继续处理后面的程序逻辑
        consumer.commitAsync(new OffsetCommitCallback() {
          @Override
          public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception exception) {
            if (exception != null) {
              System.err.println("Commit failed for " + offsets);
              System.err.println("Commit failed exception: " + exception.getStackTrace());
            }
          }
        });
      }
    }
  }

```

### 7.3 长轮询poll消息

- 默认情况下，消费者一次会poll500条消息。

```
//一次poll最大拉取消息的条数，可以根据消费速度的快慢来设置
props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 500);
```

- 代码中设置了长轮询的时间是1000毫秒

  ```
   while (true) {
        /*
         * poll() API 是拉取消息的长轮询
         */
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
        for (ConsumerRecord<String, String> record : records) {
          System.out.printf("收到消息：partition = %d,offset = %d, key = %s, value = %s%n", record.partition(),
            record.offset(), record.key(), record.value());
        }
  
  ```

  意味着：

  ```
  如果一次poll到500条，就直接执行for循环
  如果这一次没有poll到500条。且时间在1秒内，那么长轮询继续poll，要么到500条，要么到1s
  如果多次poll都没达到500条，且1秒时间到了，那么直接执行for循环
  ```

  如果两次poll的间隔超过30s，集群会认为该消费者的消费能力过弱，该消费者被踢出消费组，触发rebalance机制，rebalance机制会造成性能开销。可以通过设置这个参数，让一次poll的消息条数少一点

  ```
  // 一次poll最大拉取消息的条数，可以根据消费速度的快慢来设置
  props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 500);
  // 如果两次poll的时间如果超出了30s的时间间隔，kafka会认为其消费能力过弱，将其踢出消费组。将分区分配给其他消费者。-rebalance
  props.put(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG, 30 * 1000);
  ```

  我们可以想一想为什么kafka要这么做，这个其实和之前的send消息的时候一样，send消息的时候我们也有两个参数batch.size和linger.ms，当我们要发送的数据达到16KB或者超过linger.ms时间才会把消息发送出去.

  

  这里消费者消费消息也是同理，通过长轮询poll消息，保证每次处理的消息默认至少为500条，这样都是为了增加吞吐量

  **总结一下过程**：

  - 消费者建立了与broker之间的长连接，开始poll消息。
  - 默认一次poll500条消息（如果消费能力弱可以设置小一点，防止被踢出集群）

```
 props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 500);
```

可以根据消费速度的快慢来设置，因为如果两次poll的时间如果超出了30s的时间间隔，kafka会认为其消费能力过弱，将其踢出消费组。将分区分配给其他消费者。

可以通过这个值进行设置：

```
 props.put(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG, 30 * 1000);
```

- 如果每隔1s内没有poll到任何消息，则继续去poll消息，循环往复，直到poll到消息。如果超出了1s，则此次长轮询结束。

  ```
   ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
  ```

- 消费者发送心跳的时间间隔

```
 props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 1000);
```

- kafka如果超过10秒没有收到消费者的心跳，则会把消费者踢出消费组，进行rebalance，把分区分配给其他消费者。

```
 props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10 * 1000);
```

### 7.4 消费者的健康状态检查

消费者每隔`1s`向kafka集群发送心跳，集群发现如果有超过10s没有续约的消费者，将被踢出消费组，触发该消费组的`rebalance`机制，将该分区交给消费组里的其他消费者进行消费。

```
//consumer给broker发送心跳的间隔时间
props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 1000);
//kafka如果超过10秒没有收到消费者的心跳，则会把消费者踢出消费组，进行rebalance，把分区分配给其他消费者。
props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10 * 1000);
```

### 7.5 指定分区和偏移量、时间消费

- 指定分区消费

```
consumer.assign(Arrays.asList(new TopicPartition(TOPIC_NAME, 0)));
```

- 从头消费（回溯消费）

```
consumer.assign(Arrays.asList(new TopicPartition(TOPIC_NAME, 0)));
consumer.seekToBeginning(Arrays.asList(new TopicPartition(TOPIC_NAME, 0)));
```

- 指定offset消费

```
consumer.assign(Arrays.asList(new TopicPartition(TOPIC_NAME, 0)));
consumer.seek(new TopicPartition(TOPIC_NAME, 0), 10);
```

- 指定时间消费

```
List<PartitionInfo> topicPartitions = consumer.partitionsFor(TOPIC_NAME);
        //从1小时前开始消费
        long fetchDataTime = new Date().getTime() - 1000 * 60 * 60;
        Map<TopicPartition, Long> map = new HashMap<>();
        for (PartitionInfo par : topicPartitions) {
            map.put(new TopicPartition(TOPIC_NAME, par.partition()), fetchDataTime);
        }
        Map<TopicPartition, OffsetAndTimestamp> parMap = consumer.offsetsForTimes(map);
        for (Map.Entry<TopicPartition, OffsetAndTimestamp> entry : parMap.entrySet()) {
            TopicPartition key = entry.getKey();
            OffsetAndTimestamp value = entry.getValue();
            if (key == null || value == null) continue;
            Long offset = value.offset();
            System.out.println("partition-" + key.partition() + "|offset-" + offset);
            System.out.println();
            //根据消费里的timestamp确定offset
            if (value != null) {
                consumer.assign(Arrays.asList(key));
                consumer.seek(key, offset);
            }
        }


```

### 7.6 新消费组的消费offset规则

新消费组中的消费者在启动以后，默认会从当前分区的最后一条消息的offset+1开始消费（消费新消息）。可以通过以下的设置，让新的消费者第一次从头开始消费。之后开始消费新消息（最后消费的位置的偏移量+1）

Latest:默认的，消费新消息
earliest：第一次从头开始消费。之后开始消费新消息（最后消费的位置的偏移量+1）

```
props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
```

## 8 Spring boot集成Kafka

### 8.1 引入依赖

```
<dependency>
      <groupId>org.springframework.kafka</groupId>
      <artifactId>spring-kafka</artifactId>
</dependency>
```

### 8.2 配置文件

```
server:
  port: 8080
spring:
  kafka:
    bootstrap-servers: 172.16.253.38:9092,172.16.253.38:9093,172.16.253.38:9094
    producer: # 生产者
      retries: 3 # 设置大于0的值，则客户端会将发送失败的记录重新发送
      batch-size: 16384 # 每次发送时多少一批次 这里设置的是16kb
      buffer-memory: 33554432 # 设置内存缓存区32Mb
      acks: 1 # leader收到消息后就返回ack
      # 指定消息key和消息体的编解码方式
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      group-id: default-group # 组内单播，组间广播
      enable-auto-commit: false # 关闭消费自动提交
      auto-offset-reset: earliest # 新消费组启动会从头信息消费
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      max-poll-records: 500 # 每次长轮询拉取多少条消息
    listener:
      # 当每一条记录被消费者监听器（ListenerConsumer）处理之后提交
      # RECORD
      # 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后提交
      # BATCH
      # 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后，距离上次提交时间大于TIME时提交
      # TIME
      # 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后，被处理record数量大于等于COUNT时提交
      # COUNT
      # TIME |　COUNT　有一个条件满足时提交
      # COUNT_TIME
      # 当每一批poll()的数据被消费者监听器（ListenerConsumer）处理之后, 手动调用Acknowledgment.acknowledge()后提交
      # MANUAL
      # 手动调用Acknowledgment.acknowledge()后立即提交，一般使用这种
      # MANUAL_IMMEDIATE
      ack-mode: MANUAL_IMMEDIATE
  redis:
    host: 172.16.253.21


```

### 8.3 编写生产者

```
package com.qf.kafka.spring.boot.demo.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/msg")
public class MyKafkaController {

  private final static String TOPIC_NAME = "my-replicated-topic";

  @Autowired
  private KafkaTemplate<String,String> kafkaTemplate;

  @RequestMapping("/send")
  public String sendMessage(){

    kafkaTemplate.send(TOPIC_NAME,0,"key","this is a message!");

    return "send success!";

  }
}


```

### 8.4 编写消费者

```
package com.qf.kafka.spring.boot.demo.consumer;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.kafka.support.Acknowledgment;
import org.springframework.stereotype.Component;

@Component
public class MyConsumer {

  @KafkaListener(topics = "my-replicated-topic",groupId = "MyGroup1")
  public void listenGroup(ConsumerRecord<String, String> record, Acknowledgment ack) {
    String value = record.value();
    System.out.println(value);
    System.out.println(record);
    //手动提交offset
    ack.acknowledge();
  }

}


```

### 8.5 消费者中配置消费主题、分区和偏移量

```
  @KafkaListener(groupId = "testGroup", topicPartitions = {
    @TopicPartition(topic = "topic1", partitions = {"0", "1"}),
    @TopicPartition(topic = "topic2", partitions = "0",
      partitionOffsets = @PartitionOffset(partition = "1", initialOffset = "100"))
  },concurrency = "3")//concurrency就是同组下的消费者个数，就是并发消费数，建议小于等于分区总数
  public void listenGroupPro(ConsumerRecord<String, String> record, Acknowledgment ack) {
    String value = record.value();
    System.out.println(value);
    System.out.println(record);
    //手动提交offset
    ack.acknowledge();
  }


```



## 9 kafka集群中的controller、rebalance、HW

### 9.1 controller

什么是controller呢？其实就是集群中的一个broker，当集群中的leader挂掉时需要controller来组织进行选举

那么集群中谁来充当controller呢？

每个broker启动时会向zk创建一个临时序号节点，获得的序号最小的那个broker将会作为集群中的controller，负责这么几件事：

- 当集群中有一个副本的leader挂掉，需要在集群中选举出一个新的leader，选举的规则是从isr集合中最左边获得
- 当集群中有broker新增或减少，controller会同步信息给其他broker
- 当集群中有分区新增或减少，controller会同步信息给其他broker
  

### 9.2 rebalance机制

前提：消费组中的消费者没有指明分区来消费

触发的条件：当消费组中的消费者和分区的关系发生变化的时候

分区分配的策略：在rebalance之前，分区怎么分配会有这么三种策略

- range：根据公式计算得到每个消费者消费哪几个分区：第一个消费者是（分区总数 / 消费者数量 ）+1，之后的消费者是分区总数/消费者数量（假设 n＝分区数／消费者数量 = 2， m＝分区数%消费者数量 = 1，那么前 m 个消费者每个分配 n+1 个分区，后面的（消费者数量－m ）个消费者每个分配 n 个分区）
- 轮询：大家轮着来
- sticky：粘合策略，如果需要rebalance，会在之前已分配的基础上调整，不会改变之前的分配情况。如果这个策略没有开，那么就要进行全部的重新分配。建议开启
  

### 9.3 HW和LEO

LEO是某个副本最后消息的消息位置（log-end-offset）

HW是已完成同步的位置。消息在写入broker时，且每个broker完成这条消息的同步后，hw才会变化。在这之前消费者是消费不到这条消息的。在同步完成之后，HW更新之后，消费者才能消费到这条消息，这样的目的是防止消息的丢失。


![截屏2021-08-24 上午11.33.41](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302181114714.png)

## 10 kafka优化

10.1 如何防止消息丢失

- 生产者：1）使用同步发送 2）把ack设成1或者all，并且设置同步的分区数>=2
- 消费者：把自动提交改成手动提交

### 10.2 如何防止重复消费

在防止消息丢失的方案中，如果生产者发送完消息后，因为网络抖动，没有收到ack，但实际上broker已经收到了。

此时生产者会进行重试，于是broker就会收到多条相同的消息，而造成消费者的重复消费。

怎么解决：

生产者关闭重试：会造成丢消息（不建议）

消费者解决非幂等性消费问题：

所谓的幂等性：多次访问的结果是一样的。对于rest的请求（get（幂等）、post（非幂等）、put（幂等）、delete（幂等））

解决方案：

- 在数据库中创建联合主键，防止相同的主键 创建出多条记录，mysql 插入业务id作为主键，主键是唯一的，所以一次只能插入一条
- 使用分布式锁，以业务id为锁。保证只有一条记录能够创建成功（使用redis或zk的分布式锁（主流的方案））



### 10.3 如何做到消息的顺序消费

![image-20221030160545934](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302181118350.png)

- 生产者：使用同步的发送，并且通过设置key指定路由策略，只发送到一个分区中；ack设置成非0的值。

- 消费者：主题只能设置一个分区，消费组中只能有一个消费者；不要设置异步线程防止异步导致的乱序，或者设置一个阻塞队列进行异步消费

  

  kafka的顺序消费使用场景不多，因为牺牲掉了性能，但是比如rocketmq在这一块有专门的功能已设计好。
  

### 10.4 如何解决消息积压问题

![截屏2021-08-24 下午2.30.44](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302181118942.png)

> 消息积压会导致很多问题，比如磁盘被打满、生产端发消息导致kafka性能过慢，就容易出现服务雪崩，就需要有相应的手段：

- 方案一：在一个消费者中启动多个线程，让多个线程同时消费。——提升一个消费者的消费能力（增加分区增加消费者）。
- 方案二：如果方案一还不够的话，这个时候可以启动多个消费者，多个消费者部署在不同的服务器上。其实多个消费者部署在同一服务器上也可以提高消费能力——充分利用服务器的cpu资源。
- 方案三：让一个消费者去把收到的消息往另外一个topic上发，另一个topic设置多个分区和多个消费者 ，进行具体的业务消费。

![截屏2021-08-24 下午2.43.04](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302181121426.png)

### 10.5 实现延时队列的效果

应用场景

订单创建后，超过30分钟没有支付，则需要取消订单，这种场景可以通过延时队列来实现

具体方案

![截屏2021-08-24 下午2.43.04](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302181120900.png)

- kafka中创建创建相应的主题
- 消费者消费该主题的消息（轮询）
- 消费者消费消息时判断消息的创建时间和当前时间是否超过30分钟（前提是订单没支付）
  如果是：去数据库中修改订单状态为已取消
  如果否：记录当前消息的offset，并不再继续消费之后的消息。等待1分钟后，再次向kafka拉取该offset及之后的消息，继续进行判断，以此反复。



## 11 安装Kafka-eagle

### 11.1 下载安装包

http://www.kafka-eagle.org/

官网地址 https://www.kafka-eagle.org/
github地址:https://github.com/smartloli/kafka-eagle

```
wget https://github.com/smartloli/kafka-eagle-bin/archive/v2.0.6.tar.gz


tar -zxvf kafka-eagle-web-2.0.6-bin.tar.gz -C /usr/local/
cd /usr/local/
mv kafka-eagle-web-2.0.6 kafka-eagle
```

![image-20230217184521052](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171845102.png)

### 11.2 准备环境

- 安装jdk
- 安装mysql

```
mysql -uroot -p123456

```



- 解压缩后修改配置文件 system-config.properties

```
# 配置zk  去掉cluster2
efak.zk.cluster.alias=cluster1
cluster1.zk.list=192.168.133.103:2181
# cluster2.zk.list=xdn10:2181,xdn11:2181,xdn12:2181

#注释掉sqlite jdbc driver address 修改mysql jdbc driver address 改成自己的MySQL密码

# 配置mysql
kafka.eagle.driver=com.mysql.cj.jdbc.Driver
kafka.eagle.url=jdbc:mysql://192.168.133.103:3306/ke?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
kafka.eagle.username=root
kafka.eagle.password= 123456

```

![image-20230217191547109](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171915161.png)

修改后

![image-20230217191701512](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171917565.png)

修改环境变量

```
vi /etc/profile 

export  JAVA_HOME=/usr/java/jdk1.8.0_51
CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar
export KE_HOME=/usr/local/kafka-eagle
export PATH=$PATH:$KE_HOME/bin

source  /etc/profile


```

![image-20230217192006911](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171920967.png)



进入到bin目录，为ke.sh增加可执行的权限

```
cd /usr/local/kafka-eagle/bin

chmod +x ke.sh
```

### 11.3 启动

```
./ke.sh start
```

![image-20230217192504978](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171925031.png)

初始用户名：admin

密码：123456

访问地址：http://192.168.133.103:8048/

![image-20230217193256107](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171932200.png)

观察主题列表

![image-20230217193646924](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302171936023.png)

- kafka 如果开启了acl ssl 需要修改配置

  ```
  ######################################
  # kafka jmx acl and ssl authenticate
  ######################################
  cluster1.kafka.eagle.jmx.acl=true
  cluster1.kafka.eagle.jmx.user=keadmin
  cluster1.kafka.eagle.jmx.password=keadmin123
  cluster1.kafka.eagle.jmx.ssl=false
  cluster1.kafka.eagle.jmx.truststore.location=/Users/dengjie/workspace/ssl/certificates/kafka.truststore
  cluster1.kafka.eagle.jmx.truststore.password=ke123456
  
  ######################################
  
  ```

  - 如果Kafka开启了安全认证如则需要修改sasl设置

  ```
  cluster1.kafka.eagle.sasl.enable=true
  cluster1.kafka.eagle.sasl.protocol=SASL_PLAINTEXT
  cluster1.kafka.eagle.sasl.mechanism=GSSAPI
  cluster1.kafka.eagle.sasl.jaas.config=com.sun.security.auth.module.Krb5LoginModule required useKeyTab=true storeKey=true keyTab="/etc/security/keytabs/kafka_client.keytab" principal="kafka-eagle.org@EXAMPLE.COM";
  # make sure there is a local ticket cache "klist -l" to view
  # cluster1.kafka.eagle.sasl.jaas.config=com.sun.security.auth.module.Krb5LoginModule required useTicketCache=true renewTicket=true serviceName="kafka-eagle.org";
  # if your kafka cluster doesn't require it, you don't need to set it up
  # cluster1.kafka.eagle.sasl.client.id=
  
  ```

  

- 如果开启了zookeeper acl认证 需要修改以下配置

  ```
  # Add zookeeper acl
  cluster1.zk.acl.enable=true
  cluster1.zk.acl.schema=digest
  cluster1.zk.acl.username=test
  cluster1.zk.acl.password=test123
  
  ```

  

