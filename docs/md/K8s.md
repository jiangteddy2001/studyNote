K8s

## 1、基本概念

kubernetes具有以下特性：

- **服务发现和负载均衡**
  Kubernetes 可以使用 DNS 名称或自己的 IP 地址公开容器，如果进入容器的流量很大， Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。
- **存储编排**
  Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。
- **自动部署和回滚**
  你可以使用 Kubernetes 描述已部署容器的所需状态，它可以以受控的速率将实际状态 更改为期望状态。例如，你可以自动化 Kubernetes 来为你的部署创建新容器， 删除现有容器并将它们的所有资源用于新容器。
- **自动完成装箱计算**
  Kubernetes 允许你指定每个容器所需 CPU 和内存（RAM）。 当容器指定了资源请求时，Kubernetes 可以做出更好的决策来管理容器的资源。
- **自我修复**
  Kubernetes 重新启动失败的容器、替换容器、杀死不响应用户定义的 运行状况检查的容器，并且在准备好服务之前不将其通告给客户端。
- **密钥与配置管理**
  Kubernetes 允许你存储和管理敏感信息，例如密码、OAuth 令牌和 ssh 密钥。 你可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥。



## 2、架构

工作方式：

Kubernetes **Cluster** **=** N **Master** Node **+** N **Worker** Node：N主节点+N工作节点； N>=1

## 3、安装

- 一台兼容的 Linux 主机。Kubernetes 项目为基于 Debian 和 Red Hat 的 Linux 发行版以及一些不提供包管理器的发行版提供通用的指令
- 每台机器 2 GB 或更多的 RAM （如果少于这个数字将会影响你应用的运行内存)
- 2 CPU 核或更多
- 集群中的所有机器的网络彼此均能相互连接(公网和内网都可以)
- - **设置防火墙放行规则**
- 节点之中不可以有重复的主机名、MAC 地址或 product_uuid。请参见[这里](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#verify-mac-address)了解更多详细信息。
- - **设置不同hostname**
- 开启机器上的某些端口。请参见[这里](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports) 了解更多详细信息。
- - **内网互信**
- 禁用交换分区。为了保证 kubelet 正常工作，你 **必须** 禁用交换分区。





### 3.1 基础环境

安装docker 20.10.7

```
sudo yum remove docker*
sudo yum install -y yum-utils

#配置docker的yum地址
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo


#安装指定版本
sudo yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7 containerd.io-1.4.6

#	启动&开机启动docker
systemctl enable docker --now

# docker加速配置
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://82m9ar63.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

# 查看 docker 版本
docker -v

# 启动 docker
systemctl start docker

# 查看 docker 是否成功, 有 Client 和 Server 即成功
docker version
```

安装基础环境

```
hostnamectl set-hostname k8s-master

#各个机器设置自己的域名
hostnamectl set-hostname xxxx


//将 SELinux 设置为 permissive 模式（相当于将其禁用）
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

#关闭swap
swapoff -a  
sed -ri 's/.*swap.*/#&/' /etc/fstab

#允许 iptables 检查桥接流量
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```



### 3.2 安装kubelet、kubeadm、kubectl

```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
   http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF


sudo yum install -y kubelet-1.20.9 kubeadm-1.20.9 kubectl-1.20.9 --disableexcludes=kubernetes

sudo systemctl enable --now kubelet
```



### 3.3 使用kubeadm引导集群

下载各个机器需要的镜像

```
sudo tee ./images.sh <<-'EOF'
#!/bin/bash
images=(
kube-apiserver:v1.20.9
kube-proxy:v1.20.9
kube-controller-manager:v1.20.9
kube-scheduler:v1.20.9
coredns:1.7.0
etcd:3.4.13-0
pause:3.2
)
for imageName in ${images[@]} ; do
docker pull registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/$imageName
done
EOF

chmod +x ./images.sh && ./images.sh
```

确认镜像是否已下载完毕

```
docker images
```

![image-20230127210314386](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272103446.png)

### 3.4 初始化主节点

命令ip a

找到master的内网IP

#所有机器添加master域名映射，以下需要修改为自己的

```
echo "192.168.133.106  k8smaster" >> /etc/hosts
```



#### 3.4.1 主节点初始化

```
kubeadm init \
--apiserver-advertise-address=192.168.133.106  \
--control-plane-endpoint=k8smaster \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.169.0.0/16
```

注意：--pod-network-cidr不要配置和虚拟机一个网段。改成192.169.0.0/16

其中--service-cidr和--service-cidr、apiserver-advertise-address网络不能重叠

如果出现第一次初始化失败，继续第二次初始化会导致问题：

```
[init] Using Kubernetes version: v1.20.9
[preflight] Running pre-flight checks
	[WARNING SystemVerification]: this Docker version is not on the list of validated versions: 20.10.7. Latest validated version: 19.03
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR Port-6443]: Port 6443 is in use
	[ERROR Port-10259]: Port 10259 is in use
	[ERROR Port-10257]: Port 10257 is in use
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-apiserver.yaml]: /etc/kubernetes/manifests/kube-apiserver.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-controller-manager.yaml]: /etc/kubernetes/manifests/kube-controller-manager.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-kube-scheduler.yaml]: /etc/kubernetes/manifests/kube-scheduler.yaml already exists
	[ERROR FileAvailable--etc-kubernetes-manifests-etcd.yaml]: /etc/kubernetes/manifests/etcd.yaml already exists
	[ERROR Port-10250]: Port 10250 is in use
	[ERROR Port-2379]: Port 2379 is in use
	[ERROR Port-2380]: Port 2380 is in use
	[ERROR DirAvailable--var-lib-etcd]: /var/lib/etcd is not empty
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher

```

解决办法：

```
kubeadm reset
```



成功后，复制成果后的文字如下：

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join k8smaster:6443 --token 49gizy.46399qjobc7fznn7 \
    --discovery-token-ca-cert-hash sha256:492e61e233766bec45b621a6f8c1dd11f3eade5bfff7d8ea779b15e9dc282f80 \
    --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join k8smaster:6443 --token 49gizy.46399qjobc7fznn7 \
    --discovery-token-ca-cert-hash sha256:492e61e233766bec45b621a6f8c1dd11f3eade5bfff7d8ea779b15e9dc282f80 
```



#### 3.4.2 设置.kube/config

复制上面的文字的中间部分

![image-20230127212656778](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272126831.png)

接下来执行

```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

然后用命令kubectl get nodes查看节点

![image-20221218213339818](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212182133328.png)

#### 3.4.3 安装网络组件calico

curl https://docs.projectcalico.org/manifests/calico.yaml -O

kubectl apply -f calico.yaml

报错如下

error: unable to recognize "calico.yaml": no matches for kind "PodDisruptionBudget" in version "policy/v1"

这是k8s不支持当前[calico](https://so.csdn.net/so/search?q=calico&spm=1001.2101.3001.7020)版本的原因, calico版本与k8s版本支持关系可到calico官网查看，可以改成如下

```
curl https://docs.projectcalico.org/v3.20/manifests/calico.yaml -O
kubectl apply -f calico.yaml
```

查看当前版本命令

```
cat calico.yaml | grep image
```

另外跑大量高负载程序，造成cpu soft lockup。

解决办法：

#!/bin/bash
#修改阈值为30，写入文件
echo 30 > /proc/sys/kernel/watchdog_thresh 
#修改阈值为30，临时生效
sysctl -w kernel.watchdog_thresh=30

#修改阈值为30，写入启动文件
grep 'watchdog_thresh' /etc/sysctl.conf
if [ $? -ne 0 ]; then
	echo "kernel.watchdog_thresh=30" >> /etc/sysctl.conf
else
	echo "正常"
fi

#### 3.4.4 查看集群部署了哪些应用

#运行中的应用在docker里面叫容器，在k8s里面叫Pod
kubectl get pods -A

![image-20221218222618434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212182226481.png)

判断所有都处于Ready状态就表示可以使用了

### 3.5 安装子节点

接下来安装另外2台机器，设定域名

```
hostnamectl set-hostname k8s-node1

hostnamectl set-hostname k8s-node2
```

#### 3.5.1 初始化子节点

**如果令牌过期，需要创建新的令牌**

新令牌

kubeadm token create --print-join-command

![image-20221219164003913](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191640131.png)

kubeadm join cluster-endpoint:6443 --token et6dju.4ao09tmc6gu2jlg6     --discovery-token-ca-cert-hash sha256:c04629d3c02f70654b908f055b62bc6d3e1a79cb65fc341c1fc38560864167ab

复制上述命令，在子节点执行

![image-20221219174636863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191746914.png)

#### 3.5.2 检查防火墙

**如果出现这个异常**

![image-20221219174544768](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191745819.png)

error execution phase preflight: couldn't validate the identity of the API Server: Get "https://cluster-endpoint:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": dial tcp 192.168.133.110:6443: connect: no route to host

请检查Master的防火墙是否关闭

执行这个命令：*systemctl disable firewalld --now*

![image-20221219174516552](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191745619.png)

再次执行成功

### 3.6验证集群

```
kubectl get nodes
```

![image-20230127213950615](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272139654.png)

### 3.7 安装可视化界面

#### 3.7.1部署dashboard

在主节点安装

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml

![image-20221219225553209](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192255255.png)



![image-20221219225540361](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192255407.png)

必须等下面2个dashboard都是running状态。

![image-20230127214405982](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272144028.png)

然后设置访问端口

kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard

编辑文本

![image-20221221154750380](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212211547482.png)

type: ClusterIP 改为 type: NodePort

然后执行命令：kubectl get svc -A |grep kubernetes-dashboard

![image-20230127214657351](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272146391.png)

https://192.168.133.106:31590

如果报异常

```
[root@k8s-master ~]# kubectl logs -f  calico-kube-controllers-bcc6f659f-sw7fj -n kube-system
2023-01-22 02:23:50.297 [INFO][1] main.go 84: Loaded configuration from environment config=&config.Config{LogLevel:"info", ReconcilerPeriod:"5m", CompactionPeriod:"10m", EnabledControllers:"node", WorkloadEndpointWorkers:1, ProfileWorkers:1, PolicyWorkers:1, NodeWorkers:1, Kubeconfig:"", HealthEnabled:true, SyncNodeLabels:true, DatastoreType:"kubernetes"}
2023-01-22 02:23:50.299 [INFO][1] k8s.go 228: Using Calico IPAM
W0122 02:23:50.299506       1 client_config.go:541] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
2023-01-22 02:23:50.300 [INFO][1] main.go 105: Ensuring Calico datastore is initialized
2023-01-22 02:24:00.300 [ERROR][1] client.go 255: Error getting cluster information config ClusterInformation="default" error=Get https://10.96.0.1:443/apis/crd.projectcalico.org/v1/clusterinformations/default: dial tcp 10.96.0.1:443: i/o timeout
2023-01-22 02:24:00.300 [FATAL][1] main.go 110: Failed to initialize Calico datastore error=Get https://10.96.0.1:443/apis/crd.projectcalico.org/v1/clusterinformations/default: dial tcp 10.96.0.1:443: i/o timeout

```

dial tcp 10.96.0.1:443: i/o timeout

解决办法：重新安装

--pod-network-cidr不要配置和虚拟机一个网段。改成192.169.0.0/16

其中--service-cidr和--service-cidr、apiserver-advertise-address网络不能重叠



```
看到“你的连接不是私密连接”画面时，直接在键盘上敲击“thisisunsafe”12个字母，谷歌浏览器会自动刷新显示网页。
```

![image-20230127215459433](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272154498.png)

需要令牌

#### 3.7.2 创建用户令牌

创建一个YAML文件

```
vi dash-user.yml

```

内容如下：

```
#创建访问账号，准备一个yaml文件； vi dash.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

```
kubectl apply -f dash-user.yml
```

获取令牌

```
#获取访问令牌
kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
```

复制令牌，备用。在登录界面输入即可

```
eyJhbGciOiJSUzI1NiIsImtpZCI6Im9XcWkzenZmRk1GZUFYS0RCdjg2MUh3V3JCdG1ybzBzbW9UTGltVVdOZUUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXp0czJ6Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIwNzMxOTA3MC1lMGJjLTQxNTctOWIwYS1iYzU5MGRmNTg5MDUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.EhSngHK22hDjuBvNibgQXmFYkyN10k1AvkaWyhn_fACi9qRN1lOgMBbuX9i7ZxGeXvQDVmwwKg9LuwRluaZgfppCk6_MWxBS2G58Enaj7XuUG8pIiwxJgb0o6woK_dfPxaoMFcjwXw1Va_Ei5Y900E00BAz5mYIBZiwYrZmy8Dna809bC_k6S5HD0N0re7qktpSEudUK1dKlXmqKSxVEOsHq0K-UshR6D9o26-lbyQ3PT75oBE5sxUR0RMXQxcgJrcJ4zgHZ2cQ4YliCpnzItuy2wa6E9v8jl-XgCdq65jUpuPzmRlKEMSAousvF31o9hBoZVT3VlGKv4E7Zl89aJA
```

![image-20230127220721104](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272207177.png)

## 4、K8s的核心组件

资源创建方式分2种

用 yaml 配置 在 kubernetes 中创建资源

```
kubectl apply -f xxxx.yaml
```

用 命令行 在 kubernetes 中创建资源

```
# 查看所有命名空间
kubectl get ns
# 创建命名空间
kubectl create ns xxx
# 删除命名空间
kubectl delete ns xxx
```



### 4.1 Namespace

![image-20230127223116049](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272231130.png)

**NameSpace（命名空间）**：用来对集群资源进行划分；默认只隔离资源，不隔离网络。

kube-nod-lease、kube-public、kube-system 命名空间在装 kubernetes 就自带安装了，

kubernetes-dashboard 命名空间 在装了 dashboard 时候 才创建的。

#### 4.1.1 命令行创建

```
# 查看命名空间
kubectl get ns

# 查看 部署了哪些应用
kubectl get pods -A
# 查看 默认命名空间（default）部署的应用
kubectl get pods

# 查看 某个命名空间（kubernetes-dashboard）部署的应用
kubectl get pod -n kubernetes-dashboard

# 创建命名空间
kubectl create ns hello
# 删除命名空间
kubectl delete ns hello
```

#### 4.1.2 yaml 创建

```
# 版本号
apiVersion: v1
# 类型
kind: Namespace
# 元数据
metadata:
  # Namespace（命名空间） 的 名称
  name: hello-yaml
```



```
vi hello-yaml.yaml

# 将上述内容 粘贴进 hello-yaml.yaml

kubectl apply -f hello-yaml.yaml

kubectl get ns
# kubectl delete ns hello-yaml
# 配置文件创建的资源 用配置文件 删
kubectl delete -f hello-yaml.yaml

```



### 4.2 Pod

**Pod**：运行的一组容器，是 kubernetes 找那个应用的最小单位。（工作负载）

Pod里面可以有多个容器

![image-20230127224605842](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301272246892.png)

![image-20230128130326888](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281303959.png)

#### 4.2.1 命令行创建

```
docker ps -a

kubectl get pod -A

# 查看 每个 Pod 分配的 IP，IP 范围 在下图配置的
kubectl get pod -A -owide
```

```
kubectl run mynginx --image=nginx

# 查看default名称空间的Pod
kubectl get pod 
# 描述
kubectl describe pod 你自己的Pod名字
# 删除
kubectl delete pod Pod名字
# 查看Pod的运行日志
kubectl logs Pod名字

# 每个Pod - k8s都会分配一个ip
kubectl get pod -owide
# 使用Pod的ip+pod里面运行容器的端口
curl 192.168.169.136

# 集群中的任意一个机器以及任意的应用都能通过Pod分配的ip来访问这个Pod

```

#### 4.2.2 yaml文件创建

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mynginx-yaml
  # Pod 的 名字  
  name: mynginx-yaml
  # 指定命名空间 （不写是 默认 default）
  namespace: default
spec:
  containers:
  # 多个 容器，多个 - 
  - image: nginx
    # 容器的名字
    name: mgninx-yaml
```

```
vi mynginx-yaml.yaml

# 将上述内容 粘贴进来

kubectl apply -f mynginx-yaml.yaml

kubectl get pod
kubectl describe pod mynginx-yaml

# 配置文件创建的资源 用配置文件 删
kubectl delete -f mynginx-yaml.yaml
```

#### 4.2.3 可视化界面创建

注意： 左边菜单 有 N 标注的， 是需要 命名空间的。

![image-20230128135319154](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281353261.png)

![image-20230128141624509](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281416577.png)

创建完毕后

![image-20230128141647308](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281416367.png)

来到 Pod栏 查看 创建的 Pod

![image-20230128141721642](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281417705.png)

命令行

```
kubectl get pods -A -owide
```

![image-20230128141824852](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281418900.png)

k8s 给 每一个pod 分配 IP，IP网段是在  初始化主节点中 中 设置的

--pod-network-cidr=192.169.0.0/16

测试访问部署的Nginx

```
 curl 192.169.249.3
```

![image-20230128144037088](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281440133.png)



命令行测试执行

```
kubectl get pods -A -owide

# mynginx-yaml 是 Pod Name
kubectl exec -it mynginx-yaml 是 Pod Name -- /bin/bash

whereis nginx
cd /usr/share/nginx/html/
echo "hello k8s ngnix" > index.html
# Ctrl P + Q 退出

# 查看的 映射的IP，输出 hello k8s ngnix
curl 192.169.249.3
```

也可以在 dashboard 页面，右边， 下拉选点击 执行。

![image-20230128144722863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281447939.png)

```
echo "hello k8s ngnix dashboard" > index.html
```

![image-20230128144957407](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281449457.png)

访问 nginx 首页，修改成功

![image-20230128145028043](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281450082.png)



#### 4.2.4多容器Pod

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: myapp
  name: myapp
spec:
  containers:
  - image: nginx
    name: nginx
  - image: tomcat:8.5.68
    name: tomcat
```

```
vi multicontainer-pod.yaml

# 将上述内容 粘贴进来

kubectl apply -f multicontainer-pod.yaml

kubectl get pod
kubectl describe pod myapp

# 配置文件创建的资源 用配置文件 删
kubectl delete -f multicontainer-pod.yaml
```

可视化界面观察，正在创建中

![image-20230128145836494](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281458551.png)

绿色，表示运行成功

![image-20230128150059213](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281500264.png)

![image-20230128150115744](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281501797.png)

查看pod详情

![image-20230128150416677](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281504761.png)

![image-20230128150444427](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281504488.png)

在 同一个 Pod 共享一个 网络空间、共享网络存储。

在 可视化界面上， ngnix 中 curl 127.0.0.1:8080；tomcat 中 127.0.0.1:80 ；都是可以的。

![image-20230128150933923](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281509988.png)

下拉框可以选择2个

![image-20230128150953661](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281509709.png)

可以进入Nginx来访问tomcat

![image-20230128151136225](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281511270.png)

如果是一个pod里2个nginx，端口会被其中一个占用。

用 deployment 解决



### 4.3 Deployment

**Deployment**：控制 Pod，使 Pod 拥有多副本、自愈、扩缩容 等能力。（工作负载）

先把之前2个pod删除掉

```
[root@k8smaster ~]# kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
myapp     2/2     Running   0          22m
mynginx   1/1     Running   0          64m
[root@k8smaster ~]# kubectl delete pod myapp
pod "myapp" deleted
[root@k8smaster ~]# kubectl delete pod mynginx
pod "mynginx" deleted
```



```
# 使用两种方式创建pod，然后再删除pod，对比一下效果
# 删了就没了
kubectl run mynginx --image=nginx
# 删了之后，以一个 新的 ID 重新启动了；用 delete 删不掉的
kubectl create deployment mytomcat --image=tomcat:8.5.68
```

结论：使用run没有自愈能力，而使用create deployment 有自愈能力

#### 4.3.1 多副本

```
# --replicas=3，部署 3个 Pod my-dep
kubectl create deployment my-dep --image=nginx --replicas=3
```

![image-20230128154025246](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281540291.png)

![image-20230128154402727](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281544798.png)

删除部署

![image-20230128154500586](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281545651.png)

![image-20230128154533942](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281545002.png)

YAML文件配置

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: my-dep
  name: my-dep
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-dep
  template:
    metadata:
      labels:
        app: my-dep
    spec:
      containers:
      - image: nginx
        name: nginx
```



可视化界面部署多副本

![image-20230128154831489](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281548559.png)

```
# 查看部署的 pod 和 IP
kubectl get pod -owide

kubectl get deploy
```

![image-20230128155013085](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281550133.png)

![image-20230128155105936](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281551004.png)

#### 4.3.2 扩缩容

kubectl scale

![image-20230128155450658](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281554708.png)



```
kubectl get deploy
kubectl get pod -owide

# 扩容
kubectl scale deploy/my-dep --replicas=5

kubectl get pod -owide


```

执行扩容后增加了2个

![image-20230128161630099](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281616175.png)

```
# 缩容
kubectl scale deploy/my-dep --replicas=2

kubectl get pod -owide
kubectl get deploy

```

又缩小了

![image-20230128161901125](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281619191.png)



```
# 也可以通过 修改 yaml 中的 replicas 来达到 扩缩容
kubectl edit deploy my-dep

kubectl get pod
kubectl get deploy
```

![image-20230128161840548](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281618609.png)



可视化界面扩缩容

![image-20230128162011604](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281620677.png)

![image-20230128162032203](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281620253.png)





#### 4.3.3 自愈&故障转移

自愈：当pod因为某种原因宕机时，k8s会自动重启pod
故障转移：当pod所在服务器宕机时，能在其他节点重新部署对应的pod，所在服务恢复时，所在服务的pod将被删除

deployment 创建的 Pod 用 kubectl delete 删不掉的，会自愈

如果要删除 deployment 创建的 Pod，kubectl delete deploy xxx

模拟容器奔溃：

```
kubectl get pod -owide

#到所在节点的机器上执行
docker ps | grep xxx

docker stop xxx
```

master

![image-20230128164255170](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281642221.png)

node2

![image-20230128165307289](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281653338.png)

到 所在的 node 服务器 docker stop，回到 master 可以看到 restart + 1, 然后 接着 Running

```
[root@k8snode2 ~]# docker stop  1aa12aea5da9
1aa12aea5da9
[root@k8snode2 ~]# 

```

![image-20230128170549857](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281705907.png)



模拟宕机

把子节点服务器直接关闭

```
[root@k8snode2 ~]# shutdown -h now
```

过五分钟

![image-20230128183439143](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281834193.png)

原来的服务停止了，另一个节点Node1重新启动了一个服务



#### 4.3.4 滚动更新

类似于 灰度发布，先启动 一个新的 Pod，然后将旧的 Pod 下线。

![image-20230128183934854](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281839927.png)



```
# 查看之前用的镜像版本名字
kubectl get deploy my-dep -oyaml
```

![image-20230128184512876](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281845917.png)

```
# 改变 my-dep 中 nginx 的版本 （最新 -> 1.16.1）   --record 在 rollout histroy 中记录
kubectl set image deployment/my-dep nginx=nginx:1.16.1 --record
kubectl rollout status deployment/my-dep
```

执行前

```
[root@k8smaster ~]# kubectl get pod -w
NAME                      READY   STATUS    RESTARTS   AGE
my-dep-5b7868d854-58vvz   1/1     Running   0          4m58s
my-dep-5b7868d854-5jqtt   1/1     Running   0          4m58s
my-dep-5b7868d854-w7zjm   1/1     Running   0          4m58s
```

执行命令后

```
 # 用  kubectl get pod -w 观察过程
 kubectl get pod -w
```

![image-20230128185021431](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281850473.png)

![image-20230128185035264](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281850307.png)

之前那旧的 还在，先准备 新的。 可以看到 新的 先 Running 后 在 Terminating 旧的。

查看 spec.containers.name，镜像改成 nginx:1.16.1

```
kubectl get deploy my-dep -oyaml
```

此时版本已更新

![image-20230128185253372](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281852429.png)



#### 4.3.5 版本回退

查看历史记录

```
#历史记录
kubectl rollout history deployment/my-dep


#查看某个历史详情
kubectl rollout history deployment/my-dep --revision=2

#回滚(回到上次)
kubectl rollout undo deployment/my-dep

#回滚(回到指定版本)
kubectl rollout undo deployment/my-dep --to-revision=2
```

![image-20230128200715134](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282007189.png)



![image-20230128200817891](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282008946.png)

回滚

```
kubectl rollout undo deployment/my-dep
```

查看进度

```
kubectl get pod -w
```

![image-20230128200942463](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282009513.png)





#### 4.3.6 其他工作负载

工作负载：
Deployment：无状态应用部署，类似一些微服务、提供多副本等功能
StatefulSet：有状态应用部署，类似Redis，mysql，提供稳定的存储、网络等功能
DaemonSet：守护进程集，守护型应用部署，比如日志收集组件，在每个机器都有一份
Job/CronJob：任务/定时任务，比如垃圾清理组件，可以指定时间运行

官网文档：https://kubernetes.io/zh/docs/concepts/workloads/controllers/

可视化页面 — 工作负载

![image-20230128153324298](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281533371.png)





### 4.4 Service

**Service**：Pod 的服务发现 与 负载均衡；将一组 Pods 公开为 网络服务的抽象方法。（服务）

![image-20230128153641421](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301281536473.png)

准备测试环境。启动的3个 pod，分别 修改 index.html 演示负载均衡 

![image-20230128213756855](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282137941.png)

```
cd /usr/share/nginx/html
echo '1111' > index.html
cat index.html
```

![image-20230128213723184](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282137270.png)

再依次修改2、3节点

![image-20230128214859425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282148478.png)

#### 4.4.1 ClusterIP方式

```
# 等同于没有--type的
kubectl expose deployment my-dep --port=8000 --target-port=80 --type=ClusterIP

# 查看 service，里面有 CLUSTER-IP
# kubectl get service 或者 kubectl get svc
kubectl get service
```

![image-20230128215124428](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282151476.png)

查看pod标签

```
kubectl get pod --show-labels
```

![image-20230128215233790](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282152840.png)

体验负载均衡

![image-20230128215355131](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282153184.png)

yaml文件格式

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-dep
  name: my-dep
spec:
  ports:
  - port: 8000
    protocol: TCP
    targetPort: 80
  selector:
    app: my-dep
  type: ClusterIP
```

域名：默认是 服务.所在命名空间.svc， my-dep-02.default.svc

进入到 pod 内部 curl my-dep-02.default.svc:8000 也可以访问。

这个域名只能在  pod 内可以访问。

![image-20230128220208753](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282202825.png)

#### 4.4.2 NodePort

可以在 公网 访问。 这个缺点：暴露出来的端口是 随机的。

先删除原先的service

```
kubectl delete svc my-dep
```

执行

```
kubectl expose deployment my-dep --port=8000 --target-port=80 --type=NodePort
```

![image-20230128220621938](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282206986.png)

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-dep
  name: my-dep
spec:
  ports:
  - port: 8000
    protocol: TCP
    targetPort: 80
  selector:
    app: my-dep
  type: NodePort

```

NodePort范围在 30000-32767 之间

成功后，直接访问http://192.168.133.106:31287/

然后 任何一台 公网的 IP:31287 即可发 负载均衡的访问。

### 4.5 Ingress

**Ingress**：Service 的统一网关入口，底层就是 nginx。（服务）

官网地址：https://kubernetes.github.io/ingress-nginx/

所有的请求都先通过 Ingress，由 Ingress 来 打理这些请求。类似微服务中的 网关。



#### 4.5.1 安装

```
yum -y install wget
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.47.0/deploy/static/provider/baremetal/deploy.yaml

#修改镜像
vi deploy.yaml
#将image的值改为如下值：
registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/ingress-nginx-controller:v0.46.0

# 检查安装的结果
kubectl get pod,svc -n ingress-nginx

# 最后别忘记把svc暴露的端口要放行
```



如果报错：

```
Connecting to raw.githubusercontent.com |185.199.109.133|:443... Unable to establish SSL connect
```

解决办法如下：

```
sudo vi /etc/hosts
# 添加151.101.64.133  raw.githubusercontent.com
```

![image-20230128224701291](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282247346.png)

```
# 将 image 的值改为如下值
registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/ingress-nginx-controller:v0.46.0
```

![image-20230128225059000](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282250074.png)

改成

![image-20230128225141226](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282251278.png)

执行

```
# 安装资源
kubectl apply -f deploy.yaml

# 检查安装的结果
kubectl get pod,svc -n ingress-nginx
```

还会生成service

![image-20230128225628502](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301282256587.png)

问题如下

![image-20230129105248659](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291052773.png)

错误信息

```
MountVolume.SetUp failed for volume "webhook-cert" : secret "ingress-nginx-admission" not found
```

```
Events:
  Type     Reason       Age                   From               Message
  ----     ------       ----                  ----               -------
  Normal   Scheduled    11h                   default-scheduler  Successfully assigned ingress-nginx/ingress-nginx-controller-68466b9c78-npts7 to k8snode2
  Warning  FailedMount  11h (x7 over 11h)     kubelet            MountVolume.SetUp failed for volume "webhook-cert" : secret "ingress-nginx-admission" not found
  Warning  FailedMount  4m26s (x3 over 11m)   kubelet            Unable to attach or mount volumes: unmounted volumes=[webhook-cert], unattached volumes=[webhook-cert ingress-nginx-token-hr44m]: timed out waiting for the condition
  Warning  FailedMount  2m9s (x2 over 6m42s)  kubelet            Unable to attach or mount volumes: unmounted volumes=[webhook-cert], unattached volumes=[ingress-nginx-token-hr44m webhook-cert]: timed out waiting for the condition
  Warning  FailedMount  61s (x14 over 13m)    kubelet            MountVolume.SetUp failed for volume "webhook-cert" : secret "ingress-nginx-admission" not found

```

解决办法：

```
kubectl delete  -f deploy.yaml
kubectl apply  -f deploy.yaml
```



![image-20230129111543945](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291115024.png)

安装完毕后

```
http://192.168.133.106:32646/
https://192.168.133.106:30502/
```



#### 4.5.2 域名访问

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-server
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-server
  template:
    metadata:
      labels:
        app: hello-server
    spec:
      containers:
      - name: hello-server
        image: registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/hello-server
        ports:
        - containerPort: 9000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-demo
  name: nginx-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-demo
  template:
    metadata:
      labels:
        app: nginx-demo
    spec:
      containers:
      - image: nginx
        name: nginx
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-demo
  name: nginx-demo
spec:
  selector:
    app: nginx-demo
  ports:
  - port: 8000
    protocol: TCP
    targetPort: 80
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: hello-server
  name: hello-server
spec:
  selector:
    app: hello-server
  ports:
  - port: 8000
    protocol: TCP
    targetPort: 9000
```

```
vi test.yaml

# 复制上面的yaml文件到test.yaml，然后执行下面

kubectl apply -f test.yaml
```

![image-20230129114927382](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291149442.png)

域名规则配置

```
vi ingress-rule.yaml

# 复制下面配置

kubectl apply -f ingress-rule.yaml

# 查看 集群中的 Ingress
kubectl get ingress
```



```
apiVersion: networking.k8s.io/v1
kind: Ingress  
metadata:
  name: ingress-host-bar
spec:
  ingressClassName: nginx
  rules:
  - host: "hello.jiang.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: hello-server
            port:
              number: 8000
  - host: "demo.jiang.com"
    http:
      paths:
      - pathType: Prefix
        path: "/nginx"  # 把请求会转给下面的服务，下面的服务一定要能处理这个路径，不能处理就是404
        backend:
          service:
            name: nginx-demo  ## java，比如使用路径重写，去掉前缀nginx
            port:
              number: 8000
```

![image-20230129120719031](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291207102.png)

测试：

在 自己电脑（不是虚拟机） hosts 中增加映射：

master的公网IP hello.jiang.com

master的公网IP demo.jiang.com

浏览器访问

```
http://hello.jiang.com:32646/
http://demo.jiang.com:32646/
```



#### 4.5.3 路径重写

进入官网看示例

![image-20230129121919952](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291219062.png)

```
apiVersion: networking.k8s.io/v1
kind: Ingress  
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: ingress-host-bar
spec:
  ingressClassName: nginx
  rules:
  - host: "hello.jiang.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: hello-server
            port:
              number: 8000
  - host: "demo.jiang.com"
    http:
      paths:
      - pathType: Prefix
        path: "/nginx(/|$)(.*)"  # 把请求会转给下面的服务，下面的服务一定要能处理这个路径，不能处理就是404
        backend:
          service:
            name: nginx-demo  ## java，比如使用路径重写，去掉前缀nginx
            port:
              number: 8000
```



```
vi ingress-rule.yaml
kubectl apply -f ingress-rule.yaml

```

访问http://demo.jiang.com:32646/nginx

![image-20230129122449732](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291224787.png)





#### 4.5.4 限流

官网文档：https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration

![image-20230129122803182](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291228282.png)

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-limit-rate
  annotations:
    nginx.ingress.kubernetes.io/limit-rps: "1"
spec:
  ingressClassName: nginx
  rules:
  - host: "haha.jiang.com"
    http:
      paths:
      - pathType: Exact
        path: "/"
        backend:
          service:
            name: nginx-demo
            port:
              number: 8000
```

```
vi ingress-rule-2.yaml

# 复制上面配置

kubectl apply -f ingress-rule-2.yaml

kubectl get ing
```

在 自己电脑（不是虚拟机） hosts 中增加映射：

公网IP haha.jiang.com

测试访问：

```
http://haha.jiang.com:32646/
```

多次刷新后出现503提示

![image-20230129130255239](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291302297.png)



官方文档写

![image-20230129123016098](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291230177.png)



## 5 存储抽象

类似于 Docker 中的 挂载。但要考虑 自愈、故障转移 时的情况。

![image-20230129171456758](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291715837.png)

![image-20230129171628147](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291716207.png)

### 5.1 NFS环境搭建

#### 5.1.1 所有节点都要安装

```
#所有机器安装
yum install -y nfs-utils
```

#### 5.1.2 主节点

```
# nfs主节点
# 只在 mster 机器执行：nfs主节点，rw 读写
echo "/nfs/data/ *(insecure,rw,sync,no_root_squash)" > /etc/exports

mkdir -p /nfs/data
systemctl enable rpcbind --now
systemctl enable nfs-server --now
#配置生效
exportfs -r

# 查看
exportfs
```

![image-20230129172847710](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291728758.png)



#### 5.1.3 从节点

```
# 检查，下面的 IP 是master IP
showmount -e 192.168.133.106

#执行以下命令挂载 nfs 服务器上的共享目录到本机路径 /root/nfsmount
mkdir -p /nfs/data

在 2 个从服务器执行，将远程 和本地的 文件夹 挂载
mount -t nfs 192.168.133.106:/nfs/data /nfs/data

# 写入一个测试文件
echo "hello nfs server" > /nfs/data/test.txt

# 在 2 个从服务器查看
cd /nfs/data
ls

# 在 从服务器 修改，然后去 其他 服务器 查看，也能 同步
```

master

![image-20230129173927659](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291739709.png)

node

![image-20230129173941035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291739078.png)



### 5.2 原生方式数据挂载

在 /nfs/data/nginx-pv 挂载，然后 修改， 里面 两个 Pod 也会 同步修改。

问题：删掉之后，文件还在，内容也在，是没法管理大小的。

![image-20230129174606753](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291746807.png)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-pv-demo
  name: nginx-pv-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-pv-demo
  template:
    metadata:
      labels:
        app: nginx-pv-demo
    spec:
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html # 挂载目录
      volumes:
        - name: html
          nfs:
            server: 192.168.133.106
            path: /nfs/data/nginx-pv # 要提前创建好文件夹，否则挂载失败
```

```
cd /nfs/data
mkdir -p nginx-pv
ls

vi deploy.yaml

# 复制上面配置

kubectl apply -f deploy.yaml

kubectl get pod -owide
```

![image-20230129175203259](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291752318.png)

![image-20230129175217245](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291752291.png)

```
cd /nfs/data/
ls
cd nginx-pv/
echo "cgxin" > index.html

# 进入 pod 里面查看
```

可视化界面进入pod查看

![image-20230129175541754](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291755829.png)

![image-20230129175632379](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291756429.png)

问题：占用空间

![image-20230129175912464](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291759521.png)

删掉之后，文件还在，内容也在，是没法管理大小的。



### 5.3 PV和PVC

**PV**：持久卷（Persistent Volume），将应用需要持久化的数据保存到指定位置

**PVC**：持久卷申明（Persistent Volume Claim），申明需要使用的持久卷规格。

POD要申请多少空间，先办PVC（我要申请多少空间的申请书）

挂载目录。ConfigMap 挂载配置文件。

这里是 是 静态的， 就是自己创建好了 容量，然后 PVC 去挑。 还有 动态供应的，不用手动去创建 PV池子。

![image-20230129180514662](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291805714.png)

#### 5.3.1 创建 PV 池

静态供应

```
#nfs主节点
mkdir -p /nfs/data/01
mkdir -p /nfs/data/02
mkdir -p /nfs/data/03
```

创建PV3个

```
apiVersion: v1
kind: PersistentVolume #持久化卷
metadata:
  name: pv01-10m
spec:
  # 限制容量
  capacity:
    storage: 10M
  # 读写模式：可读可写
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    # 挂载 上面创建过的文件夹
    path: /nfs/data/01
    # nfs 主节点服务器的 IP
    server: 192.168.133.106
---
apiVersion: v1
kind: PersistentVolume
metadata:
  # 这个name 要小写，如 Gi 大写就不行
  name: pv02-1gi
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /nfs/data/02
    # nfs 主节点服务器的 IP
    server: 192.168.133.106
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv03-3gi
spec:
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteMany
  storageClassName: nfs
  nfs:
    path: /nfs/data/03
    # nfs 主节点服务器的 IP
    server: 192.168.133.106
```



```
vi pv.yaml

# 复制上面文件内容

kubectl apply -f pv.yaml

# 查看 pv， kubectl get pv
kubectl get persistentvolume
```

![image-20230129190721843](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291907883.png)





#### 5.3.2 PVC创建与绑定

创建PVC

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      # 需要 200M的 PV
      storage: 200Mi
  # 上面 PV 写的storageClassName 这里就写什么     
  storageClassName: nfs
```



```
vi pvc.yaml

# 复制上面配置

kubectl get pv

kubectl apply -f pvc.yaml

kubectl get pv

kubectl get pvc
```

![image-20230129191539702](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291915745.png)

图中显示已经绑定成功， 绑定了1G的，10M 不够，3G太大，就选择了 1G

删除命令

```
kubectl delete -f pvc.yaml
```

![image-20230129191813949](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291918997.png)

删除了，就 释放了（released），然后又运行了一遍，挂到3G的了

#### 5.3.3 创建 Pod 绑定 PVC



```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deploy-pvc
  name: nginx-deploy-pvc
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deploy-pvc
  template:
    metadata:
      labels:
        app: nginx-deploy-pvc
    spec:
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/html
      volumes:
        - name: html
          # 之前是 nfs，这里用 pvc
          persistentVolumeClaim:
            claimName: nginx-pvc
```



```
vi dep02.yaml

# 复制上面 yaml

kubectl apply -f dep02.yaml

kubectl get pod

kubectl get pv

kubectl get pvc
```

![image-20230129192208033](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291922083.png)

测试，编写一个html

![image-20230129192653315](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291926379.png)



进入 Pod 内部查看 同步的文件

![image-20230129192108449](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291921514.png)

![image-20230129192744385](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301291927446.png)

内容显示也是“hello jiang”

### 5.4  ConfigMap

**ConfigMap**：抽取应用配置，并且可以自动更新。挂载配置文件， PV 和 PVC 是挂载目录的。

K8S所有的数据保存在ETCD

redis示例

#### 5.4.1  创建配置集

```
vi redis.conf
# 写
appendonly yes

# 创建配置，redis保存到k8s的etcd；
kubectl create cm redis-conf --from-file=redis.conf

# 查看
kubectl get cm

rm -rf redis.conf
```

![image-20230129202327648](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292023704.png)

```
# 查看 ConfigMap 的 yaml 配置咋写的
kubectl get cm redis-conf -oyaml
```

![image-20230129205725996](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292057051.png)

精简后的

```
apiVersion: v1
data:   # data是所有真正的数据，key：默认是文件名   value：配置文件的内容（appendonly yes 是随便写的）
  redis.conf: |
    appendonly yes
kind: ConfigMap
metadata:
  name: redis-conf
  namespace: default

```



#### 5.4.2 创建Pod

```
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis
    command:
      - redis-server
      - "/redis-master/redis.conf"  #指的是redis容器内部的位置
    ports:
    - containerPort: 6379
    volumeMounts:
    - mountPath: /data
      name: data
    - mountPath: /redis-master
      name: config
  volumes:
    - name: data
      emptyDir: {}
    - name: config
      configMap:
        name: redis-conf
        items:
        - key: redis.conf
          path: redis.conf
```

![image-20230129210212743](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292102806.png)

redis.conf 会放在 /redis-master 下

```
vi redis.yaml

# 复制上面配置

kubectl apply -f redis.yaml

kubectl get pod
```

![image-20230129210629562](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292106620.png)

测试

页面中 进入刚才创建的 pod redis 内部，查看 redis.conf 配置文件 内容

![image-20230129210726366](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292107420.png)

修改配置

```
kubectl get cm

# 修改配置 里 redis.conf 的内容
kubectl edit cm redis-conf
```

修改 redis-conf 的 redis.conf 内容

![image-20230129211133634](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292111685.png)

同步刷新

![image-20230129211208080](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292112144.png)





#### 5.4.3 检查默认配置

```
kubectl exec -it redis -- redis-cli

127.0.0.1:6379> CONFIG GET appendonly
127.0.0.1:6379> CONFIG GET requirepass
```

检查刚才修改的配置是否生效

![image-20230129211502807](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292115856.png)

还是原生 redis 的 默认配置，说明 配置值 没有同步，但 配置文件 已同步

删除，重新创建 Pod，更新 配置文件的 配置值

![image-20230129211920387](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292119450.png)

查看 更新的 配置值

![image-20230129212109483](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292121547.png)

总结：

- 修改了 ConfigMap，Pod里面的配置文件会跟着同步。
- 但配置值 未更改，需要重新启动 Pod 才能从关联的ConfigMap 中获取 更新的值。 Pod 部署的中间件 自己本身没有热更新能力。



#### 5.4.4 修改ConfigMap

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-redis-config
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru 
```



#### 5.4.5 检查配置是否更新

```
kubectl exec -it redis -- redis-cli

127.0.0.1:6379> CONFIG GET maxmemory
127.0.0.1:6379> CONFIG GET maxmemory-policy
```

检查指定文件内容是否已经更新

修改了CM。Pod里面的配置文件会跟着变



### 5.5. Secret

Secret 对象类型用来保存敏感信息，例如密码、OAuth 令牌和 SSH 密钥。 将这些信息放在 secret 中比放在 Pod 的定义或者 容器镜像 中来说更加安全和灵活。

#### 5.5.1 Docker hub 拉取失败

注册一个Docker hub 账号，然后创建一个仓库test,设置为私有仓库



```
docker login
# 输入用户名和密码

docker pull hello-world

docker tag xxx/mynginx:v1.0.0 
#xxx 代表的是你的docker hub的账号名称

docker push xxx/mynginx:v1.0.0 
# 推送的时候可能会慢
```

![image-20230130122217330](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301222434.png)

```
# 拉取镜像
docker pull xxx/mynginx:v1.0.0
```

拒绝拉取

![image-20230129214833294](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292148346.png)

准备一个pod

```
apiVersion: v1
kind: Pod
metadata:
  name: private-jiangteddy-test
spec:
  containers:
  - name: private-jiangteddy-test
    image: jiangteddy2023/mynginx:v1.0.0
```

```
vi mypod.yaml

# 复制上面配置

kubectl apply -f mypod.yaml

kubectl get pod
```

因为权限问题下载失败

![image-20230129215816426](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292158486.png)

![image-20230129215933963](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292159049.png)

可视化界面 查看错误描述：也是没有权限。

```
# 删除配置文件 创建的错误 Pod
kubectl delete -f mypod.yaml
```



#### 5.5.2 创建Secret 

```
kubectl create secret docker-registry jiangteddy-test-secret \
--docker-username=jiangteddy \
--docker-password=123456 \
--docker-email=xxxxxx@163.com

##命令格式如下
kubectl create secret docker-registry regcred \
  --docker-server=<你的镜像仓库服务器> \
  --docker-username=<你的用户名> \
  --docker-password=<你的密码> \
  --docker-email=<你的邮箱地址>
```



```
# 查看
kubectl get secret

kubectl get secret jiangteddy-test-secret -oyaml
```

![image-20230129220623600](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301292206656.png)

 重新修改之前的yaml文件

```

```

```
apiVersion: v1
kind: Pod
metadata:
  name: private-jiangteddy-test
spec:
  containers:
  - name: private-jiangteddy-test
    image: jiangteddy2023/mynginx:v1.0.0
  # 加上 Secret  
  imagePullSecrets:
  - name: jiangteddy-test-secret
```



```
vi mypod.yaml

# 复制上面配置

kubectl apply -f mypod.yaml

kubectl get pod
```

使用 Secret 后，可以成功 拉取下来了。

![image-20230130122102621](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301221754.png)





## 6 Helm

   在 Kubernetes 中，应用管理是需求最多、挑战最大的领域。 Helm 项目提供了一个统一软件打包方式，支持版本控制，可以大大简化 Kubernetes 应用分发与部署中的复杂性。

- **Chart**：一个 Helm 包，其中包含了运行一个应用所需要的镜像、依赖和资源定义等，还可能包含 Kubernetes 集群中的服务定义，类似 Homebrew 中的 formula、APT 的 dpkg 或者 Yum 的 rpm 文件。
- **Release**：使用 `helm install` 命令在 Kubernetes 集群中部署的 Chart 称为 Release。
- **Repository**：用于发布和存储 Chart 的存储库。

Helm 有两个重要得概念：chart 和 release

chart ：是创建一个应用的信息集合，包括各种 kubernetes 对象得配置模板、参数定义、依赖关系、文档说明等，可以将 chart 想象成 yum 中的软件安装包

release ：是 chart 的运行实例，代表了一个正在运行的应用。当 chart 被安装到 kubernetes 集群，就会生成一个 release，chart 能够多次安装到同一个集群，但是只会有一个 release


### 6.1 如何安装

离线安装，先下载安装包

```
 https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
```

解压

```
tar -zxvf helm-v3.8.2-linux-amd64.tar.gz
```

复制

```
cp linux-amd64/helm /usr/local/bin
helm version
```

添加 chart 源

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```



### 6.2 基础操作

查询当前集群有哪些 chart 库

```
# 查看当前配置的仓库地址
$ helm repo list

# 删除默认仓库，默认在国外pull很慢
$ helm repo remove stable

# 添加几个常用的仓库,可自定义名字
$ helm repo add stable https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
$ helm repo add kaiyuanshe http://mirror.kaiyuanshe.cn/kubernetes/charts
$ helm repo add azure http://mirror.azure.cn/kubernetes/charts
$ helm repo add dandydev https://dandydeveloper.github.io/charts
$ helm repo add bitnami https://charts.bitnami.com/bitnami

# 搜索chart
$ helm search repo redis

# 拉取chart包到本地
$ helm pull bitnami/redis-cluster --version 8.1.2

# 安装redis-ha集群，取名redis-ha，需要指定持存储类
$ helm install redis-cluster bitnami/redis-cluster --set global.storageClass=nfs,global.redis.password=xiagao --version 8.1.2

# 卸载
$ helm uninstall redis-cluster
```

查找一个安装程序

```
helm search repo nginx
```

![image-20230130140210636](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301402687.png)

安装一个程序

```
helm install nginx bitnami/nginx
```

查询 svc

```
 kubectl get svc -n default
```

![image-20230130140345067](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301403120.png)

查看已安装的应用

```
helm list
```

删除一个应用

```
helm uninstall nginx
```

![image-20230130140501971](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301405027.png)



### 6.3 规范

Helm Chart 是一种打包格式。Chart 是一个描述一组 Kubernetes 相关资源的文件集合。

Chart 的所有相关文件都存储在一个目录中，该目录通常包含：

![image-20230130135704676](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301357730.png)

您必须为 Chart 提供 `chart.yaml` 文件。下面是一个示例文件，每个字段都有说明。

![image-20230130135814165](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301358233.png)

Values.yaml 和模板
Helm Chart 模板采用 Go 模板语言编写并存储在 Chart 的 templates 文件夹。有两种方式可以为模板提供值：

在 Chart 中创建一个包含可供引用的默认值的 values.yaml 文件。
创建一个包含必要值的 YAML 文件，通过在命令行使用 helm install 命令来使用该文件

![image-20230130135903449](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301301359510.png)

- `imageRegistry`：Docker 镜像仓库。
- `dockerTag`：Docker 镜像标签 (tag)。
- `pullPolicy`：镜像拉取策略。
- `storage`：存储后端，默认为 `minio`。

示例如下：

```
imageRegistry: "quay.io/deis"

dockerTag: "latest"

pullPolicy: "Always"

storage: "s3"

```



### 







## 

