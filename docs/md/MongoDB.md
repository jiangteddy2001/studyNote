# MongoDB

## 1 简介

### 1.1 概念

MongoDB是一个文档数据库，提供好的性能，领先的[非关系型数据库]。

采用BSON存储文档数据。
BSON（）是一种类json的一种二进制形式的存储格式，简称Binary JSON.
相对于json多了date类型和二进制数组。

### 1.2 使用场景

- 大数据
- 内容管理系统
- 移动端Apps
- 数据管理



## 2 Windows安装

### 2.1 下载安装包

下载windows安装包，版本4.4

- 在 [官网](https://www.mongodb.com/download-center/community) 下载安装文件（`.msi` 文件）

### 2.2 配置环境变量

直接安装后配置环境变量到PATH

测试是否安装成功

访问本地http://127.0.0.1:27017/

如果显示一下内容，表示已安装并启动成功

```
It looks like you are trying to access MongoDB over HTTP on the native driver port.
```

安装完毕后的目录如下：

![image-20230115142412733](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115142412733.png)

程序文件名	组件功能
bsondump.exe	MiscellaneousTools 其他工具
InstallCompass.ps1	
mongo.exe	Client 客户端
mongod.cfg	配置文件
mongod.exe	Server 服务端
mongod.pdb	
mongodump.exe	ImportExportTools 导入导出工具
mongoexport.exe	ImportExportTools 导入导出工具
mongofiles.exe	MiscellaneousTools 其他工具
mongoimport.exe	ImportExportTools 导入导出工具
mongorestore.exe	ImportExportTools 导入导出工具
mongos.exe	Router 路由
mongos.pdb	
mongostat.exe	MonitoringTools 监视工具
mongotop.exe	MonitoringTools 监视工具

| 参数              | 说明                           |
| ----------------- | ------------------------------ |
| mongodb           | 固定格式                       |
| username:password | 指定账号和密码，可选           |
| host              | 指定主机地址，默认为 127.0.0.1 |
| port              | 指定端口，默认为 27017         |
| database          | 指定数据库名称，默认为 test    |
| options           | 连接选项                       |

> mongo mongodb://admin:123456@localhost/myDB



## 3 Linux安装

### 3.1 下载安装包

下载安装包https://www.mongodb.com/try/download/community

解压缩：tar -zxvf mongodb-linux-x86_64-rhel70-4.4.18.tgz

mv mongodb-linux-x86_64-rhel70-4.4.18 /usr/local/mongodb

### 3.2 创建数据和日志目录

创建日志存放目录

mkdir -p /usr/local/mongodb/data /usr/local/mongodb/logs

### 3.3 创建配置文件

创建mongodb的配置文件conf/mongodb.conf

![image-20230115155032099](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115155032099.png)

```
port=10001
dbpath=/usr/local/mongodb/data/
logpath=/usr/local/mongodb/logs/mongodb.log
logappend=true
fork=true
bind_ip=0.0.0.0

```

解释说明：

port=10001【代表端口号，如果不指定则默认为 27017 】

dbpath=data/ 【数据库路径】

logpath=logs/mongodb.log 【日志路径】

logappend=true 【日志文件自动累加，而不是覆盖】

fork=true 【后台执行方式启动】

bind_ip=0.0.0.0 【绑定IP，如果是0.0.0.0，则允许外部连接服务器】

创建完毕后目录

![image-20230115155225425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115155225425.png)

### 3.4 配置系统环境变量

vi /etc/profile.d/mongo.sh

添加内容

```
export PATH=$PATH:/usr/local/mongodb/bin
```

执行命令让配置生效

source /etc/profile

### 3.5 启动服务

执行启动命令：

mongod --config /usr/local/mongodb/conf/mongodb.conf

![image-20230115154838999](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115154838999.png)

注意：如果启动后无法连接，检查配置文件里

bind_ip=0.0.0.0

检测结果

![image-20230115163807150](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115163807150.png)

### 3.6 自启动服务配置

注册到开机启动的方法如下：

在系统服务目录下新建mongodb的启动服务

```
cd /lib/systemd/system
vi mongodb.service
```

内容如下

```
[Unit]
  
Description=mongodb
After=network.target remote-fs.target nss-lookup.target
  
[Service]
Type=forking
ExecStart=/usr/local/mongodb/bin/mongod --config /usr/local/mongodb/conf/mongodb.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/usr/local/mongodb/bin/mongod --shutdown --config /usr/local/mongodb/conf/mongodb.conf
PrivateTmp=true
  
[Install]
WantedBy=multi-user.target
```

路径要使用绝对路径

赋予权限

chmod 754 mongodb.service

服务生效

```
启动
systemctl start mongodb.service
关闭
systemctl stop mongodb.service
注册到开机启动
systemctl enable mongodb.service
```



## 4 Mongo语法

```
mongod --install --dbpath 数据目录 --logpath 日志目录\日志名称	#创建服务
mongod --remove	    #卸载服务		
net start mongodb	#启动服务
net stop mongodb	#关闭服务
mongod #是处理MongoDB系统的主要进程。它处理数据请求，管理数据存储，和执行后台管理操作。当我们运行mongod命令意味着正在启动MongoDB进程,并且在后台运行。

```

## 5 Compass安装

进入网站下载，然后安装

![image-20230115145604746](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115145604746.png)

![image-20230115145204579](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115145204579.png)

查看数据库信息

![image-20230115145713562](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115145713562.png)





## 6 用户权限

### 6.1 创建用户

连接mongobd

```
mongo 192.168.204.104:10001
```

查看数据库

```
show databases
```

![image-20230115170556567](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115170556567.png)

- 切换到admin（设置密码需要切换到admin库）

```
use admin
```

创建一个用户

```
db.createUser( {
      user: "jiang", pwd: "123456",roles:[{role:"root",db:"admin"}]})
```

- 查看所有用户信息

```
show users
```

![image-20230115171044397](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230115171044397.png)

退出

```
exit
```



### 6.2 角色

具体角色：
Read：允许用户读取指定数据库
readWrite：允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
root：只在admin数据库中可用。超级账号，超级权限

### 6.3 查询和删除、修改

```
use admin
// 查看用户
show users
// 删除用户jiang
db.dropUser("jiang")
// 修改test用户密码为123456
db.changeUserPassword("test","123456")

```

### 6.4 开启权限认证





## 7 常用命令



## 8 Springboot操作mongdb