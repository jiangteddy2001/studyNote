# ZooKeeper

## 1 zookeeper概述

### 1.1 什么是zookeeper

zookeeper是一种分布式协调服务，用于管理大型主机。

在分布式环境中协调和管理服务是一个复杂的过程，zookeeper通过其简单的架构和API解决了这个问题。zookeeper允许开发人员专注于核心业务逻辑开发，而不必担心应用的分布式特性。

### 1.2  zookeeper的应用场景

- 分布式协调组件
  在分布式系统中，需要有zookeeper作为分布式协调组件，协调分布式系统中的状态
- 分布式锁
  zk在实现分布式锁上，可以做到强一致性（顺序一致性），在后文ZAB协议中会详细介绍zk实现分布式锁、
- 无状态化实现
  将分布式环境中多个节点的登录状态信息维护在zk数据中心中，实现了多个节点的无状态化


![img](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/9e61291d5fc816920969d3d947401fb6.png)

## 2 zookeeper服务器搭建

### 2.1 安装zookeeper

准备一台虚拟机（或linux服务器），安装jdk，配置java运行环境
官网下载指定版本的zookeeper压缩包，apache-zookeeper-3.7.0-bin.tar.gz。官网地址：https://zookeeper.apache.org/
zookeeper压缩包上传到服务器上，解压缩

```
cd /usr/local/
mkdir zookeeper

tar -zxvf apache-zookeeper-3.7.1-bin.tar.gz
mv apache-zookeeper-3.7.1-bin/ /usr/local/zookeeper/
cd /usr/local/zookeeper/apache-zookeeper-3.7.1-bin/
```

进到conf目录中，执行命令：

```
cp zoo_sample.cfg zoo.cfg  # 拷贝一份cfg配置文件，新文件命名为zoo.cfg
```

![image-20230218152906060](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218152906060.png)

修改zoo.cfg配置文件

```
# zookeeper时间配置中的基本单位（毫秒）
tickTime=2000
# 允许follower初始化连接到leader的最大时长，它表示tickTime的时间倍数，即：initLimit*tickTime
initLimit=10
# 允许follower与leader数据同步最大时长，它表示tickTime的时间倍速，即：syncLimit*tickTime
syncLimit=5
# zookeeper数据存储目录及日志保存目录（如果没有指明dataLogDir，则日志也保存在这个文件中）
dataDir=/usr/local/zookeeper/zkdata
# 对客户端提供的端口号
clientPort=2181
# 单个客户端与zookeeper最大并发连接数
maxClientCnxns=60
# 保存的数据快照数量，之外的将会被清除
autopurge.snapRetainCount=3
# 自动触发清除任务时间间隔，单位为小时，默认为0，表示不自动清除
autopurge.purgeInterval=1

```

### 2.2 启动zookeeper

```
vi  ~/.bashrc 

#编写内容
export ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.7.1-bin
export PATH=$ZOOKEEPER_HOME/bin:$PATH

# 环境变量生效
source  ~/.bashrc
```

启动命令

```
# 开启服务端
zkServer.sh start ../conf/zoo.cfg
# 查看状态
zkServer.sh status ../conf/zoo.cfg

# 停止服务
zkServer.sh stop ../conf/zoo.cfg

# 客户端
zkCli.sh

```

![image-20230218154645154](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218154645154.png)

## 3 zookeeper内部数据结构

### 3.1 zk是如何保存数据的

zk中数据是保存在节点上的，节点就是znode，多个znode之间构成了一棵树的目录结构
zk的数据模型是什么样子呢？有点像数据结构中的树，也比较像文件系统的目录。

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/8e7321a2740d6cbd440ba3b3336dd174.png)

根路径为 /。不同于树的节点，znode的引用方式是路径引用，类似于文件路径：

![image-20230218155900736](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218155900736.png)

data：Znode 存储的数据信息。

ACL：记录 Znode 的访问权限，即哪些人或哪些 IP 可以访问本节点。

stat：包含 Znode 的各种元数据，比如事务 ID、版本号、时间戳、大小等等。

child：当前节点的子节点引用.

![image-20230218162123090](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218162123090.png)

这里需要注意一点，Zookeeper 是为读多写少的场景所设计。Znode 并不是用来存储大规模业务数据，而是用于存储少量的状态和配置信息，每个节点的数据最大不能超过 1MB。

这样的层次结构，让每一个znode节点拥有唯一的路径，就像命名空间一样，对不同的信息作出清晰的隔离。
查看zk中的所有节点：

```
[zk: localhost:2181(CONNECTED) 2] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 3] create /test1
Created /test1
[zk: localhost:2181(CONNECTED) 4] ls /
[test1, zookeeper]
[zk: localhost:2181(CONNECTED) 5] create /test1/sub1
Created /test1/sub1
[zk: localhost:2181(CONNECTED) 6] create /test2 abc
Created /test2
[zk: localhost:2181(CONNECTED) 7] get /test2
abc

```

### 3.2 zk中znode

zk中的znode节点，包含了四个部分：

- data：znode存储的业务数据
- acl 权限：
  c：create 创建权限，允许在该节点下创建子节点
  w：write 写/更新权限，允许更新该节点的数据
  r：read 读权限，允许读取该节点的内容以及子节点的列表信息
  d：delete 删除权限，允许删除该节点的子节点
  a：admin 管理者权限，允许对该节点进行acl权限设置
- stat：描述当前znode的元数据/状态信息（事务id、版本号、时间戳）
- child：当前节点的子节点引用
  查看节点的详细信息：

```
get -s /xxx
```

![image-20230218165038854](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218165038854.png)

**节点的详细信息：**

cZxid：创建节点的事务id
mZxid：修改节点的事务id
pZxid：添加或删除子节点的事务id
ctime：节点创建的时间
mtime：节点最近修改的时间
dataVersion：节点内数据的版本，每更新一次，版本+1
aclVersion：此节点的权限版本
ephemeralOwner：如果当前节点是临时节点，该值是当前节点所有者的session id。如果不是临时节点，则该值为0
dataLength：节点内数据的长度
numChildren：该节点的子节点个数

### 3.3 znode的类型

- 持久节点：创建出节点后，在会话结束后依然存在 【create /xxx】
- 持久序号节点：创建出的节点，根据先后顺序，会在节点之后带上一个数值。越后创建的数值越大，单调递增。适用于分布式锁的应用场景/并发场景 【create -s /xxx】

```
[zk: localhost:2181(CONNECTED) 9] create /test3
Created /test3
[zk: localhost:2181(CONNECTED) 10] create -s /test3
Created /test30000000003
[zk: localhost:2181(CONNECTED) 11] create -s /test3
Created /test30000000004
[zk: localhost:2181(CONNECTED) 12] create -s /test3
Created /test30000000005
[zk: localhost:2181(CONNECTED) 13] 

```

![image-20230218165444868](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218165444868.png)

临时节点：创建之后，在会话结束后，节点会自动被删除。通过这个特性，zk可以实**现服务的注册与发现的效**果（如zk在dubbo中作为注册中心，实现服务注册与发现功能。服务提供者宕机，注册到zk的临时服务节点就会自动删除，消费者不可再调用） 【create -e /xxx】

- 临时节点用途---服务注册与发现

先开一个客户端开启

```
create -e /test5
```

![image-20230218165749621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218165749621.png)

然后再开启另一个客户端。把之前的客户端关掉，刷新新的客户端

![image-20230218165956840](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218165956840.png)

![image-20230218170049656](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218170049656.png)

那么临时节点是如何维持心跳的呢？
![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/fb3fcc0ee2136ef86d769085d682063f.png)

- 临时序号节点：跟持久节点类似，适用于临时的分布式锁 【create -e -s /xxx】

```
[zk: localhost:2181(CONNECTED) 8] create -e -s /test6
Created /test60000000008
[zk: localhost:2181(CONNECTED) 9] ls /
[test1, test2, test3, test30000000003, test30000000004, test30000000005, test30000000006, test60000000008, zookeeper]
[zk: localhost:2181(CONNECTED) 10] get -s /test60000000008
```

![image-20230218170527266](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218170527266.png)

- Container节点（3.5.3版本新增）：Container容器节点，当容器中没有任何子节点，该容器节点会被zk定期删除（60s）--命令 create -c

先创建一个容器节点，再在它下面创建2个子节点

```
[zk: localhost:2181(CONNECTED) 11] create -c /mycontainer
Created /mycontainer
[zk: localhost:2181(CONNECTED) 12] ls /
[mycontainer, test1, test2, test3, test30000000003, test30000000004, test30000000005, test30000000006, test60000000008, zookeeper]
[zk: localhost:2181(CONNECTED) 13] create /mycontainer/sub1
Created /mycontainer/sub1
[zk: localhost:2181(CONNECTED) 14] create /mycontainer/sub2
Created /mycontainer/sub2

```

然后删除这2个子节点，观察容器是否会60s消失

```
[zk: localhost:2181(CONNECTED) 15] delete /mycontainer/sub1
[zk: localhost:2181(CONNECTED) 16] delete /mycontainer/sub2
```

测试结果：

![image-20230218171431257](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218171431257.png)

- TTL节点：可以指定节点的到期时间，到期后被zk删除，只能通过系统配置 zookeeper.extendedTypesEnabled=true开启

### 3.4 zk的数据持久化

zk的数据是运行在内存中的，zk提供了两种持久化机制：

- 事务日志
  zk把执行的命令以日志的形式保存在dataLogDir指定的路径的文件中（如果没有指定dataLogDir，则按dataDir指定的路径）
- 数据快照
  zk会在一定的时间间隔内生成一次内存数据的快照，把该时刻的内存数据保存在快照文件中

![image-20230218172651182](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218172651182.png)

![image-20230218172828213](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/image-20230218172828213.png)

```
zk通过两种形式的持久化，在恢复时先恢复快照文件中的数据到内存中，再用日志文件中的数据做增量恢复。这样的恢复速度更快
```

## 4 zookeeper客户端

### 4.1 多节点类型的创建

- 创建持久节点 —— ***create /xxx\***
- 创建持久序号节点 —— ***create -s /xxx\***
- 创建临时节点 —— ***create -e /xxx\***
- 创建临时序号节点 —— ***create -e -s /xxx\***
- 创建容器节点 —— ***create -c /xxx\***

### 4.2 查询节点

- 普通查询
  - ls /xxx _（查询/xxx下的一级子节点）
  - ***ls -R /xxx\*** （查询/xxx下的所有子节点）
  - ***get /xxx\*** （查询/xxx节点上的数据）

- 查询节点信息 —— ***get -s /xxx\*** （查询/xxx节点的所有信息）

  cZxid：创建节点的事务id
  mZxid：修改节点的事务id
  pZxid：添加或删除子节点的事务id
  ctime：节点创建的时间
  mtime：节点最近修改的时间
  dataVersion：节点内数据的版本，每更新一次，版本+1
  aclVersion：此节点的权限版本
  ephemeralOwner：如果当前节点是临时节点，该值是当前节点所有者的session id。如果不是临时节点，则该值为0
  dataLength：节点内数据的长度
  numChildren：该节点的子节点个数

### 4.3 删除节点

- 普通删除
  delete /xxx
  deleteall /xxx
- 乐观锁删除
  delete -v dataVersion(节点的数据版本号) /xxx 【只有指定删除的数据版本 == 当前节点的数据版本号，才能够删除成功】，每对节点进行一次数据操作，节点的dataVersion就会+1。这样删除，以乐观锁的机制，可保证并发下数据操作的唯一性

```
[zk: localhost:2181(CONNECTED) 4] create /test2

[zk: localhost:2181(CONNECTED) 7] set /test2 abc
[zk: localhost:2181(CONNECTED) 8] get -s /test2

# 数据版本号+1
```

![image-20230219151704362](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302191517521.png)

乐观锁是验证，不是阻止

![image-20230219152355964](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302191523025.png)




### 4.4 权限设置

- 注册当前会话的账号和密码：

```
addauth digest baixiong:123456
```

 创建节点，并设置权限

```
# 创建一个节点/test-node，节点数据为abcd，此节点只能由账号为xiangwang，密码为123456的用户操作，可进行创建、删除、写、读、权限设置操作
create /test-node abcd auth:baixiong:123456:cdwra
```

在另一个会话中，必须先使用账号密码，才能拥有操作节点的权限

![image-20230219152812967](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302191528004.png)

![image-20230219152852390](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302191528429.png)



## 5 Curator客户端的使用

### 5.1 Curator介绍

Curator是Netflix公司开源的一套zookeeper客户端框架，Curator是对zookeeper支持最好的客户端框架。Curator封装了大部分zookeeper的功能，比如leader选举、分布式锁等，减少了技术人员在使用zookeeper时的底层细节开发工作。
在Java程序中使用zookeeper，可以引入Curator



### 5.2 配置

引入依赖

```
<!-- Curator -->
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-framework</artifactId>
  <version>2.12.0</version>
</dependency>
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-recipes</artifactId>
  <version>2.12.0</version>
</dependency>
<!-- zookeeper -->
<dependency>
  <groupId>org.apache.zookeeper</groupId>
  <artifactId>zookeeper</artifactId>
  <version>3.7.0</version>
</dependency>

```

配置文件

```
# 重试次数
curator.retryCount=5
# 临时节点的超时时间
curator.elapsedTimeMs=5000
# 连接地址 
curator.connectionString=192.168.133.103:2181
# session超时时间
curator.sessionTimeoutMs=60000
# 连接超时时间
curator.connectionTimeoutMs=5000
```



```
package com.jiang.zkclient.config;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.RetryNTimes;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月20日 上午10:42:11
* @DESCRIPTION :
*/
@Configuration
public class CuratorConfig {
	
	@Autowired
  WrapperZK wrapperZK;
	
	@Bean(initMethod="start")
	public CuratorFramework curatorFramework() {
		
		return CuratorFrameworkFactory.newClient(
				wrapperZK.getConnectionString(), 
				wrapperZK.getSessionTimeoutMs(), 
				wrapperZK.getConnectionTimeoutMs(), 
				new RetryNTimes(wrapperZK.getRetryCount(), wrapperZK.getElapsedTimeMs()));
	}

}

```

```
package com.jiang.zkclient.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;



/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月20日 上午10:35:07
* @DESCRIPTION :
*/

@Component
@ConfigurationProperties(prefix="curator")
public class WrapperZK {
	
	private int retryCount;
	
	private int elapsedTimeMs;
	
	private String connectionString;
	
	private int sessionTimeoutMs;
	
	private int connectionTimeoutMs;

	public int getRetryCount() {
		return retryCount;
	}

	public void setRetryCount(int retryCount) {
		this.retryCount = retryCount;
	}

	public int getElapsedTimeMs() {
		return elapsedTimeMs;
	}

	public void setElapsedTimeMs(int elapsedTimeMs) {
		this.elapsedTimeMs = elapsedTimeMs;
	}

	

	public String getConnectionString() {
		return connectionString;
	}

	public void setConnectionString(String connectionString) {
		this.connectionString = connectionString;
	}

	public int getSessionTimeoutMs() {
		return sessionTimeoutMs;
	}

	public void setSessionTimeoutMs(int sessionTimeoutMs) {
		this.sessionTimeoutMs = sessionTimeoutMs;
	}

	public int getConnectionTimeoutMs() {
		return connectionTimeoutMs;
	}

	public void setConnectionTimeoutMs(int connectionTimeoutMs) {
		this.connectionTimeoutMs = connectionTimeoutMs;
	}
	
	

}

```



- 节点监听事件

```

```

测试类

```
package com.jiang.zkclient;

import javax.annotation.Resource;

import org.apache.curator.framework.CuratorFramework;
import org.apache.zookeeper.CreateMode;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;


/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月20日 上午10:49:10
* @DESCRIPTION :
*/
@SpringBootTest(classes = BootZkClientApplication.class)
public class BootZkClientApplicationTest {
	
	 @Autowired
	 private CuratorFramework curatorFramework;
	 
	 @Test
	 public void createNode() throws Exception {
		 //创建一个持久节点
		 //String path =curatorFramework.create().forPath("/curator-node");
		 
		 
		 //创建一个临时节点
		 String path =curatorFramework.create().withMode(CreateMode.EPHEMERAL_SEQUENTIAL).forPath("/curator-node","some-data".getBytes());
		 System.out.println(String.format("curator create node :%s successfully!", path));
		 System.in.read();
	 }
	 
	 @Test
	 public void testGetData() throws Exception {
		 byte[] bytes = curatorFramework.getData().forPath("/curator-node");
		 System.out.println(new String(bytes));
		 
	 }
	 
	 @Test
	 public void testSetData() throws Exception {
		 curatorFramework.setData().forPath("/curator-node", "changed!".getBytes());
		 byte[] bytes = curatorFramework.getData().forPath("/curator-node");
		 System.out.println(new String(bytes));
		 
	 }
	 
	 @Test
	 public void testCreateWithParent() throws Exception {
		 String pathWithParent="/node-parent/sub-node-1";
		 String path =curatorFramework.create().creatingParentContainersIfNeeded().forPath(pathWithParent);
		 System.out.println(String.format("curator create node :%s successfully!", path));
	 }
	 
	 @Test
	 public void testDelete() throws Exception {
		String pathWithParent="/node-parent";
		curatorFramework.delete().guaranteed().deletingChildrenIfNeeded().forPath(pathWithParent);
	 }
	
}

```



## 6 zookeeper实现分布式锁

### 6.1 锁的种类

- 读锁：大家都可以读。想上读锁的前提是：上锁之前的，所有锁中没有写锁
- 写锁：只有得到写锁才能写数据。想上写锁的前提是：上锁之前，没有任何锁

读读共享，读写互斥，写写互斥

读锁共享，写锁排他

### 6.2 ZK如何上读锁

- 创建一个临时序号节点，节点的数据是read，表示是读锁
- 获取当前zk中序号比自己小的所有节点
  - 判断最小节点是否是读锁：
  - 如果是读锁的话，则上锁成功
  - 如果是写锁，则上锁失败。为最小节点设置监听，阻塞等待，zk的watch机制会当最小节点发生变化时通知当前节点，于是再次执行第二步的流程
    

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201119139.png)

### 6.3 如何上写锁

- 创建一个临时序号节点，节点的数据是write，表示是写锁

- 获取zk中所有的子节点

  - 判断自己是否是最小的节点

    ：

    - 如果是，则上锁成功
    - 如果不是，说明前面还有锁，则上锁失败。监听最小的节点，如果最小节点发生变化，则回到第二步



### 6.4 羊群效应

如果用上述的上锁方式，只要节点发生变化，就会触发其他节点的监听事件，这样的话对zk的压力非常大，这就是所谓的羊群效应（惊群效应）。
***可以调整为链式监听***，解决这个问题。

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201233733.png)

### 6.5 Curator实现读写锁

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201233112.png)

获取写锁

```
// 将上面的获取读锁改成获取写锁
InterProcessLock interProcessLock = interProcessReadWriteLock.writeLock();
```

```
package com.jiang.zkclient;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.recipes.locks.InterProcessLock;
import org.apache.curator.framework.recipes.locks.InterProcessReadWriteLock;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

/**
* @author 作者 jiangbaixiong
* @version 创建时间：2023年2月20日 下午3:52:45
* @DESCRIPTION :测试读写锁
*/
@SpringBootTest
@RunWith(SpringRunner.class)
public class TestReadWriteLock {
	
	@Autowired
	CuratorFramework client;
	
	@Test
	public void testGetReadLock() throws Exception {
		InterProcessReadWriteLock interProcessReadWriteLock = new InterProcessReadWriteLock(client ,"/lock1");
		InterProcessLock interProcessLock = interProcessReadWriteLock.readLock();
		System.out.println("等待获取读锁对象");
		interProcessLock.acquire();
		for (int i = 1; i < 100; i++) {
			Thread.sleep(3000);
			System.out.println(i);
		}
		interProcessLock.release();
		System.out.println("等待释放读锁对象");
	}
	
	@Test
	public void testGetWriteLock() throws Exception {
		InterProcessReadWriteLock interProcessReadWriteLock = new InterProcessReadWriteLock(client ,"/lock1");
		InterProcessLock interProcessLock = interProcessReadWriteLock.writeLock();
		System.out.println("等待获取写锁对象");
		interProcessLock.acquire();
		for (int i = 1; i < 100; i++) {
			Thread.sleep(3000);
			System.out.println(i);
		}
		interProcessLock.release();
		System.out.println("等待释放写锁对象");
	}

}

```

## 7 zookeeper的watch机制

### 7.1 机制介绍

可以把watch机制理解成注册在znode上的触发器。当znode发生变化了（调用了create、delete、setData等方法时），将会触发znode上注册的对应事件，请求znode的客户端会接收到异步通知。
具体交互过程：

- 客户端调用getData方法，watch参数设置为true（即客户端监听节点的变化）。服务端接收到请求，返回节点数据，并且在对应的哈希表中插入被watch的znode路径，以及watcher列表。
  - 监听节点数据变化：get -w /xxx
  - 当节点数据发生变化了，客户端会有监听信息的打印
  - 注意：客户端的监听只生效一次。如果想持续监听，需要在每次监听信息打印后，查看数据的时候，再使用：get -w /xxx
    （get -w /xxx 的方式）在被监听的节点上创建子节点，watch监听事件不会被触发

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201235058.png)

- 当被watch的znode已删除，服务端会查找哈希表，找到znode对应的所有watcher，异步通知客户端，并删除哈希表中对应的key-value。

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201235693.png)

客户端使用了NIO的通信模式监听服务端的调用。

### 7.2 zkCli客户端使用watch

```
create /xxx abc  # 创建持久节点/xxx，并设置节点数据为abc
get -w /xxx # 一次性监听节点
ls -w /xxx # 监听目录，创建和删除子节点会收到通知，子节点中新增节点不会收到通知
ls -R -w /xxx # 监听节点所有层级子节点的变化，但内容的变化不会收到通知
```

![image-20230220153730280](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201537356.png)

![image-20230220154020996](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201540046.png)

![image-20230220154237494](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201542541.png)

### 7.3  Curator客户端使用watch

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201236119.png)

```
@Test
	public void addNodeListener() throws Exception {
		NodeCache nodeCache = new NodeCache(curatorFramework, "/curator-node");
		nodeCache.getListenable().addListener(new NodeCacheListener() {

			@Override
			public void nodeChanged() throws Exception {
				System.out.println("path node changed");
				printNodeData();
			}
		});

		nodeCache.start();
		System.in.read();

	}

	public void printNodeData() throws Exception {
		byte[] bytes = curatorFramework.getData().forPath("/curator-node");
		System.out.println(new String(bytes));
	}
```



## 8 zookeeper集群

### 8.1 集群角色

zookeeper集群中的节点有三种角色：

- leader：处理集群的所有事务请求，一般进行写请求处理，集群中只有一个leader
- follower：只能处理读请求，可参与leader选举
- observer：只能处理读请求，提高集群读的性能，但不能参与leader选举

### 8.2  伪集群搭建

如果已经启动服务了，必须先停止zookeeper服务

```
zkServer.sh stop
```

进入zkdata文件夹

```
cd /usr/local/zookeeper/zkdata

[root@localhost zookeeper]# cd zkdata/
[root@localhost zkdata]# mkdir zk1
[root@localhost zkdata]# mkdir zk2
[root@localhost zkdata]# mkdir zk3
[root@localhost zkdata]# mkdir zk4
```

搭建4个节点，其中一个节点为observer

```
# 在/usr/local/zookeeper中创建四个文件
/usr/local/zookeeper/zkdata/zk1# echo 1 > myid
/usr/local/zookeeper/zkdata/zk2# echo 2 > myid 
/usr/local/zookeeper/zkdata/zk3# echo 3 > myid
/usr/local/zookeeper/zkdata/zk4# echo 4 > myid

```

编写4个zoo.cfg

```
# 不同的zoo.cfg文件中需要更改两处：dataDir和clientPort



dataDir=/usr/local/zookeeper/zkdata/zk1  # 数据日志存放路径
clientPort=2181 # 提供给客户端通信的端口
# 2001,2002,2003,2004是用于服务节点之间通信的
# 3001，3002，3003，3004是用于投票选举leader的端口
server.1=192.168.133.103:2001:3001
server.2=192.168.133.103:2002:3002
server.3=192.168.133.103:2003:3003
server.4=192.168.133.103:2004:3004:observer

```

举例，修改zoo2.cfg

![image-20230220173154240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201731304.png)



启动4个服务节点

```
[root@localhost bin]# zkServer.sh start ../conf/zoo1.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo1.cfg
Starting zookeeper ... STARTED
[root@localhost bin]# zkServer.sh start ../conf/zoo2.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo2.cfg
Starting zookeeper ... STARTED
[root@localhost bin]# zkServer.sh start ../conf/zoo3.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo3.cfg
Starting zookeeper ... STARTED
[root@localhost bin]# zkServer.sh start ../conf/zoo4.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo4.cfg
Starting zookeeper ... STARTED

```

查看集群的角色

```
[root@localhost bin]# zkServer.sh status ../conf/zoo1.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo1.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
[root@localhost bin]# zkServer.sh status ../conf/zoo2.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo2.cfg
Client port found: 2182. Client address: localhost. Client SSL: false.
Mode: leader
[root@localhost bin]# zkServer.sh status ../conf/zoo3.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo3.cfg
Client port found: 2183. Client address: localhost. Client SSL: false.
Mode: follower
[root@localhost bin]# zkServer.sh status ../conf/zoo4.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo4.cfg
Client port found: 2184. Client address: localhost. Client SSL: false.
Mode: observe
```

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201734450.png)

### 8.3 客户端连接zookeeper集群

```
[root@localhost ~]# zkCli.sh -server 192.168.133.103:2181,192.168.133.103:2182,192.168.133.103:2183
```

![image-20230220173722397](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201737475.png)



## 9 ZAB协议

### 9.1 什么是ZAB协议

zookeeper作为非常重要的分布式协调组件，需要进行集群部署，集群中会以一主多从的形式进行部署。zookeeper为了保证数据一致性，使用了ZAB协议（zookeeper atomic broadcast），这个协议解决了zookeeper的崩溃恢复和主从数据同步的问题。
![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201601069.png)



### 9.2 ZAB协议定义的四种节点状态

- looking：选举状态
- following：follower节点（从节点）所处的状态
- leading：leader节点（主节点）所处的状态
- observer：观察者节点所处的状态

### 9.3 leader选举机制

zookeeper集群中的节点上线时，将会进入looking状态，即leader选举状态，会经历过程如下：

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201602196.png)

- 选票格式（由两部分组成）
  myid：每个节点初始配置的myid值
  zXid：事务id，节点上每进行一次事务的增删改操作，zXid就会+1
- 第一轮投票：
  两个节点根据自己的myid和zXid生成一张自己的选票
  然后将各自持有的选票投递给对方
  这样，两个节点都各自拥有两张选票。然后各自按照（先比较zXid，取出较大者；如果zXid相同，再比较myid，取出较大者）的规则，选出一张选票，投到投票箱中
  - 为什么先比较zXid？ —— 因为zXid较大者，代表节点上的数据更新次数较多，更适合作为leader。而且后面，经历过数据同步，最终，各个节点上的数据都会保持一致
  - 第一轮投票结束。投票箱中产生了各个节点的选票（node-1中（2，0）；node-2中（2，0））
- 第二轮投票：—— 由于对于一个节点的来说，投票箱中的票数没有过半（3个参与投票的节点，过半票数为2，目前节点中的票数为1，1<2，所以需要进行第二轮投票）
  各个节点，将上一轮手中较大的选票投递给对方
  各自按照（先比较zXid，取出较大者；如果zXid相同，再比较myid，取出较大者）的规则，选出一张选票，投到投票箱中
  第二轮投票结束。投票箱中产生了各个节点的选票（node-1中（2，0）（2，0）；node-2中（2，0）（2，0））
- 此时，投票箱中有票数过半的节点，该节点确定为leader，选票结束
- 第三台节点启动后，发现集群中已经选举出了leader，于是把自己作为follower了
  总结：zookeeper集群中，一般设置奇数节点比较好，leader选举的时候，比较好判断票数过半的节点

### 9.4 崩溃恢复时的leader选举

leader建立完后，leader周期性地不断向follower发送心跳（ping命令，没有内容地socket）。当leader崩溃后，follower发现socket通道关闭，于是follower就会从following状态切换到looking状态，重新回到第3节中地leader选举状态。
此时集群不能对外提供服务。

### 9.5 主从服务器之间的数据同步

![image.png](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202302201603674.png)

- leader收到半数以上follower的ack，就发送commit（向所有follower，和自己）
  - 为什么要半数以上？ —— 半数以上的好处：提升整个集群写数据的性能。因为集群中3台节点，有两台都写成功了，说明网络通信基本正常，集群能够持续提供服务
  - 半数，指的是整个集群所有节点的半数
- 也可以理解成分布式事务中的两阶段提交
  

### 9.6 zookeeper中的NIO和BIO的应用

zookeeper在3.1之后的版本用的Netty

- NIO
  用于被客户端连接的2181端口，使用的是NIO模式与客户端建立连接
  客户端开启watch时，也使用NIO，等待zookeeper服务器的回调
- BIO
  集群在选举时，多个节点之间的投票通信端口，使用的是BIO进行通信

### 9.7 zookeeper的一致性问题

*zookeeper在数据同步时，追求的并不是强一致性，它保证的是顺序一致性（通过事务id的单调递增）*



## 10 CAP理论、Base理论

### 10.1 CAP

一个分布式系统中最多只能同时满足一致性、可用性、分区容错性三项中的两项（CP、或者AP）

- 一致性
  即更新操作成功并返回客户端完成后，所有节点在同一时刻的数据完全一致
- 可用性
  即服务一直可用，而且是正常响应时间
- 分区容错性
  即分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性或可用性的服务。避免单点故障，就要进行冗余部署，冗余部署相当于是服务的分区，这样的分区就具备了容错性。

### 10.2  BASE理论

BASE理论是对CAP理论的延伸，核心思想：即使无法做到强一致性，但应用可以采用适合的方式达到最终一致性

- 基本可用（basically available）
  - 指分布式系统在出现故障的时候，允许损失部分可用性，即保证核心可用。（如电商大促时，为了应对访问量激增，部分用户可以会被引导到降级页面，服务处也可能只提供降级服务，这就是损失部分可用性的体现）
- 软状态（soft state）
  - 指允许系统存在中间状态，而该状态不会影响系统的整体可用性（如：支付中）。分布式存储中，一般一份数据至少会有三个副本，允许不同节点间副本同步的延时就是软状态的体现。mysql replication的异步复制也是软状态的一种体现。 
- 最终一致性（eventual consistency）
  - 指系统中的所有数据副本经过一定时间后，最终能够达到一致的状态。弱一致性和强一致性相反，最终一致性是弱一致性的一种特殊情况。