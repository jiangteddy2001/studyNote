# Docker

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

  

## 3 安装Docker

### 3.1 离线安装

首先我们去官网下载docker的安装包

docker官网地址： https://download.docker.com/linux/static/stable/

在下载列表中选择一个你要的docker版本下载本地

选择.tgz的包下载

下载完成后上传到服务器的用户目录下

然后执行命令：

tar -zxvf docker-20.10.19.tgz
cp docker/* /usr/local/etc/docker/

增加系统启动服务

vim /etc/systemd/system/docker.service

chmod +x /etc/systemd/system/docker.service
systemctl daemon-reload

开启启动设置

systemctl enable docker.service

systemctl start docker

然后执行docker -v 查看docker版本

### 3.2 yum安装

Install Docker Engine on CentOS

3.2.1移除旧的Docker包

sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine



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

systemctl enable docker --now



3.2.5 配置加速

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

 docker rm  容器id/名字
  docker rm -f mynginx   #强制删除正在运行中的

 #停止容器
  docker stop 容器id/名字
  #再次启动
  docker start 容器id/名字

 #应用开机自启
  docker update 容器id/名字 --restart=always

 

###  4.3 修改容器内容

 1）容器内部修改

 docker exec -it 容器id  /bin/bash

 2）挂载数据到外部修改

 docker run --name=mynginx   \
  -d  --restart=always \
  -p  88:80 -v /data/html:/usr/share/nginx/html:ro  \
  nginx

 修改页面只需要去主机的 /data/html/



### 4.4 提交改变

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

docker commit -a "leifengyang"  -m "首页变化" 341d81f7504f guignginx:v1.0

打包

docker save -o abc.tar guignginx:v1.0

别的机器加载这个镜像

docker load -i abc.tar



### 4.5 推送远程仓库

推送镜像到docker hub；应用市场
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

docker run -v /home/jiang/redis/redis.conf:/etc/redis/redis.conf \
-v /home/jiang/redis/data:/data \
-d --name myredis \
-p 6379:6379 \
redis:latest  redis-server /etc/redis/redis.conf



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



## 6 Docker Compose

### 6.1 概念

是一个用来定义和运行复杂应用的Docker工具。一个使用Docker容器的应用，通常由多个容器组成。使用Docker Compose不再需要使用shell脚本来启动容器。 



### 6.2 安装

离线安装方法：

文件下载地下：https://github.com/docker/compose/releases

![image-20221031145325240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111220481.png)



改名为docker-compose 复制到 /usr/local/bin/下

cp -f docker-compose-linux-x86_64 /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

结束，输入命令docker-compose --version验证成功



### 6.3 使用Compose 

1. 定义应用程序的Dockerfile文件，以便在任何地方都能复制；
2. 定义docker-compose.yml 文件，以便他们可以在隔离环境中一起运行；
3. 运行 docker compose up 启动整个应用，也可以使用docker-compose up 运行；

简单的例子docker-compose.yml 

version: "3.9"  # 从 v1.27.0 之后可选
services:
  web:
    build: .
    ports:

      - "5000:5000"
        volumes:
            - .:/code
                  - logvolume01:/var/log
            links:
                  - redis
        redis:
                image: redis
      volumes:
        logvolume01: {}

## 7 Docker Swarm

Swarm是[Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020)公司推出的用来管理docker集群的平台，几乎全部用GO语言来完成的开发的，代码开源在https://github.com/docker/swarm

简单理解就是多台服务器搭建一个docker集群，每个服务器就是集群中的一个节点。

### 7.1 组件

架构图

![image-20221211094701561](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212110947822.png)

在结构图可以看出 Docker Client使用Swarm对 集群(Cluster)进行调度使用。



### 7.2概念

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

#初始化集群
docker swarm init
#查看工作节点的 token
docker swarm join-token worker
#查看管理节点的 token
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

服务日志排查

docker service logs

服务扩容

docker service scale 



## 9 Docker  Stack

### 9.1 概念

Docker Stack 则适用于大规模场景和生产环境，Stack 能够在单个声明文件中定义复杂的多服务应用。Stack 还提供了简单的方式来部署应用并管理其完整的生命周期：初始化部署 -> 健康检查 -> 扩容 -> 更新 -> 回滚，以及其他功能！

从体系结构上来讲，Stack 位于 Docker 应用层级的最顶端。Stack 基于服务进行构建，而服务又基于容器结构如下图：

![image-20221211100815216](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212111008278.png)

### 9.2常用命令

//用于根据 Stack 文件（通常是 docker-compose.yml）部署和更新 Stack 服务的命令
docker stsack deploy -c docker-compose.yml test

//会列出 Swarm 集群中的全部 Stack，包括每个 Stack 拥有多少服务
docker stack ls

// 列出某个已经部署的 Stack 相关详情。该命令支持 Stack 名称作为其主要参数，列举了服务副本在节点的分布情况，以及期望状态和当前状态
docker stack ps

//命令用于从 Swarm 集群中移除 Stack。移除操作执行前并不会进行二次确认
docker stack rm

docker-compose、docker stack工具命令均可以使用version3 编写的docker-compose.yml 文件上，版本3以前的docker-compose.yml 文件可继续使用docker-compose工具，若是你仅须要一个能操做多个容器的工具，依旧可使用docker-compose工具。

docker stack几乎能作docker-compose全部的事情 （生产部署docker stack表现还更好），若是打算使用docker swarm集群编排，可迁移到docker stack。



## 10 Docker安全

### 10.1Docker 架构缺陷与安全机制

1、容器之间的局域网攻击
主机上的容器之间可以构成局域网，因此针对局域网的 ARP 欺骗、嗅探、广播风暴等攻 击方式便可以用上。
所以，在一个主机上部署多个容器需要合理的配置网络，设置 iptable 规则。

2、DDoS 攻击耗尽资源
Cgroups 安全机制就是要防止此类攻击的，不要为单一的容器分配过多的资源即可避免此类问题。

3、有漏洞的系统调用
Docker与虚拟机的一个重要的区别就是Docker与宿主机共用一个操作系统内核。
一旦宿主内核存在可以越权或者提权漏洞，尽管Docker使用普通用户执行，在容器被入侵时，攻击者还可以利用内核漏洞跳到宿主机做更多的事情。

4、共享root用户权限
如果以 root 用户权限运行容器，容器内的 root 用户也就拥有了宿主机的root权限。



### 10.2 Docker 安全基线标准

1、内核级别
（1）及时更新内核。
（2）User NameSpace（容器内的 root 权限在容器之外处于非高权限状态）。
（3）Cgroups（对资源的配额和度量）。
（4）SELiux/AppArmor/GRSEC（控制文件访问权限）。
（5）Capability（权限划分）。
（6）Seccomp（限定系统调用）。
（7）禁止将容器的命名空间与宿主机进程命名空间共享。


2、主机级别
（1）为容器创建独立分区。
（2）仅运行必要的服务。
（3）禁止将宿主机上敏感目录（例如：root目录可能导致目录被删，进而导致grub文件丢失无法开机）映射到容器。
（4）对 Docker 守护进程、相关文件和目录进行审计。
（5）设置适当的默认文件描述符数。
（文件描述符：内核（kernel）利用文件描述符（file descriptor）来访问文件。文件描述符是非负整数。
打开现存文件或新建文件时，内核会返回一个文件描述符。读写文件也需要使用文件描述符来指定待读写的文件）
（6）用户权限为 root 的 Docker 相关文件的访问权限应该为 644 或者更低权限（例：600），只有属主才可进行操作。
（7）周期性检查每个主机的容器清单，并清理不必要的容器。


3、网络级别
（1）通过 iptables 设定规则实现禁止或允许容器之间网络流量。
（2）允许 Docker 修改 iptables。
（3）禁止将 Docker 绑定到其他 IP/Port 或者 Unix Socket。
（4）禁止在容器上映射特权端口（例如apache的80和tomcat的8080端口）。
（5）容器上只开放所需要的端口。
（6）禁止在容器上使用主机网络模式（主机网络可能是局域网，可能导致局域网内被攻击）。
（7）若宿主机有多个网卡，将容器进入流量绑定到特定的主机网卡上（独立区域，独立设置安全机制）。

4、镜像级别
（1）创建本地镜像仓库服务器。
（2）镜像中软件都为最新版本。
（3）使用可信镜像文件，并通过安全通道下载。
（4）重新构建镜像而非对容器和镜像打补丁。
（5）合理管理镜像标签，及时移除不再使用的镜像（一定要建项目，要不然打标签也没什么意义）。
（6）使用镜像扫描。
（7）使用镜像签名。


5、容器级别
（1）容器最小化，操作系统镜像最小集。
（2）容器以单一主进程的方式运行。
（3）禁止 privileged 标记使用特权容器（做降权处理，例如：防止到哪都是root权限）。
（4）禁止在容器上运行 ssh 服务（最好用exec）。
（5）以只读的方式挂载容器的根目录系统。
（6）明确定义属于容器的数据盘符。
（7）通过设置 on-failure 限制容器尝试重启的次数，容器反复重启容易丢失数据。
（8）限制在容器中可用的进程树，以防止 fork bomb。（fork炸弹又叫进程炸弹，迅速增长子进程，耗尽系统进程数量）

6、其他设置
（1）定期对宿主机系统及容器进行安全审计。
（2）使用最少资源和最低权限运行容器。
（3）避免在同一宿主机上部署大量容器，维持在一个能够管理的数量（48核心的服务器控制容器在80到100之间）。
（4）监控 Docker 容器的使用，性能以及其他各项指标。
（5）增加实时威胁检测和事件响应功能。
（6）使用中心和远程日志收集服务



### 10.3 安全控制策略

1、 Docker remote api 访问控制

临时更改
docker -d -H uninx:///var/run/docker.sock -H tcp://192.168.222.10:2375
或者永久更改
vim /usr/lib/systemd/system/docker.service
//注释之前的ExecStart，添加下面的ExecStart
//开放本地监听地址和端口
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://192.168.222.10:2375
systemctl daemon-reload
systemctl restart docker
//客户端操作实现远程调用
docker -H tcp://192.168.222.10 images

2、限制流量流向

firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4" source address="192.168.112.0/24" reject"
firewall-cmd --reload

3、镜像安全

Docker 镜像安全扫描，在镜像仓库客户端使用证书认证，对下载的镜像进行检查。
通过与 CVE 数据库同步扫描镜像，一旦发现漏洞则通知用户处理，或者直接阻止镜像继续构建。

如果公司使用的是自己的镜像源，可以跳过此步；否则，至少需要验证 baseimage 的 md5 等特征值，确认一致后再基于 baseimage 进一步构建。
一般情况下，要确保只从受信任的库中获取镜像，并且不建议使用–insecure-registry=[] 参数，推荐使用harbor私有仓库。

4、密钥和数据完整性

Docker-TLS