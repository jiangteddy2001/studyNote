

# Docker

硬件环境

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302061759925.png)

## 1 Docker用途

1.1统一标准

- 应用构建 

- - Java、C++、JavaScript
  - 打成软件包
  - .exe
  - docker build ....   镜像

- 应用分享

- - 所有软件的镜像放到一个指定地方  docker hub
  - 安卓，应用市场

- 应用运行

- - 统一标准的 **镜像**
  - docker run

1.2 资源隔离

- cpu、memory资源隔离与限制
- 访问设备隔离与限制
- 网络隔离与限制
- 用户、用户组隔离限制

## 2 Docker架构

### 2.1架构

![image-20221210094746491](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212100947184.png)

- Docker_Host：

 - 安装Docker的主机

 Docker Daemon：

 - 运行在Docker主机上的Docker后台进程

 Client：

 - 操作Docker主机的客户端（命令行、UI等）

 Registry：

 - 镜像仓库
  - Docker Hub

 Images：

 - 镜像，带环境打包好的程序，可以直接启动运行

 Containers：

 - 容器，由镜像启动起来正在运行中的程序

###   2.2 Docker的隔离技术

如何理解Namespace

Namespace是Linux内核用来隔离内核资源的方式。通过Namespace可以让一些进程只能看到与自己相关的一部分资源，而另外一些进程也只能看到与它们自己相关的资源，这两拨进程根本就感觉不到对方的存在。

目前，Linux 内核实现了6种 Namespace:

![image-20230201205815397](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012058439.png)

隔离原理：

Linux Namespace 是 Linux 提供的一种内核级别环境隔离的方法，因此Linux 提供了多个 API 用来操作 Namespace，它们是 clone()、setns() 和 unshare() 函数，简单介绍一下三个系统调用的功能：

1. clone() : 实现线程的系统调用，用来创建一个新的进程，并可以通过设计上述系统调用参数达到隔离的目的;
2. unshare() : 使某进程脱离某个 namespace;
3. setns() : 把某进程加入到某个 namespace;

总结：

Docker容器是在创建容器进程时，指定了这个进程所需要启用的一组Namespace参数，这样容器就只能看到到当前Namespace所限定的资源、文件、设备、状态，或者配置。而对于宿主机以及其他不相关的程序，它就完全看不到了，因此容器本质上就是一个特殊的进程。



## 3 安装Docker

### 3.1 离线安装

首先我们去官网下载docker的安装包

docker官网地址： https://download.docker.com/linux/static/stable/

在下载列表中选择一个你要的docker版本下载本地

选择.tgz的包下载

下载完成后上传到服务器的用户目录下

然后执行命令：

```
tar -zxvf docker-20.10.19.tgz
cp docker/* /usr/local/etc/docker/
```

增加系统启动服务

```
vim /etc/systemd/system/docker.service

chmod +x /etc/systemd/system/docker.service
systemctl daemon-reload
```

开启启动设置

```
systemctl enable docker.service

systemctl start docker
```

然后执行docker -v 查看docker版本

### 3.2 yum安装

Install Docker Engine on CentOS

3.2.1移除旧的Docker包

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```



3.2.2 配置YUM源

sudo yum install -y yum-utils
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo



3.2.3安装Docker

sudo yum install -y docker-ce docker-ce-cli containerd.io

#以下是在安装k8s的时候使用
yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7  containerd.io-1.4.6

3.2.4 启动

```
systemctl enable docker --now
```



3.2.5 配置加速

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://82m9ar63.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



 ## 4 常用命令

###  4.1 寻找镜像

 去[docker hub](http://hub.docker.com)，找到nginx镜像

 docker pull nginx  #下载最新版

 镜像名:版本名（标签）

 docker pull nginx:1.20.1

 docker pull redis  #下载最新
  docker pull redis:6.2.4

 docker images  #查看所有镜像

 redis = redis:latest

 docker rmi 镜像名:版本号/镜像id

###  4.2 启动容器

 docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

 【docker run  设置项   镜像名  】 镜像启动运行的命令（镜像里面默认有的，一般不会写）

 docker run --name=mynginx   -d  --restart=always -p  88:80   nginx

//-d 表示要长久运行

 //查看正在运行的容器

 docker ps

 //查看所有容器

 docker ps -a

 //删除容器

```
 docker rm  容器id/名字
 docker rm -f mynginx   #强制删除正在运行中的
  
   #停止容器
  docker stop 容器id/名字
  #再次启动
  docker start 容器id/名字
  
   #应用开机自启
  docker update 容器id/名字 --restart=always
```

###  4.3 修改容器内容

 1）容器内部修改

 docker exec -it 容器id  /bin/bash

 2）挂载数据到外部修改

```
 docker run --name=mynginx   \
  -d  --restart=always \
  -p  88:80 -v /data/html:/usr/share/nginx/html:ro  \
  nginx
```

 修改页面只需要去主机的 /data/html/



### 4.4 提交改变

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

docker commit -a "leifengyang"  -m "首页变化" 341d81f7504f guignginx:v1.0

打包

```
docker save -o abc.tar guignginx:v1.0
```

别的机器加载这个镜像

docker load -i abc.tar



### 4.5 推送远程仓库

```
#推送镜像到docker hub；应用市场
docker tag local-image:tagname new-repo:tagname
docker push new-repo:tagname

// 把旧镜像的名字，改成仓库要求的新版名字
docker tag guignginx:v1.0 leifengyang/guignginx:v1.0

// 登录到docker hub
docker login       

docker logout（推送完成镜像后退出）

// 推送
docker push leifengyang/guignginx:v1.0

// 别的机器下载
docker pull leifengyang/guignginx:v1.0
```

### 4.6其他操作

docker logs 容器名/id   排错

docker exec -it 容器id /bin/bash

// docker 经常修改nginx配置文件
docker run -d -p 80:80 \
-v /data/html:/usr/share/nginx/html:ro \
-v /data/conf/nginx.conf:/etc/nginx/nginx.conf \
--name mynginx-02 \
nginx

//把容器指定位置的东西复制出来 
docker cp 5eff66eec7e1:/etc/nginx/nginx.conf  /data/conf/nginx.conf
//把外面的内容复制到容器里面
docker cp  /data/conf/nginx.conf  5eff66eec7e1:/etc/nginx/nginx.conf

## 5 Docker打包应用

### 5.1 部署中间件Redis

```
docker run -v /home/jiang/redis/redis.conf:/etc/redis/redis.conf \
-v /home/jiang/redis/data:/data \
-d --name myredis \
-p 6379:6379 \
redis:latest  redis-server /etc/redis/redis.conf
```



### 5.2 -Dockerfile

部署一个Java应用，然后填写Dockerfile

FROM openjdk:8-jdk-slim
LABEL maintainer=jiangbx

COPY target/*.jar   /app.jar

ENTRYPOINT ["java","-jar","/app.jar"]

把Dockerfile和jar包一起上传到服务器，执行命令：

docker build -t java-demo:v1.0 .



### 5.3 启动容器

docker run -d -p 8080:8080 --name myjava-app java-demo:v1.0

启动后查看启动日志

docker logs --tail=1000 myjava-app

### 5.4 分享镜像

//登录docker hub
docker login

//给旧镜像起名
docker tag java-demo:v1.0  jiangbx/java-demo:v1.0

//推送到docker hub
docker push jiangbx/java-demo:v1.0

//别的机器
docker pull jiangbx/java-demo:v1.0

//别的机器运行
docker run -d -p 8080:8080 --name myjava-app java-demo:v1.0 

### 5.5其他命令

![image-20221214174831940](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212141748732.png)

操作docker的redis

docker exec -it fdb2b3e99e93 redis-cli

查看Docker网络

docker network ls

docker network inspect

docker network inspect bridge

## 6 Docker Compose

### 6.1 概念

 Compose 是 Docker 公司推出的一个工具软件，可以管理多个 Docker 容器组成一个应用。你需要定义一个 YAML 格式的配置文件docker-compose.yml，写好多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器。  容器启动需要有加载顺序，比如 jar包，需要先启动mysql + redis容器。

对 docker 容器集群的快速编排  （容器调用关系，一键启动，一键stop）

就像容器 aplicaitonContext.xml 对bean对象统计集中管理起来

java里有对象， docker 里有容器， docker-compose 就是管理容器。

![image-20230201205952650](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012059723.png)

下载官网：https://docs.docker.com/compose/install/  选择 Linux，翻译成中文照着操作。

查看 docker-compose 版本：docker-compose --version

执行步骤：

三步走：

- 编写Dockerfile定义各个微服务应用并构建出对应的镜像文件
- 使用 docker-compose.yml 定义一个完整业务单元，安排好整体应用中的各个容器服务。
- 最后，执行docker-compose up命令来启动并运行整个应用程序，完成一键部署上线



### 6.2 安装

离线安装方法：

文件下载地下：https://github.com/docker/compose/releases

![image-20221031145325240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111220481.png)



改名为docker-compose 复制到 /usr/local/bin/下

```
cp -f docker-compose-linux-x86_64 /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```

结束，输入命令docker-compose --version验证成功

```
docker-compose --version
```



### 6.3 基本命令

| docker-compose -h                            | 查看帮助                                     |
| -------------------------------------------- | -------------------------------------------- |
| docker-compose up                            | 启动所有docker-compose服务                   |
| docker-compose up -d                         | 启动所有docker-compose服务并后台运行         |
| docker-compose down                          | 停止并删除容器、网络、卷、镜像。             |
| docker-compose exec  yml里面的服务id         | 进入容器实例内部  docker-compose exec        |
| docker-compose yml文件中写的服务id /bin/bash |                                              |
| docker-compose ps                            | 展示当前docker-compose编排过的运行的所有容器 |
| docker-compose top                           | 展示当前docker-compose编排过的容器进程       |
| docker-compose logs  yml里面的服务id         | 查看容器输出日志                             |
| docker-compose config                        | 检查配置                                     |
| docker-compose config -q                     | 检查配置，有问题才有输出                     |
| docker-compose restart                       | 重启服务                                     |
| docker-compose start                         | 启动服务                                     |
| docker-compose stop                          | 停止服务                                     |



### 6.4使用Compose 

1. 定义应用程序的Dockerfile文件，以便在任何地方都能复制；
2. 定义docker-compose.yml 文件，以便他们可以在隔离环境中一起运行；
3. 运行 docker compose up 启动整个应用，也可以使用docker-compose up 运行；

简单的例子docker-compose.yml 

```
version: '3'
services:
  mysql:
    image: mysql:5.7
    container_name: mysql-5.7
    privileged: true
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_USER: jiang
      MYSQL_PASS: 123456
      TZ: Asia/Shanghai
    command:
      --wait_timeout=28800
      --interactive_timeout=28800
      --max_connections=1000
      --default-authentication-plugin=mysql_native_password
    volumes:
      - "/home/jiang/mysql-5.7/data:/var/lib/mysql"
      - "/home/jiang/mysql-5.7/config/my.cnf:/etc/mysql/my.cnf"

```

参数解析

![image-20230201210543364](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012105414.png)

编写MYSQL的配置文件

```
[client]
# 客户端来源数据的默认字符集
default-character-set=utf8mb4

[mysqld]
# 服务端默认字符集
character-set-server=utf8mb4
# 连接层默认字符集
collation-server=utf8mb4_unicode_ci

[mysql]
# 数据库默认字符集
default-character-set=utf8mb4

```

命令

```
# 前台启动
docker-compose -f docker-compose.yml up 

# 守护进程启动
docker-compose -f docker-compose.yml up -d

# 停止
docker-compse -f docker-compose.yml stop

# 停止并删除容器
docker-compose -f docker-compose.yml down


```

启动成功

![image-20230201213252144](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012132196.png)



### 6.5 Docker Compose Yml文件介绍

版本

对于版本没什么介绍的，就是指定使用的版本；

Services

每个Service代表一个Container，与Docker一样，Container可以是从DockerHub中拉取到的镜像，也可以是本地Dockerfile Build的镜像。

image

标明image的ID，这个image ID可以是本地也可以是远程的，如果本地不存在，Docker Compose会尝试pull下来;

```makefile
image: ubuntu
```

build

该参数指定Dockerfile文件的路径，Docker Compose会通过Dockerfile构建并生成镜像，然后使用该镜像;

```bash
build:
  #构建的地址
  context: /usr/local/docker-compose-demo
  dockerfile: Dockerfile
```

ports

暴露端口，指定宿主机到容器的端口映射，或者只指定容器的端口，则表示映射到主机上的随机端口，一般采用主机:容器的形式来映射端口；

```yaml
#暴露端口
ports:
  - 8081:8080/tcp
```

expose

暴露端口，但不需要建立与宿主机的映射，只是会向链接的服务提供；

environment

加入环境变量，可以使用数组或者字典，只有一个key的环境变量可以在运行compose的机器上找到对应的值；

env_file

从一个文件中引入环境变量，该文件可以是一个单独的值或者一个列表，如果同时定义了environment，则environment中的环境变量会重写这些值；

depends_on

定义当前服务启动时，依赖的服务，当前服务会在依赖的服务启动后启动;

```makefile
depends_on: 
  - docker-compose-demo02
  - docker-compose-demo01
```

deploy

该配置项在version 3里才引入，用于指定服务部署和运行时相关的参数；

replicas

指定副本数；

```vbnet
version: '3.4'
services:
  worker:
    image: nginx:latest
    deploy:
      replicas: 6
```

restart_policy

指定重启策略;

```yaml
version: "3.4"
services:
  redis:
    image: redis:latest
    deploy:
      restart_policy:
        condition: on-failure   #重启条件：on-failure, none, any
        delay: 5s   # 等待多长时间尝试重启
        max_attempts: 3 #尝试的次数
        window: 120s    # 在决定重启是否成功之前等待多长时间
```

update_config

定义更新服务的方式，常用于滚动更新;

```yaml
version: '3.4'
services:
  vote:
    image: docker-compose-demo
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2  # 一次更新2个容器
        delay: 10s  # 开始下一组更新之前，等待的时间
        failure_action：pause  # 如果更新失败，执行的动作：continue, rollback, pause，默认为pause
        max_failure_ratio： 20 # 在更新过程中容忍的失败率
        order: stop-first   # 更新时的操作顺序，停止优先（stop-first，先停止旧容器，再启动新容器）还是开始优先（start-first，先启动新容器，再停止旧容器），默认为停止优先，从version 3.4才引入该配置项
```

resources

限制服务资源；

```yaml
version: '3.4'
services:
  redis:
    image: redis:alpine
    deploy:
      resources:
        #限制CPU的使用率为50%内存50M
        limits:
          cpus: '0.50'
          memory: 50M
        #始终保持25%的使用率内存20M
        reservations:
          cpus: '0.25'
          memory: 20M
```

healthcheck

执行健康检查;

```bash
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]   # 用于健康检查的指令
  interval: 1m30s   # 间隔时间
  timeout: 10s  # 超时时间
  retries: 3    # 重试次数
  start_period: 40s # 启动多久后开始检查
```

restart

重启策略;

```avrasm
#默认的重启策略，在任何情况下都不会重启容器
restart: "no"
#容器总是重新启动
restart: always
#退出代码指示失败错误，则该策略会重新启动容器
restart: on-failure
#重新启动容器，除非容器停止
restart: unless-stopped
```

networks

网络类型，可指定容器运行的网络类型;

```yaml
#指定对应的网络
networks:
  - docker-compose-demo-net
  
  
networks:
  docker-compose-demo-net:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.1.0/24
          gateway: 192.168.1.1
```

ipv4_address, ipv6_address

加入网络时，为此服务指定容器的静态 IP 地址；

```yaml
version: "3.9"

services:
  app:
    image: nginx:alpine
    networks:
      app_net:
        ipv4_address: 172.16.238.10
        ipv6_address: 2001:3984:3989::10

networks:
  app_net:
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"
        - subnet: "2001:3984:3989::/64"
```

Networks

网络决定了服务之间以及服务和外界之间如何去通信，在执行docker-compose up的时候，docker会默认创建一个默认网络，创建的服务也会默认的使用这个默认网络。服务和服务之间，可以使用服务的名字进行通信，也可以自己创建网络，并将服务加入到这个网络之中，这样服务之间可以相互通信，而外界不能够与这个网络中的服务通信，可以保持隔离性。

Volumes

挂载主机路径或命名卷，指定为服务的子选项。可以将主机路径挂载为单个服务定义的一部分，无需在顶级volume中定义。如果想在多个服务中重用一个卷，则在顶级volumes key 中定义一个命名卷，将命名卷与服务一起使用。



## 7 Docker Swarm

Swarm是[Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020)公司推出的用来管理docker集群的平台，几乎全部用GO语言来完成的开发的，代码开源在https://github.com/docker/swarm

简单理解就是多台服务器搭建一个docker集群，每个服务器就是集群中的一个节点。

### 7.1 组件

架构图

![image-20221211094701561](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110947822.png)

在结构图可以看出 Docker Client使用Swarm对 集群(Cluster)进行调度使用。

![image-20230201213554154](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20230201213554154.png)

### 7.2 概念

1.Swarm
集群的管理和编排是使用嵌入docker引擎的SwarmKit，可以在docker初始化时启动swarm模式或者加入已存在的swarm

2.Node
一个节点是docker引擎集群的一个实例。您还可以将其视为Docker节点。您可以在单个物理计算机或云服务器上运行一个或多个节点，但生产群集部署通常包括分布在多个物理和云计算机上的Docker节点。
要将应用程序部署到swarm，请将服务定义提交给 管理器节点。管理器节点将称为任务的工作单元分派 给工作节点。
Manager节点还执行维护所需群集状态所需的编排和集群管理功能。Manager节点选择单个领导者来执行编排任务。
工作节点接收并执行从管理器节点分派的任务。默认情况下，管理器节点还将服务作为工作节点运行，但您可以将它们配置为仅运行管理器任务并且是仅管理器节点。代理程序在每个工作程序节点上运行，并报告分配给它的任务。工作节点向管理器节点通知其分配的任务的当前状态，以便管理器可以维持每个工作者的期望状态。

3.Service
一个服务是任务的定义，管理机或工作节点上执行。它是群体系统的中心结构，是用户与群体交互的主要根源。创建服务时，你需要指定要使用的容器镜像。

4.Task
任务是在docekr容器中执行的命令，Manager节点根据指定数量的任务副本分配任务给worker节点

### 7.3使用方法

docker swarm：集群管理，子命令有init, join, leave, update。（docker swarm --help查看帮助）

docker node：节点管理，子命令有accept, promote, demote, inspect, update, tasks, ls, rm。（docker node --help查看帮助）
docker service：服务创建，子命令有create, inspect, update, remove, tasks。（docker service--help查看帮助）

node是加入到swarm集群中的一个docker引擎实体，可以在一台物理机上运行多个node，node分为：
manager nodes，也就是管理节点
worker nodes，也就是工作节点

1）manager node管理节点：执行集群的管理功能，维护集群的状态，选举一个leader节点去执行调度任务。
2）worker node工作节点：接收和执行任务。参与容器集群负载调度，仅用于承载task。
3）service服务：一个服务是工作节点上执行任务的定义。创建一个服务，指定了容器所使用的镜像和容器运行的命令。
   service是运行在worker nodes上的task的描述，service的描述包括使用哪个docker 镜像，以及在使用该镜像的容器中执行什么命令。
4）task任务：一个任务包含了一个容器及其运行的命令。task是service的执行实体，task启动docker容器并在容器中执行任务。 

Node:

![image-20221211094825472](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110948521.png)

Service

![image-20221211094910674](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110949716.png)

任务与调度

![image-20221211094936327](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110949383.png)

服务副本和全局服务

![image-20221211095003357](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110950396.png)

### 7.4调度策略

Swarm在调度(scheduler)节点（leader节点）运行容器的时候，会根据指定的策略来计算最适合运行容器的节点，目前支持的策略有：spread, binpack, random.

1）Random

顾名思义，就是随机选择一个Node来运行容器，一般用作调试用，spread和binpack策略会根据各个节点的可用的CPU, RAM以及正在运行的容器的数量来计算应该运行容器的节点。

2）Spread

在同等条件下，Spread策略会选择运行容器最少的那台节点来运行新的容器，binpack策略会选择运行容器最集中的那台机器来运行新的节点。使用Spread策略会使得容器会均衡的分布在集群中的各个节点上运行，一旦一个节点挂掉了只会损失少部分的容器。 

3）Binpack

Binpack策略最大化的避免容器碎片化，就是说binpack策略尽可能的把还未使用的节点留给需要更大空间的容器运行，尽可能的把容器运行在一个节点上面。 

### 7.5常用命令

docker swarm

```
# 初始化集群
docker swarm init
# 查看工作节点的 token
docker swarm join-token worker
# 查看管理节点的 token
docker swarm join-token manager
#加入集群中
docker swarm join               
#删除swarm节点
docker swarm leave --force    #管理节点，离开节点，强制离开
docker swarm leave               #工作节点，离开节点

docker node

docker node ls                          #查看所有集群节点
docker node ps                          #查看节点中的 Task 任务
docker node rm 节点名称| 节点ID           #删除某个节点
docker node rm -f 节点名称| 节点ID        #删除某个节点（强制删除）
docker node inspect 节点名称| 节点ID      #查看节点详情
docker node demote 节点名称| 节点ID       #节点降级，由管理节点降级为工作节点
docker node promote 节点名称| 节点ID      #节点升级，由工作节点升级为管理节点
docker node update 节点名称| 节点ID       #更新节点

docker service 常用命令

docker service --help                      #帮助文档
docker service create                      #部署服务
docker service ls                          #查看swarm集群正在运行的列表服务
docker service ps nginx                    #列出服务的任务
docker service ps redis                    #列出服务的任务
docker service inspect 服务名称|服务ID       #查看服务详情
docker service logs 服务名称|服务ID          #查看某个服务日志
docker service rm 服务名称|服务ID            #删除某个服务（-f强制删除）
docker service scale 服务名称|服务ID=n       #设置某个服务个数，弹性服务，动态 扩/缩 容
docker service scale nginx=3               #修改服务实例数量为3
docker service update 服务名称|服务ID        #更新某个服务

# 服务日志排查
docker service logs

# 服务扩容
docker service scale 
```



### 7.6 Swarm实战

109作为主节点，105作为子节点

#### 7.6.1 初始化主节点

docker info 这个命令可以查看我们的docker engine 有没有激活swarm模式

```
docker info
```

![image-20230206192559303](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302061925356.png)

inactive表示关闭状态， active表示开户状态

关掉防火墙

```
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld
```

进入109初始化

![image-20230206194420015](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302061944099.png)

```
# 查看docker swarm命令
docker swarm --help

# 域名
hostnamectl set-hostname dockermaster

# 创建docker swarm 集群
docker swarm init --advertise-addr 192.168.133.109
# init后会返回一个token，这个token是其他节点加入集群所需要的token

# 关闭docker swarm 集群
docker swarm leave [--force]
# 单节点关闭会有个报错，这个时候就需要用到 --force进行强制离开

# 查看docker swarm 集群的节点信息
docker node ls

```



![image-20230206194555935](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302061945983.png)

```
Swarm initialized: current node (0qvm9q6ofepx85i46cy80x20r) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-2zh0y3mx7ahgm9s9oi8msf9qcy2qhz7av85mp54b0b2r35jcrg-4miuly8hbl2tw8kyd7qtttjgi 192.168.133.109:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

docker swarm join-token manage 表示加入一个管理节点

docker swarm join 表示加入一个工作节点





#### 7.6.2 工作节点加入主节点

进入105服务器

```
# 域名
hostnamectl set-hostname worker1

# 加入集群
docker swarm join --token SWMTKN-1-2zh0y3mx7ahgm9s9oi8msf9qcy2qhz7av85mp54b0b2r35jcrg-4miuly8hbl2tw8kyd7qtttjgi 192.168.133.109:2377

```

加入成功

![image-20230206200046228](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062000277.png)

查询节点

```
 docker node ls
```



![image-20230206200007307](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062000360.png)



#### 7.6.3 集群部署服务

```
 docker service --help
```

![image-20230206200654042](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062006095.png)

1、创建服务

```
[root@localhost ~]# docker service create --name demo busybox /bin/sh -c "while true; do sleep 3600; done "
53oj98fhb98sruz8bn3us08e7
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged
```

可以查看是否创建成功：

```
[root@localhost ~]# docker service ls
ID             NAME      MODE         REPLICAS   IMAGE            PORTS
53oj98fhb98s   demo      replicated   1/1        busybox:latest
```

可以具体看这个service的内容：

```
[root@localhost ~]# docker service ps demo
ID             NAME      IMAGE            NODE           DESIRED STATE   CURRENT STATE                ERROR     PORTS
funaztjr97gu   demo.1    busybox:latest   dockermaster   Running         Running about a minute ago
```



2、扩容缩容

水平扩容到5

```
[root@localhost ~]# docker service scale demo=5
demo scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converge
```

查看服务是否有5个

```
[root@localhost ~]# docker service ls
ID             NAME      MODE         REPLICAS   IMAGE            PORTS
53oj98fhb98s   demo      replicated   5/5        busybox:latest 
```

再看看具体的容器数量：

```
[root@localhost ~]# docker service ps demo
ID             NAME      IMAGE            NODE           DESIRED STATE   CURRENT STATE           ERROR     PORTS
funaztjr97gu   demo.1    busybox:latest   dockermaster   Running         Running 4 minutes ago             
xtu9j9jh35d3   demo.2    busybox:latest   worker1        Running         Running 2 minutes ago             
je3wu31m1dxt   demo.3    busybox:latest   dockermaster   Running         Running 3 minutes ago             
tkfo2ycyojh5   demo.4    busybox:latest   worker1        Running         Running 3 minutes ago             
9q0jnrk2pr0q   demo.5    busybox:latest   worker1        Running         Running 2 minutes ago 
```

可以看到5个容器在不同的节点上，其中有2个容器在manager节点，有3个在worker节点上

每一个节点都可以自己查看自己目前运行的容器，比如worker-105节点查看自己运行容器的数量

```
docker ps
```

![image-20230206201514040](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062015101.png)

节点带有自动修复功能。删除一个服务后，服务会自动修复。



3、删除服务

```
docker service rm demo
```



#### 7.6.4 Raft协议

Raft协议： 保证大多数节点存活才可以用。 只要>1 ，集群至少大于3台！



#### 7.6.5 锁机制

启用锁

```
docker swarm update --autolock=true
```

命令会输入一个安全解锁码，请确保将解锁码妥善保管在安全的地方！

尝试重启某一个管理节点，会发现管理节点仍然未被允许重新接入集群

执行docker swarm unlock命令来为重启的管理节点解锁Swarm。该命令需要在重启的节点上执行，同时需要提供解锁码。

```
docker swarm unlock
```



#### 7.6.6 网络

创建swarm集群后，系统会默认添加两个网络一个是overlay网络，一个是bridge网络

第一： 东西向流量， 也就是不同的swarm节点上的容器之间如何通信，swarm 通过 overlay网络来解决

第二： 南北向流量， 也就是swarm集群里的容器如何对外访问， 比如互联网这个是 linux bridge + iptables NAT来解决

创建 overlay 网络

```
docker network create -d overlay mynet
```



#### 7.6.7 滚动升级

1、登录到manager节点的主机

2、部署redis service (配置更新延迟参数为：10s)

```
docker service create \
  --replicas 3 \
  --name redis \
  --update-delay 10s \
  redis:3.0.6
```

配置说明：

- 可以在service部署时，配置滚动更新策略
- --update-delay设置每个task之间的更新延迟 （第一个task正确运行后，隔多久，第二个task开始更新），单位可以设置s,m,h [例如：10m30s表示10分钟30秒的延迟]
- 默认情况下，调度器一次更新一个task.可以使用--update-parallelism参数来设置，调度器同时更新task的数量。
- 当进行滚动升级时，当一个task返回RUNNING状态时，调度器调度下一个task,以这样的方式，直到所有的task都更新完成。
- 当更新的过程中，任何的task返回FAILED，调度器就会终止更新。可以通过--update-failure-action这个参数来控制更新失败时的行为。



查看结果

```
 docker service ls
 
 docker service ps redis

```

![image-20230206203710660](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062037724.png)



查看serivce的详细信息

```
docker service inspect --pretty redis
```

```
[root@localhost ~]# docker service inspect --pretty redis

ID:		87iy7kx0u4etdd00dvacdb8a9
Name:		redis
Service Mode:	Replicated
 Replicas:	3
Placement:
UpdateConfig:
 Parallelism:	1
 Delay:		10s
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Update order:      stop-first
RollbackConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Rollback order:    stop-first
ContainerSpec:
 Image:		redis:3.0.6@sha256:6a692a76c2081888b589e26e6ec835743119fe453d67ecf03df7de5b73d69842
 Init:		false
Resources:
Endpoint Mode:	vip

```

重点关注更新配置UpdateConfig

```
UpdateConfig:

Parallelism: 1 #并行更新实例个数

Delay: 10s #task更新延迟

On failure: pause #更新失败时的行为

Monitoring Period: 5s #监控周期

Max failure ratio: 0

Update order: stop-first #更新顺序，先停止task，再更新task
```



使用redis 3.0.7版本镜像更新redis服务

```
docker service update --image redis:3.0.7 redis
```

步骤：

1. 停止第一个task
2. 更新停止的task
3. 启动已经更新好的task
4. 如果更新的这个task成功，返回RUNNING，然后，停下来，等一个--update-delay参数指定的延时周期，然后更新下一个task.
5. 如果在更新的过程中，任何时间，只要返回FAILED状态，暂停更新。

注意！如果**更新失败**了，从哪里可以看到失败的原因？

 只需要 docker service inspect 即可

查看升级过程

```
docker service ps redis
```

![image-20230206204518509](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302062045583.png)

## 9 Docker  Stack

### 9.1 概念

Docker Stack 则适用于大规模场景和生产环境，Stack 能够在单个声明文件中定义复杂的多服务应用。

Docker Stack 部署应用的生命周期：初始化部署 > 健康检查 > 扩容 > 更新 > 回滚。

使用单一声明式文件即可完成部署，即只需要docker-stack.yml文件，使用docker stack deploy命令即可完成部署。

stack 文件其实就是 Docker compose 文件，唯一的要求就是 version 需要为 3.0 或者更高的值。

Stack 完全集成到了 Docker 中，不像 compose 还需要单独安装。


![image-20221211100815216](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111008278.png)

### 9.2 常用命令

```
//用于根据 Stack 文件（通常是 docker-compose.yml）部署和更新 Stack 服务的命令
docker stsack deploy -c docker-compose.yml test

//会列出 Swarm 集群中的全部 Stack，包括每个 Stack 拥有多少服务
docker stack ls

// 列出某个已经部署的 Stack 相关详情。该命令支持 Stack 名称作为其主要参数，列举了服务副本在节点的分布情况，以及期望状态和当前状态
docker stack ps

//命令用于从 Swarm 集群中移除 Stack。移除操作执行前并不会进行二次确认
docker stack rm
```

docker-compose、docker stack工具命令均可以使用version3 编写的docker-compose.yml 文件上，

版本3以前的docker-compose.yml 文件可继续使用docker-compose工具，

若是你仅须要一个能操做多个容器的工具，依旧可使用docker-compose工具。

Docker Stack 与 Docker Compose的区别
docker stack 是swarm mode的一部分, 即使是单机使用, 也需要一个 swarm 节点

docker stack 强化了service的概念 

### 9.3 编排部署

```
# 单机
docker-compose up -d wordpress.yaml
# 集群
docker stack deploy wordpress.yaml

```

```
# docker-compose 文件
version: '3.4'
services:
    mongo:
        image: mongo
        restart: always
        networks:
            - mongo_network
        deploy:
            restart_policy:
                condition: on-failure
            replicas: 2
    mongo-express:
        image: mongo-express
        restart: always
        networks:
            - mongo_network
        ports:
            - target: 8081
              published: 80
              protocol: tcp
              mode: ingress
        environment:
            ME_CONFIG_MONGODB_SERVER: mongo
            ME_CONFIG_MONGODB_PORT: 27017
        deploy:
            restart_policy:
                condition: on-failure
            replicas: 1
networks:
    mongo_network:
        external: true

```



## 10 Docker数据卷

容器数据卷的方式完成数据的持久化，重要资料backup（备份）

将容器内的数据 备份 + 持久化 到本地主机目录。

### 10.1添加容器卷

docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录      镜像名

docker run -it --privileged=true -v /root/01Sofeware/10Docker/host_data:/tmp/docker_data --name=u1 ubuntu

就会进入到 ubuntu 镜像创建的一个容器里，

docker inspect 803b5d9531c0  查看容器挂载信息。



### 10.2 读写规则

上面默认 不加 是可读可写的。

加个 ro（read only），容器里只能读，不能写。



### 10.3 卷的继承和共享

```
docker run -it --privileged=true --volumes-from 父类 --name u2 ubuntu
docker run -it --privileged=true --volumes-from u1 --name u2 ubuntu

```



## 11 Docker网络

启动docker后，产生一个 docker0 的虚拟网桥。

![image-20230201182229663](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302011822768.png)

安装完 docker 默认创建3个网络模式：

docker network ls

### 11.1 容器查看网络

```
docker network ls
```

docker network inspect bridge

### 11.2 安装PING

我们在创建基础容器之后，进入容器，进行编辑配置文件的时候，需要使用ping，但是会出现：

bash: ping: command not found

解决办法：

使用如下命令安装：

```undefined
apt install iputils-ping
```

如果执行错误，先执行一下命令：

```sql
apt-get update
```

执行ping命令

docker exec -it fdb2b3e99e93（容器ID） /bin/bash



### 11.3 网络模式

![image-20230201203255816](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012032866.png)

Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接通信。

网桥docker0创建一对对等虚拟设备接口一个叫veth，另一个叫eth0，成对匹配。

- 整个宿主机的网桥模式都是docker0，类似一个交换机有一堆接口，每个接口叫veth，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫veth pair）；
- 每个容器实例内部也有一块网卡，每个接口叫eth0；
- docker0上面的每个veth匹配某个容器实例内部的eth0，两两配对，一一匹配。

通过上述，将宿主机上的所有容器都连接到这个内部网络上，两个容器在同一个网络下,会从这个网关下各自拿到分配的ip，此时两个容器的网络是互通的。

![image-20230201203348970](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012033023.png)







## 12 Portainer - Docker

Portainer 是一款轻量级的应用，它提供了图形化界面，用于方便地管理Docker环境，包括单机环境和集群环境。

官网：https://docs.portainer.io/v/ce-2.9/start/install/server/docker/linux

### 12.1 安装

docker命令安装：

```
docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

安装完毕后，访问地址：

```
http://192.168.133.109:9000/
```

第一次登录需要创建admin。

![image-20230201200727742](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012007802.png)

密码：admin123

![image-20230201200859817](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012008877.png)



### 12.2 使用

![image-20230201200940419](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012009486.png)

![image-20230201202253884](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012022957.png)

12.3 配置Nginx

拉取镜像

![image-20230201202540183](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012025244.png)

![image-20230201202732962](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012027039.png)



Deploy the container：部署容器。

![image-20230201203007813](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012030860.png)

测试，访问地址：http://192.168.133.109:8080/

![image-20230201203102106](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012031148.png)



## 13 CIG - Docker重量级监控系统

### 13.1 简介

CAdvisor监控收集 + InfluxDB存储数据 + Granfana展示图表

```
docker stats  
#通过docker stats命令可以很方便的看到当前宿主机上所有容器的CPU,内存以及网络流量等数据，一般小公司够用了。。。。
#但是，
#docker stats统计结果只能是当前宿主机的全部容器，数据资料是实时的，没有地方存储、没有健康指标过线预警等功能
```

![image-20230201212927737](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302012129788.png)



### 13.2 安装CIG

```
mkdir cig
cd cig
vim docker-compose.yml

```

```
version: '3.1'
 
 
 
volumes:
 
  grafana_data: {}
 
 
 
services:
 
 influxdb:
 
  image: tutum/influxdb:0.9
 
  restart: always
 
  environment:
 
    - PRE_CREATE_DB=cadvisor
 
  ports:
 
    - "8083:8083"
 
    - "8086:8086"
 
  volumes:
 
    - ./data/influxdb:/data
 
 
 
 cadvisor:
 
  image: google/cadvisor
 
  links:
 
    - influxdb:influxsrv
 
  command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086
 
  restart: always
 
  ports:
 
    - "8080:8080"
 
  volumes:
 
    - /:/rootfs:ro
 
    - /var/run:/var/run:rw
 
    - /sys:/sys:ro
 
    - /var/lib/docker/:/var/lib/docker:ro
 
 
 
 grafana:
 
  user: "104"
 
  image: grafana/grafana
 
  user: "104"
 
  restart: always
 
  links:
 
    - influxdb:influxsrv
 
  ports:
 
    - "3000:3000"
 
  volumes:
 
    - grafana_data:/var/lib/grafana
 
  environment:
 
    - HTTP_USER=admin
 
    - HTTP_PASS=admin
 
    - INFLUXDB_HOST=influxsrv
 
    - INFLUXDB_PORT=8086
 
    - INFLUXDB_NAME=cadvisor
 
    - INFLUXDB_USER=root
 
    - INFLUXDB_PASS=root
```

```

docker-compose config -q #检查配置是否有问题

```

没有任何的报错提示，即正确

![image-20230207142502029](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071425148.png)

分析一下上面配置文件

```
version: '3.1'				#必须是3.0以上才能运行docker-compose
 
volumes:
  grafana_data: {}			#实现了grafana数据的挂载
 
services:					#表示我们要启动的服务，即要docker run的内容，多个实例服务
 influxdb:					#A
  image: tutum/influxdb:0.9
  restart: always
  environment:
    - PRE_CREATE_DB=cadvisor	#预先创建一个数据库，创建一个数据库一样
  ports:
    - "8083:8083"		#对外是8083
    - "8086:8086"		#内部即8086
  volumes:
    - ./data/influxdb:/data	#B 从A-B即influxdb服务，拉取的镜像，安装的环境，暴露的端口，下面的cadvisor，grafana都是一样的
 
 cadvisor:
  image: google/cadvisor
  links:
    - influxdb:influxsrv
  command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086 #这就是相当于mysql选择的那个驱动
  restart: always
  ports:
    - "8080:8080"
  volumes:
    - /:/rootfs:ro	
    - /var/run:/var/run:rw
    - /sys:/sys:ro
    - /var/lib/docker/:/var/lib/docker:ro		#四个容器数据卷
 
 grafana:
  user: "104"
  image: grafana/grafana
  user: "104"
  restart: always							#因为有restart，所以如影随形，随着docker启动，就启动
  links:
    - influxdb:influxsrv
  ports:
    - "3000:3000"
  volumes:
    - grafana_data:/var/lib/grafana
  environment:
    - HTTP_USER=admin
    - HTTP_PASS=admin
    - INFLUXDB_HOST=influxsrv
    - INFLUXDB_PORT=8086
    - INFLUXDB_NAME=cadvisor
    - INFLUXDB_USER=root
    - INFLUXDB_PASS=root
    #千言万语一句话，全部由docker-compose一键部署
```



### 13.3 启动

```
docker-compose up		#前台启动，为了看一下过程
docker-compose up -d 	#后台启动，推荐使用
```



查看启动的服务

```
docker ps
```

![image-20230207151711036](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071517118.png)



### 13.4 测试服务

#### 13.4.1 测试`CAdvisor`**监控服务**

```
http://192.168.133.109:8080/
```

往下拉动，有很多图形化监控。

cadvisor也有基础的图形展现功能，这里主要用它来作数据采集。

![image-20230207151907920](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071519013.png)

![image-20230207151924192](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071519291.png)





#### 13.4.2 测试`influxdb`存储服务

```
http://192.168.133.109:8083/
```

![image-20230207152110156](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071521233.png)





#### 13.4.3 测试`Grafana`**展览服务**

```
http://192.168.133.109:3000/
```

默认账号密码：admin / admin

![image-20230207152145358](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071521637.png)

配置数据源

![image-20230207153340095](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071533167.png)



选择influxDB 

![image-20230207153544090](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071535145.png)

配置InfluxDB

![image-20230207154008668](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071540737.png)

配置数据库账户

![image-20230207154121840](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071541905.png)

![image-20230207154154442](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071541513.png)

点击save & test

![image-20230207154225171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071542246.png)

配置面板

![image-20230207154309498](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071543559.png)

![image-20230207154419473](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071544533.png)

![image-20230207154433130](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071544197.png)

![image-20230207154553700](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071545784.png)

写上名字，然后点击保存 

![image-20230207154640479](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071546536.png)

此时还没有数据

![image-20230207154659232](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071546299.png)

点击edit

![image-20230207154727715](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071547784.png)

选择CPU监控，然后根据容器名称选择一个容器，写上别名保存

![image-20230207155041424](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071550490.png)

![image-20230207155124885](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071551969.png)

配置完成后，我们就可以看到展示的数据 

![image-20230207155157369](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071551493.png)

首页效果

![image-20230207155228641](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302071552741.png)
