# Centos7 Mini无法使用TCPDUMP

## 1、问题描述

安装了Centos7 Mini但是无法使用TCPDUMP

报错：

## 2、解决步骤：

### 2.1 安装GCC

解压centos7的ISO镜像，去里面找gcc缺少的安装包。然后复制到新的文件夹gcc，把文件夹上传到服务器

然后cd gcc

rpm -Uvh --force --nodeps *.rpm

安装完之后我们要去验证下是否安装成功了吧，输入，并执行：

 gcc  -v；

可以输入并执行以下命令，查看是否缺少gcc，gcc-c++环境安装包！

​     rpm  -qa|grep  gcc；

​     rpm  -q  gcc  rpm  -q  gcc-c++  rpm  -q  make； 

### 2.2 安装perl-5.30.1

 cd perl-5.30.1
./Configure -des -Dprefix=$HOME/localperl
 make && make install

### 2.3 opensll

 下载openssl-1.1.1e并解压缩

 cd openssl-1.1.1e
 ./config shared --openssldir=/usr/local/openssl --prefix=/usr/local/openssl
 make && make install

```
echo "/usr/local/lib64/" >> /etc/ld.so.conf
ldconfig
```

再次使用`openssl version`

找一下库`libcrypto.so 的位置：`

```
find / -name libcrypto.so
```

配置环境变量：

cd /etc/

vim ld.so.conf

添加`libcrypto.so 所在文件路径：`

再执行：ldconfig命令