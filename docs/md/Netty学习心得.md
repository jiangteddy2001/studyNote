# Netty

## 1、概念

Netty 是由 JBOSS 提供的一个 Java 开源框架。Netty 提供异步的、基于事件驱动的网络应用程序框架，用以快速开发高性能、高可靠性的网络 IO 程序,是目前最流行的 NIO 框架，Netty 在互联网领域、大数据分布式计算领域、游戏行业、通信行业等获得了广泛的应用，知名的 Elasticsearch 、Dubbo 框架内部都采用了 Netty。

![image-20221211152146883](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111521933.png)

典型应用：

- 阿里分布式服务框架 Dubbo，默认使用 Netty 作为基础通信组件，
- 还有 RocketMQ 也是使用 Netty 作为通讯的基础。

常见的使用场景如下：

- 互联网行业 在分布式系统中，各个节点之间需要远程服务调用，高性能的RPC框架必不可少，Netty作为异步高新能的通信框架,往往作为基础通信组件被这些RPC框架使用。 典型的应用有：阿里分布式服务框架Dubbo的RPC框架使用Dubbo协议进行节点间通信，Dubbo协议默认使用Netty作为基础通信组件，用于实现各进程节点之间的内部通信。
- 游戏行业 无论是手游服务端还是大型的网络游戏，Java语言得到了越来越广泛的应用。Netty作为高性能的基础通信组件，它本身提供了TCP/UDP和HTTP协议栈。 非常方便定制和开发私有协议栈，账号登录服务器，地图服务器之间可以方便的通过Netty进行高性能的通信
- 大数据领域 经典的Hadoop的高性能通信和序列化组件Avro的RPC框架，默认采用Netty进行跨界点通信，它的Netty Service基于Netty框架二次封装实现



## 2、NIO

- BIO：一个连接一个线程，客户端有连接请求时服务器端就需要启动一个线程进行处理。线程开销大。
  伪异步IO：将请求连接放入线程池，一对多，但线程还是很宝贵的资源。
- NIO：一个请求一个线程，但客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

- AIO：一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理，
  

NIO的特点：事件驱动模型、单线程处理多任务、非阻塞I/O，I/O读写不再阻塞，而是返回0、基于block的传输比基于流的传输更高效、更高级的IO函数zero-copy、IO多路复用大大提高了Java网络应用的可伸缩性和实用性。基于Reactor线程模型。

## 3、线程模型

Netty主要基于主从Reactors多线程模型（如下图）做了一定的修改，其中主从Reactor多线程模型有多个Reactor：MainReactor和SubReactor：

- MainReactor负责客户端的连接请求，并将请求转交给SubReactor
- SubReactor负责相应通道的IO读写请求
- 非IO请求（具体逻辑处理）的任务则会直接写入队列，等待worker threads进行处理



![image-20221211152114798](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111521955.png)

## 4、使用Netty

```
<dependency>
        <groupId>io.netty</groupId>
        <artifactId>netty-all</artifactId>
        <version>4.1.20.Final</version>
</dependency>
```

功能：

- 传输服务 支持BIO和NIO
- 容器集成 支持OSGI、JBossMC、Spring、Guice容器
- 协议支持 HTTP、Protobuf、二进制、文本、WebSocket等一系列常见协议都支持。 还支持通过实行编码解码逻辑来实现自定义协议
- Core核心 可扩展事件模型、通用通信API、支持零拷贝的ByteBuf缓冲对象



## 5、核心组件

- Channel：Netty 网络操作抽象类，它除了包括基本的 I/O 操作，如 bind、connect、read、write 等。
- EventLoop：主要是配合 Channel 处理 I/O 操作，用来处理连接的生命周期中所发生的事情。
- ChannelFuture：Netty 框架中所有的 I/O 操作都为异步的，因此我们需要 ChannelFuture 的 addListener()注册一个 ChannelFutureListener 监听事件，当操作执行成功或者失败时，监听就会自动触发返回结果。
- ChannelHandler：充当了所有处理入站和出站数据的逻辑容器。ChannelHandler 主要用来处理各种事件，这里的事件很广泛，比如可以是连接、数据接收、异常、数据转换等。
- ChannelPipeline：为 ChannelHandler 链提供了容器，当 channel 创建时，就会被自动分配到它专属的 ChannelPipeline，这个关联是永久性的。



## 6、工作架构

![image-20221211152522696](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111525753.png)

server端包含1个Boss NioEventLoopGroup和1个Worker NioEventLoopGroup，NioEventLoopGroup相当于1个事件循环组，这个组里包含多个事件循环NioEventLoop，每个NioEventLoop包含1个selector和1个事件循环线程。

每个Boss NioEventLoop循环执行的任务包含3步：

1 轮询accept事件

2 处理accept I/O事件，与Client建立连接，生成NioSocketChannel，并将NioSocketChannel注册到某个Worker NioEventLoop的Selector上 *3 处理任务队列中的任务，runAllTasks。

任务队列中的任务包括用户调用eventloop.execute或schedule执行的任务，或者其它线程提交到该eventloop的任务。

每个Worker NioEventLoop循环执行的任务包含3步：

1 轮询read、write事件；

2 处I/O事件，即read、write事件，在NioSocketChannel可读、可写事件发生时进行处理

3 处理任务队列中的任务，runAllTasks。



## 7、TCP 粘包/拆包

### 7.1 TCP粘包/分包的原因：

TCP是以流的方式来处理数据，一个完整的包可能会被TCP拆分成多个包进行发送，也可能把小的封装成一个大的数据包发送。

拆包：一个完整的应用包可能会被 TCP 拆分成多个包进行发送。

粘包：也可能把小应用包的封装成一个大的 TCP数据包发送。

应用程序写入的字节大小大于套接字发送缓冲区的大小，会发生拆包现象，

而应用程序写入数据小于套接字缓冲区大小，网卡将应用多次写入的数据发送到网络上，这将会发生粘包现象。


### 7.2解决办法

Netty中提供了多个 Decoder 解析类 用于解决上述问题：

- FixedLengthFrameDecoder 、LengthFieldBasedFrameDecoder ，固定长度是消息头指定消息长度的一种形式，进行粘包拆包处理的。

- LineBasedFrameDecoder 、DelimiterBasedFrameDecoder ，换行是于指定消息边界方式的一种形式，进行消息粘
  

## 8 心跳检测

### 8.1 支持的心跳类型

- readerIdleTime：为读超时时间（即测试端一定时间内未接受到被测试端消息）。
- writerIdleTime：为写超时时间（即测试端一定时间内向被测试端发送消息）。
- allIdleTime：所有类型的超时时间。

### 8.2 核心实现--IdleStateHandler

public IdleStateHandler(int readerIdleTimeSeconds, int writerIdleTimeSeconds, int allIdleTimeSeconds) {
    this((long)readerIdleTimeSeconds, (long)writerIdleTimeSeconds, (long)allIdleTimeSeconds, TimeUnit.SECONDS);
}

核心三大参数

- readerIdleTimeSeconds: 读超时. 即当在指定的时间间隔内没有从 Channel 读取到数据时, 会触发一个 READER_IDLE 的 IdleStateEvent 事件.
- writerIdleTimeSeconds: 写超时. 即当在指定的时间间隔内没有数据写入到 Channel 时, 会触发一个 WRITER_IDLE 的 IdleStateEvent 事件.
- allIdleTimeSeconds: 读/写超时. 即当在指定的时间间隔内没有读或写操作时, 会触发一个 ALL_IDLE 的 IdleStateEvent 事件.

IdleStateHandler心跳检测主要是通过向线程任务队列中添加定时任务，判断channelRead()方法或write()方法是否调用空闲超时，如果超时则触发超时事件执行自定义userEventTrigger()方法；

Netty通过IdleStateHandler实现最常见的心跳机制不是一种双向心跳的PING-PONG模式，而是客户端发送心跳数据包，服务端接收心跳但不回复，因为如果服务端同时有上千个连接，心跳的回复需要消耗大量网络资源；如果服务端一段时间内内有收到客户端的心跳数据包则认为客户端已经下线，将通道关闭避免资源的浪费；在这种心跳模式下服务端可以感知客户端的存活情况，无论是宕机的正常下线还是网络问题的非正常下线，服务端都能感知到，而客户端不能感知到服务端的非正常下线；

要想实现客户端感知服务端的存活情况，需要进行双向的心跳；Netty中的channelInactive()方法是通过Socket连接关闭时挥手数据包触发的，因此可以通过channelInactive()方法感知正常的下线情况，但是因为网络异常等非正常下线则无法感知；

## 9 Netty长连接

1. 可以实现socket长连接，心跳机制每隔N秒客户端给服务器发送一条消息，代表客户端还存活。
2. 可以实现在随意代码位置按照用户id标识，发送消息给服务端。



## 10 实现断线重连

- 如何监听到客户端和服务端连接断开 ?
- 如何实现断线后重新连接 ?
- netty客户端线程给多大比较合理 ?

## 11 Netty启动默认调用线程数

Netty的默认启动了电脑可用线程数的两倍，在调用了bind方法的时候执行。

我们真的需要每个EventLoopGroup都默认启用线程吗？

答案当然不是！

我们具体的线程数应该交由业务来决定而不是使用默认的参数。

有的时候我们只需要使用一个线程来监听注册事件。

比如，在客户端代码中， 线程数的调整，非常有必要。

## 12 Netty零拷贝

### 12.1什么是零拷贝

传统拷贝：

是在发送数据的时候，传统的实现方式是：

- File.read(bytes)

- Socket.send(bytes)

这种方式需要四次数据拷贝和四次上下文切换：

- 数据从磁盘读取到内核的read buffer
- 数据从内核缓冲区拷贝到用户缓冲区
- 数据从用户缓冲区拷贝到内核的socket buffer
- 数据从内核的socket buffer拷贝到网卡接口（硬件）的缓冲区

零拷贝指在I/O操作过程中

a. 不消耗CPU资源来控制数据拷贝

b. 直接在内核空间完成数据操作

明显上面的第二步和第三步是没有必要的，通过java的FileChannel.transferTo方法，可以避免上面两次多余的拷贝（当然这需要底层操作系统支持）

1. 调用transferTo,数据从文件由DMA引擎拷贝到内核read buffer
2. 接着DMA从内核read buffer将数据拷贝到网卡接口buffer

### 12.2 实现零拷贝

主要体现在三个方面：

1、bytebuffer

Netty发送和接收消息主要使用bytebuffer，bytebuffer使用对外内存（DirectMemory）直接进行[Socket](https://so.csdn.net/so/search?q=Socket&spm=1001.2101.3001.7020)读写。

原因：如果使用传统的堆内存进行Socket读写，JVM会将堆内存[buffer](https://so.csdn.net/so/search?q=buffer&spm=1001.2101.3001.7020)拷贝一份到直接内存中然后再写入socket，多了一次缓冲区的内存拷贝。DirectMemory中可以直接通过DMA发送到网卡接口

2、Composite Buffers

传统的ByteBuffer，如果需要将两个ByteBuffer中的数据组合到一起，我们需要首先创建一个size=size1+size2大小的新的数组，然后将两个数组中的数据拷贝到新的数组中。但是使用Netty提供的组合ByteBuf，就可以避免这样的操作，因为CompositeByteBuf并没有真正将多个Buffer组合起来，而是保存了它们的引用，从而避免了数据的拷贝，实现了零拷贝。

3、对于FileChannel.transferTo的使用

Netty中使用了FileChannel的transferTo方法，该方法依赖于操作系统实现零拷贝。



## 13 Netty内存池

![image-20221211154225145](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111542183.png)

**Netty内存池的思想源于 jemalloc github ，可以说是 jemalloc 的 Java 版本**

Netty 对内存大小划分为：Tiny、Small、Normal 和 Huge 四类。

Netty 默认向操作系统申请的内存大小为 16MB，对于大于 16MB 的内存定义为 Huge 类型，

Netty 对 Huge 类型的处理方式为：

**大型内存不做缓存、不做池化，直接以 Unpool 的形式分配内存，用完后回收**。

Tiny、Small、Normal类型

对于 16MB 及更小的内存，分类为：Tiny、Small、Normal，也有对应的枚举 SizeClass 进行描述。

![image-20221211154010535](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111540632.png)



![image-20221211154255917](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111542968.png)
