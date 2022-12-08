# RocketMq

## 1 介绍

RocketMQ作为一款纯java、分布式、队列模型的开源消息中间件，支持事务消息、顺序消息、批量消息、定时消息、消息回溯等。

## 2 基本概念

RocketMQ主要有四大核心组成部分：**NameServer**、**Broker**、**Producer**以及**Consumer**四部分。

### 2.1 Name Server

Name Server是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。

NameServer 是整个 RocketMQ 的“大脑” ，它是 RocketMQ 的服务注册中心，所以 RocketMQ 需要先启动 NameServer 再启动 Rocket 中的 Broker。

### 2.2Broker

消息服务器（Broker）是消息存储中心，主要作用是接收来自 Producer 的消息并存储，Consumer 从这里取得消息。它还存储与消息相关的元数据，包括用户组、消费进度偏移量、队列信息等。从部署结构图中可以看出 Broker 有 Master 和 Slave 两种类型，Master 既可以写又可以读，Slave不可以写只可以读。

### 2.3Producer

也称为消息发布者，负责生产并发送消息至 Topic。
生产者向brokers发送由业务应用程序系统生成的消息。RocketMQ提供了发送：同步、异步和单向（one-way）的多种范例。

### 2.4 Consumer

也称为消息订阅者，负责从 Topic 接收并消费消息。
消费者从brokers那里拉取信息并将其输入应用程序。

## 3 安装

注意：要先安装JDK。**unzip解压缩rocketmq到指定位置**

配置环境变量



如果是Windows进入bin 目录

打开runserver.cmd,把Xms、Xmx、-Xmn都设置成512m



**同理设置 runbroker**

启动nameserver 

鼠标双击执行bin目录下的mqnamesrv.cmd 文件 或者 cmd 打开控制台，切换目录到rocket的bin目录下，执行命令：start mqnamesrv.cmd

启动broker  - 执行bin目录下的 mqbroker.cmd 文件 或者 cmd 打开控制台，切换目录到rocket的bin目录下，执行命令：start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true（默认端口是9876 autoCreateTopicEnable=true表示允许自动创建topic）



RocketMQ控制台安装

载地址源码
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