# Gitlab

## 前言

自建gitlab代码托管平台，让代码管理更加方便

## 一、gitlab是什么？

GitLab 是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的Web服务。

## 二、搭建使用步骤

### 1.下载安装包

首先去下载gitlab社区版的RPM包，下面是清华大学的镜像

https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el8/?C=M&O=D

### 2.配置环境

服务器是centos8,命令如下：
dnf install -y curl policycoreutils openssh-server openssh-clients

yum install policycoreutils-python-utils
`

### 3.配置URl

### 4.配置防护墙端口，开放8082端口

firewall-cmd --zone=public --add-port=8082/tcp --permanent

firewall-cmd --reload

### 5.修改密码

输入命令：gitlab-rails console -e production

### 6.使用控制台输出日志

查看所有的logs; 按 Ctrl-C 退出

sudo gitlab-ctl tail

拉取/var/log/gitlab下子目录的日志

sudo gitlab-ctl tail gitlab-rails

拉取某个指定的日志文件

sudo gitlab-ctl tail nginx/gitlab_error.log


