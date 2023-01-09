# Jenkins

## 1 安装Jenkins

下载Jenkins

docker pull jenkins/jenkins:2.319.1-lts

编辑docker-compose.yml

version: '3.1'
services:
  jenkins:
    restart: always
    image: jenkins/jenkins:2.319.1-lts
    container_name: jenkins
    ports:

   - 9001:8080
     50001:50000
     volumes:
        - ./data:/var/jenkins_home/

然后docker-compose up -d



![image-20221226223652576](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262236616.png)

插件管理

搜索Git Parameter

![image-20221226223800363](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262238400.png)

![image-20221226223946831](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212262239859.png)

## 2 配置

把maven、jdk移动

cd /usr/local/docker/jenkins_docker/data

mv /usr/local/maven/ ./

mv /usr/java/jdk1.8.0_51 ./

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





## 3 pipeline任务构建

### 3.1 为什么要pipeline（流水线）

流水线Pipeline，简而言之，就是一套运行于Jenkins上的工作流框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂流程编排与可视化；
Pipeline 是Jenkins 2.X 的最核心的特性，帮助Jenkins 实现从CI 到 CD 与 DevOps的转变。
Pipeline 是一组插件，让jenkins 可以实现持续交付管道的落地和实施。持续交付管道是将软件从版本控制阶段到交付给用户/客户的完整过程的自动化表现。

### 32 创建简单任务

![image-20230108111532530](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081115603.png)

![image-20230108111611328](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081116394.png)

![image-20230108111846825](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081118883.png)

![image-20230108111915233](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081119291.png)

![image-20230108111936955](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081119016.png)



### 3.3 基本语法

![image-20230108135925067](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081359130.png)

//所有的脚本命令都放在pipeline中
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

  



![image-20230108140455776](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081404838.png)

![image-20230108140604319](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081406387.png)

使用流水线语法

![image-20230108140847619](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081408689.png)

![image-20230108145345095](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081453161.png)

![image-20230108145505590](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081455658.png)

点击生成流水线脚本

![image-20230108145602394](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081456480.png)





### 3.4 jenkinsfile维护脚本

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



### 3.5 拉取git代码

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



### 3.6 pipeline-maven

在流水线语法使用

![image-20230108154052735](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301081540806.png)

编辑项目 pipeline - 流水线 - 流水线语法 - 片段生成器 

/var/jenkins_home/maven/bin/mvn clean package -DskipTests

![image-20230108212729563](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082127710.png)

修改脚本

![image-20230108215414352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082154446.png)

再次构建

![image-20230108215604971](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082156043.png)



### 3.7 pipeline-soanrqube代码质量

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



### 3.8 pipeline-Docker自定义镜像

准备脚本

mv ./target/*.jar ./docker/
docker build -t ${JOB_NAME}:${tag} ./docker/

![image-20230108221521167](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082215244.png)

![image-20230108221637487](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082216553.png)

提交后再次构建

![image-20230108221736589](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082217656.png)

![image-20230108221829560](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082218615.png)



### 3.9 pipeline-harbor仓库

修改Jenkinsfile,增加全局变量

​        harboUser = 'admin'
​        harborPass = 'Harbor12345'
​        harborAddress = '192.168.133.113:80'
​        harborRepo = 'repo'

![image-20230108222105097](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082221157.png)



 sh '''docker login -u ${harboUser} -p ${harborPass} ${harborAddress} docker tag ${JOB_NAME}:${tag} ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag} docker push ${harborAddress}/${harborRepo}/${JOB_NAME}:${tag}'''

![image-20230108222449621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082224683.png)

![image-20230108222515322](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082225379.png)

### 3.10 通知目标服务器构建项目

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



### 3.11 钉钉通知消息

下载钉钉插件

![image-20230108230714341](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082307405.png)

![image-20230108231346643](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082313708.png)

![image-20230108231359979](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082314036.png)

添加成功后，复制 Webhook 地址，在配置 Jenkins 时使用

![image-20230108231427078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301082314137.png)

在jenkinsfile中添加完成后的操作

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