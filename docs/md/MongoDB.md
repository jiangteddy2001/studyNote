# MongoDB

附件：vuepress搭建博客，使用了vuepress-vdoing主题

参考网址：https://frxcat.fun/pages/91197c/

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

![image-20230313205727940](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313205727940.png)

### 7.1 数据库操作

#### 7.1.1选择和创建数据库

- 选择和创建数据库的语法格式：

```
use 数据库名称
```

- 如果数据库不存在则自动创建，例如，以下语句创建 articledb数据库：

```
# 登录
mongo

# 创建数据库
use articledb
```

![image-20230313211234477](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313211234477.png)

刚创建数据库查不到，因为创建在内存里。只有当数据库里有数据了，才会显示。

- 查看有权限查看的所有的数据库命令

```
show dbs
或
show databases
```

- **注意: 在 MongoDB 中，集合只有在内容插入后才会创建! 就是说，创建集合(数据表)后要再插入一个文档(记录)，集合才会真正创建。**
- 查看当前正在使用的数据库命令

```
db
```

![image-20230313211407871](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313211407871.png)

- MongoDB 中默认的数据库为 test，如果你没有选择数据库，集合将存放在 test 数据库中。
- 另外：
  数据库名可以是满足以下条件的任意UTF-8字符串。
  不能是空字符串（“”)。
  不得含有’ '（空格)、.、$、/、\和\0 (空字符)。
  应全部小写。
  最多64字节。
- 有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。
  - admin： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器.
  - local: 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
  - config: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

#### 7.1.2 删除数据库

- MongoDB 删除数据库的语法格式如下：

```
db.dropDatabase()
```

- 提示：主要用来删除已经持久化的数据库

### 7.2 集合操作

- 集合，类似关系型数据库中的表。
- 可以显示的创建，也可以隐式的创建。

#### 7.2.1 集合的显式创建（了解）

- 基本语法格式：

```
db.createCollection(name)

db.createCollection("jiang")

# 查询
show collections
```

- 参数说明：

  - name: 要创建的集合名称

- 集合的命名规范：

  - 集合名不能是空字符串""。
  - 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
  - 集合名不能以"system."开头，这是为系统集合保留的前缀。
  - 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。

  ![image-20230313212954659](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313212954659.png)

#### 7.2.2集合的隐式创建(常用）

- 当向一个集合中插入一个文档的时候，如果集合不存在，则会自动创建集合。

#### 7.2.3 集合的删除

- 集合删除语法格式如下：

```
db.collection.drop()
或
db.集合.drop()
```

- 返回值
  - 如果成功删除选定集合，则 drop() 方法返回 true，否则返回 false。

### 7.3  文档基本CRUD

- 文档（document）的数据结构和 JSON 基本一样。
- 所有存储在集合中的数据都是 BSON 格式。

#### 7.3.1 文档的插入

1. 单个文档插入
   使用`insert()` 或 `save()` 方法向集合中插入文档，语法如下：

```
db.collection.insert(
<document or array of documents>,
{
writeConcern: <document>,
ordered: <boolean>
}
)
```

参数：

![image-20230313212346268](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313212346268.png)

- 提示：

mongo中的数字，默认情况下是double类型，如果要存整型，必须使用函数NumberInt(整型数字)，否则取出来就有问题了。
插入当前日期使用 new Date()
插入的数据没有指定 _id ，会自动生成主键值
如果某字段没值，可以赋值为null，或不写该字段。

- 注意：

1 文档中的键/值对是有序的。
2 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3 MongoDB区分类型和大小写。
4 MongoDB的文档不能有重复的键。
5 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：

- 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。
  

2 、批量插入

语法：

```
db.collection.insertMany(
[ <document 1> , <document 2>, ... ],
{
writeConcern: <document>,
ordered: <boolean>
}
)
```

![image-20230313212553692](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313212553692.png)

- 提示：
  - 插入时指定了 _id ，则主键就是该值。
  - 如果某条数据插入失败，将会终止插入，但已经插入成功的数据不会回滚掉。
  - 因为批量插入由于数据较多容易出现失败，因此，可以使用try catch进行异常捕捉处理，测试的时候可以不处理。如（了解）：

```
try {
db.comment.insertMany([
# 插入的数据集合
]);
} catch (e) {
print (e);
}
```

#### 7.3.2 文档的基本查询

语法：

```
db.collection.find(<query>, [projection])
```

参数：

![image-20230313212725400](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313212725400.png)

- 查询所有
- 如果我们要查询spit集合的所有文档，我们输入以下命令

```
db.comment.find()
或
db.comment.find({})
```



## 8 Springboot操作mongdb