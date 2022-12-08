# EMQX知识点

## 产品概述

*EMQ X* (Erlang/Enterprise/Elastic MQTT Broker) 是基于 Erlang/OTP 平台开发的开源物联网 MQTT 消息服务器。



## 安装

1 windows安装

```
EMQ 注册Windows 服务:
bin\emqttd install

EMQ 服务启动:
bin\emqttd start

EMQ 服务停止:
bin\emqttd stop

EMQ 服务卸载:
bin\emqttd uninstall
```

如果是Linux 命令转成emqx start

## Dashboard 

EMQ X 提供了 Dashboard 以方便用户管理设备与监控相关指标。通过 Dashboard，你可以查看服务器基本信息、负载情况和统计数据，可以查看某个客户端的连接状态等信息甚至断开其连接，也可以动态加载和卸载指定插件。除此之外，EMQ X Dashboard 还提供了规则引擎的可视化操作界面，同时集成了一个简易的 MQTT 客户端工具供用户测试使用。

控制台地址: [http://127.0.0.1:18083](http://127.0.0.1:18083/)，默认用户: admin，密码：public

进入EMQ管理控制台还可以将其设置为中文面板。

### （一）监控页面

1连接数

显示最大的连接数（1024000）---实际11239

2 主题数

经测试单个客户端最大订阅主题数81515

3 保留消息数

服务端收到 Retain 标志为 1 的 PUBLISH 报文时，会将该报文视为保留消息，除了被正常转发以外，保留消息会被存储在服务端，每个主题下只能存在一份保留消息，因此如果已经存在相同主题的保留消息，则该保留消息被替换。

保留消息虽然存储在服务端中，但它并不属于会话的一部分。也就是说，即便发布这个保留消息的会话终结，保留消息也不会被删除。删除保留消息只有两种方式：

客户端往某个主题发送一个 Payload 为空的保留消息，服务端就会删除这个主题下的保留消息。
如果包含保留消息的 PUBLISH 报文设置了消息过期间隔属性，那么保留消息在服务端存储超过过期时间后就会被删除。

4 会话数



5 订阅数



6 共享订阅数

多个客户端订阅了同一个主题，发布者发布主题时，每个客户端都会同时收到这个主题的消息。在客户端集群部署的场景下会出现消息重复处理的问题。
EMQ支持共享订阅，多个客户端订阅了同一个主题，发布者发布主题时，只有其中一个客户端接收到消息。

### （二） 客户端

1 可以查看客户端详情



2 查看客户端列表



### （三）订阅

页面提供了指定节点下的所有订阅信息，并且支持用户通过 `Client ID` 查询指定客户端的所有订阅。



## 功能点

### 1 创建用户方法

打开管理后台http://x.x.x.x:18083/
左侧根目录中选择Plugins
启用emqx_auth_username，然后添加用户和密码



### 2 最大允许连接数

设置 TCP 监听器的 Acceptor 池大小,配置文件 emqx/etc/emqx.conf:

1. \## TCP Listener
2. listener.tcp.external = 0.0.0.0:1883
3. listener.tcp.external.acceptors = 64
4. listener.tcp.external.max_connections = 1024000



### 3 速率限制

速率限制是一种 *backpressure* 方案，从入口处避免了系统过载，保证了系统的稳定和可预测的吞吐。速率限制可在 `etc/emqx.conf` 中配置：

| 配置项                                     | 类型            | 默认值 | 描述                                 |
| ------------------------------------------ | --------------- | ------ | ------------------------------------ |
| listener.tcp.external.max_conn_rate        | Number          | 1000   | 本节点上允许的最大连接速率 (conn/s)  |
| zone.external.rate_limit.conn_messagess_in | Number,Duration | 无限制 | 单连接上允许的最大发布速率 (msg/s)   |
| zone.external.rate_limit.conn_bytes_in     | Size,Duration   | 无限制 | 单连接上允许的最大报文速率 (bytes/s) |

### 4 acl规则

**发布订阅 ACL** 指对 **发布 (PUBLISH)/订阅 (SUBSCRIBE)** 操作的 **权限控制**。

EMQ X 支持使用配置文件、外部主流数据库和自定义 HTTP API 作为 ACL 数据源。

客户端订阅主题、发布消息时插件通过检查目标主题（Topic）是否在指定数据源允许/禁止列表内来实现对客户端的发布、订阅权限管理。

可以采用Mnesia ACL 使用 EMQ X 内置的 Mnesia 数据库存储 ACL 规则

步骤：

1 开启插件



2调用HTTP接口



3 测试采用1个服务端发布，2个客户端接受。最后只有1个客户端能订阅



注意点：

1 修改D:\dev\emqx-windows-v4.1.5\emqx\etc\plugins路径下配置文件emqx_auth_mnesia.conf

改 auth.mnesia.as = clientid

**2 注意文档和安装的EMQX的版本是否一致**



### 5 使用MYSQL配置ACL规则

安装插件emqx_auth_mysql

修改emqx_auth_mysql.conf配置文件



新建数据库后建立2个表，然后SQL语句插入权限

INSERT INTO mqtt_acl (allow, ipaddr, username, clientid, access, topic) VALUES (1, '127.0.0.1', NULL, 'jiang', 1, 'test');
INSERT INTO mqtt_acl (allow, ipaddr, username, clientid, access, topic) VALUES (0, '127.0.0.1', NULL, 'jiang123', 1, 'test');





## 接口HTTP API

EMQ X 提供了 HTTP API 以实现与外部系统的集成，例如查询客户端信息、发布消息和创建规则等。

EMQ X 的 HTTP API 服务默认监听 8081 端口，可通过 `etc/plugins/emqx_management.conf` 配置文件修改监听端口，或启用 HTTPS 监听。



接口安全

EMQ X 的 HTTP API 使用 [Basic 认证 (opens new window)](https://en.wikipedia.org/wiki/Basic_access_authentication)方式，`id` 和 `password` 须分别填写 AppID 和 AppSecret。 默认的 AppID 和 AppSecret 是：`admin/public`。你可以在 Dashboard 的左侧菜单栏里，选择 "管理" -> "应用" 来修改和添加 AppID/AppSecret。





## 测试用例

先修改配置文件

测试链接速率改为10000
system.large_heap=64MB

测试场景
1 100万连接+100万主题订阅
2 30万吞吐，100个PUB客户端，每秒发送200条QOS0的消息，1500个客户端SUB消费，SUB吞吐量理论值=1500*200



## jmeter压测性能



## 企业版多了哪些功能

1 消息数据保存(数据持久化)

EMQ X 企业版支持存储订阅关系、MQTT 消息、设备状态到 Redis、MySQL、PostgreSQL、MongoDB、Cassandra、TimescaleDB、InfluxDB、DynamoDB、OpenTDSB 数据库:



2 消息桥接转发

EMQ X 企业版支持直接转发 MQTT 消息到 RabbitMQ、Kafka、Pulsar、RocketMQ、MQTT Broker



3 规则引擎

规则引擎可以灵活地处理消息和事件。

4编解码

解码后的数据可直接被规则引擎和其他插件使用。

5 主题监控