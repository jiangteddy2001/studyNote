# OAuth2

## 1 使用目的

目前接口访问的认证安全，主要针对包括身份认证、API的权限控制。我们选用OAuth2协议来保证数据传输的安全。

## 2 技术选型

技术上采用springboot2+spring-security+jwt实现基于OAuth2授权。

## 3 授权机制

当管理平台发布了API以后，是给第三方使用者用来调用的。如果想调用API的话，需要创建一个调用方应用，同时会颁发一对client_id以及client_secret。

OAuth 就是一种授权机制。数据的所有者告诉系统，同意授权第三方应用进入系统，获取这些数据。系统从而产生一个短期的进入令牌（token），用来代替密码，供第三方应用使用。

（1）令牌是短期的，到期会自动失效，用户自己无法修改。密码一般长期有效，用户不修改，就不会发生变化。

（2）令牌可以被数据所有者撤销，会立即失效。

（3）令牌有权限范围（scope）。对于网络服务来说，只读令牌就比读写令牌更安全。

## 4**OAuth 2.0****原理**

OAuth 2.0 规定了四种获得令牌的流程。我们可以选择最适合自己的那一种，向第三方应用颁发令牌。

关键词：

response_type：code 表示要求返回授权码，token 表示直接返回令牌

client_id：客户端身份标识

client_secret：客户端密钥

redirect_uri：重定向地址

scope：表示授权的范围，read只读权限，all读写权限

grant_type：表示授权的方式，AUTHORIZATION_CODE（授权码）、password（密码）、client_credentials（凭证式）、refresh_token 更新令牌

state：应用程序传递的一个随机数，用来防止CSRF攻击。



## 5 OAuth 2.0模式

- 授权码（authorization-code）---适用于服务端应用之间，常用模式例子（微信公众号开发中授权码模式、统一身份认证单点登录）。
- 隐藏式（implicit）----纯前端应用（移动APP），允许直接给前端令牌。前端应用直接获取 token，response_type 设置为 token，要求直接返回令牌，跳过授权码，WX授权通过后重定向到指定 redirect_uri 。
- 密码式（password）：-----直接给出用户名和密码，风险大。除非该应用一定是可以高度信任的。
- 客户端凭证（client credentials）---适用没有前端的命令行。可以用最简单的方式获取令牌，在请求响应的 JSON 结果中返回 token。应用于API访问

注意，不管哪一种授权方式，第三方应用申请令牌之前，都必须先到系统备案说明自己的身份，然后会拿到两个身份识别码：客户端 ID（client ID）和客户端密钥（client secret）。



## 6 客户端凭证模式讲解

![image-20221209221429420](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092214579.png)

Step 1 - 客户端使用授权服务器进行身份验证，并从令牌端点发出访问令牌请求。

Step 2 - 授权服务器对客户端进行身份验证，并在访问令牌有效和授权时提供访问令牌。

pom文件里主要依赖：

![image-20221209221501299](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092215341.png)

数据库插入基础数据：

![image-20221209221548768](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092215805.png)



认证服务配置：AuthorizationServerConfigurerAdapter

![image-20221209221622951](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092216999.png)

进行资源配置ResourceServerConfigurerAdapter

![image-20221209221709322](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092217352.png)

ClientDetailsServiceConfigurer

我们选择的数据库模式，对应的表

![image-20221209221736523](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092217557.png)

怎么对应的呢？(属于Java的建造者模式)

ClientDetailsServiceConfigurer-->JdbcClientDetailsServiceBuilder--->Set<ClientDetails> clientDetails-->ClientDetails

![image-20221209221811928](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092218963.png)

第一步：通过client_id和client_secret访问获取到TOKEN

![image-20221209221844277](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092218344.png)

token失效时间对应数据库字段

![image-20221209221922155](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092219186.png)

返回的token是JWT格式

## 7 JWT

JWT就是上述流程当中token的一种具体实现方式，其全称是JSON Web Token。

通俗地说，JWT的本质就是一个字符串，它是将用户信息保存到一个Json字符串中，然后进行编码后得到一个JWT token，并且这个JWT token带有签名信息，接收后可以校验是否被篡改，所以可以用于在各方之间安全地将信息作为Json对象传输。

![image-20221209222017800](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092220834.png)

根据TOKEN去访问资源

![image-20221209222054278](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092220315.png)

访问成功后

![image-20221209222130084](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212092221138.png)