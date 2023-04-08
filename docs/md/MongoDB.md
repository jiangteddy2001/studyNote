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

修改配置文件/usr/local/mongodb/conf/mongodb.conf

```
security:
  #开启授权认证
  authorization: enabled

```

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

![image-20230325133319837](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325133319837.png)

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

举例子

```
db.comment.insert({"articleid":"1000001","content":"今天日子天晴","userid":"1001","nickname":"jiangbx"})
```

![image-20230325134717951](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325134717951.png)

查询验证

```
db.comment.find()
```



![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325134809105.png)

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

```
db.comment.insertMany([
{"_id":"1","articleid":"1000002","content":"今天日子多云","userid":"1002","likenum":3000,"nickname":"jim"},
{"_id":"2","articleid":"1000006","content":"今天日子下雨","userid":"1003","likenum":3000,"nickname":"tom"},
{"_id":"3","articleid":"1000007","content":"今天日子下雨","userid":"1003","likenum":3000,"nickname":"tom2"},
{"_id":"4","articleid":"1000022","content":"今天日子下雨","userid":"1003","likenum":3000,"nickname":"tom3"}
])
```

![image-20230325151444652](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325151444652.png)

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

![image-20230325134809105](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325134809105.png)

参数：

![image-20230313212725400](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230313212725400.png)

- 查询所有
- 如果我们要查询spit集合的所有文档，我们输入以下命令

```
db.comment.find()
或
db.comment.find({})
```

- 查询指定id

```
db.comment.find({userid:"1002"})
```

![image-20230325142511532](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325142511532.png)

- 指定id但只要一个文档

```
db.comment.findOne({userid:'1003'})
```

投影查询（Projection Query）：

- 如果要查询结果返回部分字段，则需要使用投影查询（不显示所有字段，只显示指定的字段）。
  - 如：查询结果只显示 _id、userid、nickname :>db.comment.find({userid:"1003"},{userid:1,nickname:1}),其中默认 _id 会显示_
  - _如：查询结果只显示 、userid、nickname ，不显示 _id ：>db.comment.find({userid:"1003"},{userid:1,nickname:1,_id:0})
  - 再例如：查询所有数据，但只显示 _id、userid、nickname :>db.comment.find({},{userid:1,nickname:1})

```
db.comment.find({userid:"1003"},{userid:1,nickname:1})

db.comment.find({userid:"1003"},{userid:1,nickname:1,_id:0})

db.comment.find({},{userid:1,nickname:1})
```

![image-20230325142933938](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325142933938.png)

插入数据的try...catch.....

如果某条数据插入失败, 将会终止插入, 但已经插入成功的数据**不会回滚掉**. 因为批量插入由于数据较多容易出现失败, 因此, 可以使用 `try catch` 进行异常捕捉处理, 测试的时候可以不处理

```
try {
  db.comment.insertMany([
    {"_id":"1","articleid":"100001","content":"我们不应该把清晨浪费在手机上, 健康很重要, 一杯温水幸福你我 他.","userid":"1002","nickname":"相忘于江湖","createdatetime":new Date("2019-0805T22:08:15.522Z"),"likenum":NumberInt(1000),"state":"1"},
    {"_id":"2","articleid":"100001","content":"我夏天空腹喝凉开水, 冬天喝温开水","userid":"1005","nickname":"伊人憔 悴","createdatetime":new Date("2019-08-05T23:58:51.485Z"),"likenum":NumberInt(888),"state":"1"},
    {"_id":"3","articleid":"100001","content":"我一直喝凉开水, 冬天夏天都喝.","userid":"1004","nickname":"杰克船长","createdatetime":new Date("2019-08-06T01:05:06.321Z"),"likenum":NumberInt(666),"state":"1"},
    {"_id":"4","articleid":"100001","content":"专家说不能空腹吃饭, 影响健康.","userid":"1003","nickname":"凯 撒","createdatetime":new Date("2019-08-06T08:18:35.288Z"),"likenum":NumberInt(2000),"state":"1"},
    {"_id":"5","articleid":"100001","content":"研究表明, 刚烧开的水千万不能喝, 因为烫 嘴.","userid":"1003","nickname":"凯撒","createdatetime":new Date("2019-0806T11:01:02.521Z"),"likenum":NumberInt(3000),"state":"1"}

]);

} catch (e) {
  print (e);
}
```

![image-20230325143428734](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325143428734.png)

#### 7.3.3 文档的更新

- 更新文档的语法：

```
db.collection.update(query, update, options)
//或
db.collection.update(
	<query>,
	<update>,
	{
	upsert: <boolean>,
	multi: <boolean>,
	writeConcern: <document>,
	collation: <document>,
	arrayFilters: [ <filterdocument1>, ... ],
	hint: <document|string> // Available starting in MongoDB 4.2
	}
)

```

- 覆盖修改

```
db.comment.update({_id:"1"},{userid:NumberInt(1008)})
```

![image-20230325144547659](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325144547659.png)

我们发现，执行完之后其他字段数据都消失了。

- 局部修改

```
db.comment.update({_id:"2"},{$set:{userid:NumberInt(889)}})
```

![image-20230325144927176](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230325144927176.png)

- 批量的修改

```
//默认只修改第一条数据
db.comment.update({userid:"1003"},{$set:{nickname:"凯撒2"}})
//修改所有符合条件的数据
db.comment.update({userid:"1003"},{$set:{nickname:"凯撒大帝"}},{multi:true})
```

![image-20230325145644550](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325145644550.png)

- 列值增长的修改
  - 如果我们想实现对某列值在原有值的基础上进行增加或减少，可以使用 $inc 运算符来实现。
    需求：对3号数据的点赞数，每次递增1

```
db.comment.update({_id:"3"},{$inc:{likenum:NumberInt(1)}})
```

![image-20230325151625706](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325151625706.png)

#### 7.3.4 文档的删除

- 删除文档的语法结构：

```
db.集合名称.remove(条件)
```

- 以下语句可以将数据全部删除，请慎用

```
db.comment.remove({})
```

- 如果删除_id=1的记录，输入以下语句

```
db.comment.remove({_id:"1"})
```

![image-20230325152559722](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325152559722.png)

#### 7.3.5 文档的分页查询

##### 7.3.5.1 统计查询

- 统计查询使用count()方法，语法如下：

```
db.collection.count(query, options)
```

![image-20230325152711823](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325152711823.png)

- 统计所有记录数：

```
db.comment.count()
```

- 按条件统计记录数：

```
db.comment.count({userid:"1003"})
```

- 提示：默认情况下 count() 方法返回符合条件的全部记录条数。

![image-20230325153559349](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325153559349.png)

##### 7.3.5.2 分页列表查询

- 可以使用limit()方法来读取指定数量的数据，使用skip()方法来跳过指定数量的数据。
- 基本语法如下所示：

```
>db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```

举例

```
db.comment.find().limit(2)
```

![image-20230325153803203](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325153803203.png)

跳过前面2条数据查询

```
db.comment.find().limit(2).skip(2)
```

![image-20230325154042839](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325154042839.png)

##### 7.3.5.3 排序查询

- sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。
- 语法如下所示：

```
db.COLLECTION_NAME.find().sort({KEY:1})
或
db.集合名称.find().sort(排序方式)
```

- 提示：skip(), limilt(), sort()三个放在一起执行的时候，执行的顺序是先 sort(), 然后是 skip()，最后是显示的 limit()，和命令编写顺序无关。

升序查询

```
db.comment.find().sort(likenum:1)
```

![image-20230325154829151](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325154829151.png)

```
db.comment.find().sort(likenum:-1)
```

![image-20230325155251217](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325155251217.png)

#### 7.3.6 文档的更多查询

##### 7.3.6.1 正则的复杂条件查询

- MongoDB的模糊查询是通过正则表达式的方式实现的。格式为：

```
db.collection.find({field:/正则表达式/})
或
db.集合.find({字段:/正则表达式/})
```

- 提示：正则表达式是js的语法，直接量的写法。
  例如，我要查询评论内容包含“开水”的所有文档，代码如下：

```
db.collections.find({content:/开水/})
```

举例

```
db.comment.find({content:/开水/})
```

![image-20230325160143175](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325160143175.png)



##### 7.3.6.2 比较查询

- `<, <=, >, >= `这个操作符也是很常用的，格式如下:

```
db.集合名称.find({ "field" : { $gt: value }}) // 大于: field > value
db.集合名称.find({ "field" : { $lt: value }}) // 小于: field < value
db.集合名称.find({ "field" : { $gte: value }}) // 大于等于: field >= value
db.集合名称.find({ "field" : { $lte: value }}) // 小于等于: field <= value
db.集合名称.find({ "field" : { $ne: value }}) // 不等于: field != value
```

举例

```
db.comment.find({"likenum":{$gt:1000}})
```

![image-20230325161148622](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325161148622.png)



##### 7.3.6.3 包含查询

- 包含使用`$in`操作符。 示例：查询评论的集合中userid字段包含1003或1004的文档

```
db.comment.find({userid:{$in:["1003","1004"]}})
```

- 不包含使用`$nin`操作符。 示例：查询评论集合中userid字段不包含1003和1004的文档

```
db.comment.find({userid:{$nin:["1003","1004"]}})
```

![image-20230325161312777](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325161312777.png)

##### 7.3.6.4 条件连接查询

我们如果需要查询同时满足两个以上条件，需要使用`$and`操作符将条件进行关联。（相 当于SQL的and） 格式为：

```
$and:[ { },{ },{ } ]
```

查询集合中点击量大于2000，并且小于3000

```
db.comment.find({$and:[ {"likenum":{$gt:NumberInt(2000)} },{"likenum":{$lt:NumberInt(3000)}}]})
```

![image-20230325162717776](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325162717776.png)

查询集合中userid=1002,或点击量大于3000

```
db.comment.find({$or:[ {"userid": "1003"},{"likenum":{$lt:1000}}]})
```

![image-20230325162921582](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325162921582.png)

### 7.4 常用命令小结

```
选择切换数据库：use articledb
插入数据：db.comment.insert({bson数据})
查询所有数据：db.comment.find();
条件查询数据：db.comment.find({条件})
查询符合条件的第一条记录：db.comment.findOne({条件})
查询符合条件的前几条记录：db.comment.find({条件}).limit(条数)
查询符合条件的跳过的记录：db.comment.find({条件}).skip(条数)
修改数据：db.comment.update({条件},{修改后的数据}) 或db.comment.update({条件},{$set:{要修改部分的字段:数据})
修改数据并自增某字段值：db.comment.update({条件},{$inc:{自增的字段:步进值}})
删除数据：db.comment.remove({条件})
统计查询：db.comment.count({条件})
模糊查询：db.comment.find({字段名:/正则表达式/})
条件比较运算：db.comment.find({字段名:{$gt:值}})
包含查询：db.comment.find({字段名:{$in:[值1，值2]}})或db.comment.find({字段名:{$nin:[值1，值2]}})
条件连接查询：db.comment.find({$and:[{条件1},{条件2}]})或db.comment.find({$or:[{条件1},{条件2}]})

```

### 7.5 索引操作

#### 7.5.1 概述

- 索引支持在MongoDB中高效地执行查询。如果没有索引，MongoDB必须执行全集合扫描，即扫描集合中的每个文档，以选择与查询语句匹配的文档。这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的。
- 如果查询存在适当的索引，MongoDB可以使用该索引限制必须检查的文档数。
- 索引是特殊的数据结构，它以易于遍历的形式存储集合数据集的一小部分。索引存储特定字段或一组字段的值，按字段值排序。索引项的排序支持有效的相等匹配和基于范围的查询操作。此外，MongoDB还可以使用索引中的排序返回排序结果。
- 官网文档：https://docs.mongodb.com/manual/indexes/
  了解：
  - MongoDB索引使用B树数据结构（确切的说是B-Tree，MySQL是B+Tree）
    

#### 7.5.2 索引的类型

##### 7.5.2.1 单字段索引

MongoDB支持在文档的单个字段上创建用户定义的升序/降序索引，称为单字段索引（Single Field Index）。
对于单个字段索引和排序操作，索引键的排序顺序（即升序或降序）并不重要，因为MongoDB可以在任何方向上遍历索引。

![image-20230325164242107](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325164242107.png)



##### 7.5.2.2 复合索引

- MongoDB还支持多个字段的用户定义索引，即复合索引（Compound Index）。
- 复合索引中列出的字段顺序具有重要意义。例如，如果复合索引由 { userid: 1, score: -1 } 组成，则索引首先按userid正序排序，然后在每个userid的值内，再在按score倒序排序。
  

![image-20230325164348404](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325164348404.png)

##### 7.5.2.3 其他索引

- 地理空间索引（Geospatial Index）
  为了支持对地理空间坐标数据的有效查询，MongoDB提供了两种特殊的索引：返回结果时使用平面几何的二维索引和返回结果时使用球面几何的二维球面索引。
- 文本索引（Text Indexes）
  MongoDB提供了一种文本索引类型，支持在集合中搜索字符串内容。这些文本索引不存储特定于语言的停止词（例如“the”、“a”、“or”），而将集合中的词作为词干，只存储根词。
- 哈希索引（Hashed Indexes）
  为了支持基于散列的分片，MongoDB提供了散列索引类型，它对字段值的散列进行索引。这些索引在其范围内的值分布更加随机，但只支持相等匹配，不支持基于范围的查询。


#### 7.5.3 索引的操作

##### 7.5.3.1 索引的查看

```
db.collection.getIndexes()
```

![image-20230325170615220](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325170615220.png)

- 提示：该语法命令运行要求是MongoDB 3.0+
- 默认_id索引：MongoDB在创建集合的过程中，在 _id 字段上创建一个唯一的索引，默认名字为 id ，该索引可防止客户端插入两个具有相同值的文=档，您不能在_id字段上删除此索引。
- 注意：该索引是唯一索引，因此值不能重复，即 _id 值不能重复的。在分片集群中，通常使用 _id 作为片键。

#### 7.5.3.2  创建索引

```
db.collection.createIndex(keys, options)
```

参数：

![image-20230325170952841](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230325170952841.png)

options（更多选项）列表：

![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/529142acdea940e5b954e02e0f096a2f.png)

提示：

- 注意在 3.0.0 版本前创建索引方法为 `db.collection.ensureIndex()` ，之后的版本使用了 `db.collection.createIndex()` 方法，
  ensureIndex() 还能用，但只是 createIndex() 的别名。

- 单字段索引

```
db.comment.createIndex({userid:1})
```

![image-20230326140408779](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326140408779.png)

![image-20230326140650079](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326140650079.png)

- 复合索引：对 userid 和 nickname 同时建立复合（Compound）索引：

```
 db.comment.createIndex({userid:1,nickname:-1})
```

- 查看索引：

```
 db.comment.getIndexes()
```

![image-20230326141058466](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326141058466.png)

![image-20230326141207523](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326141207523.png)

##### 7.5.3.3 删除索引

1. 指定索引的移除

```
db.comment.dropIndex(index)
```

参数：

![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/adcff064d2154ca9be5f25cd3c814eef.png)

```
db.comment.dropIndex({userid:1})
```

![image-20230326141837822](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326141837822.png)

2. 所有索引的移除

```
db.comment.dropIndexes()
```

![image-20230326142553417](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326142553417.png)

#### 7.5.4 引用索引

##### 7.5.4.1 执行计划

分析查询性能 (Analyze Query Performance) 通常使用执行计划 (解释计划 - Explain Plan) 来查看查询的情况

语法：

```
db.collection.find(query,options).explain(options)
```

例如：查看根据userid查询数据的情况

```
db.comment.find({userid:"1003"}).explain()
```

![image-20230326142655968](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326142655968.png)

- 在具体信息中关键点看： `"stage" : "COLLSCAN"`, 表示全集合扫描,或者`"stage" : "IXSCAN" `,基于索引的扫描

compass查看：

![image-20230326142730029](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326142730029.png)

现在添加一个索引

```
 db.comment.createIndex({userid:1})
```

![image-20230326142901337](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326142901337.png)

```
db.comment.find({userid:"1002"}).explain()
```

![image-20230326143017327](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326143017327.png)

![image-20230326143117496](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326143117496.png)

##### 7.5.4.2 涵盖的查询

- Covered Queries
- 当查询条件和查询的投影仅包含索引字段时，MongoDB直接从索引返回结果，而不扫描任何文档或将文档带入内存。 这些覆盖的查询可以非常有效。

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/0555e761e3ae4ee9a20bd6cdad9c99ee.png)

- 更多：https://docs.mongodb.com/manual/core/query-optimization/#read-operations-covered-query

Compass中查看:

点击more

![image-20230326143451404](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326143451404.png)

![image-20230326143554247](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326143554247.png)

发现查询变为0

![image-20230326143631565](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326143631565.png)



## 8 Springboot操作mongdb

### 8.1 微服务项目搭建

![image-20230326152848981](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326152848981.png)

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.jiang</groupId>
    <artifactId>article</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>
    </dependencies>

</project>
```

创建application.yml

```yaml
spring:
  #数据源配置
  data:
    mongodb:
      # 主机地址
      host: 192.168.204.104
      # 数据库
      database: articledb
      # 默认端口是27017
      port: 10001
      #也可以使用uri连接
      #uri: mongodb://192.168.204.104:10001/articledb

```

启动类

```java 
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ArticleApplication {

    public static void main(String[] args) {
        SpringApplication.run(ArticleApplication.class, args);
    }

}
```

![image-20230326164233069](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326164233069.png)

### 8.2 实体类的编写

```java
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.index.Indexed;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

import java.time.LocalDateTime;
import java.util.Date;

/**
 * 文章评论实体类
 */
//把一个java类声明为mongodb的文档，可以通过collection参数指定这个类对应的文档。
//@Document(collection="mongodb 对应 collection 名")
// 若未加 @Document ，该 bean save 到 mongo 的 comment collection
// 若添加 @Document ，则 save 到 comment collection
@Document(collection = "comment")//可以省略，如果省略，则默认使用类名小写映射集合
//复合索引
// @CompoundIndex( def = "{'userid': 1, 'nickname': -1}")
public class Comment  implements Serializable{

    //主键标识，该属性的值会自动对应mongodb的主键字段"_id"，如果该属性名就叫“id”,则该注解可以省略，否则必须写
    @Id
    private String id;//主键
    //该属性对应mongodb的字段的名字，如果一致，则无需该注解
    @Field("content")
    private String content;//吐槽内容
    private Date publishtime;//发布日期
    //添加了一个单字段的索引
    @Indexed
    private String userid;//发布人ID
    private String nickname;//昵称
    private LocalDateTime createdatetime;//评论的日期时间
    private Integer likenum;//点赞数
    private Integer replynum;//回复数
    private String state;//状态
    private String parentid;//上级ID
    private String articleid;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public Date getPublishtime() {
        return publishtime;
    }

    public void setPublishtime(Date publishtime) {
        this.publishtime = publishtime;
    }

    public String getUserid() {
        return userid;
    }

    public void setUserid(String userid) {
        this.userid = userid;
    }

    public String getNickname() {
        return nickname;
    }

    public void setNickname(String nickname) {
        this.nickname = nickname;
    }

    public LocalDateTime getCreatedatetime() {
        return createdatetime;
    }

    public void setCreatedatetime(LocalDateTime createdatetime) {
        this.createdatetime = createdatetime;
    }

    public Integer getLikenum() {
        return likenum;
    }

    public void setLikenum(Integer likenum) {
        this.likenum = likenum;
    }

    public Integer getReplynum() {
        return replynum;
    }

    public void setReplynum(Integer replynum) {
        this.replynum = replynum;
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }

    public String getParentid() {
        return parentid;
    }

    public void setParentid(String parentid) {
        this.parentid = parentid;
    }

    public String getArticleid() {
        return articleid;
    }

    public void setArticleid(String articleid) {
        this.articleid = articleid;
    }

    @Override
    public String toString() {
        return "Comment{" +
                "id='" + id + '\'' +
                ", content='" + content + '\'' +
                ", publishtime=" + publishtime +
                ", userid='" + userid + '\'' +
                ", nickname='" + nickname + '\'' +
                ", createdatetime=" + createdatetime +
                ", likenum=" + likenum +
                ", replynum=" + replynum +
                ", state='" + state + '\'' +
                ", parentid='" + parentid + '\'' +
                ", articleid='" + articleid + '\'' +
                '}';
    }

}
```

- 说明：索引可以大大提升查询效率，一般在查询字段上添加索引，索引的添加可以通过Mongo的命令来添加，也可以在Java的实体类中通过注解添加。

1. 单字段索引注解@Indexed
   org.springframework.data.mongodb.core.index.Indexed.class
   声明该字段需要索引，建索引可以大大的提高查询效率。

2. 复合索引注解@CompoundIndex
   org.springframework.data.mongodb.core.index.CompoundIndex.class
   复合索引的声明，建复合索引可以有效地提高多字段的查询效率。

### 8.3 基本增删改查

dao

```
package com.jiang.dao;

import com.jiang.po.Comment;
import org.springframework.data.mongodb.repository.MongoRepository;

//评论的持久层接口
public interface CommentRepository extends MongoRepository<Comment,String> {

}
```

service

```java
package com.jiang.service;

import com.jiang.dao.CommentRepository;
import com.jiang.po.Comment;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

//评论的业务层
@Service
public class CommentService {

    //注入dao
    @Autowired
    private CommentRepository commentRepository;

    /**
     * 保存一个评论
     *
     * @param comment
     */
    public void saveComment(Comment comment) {
     //如果需要自定义主键，可以在这里指定主键；如果不指定主键，MongoDB会自动生成主键
     //设置一些默认初始值。。。
     //调用dao
        commentRepository.save(comment);
    }

    /**
     * 更新评论
     *
     * @param comment
     */
    public void updateComment(Comment comment) {
        //调用dao
        commentRepository.save(comment);
    }

    /**
     * 根据id删除评论
     *
     * @param id
     */
    public void deleteCommentById(String id) {
         //调用dao
        commentRepository.deleteById(id);
    }

    /**
     * 查询所有评论
     *
     * @return
     */
    public List<Comment> findCommentList() {
        //调用dao
        return commentRepository.findAll();
    }

    /**
     * 根据id查询评论
     *
     * @param id
     * @return
     */
    public Comment findCommentById(String id) {
        //调用dao
        return commentRepository.findById(id).get();
    }

}

```

代码结构

![image-20230326173919412](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326173919412.png)

### 8.4 测试类

```java
package com.jiang.service;

import com.jiang.ArticleApplication;
import com.jiang.po.Comment;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.time.LocalDateTime;
import java.util.List;
//SpringBoot的Junit集成测试
@RunWith(SpringRunner.class)
//SpringBoot的测试环境初始化，参数：启动类
@SpringBootTest(classes = ArticleApplication.class)
public class CommentServiceTest {
    //注入Service
    @Autowired
    private CommentService commentService;
    /**
     * 保存一个评论
     */
    @Test
    public void testSaveComment(){
        Comment comment=new Comment();
        comment.setArticleid("100000");
        comment.setContent("测试添加的数据");
        comment.setCreatedatetime(LocalDateTime.now());
        comment.setUserid("1003");
        comment.setNickname("凯撒大帝");
        comment.setState("1");
        comment.setLikenum(0);
        comment.setReplynum(0);
        commentService.saveComment(comment);
    }
    /**
     * 查询所有数据
     */
    @Test
    public void testFindAll(){
        List<Comment> list = commentService.findCommentList();
        System.out.println(list);
    }
    /**
     * 测试根据id查询
     */
    @Test
    public void testFindCommentById(){
        Comment comment = commentService.findCommentById("6420104de662281340d11899");
        System.out.println(comment);
    }

}

```

![image-20230326173103693](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326173103693.png)

### 8.5 分页查询

CommentRepository新增方法定义

```
//根据父id，查询子评论的分页列表
Page<Comment> findByParentid(String parentid, Pageable pageable);
```

CommentService新增方法

```
/**
* 根据父id查询分页列表
* @param parentid
* @param page
* @param size
* @return
*/
public Page<Comment> findCommentListPageByParentid(String parentid,int page ,int size){
return commentRepository.findByParentid(parentid, PageRequest.of(page-1,size));
}
```

测试用例

```
/**
* 测试根据父id查询子评论的分页列表
*/
@Test
public void testFindCommentListPageByParentid(){
Page<Comment> pageResponse = commentService.findCommentListPageByParentid("3", 1, 2);
System.out.println("----总记录数："+pageResponse.getTotalElements());
System.out.println("----当前页数据："+pageResponse.getContent());
}
```

![image-20230326173847317](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230326173847317.png)

### 8.6 更新操作

CommentService 新增updateThumbup方法

```java
/**
* 点赞-效率低
* @param id
*/
public void updateCommentThumbupToIncrementingOld(String id){
Comment comment = CommentRepository.findById(id).get();
comment.setLikenum(comment.getLikenum()+1);
CommentRepository.save(comment);
}
```

- 以上方法虽然实现起来比较简单，但是执行效率并不高，因为我只需要将点赞数加1就可以了，没必要查询出所有字段修改后再更新所有字段。(蝴蝶效应)我们可以使用`MongoTemplate`类来实现对某列的操作

修改CommentService

```java
//注入MongoTemplate
@Autowired
private MongoTemplate mongoTemplate;
/**
* 点赞数+1
* @param id
*/
public void updateCommentLikenum(String id){
//查询对象
Query query=Query.query(Criteria.where("_id").is(id));
//更新对象
Update update=new Update();
//局部更新，相当于$set
// update.set(key,value)
//递增$inc
// update.inc("likenum",1);
update.inc("likenum");
//参数1：查询对象
//参数2：更新对象
//参数3：集合的名字或实体类的类型Comment.class
mongoTemplate.updateFirst(query,update,"comment");
}

```

测试用例

```java
/**
* 点赞数+1
*/
@Test
public void testUpdateCommentLikenum(){
//对3号文档的点赞数+1
commentService.updateCommentLikenum("3");
}
```

**官方文档:https://www.mongodb.com/docs/manual/introduction/**

## 9 副本集-Replica Sets

### 9.1 简介

- MongoDB中的副本集（Replica Set）是一组维护相同数据集的mongod服务。 副本集可提供冗余和高
  可用性，是所有生产部署的基础。
- 也可以说，副本集类似于有自动故障恢复功能的主从集群。通俗的讲就是用多台机器进行同一数据的异
  步同步，从而使多台机器拥有同一数据的多个副本，并且当主库当掉时在不需要用户干预的情况下自动
  切换其他备份服务器做主库。而且还可以利用副本服务器做只读服务器，实现读写分离，提高负载


1 冗余和数据可用性

- 复制提供冗余并提高数据可用性。 通过在不同数据库服务器上提供多个数据副本，复制可提供一定级别的容错功能，以防止丢失单个数据库服务器。
- 在某些情况下，复制可以提供增加的读取性能，因为客户端可以将读取操作发送到不同的服务上， 在不同数据中心维护数据副本可以增加分布式应用程序的数据位置和可用性。 您还可以为专用目的维护其他副本，例如灾难恢复，报告或备份。

2 MongoDB中的复制

- 副本集是一组维护相同数据集的mongod实例。 副本集包含多个数据承载节点和可选的一个仲裁节点。在承载数据的节点中，一个且仅一个成员被视为主节点，而其他节点被视为次要（从）节点。

- 主节点接收所有写操作。 副本集只能有一个主要能够确认具有{w：“most”}写入关注的写入; 虽然在某些情况下，另一个mongod实例可能暂时认为自己也是主要的。主要记录其操作日志中的数据集的所有更改，即oplog。
  

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/add39f7a15ee44b8ab145b8646582b34.png)

- 辅助(副本)节点复制主节点的oplog并将操作应用于其数据集，以使辅助节点的数据集反映主节点的数据
  集。 如果主要人员不在，则符合条件的中学将举行选举以选出新的主要人员

3. 主从复制和副本集区别
   - 主从集群和副本集最大的区别就是副本集没有固定的“主节点”；整个集群会选出一个“主节点”，当其挂掉后，又在剩下的从节点中选中其他节点为“主节点”，副本集总有一个活跃点(主、primary)和一个或多个备份节点(从、secondary)。

### 9.2 副本集的三个角色

- 副本集有两种类型三种角色
- 两种类型：
  - 主节点（Primary）类型：数据操作的主要连接点，可读写。
  - 次要（辅助、从）节点（Secondaries）类型：数据冗余备份节点，可以读或选举。

- 三种角色：
  - 主要成员（Primary）：主要接收所有写操作。就是主节点。
  - 副本成员（Replicate）：从主节点通过复制操作以维护相同的数据集，即备份数据，不可写操作，但可
    以读操作（但需要配置）。是默认的一种从节点类型。
  - 仲裁者（Arbiter）：不保留任何数据的副本，只具有投票选举作用。当然也可以将仲裁服务器维护为副本集的一部分，即副本成员同时也可以是仲裁者。也是一种从节点类型。
    

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/0f2b0daad4cb40b3945146821cb08574.png)

关于仲裁者的额外说明：

- 您可以将额外的mongod实例添加到副本集作为仲裁者。 仲裁者不维护数据集。 仲裁者的目的是通过响应其他副本集成员的心跳和选举请求来维护副本集中的仲裁。 因为它们不存储数据集，所以仲裁器可以是提供副本集仲裁功能的好方法，其资源成本比具有数据集的全功能副本集成员更便宜。
- 如果您的副本集具有偶数个成员，请添加仲裁者以获得主要选举中的“大多数”投票。 仲裁者不需要专用
  硬件。
- 仲裁者将永远是仲裁者，而主要人员可能会退出并成为次要人员，而次要人员可能成为选举期间的主要
  人员。
- 如果你的副本+主节点的个数是偶数，建议加一个仲裁者，形成奇数，容易满足大多数的投票。
- 如果你的副本+主节点的个数是奇数，可以不加仲裁者。
  

### 9.3  副本集架构目标

- 一主一副本一仲裁

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/8075339c49974ce98f6f920a15ea34bf.png)

### 9.4 副本集的创建

先把mongodb服务停止

```
ps -ef|grep mongo

kill -2 959
```

![image-20230401154921443](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401154921443.png)

#### 9.4.1 创建主节点

```
#-----------myrs
#主节点
mkdir -p /mongodb/replica_sets/myrs_27017/log \ &
mkdir -p /mongodb/replica_sets/myrs_27017/data/db
```

新建或修改配置文件

```
vi /mongodb/replica_sets/myrs_27017/mongod.conf
```

myrs_27017：

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/replica_sets/myrs_27017/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/replica_sets/myrs_27017/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/replica_sets/myrs_27017/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27017
replication:
  #副本集的名称  
  replSetName: myrs

```

启动

```
[root@bobohost replica_sets]# /usr/local/mongodb/bin/mongod -f
/mongodb/replica_sets/myrs_27017/mongod.conf

```

#### 9.4.2 创建副本节点

```
#副本节点
mkdir -p /mongodb/replica_sets/myrs_27018/log \ &
mkdir -p /mongodb/replica_sets/myrs_27018/data/db
```

新建配置文件

```
vi /mongodb/replica_sets/myrs_27018/mongod.conf
```

配置文件

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/replica_sets/myrs_27018/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/replica_sets/myrs_27018/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/replica_sets/myrs_27018/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27018
replication:
  #副本集的名称  
  replSetName: myrs

```

启动节点服务

```
[root@bobohost replica_sets]# /usr/local/mongodb/bin/mongod -f
/mongodb/replica_sets/myrs_27018/mongod.conf

```



#### 9.4.3 创建仲裁节点

```
#-----------myrs
#仲裁节点
mkdir -p /mongodb/replica_sets/myrs_27019/log \ &
mkdir -p /mongodb/replica_sets/myrs_27019/data/db
```

```
vi /mongodb/replica_sets/myrs_27019/mongod.conf
```

新建或修改配置文件：

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/replica_sets/myrs_27019/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/replica_sets/myrs_27019/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/replica_sets/myrs_27019/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27019
replication:
  #副本集的名称  
  replSetName: myrs
```

启动节点服务：

```
[root@bobohost replica_sets]# /usr/local/mongodb/bin/mongod -f
/mongodb/replica_sets/myrs_27019/mongod.conf

```

![image-20230401164522853](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401164522853.png)

![image-20230401164614450](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401164614450.png)

#### 9.4.4 初始化配置副本集和主节点

- 使用客户端命令连接任意一个节点，但这里尽量要连接主节点(27017节点)：

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27017
```

- 结果，连接上之后，很多命令无法使用，，比如 show dbs 等，必须初始化副本集才行
  准备初始化新的副本集：

```
rs.initiate(configuration)
```

选项：

- configuration: https://www.mongodb.com/docs/manual/reference/replica-configuration/#replica-set-configuration-document

![image-20230401164837171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401164837171.png)

我们直接不带参数初始化

```
rs.initiate()
```

![image-20230401165139341](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401165139341.png)

- 初始化完成后
  - “ok”的值为1，说明创建成功。
  - 命令行提示符发生变化，变成了一个从节点角色，此时默认不能读写。稍等片刻，回车，变成主节
    点。

按一下回车，变成主节点

![image-20230401165321134](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401165321134.png)



#### 9.4.5 查看副本集的配置内容

```
rs.conf()
```

![image-20230401165440552](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401165440552.png)

- 说明：返回包含当前副本集配置的文档。
- 语法：rs.conf(configuration)
- 提示：
  rs.config()是该方法的别名。
  configuration：可选，如果没有配置，则使用默认主节点配置。
- 说明：
  “_id” : “” ：副本集的配置数据存储的主键值，默认就是副本集的名字
  “members” ：副本集成员数组，该成员不是仲裁节点： “arbiterOnly” : false ，优先级（权重值）： “priority” : 1
  “settings” ：副本集的参数配置。
  ![image-20230401165745741](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401165745741.png)

**副本集配置的查看命令，本质是查询的是 system.replset 的表中的数据：**

#### 9.4.6 查看副本集状态

![image-20230401170001479](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401170001479.png)

- 检查副本集状态。
- 说明：
- 返回包含状态信息的文档。此输出使用从副本集的其他成员发送的心跳包中获得的数据反映副本集的当前状态。
- 语法：rs.status()
- 说明：在展示的信息中
  “set” : “” ：副本集的名字
  “myState” : 1：说明状态正常
  “members” ：副本集成员数组
  “health” : 1 , 该节点是健康的：

#### 9.4.7 添加副本从节点

- 在主节点添加从节点，将其他成员加入到副本集

```
rs.add(host, arbiterOnly)
```

![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/b0d55c7f19fd43f38d114702e3bba0ce.png)

```
rs.add("192.168.204.104:27018")
```

![image-20230401190640773](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401190640773.png)



#### 9.4.8 添加仲裁从节点

```
rs.addArb(host)
```

- 添加一个仲裁节点到副本集

```
rs.addArb("192.168.204.104:27019")
```

 ![image-20230401190834248](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401190834248.png)

查看状态，发现members不是只有1个了，有3个

```
rs.status()
```

![image-20230401191036233](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401191036233.png)

从节点

![image-20230401191118906](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401191118906.png)

仲裁点

![image-20230401191144330](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401191144330.png)



### 9.5  副本集的数据读写操作

- 登录节点命令:[root@bobohost ~]# /usr/local/mongodb/bin/mongo --host 192.168.204.104--port 27017

```
#登录主节点

myrs:PRIMARY> use articledb
switched to db articledb
myrs:PRIMARY> db.comment.insert({"articleid":"1000001","content":"今天日子天晴","userid":"1001","nickname":"jiangbx"})
WriteResult({ "nInserted" : 1 })
myrs:PRIMARY> db.comment.find()
{ "_id" : ObjectId("6428125d4bca6baf5425d0ef"), "articleid" : "1000001", "content" : "今天日子天晴", "userid" : "1001", "nickname" : "jiangbx" }

```

![image-20230401191658426](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401191658426.png)

登录从节点20718

```
/usr/local/mongodb/bin/mongo --host 192.168.204.104 --port 27018
```

此时执行命令，报错如下：

![image-20230401192207664](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401192207664.png)

此时要执行一下命令，确认自己是从节点

```
rs.slaveOk()
#或
rs.slaveOk(true)
```

- 提示：
  该命令是 `db.getMongo().setSlaveOk()` 的简化命令。
- 插入成功后,发现可以读,但是不可以写

此时可以看到之前创建的数据库了

并且也可以执行查询

![image-20230401192627539](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401192627539.png)

如果要取消从节点，执行下面命令

```
rs.slaveOk(false)
```



仲裁者节点，不存放任何业务数据，可以登录查看。

```
/usr/local/mongodb/bin/mongo --host 192.168.204.104 --port 27019
```

就算设置它为从节点，也看不到数据

![image-20230401193011321](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401193011321.png)

它只存放副本集配置等数据。



### 9.6 主节点的选举原则

- MongoDB在副本集中，会自动进行主节点的选举，主节点选举的触发条件：
  1. 主节点故障
  2. 主节点网络不可达（默认心跳信息为10秒）
  3. 人工干预（rs.stepDown(600)）

- 一旦触发选举，就要根据一定规则来选主节点。

- 选举规则是根据票数来决定谁获胜：

1 票数最高，且获得了“大多数”成员的投票支持的节点获胜。
2 “大多数”的定义为：假设复制集内投票成员数量为N，则大多数为 N/2 + 1。例如：3个投票成员，则大多数的值是2。当复制集内存活成员数量不足大多数时，整个复制集将无法选举出Primary，复制集将无法提供写服务，处于只读状态。
3 若票数相同，且都获得了“大多数”成员的投票支持的，数据新的节点获胜。数据的新旧是通过操作日志oplog来对比的。

在获得票数相同的时候，优先级（priority）参数影响重大。

- 可以通过设置优先级（priority）来设置额外票数。优先级即权重，取值为0-1000，相当于可额外增加
  0-1000的票数，优先级的值越大，就越可能获得多数成员的投票（votes）数。指定较高的值可使成员
  更有资格成为主要成员，更低的值可使成员更不符合条件。默认情况下，优先级的值是1


了解修改优先级

```
rs.conf()
```

前面2个节点优先级都是1

![image-20230401194947158](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401194947158.png)

仲裁节点

![image-20230401195007941](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401195007941.png)



1. 先将配置导入cfg变量`myrs:SECONDARY> cfg=rs.conf()`
2. 然后修改值（ID号默认从0开始）：`myrs:SECONDARY> cfg.members[1].priority=2 `）
3. 重新加载配置:`myrs:SECONDARY> rs.reconfig(cfg)`

### 9.7 Compass连接副本集

- 如果使用云服务器需要修改配置中的主节点ip

```
var config = rs.config();
config.members[0].host="180.76.159.126:27017";rs.reconfig(config)
```

- compass连接

```
Connect to Host
```

![image-20230401195430419](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401195430419.png)

![image-20230401195518196](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230401195518196.png)

### 9.8 SpringDataMongoDB连接副本集

语法：

```
mongodb://host1,host2,host3/articledb?
connect=replicaSet&slaveOk=true&replicaSet=副本集名字
```

- slaveOk=true：开启副本节点读的功能，可实现读写分离。
- connect=replicaSet：自动到副本集中选择读写的主机。如果slaveOK是打开的，则实现了读写分
  离

```yaml
spring:
  #数据源配置
  data:
    mongodb:
      # 主机地址
      #host: 192.168.204.104
      # 数据库
      #database: articledb
      # 默认端口是27017
      #port: 10001
      #也可以使用uri连接
      uri: mongodb://192.168.204.104:27017,192.168.204.104:27018,192.168.204.104:27019/articledb?
connect=replicaSet&slaveOk=true&replicaSet=myrs
      
```

注意：

- 主机必须是副本集中所有的主机，包括主节点、副本节点、仲裁节点。
- SpringDataMongoDB自动实现了读写分离：
- 写操作时，只打开主节点连接
- 读操作是，同时打开主节点和从节点连接，但使用从节点获取数据

mongoDB客户端连接语法：

```
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]]
[/[database][?options]]

```

- mongodb:// 这是固定的格式，必须要指定。
- username:password@ 可选项，如果设置，在连接数据库服务器之后，驱动都会尝试登陆这个
  数据库
- host1 必须的指定至少一个host, host1 是这个URI唯一要填写的。它指定了要连接服务器的地址。如果要连接复制集，请指定多个主机地址。
- portX 可选的指定端口，如果不填，默认为27017
- /database 如果指定username:password@，连接并验证登陆指定数据库。若不指定，默认打开test 数据库。
- ?options 是连接选项。如果不使用/database，则前面需要加上/。所有连接选项都是键值对name=value，键值对之间通过&或;（分号）隔开
- 标准的连接格式包含了多个选项(options)，如下所示：
  ![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/9cda840e8cd646a9b64f11ed13500d6b.png)



## 10 分片集群-Sharded Cluster

### 10.1 分片概念

- 分片（sharding）是一种跨多台机器分布数据的方法， MongoDB使用分片来支持具有非常大的数据集和高吞吐量操作的部署。
- 换句话说：分片(sharding)是指将数据拆分，将其分散存在不同的机器上的过程。有时也用分区(partitioning)来表示这个概念。将数据分散到不同的机器上，不需要功能强大的大型计算机就可以储存更多的数据，处理更多的负载。
- 具有大型数据集或高吞吐量应用程序的数据库系统可以会挑战单个服务器的容量。例如，高查询率会耗尽服务器的CPU容量。工作集大小大于系统的RAM会强调磁盘驱动器的I / O容量。
- 有两种解决系统增长的方法：垂直扩展和水平扩展。
- 垂直扩展意味着增加单个服务器的容量，例如使用更强大的CPU，添加更多RAM或增加存储空间量。可用技术的局限性可能会限制单个机器对于给定工作负载而言足够强大。此外，基于云的提供商基于可用的硬件配置具有硬性上限。结果，垂直缩放有实际的最大值。
- 水平扩展意味着划分系统数据集并加载多个服务器，添加其他服务器以根据需要增加容量。虽然单个机器的总体速度或容量可能不高，但每台机器处理整个工作负载的子集，可能提供比单个高速大容量服务器更高的效率。扩展部署容量只需要根据需要添加额外的服务器，这可能比单个机器的高端硬件的总体成本更低。权衡是基础架构和部署维护的复杂性增加。
- MongoDB支持通过分片进行水平扩展。
  

### 10.2  分片集群包含的组件

MongoDB分片群集包含以下组件：

- 分片（存储）：每个分片包含分片数据的子集。 每个分片都可以部署为副本集。
- mongos（路由）：mongos充当查询路由器，在客户端应用程序和分片集群之间提供接口。
- config servers（“调度”的配置）：配置服务器存储群集的元数据和配置设置。 从MongoDB 3.4开始，必须将配置服务器部署为副本集（CSRS）。
  

![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/3eadd6f702874d5183d15387ce141f36.png)

- MongoDB在集合级别对数据进行分片，将集合数据分布在集群中的分片上。
- 27018 if mongod is a shard member；
- 27019 if mongod is a config server member

### 10.3 分片集群架构目标

两个分片节点副本集（3+3）+一个配置节点副本集（3）+两个路由节点（2），共11个服务节点。

![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/0163df4f349649ef8e2d28f761661dac.png)

### 10.4 分片（存储）节点副本集的创建

#### 10.4.1 创建2套副本集

先关闭原来的mongdb服务

```
ps -ef|grep mongo

kill -2 进程号
```



1 创建第一个副本集

- 准备存放数据的目录时还要准备一个存放日志的目录

```
#-----------myshardrs01
mkdir -p /mongodb/sharded_cluster/myshardrs01_27018/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs01_27018/data/db \ &

mkdir -p /mongodb/sharded_cluster/myshardrs01_27118/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs01_27118/data/db \ &

mkdir -p /mongodb/sharded_cluster/myshardrs01_27218/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs01_27218/data/db
```

- 在配置文件中加入以下内容

```
sharding:
  #分片角色
  clusterRole: shardsvr
```

![在这里插入图片描述](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/99062b9442a943b5a9e286909f874424.png)

- config server:https://www.mongodb.com/docs/manual/reference/glossary/#term-config-server
- shard:https://www.mongodb.com/docs/manual/reference/glossary/#term-shard
- 注意：设置sharding.clusterRole需要mongod实例运行复制。 要将实例部署为副本集成员，请使用
  replSetName设置并指定副本集的名称。


- 新建或修改配置文件：

```
vi /mongodb/sharded_cluster/myshardrs01_27018/mongod.conf
vi /mongodb/sharded_cluster/myshardrs01_27118/mongod.conf
vi /mongodb/sharded_cluster/myshardrs01_27218/mongod.conf
```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs01_27018/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myashardrs01_27018/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27018/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27018
replication:
  #副本集的名称  
  replSetName: myshardrs01
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs01_27118/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27118/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27118/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27118
replication:
  #副本集的名称  
  replSetName: myshardrs01
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs01_27218/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myshardrs01_27218/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs01_27218/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27218
replication:
  #副本集的名称  
  replSetName: myshardrs01
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

启动命令

```
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27018/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27118/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27218/mongod.conf
```

![image-20230402160447899](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230402160447899.png)

2 创建第2个副本集

```
mkdir -p /mongodb/sharded_cluster/myshardrs02_27318/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs02_27318/data/db \ &

mkdir -p /mongodb/sharded_cluster/myshardrs02_27418/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs02_27418/data/db \ &

mkdir -p /mongodb/sharded_cluster/myshardrs02_27518/log \ &
mkdir -p /mongodb/sharded_cluster/myshardrs02_27518/data/db
```

修改配置

```
vi /mongodb/sharded_cluster/myshardrs02_27318/mongod.conf
vi /mongodb/sharded_cluster/myshardrs02_27418/mongod.conf
vi /mongodb/sharded_cluster/myshardrs02_27518/mongod.conf
```

配置内容：

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs02_27318/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myshardrs02_27318/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs02_27318/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27318
replication:
  #副本集的名称  
  replSetName: myshardrs02
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs02_27418/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myashardrs02_27418/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs02_27418/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27418
replication:
  #副本集的名称  
  replSetName: myshardrs02
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myshardrs02_27518/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myshardrs02_27518/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myshardrs02_27518/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27518
replication:
  #副本集的名称  
  replSetName: myshardrs02
sharding:
  #副本集的名称  
  clusterRole: shardsvr

```

启动命令：

```
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs02_27318/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs02_27418/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs02_27518/mongod.conf
```

![image-20230402165030530](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230402165030530.png)



#### 10.4.2 配置节点副本集

- 将上节配置文件中最后一行的`clusterRole`改为`configsvr`即可,其他大同小异

```
sharding:
  #副本集的名称  
  clusterRole: configsvr
```

步骤：

```
mkdir -p /mongodb/sharded_cluster/myconfigrs_27019/log \ &
mkdir -p /mongodb/sharded_cluster/myconfigrs_27019/data/db \ &

mkdir -p /mongodb/sharded_cluster/myconfigrs_27119/log \ &
mkdir -p /mongodb/sharded_cluster/myconfigrs_27119/data/db \ &

mkdir -p /mongodb/sharded_cluster/myconfigrs_27219/log \ &
mkdir -p /mongodb/sharded_cluster/myconfigrs_27219/data/db \ &
```

新建或修改配置文件：

```
vi /mongodb/sharded_cluster/myconfigrs_27019/mongod.conf
vi /mongodb/sharded_cluster/myconfigrs_27119/mongod.conf
vi /mongodb/sharded_cluster/myconfigrs_27219/mongod.conf
```

配置文件

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myconfigrs_27019/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27019/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27019/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27019
replication:
  #副本集的名称  
  replSetName: myconfigrs
sharding:
  #副本集的名称  
  clusterRole: configsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myconfigrs_27119/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27119/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27119/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27119
replication:
  #副本集的名称  
  replSetName: myconfigrs
sharding:
  #副本集的名称  
  clusterRole: configsvr

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/myconfigrs_27219/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/sharded_cluster/myconfigrs_27219/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/myconfigrs_27219/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27219
replication:
  #副本集的名称  
  replSetName: myconfigrs
sharding:
  #副本集的名称  
  clusterRole: configsvr

```

启动

```
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27019/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27119/mongod.conf
 /usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myconfigrs_27219/mongod.conf
```

![image-20230402170219230](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230402170219230.png)

#### 10.4.3 初始化三套副本集

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27018
```

初始化

```
rs.initiate()
```

![image-20230402170620547](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230402170620547.png)

初始化完毕后，回车

加入从节点

```
rs.add("192.168.204.104:27118")
```

加入仲裁节点

```
rs.addArb("192.168.204.104:27218")
```

![image-20230402171816160](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230402171816160.png)

退出后，再次初始化下一个副本集

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27318

rs.initiate()

rs.add("192.168.204.104:27418")

rs.addArb("192.168.204.104:27518")
```

再次初始化配置节点副本集

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27019

rs.initiate()

rs.add("192.168.204.104:27119")

rs.add("192.168.204.104:27219")
```

查看副本状态

```
rs.conf()
rs.status()
```

![image-20230405135537680](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405135537680.png)

#### 10.4.4 路由节点的创建与操作

第一步，准备目录

```
mkdir -p /mongodb/sharded_cluster/mymongos_27017/log
```

新建或修改配置文件,注意是mongos.conf

```
vi /mongodb/sharded_cluster/mymongos_27017/mongos.conf
```

配置文件内容：

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/mymongos_27017/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/mymongos_27017/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27017
sharding:
  #副本集的名称  
  configDB: myconfigrs/192.168.204.104:27019,192.168.204.104:27119,192.168.204.104:27219
```

 启动：

```
/usr/local/mongodb/bin/mongos -f /mongodb/sharded_cluster/mymongos_27017/mongos.conf
```

![image-20230405160305598](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405160305598.png)

客户端连接

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27017
```

此时因为没加入分片，所以不能插入数据

![image-20230405160748550](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405160748550.png)

#### 10.4.5 在路由节点上进行分片配置操作

1 加分片：

- 语法：`sh.addShard("IP:Port")`

- 提示：如果添加分片失败，需要先手动移除分片，检查添分片的信息的正确性后，再次添加分片。
  移除分片参考(了解)：

```
use admin
db.runCommand( { removeShard: "myshardrs02" } )
```



把第一套分片加入

```
sh.addShard("myshardrs01/192.168.204.104:27018,192.168.204.104:27118,192.168.204.104:27218")
```

第2套分片加入

```
sh.addShard("myshardrs02/192.168.204.104:27318,192.168.204.104:27418,192.168.204.104:27518")
```

![image-20230405161524017](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405161524017.png)

```
mongos> sh.status()
```

![image-20230405161617032](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405161617032.png)

2 开启分片功能：

sh.enableSharding(“库名”)、sh.shardCollection(“库名.集合名”,{“key”:1})

```
sh.enableSharding("articledb")
```

![image-20230405162026102](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405162026102.png)



3 集合分片

- 对集合分片，你必须使用 sh.shardCollection() 方法指定集合和分片键。

参数：

![image-20230405162128490](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405162128490.png)

分片规则1 哈希：

- 对于 基于哈希的分片 ,MongoDB计算一个字段的哈希值,并用这个哈希值来创建数据块.在使用基于哈希分片的系统中,拥有”相近”片键的文档 很可能不会 存储在同一个数据块中,因此数据的分离性更好一些.

```
mongos> sh.shardCollection("articledb.comment",{"nickname":"hashed"})
```

![image-20230405162839725](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405162839725.png)

再次查看sh.status()

![image-20230405162941714](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405162941714.png)

分片规则2：范围策略

- 对于 基于范围的分片 ,MongoDB按照片键的范围把数据分成不同部分.假设有一个数字的片键:想象一个从负无穷到正无穷的直线,每一个片键的值都在直线上画了一个点.MongoDB把这条直线划分为更短的不重叠的片段,并称之为 数据块 ,每个数据块包含了片键在一定范围内的数据.
- 在使用片键做范围划分的系统中,拥有”相近”片键的文档很可能存储在同一个数据块中,因此也会存储在同
  一个分片中.
- 如使用作者年龄字段作为片键，按照点赞数的值进行分片：
- 注意的是：
  - 一个集合只能指定一个片键，否则报错。
  - 一旦对一个集合分片，分片键和分片值就不可改变。 如：不能给集合选择不同的分片键、不能更新分片键的值。
  - 根据age索引进行分配数据。
    

#### 10.4.6 分片方式性能对比

- 基于范围的分片方式提供了更高效的范围查询,给定一个片键的范围,分发路由可以很简单地确定哪个数
  据块存储了请求需要的数据,并将请求转发到相应的分片中.

- 不过,基于范围的分片会导致数据在不同分片上的不均衡,有时候,带来的消极作用会大于查询性能的积极
  作用.比如,如果片键所在的字段是线性增长的,一定时间内的所有请求都会落到某个固定的数据块中,最终
  导致分布在同一个分片中.在这种情况下,一小部分分片承载了集群大部分的数据,系统并不能很好地进行
  扩展.

- 与此相比,基于哈希的分片方式以范围查询性能的损失为代价,保证了集群中数据的均衡.哈希值的随机性
  使数据随机分布在每个数据块中,因此也随机分布在不同分片中.但是也正由于随机性,一个范围查询很难
  确定应该请求哪些分片,通常为了返回需要的结果,需要请求所有分片.

- 如无特殊情况，一般推荐使用 Hash Sharding。
- 理想化的 shard key 可以让 documents 均匀地在集群中分布：

![image-20230405164930621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405164930621.png)

显示集群的详细信息

```
mongos> db.printShardingStatus()
```

看均衡器是否工作（需要重新均衡时系统才会自动启动，不用管它）：

```
mongos> sh.isBalancerRunning()
```

- 查看当前Balancer状态：

```
mongos> sh.getBalancerState()
```

#### 10.4.7 再增加一个路由节点

第一步，准备目录

```
mkdir -p /mongodb/sharded_cluster/mymongos_27117/log
```

新建或修改配置文件,注意是mongos.conf

```
vi /mongodb/sharded_cluster/mymongos_27117/mongos.conf
```

配置文件内容：

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件  
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/sharded_cluster/mymongos_27117/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/sharded_cluster/mymongos_27117/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27117
sharding:
  #副本集的名称  
  configDB: myconfigrs/192.168.204.104:27019,192.168.204.104:27119,192.168.204.104:27219
```

 启动：

```
/usr/local/mongodb/bin/mongos -f /mongodb/sharded_cluster/mymongos_27117/mongos.conf
```

配置已经自动完成

### 10.5 Compass连接分片集群

连接2个路由地址就可以了，譬如：

![image-20230405165913691](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405165913691.png)

![image-20230405165936799](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405165936799.png)



### 10.6 SpringDataMongDB连接分片集群

- Java客户端常用的是SpringDataMongoDB，其连接的是mongs路由，配置和单机mongod的配置是一样的。
- 多个路由的时候的SpringDataMongoDB的客户端配置参考如下：

```yaml
spring:
  #数据源配置
  data:
    mongodb:
      # 主机地址
      # host: 192.168.204.104
      # 数据库
      # database: articledb
      # 默认端口是27017
      # port: 10001
      #也可以使用uri连接
      #uri: mongodb://192.168.204.104:27017/articledb
      #连接路由字符串
      uri: mongodb://192.168.204.104:27017,192.168.204.104:27117/articledb
      
```

10.7 清除节点数据（备用）

如果在搭建分片的时候有操作失败或配置有问题，需要重新来过的，可以进行如下操作：

- 第一步：查询出所有的测试服务节点的进程：

  ```
  [root@bobohost sharded_cluster]# ps -ef |grep mongo
  ```

  - 根据上述的进程编号，依次中断进程：

- 第二步：清除所有的节点的数据：rm -rf /mongodb/sharded_cluster/myconfigrs_27019/data/db/*.* \ &

- 第三步：查看或修改有问题的配置、

- 第四步：依次启动所有节点，不包括路由节点:/usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/myshardrs01_27018/mongod.conf

- 第五步：对两个数据分片副本集和一个配置副本集进行初始化和相关配置

- 第六步：检查路由mongos的配置，并启动mongos/usr/local/mongodb/bin/mongod -f /mongodb/sharded_cluster/mymongos_27017/mongos.cfg

- 第七步：mongo登录mongos，在其上进行相关操作。
  

## 11 安全认证

### 11.1 MongoDB的用户和角色权限简介

常用的内置角色：

- 数据库用户角色：read、readWrite;
- 所有数据库用户角色：readAnyDatabase、readWriteAnyDatabase、
  userAdminAnyDatabase、dbAdminAnyDatabase
- 数据库管理角色：dbAdmin、dbOwner、userAdmin；
- 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
- 备份恢复角色：backup、restore；
- 超级用户角色：root
- 内部角色：system


命令：

```
// 查询所有角色权限(仅用户自定义角色)
> db.runCommand({ rolesInfo: 1 })
// 查询所有角色权限(包含内置角色)
> db.runCommand({ rolesInfo: 1, showBuiltinRoles: true })
// 查询当前数据库中的某角色的权限
> db.runCommand({ rolesInfo: "<rolename>" })
// 查询其它数据库中指定的角色权限
> db.runCommand({ rolesInfo: { role: "<rolename>", db: "<database>" } }
// 查询多个角色权限
> db.runCommand(
{
rolesInfo: [
"<rolename>",
{ role: "<rolename>", db: "<database>" },
...
]
}
)

```

### 11.2 单实例环境

#### 11.2.1 添加用户和权限

- 创建两个管理员用户，一个是系统的超级管理员 myroot ，一个是admin库的管理用户
  myadmin

```
#客户端连接
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=10001
use admin

// 创建系统超级用户 myroot,设置密码123456，设置角色root
db.createUser({user:"myroot",pwd:"123456",roles:["root"]})
// 创建专门用来管理admin库的账号myadmin，只用来作为用户权限的管理
db.createUser({user:"myadmin",pwd:"123456",roles:
[{role:"userAdminAnyDatabase",db:"admin"}]})

// 查看已经创建了的用户的情况：
> db.system.users.find()
```

![image-20230405172558410](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405172558410.png)

```
//查看已经创建了的用户的情况：
> db.system.users.find()
```

![image-20230405172703788](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405172703788.png)

```
//删除用户
> db.dropUser("myadmin")
```

![image-20230405172811062](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405172811062.png)

```
//修改密码
> db.changeUserPassword("myroot", "123456")
```

认证测试：

```
use admin
# 测试原密码，验证失败
db.auth("myroot","12345")
# 测试已改的密码
db.auth("myroot","123456")
```

![image-20230405173108323](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405173108323.png)

然后我们一般不用超管用户登录，会使用普通用户

创建普通用户

```
> use articledb
switched to db articledb
> db.createUser({user: "bobo", pwd: "123456", roles: [{ role: "readWrite", db:
... "articledb" }]})
Successfully added user: {
	"user" : "bobo",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "articledb"
		}
	]
}
> db.auth("bobo","123456")
1

```

![image-20230405173540939](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405173540939.png)



#### 11.2.2 服务端开启和客户端登录

1、关闭已经开启的服务

```
ps -ef|grep mongo

kill -2 进程号
```

2、重新启动

加参数(此方法不方便)

```
mongod --config /usr/local/mongodb/conf/mongodb.conf -auth
```

修改配置文件/usr/local/mongodb/conf/mongodb.conf

```
security:
  #开启授权认证
  authorization: enabled

```

启动时，可不加-auth参数

添加单机

```
mkdir -p /mongodb/single/data/db
mkdir -p /mongodb/single/log

vi /mongodb/single/mongod.conf

```

```yaml
systemLog:
  #MongoDB发送所有日志输出的目标指定为文件 
  destination: file
  #mongod或mongos应向其发送所有诊断日志记录信息的日志文件的路径  
  path: "/mongodb/single/log/mongod.log"
  #当mongos或mongod实例重新启动时，mongos或mongod会将新条目附加到现有日志文件的末尾  
  logAppend: true
storage:
  #mongod实例存储其数据的目录。storage.dbPath设置仅适用于mongod
  dbPath: "/mongodb/single/data/db"
  journal:
    #启用或禁用持久性日志以确保数据文件保持有效和可恢复。     
    enabled: true
processManagement:
  #启用在后台运行mongos或mongod进程的守护进程模式。  
  fork: true
  #指定用于保存mongos或mongod进程的进程ID的文件位置，其中mongos或mongod将写入其PID
  pidFilePath: "/mongodb/single/log/mongod.pid"
net:
  #服务实例绑定所有IP，有副作用，副本集初始化的时候，节点名字会自动设置为本地域名，而不是ip
  #bindIpAll: true
  #服务实例绑定的IP,0.0.0.0让所有机器都能连接。
  bindIp: localhost,192.168.204.104
  #绑定的端口  
  port: 27017
security:
  #开启授权认证
  authorization: enabled
```

再次启动

```
/usr/local/mongodb/bin/mongod -f /mongodb/single/mongod.conf
```

连接

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27017
```

发现没有权限，啥也看不了

![image-20230405181021637](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230405181021637.png)

登录

```
db.auth("myroot","123456")
```

![image-20230408150552260](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408150552260.png)

测试登录普通用户

```
> use articledb
switched to db articledb
> db.auth("bobo","123456")
1
```

![image-20230408151650313](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408151650313.png)



#### 11.2.3 SpringDataMongoDB连接认证

- 使用用户名和密码连接到 MongoDB 服务器，你必须使用'username:password@hostname/dbname' 格式，'username'为用户名，'password' 为密码。
- 目标：使用用户bobo使用密码 123456 连接到MongoDB 服务上。
- application.yml：
  

```yaml
spring:
  #数据源配置
  data:
    mongodb:
      # 主机地址
      # host: 192.168.204.104
      # 数据库
      # database: articledb
      # 默认端口是27017
      # port: 10001
      #帐号
      # username: bobo
      #密码
      # password: 123456
      #也可以使用uri连接
      uri: mongodb://bobo:123456@192.168.204.104:27017/articledb
```

![image-20230408152508811](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408152508811.png)

### 11.3 副本集环境

- 对于搭建好的mongodb副本集，为了安全，启动安全认证，使用账号密码登录。
- 副本集环境使用之前搭建好的，架构如下

![image-20230408152858734](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408152858734.png)

首先停止单机服务

```
ps -ef|grep mongo

kill -2 进程号
```

启动副本集

```
/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27017/mongod.conf
/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27018/mongod.conf
/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27019/mongod.conf
```

![image-20230408153611257](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408153611257.png)

登录主节点

```
/usr/local/mongodb/bin/mongo --host=192.168.204.104 --port=27017
```

#### 11.3.1 创建管理员账户

创建超管用户

```
use admin

// 创建系统超级用户 myroot,设置密码123456，设置角色root
db.createUser({user:"myroot",pwd:"123456",roles:["root"]})
```

![image-20230408154458509](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408154458509.png)



#### 11.3.2 创建副本集的Key文件

- 生成一个key文件到当前文件夹

- 可以使用任何方法生成密钥文件。例如，以下操作使用openssl生成密码文件，然后使用chmod来更改
  文件权限，仅为文件所有者提供读取权限

```
openssl rand -base64 90 -out ./mongo.keyfile
# 授权只有当前用户可以使用
chmod 400 ./mongo.keyfile
ll mongo.keyfile

```

![image-20230408155030231](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408155030231.png)

提示：

- 所有副本集节点都必须要用同一份keyfile，一般是在一台机器上生成，然后拷贝到其他机器上，且必须
  有读的权限，否则将来会报错： permissions on /mongodb/replica_sets/myrs_27017/mongo.keyfile are too open
- 一定要保证密钥文件一致，文件位置随便。但是为了方便查找，建议每台机器都放到一个固定的位置，
  都放到和配置文件一起的目录中。
- 这里将该文件分别拷贝到多个目录中：

```
cp mongo.keyfile /mongodb/replica_sets/myrs_27017
cp mongo.keyfile /mongodb/replica_sets/myrs_27018
cp mongo.keyfile /mongodb/replica_sets/myrs_27019
```

![image-20230408155547694](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408155547694.png)

#### 11.3.3 修改配置文件指定keyfile

- 分别编辑几个服务的mongod.conf文件，添加相关内容：
- 例如,在`/mongodb/replica_sets/myrs_27017/mongod.conf`中加上

```
vi  /mongodb/replica_sets/myrs_27017/mongod.conf
```

```yaml
security:
  #KeyFile鉴权文件
  keyFile: /mongodb/replica_sets/myrs_27017/mongo.keyfile
  #开启授权认证
  authorization: enabled

```

![image-20230408160630633](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408160630633.png)

```yaml
security:
  #KeyFile鉴权文件
  keyFile: /mongodb/replica_sets/myrs_27018/mongo.keyfile
  #开启授权认证
  authorization: enabled
```

```yaml
security:
  #KeyFile鉴权文件
  keyFile: /mongodb/replica_sets/myrs_27019/mongo.keyfile
  #开启授权认证
  authorization: enabled
```

重新启动3个服务

```
ps -ef|grep mongo

kill -2 进程号

/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27017/mongod.conf
/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27018/mongod.conf
/usr/local/mongodb/bin/mongod -f /mongodb/replica_sets/myrs_27019/mongod.conf
```

再次登录客户端

```
/usr/local/mongodb/bin/mongo  --port=27017
```

此时发现，执行show dbs命令查不到任何结果

![image-20230408161136095](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408161136095.png)

用超管用户登录

```
use admin

db.auth("myroot","123456")
```

![image-20230408162904628](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408162904628.png)

创建普通用户

```
> use articledb
switched to db articledb
> db.createUser({user: "bobo", pwd: "123456", roles: [{ role: "readWrite", db:
... "articledb" }]})

```

#### 11.3.4 SpringDataMongoDB连接认证

application.yml：

```yaml
spring:
  #数据源配置
  data:
    mongodb:
      # 主机地址
      # host: 192.168.204.104
      # 数据库
      # database: articledb
      # 默认端口是27017
      # port: 10001
      #也可以使用uri连接
      #uri: mongodb://192.168.204.104:27017/articledb
      #连接路由字符串
      uri: mongodb://bobo:123456@192.168.204.104:27017,192.168.204.104:27018,192.168.204.104:27019/articledb?connect=replicaSet&slaveOk=true&replicaSet=myrs
```

![image-20230408164254007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230408164254007.png)

### 11.4 分片安全认证

基本类似副本集的操作





