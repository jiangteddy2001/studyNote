# Rabbitmq

## 1、简介

RabbitMQ是一个由erlang开发的AMQP（Advanced Message Queue 高级消息队列协议 ）的开源实现，由于erlang 语言的高并发特性，性能较好，本质是个队列，FIFO 先入先出，里面存放的内容是message

## 2、为什么用MQ

消息队列的核心作用3 个：**解耦**、**异步**、**削峰**。

## 3、核心概念

生产者：产生数据发送消息的程序是生产者

交换机
交换机是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

队列
队列是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

消费者
消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

![image-20221222143350817](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212221433908.png)

组成部分说明：

Broker：消息队列服务进程，此进程包括两个部分：Exchange和Queue
Exchange：消息队列交换机，按一定的规则将消息路由转发到某个队列，对消息进行过虑。
Queue：消息队列，存储消息的队列，消息到达队列并转发给指定的
Producer：消息生产者，即生产方客户端，生产方客户端将消息发送
Consumer：消息消费者，即消费方客户端，接收MQ转发的消息。
生产者发送消息流程：

1、生产者和Broker建立TCP连接。

2、生产者和Broker建立通道。

3、生产者通过通道消息发送给Broker，由Exchange将消息进行转发。

4、Exchange将消息转发到指定的Queue（队列）

消费者接收消息流程：

1、消费者和Broker建立TCP连接

2、消费者和Broker建立通道

3、消费者监听指定的Queue（队列）

4、当有消息到达Queue时Broker默认将消息推送给消费者。

5、消费者接收到消息。

6、ack回复

## 4、安装Rabbitmq

### 4.1 RPM安装

- rpm -ivh erlang-23.3.4.5-1.el8.x86_64.rpm    #先安装erlang环境
- rpm -ivh socat-1.7.4.1-1.el8.x86_64.rpm   #这里是安装依赖包
- rpm -ivh rabbitmq-server-3.8.19-1.el8.noarch.rpm

- systemctl start rabbitmq-server.service     #启动rabbitmq
- systemctl status rabbitmq-server.service     #查看状态
- systemctl enable rabbitmq-server.service     #添加到启动项

- systemctl stop rabbitmq-server.service     #先停止rabbitmq的运行，如果没有启动跳过这一步
- rabbitmq-plugins enable rabbitmq_management     #安装web插件
- systemctl start rabbitmq-server.service     #再次启动

- firewall-cmd --zone=public --permanent --add-port=15672/tcp    #防火墙添加15672端口
- systemctl restart firewalld    #重启防火墙
- firewall-cmd --list-all    #查看防火墙状态

打开本地的WEB管理界面http://192.168.133.102:15672/

### 4.2基本配置

- rabbitmqctl add_user admin 123   #创建账号admin，密码123
- rabbitmqctl set_user_tags admin administrator    #设置用户admin的角色为administrator超级管理员
- rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"    #设置用户权限，所有资源的配置、写、读权限

然后用刚才创建的用户名登录

![image-20221222144513896](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212221445967.png)

![image-20221222144529863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212221445911.png)