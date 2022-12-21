# K8s

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

- - **永久关闭**

### 3.1 基础环境

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

### 3.2 安装kubelet、kubeadm、kubectl

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

### 3.3 使用kubeadm引导集群

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



### 3.4 初始化主节点

命令ip a

找到master的内网IP

#所有机器添加master域名映射，以下需要修改为自己的
echo "192.168.133.110  cluster-endpoint" >> /etc/hosts

#主节点初始化

kubeadm init \
--apiserver-advertise-address=192.168.133.110  \
--control-plane-endpoint=cluster-endpoint \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.168.0.0/16

其中--service-cidr和--service-cidr、apiserver-advertise-address网络不能重叠

出现了错误

[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.

![image-20221217225611744](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212172256139.png)

解决办法：

echo "192.168.133.110  cluster-endpoint" >> /etc/hosts



成功的结果：

[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

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

  kubeadm join cluster-endpoint:6443 --token v3pkbh.n4jw9mmaj7omcmm8 \
    --discovery-token-ca-cert-hash sha256:c04629d3c02f70654b908f055b62bc6d3e1a79cb65fc341c1fc38560864167ab \
    --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join cluster-endpoint:6443 --token v3pkbh.n4jw9mmaj7omcmm8 \

**设置.kube/config**

接下来执行

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

然后用命令kubectl get nodes查看节点

![image-20221218213339818](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212182133328.png)

**安装网络组件**

curl https://docs.projectcalico.org/manifests/calico.yaml -O

kubectl apply -f calico.yaml

报错如下

error: unable to recognize "calico.yaml": no matches for kind "PodDisruptionBudget" in version "policy/v1"

这是k8s不支持当前[calico](https://so.csdn.net/so/search?q=calico&spm=1001.2101.3001.7020)版本的原因, calico版本与k8s版本支持关系可到calico官网查看，可以改成如下

curl https:*//docs.projectcalico.org/v3.20/manifests/calico.yaml -O*

kubectl apply -f calico.yaml



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

#查看集群部署了哪些应用

#运行中的应用在docker里面叫容器，在k8s里面叫Pod
kubectl get pods -A

![image-20221218222618434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212182226481.png)

判断所有都处于Ready状态就表示可以使用了

### 3.5 安装子节点

接下来安装另外2太机器

hostnamectl set-hostname k8s-node1

hostnamectl set-hostname k8s-node2



**如果令牌过期，需要创建新的令牌**

新令牌

kubeadm token create --print-join-command

![image-20221219164003913](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191640131.png)

kubeadm join cluster-endpoint:6443 --token et6dju.4ao09tmc6gu2jlg6     --discovery-token-ca-cert-hash sha256:c04629d3c02f70654b908f055b62bc6d3e1a79cb65fc341c1fc38560864167ab

复制上述命令，在子节点执行

![image-20221219174636863](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191746914.png)

**如果出现这个异常**

![image-20221219174544768](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191745819.png)

error execution phase preflight: couldn't validate the identity of the API Server: Get "https://cluster-endpoint:6443/api/v1/namespaces/kube-public/configmaps/cluster-info?timeout=10s": dial tcp 192.168.133.110:6443: connect: no route to host

请检查Master的防火墙是否关闭

执行这个命令：*systemctl disable firewalld --now*

![image-20221219174516552](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212191745619.png)

再次执行成功

### 3.6验证集群

- - kubectl get nodes

- ![image-20221219225349452](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192253633.png)

- 

### 3.7 安装可视化界面

部署dashboard

在主节点安装

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml

![image-20221219225553209](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192255255.png)



![image-20221219225540361](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192255407.png)

必须等下面2个dashboard都是running状态。

问题：kubernetes(k8s)创建Dashboard失败，Dashboard的pod状态为CrashLoopBackOff

解决办法：

首先查询错误日志：

 kubectl logs -f -n kubernetes-dashboard kubernetes-dashboard-658485d5c7-9zdm8

![image-20221219230107478](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202212192301512.png)

Error from server: Get "https://192.168.133.111:10250/containerLogs/kubernetes-dashboard/kubernetes-dashboard-658485d5c7-9zdm8/kubernetes-dashboard?follow=true": dial tcp 192.168.133.111:10250: connect: no route to host



然后设置访问端口

kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard

编辑文本
