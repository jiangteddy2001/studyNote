# Nginx

## 1 Nginx反向代理

### 1.1名词解释--代理服务器

客户机在发送请求时，不会直接发送给目的主机，而是先发送给代理服务器，代理服务接受客户机请求之后，再向主机发出，并接收目的主机返回的数据，存放在代理服务器的硬盘中，再发送给客户机。

为什么要用代理服务器呢？

1）提高访问速度

由于目标主机返回的数据会存放在代理服务器的硬盘中，因此下一次客户再访问相同的站点数据时，会直接从代理服务器的硬盘中读取，起到了缓存的作用能明显提高请求速度。

2）防火墙作用

由于所有的客户机请求都必须通过代理服务器访问远程站点，因此可在代理服务器上设限，过滤某些不安全信息。

### 1.2 反向代理服务器作用

**反向代理服务器**架设在服务器端，通过缓冲经常被请求的页面来缓解服务器的工作量，将客户机请求转发给内部网络上的目标服务器；

反向代理的优点 : **不暴露真实IP地址,安全性更高.**

通过代理分开了客户端到应用程序服务器端的连接，实现了安全措施。在反向代理之前设置防火墙，仅留一个入口供代理服务器访问。

![image-20221206220531409](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062205450.png)

### 1.3 Nginx反向代理模块

Nginx反向代理模块的指令是由ngx_http_proxy_module模块进行解析。

### 1.4安装nginx

首先我们先安装nginx

![image-20221206220632899](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062206930.png)

### 1.5配置nginx反向代理

步骤：

1、打开nginx.conf

（命令：vi /etc/nginx/nginx.conf）

2、修改配置

3、加入proxy_pass http://127.0.01:8089

4、重启nginx服务器 nginx -s reload

![image-20221206220702185](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062207211.png)

1）指令proxy_pass:该指令用来设置被代理服务器地址，可以是主机名称、IP地址加端口号形式。

2）proxy_set_header 该指令可以更改Nginx服务器接收到的客户端请求的请求头信息，然后将新的请求头发送给代理的服务器

3）proxy_redirect：该指令是用来重置头信息中的"Location"和"Refresh"的值。

1.6 Nginx反向代理系统调优

反向代理值Buffer（"缓冲"）和Cache（"缓存"）。

相同点:两种方式都是用来提供IO吞吐效率，都是用来提升Nginx代理的性能。

不同点:缓冲主要用来解决不同设备之间数据传递速度不一致导致的性能低的问题，缓冲中的数据一旦此次操作完成后，就可以删除。

缓存主要是备份，将被代理服务器的数据缓存一份到代理服务器，这样的话，客户端再次获取相同数据的时候，就只需要从代理服务器上获取，效率较高，缓存中的数据可以重复使用，只有满足特定条件才会删除.

通用的配置如下：

![image-20221206221116816](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062211849.png)

proxy_buffering :该指令用来开启或者关闭代理服务器的缓冲区；

proxy_buffers:该指令用来指定单个连接从代理服务器读取响应的缓存区的个数和大小。

proxy_buffer_size:该指令用来设置从被代理服务器获取的第一部分响应数据的大小。保持与proxy_buffers中的size一致即可，当然也可以更小。

proxy_busy_buffers_size：该指令用来限制同时处于BUSY状态的缓冲总大小。

proxy_temp_path:当缓冲区存满后，仍未被Nginx服务器完全接受，响应数据就会被临时存放在磁盘文件上，该指令设置文件路径（最多三层）

其中/tmp/nginx_proxy_tmp为临时文件所在目录，1表示层级1的目录名为1个数字(0-9),2表示层级2目录名为2个数字(00-99)

proxy_max_temp_file_size 设置临时文件的总大小

proxy_temp_file_write_size：该指令用来设置同时写入临时文件的数据量的总大小。通常设置为8k或者16k。



## 2负载均衡

### 2.1 名词解释

**请求数量按照一定的规则进行分发到不同的服务器处理的规则，就是一种均衡规则**

所以~将服务器接收到的请求按照规则分发的过程，称为负载均衡。

### 2.2 配置

 在http节点下，添加upstream节点。

![image-20221206221317403](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062213432.png)

![image-20221206221334315](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212062213343.png)

现在负载均衡初步完成了。

upstream按照轮询（默认）方式进行负载，每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除.

### 2.3负载均衡策略

weight（权重）

指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。如下所示，10.0.0.88的访问比率要比10.0.0.77的访问比率高一倍。

upstream jryserver{ 

   server 10.0.0.77 weight=5; 

   server 10.0.0.88 weight=10; 

}

 

  ip_hash（访问ip）

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。

 

upstream jryserver{ 

   ip_hash; 

   server 10.0.0.10:8080; 

   server 10.0.0.11:8080; 

}

 

  fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。与weight分配策略类似。

 upstream jryserver{    

   server 10.0.0.10:8080; 

   server 10.0.0.11:8080; 

   fair; 

}

 

url_hash（第三方）

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。



注意：在upstream中加入hash语句，server语句中不能写入weight等其他的参数**，hash_method是使用的hash算法**。

 upstream jryserver{ 

   server 10.0.0.10:7777; 

   server 10.0.0.11:8888; 

   hash $request_uri; 

   hash_method crc32; 

}

upstream还可以为每个设备设置状态值，这些状态值的含义分别如下：

down 表示单前的server暂时不参与负载.

weight 默认为1.weight越大，负载的权重就越大。

max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误.

fail_timeout : max_fails次失败后，暂停的时间。

backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

例子：

upstream jryserver{ #定义负载均衡设备的Ip及设备状态 
   ip_hash; 
   server 10.0.0.11:9090 down; 
   server 10.0.0.11:8080 weight=2; 
   server 10.0.0.11:6060; 
   server 10.0.0.11:7070 backup; 
}