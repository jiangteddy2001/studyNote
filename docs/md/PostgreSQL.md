# PostgreSQL

## 1 概述

### 1.1 简介

PostgreSQL是一个开源的，特性非常齐全的对象-关系型数据库，同时还支持NoSql的文档型存储。

### 1.2 特征

- 多版本并发控制：PostgreSQL使用多版本并发控制（MVCC，Multiversion concurrency control）系统进行并发控制，该系统向每个用户提供了一个数据库的"快照"，用户在事务内所作的每个修改，对于其他的用户都不可见，直到该事务成功提交。

- 数据类型：包括文本、任意精度的数值数组、JSON 数据、枚举类型、XML 数据等。

- 全文检索：通过 Tsearch2 或 OpenFTS。

- NoSQL：JSON，JSONB，XML，HStore 原生支持，甚至 NoSQL 数据库的外部数据包装器。

- 数据仓库：能平滑迁移至同属 PostgreSQL 生态的 GreenPlum，DeepGreen等，使用 FDW（Foreign data wrappers） 进行 ETL（Extract-Transform-Load）。

- 函数：通过函数，可以在数据库服务器端执行指令程序。

- 索引：用户可以自定义索引方法，或使用内置的 B 树，哈希表与 GiST（Generalized Search Tree） 索引。

- 触发器：触发器是由SQL语句查询所触发的事件。如：一个INSERT语句可能触发一个检查数据完整性的触发器。触发器通常由INSERT或UPDATE语句触发。

- 规则：规则（RULE）允许一个查询能被重写，通常用来实现对视图（VIEW）的操作，如插入（INSERT）、更新（UPDATE）、删除（DELETE）。

- 继承：PostgreSQL实现了表继承，一个表可以从0个或者多个其他表继承，而对一个表的查询则可以引用一个表的所有行或者该表的所有行加上它所有的后代表。
  

### 1.3 约定

PostgreSQL使用一种客户端/服务器的模型

服务器进程（postgres）：管理数据库文件、接受来自客户端应用与数据库的联接并且代表客户端在数据库上执行操作

客户端应用：可以是一个面向文本的工具， 也可以是一个图形界面的应用，或者是一个通过访问数据库来显示网页的网页服务器，或者是一个特制的数据库管理工具


PostgreSQL支持标准的SQL类型`int`、`smallint`、`real`、`double precision`、`char(*N*)`、`varchar(*N*)`、`date`、`time`、`timestamp`和`interval`，还支持其他的通用功能的类型和丰富的几何类型。

### 1.4 优点

PostgreSQL的主要优点：

1.PostgreSQL是完全免费的，它是BSD协议。PostgreSQL数据库将不受其他公司的控制。oracle数据库是商业数据库，不是开放的。尽管MySQL数据库是开源的，但由于SUN被Oracle收购，因此它现在基本上由Oracle控制。实际上，在收购SUN之前，MySQL中最重要的InnoDB引擎也由Oracle控制。在MySQL中InnoDB引擎中的许多重要数据都放在InnoDB引擎中。因此，如果MySQL的市场范围与oracle数据库的市场范围冲突，oracle公司肯定会牺牲MySQL，这是毫无疑问的。

2.有很多与PostgreSQl合作的开源软件，还有很多分布式集群软件，例如pgpool，pgcluster，slony，plploxy等。它很容易实现解决方案，例如读写分离，负载平衡和数据级别拆分，这在MySQL下比较困难。

3.PostgreSQL源代码写得很清楚，可读性比MySQL好。因此，许多公司都使用基本PostgreSQL进行二次开发。

4.PostgreSQL在许多方面都比MySQL强，例如复杂的SQL执行，存储过程，触发器和索引。同时，PostgreSQL是多进程的，而MySQL是线程化的。尽管在并发性不高时MySQL的处理速度很快，但是在并发性高时，MySQL的整体处理性能不如在具有多核的单台计算机上的PostgreSQL更好。原因是MySQL线程无法充分利用CPU的功能。



## 2 安装

### 2.1 windows安装

访问 https://www.postgresql.org/download/windows/

![image-20230301144232558](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011442745.png)

解压缩包

![image-20230301144528457](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011445505.png)

***在bin目录同级下新建一个data文件夹，用来存放数据\***

![image-20230301144600167](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011446207.png)

**初始化数据库，进入bin目录并执行初始化命令**

```
 初始化DB命令： initdb.exe -D  D:\dev\postgresql-14.7-1-windows-x64-binaries\pgsql\data  -E UTF-8 --locale=chs
-U postgres -W 

注：
-D ：指定数据库簇的存储目录D:\postgresql-14.4-1\data
-E ：指定DB的超级用户的用户名postgres –locale：关于区域设置（chinese-simplified-china）
-U ：默认编码格式chs
-W ：为超级用户指定密码的提示

```

![image-20230301144939924](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011449978.png)

![image-20230301145028414](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011450451.png)

启动数据库

```
启动数据库命令：pg_ctl -D D:\dev\postgresql-14.7-1-windows-x64-binaries\pgsql\data -l logfile start
```

![image-20230301145214425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011452460.png)



客户端访问

接下来用它自带的客户端访问：
点击安装目录下的pgAdmin 4文件夹，进入bin目录选择pgAdmin4.exe文件运行

![image-20230301145307941](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011453977.png)

![image-20230301145905329](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011459378.png)

![image-20230301150703774](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011507842.png)

![image-20230301150832311](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011508355.png)

![image-20230301150842796](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011508843.png)

创建数据库

![image-20230301150957257](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011509299.png)

添加配置信息

![image-20230301151052863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011510909.png)

由于初始化没设置密码，出现“FATAL:  role "postgres" does not exist”。解决办法是用Administrator登录

![image-20230301151909474](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011519525.png)

![image-20230301153142445](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011531478.png)

```
# 用超级管理员登录，默认无密码
psql –U Administrator –d postgres

# 创建用户
create user postgres superuser;
# 修改用户密码
ALTER USER postgres WITH PASSWORD '123456';
```

**使用Navicat Premium 操作 PostgreSQL**

![image-20230301153402553](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011534589.png)

测试连接

![image-20230301153525754](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011535791.png)

注册为系统服务

```
# 注册PostgreSQL系统服务命令
pg_ctl register -N postgresql -D D:\dev\postgresql-14.7-1-windows-x64-binaries\pgsql\data
```

在bin目录下用`管理员身份`执行命令：

![image-20230301154457227](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011544273.png)

查看是否成功：
**win+r** 弹出的运行框中，输入：**services.msc**，如下：

![image-20230301154638031](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011546076.png)

这样在windows下安装postgresql就成功了！



### 2.2 Linux安装

https://www.postgresql.org/download/



![image-20230301161359037](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011613079.png)

![image-20230301161459508](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011614556.png)

![image-20230301161615554](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011616597.png)

把压缩包上传到/opt

![image-20230301161808946](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011618979.png)



##### 创建用户

```
#创建用户
useradd postgres
#设置密码-密码1qaz2wsx
passwd postgres

```

##### 安装依赖包

```
yum install -y gcc-c++ gcc cmake ncurses-devel perl zlib* readline-devel
```

##### 创建目录并授权

```
[root@localhost opt]# mkdir -p /usr/local/pg12
[root@localhost opt]# mkdir -p /pgdata/12/data
[root@localhost opt]# chown -R postgres. /pgdata
[root@localhost opt]# chown -R postgres. /usr/local/pg12
[root@localhost opt]# chmod 700 /pgdata/12/data -R
```

##### 系统参数优化

```
vi /etc/sysctl.conf

kernel.shmmax = 6442450944
kernel.shmall = 1677721
kernel.shmmni = 4096
kernel.sem = 4096 83886080 4096 20480
fs.file-max=76724600
net.ipv4.ip_local_port_range=40000 65535
net.core.rmem_default=262144
net.core.rmem_max=4194304
net.core.wmem_max=4194304
net.core.wmem_default=262144

sysctl -p

vi /etc/security/limits.conf

 
* soft nproc 131072
* hard nproc 131072
* soft nofile 131072
* hard nofile 131072
* soft memlock 50000000
* hard memlock 50000000
* soft core unlimited
* hard core unlimited



```

##### 源码安装

```
#解压缩
tar xf postgresql-12.6.tar.gz

cd postgresql-12.6


./configure --prefix=/usr/local/pg12 --with-pgport=1921
gmake world
gmake install-world

```

##### 环境变量

```
su - postgres

 vi .bash_profile
 
export PGDATA=/pgdata/12/data 
export LANG=en_US.utf8
export PGHOME=/usr/local/pg12
export LD_LIBRARY_PATH=$PGHOME/lib:/lib64:/usr/lib64:/usr/local/lib64:/lib:/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH
export PATH=$PGHOME/bin:$PATH:.
export PGUSER=postgres
export DATE=`date +"%Y%m%d%H%M"`
export MANPATH=$PGHOME/share/man:$MANPATH

source .bash_profile

# 检查
psql --version

 
```

![image-20230301172828379](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303011728424.png)



##### 初始化数据

```
 initdb -D /pgdata/12/data -w
 # 生产环境
 initdb -A md5 -D $PGDATA -E UTF8 --locale=C -W
 
 # 输入密码123456
```



## 3 启动和关闭

### 3.1 手动方式

```
pg_ctl -D /pgdata/12/data -l logfile start

# 断开连接扣关闭
pg_ctl -D /pgdata/12/data stop -ms

# 快速关闭
pg_ctl -D /pgdata/12/data stop -mf 

# 不能保证安全的关闭
pg_ctl -D /pgdata/12/data stop -mi

pg_ctl restart  -mf

```

### 3.2 脚本方式

```
/opt/postgresql-12.6/contrib/start-scripts/linux
```



## 4 基础管理

### 4.1 连接关联

本地Socket连接

```
psql -W
# 输入密码

\q
# 退出
```

远程连接

```
psql -d postgres -h 192.168.133.118 -p 1921 -U postgres
```

防火墙介绍

权限控制

```
pg_hba.conf文件为pg实例的防火墙配置文件
配置文件分为5个部分

文件在数据的目录下
cd /pgdata/12/data

vi pg_hba.conf
```

![image-20230301222417572](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303012224733.png)

![image-20230301223021337](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303012230398.png)

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     md5
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
```

增加一行，允许用户访问

![image-20230301231657439](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303012316479.png)

```
# IPv4 local connections:
host    all             all             0.0.0.0/0            md5

# md5可以改成reject，那么下面其他规则不执行了
pg_ctl reload

vi postgresql.conf


```

![image-20230301230540820](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303012305870.png)

重启服务

```
pg_ctl restart  -mf
```



### 4.2 用户管理

#### 4.2.1 用户的作用

用来登录数据库实例，管理数据库对象

#### 4.2.2 创建用户

```
# 创建的用户自带连接数据库功能
create user

# 创建的不自带连接数据库
create role

注意：CREATE USER和CREATE ROLE的区别在于，CREATE USER指令创建的用户默认是有登录权限的，而CREATE ROLE没有。
```

```
例子：创建用户username 密码123456，并且有创建数据库权限和创建角色权限，如：
create user username password '123456' createdb createrole;

--删除用户
drop user username;

--修改用户密码
alter user username  password '123456';
```

```
create user admin with SUPERUSER password '123';
psql -d postgres -U admin -h 192.168.133.118 -p 1921;

postgres=# create database admin;
CREATE DATABASE
postgres=# \c admin
Password: 
You are now connected to database "admin" as user "postgres".
admin=# create table tl(id int);
CREATE TABLE
admin=# 

# 删除用户
postgres=# drop user jiangbx;

postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 admin     | Superuser                                                  | {}
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}



```

![image-20230302114607927](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021146073.png)





### 4.3 权限管理

#### 4.3.1 权限级别

**cluster权限**：实例通过pg_hba.conf配置

**database级别**：数据库权限可以通过grant和revoke操作schema配置

**TBS级别**：表空间权限通过grant和revoke操作表、物化视图、索引、临时表配置

**schema级别**：模式权限通过grant和revoke操作模式下的对象配置

**object权限**：对象权限通过grant和revoke配置

![image-20230302152851194](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021528329.png)





#### 4.3.2 权限定义

- database权限设置

```
GRANT create on DATABASE test2023 TO test;
```

**schema权限设置**

```
ALTER SCHEMA abc OWNER to abc;

GRANT select,insert,update,delete ON ALL TABLES IN SCHEMA abc to abc;
```

**object权限**：

```
grant select,insert,update,delete on a.b to u;
```



## 5 基础命令介绍

```
? \ 所有命令帮助
\l show databases， 列出所有数据库
\di 列出连接数据库中所有index
\dv 列出连接数据库中所有view
\h sql命令帮助
\dt show tables， 列出连接数据库中所有表
\d
\d后面什么都不带，将列出当前数据库中的所有表。
\d后面跟一个表名，表示显示这个表的结构定义
\q 退出连接
\c [database_name] // 切换到指定数据库,相当于use
\c show current DB name and user
\conninfo 显示客户端的连接信息
\du list all roles
显示字符集：\encoding
\du // 显示所有用户
select version(); // 获取版本信息
```



```
\? #分页显示 一些命令
```

![image-20230302153727521](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021537594.png)

```
\l #show databases， 列出所有数据库
```

![image-20230302153809352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021538398.png)

```
\h sql命令帮助
```

![image-20230302154104004](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021541067.png)

```
# list all roles
\du 
```

![image-20230302154142821](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021541857.png)



## 6 体系结构介绍

### 6.1 C/S 结构

```
PG也是典型的C/S结构
```

### 6.2结构概述

![image-20230302154759998](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021548058.png)





### 6.3 实例结构

![image-20230302155257811](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021552875.png)

### 6.4 进程结构

![image-20230302161024532](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021610618.png)

#### 6.4.1 建立会话过程：

阶段一：

客户端发起请求。

阶段二：
该阶段由主服务postmaster进程负责。

服务器是否接受客户端的host通信认证。 

服务器对客户端进行身份鉴别。

PM进程：提供监听、连接协议、验证功能，fork其他进程 ，监听哪个IP是受到postgres.conf影响的，默认提供socket和TCP方式连接，建立会话的过程 。

验证功能：通过pg_hba.conf和用户验证模块来提供。 

阶段三：
阶段二通过之后，主服务进程为该客户端单独fork一个客户端工作进程Postgres。

SP进程：会话进程。用户一旦验证成功就会fork一个新的进程。

分配PGA里面的work_mem，从磁盘读取数据到SGA中，与SP通信。

阶段四：

客户端与Postgres进程建立通信连接，由Postgres进程负责后续所有的客户端请求操作，直至客户端退出后，该Postgres进程消失。


![image-20230302155803789](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021558853.png)

#### 6.4.2 **更新语句的流程**

BgWriter（后台写）进程 

WaLWriter（预写式日志）进程 

AutoVAcuum（系统自动清理） 

SysLogger（系统日志）进程 

PgArch（归档）进程     

PgStat（统计数据收集）进程     

CheckPoint（检查点）进程

建立通信之后，更新操作将磁盘中数据读取到shared_buffers，对数据的操作在此进行，同时会在log buffer中记录更新操作，并且后续会有BgWriter（图中BGW进程）进程将数据刷脏到磁盘中。

数据刷脏前，日志会先落盘，log buffer会被进程WaLWriter（预写式日志）进程刷新到磁盘


#### 6.4.3 名词解释

```
PM进程：数据库守护进程。通过pg_hba.conf和用户验证模块来提供

SP进程：Session Processers 会话进程，用户一旦验证成功就fork一个新的进程

BGW进程：background write进程，主要负责后台刷新脏页

Sysloger进程:主要负责数据库状态的信息日志记录

CKPT：检查点进程

WALW:WAL（redo）日志刷新进程

ARCH：WAL日志的归档日志
```



### 6.5 内存结构

![image-20230302161128736](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021611796.png)



### 6.6 存储结构

![image-20230302161203820](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021612869.png)



## 7 文件介绍

### 7.1 日志文件种类

1. pg_wal（WAL 日志，即重做日志） 内容一般不具有可读性`强制开启`
2. pg_log（数据库运行日志） 内容可读 `默认关闭`的，需要设置参数启动
3. pg_clog（事务提交日志，记录的是事务的元数据） 内容一般不具有可读性 `强制开启`

```
PGDATA/log 运行日志（pg10前为PGDATA/pg_log）
PGDATA/pg_wal 重做日志（pg10前为PGDATA/pg_xlog）
PGDATA/pg_xact 事务提交日志（pg10前为PGDATA/pg_clog）
服务器日志，可以再启动的时候指定，比如pg_ctl start -l ./alert.log
```

### 7.2 运行日志参数

PostgreSQL运行日志可以实现日志输出记录，默认是没有启动记录。这个日志一般是记录服务器与DB的状态，比如各种Error信息，定位慢查询SQL，数据库的启动关闭信息，发生checkpoint过于频繁等的告警信息，诸如此类。

![image-20230302161640799](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303021616865.png)

```
log_line_prefix参数说明：

%a = application name 应用名称
%u = user name 用户名称
%d = database name 数据库名称
%r = remote host and port 远程主机与端口
%h = remote host 远程主机
%p = process ID 进程号
%t = timestamp without milliseconds 时间戳格式
%m = timestamp with millisecond 时间戳格式
%n = timestamp with milliseconds (as a Unix epoch) 时间戳格式
%i = command tag 命令标签
%e = SQL state SQL语句状态

```



### 7.3 CSV日志入库

创建表

```
CREATE TABLE postgres_log
(
 log_time timestamp(3) with time zone,
 user_name text,
 database_name text,
 process_id integer,
 connection_from text,
 session_id text,
 session_line_num bigint,
 command_tag text,
 session_start_time timestamp with time zone,
 virtual_transaction_id text,
 transaction_id bigint,
 error_severity text,
 sql_state_code text,
 message text,
 detail text,
 hint text,
 internal_query text,
 internal_query_pos integer,
 context text,
 query text,
 query_pos integer,
 location text,
 application_name text,
 PRIMARY KEY (session_id, session_line_num)
);

```

```
\copy postgres_log from ‘<CSV日志路径>' with csv;
```

```
postgres=# copy pg_log from '$PGLOG/postgresql-16.csv' with csv;
COPY 3
```



### 7.4 postgresql.conf







### 7.5 pg_hba.conf







### 7.6 pg_ident.conf





### 7.7 控制文件





### 7.8 数据文件





### 7.9 Online WAL日志





### 7.10 ARCH WAL日志





## 8 备份恢复





## 9 Springboot

### 9.1 建表

```
postgres=# create database webapp;
CREATE DATABASE

postgres=# \c webapp
```

```
DROP TABLE IF EXISTS "jb_user";
CREATE TABLE "public"."jb_user" (
  "id" SERIAL8 NOT NULL,
  "name" varchar(50) COLLATE "pg_catalog"."default",
  "mobile" varchar(50) COLLATE "pg_catalog"."default",
  PRIMARY KEY ("id")
);
COMMENT ON COLUMN "public"."jb_user"."id" IS '主键';
COMMENT ON COLUMN "public"."jb_user"."name" IS '用户名';
COMMENT ON COLUMN "public"."jb_user"."mobile" IS '手机号';
COMMENT ON TABLE "public"."jb_user" IS '用户表';

INSERT INTO "public"."jb_user" (name,mobile) VALUES ('Allen', '19137709987');
SELECT * FROM "public"."jb_user";

```

![image-20230303170835084](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303031708250.png)

### 9.2 创建项目

![image-20230303170945206](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303031709280.png)

配置文件

```
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://192.168.133.118:1921/webapp?useUnicode=true&characterEncoding=UTF-8
spring.datasource.username=postgres
spring.datasource.password=123456
logging.level.com.xx.mybatis.dao=Debug

```

依赖

```
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.0</version>
        </dependency>

        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

实体类

```java
package com.jiang.jbpsql.domain;

/**
 * [实体]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/3/3 14:19]
 */
public class UserBean {
    private int id;
    private String name ;
    private String mobile ;

    public void setId(int id) {
        this.id = id;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getMobile() {
        return mobile;
    }
    public void setMobile(String mobile) {
        this.mobile = mobile;
    }

    @Override
    public String toString() {
        return "user[id="+id+",name="+name+",mobile="+mobile+"]";
    }

}

```

接口

```java
package com.jiang.jbpsql.dao;


import com.jiang.jbpsql.domain.UserBean;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {

    @Select(value = "select * from jb_user where id = #{id}")
    public UserBean findById(int id);
}

```

service

```java
package com.jiang.jbpsql.service;

import com.jiang.jbpsql.dao.UserMapper;
import com.jiang.jbpsql.domain.UserBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * [查询]
 *
 * @author : jiangbx
 * @version : [v1.0]
 * @createTime : [2023/3/3 16:42]
 */
@Service
public class UserService {

    @Autowired
    private UserMapper userMapper;

    public UserBean findById(int id) {
        return userMapper.findById(id);
    }

}

```

入口类

```Java
package com.jiang.jbpsql;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 入口
 */
@SpringBootApplication
@MapperScan(basePackages = "com.jiang.jbpsql.dao")
public class JbPsqlApplication {

    public static void main(String[] args) {
        SpringApplication.run(JbPsqlApplication.class, args);
    }

}

```

### 9.3 测试验证

测试类

```java
package com.jiang.jbpsql;

import com.jiang.jbpsql.domain.UserBean;
import com.jiang.jbpsql.service.UserService;
import org.junit.Assert;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;


@SpringBootTest
@RunWith(SpringRunner.class)
class JbPsqlApplicationTests {

    @Autowired
    private UserService userService;

    @Test
    void contextLoads() {
        UserBean user = userService.findById(1);

        Assert.assertEquals("Allen", user.getName());
    }

}

```

![image-20230303171404972](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303031714050.png)
