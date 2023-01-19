# DevOps

![image-20221224222521427](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242225503.png)

CI 过程即是通过 Jenkins 将代码拉取、构建、制作镜像交给测试人员测试。
持续集成：让软件代码可以持续的集成到主干上，并自动构建和测试。
CD 过程即是通过 Jenkins 将打好标签的发行版本代码拉取、构建、制作镜像交给运维人员部署。
持续交付：让经过持续集成的代码可以进行手动部署。
持续部署：让可以持续交付的代码随时随地的自动化部署

DevOps 的方式可以让公司能够更快地应对更新和市场发展变化，开发可以快速交付，部署也更加稳定。

核心就在于简化 Dev 和 Ops 团队之间的流程，使整体软件开发过程更快速。

DevOps 强调的是高效组织团队之间如何通过自动化的工具协作和沟通来完成软件的生命周期管理，从而更快、更频繁地交付更稳定的软件。

硬件配置如下：

| 虚机IP          | 硬件配置       | 系统      | 已安装                                         | 备注       |
| --------------- | -------------- | --------- | ---------------------------------------------- | ---------- |
| 192.168.133.104 | Cpu:2 内存：4G | Centos7.8 | Docker、Gitlab                                 | git仓库    |
| 192.168.133.113 | Cpu:2 内存：4G | Centos7.8 | Docker、Jenkins、Maven、JDK、Harbor、Sonarqube | devOps     |
| 192.168.133.114 | Cpu:2 内存：4G | Centos7.8 | Kuboard                                        | k8s master |
| 192.168.133.115 | Cpu:2 内存：4G | Centos7.8 | Kuboard                                        | k8s worker |





## 1 Gitlab

### 1.1 拉取gitlab镜像

docker  pull gitlab/gitlab-ce:latest



### 1.2 编辑docker-compose.yml

vi docker-compose.yml

编辑docker-compose.yml内容

```
version: '3.1'
services:
  gitlab:
    image: 'gitlab/gitlab-ce:latest'
    container_name: gitlab
    restart: always
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.133.104:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
    ports:
     - '8929:8929'
        - '2224:22'
    volumes:
        - './config:/etc/gitlab'
              - './logs:/var/log/gitlab'
              - './data:/var/opt/gitlab'
```

执行命令

```
docker-compose up -d
```

### 1.3 访问界面



http://192.168.133.104:8929/

![image-20221224193703599](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212241937587.png)

docker exec -it gitlab bash

进入Docker容器来查看初始密码

![image-20221224194240509](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212241942545.png)



### 1.4 修改用户信息和密码

修改用户信息

![image-20221224194431732](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212241944764.png)



![image-20221224194501838](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212241945885.png)





## 2 Maven

### 2.1 安装maven

tar -zxvf apache-maven-3.6.3-bin.tar.gz

mv apache-maven-3.6.3 /usr/local/

cd /usr/local

mv apache-maven-3.6.3/ maven

### 2.2 修改配置文件

![image-20221224212443146](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242124185.png)

![image-20221224213403819](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242134853.png)





## 3  Jenkins

### 3.1安装

下载Jenkins

docker pull jenkins/jenkins:2.319.1-lts





如果报错：

Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

vi  /etc/docker/daemon.json

添加阿里云

```
{
 "registry-mirrors":["https://6kx4zyno.mirror.aliyuncs.com"]
}
```

执行

systemctl daemon-reload 
systemctl restart docker

编辑docker-compose.yml

```
version: '3.1'
services:
  jenkins:
    restart: always
    image: jenkins/jenkins:2.319.1-lts
    container_name: jenkins
    ports:
      - 9001:8080
            - 50001:50000
        volumes:
            - ./data:/var/jenkins_home/
```

然后docker-compose up -d





![image-20221224225434351](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242254393.png)

如果出错了

执行chmod -R +777 data

docker-compose restart

![image-20221224225727867](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242257901.png)



如果发现8080无法访问，请执行

![image-20221224231000534](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212242310567.png)

http://192.168.133.113:9001

![image-20221226222133708](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262221555.png)

![image-20221226222221401](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262222464.png)

可以改一下默认的下载地址

![image-20221226222632327](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262226369.png)

![image-20221226223517004](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262235087.png)

![image-20221226223652576](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262236616.png)

插件管理

搜索Git Parameter

![image-20221226223800363](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262238400.png)

![image-20221226223946831](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262239859.png)

### 3.2配置JENKINS

把maven、jdk移动

```
cd /usr/local/docker/jenkins_docker/data

mv /usr/local/maven/ ./

mv /usr/java/jdk1.8.0_51 ./
```



然后全局配置

![image-20230103231643917](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032316954.png)



![image-20230103232247917](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032322966.png)

![image-20230103232508007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032325056.png)

查看插件是否安装完毕

![image-20230103232633640](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032326674.png)

点击系统配置

![image-20230103232732522](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032327561.png)



拉到最底部

![image-20230103232759776](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032327837.png)

指定目标服务器地址和目录

![image-20230104111143217](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041111192.png)

然后点击高级，增加密码

![image-20230104111337388](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041113440.png)

记得要提前创建好目录

![image-20230104111447312](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041114354.png)

测试连接，成功

![image-20230104111610254](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041116310.png)





### 3.3 Jenkins 实现基础的拉取操作

1、创建一个springboot项目

2、gitlab建立仓库

![image-20230103222331561](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032223842.png)

然后把代码上传到仓库

![image-20230103223403260](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032234312.png)

3、建立任务

进入JENKINS新建一个任务

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032245110.png)

![image-20230103224633169](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032246231.png)

添加凭据

![image-20230103225854832](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032258892.png)

![image-20230103225926081](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032259123.png)

4、MAVEN任务构建

调用maven

![image-20230103230436546](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301032304586.png)

然后点击立即构建

如果报错：

```
ERROR: Couldn't find any revision to build. Verify the repository and branch configuration ``for` `this` `job.
Finished: FAILURE
```

请检查git配置

把master修改为main就不会报错了

![image-20230104113333603](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041133661.png)

报错：

```
FATAL: command execution failed
java.io.IOException: error=2, No such file or directory
	at java.base/java.lang.ProcessImpl.forkAndExec(Native Method)
	at java.base/java.lang.ProcessImpl.<init>(ProcessImpl.java:340)
	at java.base/java.lang.ProcessImpl.start(ProcessImpl.java:271)
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1107)
Caused: java.io.IOException: Cannot run program "mvn" (in directory "/var/jenkins_home/workspace/mytest"): error=2, No such file or directory
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1128)
	at java.base/java.lang.ProcessBuilder.start(ProcessBuilder.java:1071)
	at hudson.Proc$LocalProc.<init>(Proc.java:252)
	at hudson.Proc$LocalProc.<init>(Proc.java:221)
	at hudson.Launcher$LocalLauncher.launch(Launcher.java:995)
	at hudson.Launcher$ProcStarter.start(Launcher.java:507)
	at hudson.Launcher$ProcStarter.join(Launcher.java:518)
	at hudson.tasks.Maven.perform(Maven.java:368)
	at hudson.tasks.BuildStepMonitor$1.perform(BuildStepMonitor.java:20)
	at hudson.model.AbstractBuild$AbstractBuildExecution.perform(AbstractBuild.java:806)
	at hudson.model.Build$BuildExecution.build(Build.java:198)
	at hudson.model.Build$BuildExecution.doRun(Build.java:163)
	at hudson.model.AbstractBuild$AbstractBuildExecution.run(AbstractBuild.java:514)
	at hudson.model.Run.execute(Run.java:1888)
	at hudson.model.FreeStyleBuild.run(FreeStyleBuild.java:43)
	at hudson.model.ResourceController.execute(ResourceController.java:99)
	at hudson.model.Executor.run(Executor.java:432)
Build step 'Invoke top-level Maven targets' marked build as failure
Finished: FAILURE
```

解决办法：

![image-20230104115537793](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041155845.png)

“构建”一节，选了“invoke top-level Maven tagets”之后。Maven Version属性里，有个迷惑人的默认值"（Default）"。把它改成我们自定义的maven

![image-20230104120436977](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041204027.png)

编译成功后，在工作区多了一个target文件夹

![image-20230104120612347](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041206381.png)

5、构建后发布到服务器

增加构建后操作：

![image-20230104120728301](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041207351.png)

![image-20230104120853816](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041208878.png)

编辑配置

![image-20230104121018836](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041210898.png)

再次构建

![image-20230104121412397](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041214436.png)

检查目标目录下

![image-20230104121501115](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041215149.png)

在项目目录下新建文件 Dockerfile

![image-20230104125552773](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041255812.png)

新建docker-compose.yml

![image-20230104125617549](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041256595.png)

上传GIT，然后点击构建

![image-20230104125953065](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041259117.png)

6、构建后操作

![image-20230104130713709](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041307759.png)

```
cd /usr/local/test/docker
mv ../target/*.jar ./
docker-compose down
docker-compose up -d --build
#删除多余的镜像
docker image prune -f
```

![image-20230104153313968](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041533025.png)

点击保存

再次立即构建，发现未成功

![image-20230104150733857](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041507900.png)

解决办法：

检查一下脚本是否有错字



问题：

Exception when publishing, exception message [Exec timed out or was interrupted

出现构建时间太长失败的

解决办法：

延长timeout时间如下，默认的timeout时间为120秒

![image-20230104155501523](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041555563.png)



![image-20230104155736601](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041557638.png)

然后执行命令：docker images;

![image-20230104155554668](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041555700.png)

发现镜像已经构建成功

http://192.168.133.113:8081/test

![image-20230104155650570](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301041556605.png)

### 3.4 JENKINS的CD操作

参数化构建

![image-20230105145401060](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051454453.png)



![image-20230105152207435](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051522504.png)

增加SHELL

![image-20230105153457105](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051534151.png)

![image-20230105154001539](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051540592.png)

保存

然后在git仓库打上一个TAG标签

![image-20230105154237493](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051542538.png)

![image-20230105154454317](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051544384.png)

修改idea项目

![image-20230105155837554](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051558596.png)



再次创建GIT的新标签

![image-20230105163410200](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051634246.png)

![image-20230105163358317](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051633373.png)

可以发现增加的标签的选项

![image-20230105163448366](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051634410.png)

当我们选择1.0.0构建，看控制台的日志显示

![image-20230105164045970](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051640016.png)

选择2.1.0g构建

![image-20230105164906873](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051649918.png)



再次访问http://192.168.133.113:8081/test

![image-20230105164834082](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301051648118.png)





## 4 Sonar Qube

SonarQube 是一个自我管理的自动代码管理工具，sonarqube实例包含三个组件：sonarqube-scanner ，sonarqube server和Database server

数据库采用的是PostgreSQL

安装：

docker pull postgres

然后再拉取

### 4.1 安装 sonarqube

使用docker-compose.yml

version: '3.1'
services: 
  db: 
    image: postgres
    restart: always
    container_name: db
    ports:
      - 5432:5432
        environment:
            POSTGRES_USER: sonar   
            POSTGRES_PASSWORD: sonar
        networks: 
            - sonar-network
      sonarqube:
            image: sonarqube:8.9.6-community
            restart: always 
            container_name: sonarqube
            depends_on:
                  - db    
                ports:
                        - 9000:9000
                    environment:
                              SONARQUBE_JDBC_USERNAME: sonar
                              SONARQUBE_JDBC_PASSWORD: sonar
                              SONARQUBE_JDBC_URL: jdbc:postgresql://db:5432/sonar
                    networks: 
                              - sonar-network
                    networks:
            sonar-network:
                        driver: bridge

报错：

![image-20221228164855224](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212281649349.png)

### 4.2 修改虚拟内存配置

vi /etc/sysctl.conf

![image-20221228202510171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212282025219.png)

改完之后重新 docker-compose up -d

访问地址：http://192.168.133.113:9000/

![image-20221228203257127](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212282032164.png)



### 4.3 装中文插件

\#进入sonarqube页面 ip:9000 默认账户密码admin/admin 

#安装中文插件Administrator -Marketplace -下面搜索框中查询Chines同意 I Chinese-lunderstand the risk -下载Install 下载完毕提示重启 Restart Server

![image-20221228203814590](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212282038669.png)

![image-20221228203845261](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212282038315.png)



### 4.5 查看日志

docker logs -f sonarqube

### 4.6 sonarscanner

下载https://docs.sonarqube.org/latest/analyzing-source-code/scanners/sonarscanner/

![image-20230105220028476](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301052200600.png)

安装解压缩工具

yum -y install unzip

把文件夹上传到服务器

![image-20230106123519014](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061235059.png)

unzip sonar-scanner-cli-4.6.0.2311-linux.zip 

mv sonar-scanner-4.6.0.2311-linux sonar-scanner

![image-20230106123941827](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061239867.png)

然后把sonar-scanner 复制到 jenkins容器数据卷中

![image-20230106124149067](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061241124.png)

修改配置文件，修改默认IP

vi sonar-scanner.properties 

![image-20230106124654382](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061246419.png)

保存。



### 4.6 jenkins整合 Sonarscanner

进入插件管理

![image-20230106110629638](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061106883.png)



选择插件

![image-20230106110753615](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061107664.png)

![image-20230106110835111](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061108151.png)

安装完毕

![image-20230106110942196](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061109259.png)

在jenkins里配置sonarqube

![](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061137658.png)

填写完信息后，点击添加

![image-20230106114022211](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061140261.png)

先去sonarqube生成TOKEN

![image-20230106114135899](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061141944.png)

然后添加凭据

![image-20230106114314876](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061143936.png)

![image-20230106114340196](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061143244.png)



在任务管理里增加配置

![image-20230106122619964](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061226030.png)

```
sonar.projectname=${JOB_NAME}
sonar.projectKey=${JOB_NAME}
sonar.source=./
sonar.java.binaries=target
```



![image-20230106122829493](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061228544.png)

再去全局配置里配置一下sonarqube的地址

![image-20230106124941679](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061249732.png)

![image-20230106125230933](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061252983.png)

然后再次构建

![image-20230106125416691](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061254738.png)

构建完成，查看控制台日志

![image-20230106125533849](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061255899.png)

![image-20230106125603391](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061256441.png)



## 5 harbor

### 5.1 概念

**Harbor是一个用于存储和分发Docker镜像的企业级Registry服务器**，虽然Docker官方也提供了公共的镜像仓库，但是从安全和效率等方面考虑，部署企业内部的私有环境Registry是非常必要的，**Harbor和docker中央仓库的关系，就类似于nexus和Maven中央仓库的关系**，Harbor除了存储和分发镜像外还具有**用户管理**，**项目管理**，**配置管理和日志查询**，**高可用部署**等主要功能。

下载地址：https://github.com/goharbor/harbor/releases

### 5.2 安装

下载安装包

上传到服务器并解压缩

![image-20230106181550841](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061815984.png)

tar -zxvf harbor-offline-installer-v2.3.4.tgz -C /opt

![image-20230106183013530](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061830584.png)

复制一份YML文件

cp harbor.yml.tmpl harbor.yml

然后编辑配置文件

![image-20230106183412866](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061834923.png)

密码：Harbor12345

安装命令：./install.sh

![image-20230106183540822](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061835866.png)

![image-20230106183612574](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061836621.png)

安装完毕。

![image-20230106183702530](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061837576.png)

默认访问地址：

http://192.168.133.113/

![image-20230106183816965](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061838043.png)

用户名：admin

密码：Harbor12345

![image-20230106183908599](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301061839675.png)

启动和关闭harbor服务命令

进入harbor目录，执行

docker-compose start

docker-compose stop

![image-20230107111255593](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071113689.png)

### 5.3 设置开机自启动

vim /usr/lib/systemd/system/harbor.service

[Unit]
Description=Harbor
After=docker.service systemd-networkd.service systemd-resolved.service
Requires=docker.service
Documentation=http://github.com/vmware/harbor

[Service]
Type=simple
Restart=on-failure
RestartSec=5
ExecStart=/usr/local/bin/docker-compose -f /opt/harbor/docker-compose.yml up
ExecStop=/usr/local/bin/docker-compose -f /opt/harbor/docker-compose.yml down

[Install]
WantedBy=multi-user.target

然后

sudo systemctl enable harbor

sudo systemctl start harbor



重启验证

命令：*reboot*

### 5.4 使用

建立一个新的仓库

![image-20230107153417177](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071534249.png)

/etc/docker/daemon.json 是 docker 的配置文件

添加私有仓库地址

vi /etc/docker/daemon.json

![image-20230107153857570](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071538621.png)

{
"insecure-registries":["192.168.133.113:80"]
}

编辑保存后，重启Docker

systemctl restart docker



### 5.5 Jenkins绑定Docker

如何让jenkins使用宿主机的Docker

cd /var/run

![image-20230107154941124](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071549157.png)

修改这个文件的权限

chown root:root docker.sock

chmod o+rw docker.sock

赋予权限

![image-20230107161942684](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071619726.png)





修改jenkins配置

![image-20230107155550294](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071555335.png)

version: '3.1'
services:
  jenkins:
    restart: always
    image: jenkins/jenkins:2.319.1-lts
    container_name: jenkins
    ports:
      - 9001:8080
            - 50001:50000
        volumes:
            - ./data:/var/jenkins_home/
                  - /var/run/docker.sock:/var/run/docker.sock
                  - /usr/bin/docker:/usr/bin/docker
                        - /etc/docker/daemon.json:/etc/docker/daemon.json

![image-20230107155902753](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071559795.png)

点击保存，重启容器

docker-compose up -d

报错：

![image-20230107160347992](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071603041.png)

failed to start daemon: invalid mirror: "192.168.133.113:80" is not a valid URI

解决办法：vi /etc/docker/daemon.json 检查配置项是否正确

[root@localhost jenkins_docker]# systemctl daemon-reload
[root@localhost jenkins_docker]# systemctl restart docker
[root@localhost jenkins_docker]# docker-compose up -d

![image-20230107161130084](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071611127.png)

进入Jenkins容器里执行docker命令

[root@localhost jenkins_docker]# docker exec -it jenkins bash
jenkins@962680d0848a:/$ docker version

![image-20230107162028017](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071620071.png)



### 5.6 Jenkins自定义镜像推送到Harbor

准备工作：

删掉工程项目的docker-compose.yml

提交新代码

打标签V3.0.0

![image-20230107193214229](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071932290.png)

准备Jenkins的脚本

mv target/*.jar docker/
docker build -t mytest:$tag docker/
docker login -u admin -p Harbor12345 192.168.133.113:80
docker tag mytest:$tag 192.168.133.113:80/repo/mytest:$tag
docker push 192.168.133.113:80/repo/mytest:$tag





把原来JENKINS的删掉

![image-20230107194254991](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071942060.png)

增加构建后的SHELL命令

![image-20230107195017246](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071950303.png)



![image-20230107195056929](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071950979.png)

点击构建

![image-20230107195224555](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301071952604.png)

构建后检查仓库

报错： dial unix /var/run/docker.sock: connect: permission denied

![image-20230107200545185](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072005243.png)

解决办法：

检查docker.sock文件的权限

ll /var/run/docker.sock

![image-20230107200640398](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072006440.png)



sudo chmod 666 /var/run/docker.sock

但是每次重启权限会失效

如果要长期设置有效

设置一个重启任务

vi /root/grant.sh

chmod 777 /var/run/docker.sock

chmod 777 /root/grant.sh

crontab -e

```
*/5 * * * *  /root/grant.sh
```

每五分钟执行一次



报错：

```
Step 1/4 : FROM daocloud.io/library/java:8u40-jdk
Get "https://daocloud.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
Build step 'Execute shell' marked build as failure
```



解决办法：

nmcli con mod ens33 ipv4.dns  "114.114.114.114 8.8.8.8"

使DNS配置生效

nmcli con up ens33

构建成功。

![image-20230107220234302](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072202374.png)

![image-20230107220303055](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072203114.png)



### 5.7 目标服务器准备脚本

步骤如下：

1、告知目标服务器拉取哪个镜像

2、判断当前服务器是否正在运行容器，需要删除

3、如果目标服务器正在运行容器，需要删除

4、目标服务器拉取harbor上的镜像

5、将拉取下的镜像运行成容器



脚本内容：

vi  /root/deploy.sh 

horbar_addr=$1
horbar_repo=$2
project=$3
version=$4
echo "容器运行时端口"
container_port=$5
echo "宿主机映射端口"
host_port=$6
echo "镜像名称"
imagesName=$horbar_addr/$horbar_repo/$project:$version
echo $imagesName
echo "拿到正在运行的id"
containerId=`docker ps -a |grep ${project} | awk '{print $1}'`
echo $containerId
echo "存在id 停止删除进程"
if [ "$containerId" != "" ] ; then
  docker stop $containerId
  docker rm $containerId
fi
echo "打印工程  tag版本"
tag=`docker images | grep ${project} | awk '{print $2}'`
echo $tag
echo  "versin中包含tag版本，删除镜像"
if [[ "$tag" =~ "$version" ]] ; then
  docker rmi $imagesName
fi
echo  "登入harbor仓库"
docker login -u admin -p Harbor12345 $horbar_addr
echo  "推送镜像"
docker pull $imagesName
echo  "删除 none多余镜像"
docker images | grep none | awk '{print $3}'| xargs docker rmi --force
docker run -d -p $host_port:$container_port --name $project $imagesName
echo "success"



### 5.8 最终部署

配置jenkins的构建后操作

![image-20230107221637247](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072216297.png)

 chmod a+x deploy.sh

mv deploy.sh /usr/bin/

增加脚本

![image-20230107222056997](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072220047.png)

添加端口字符参数

![image-20230107222152810](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072221871.png)



![image-20230107223419041](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072234123.png)

在参数里增加

deploy.sh 192.168.133.113:80 repo ${JOB_NAME} $tag $container_port $host_port

![image-20230107223949936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072239985.png)

构建成功

![image-20230107231743709](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072317758.png)



![image-20230107231729887](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301072317941.png)









## 6 pipeline构建

### 6.1 为什么要pipeline（流水线）

流水线Pipeline，简而言之，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排与可视化；
Pipeline 是Jenkins 2.X 的最核心的特性，帮助Jenkins 实现从CI 到 CD 与 DevOps的转变。
Pipeline 是一组插件，让jenkins 可以实现持续交付管道的落地和实施。持续交付管道是将软件从版本控制阶段到交付给用户/客户的完整过程的自动化表现。

### 6.2 创建简单任务

![image-20230108111532530](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081115603.png)

![image-20230108111611328](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081116394.png)

![image-20230108111846825](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081118883.png)

![image-20230108111915233](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081119291.png)

![image-20230108111936955](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081119016.png)



### 6.3 基本语法

![image-20230108135925067](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081359130.png)

//所有的脚本命令都放在pipeline中

```
pipeline {
   //执行任务在哪个集群节点中执行
   agent any
   //声明全局变量
    environment {
        key = 'valus'
    }

   stages {
        stage('拉取git仓库代码') {
            steps {
                echo '拉取git仓库代码 - SUCCESS'
            }
        }
        stage('通过maven构建项目') {
        steps {
            echo '通过maven构建项目 - SUCCESS'
        }            
    }

    stage('通过sonarQube做代码质量检测') {
        steps {
            echo '通过sonarQube做代码质量检测 - SUCCESS'
        }            
    }

    stage('通过docker制作自定义镜像') {
        steps {
            echo '通过docker制作自定义镜像 - SUCCESS'
        }            
    }

     stage('将自定义镜像推送到Harbor') {
        steps {
            echo '将自定义镜像推送到Harbor - SUCCESS'
        }            
    }

    stage('通过 Publish Over SSH通知目标服务器') {
        steps {
            echo '通过 Publish Over SSH通知目标服务器 - SUCCESS'
        }            
    }
}
}
```


       

![image-20230108140455776](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081404838.png)

![image-20230108140604319](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081406387.png)

使用流水线语法

![image-20230108140847619](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081408689.png)

![image-20230108145345095](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081453161.png)

![image-20230108145505590](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081455658.png)

点击生成流水线脚本

![image-20230108145602394](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081456480.png)





### 6.4 jenkinsfile维护脚本

![image-20230108145846360](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081458422.png)

在GIT项目中新增一个文件，把刚才的脚本复制到文件里。

![image-20230108150434435](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081504495.png)

![image-20230108150901738](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081509815.png)

![image-20230108150950888](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081509965.png)

![image-20230108151321846](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081513914.png)

然后在jenkins里配置

![image-20230108151435091](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081514170.png)

再次立即构建

![image-20230108151555975](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081515053.png)



### 6.5 拉取git代码

增加参数化构建

![image-20230108151803567](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081518622.png)

添加一个git参数

![image-20230108152236460](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081522524.png)

![image-20230108152350194](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081523266.png)

获取生成的流水线脚本

![image-20230108152510342](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081525405.png)

checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'ab9ca15c-66fe-48f8-9e05-0665acbaa794', url: 'http://192.168.133.104:8929/gitlab-instance-24163a24/mytest.git']]])

注意，要改成${tag}

![image-20230108152848841](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081528907.png)

然后点击构建，查看日志

![image-20230108153138579](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081531651.png)

![image-20230108153155998](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081531065.png)



### 6.6 pipeline-maven

在流水线语法使用

![image-20230108154052735](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081540806.png)

编辑项目 pipeline - 流水线 - 流水线语法 - 片段生成器 

/var/jenkins_home/maven/bin/mvn clean package -DskipTests

![image-20230108212729563](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082127710.png)

修改脚本

![image-20230108215414352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082154446.png)

再次构建

![image-20230108215604971](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082156043.png)



### 6.7 pipeline-soanrqube代码质量

soanrqube生成密钥

![image-20230108220333398](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082203461.png)

准备好脚本

/var/jenkins_home/sonar-scanner/bin/sonar-scanner -Dsonar.source=./ -Dsonar.projectname=${JOB_NAME} -Dsonar.projectKey=${JOB_NAME} -Dsonar.java.binaries=./target/ -Dsonar.login=1c4c038146f9f2972fcf33bcbd4b3ee627faed53

生成流水线脚本

sh '/var/jenkins_home/sonar-scanner/bin/sonar-scanner -Dsonar.source=./ -Dsonar.projectname=${JOB_NAME} -Dsonar.projectKey=${JOB_NAME} -Dsonar.java.binaries=./target/ -Dsonar.login=1c4c038146f9f2972fcf33bcbd4b3ee627faed53'

![image-20230108220433436](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082204510.png)

修改jenkinsfile

![image-20230108220539499](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082205554.png)

再次构建

![image-20230108220708873](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082207938.png)



![image-20230108220651313](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082206370.png)



### 6.8 pipeline-Docker自定义镜像

准备脚本

mv ./target/*.jar ./docker/
docker build -t ${JOB_NAME}:${tag} ./docker/

![image-20230108221521167](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082215244.png)

![image-20230108221637487](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082216553.png)

提交后再次构建

![image-20230108221736589](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082217656.png)

![image-20230108221829560](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082218615.png)



### 6.9 pipeline-harbor仓库

修改Jenkinsfile,增加全局变量

​        harboUser = 'admin'
​        harborPass = 'Harbor12345'
​        harborAddress = '192.168.133.113:80'
​        harborRepo = 'repo'

![image-20230108222105097](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082221157.png)



 sh '''docker login -u ${harboUser} -p ${harborPass} ${harborAddress} docker tag ${JOB_NAME}:${tag} ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag} docker push ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag}'''

![image-20230108222449621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082224683.png)

![image-20230108222515322](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082225379.png)

### 6.10 通知目标服务器构建项目

pipeline-参数化构建过程-添加参数-字符参数 

container_port 8080 容器内部占用端口 

host_port 8081 宿主机映射端口

![image-20230108225852579](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082258650.png)

编辑项目pipeline-流水线-流水线语法-片段生成器-示例步骤（sshPublisher:Send build artifacts over SSH) -生成后的流水线脚本添加至git仓库修改Jenkinsfile

![image-20230108222936970](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082229031.png)

deploy.sh $harborAddress $harborRepo $JOB_NAME $tag $container_port $host_port

生成流水线脚本

sshPublisher(publishers: [sshPublisherDesc(configName: 'test', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'deploy.sh $harborAddress $harborRepo $JOB_NAME $tag $container_port $host_port', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

![image-20230108230059710](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082300787.png)

注意

水线脚本中的一个坑，这里的’’ 不会引用Jenkinsfile文件中的变量，需要换成双引号

![image-20230108230256839](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082302896.png)



构建成功

![image-20230108230542966](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082305044.png)

![image-20230108230527475](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082305530.png)



### 6.11 钉钉通知消息

下载钉钉插件

![image-20230108230714341](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082307405.png)

![image-20230108231346643](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082313708.png)

![image-20230108231359979](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082314036.png)

添加成功后，复制 Webhook 地址，在配置 Jenkins 时使用

![image-20230108231427078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082314137.png)

在jenkinsfile中添加完成后的操作

```
post{
   success{
       dingtalk(
           robot:'Jenkins-DingDing',
           type:'MARKDOWN',
           title:"success:@{JOB_NAME}",
           text:["- 成功构建: @{JOB_NAME}! \n- 版本:${tag} \n- 持续时间:${currentBuild.durationString}]
      )
   }
   failure{
       dingtalk(
           robot:'Jenkins-DingDing',
           type:'MARKDOWN',
           title:"success:@{JOB_NAME}",
           text:["- 构建失败: @{JOB_NAME}! \n- 版本:${tag} \n- 持续时间:${currentBuild.durationString}]
      )
   }
}
```





## 7 K8S

访问官网文档

https://kubernetes.io/zh-cn/docs/home/

### 7.1 为什么用K8s

容器是打包和运行应用程序的好方式。在生产环境中， 你需要管理运行着应用程序的容器，并确保服务不会下线。 例如，如果一个容器发生故障，则你需要启动另一个容器。 如果此行为交由给系统处理，是不是会更容易一些？

这就是 Kubernetes 要来做的事情！ Kubernetes 为你提供了一个可弹性运行分布式系统的框架。 Kubernetes 会满足你的扩展要求、故障转移你的应用、提供部署模式等。 例如，Kubernetes 可以轻松管理系统的 Canary (金丝雀) 部署。

Kubernetes 为你提供：

- **服务发现和负载均衡**

  Kubernetes 可以使用 DNS 名称或自己的 IP 地址来暴露容器。 如果进入容器的流量很大， Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。

- **存储编排**

  Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。

- **自动部署和回滚**

  你可以使用 Kubernetes 描述已部署容器的所需状态， 它可以以受控的速率将实际状态更改为期望状态。 例如，你可以自动化 Kubernetes 来为你的部署创建新容器， 删除现有容器并将它们的所有资源用于新容器。

- **自动完成装箱计算**

  你为 Kubernetes 提供许多节点组成的集群，在这个集群上运行容器化的任务。 你告诉 Kubernetes 每个容器需要多少 CPU 和内存 (RAM)。 Kubernetes 可以将这些容器按实际情况调度到你的节点上，以最佳方式利用你的资源。

- **自我修复**

  Kubernetes 将重新启动失败的容器、替换容器、杀死不响应用户定义的运行状况检查的容器， 并且在准备好服务之前不将其通告给客户端。

- **密钥与配置管理**

  Kubernetes 允许你存储和管理敏感信息，例如密码、OAuth 令牌和 ssh 密钥。 你可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥

### 7.2 架构

![image-20230109164224506](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301091642711.png)



### 7.3 使用Kuboard安装K8S

- Kubernetes v1.19

版本要降低到1.19

选择历史安装版本

![image-20230109225550070](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20230109225550070.png)



![image-20230111171014014](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111710194.png)

```sh
# 在 master 节点和 worker 节点都要执行
cat /etc/redhat-release

# 此处 hostname 的输出将会是该机器在 Kubernetes 集群中的节点名字
# 不能使用 localhost 作为节点的名字
hostname

# 请使用 lscpu 命令，核对 CPU 信息
# Architecture: x86_64    本安装文档不支持 arm 架构
# CPU(s):       2         CPU 内核数量不能低于 2
lscpu
```

检查操作系统

![image-20230111172451756](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111724810.png)

修改域名

```sh
hostnamectl set-hostname your-new-host-name
```

![image-20230111172737854](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111727898.png)

```sh
echo "127.0.0.1   $(hostname)" >> /etc/hosts
```

关闭防火墙

systemctl stop firewalld

systemctl disable  firewalld

开始安装

```sh
export REGISTRY_MIRROR=https://registry.cn-hangzhou.aliyuncs.com
curl -sSL https://kuboard.cn/install-script/v1.19.x/install_kubelet.sh | sh -s 1.19.5
```



初始化 master 节点

// 只在 master 节点执行
// 替换 x.x.x.x 为 master 节点实际 IP（请使用内网 IP）
// export 命令只在当前 shell 会话中有效，开启新的 shell 窗口后，如果要继续安装过程，请重新执行此处的 export 命令
export MASTER_IP=192.168.133.114
// 替换 apiserver.demo 为 您想要的 dnsName
export APISERVER_NAME=jiang.demo
// Kubernetes 容器组所在的网段，该网段安装完成后，由 kubernetes 创建，事先并不存在于您的物理网络中
export POD_SUBNET=10.100.0.1/16
echo "${MASTER_IP}    ${APISERVER_NAME}" >> /etc/hosts
curl -sSL https://kuboard.cn/install-script/v1.19.x/init_master.sh | sh -s 1.19.5

**检查 master 初始化结果**

```sh
watch kubectl get pod -n kube-system -o wide
```

所有都变成running状态

![image-20230111181308504](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111813555.png)



接下来初始化worker节点

Master先要生成一个token

```sh
kubeadm token create --print-join-command
```

![image-20230111181731847](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111817895.png)

有效时间

该 token 的有效时间为 2 个小时，2小时内，您可以使用此 token 初始化任意数量的 worker 节点



然后初始化worker节点

// 只在 worker 节点执行
// 替换 x.x.x.x 为 master 节点的内网 IP
export MASTER_IP=192.168.133.114
// 替换 apiserver.demo 为初始化 master 节点时所使用的 APISERVER_NAME
export APISERVER_NAME=jiang.demo
echo "${MASTER_IP}    ${APISERVER_NAME}" >> /etc/hosts

![image-20230111182114947](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111821993.png)

最后把前面生成的

kubeadm join jiang.demo:6443 --token v6gitv.vl41oejpbwj2zhma     --discovery-token-ca-cert-hash sha256:cba932e6d037c0b46d0c29bac179ae3d0c9f91a4ad2eb2ce0e7974a9ecbf8279 

在worker节点执行。

![image-20230111182246441](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111822495.png)

然后检查结果

在 master 节点上执行

```sh
kubectl get nodes -o wide
```

![image-20230111182343588](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111823634.png)



### 7.4 安装图形管理工具

![image-20230111182618111](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111826189.png)

![image-20230111182754166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111827219.png)



kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3.yaml
// 您也可以使用下面的指令，唯一的区别是，该指令使用华为云的镜像仓库替代 docker hub 分发 Kuboard 所需要的镜像
// kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3-swr.yaml

等待 Kuboard v3 就绪

执行指令 `watch kubectl get pods -n kuboard`，等待 kuboard 名称空间中所有的 Pod 就绪

![image-20230111183034006](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111830050.png)

![image-20230111183113655](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111831705.png)



访问地址

http://192.168.133.114:30080/

- 用户名： `admin`
- 密码： `Kuboard123`

![image-20230111183250066](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301111832133.png)



### 7.5 NameSpace

名称空间的用途是，为不同团队的用户（或项目）提供虚拟的集群空间，也可以用来区分开发环境/测试环境、准上线环境/生产环境。

执行命令 `kubectl get namespaces` 可以查看名称空间

![image-20230112175023421](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301121750566.png)

Kubernetes 安装成功后，默认有初始化了三个名称空间：

- **default** 默认名称空间，如果 Kubernetes 对象中不定义 `metadata.namespace` 字段，该对象将放在此名称空间下
- **kube-system** Kubernetes系统创建的对象放在此名称空间下
- **kube-public** 此名称空间自动在安装集群是自动创建，并且所有用户都是可以读取的（即使是那些未登录的用户）。主要是为集群预留的，例如，某些情况下，某些Kubernetes对象应该被所有集群用户看到。



//创建命名空间

kubectl create ns test-dev

//删除

kubectl delete ns test-dev

也可以使用yml文件来创建命名空间

vi namespace-test.yml

```
apiVersion: v1
kind: Namespace
metadata:
  name: test-dev           # 名称 
```

然后执行命令： kubectl apply -f  namespace-test.yml

### 7.6 Pod操作

![image-20230112221707782](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301122217855.png)

Pod（容器组）是 k8s 集群上的最基本的单元。当我们在 k8s 上创建 Deployment 时，会在集群上创建包含容器的 Pod (而不是直接创建容器)。每个Pod都与运行它的 worker 节点（Node）绑定

- Pod 是一组容器（可包含一个或多个应用程序容器），以及共享存储（卷 Volumes）、IP 地址和有关如何运行容器的信息。
- 如果多个容器紧密耦合并且需要共享磁盘等资源，则他们应该被部署在同一个Pod（容器组）中。

查看所有的POD

kubectl get pods -A

![image-20230112221932858](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301122219910.png)



// 1、创建一个命名空间
kubectl create namespace test-dev 

// 2、查询命名空间列表
kubectl get ns 

// 3、创建一个 nginx 的 pod 服务
kubectl run nginx --image=nginx:1.17.9 --port=80 --namespace=test-dev

// 4、查询指定命名空间的 pod 列表
kubectl get pods -n test-dev

kubectl logs -f nginx -n test-dev

kubectl describe pod nginx -n test-dev

![image-20230112224755199](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301122247261.png)

可以看到nginx服务的IP是10.100.162.193

执行curl 10.100.162.193

![image-20230112224859219](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301122248267.png)



 kubectl get pod  -n test-dev

![image-20230113181047762](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131810909.png)



### 7.6 Pod运行多容器

![image-20230113190334607](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131903691.png)

搜镜像

![image-20230113190509350](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131905405.png)

daocloud.io/library/nginx:1.9.1



![image-20230113190857108](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131908166.png)

选择一个版本8.5.57

![image-20230113191542772](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131915829.png)

daocloud.io/library/tomcat:8.5.57

编辑YML文件

vi pod-nginx-tomcat.yml

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-tomcat
  namespace:test-dev
spec:
  containers:
    - image:daocloud.io/library/nginx:1.9.1
      name:nginx
    - image:daocloud.io/library/tomcat:8.5.57
      name:tomcat
```



![image-20230113191716085](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131917133.png)

执行命令：kubectl apply -f pod-nginx-tomcat.yml

![image-20230113192624866](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131926914.png)

进入图形化界面查看



![image-20230113191156014](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131911096.png)



![image-20230113193943072](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131939150.png)

![image-20230113194010063](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301131940153.png)



### 7.7 Deployment操作

Deployment 是最常用的用于部署无状态服务的方式。Deployment 控制器使得您能够以声明的方式更新 Pod（容器组）和 ReplicaSet（副本集）。

创建方式有2种

- 使用 kubectl 创建 Deployment

创建命令： kubectl create deployment deploy-nginx -n test-dev --image=daocloud.io/library/nginx:1.9.1

![image-20230113200433302](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132004348.png)

删除命令：kubectl delete deployment deploy-nginx

使用YML文件创建

![image-20230113201327947](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132013022.png)



```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: test-dev
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
       - name: nginx
    image: daocloud.io/library/nginx:1.9.1
    ports:
    - containerPort: 80
```


​    

![image-20230113202938532](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132029578.png)

图形界面查看结果

![image-20230113203011920](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132030995.png)

![image-20230113203251469](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132032531.png)

- 使用 Kuboard 创建 Deployment

1 进入 Kuboard 名称空间页面，点击左侧菜单中的 ***创建工作负载*** 按钮

![image-20230113203034358](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132030419.png)

并填写如下表单：

![image-20230113203100419](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132031472.png)

2 切换到 ***容器信息*** Tab 页，如下图所示

![image-20230113203128010](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301132031070.png)

并填写如下表单：

| 区域                    | 字段名称 | 填写内容    | 字段说明         |
| ----------------------- | -------- | ----------- | ---------------- |
| 容器信息-->添加工作容器 | 容器名称 | nginx       |                  |
|                         | 镜像     | nginx:1.7.9 |                  |
|                         | Ports    | TCP : 80    | 容器组暴露的端口 |

点击保存后，可以看到 Deployment 的更新界面

可以用图形化界面工具控制伸缩

![image-20230114102939536](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141029668.png)

![image-20230114103309371](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141033437.png)

调整为2

容器组显示只有2个

![image-20230114103337136](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141033189.png)



### 7.8 Service

#### 7.8.1 为什么要用Service？

Kubernetes 中 Pod 是随时可以消亡的（节点故障、容器内应用程序错误等原因）。如果使用 [Deployment](https://www.kuboard.cn/learning/k8s-intermediate/service/learning/k8s-intermediate/workload/wl-deployment/) 运行您的应用程序，Deployment 将会在 Pod 消亡后再创建一个新的 Pod 以维持所需要的副本数。每一个 Pod 有自己的 IP 地址，然而，对于 Deployment 而言，对应 Pod 集合是动态变化的。

Service 是 Kubernetes 中的一种服务发现机制：

- Pod 有自己的 IP 地址
- Service 被赋予一个唯一的 dns name
- Service 通过 label selector 选定一组 Pod
- Service 实现负载均衡，可将请求均衡分发到选定这一组 Pod 中

Kubernetes 通过引入 Service 的概念，将前端与后端解耦

![image-20230114104050720](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141040790.png)



#### 7.8.2 创建及使用

命令方式创建

kubectl expose deployment nginx-deployment --port=8888 --target-port=80 -n test-dev

[root@k8smaster ~]# kubectl expose deployment nginx-deployment --port=8888 --target-port=80 -n test-dev
service/nginx-deployment exposed

[root@k8smaster ~]# kubectl get service -n test-dev
NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
nginx-deployment   ClusterIP   10.96.88.76   <none>        8888/TCP   43s

为了查看效果，先进到容器里修改一下

![image-20230114105101071](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141051129.png)

![image-20230114105601141](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141056193.png)

![image-20230114105722611](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141057663.png)

用命令测试访问nginx

![image-20230114105848851](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141058896.png)

但是此时没有暴露对外访问端口。我们先删除之前创建的service

kubectl delete service nginx-deployment -n test-dev

然后重新用命令创建

kubectl expose deployment nginx-deployment --port=8888 --target-port=80 -n test-dev --type=NodePort

![image-20230114110359598](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141103651.png)

访问地址http://192.168.133.114:31718/

![image-20230114114728168](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141147217.png)

#### 7.8.3 用YAML文件创建

先删除原来创建的

![image-20230114115045600](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141150676.png)

![image-20230114115117841](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141151903.png)

![image-20230114115158865](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141151941.png)

![image-20230114115228192](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141152244.png)



编辑脚本vi deployment-nginx.yml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: test-dev
  name: nginx-deployment
  labels:
    app: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: daocloud.io/library/nginx:1.9.1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  namespace: test-dev
  name: nginx-deployment
  labels:
    app: nginx-deployment
spec:
  selector:
    app: nginx
  ports:
  - port: 8888
    targetPort: 80
  type: NodePort


```

[root@k8smaster ~]# kubectl apply -f deployment-nginx.yml
deployment.apps/nginx-deployment unchanged
service/nginx-deployment created

![image-20230114121112783](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141211863.png)

![image-20230114121137182](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141211265.png)



查看服务

![image-20230114121214377](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301141212466.png)

### 7.9 Ingress操作

Ingress 是 Kubernetes 的一种 API 对象，将集群内部的 Service 通过 HTTP/HTTPS 方式暴露到集群外部，并通过规则定义 HTTP/HTTPS 的路由。

Ingress 具备如下特性：集群外部可访问的 URL、负载均衡、SSL Termination、按域名路由（name-based virtual hosting）。

![image-20230115104525397](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151045507.png)

#### 7.9.1 安装Ingress

![image-20230115111851478](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151118577.png)

选择安装，注意名字字母不要大写

否则报错：

ConfigMap "ingress-nginx-controller-Ingress" is invalid: metadata.name: Invalid value: "ingress-nginx-controller-Ingress": a DNS-1123 subdomain must consist of lower case alphanumeric characters, '-' or '.', and must start and end with an alphanumeric cha

![image-20230115113603905](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151136983.png)

![image-20230115113707477](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151137570.png)

#### 7.9.2 编写YML文件



先把原来创建的停掉

kubectl delete -f deployment-nginx.yml

![image-20230115113854166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151138214.png)



```
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: test-dev
  name: nginx-deployment
  labels:
    app: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: daocloud.io/library/nginx:1.9.1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  namespace: test-dev
  name: nginx-deployment
  labels:
    app: nginx-deployment
spec:
  selector:
    app: nginx
  ports:
  - port: 8888
    targetPort: 80
  type: NodePort
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: test-dev
  name: nginx-ingress
spec:
  ingressClassName: ingress
  rules:
  - host: jiang.nginx.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend: 
          service:
            name: nginx-deployment
            port:
              number: 8888

```

查看结果

![image-20230115115603361](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151156447.png)

访问地址http://192.168.133.114:30905/

也可以配置自定义域名来访问jiang.nginx.com

![image-20230115115758982](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151157033.png)

再次用域名访问
http://jiang.nginx.com:30905/

![image-20230115115902859](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301151159911.png)







## 8 JENKINS整合K8S

### 8.1 准备部署的YAML文件

pipeline.yml

```
apiVersion: v1
kind: Service
metadata:
  namespace: test-dev
  name: pipeline
  labels:
    app: pipeline
spec:
  selector:
    app: pipeline
  ports:
  - port: 8081
    targetPort: 8080
  type: NodePort
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: test-dev
  name: pipeline
  labels:
    app: pipeline
spec:
  replicas: 2
  selector:
    matchLabels:
      app: pipeline
  template:
    metadata:
      labels:
        app: pipeline
    spec:
      containers:
        - name: pipeline
          image: 192.168.133.113:80/repo/pipeline:V3.0.0
          #一直从仓库拉取镜像
          imagePullPolicy: Always
          ports:
            - containerPort: 8080
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: test-dev
  name: pipeline
spec:
  ingressClassName: ingress
  rules:
  #记得给自己的k8s集群服务器添加本地host域名解析
  - host: jiang.pipeline.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-deployment
            port:
              number: 8888

```

测试文件

删除原来的服务和deployment

![image-20230116202732888](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162027043.png)

![image-20230116202854061](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162028145.png)

容器组也删掉

![image-20230116202953007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162029079.png)

然后编辑脚本

```
vi pipeline.yml
```

用脚本启动

```
kubectl apply -f pipeline.yml
```

创建成功

![image-20230116205259132](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162052207.png)

![image-20230116205311673](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162053733.png)

![image-20230116205331171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162053240.png)

![image-20230116205400153](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162054226.png)



### 8.2 配置Docker私服信息

#### 8.2.1 创建私服密钥



![image-20230116210835589](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162108687.png)

![image-20230116210908080](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162109166.png)

填写Harbor的用户名和密码

用户名：admin

密码：Harbor12345

地址：http://192.168.133.113:80

![image-20230116211139575](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301162111640.png)

#### 8.2.2 添加Harbor仓库地址

在master和worker服务器上增加私有镜像仓库地址：

```
{
"insecure-registries":["192.168.133.113:80"]
}
```

在master和worker服务器上执行命令

```
vi /etc/docker/daemon.json

```

修改内容

![image-20230117152318188](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171523334.png)

重启Docker

```
systemctl restart docker
```

 再次添加密钥测试

![image-20230117153022730](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171530828.png)

复制指令到服务器上测试

![image-20230117153137896](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171531954.png)

然后在K8snaster上执行

```
kubectl apply -f pipeline.yml
```

给自己的HOST文件追加一个新域名

![image-20230117153625137](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171536195.png)

容器已就绪

![image-20230117160904305](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171609374.png)

![image-20230117160850955](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171608038.png)

![image-20230117160809773](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171608854.png)

访问地址http://jiang.pipeline.com:30064/test

显示结果

```
hello jenkins，V3.0.0
```



### 8.3 Jenkins整合K8S

#### 8.3.1 gitlab上增加文件

![image-20230117163709935](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171637021.png)

新增一个文件pipeline.yml

把之前的yaml文件内容复制到pipeline.yml

![image-20230117164030415](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171640500.png)

![image-20230117164052637](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171640707.png)





#### 8.3.2 准备环境

在k8s服务器增加目录

```
[root@k8smaster ~]# cd /usr/local/
[root@k8smaster local]# mkdir k8s
[root@k8smaster local]# cd k8s
[root@k8smaster k8s]# pwd
/usr/local/k8s
[root@k8smaster k8s]# 
```

进入Jenkins里配置

![image-20230117165107238](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171651301.png)

进入系统配置修改Publish over SSH，点击新增

![image-20230117165419513](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171654609.png)

 设置密码 点击高级-√ Use password authentication, or use a different key-Passphrase / Password（填写自己的密码）- Test Configuration(点击测试)

点击测试一下连接

![image-20230117165444311](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171654382.png)

保存。

#### 8.3.3  git 仓库修改 Jenkinsfile 文件

进入语法

编辑项目pipeline-流水线-流水线语法-片段生成器-示例步骤（shhPublisher: Send build artifacts over SSH） 

Name-(k8s) 

Source files-(pipeline.yaml)

![image-20230117221044076](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301172210181.png)

点击生成流水线脚本

![image-20230117170024342](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171700430.png)

```
sshPublisher(publishers: [sshPublisherDesc(configName: 'k8s', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'pipeline.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
```

修改git 仓库修改 Jenkinsfile 文件

![image-20230117170143692](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171701792.png)

替换：

![image-20230117170338162](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171703239.png)

修改代码为4.0.0

![image-20230117170448945](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171704017.png)

新增一个标签

![image-20230117170546381](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171705462.png)

![image-20230117170623621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171706697.png)

回到Jenkins再次构建

![image-20230117170716427](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171707504.png)

报错：

![image-20230117171437879](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301171714940.png)

```
An SSH Transfer Set must not have an empty Source files and an empty Exec command - the transfer set should transfer files, execute a command or do both
```

![image-20230117221000519](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301172210654.png)

处理完错误后重新构建

![image-20230117221409191](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301172214254.png)

已传输一个文件



但是我们需要Jenkins内部以无密码的方式，连接到K8S

#### 8.3.4 Jeknins配置linux免密远程部署

首先复制jenkins的公钥

进入jenkins内部

```
[root@localhost ~]# docker exec -it jenkins bash
jenkins@962680d0848a:/$ cd ~
```

如果没有.ssh文件夹，就创建

mkdir .ssh

执行命令

```
ssh-keygen -t rsa
```

![image-20230117223315713](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301172233772.png)

```
jenkins@962680d0848a:~/.ssh$ ls
id_rsa	id_rsa.pub
jenkins@962680d0848a:~/.ssh$ cat id_rsa.pub
```

显示公钥后复制

```
cp id_rsa.pub authorized_keys
```

复制docker容器的文件到主机（此命令不在docker bash里执行，在宿主机执行）

```
docker cp 962680d0848a:/var/jenkins_home/.ssh/authorized_keys /tmp/
```

然后把这个文件下载下来，再上传到K8S（114）的服务器



进入K8S（114）的服务器，进入用户目录创建.ssh文件夹

```
[root@k8smaster k8s]# cd ~
[root@k8smaster ~]# pwd
/root
[root@k8smaster ~]# ll -a
total 76
dr-xr-x---.  5 root root  4096 Jan 17 16:05 .
dr-xr-xr-x. 17 root root   244 Jan 11 18:33 ..
-rw-------.  1 root root  1902 Jan  9 22:33 anaconda-ks.cfg
-rw-------.  1 root root  4266 Jan 17 17:15 .bash_history
-rw-r--r--.  1 root root    18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root   176 Dec 29  2013 .bash_profile
-rw-r--r--.  1 root root   176 Dec 29  2013 .bashrc
-rw-r--r--.  1 root root 21077 Jan  8 19:49 calico-3.13.1.yaml
-rw-r--r--.  1 root root   100 Dec 29  2013 .cshrc
-rw-r--r--   1 root root   984 Jan 15 11:53 deployment-nginx.yml
drwx------   2 root root    25 Jan 17 15:31 .docker
drwxr-xr-x.  3 root root    33 Jan 11 18:11 .kube
-rw-r--r--.  1 root root   277 Jan 11 18:10 kubeadm-config.yaml
-rw-r--r--   1 root root  1109 Jan 17 16:05 pipeline.yml
drwxr-----.  3 root root    19 Jan 11 18:05 .pki
-rw-r--r--   1 root root   220 Jan 13 19:26 pod-nginx-tomcat.yml
-rw-r--r--.  1 root root   129 Dec 29  2013 .tcshrc
[root@k8smaster ~]# mkdir .ssh

```

进入.ssh文件夹

把Jenkins的公钥文件authorized_keys复制到.ssh文件夹下

![image-20230119154341693](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191543795.png)

测试

![image-20230119154451882](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191544938.png)

证明已经成功。

生成流水线脚本

```
ssh root@192.168.133.114 kubectl apply -f /usr/local/k8s/pipeline.yml
```

![image-20230119171711443](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191718673.png)

生成脚本

```
sh 'ssh root@192.168.133.114 kubectl apply -f /usr/local/k8s/pipeline.yml'
```

修改git 仓库修改 Jenkinsfile 文件



![image-20230119171811436](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191718505.png)

修改pipeline.yml（切记版本号，注意大小写）

![image-20230119180434614](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191804700.png)

这个版本号对应Harbor的版本号

![image-20230119180609679](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191806752.png)

重新打标签

![image-20230119180403834](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191804908.png)



再次构建

![image-20230119180342656](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191803754.png)

![image-20230119180326604](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191803680.png)





### 8.4 自动化CI操作

之前的是CD操作

现在要让gitlab通知JENKINS来拉取代码

#### 8.4.1 安装gitlab插件

Jenkins - 安装插件 - 在插件管理中，安装插件 GitLab , 安装后重启 jenkins

![image-20230119191212147](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191912228.png)

![image-20230119193850677](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191938756.png)

#### 8.4.2 修改Jenkins配置

![image-20230119195525739](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301191955816.png)

增加构建器

![image-20230119200625804](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192006885.png)

复制地址

```
http://192.168.133.113:9001/project/pipeline
```



#### 8.4.3 Git仓库配置

进入gitlab修改

Meun-Admin-Settings-Network-Outbound requests-Expand

![image-20230119202935515](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192029618.png)

![image-20230119203104441](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192031527.png)

再回到项目--添加Webhooks

![image-20230119202656267](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192026370.png)

点击测试

![image-20230119204404272](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192044344.png)

![image-20230119204604152](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192046226.png)

测试成功

![image-20230119204621910](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192046986.png)

此时构建已经开始

![image-20230119204909097](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192049184.png)





#### 8.4.4 修改脚本

上面看到我们构建失败了，因为之前是自动CD，现在我们要改成自动CI

去除参数化构建，改为构建最新的版本

![image-20230119205209693](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192052777.png)

修改Jenkinsfile

把tag都替换成main

![image-20230119205500773](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192055847.png)

版本修改

![image-20230119205750695](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192057765.png)

修改pipeline.yml文件

![image-20230119210603799](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192106882.png)

需要重新启动K8S容器

```
ssh root@192.168.133.114 kubectl  rollout restart deployment -n test-dev
```

修改Jenkinsfile



![image-20230119211232949](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192112013.png)



8.4.5 测试

![image-20230119211429869](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192114945.png)

![image-20230119211521261](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192115353.png)

![image-20230119211504585](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301192115647.png)
