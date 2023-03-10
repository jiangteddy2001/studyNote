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
 initdb -D /pgdata/12/data -W
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

![image-20230308215635008](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303082156192.png)

```
logging_collector =on
#配置日志目录，默认为pg_log即可
log_directory = 'pg_log' 
log_statement = 'all'			# none, ddl, mod, all,日志记录级别，根据需要定，我是把所有的日志全记录下来方便定位问题，一般ddl就够了
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log' 
log_file_mode = 0600
log_rotation_age = 1d

```

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

![image-20230307142748035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071427253.png)



### 7.5 pg_hba.conf

```
略
```

### 7.6 pg_ident.conf

![image-20230307142841650](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071428722.png)



### 7.7 控制文件

```
$ pg_controldata $PGDATA
```

![image-20230307143041073](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071430129.png)

```
pg_control version number:            1201
Catalog version number:               201909212
Database system identifier:           7205531806125590987     #dbid
Database cluster state:               in production           # primary
pg_control last modified:             Tue 07 Mar 2023 02:25:25 PM CST
Latest checkpoint location:           0/16891A0
Latest checkpoint's REDO location:    0/16891A0                 # redo位置
Latest checkpoint's REDO WAL file:    000000010000000000000001  #文件号
Latest checkpoint's TimeLineID:       1
Latest checkpoint's PrevTimeLineID:   1
Latest checkpoint's full_page_writes: on
Latest checkpoint's NextXID:          0:499                   #下一个事务ID
Latest checkpoint's NextOID:          16407                   # 下一个OID
Latest checkpoint's NextMultiXactId:  1
Latest checkpoint's NextMultiOffset:  0
Latest checkpoint's oldestXID:        480
Latest checkpoint's oldestXID's DB:   1
Latest checkpoint's oldestActiveXID:  0
Latest checkpoint's oldestMultiXid:   1
Latest checkpoint's oldestMulti's DB: 1
Latest checkpoint's oldestCommitTsXid:0
Latest checkpoint's newestCommitTsXid:0
Time of latest checkpoint:            Fri 03 Mar 2023 05:50:19 PM CST
Fake LSN counter for unlogged rels:   0/3E8
Minimum recovery ending location:     0/0
Min recovery ending loc's timeline:   0
Backup start location:                0/0
Backup end location:                  0/0
End-of-backup record required:        no
wal_level setting:                    replica  #wal级别
wal_log_hints setting:                off
max_connections setting:              100    #最大连接数
max_worker_processes setting:         8
max_wal_senders setting:              10
max_prepared_xacts setting:           0
max_locks_per_xact setting:           64
track_commit_timestamp setting:       off
Maximum data alignment:               8
Database block size:                  8192      # 数据块大小
Blocks per segment of large relation: 131072
WAL block size:                       8192     #wal 数据块大小
Bytes per WAL segment:                16777216     #单个wal大小
Maximum length of identifiers:        64
Maximum columns in an index:          32
Maximum size of a TOAST chunk:        1996
Size of a large-object chunk:         2048
Date/time type storage:               64-bit integers
Float4 argument passing:              by value
Float8 argument passing:              by value
Data page checksum version:           0
Mock authentication nonce:            7f551d2130b3be2b0ffaf3de1458ddf44e14805868ea1c61b7c7cfaca2bf9565

```



### 7.8 数据文件

```
pg中，每个索引、每个表都是一个单独的文件，pg中称为page（也称为段）,默认是每个大于1G的page会被分割。例如某个表有200g的大小，那么会被分割为200个文件存储
```

```
select relfilenode from pg_class where relname='pg_statistic';

select pg_relation_filepath('pg_statistic');

show data_directory;

postgres=# exit;
[postgres@localhost ~]$ ls -al /pgdata/12/data/base/13593/2619;

```

![image-20230307143444233](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071434289.png)



![image-20230307145121648](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071451710.png)



### 7.9 Online WAL日志（redo）

关于Online WAL日志

```
即Write Ahead Log预写式日志,简称wal日志,相当于oracle中的redo日志。
pg中wal日志是动态切换,单个wal日志写满继续写下一个wal日志，连续不断生成wal日志。
这个日志存在的目的，是保证奔溃后的安全。但也存在日志膨胀问题
```

设置Online WAL日志

提供以下参数设置

```
max_wal_size=1GB
min_wal_size=80M
```

![image-20230307145804302](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071458387.png)

wal位置

wal在$PGDATA/pg_wal下

```
[postgres@localhost ~]$ cd /pgdata/12/data
[postgres@localhost data]$ cd pg_wal/
[postgres@localhost pg_wal]$ ll
total 16384
-rw-------. 1 postgres postgres 16777216 Mar  7 14:30 000000010000000000000001
drwx------. 2 postgres postgres        6 Mar  1 19:03 archive_status

```

wal命名格式

![image-20230307150133343](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071501401.png)

查看wal时间

```
select pg_walfile_name(pg_current_wal_lsn());   --查看当前wal日志文件

select pg_current_wal_lsn();    --查看当前wal日志

select * from pg_ls_waldir() order by modification asc; --查看所有wal日志

select current_timestamp;    --查看当前时间

```

切换日志

```
select pg_switch_wal();  --日志切换
```

查看具体内容

```
pg_waldump  $PGDATA/pg_wal/000000010000000000000002  --查看日志文件内容 （）
```



### 7.10 ARCH WAL日志（归档日志）

![image-20230307150518983](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303071505082.png)

配置参数

```
cd /pgdata/12/data
vi postgresql.conf

wal_level = replica

archive_mode = on

archive_command = 'test ! -f /archive/%f && cp %p /archive/%f'

# 记得要提前创建文件夹
mkdir /archive
chown -R postgres. /archive
```

![image-20230307231231810](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072312871.png)

![image-20230307231431404](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072314460.png)

![image-20230308225706664](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303082257732.png)

修改完参数之后重启数据库生效

```
pg_ctl restart 
```

查看归档日志

```

select pg_switch_wal();
```

## 8 备份恢复

### 8.1 需要备份什么？

```
数据
归档日志
```

### 8.2 备份方式

#### 8.2.1 逻辑导出工具

- 用到pg_dump和pg_dumpall两个备份工具，这两个工具不会产生文件系统级别的备份，并且不能用于连续归档方案。尽管如此，在很多需要高可靠性的情况下，它是首选的备份技术。

- `pg_dump`是一个普通的PostgreSQL客户端应用，可以访问该数据库的远端主机上进行备份工作。
- `pg_dump`每次只转储一个数据库，而且它不会转储关于角色或表空间（因为它们是集簇范围的）的信息。

```
pg_dump

pg_dumpall
```

![image-20230307220344824](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072203004.png)

导出命令

```
pg_dump -d postgres >/home/jiang/backup.sql;
```

导入命令

```
psql </tmp/backup.sql
```

![image-20230307220613424](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072206478.png)

对于非常大型的数据库，可以将将split配合其他两种方法(gizip,并行度)之一进行使用。

```
# 处理大型数据库时,可以压缩存储
    pg_dump dbname | gzip > filename.gz
    
    # 此时的恢复:
        gunzip -c filename.gz | psql dbname
        # 或者
        cat filename.gz | gunzip | psql dbname
 
# 大型数据库，分割存储
    # 让每一块的大小为1兆字节
        pg_dump dbname | split -b 1m - filename
   
    # 恢复
        cat filename* | psql dbname
 
# 自定义转储格式
    # 如果PostgreSQL所在的系统上安装了zlib压缩库，
    # 自定义转储格式将在写出数据到输出文件时对其压缩。这
    # 将产生和使用gzip时差不多大小的转储文件，
    # 但是这种方式的一个优势是其中的表可以被有选择地恢复。
    pg_dump -Fc dbname > filename
 
    # 恢复
    # 自定义格式的转储不是psql的脚本，只能通过pg_restore恢复
    pg_restore -d dbname filename
 
# 使用并行模式转储
    # 使用-j参数控制并行度，并行转储只支持“目录”归档格式
    pg_dump -j num -F d -f out.dir dbname
 
#目录格式备份：
pg_dump -h localhost -p 5432 -U someuser -F d -f /somepath/a_directory mydb
 
# 从9.3版本开始支持并行备份选项--jobs (-j),
# 此选项只有在按目录格式进行备份时才会生效，每个写线程只负责写一个单独的文件，
# 因此一定是输出结果为多个独立的文件时才可以并行。
pg_dump -h localhost -p 5432 -U someuser -j 3 -Fd -f /somepath/a_directory mydb
```

pg_dumpall命令

```
pg_dumpall备份一个给定集簇中的每一个数据库，并且也保留了集簇范围的数据，如角色和表空间定义。
pg_dump只备份数据库集群中的某个数据库的数据，它不会导出角色和表空间相关的信息，因为这些信息是整个数据库集群共用的，不属于某个单独的数据库。pg_dumpall，对集簇中的每个数据库调用pg_dump来完成该工作,还会还转储对所有数据库公用的全局对象（pg_dump不保存这些对象）。
pg_dumpall工作时会发出命令重新创建角色、表空间和空数据库，接着为每一个数据库pg_dump。这意味着每个数据库自身是一致的，但是不同数据库的快照并不同步。

集簇范围的数据可以使用pg_dumpall的–globals-only选项来单独转储。

```

建议

```
建议每天对角色和表空间定义等全局对象进行备份，但不建议每天使用pg_dumpall来备份全库数据，因为pg_dumpall仅支持导出为SQL文本格式，而使用这种庞大的SQL文本备份来进行全库级别的数据库恢复时及其耗时的，所以一般只建议使用pg_dumpall来备份全局对象而非全库数据。
pg_dumpall可实现仅备份角色和表空间定义：
pg_dumpall -h localhost -U postgres --port=5432 -f myglobals.sql --globals-only
如果仅需备份角色定义而无需备份表空间，添加--roles-only选项：
pg_dumpall -h localhost -U postgres --port=5432 -f myroles.sql --roles-only 

```



#### 8.2.2 物理备份工具

连续归档

```
pg_basebackup
```

该方式的策略是把一个文件系统级别的全量备份和WAL(预写式日志)级别的增量备份结合起来。当需要恢复时，先恢复文件系统级别的备份，然后重放备份的WAL文件，把系统恢复到之前的某个状态。

在任何时间，PostgreSQL在数据集簇目录的pg_wal/子目录下都保持有一个预写式日志（WAL）。这个日志存在的目的是为了保证崩溃后的安全：如果系统崩溃，可以“重放”从最后一次检查点以来的日志项来恢复数据库的一致性。

这种备份有显著的优点：

- 不需要一个完美的一致的文件系统备份作为开始点。备份中的任何内部不一致性将通过日志重放来修正。
- 可以结合一个无穷长的WAL文件序列用于重放，可以通过简单地归档WAL文件来达到连续备份。
- 不需要重放WAL项一直到最后。可以在任何点停止重放，并使数据库恢复到当时的一致状态。
- 可以连续地将一系列WAL文件输送给另一台已经载入了相同基础备份文件的机器，得到一个实时的热备份系统。
  

### 8.3 pg_basebackup物理备份工具应用

#### 8.3.1备份

```
# 备份的目录必须提前创建好，并且是空的
pg_basebackup -D /pgdata/pg_backup/ -Ft -Pv -Upostgres -h 192.168.133.118 -p 1921 -R
```

![image-20230307222808598](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072228652.png)

修改pg_hba.conf

![image-20230307222745559](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072227615.png)

重启

```
pg_ctl restart
```

开始备份

```
pg_basebackup -D /pgdata/pg_backup/ -Ft -Pv -Upostgres -h 192.168.133.118 -p 1921 -R
```

![image-20230307222950585](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072229639.png)

备份成功

![image-20230307223107413](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072231460.png)

模拟删除数据

```
[postgres@localhost pg_backup]$ pg_ctl stop -mi
waiting for server to shut down....2023-03-07 22:33:01.551 CST [1860] LOG:  received immediate shutdown request
2023-03-07 22:33:01.554 CST [1860] LOG:  database system is shut down
 done
server stopped
[postgres@localhost pg_backup]$ rm -rf /pgdata/12/data/*
[postgres@localhost pg_backup]$ rm -rf /archive/*
```



#### 8.3.2恢复

```
tar xf base.tar -C /pgdata/12/data/

# 解压缩归档日志
tar xf pg_wal.tar -C /archive/

```

![image-20230307225046569](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072250630.png)

修改配置文件postgresql.auto.conf

```
# 追加
restore_command = 'cp /archive/%f %p'
recovery_target = 'immediate'

# 执行命令（可选操作）
touch /pgdata/12/data/recovery.signal

# 停止继续恢复
select pg_wal_replay_resume();

```

![image-20230307230034845](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303072300907.png)

![image-20230309125338387](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091253492.png)

### 8.4 PITR实战应用

#### 8.4.1 场景介绍

![image-20230309125313028](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091253224.png)

```
# 先创建一个数据库pit
postgres=# create database pit;
CREATE DATABASE
postgres=# \c pit;
Password: 
You are now connected to database "pit" as user "postgres".

pit=# create table tl (id int);
CREATE TABLE
pit=# insert into tl values(1);
INSERT 0 1
pit=# insert into tl values(2);
INSERT 0 1
pit=# insert into tl values(3);

#删除原来的备份
[postgres@localhost ~]$ rm -rf /pgdata/pg_backup/*

# 重新发起一个备份
pg_basebackup -D /pgdata/pg_backup/ -Ft -Pv -Upostgres -h 192.168.133.118 -p 1921 -R


# 再次新建一个表t2
pit=# create table t2(id int);
CREATE TABLE
pit=# insert into t2 values(1);
INSERT 0 1
pit=# insert into t2 values(2);

#删除库
postgres=# drop database pit;
DROP DATABASE



```

观察一下归档日志

![image-20230309132426084](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091324141.png)

```
postgres=# select pg_switch_wal();
 pg_switch_wal 
---------------
 0/60120B8
(1 row)

```

模拟停止数据库

```
[postgres@localhost archive]$ pg_ctl stop -mf
waiting for server to shut down.... done
server stopped
[postgres@localhost archive]$ rm -rf /pgdata/12/data/*
```

开始恢复

```

[postgres@localhost archive]$ cd /pgdata/pg_backup/
[postgres@localhost pg_backup]$ ll
total 48956
-rw-------. 1 postgres postgres 33347072 Mar  9 13:13 base.tar
-rw-------. 1 postgres postgres 16780800 Mar  9 13:13 pg_wal.tar
[postgres@localhost pg_backup]$ tar xf base.tar -C /pgdata/12/data/
[postgres@localhost pg_backup]$ tar xf pg_wal.tar  -C /archive/
```

![image-20230309133305065](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091333121.png)

修改配置文件

```
[postgres@localhost pg_backup]$ cd $PGDATA
[postgres@localhost data]$ vi postgresql.auto.conf

```

![image-20230309133430208](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091334271.png)

配置文件里，指定恢复时间点

![image-20230309133940469](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091339532.png)

备份的归档日志，解压缩里面

![image-20230309134052634](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091340693.png)

查看归档日志里

```
[postgres@localhost data]$ cd /archive/
[postgres@localhost archive]$ ll
```

我们drop表的归档日志位置如下

![image-20230309134527347](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091345404.png)

```
[postgres@localhost archive]$ pg_waldump 000000020000000000000006
```

日志内容里，有记录

![image-20230309134448286](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091344362.png)

如果要恢复到drop之前，所以ID号在之前

![image-20230309134655448](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091346522.png)

```
[postgres@localhost archive]$ cd $PGDATA
[postgres@localhost data]$ vi postgresql.auto.conf


# Do not edit this file manually!
# It will be overwritten by the ALTER SYSTEM command.
#primary_conninfo = 'user=postgres password=123456 host=192.168.133.118 port=1921 sslmode=disable sslcompression=0 gssencmode=disable krbsrvname=postgres target_session_attrs=any'
restore_command = 'cp /archive/%f %p'
recovery_target_xid = '494'
#primary_conninfo = 'user=postgres password=123456 host=192.168.133.118 port=1921 sslmode=disable sslcompression=0 gssencmode=disable krbsrvname=postgres target_session_attrs=any'

```

![image-20230309135129328](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091351380.png)

```
[postgres@localhost data]$ pg_ctl start

postgres=# psql -W
postgres-# \c pit;
Password: 
You are now connected to database "pit" as user "postgres".
pit-# \dt;
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 public | t2   | table | postgres
 public | tl   | table | postgres
(2 rows)

```

测试是否恢复成功

![image-20230309135510033](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091355086.png)

观察配置文件里关于归档恢复的说明

```
[postgres@localhost data]$ vi postgresql.conf
```

![image-20230309135656071](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303091356138.png)

小技巧

```
# 做危险操作前，打一个恢复点
select pg_create_restore_point('postgres-before-delete')
```



## 9 Springboot集成

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



## 10 流复制

### 10.1基础环境准备（多实例）

```
hostnamectl set-hostname pg2
```

| 192.168.133.118 | 1921 | Centos7.8 | postgresql | master |
| --------------- | ---- | --------- | ---------- | ------ |
| 192.168.133.120 | 1921 | Centos7.8 | postgresql | standy |

### 10.2 设置配置文件

#### 10.2.1 Master节点

```
# 创建用户
create role replica with replication login password '123456';
alter user replica with password '123456';

# 修改pg_hba.conf文件，加入如下一行：
host    replication     replica              0.0.0.0/0               md5

# 修改配置vi postgresql.conf
wal_level = replica  # 这个是设置主位wal的主机
max_wal_senders = 5 #最多可以有几个流复制连接，差不多有几个从就设置几个
wal_keep_size = 128   #设置流复制保留的最多的xlog数目
wal_sender_timeout = 60s #流复制主机发送数据库的超时时间
max_connections = 200   # 一般查多于写的应用从库的最大连接数要比较大
hot_standby = on  #说明这台机器不仅仅用于数据归档也用于数据查询
max_standby_streaming_delay = 30s  #数据流备份的最大延迟时间
wal_receiver_status_interval = 10s #多久向主机报告一次从的状态，从每次数据复制都会向主报告状态，这里只是设置最长的时间间隔
hot_standby_feedback = on #如果有错误的数据复制，是否向主进行反馈
wal_log_hints = on    # also do full page writes of non-critical updates

```

![image-20230309224522067](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303092245217.png)

![image-20230309224636425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303092246494.png)

```
[postgres@localhost data]$ vi pg_hba.conf
[postgres@localhost data]$ vi postgresql.conf
```

![image-20230309225046808](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303092250862.png)

![image-20230309225239936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303092252989.png)



#### 10.2.2 standy节点

```
# 清空数据和归档
[postgres@pg2 ~]$ rm -rf /pgdata/12/data/*
[postgres@pg2 ~]$ rm -rf /archive/*
[postgres@pg2 pgdata]$ rm -rf /pgdata/pg_backup/*

# 备份主机的数据
[postgres@pg2 pgdata]$ pg_basebackup -D /pgdata/pg_backup/ -Ft -Pv -Upostgres -h 192.168.133.118 -p 1921 -R

# 恢复
[postgres@pg2 pg_backup]$ tar xf base.tar  -C /pgdata/12/data
[postgres@pg2 pg_backup]$ tar xf pg_wal.tar -C /archive/

# 修改配置文件
vi standby.signal
standby_mode = 'on'

# vi postgresql.conf
recovery_target_timeline = 'latest'
max_connections = 120 # 大于等于主节点
hot_standby = on  #说明这台机器不仅用于数据归档，还可以用于数据查询
max_standby_streaming_delay = 30s  #数据流备份的最大延迟时间
wal_receiver_status_interval = 10s #多久向主机报告一次从的状态，从每次数据复制都会向主报告状态，这里只是设置最长的时间间隔
hot_standby_feedback = on #如果有错误的数据复制，是否向主进行反馈
max_wal_senders = 15 #最多可以有几个流复制连接，差不多有几个从就设置几个
logging_collector =on
log_directory = 'pg_log' 
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log' 


#  postgresql.auto.conf
restore_command = 'cp /var/postgre/archive1/%f %p'
primary_conninfo = 'user=postgres password=123456 host=172.16.0.103 port=1921 sslmode=disable sslcompression=0 
gssencmode=disable krbsrvname=postgres target_session_attrs=any'

```

![image-20230310123847739](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20230310123847739.png)

![image-20230310124631437](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101246507.png)

![image-20230310124728548](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101247617.png)

启动

```
pg_ctl start
```



#### 10.2.3 监控状态

主库 

```
Select pid,state,client_addr,sync_state,sync_priority from pg_stat_replication;
```

![image-20230310125655869](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101256917.png)

备库

```
psql -c "\x" -c "select * from pg_stat_wal_receiver;"
```

![image-20230310125752318](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101257380.png)

测试：

在主服务器新建一个数据库，然后在副本上查看。

```
postgres=# create database testjiang;
CREATE DATABASE
```

副本上已经有这个库了

```
[postgres@pg2 data]$ psql -W
Password: 
psql (12.6)
Type "help" for help.

postgres=# \l

```

![image-20230310130316595](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202303101303644.png)
