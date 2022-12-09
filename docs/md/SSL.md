# SSL

## 1**http和https**

HTTPS是一种通过计算机网络进行安全通信的传输协议。

它经由HTTP进行通信，利用SSL/TLS建立全通信，加密数据包，确保数据的安全性。

http协议是明文传输数据，存在安全问题，而https是加密传输，相当于http+ssl，并且可以防止流量劫持。

## 2SSL和TSL

SSL：Secure Socket Layer，安全套接字层协议，分为SSLv2和SSLv3两个版本。

TSL在SSL3.0基础之上提出的安全通信标准化版。主要是为了加密传输数据而产生的协议，能使用户/服务器应用之间的通信不被攻击者窃听，并且始终对服务器进行认证，还可选择对用户进行认证。



上述这两个是为网络通信提供安全及数据完整性的一种安全协议，TLS和SSL在传输层和应用层对网络连接进行加密。

## 3 SSL工作原理

SSL工作原理分为三个协议：

握手协议（Handshake protocol）、记录协议（Record protocol）、警报协议（Alert protocol）。

SSL的基本思想是用非对称加密来建立链接（握手阶段），用对称加密来传输数据（传输阶段）。这样既保证了密钥分发的安全，也保证了通信的效率。

为什么要这么做呢？

保证通信安全是用非对称加密,但是数字证书认证需要双方都认证，存在2大问题：

1）非对称加密速度缓慢，消耗资源 

如果客户端和服务器之间传输文件用非对称加密的话，速度一定慢的忍无可忍。 

 

2）不可能要求每个用户都去申请数字证书 

申请数字证书是一个相当麻烦的过程，要求每个上网的用户都拥有证书是不可能的事情。

SSL通过“握手协议”和“传输协议”来解决上述问题。握手协议是基于非对称加密的，而传输协议是基于对称加密的。根据不同的应用，SSL对证书的要求也是不一样的，可以是单方认证（比如HTTP, FTP），也可以是双方认证（比如网上银行）。通常情况下，服务器端的证书是一定要具备的，客户端的证书不是必须的。



## 4 **Nginx配置SSL**

Nginx要想使用SSL，需要配置SSL证书。有2个办法可以使用.

### 4.1 OpenSSL

查看Openssl version

![image-20221209161936566](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091619817.png)

然后生成SSL证书

   Key是私用秘钥，通常是RSA算法

Csr是证书请求文件，用于申请证书

Crt是CA认证后的证书文件，签署人用自己的key给你签署凭证

 \#建立生成证书的路径

mkdir /home/goldsun/cert && cd /home/goldsun/cert

\#生成rsa私钥，des3算法，openssl格式，2048位强度。

注意：注意：centos版本如果是CentOS8 (Core)版本，私钥长度不能设置成1024位，必须2048位。不然再最后启动nginx时会出如下错误。

nginx: [emerg] SSL_CTX_use_certificate("/root/cert/server.crt") failed (SSL: error:140AB18F:SSL routines:SSL_CTX_use_certificate:ee key too small)

openssl genrsa -des3 -out server.key 2048（输入两次密码，123456）

输入密码，确认密码，记住密码，后面会用到

\#创建服务器证书的申请文件server.csr

openssl req -new -key server.key -out server.csr



需要依次输入国家，地区，组织，email

最重要的是有一个common name，可以写你的名字或者域名，如果为了https申请，这个必须和域名吻合，否则会引发浏览器警报。

Enter pass phrase for root.key: ← 输入前面创建的密码

Country Name (2 letter code) [AU]:CN ← 国家代号，中国输入CN

State or Province Name (full name) [Some-State]:BeiJing ← 省的全名，拼音

Locality Name (eg, city) []:BeiJing ← 市的全名，拼音

Organization Name (eg, company) [Internet Widgits Pty Ltd]: ← 公司英文名

Organizational Unit Name (eg, section) []: ← 可以不输入

Common Name (eg, YOUR name) []: ← 此时不输入

Email Address []: ← 电子邮箱

Please enter the following ‘extra’ attributes

to be sent with your certificate request

A challenge password []: ← 可以不输入

An optional company name []: ← 可以不输入



备份服务器密钥文件

cp server.key server.key.org

去除文件口令

openssl rsa -in server.key.org -out server.key



最后一步，生成证书文件server.crt

openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

### 4.2阿里云获取SSL证书

登录之后找到SSL购买界面，购买免费型DV SSL。

![image-20221209162204734](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091622775.png)

申请成功之后，之后配置需要用到pem和key文件，相应的文件下载在"已签发"这块，点击下载，选择Nginx版本下载之后就可以了。

.crt文件：是证书文件，crt是pem文件的扩展名（有时候没有crt只有pem的，所以不要惊讶）

.key文件：证书的私钥文件（申请证书时如果没有选择自动创建CSR，则没有该文件）

.pem扩展名的证书文件采用Base64-encoded的PEM格式文本文件，可根据需要修改扩展名。

然后修改Nginx的SSL配置，然后重启Nginx服务即可。

## 5 **Nginx开启SSL**

![image-20221209162245169](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091622203.png)

nginx -s reload

systemctl restart nginx

## 6 **开启http自动跳转https**

在nginx中设置两个server，一个保证http访问从80被转发443，需要配置自动转https。

![image-20221209162337000](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212091623037.png)

当我们在浏览器输入URL，自动跳转到HTTPS