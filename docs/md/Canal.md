# Canal

## 1、概念

译意为水道/管道/沟渠，主要用途是基于 **MySQL 数据库增量日志解析**，提供**增量数据订阅和消费**。

canal的工作原理就是**把自己伪装成MySQL slave，模拟MySQL slave的交互协议向MySQL Mater发送 dump协议，MySQL mater收到canal发送过来的dump请求，开始推送binary log给canal，然后canal解析binary log，再发送到存储目的地**，比如MySQL，Kafka，Elastic Search等等。

当前的 canal 支持源端 MySQL 版本包括 5.1.x , 5.5.x , 5.6.x , 5.7.x , 8.0.x

## 2、使用用途

- 数据库镜像
- 数据库实时备份
- 索引构建和实时维护
- 业务cache(缓存)刷新
- 带业务逻辑的增量数据处理



## 3、安装

解压缩 *tar* -zxvf canal.deployer-1.1.5.tar.gz



## 4、开启数据同步MQ

### 4.1 MySql开启biglog 

vim /etc/my.cnf

添加配置

[mysqld]
log-bin=mysql-bin # 开启binlog
binlog-format=ROW # 选择ROW模式
server_id=1 # 配置MySQL replaction需要定义，不和Canal的slaveId重复即可

重启MySQL ，查看配置是否生效 

show variables like 'log_bin';

### 4.2 RabbitMQ 队列创建



### 4.3 修改canal配置

canal.properties

**instance.properties**

rabbitmq.host = 127.0.0.1
rabbitmq.virtual.host = /

#rabbitmq中新建的 Exchange

rabbitmq.exchange = canal.topic
rabbitmq.username = guest
rabbitmq.password = guest