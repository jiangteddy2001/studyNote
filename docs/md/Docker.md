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

5.1 部署中间件Redis



5.2 -Dockerfile



5.3 启动容器