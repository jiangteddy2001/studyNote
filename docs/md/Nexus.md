# Nexus

## 1 简介

nexus的全称是Nexus Repository Manager，是Sonatype公司的一个产品。它是一个强大的仓库管理器，极大地简化了内部仓库的维护和外部仓库的访问。主要用来搭建公司内部的maven私服。但它的功能不仅仅是创建maven私有仓库，还可以作为nuget、docker、npm、bower、pypi、rubygems、git lfs、yum、go、apt等的私有仓库，功能非常强大

Maven 私服是一种特殊的远程仓库，它是架设在局域网内的仓库服务，用来代理位于外部的远程仓库（中央仓库、其他远程公共仓库）。
建立了 Maven 私服后，当局域网内的用户需要某个构件时，会按照如下顺序进行请求和下载。

请求本地仓库，若本地仓库不存在所需构件，则跳转到第 2 步；
请求 Maven 私服，将所需构件下载到本地仓库，若私服中不存在所需构件，则跳转到第 3 步。
请求外部的远程仓库，将所需构件下载并缓存到 Maven 私服，若外部远程仓库不存在所需构件，则 Maven 直接报错。

![image-20221222091941317](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212220919512.png)

## 2 安装

### 2.1下载

下载地址

https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-2.14.5-02-bundle.tar.gz

下载完毕后解压缩

```ruby
tar -zvxf nexus-2.14.5-02-bundle.tar.gz
```

```ruby
mv nexus-2.14.5-02 /usr/local/nexus
```

### 2.2配置Nexus

 nexus默认使用的是8081端口，如果不想使用默认的8081端口的话，可以使用以下的命令进行修改

vi /usr/local/nexus/conf/nexus.properties 



### 2.3 配置自启动

```
vi /etc/init.d/nexus
```

```
#!/bin/bash    
#chkconfig:2345 20 90    
#description:nexus    
#processname:nexus    
export NEXUS_HOME=/usr/local/nexus/
case $1 in    
        start) su root $NEXUS_HOME/bin/nexus start;;    
        stop) su root $NEXUS_HOME/bin/nexus stop;;    
        status) su root $NEXUS_HOME/bin/nexus status;;    
        restart) su root $NEXUS_HOME/bin/nexus restart;;    
        dump ) su root $NEXUS_HOME/bin/nexus dump ;; 
        console ) su root $NEXUS_HOME/bin/nexus console ;;          
        *) echo "require console | start | stop | restart | status | dump " ;;    
esac
```

![image-20221222165355392](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212221654476.png)

chmod 744 /etc/init.d/nexus

解压后进入bin目录，修改nexus，将RUN_AS_USER=root

修改之前记得备份

![image-20221222165726463](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212221657522.png)

执行以下命令进行启动、停止 和 重启nexus服务

```
#启动
service nexus start

#停止
service nexus stop

#重启
service nexus restart

#查看nexus的状态
service nexus status
```

**设置开机自启动**

```
#向chkconfig添加 nexus 服务的管理
chkconfig --add nexus

#设置nexus服务自启动
chkconfig nexus on

#关闭nexus服务自启动
chkconfig nexus off

#删除nexus服务在chkconfig上的管理
chkconfig –del nexus
```



## 3 配置

安装完毕后，进入nexus首页后，点击右上角的"Log In"按钮，输入用户名"admin"，默认密码"admin123"

![image-20221222223430300](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212222234212.png)

目前nexus已经开始正常工作了，接下来我们开始使用私有仓库；



## 4 上传私有仓库

设置密码123456

![image-20221222230640010](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212222306069.png)

设置为允许上传release的jar包，操作如下图

![image-20221222231027582](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212222310665.png)

置为允许上传snapshots的jar包，操作如下图：

![image-20221222231122411](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212222311494.png)



修改当前电脑的maven配置文件

打开当前电脑的maven配置文件apache-maven-3.3.3\conf\settings.xml，添加如下信息：

1. 找到servers节点，添加如下两个子节点：

```xml
<server>   
	<id>bolingcavalry-nexus-releases</id>   
    <username>deployment</username>   
    <password>admin123</password>   
</server>
<server>   
	<id>bolingcavalry-nexus-snapshots</id>   
    <username>deployment</username>   
    <password>admin123</password>   
</server>
```

以上配置了两个server的用户名和密码信息 ，接下来需要身份验证的时候，都可以通过bolingcavalry-nexus-releases和bolingcavalry-nexus-snapshots这两个id来使用对应的用户名和密码；
2. 找到mirrors节点，添加如下两个子节点：

```xml
  <mirror>     
  	<id>bolingcavalry-nexus-releases</id>     
      <mirrorOf>*</mirrorOf>     
      <url>http://192.168.119.155:8081/nexus/content/groups/public</url>     
  </mirror>    
  <mirror>     
      <id>bolingcavalry-nexus-snapshots</id>     
      <mirrorOf>*</mirrorOf>     
      <url>http://192.168.119.155:8081/nexus/content/groups/public-snapshots</url>     
  </mirror>     
```

3.  找到profile节点下面的repositories节点，添加如下两个子节点：

```xml
  <repository>  
    <id>bolingcavalry-nexus-releases</id>  
    <url>http://nexus-releases</url>  
    <releases><enabled>true</enabled></releases>  
    <snapshots><enabled>true</enabled></snapshots>
  </repository>
  <repository>  
    <id>bolingcavalry-nexus-snapshots</id>  
    <url>http://nexus-snapshots</url>  
    <releases><enabled>true</enabled></releases>  
     <snapshots><enabled>true</enabled></snapshots>  
  </repository>
```

  以上配置release和snapshots的部署时，使用哪个仓库和server的配置信息；

  4. 找到profile节点下面的pluginRepositories节点，添加如下两个子节点：

     ```xml
     <pluginRepository>  
     	<id>bolingcavalry-nexus-releases</id>  
         <url>http://nexus-releases</url>  
         <releases><enabled>true</enabled></releases>  
         <snapshots><enabled>true</enabled></snapshots>  
     </pluginRepository>  
     <pluginRepository>  
     	<id>bolingcavalry-nexus-snapshots</id>  
     	<url>http://nexus-snapshots</url>  
     	<releases><enabled>true</enabled></releases>  
     	<snapshots><enabled>true</enabled></snapshots>  
     </pluginRepository>
     ```

     以上配置release和snapshots的部署时的插件仓库配置；

  5. 然后创建maven项目。POM文件加入


```xml
<distributionManagement>
        <repository>
            <id>bolingcavalry-nexus-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://192.168.133.102:8081/nexus/content/repositories/releases</url>
        </repository>
        <snapshotRepository>
            <id>bolingcavalry-nexus-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://192.168.133.102:8081/nexus/content/repositories/snapshots</url>
        </snapshotRepository>
    </distributionManagement>
```

