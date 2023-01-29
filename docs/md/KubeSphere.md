# KubeSphere

## 1 概念

KubeSphere作为企业级的全栈化容器平台，为用户提供了一个具备极致体验的 Web 控制台，让您能够像使用任何其他互联网产品一样，快速上手各项功能与服务。KubeSphere 目前提供了工作负载管理、微服务治理、DevOps 工程、Source to Image、多租户管理、多维度监控、日志查询与收集、告警通知、服务与网络、应用管理、基础设施管理、镜像管理、应用配置密钥管理等功能模块，开发了适用于适用于物理机部署 Kubernetes 的 负载均衡器插件 Porter，并支持对接多种开源的存储与网络方案，支持高性能的商业存储与网络服务。





## 2 安装

### 2.1 基础环境配置

使用三台虚机配置

![image-20230125184032173](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251840221.png)

```
uuidgen

#复制UUID
[root@localhost ~]# vi /etc/sysconfig/network-scripts/ifcfg-ens33
[root@localhost ~]# systemctl restart network
```



安装基础工具

```
yum install -y wget && yum install -y vim && yum install -y lsof && yum install -y net-tools
```

给三台机器设置域名

```
hostnamectl set-hostname k8smaster
hostnamectl set-hostname k8sworker1
hostnamectl set-hostname k8sworker2
```

关闭防火墙

```
systemctl stop firewalld
systemctl disable firewalld
systemctl status firewalld
```

关闭selinux

```
sed -i 's/enforcing/disabled/' /etc/selinux/config
setenforce 0
cat /etc/selinux/config
```

关闭swap

```
swapoff -a  
sed -ri 's/.*swap.*/#&/' /etc/fstab  
free -l -h
```

允许 iptables 检查桥接流量

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```

### 2.2 安装Docker

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
```

执行命令测试

```
docker ps
```



### 2.3 安装kubelet、kubeadm、kubectl

```
#配置k8s的yum源地址
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
   http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF


#安装 kubelet，kubeadm，kubectl
sudo yum install -y kubelet-1.20.9 kubeadm-1.20.9 kubectl-1.20.9

#启动kubelet
sudo systemctl enable --now kubelet

#所有机器配置master域名
echo "192.168.133.110  k8smaster" >> /etc/hosts
```

设置完毕后，用子节点测试ping主节点

![image-20230122180438090](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221804210.png)



### 2.4 初始化master节点

注：初始化 主节点命令，只在 master 服务执行。其他 node 服务器 不用。

![image-20230122180619337](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221806386.png)

```
kubeadm init \
--apiserver-advertise-address=192.168.133.110 \
--control-plane-endpoint=k8smaster \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.169.0.0/16
```

保留关键信息

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

  kubeadm join k8smaster:6443 --token sbt60w.5qyalhi7t1vt9ood \
    --discovery-token-ca-cert-hash sha256:e25e347c23eedf2e90bb0a9f407c5019cbe8918d1fa5fba99a38279b81527e64 \
    --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join k8smaster:6443 --token sbt60w.5qyalhi7t1vt9ood \
    --discovery-token-ca-cert-hash sha256:e25e347c23eedf2e90bb0a9f407c5019cbe8918d1fa5fba99a38279b81527e64
```





复制上面三行命令，在master执行

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

执行完毕后，执行

```
kubectl get nodes
```

查看结果

![image-20230122192026352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221920386.png)

安装网络组件

```
curl https://docs.projectcalico.org/v3.20/manifests/calico.yaml -O
kubectl apply -f calico.yaml
```

然后执行命令，查看结果

```
kubectl get pod -A
```

![image-20230122192949452](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221929489.png)



### 2.5 加入worker节点

在worker节点执行初始化命令

```
kubeadm join k8smaster:6443 --token c9h52t.bj5cwk2bubhw4s61     --discovery-token-ca-cert-hash sha256:b52d25688e793da991107c17e64032a605f4fcc746c093ca4cc191a20ad106b9
```

如果初始化报错

```
[preflight] Running pre-flight checks
	[WARNING SystemVerification]: this Docker version is not on the list of validated versions: 20.10.7. Latest validated version: 19.03
	[WARNING Hostname]: hostname "k8sworker1" could not be reached
	[WARNING Hostname]: hostname "k8sworker1": lookup k8sworker1 on 8.8.8.8:53: no such host
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`

```

执行一下命令

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

检查结果

![image-20230122194326813](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221943849.png)

![image-20230122194841001](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301221948044.png)



### 2.6 安装前置环境

#### 2.6.1 安装NFS文件系统

先在每个机器执行命令，安装工具类

```
yum install -y nfs-utils
```

然后在master上执行

```
# 在master 执行以下命令 
echo "/nfs/data/ *(insecure,rw,sync,no_root_squash)" > /etc/exports


# 执行以下命令，启动 nfs 服务;创建共享目录
mkdir -p /nfs/data


# 在master执行
systemctl enable rpcbind
systemctl enable nfs-server
systemctl start rpcbind
systemctl start nfs-server

# 使配置生效
exportfs -r


#检查配置是否生效
exportfs
```

![image-20230122200527726](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301222005768.png)

在其他节点配置NFS的客户端

```
showmount -e 192.168.133.110

mkdir -p /nfs/data

mount -t nfs 192.168.133.110:/nfs/data /nfs/data
```

配置默认存储

![image-20230122204359849](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301222043912.png)

```
vi sc.yaml
```

把下方的内容复制到yaml文件里，记得修改ip

```
## 创建了一个存储类
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-storage
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: k8s-sigs.io/nfs-subdir-external-provisioner
parameters:
  archiveOnDelete: "true"  ## 删除pv的时候，pv的内容是否要备份

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
  labels:
    app: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
spec:
  replicas: 1
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nfs-client-provisioner
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/nfs-subdir-external-provisioner:v4.0.2
          # resources:
          #    limits:
          #      cpu: 10m
          #    requests:
          #      cpu: 10m
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: k8s-sigs.io/nfs-subdir-external-provisioner
            - name: NFS_SERVER
              value: 192.168.133.110 ## 指定自己nfs服务器地址
            - name: NFS_PATH  
              value: /nfs/data  ## nfs服务器共享的目录
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.133.110
            path: /nfs/data
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: nfs-client-provisioner-runner
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["persistentvolumes"]
    verbs: ["get", "list", "watch", "create", "delete"]
  - apiGroups: [""]
    resources: ["persistentvolumeclaims"]
    verbs: ["get", "list", "watch", "update"]
  - apiGroups: ["storage.k8s.io"]
    resources: ["storageclasses"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["events"]
    verbs: ["create", "update", "patch"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: run-nfs-client-provisioner
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: ClusterRole
  name: nfs-client-provisioner-runner
  apiGroup: rbac.authorization.k8s.io
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["endpoints"]
    verbs: ["get", "list", "watch", "create", "update", "patch"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: leader-locking-nfs-client-provisioner
  # replace with namespace where provisioner is deployed
  namespace: default
subjects:
  - kind: ServiceAccount
    name: nfs-client-provisioner
    # replace with namespace where provisioner is deployed
    namespace: default
roleRef:
  kind: Role
  name: leader-locking-nfs-client-provisioner
  apiGroup: rbac.authorization.k8s.io
```

```
#安装存储类
kubectl apply -f sc.yaml
#确认配置是否生效
kubectl get sc
```

![image-20230122204958779](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301222049820.png)

![image-20230122205136921](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301222051958.png)

vi pvc.yaml

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 200Mi
```



```
[root@localhost ~]# kubectl apply -f pvc.yaml
persistentvolumeclaim/nginx-pvc created
[root@localhost ~]# kubectl get pvc
```



#### 2.6.2 metrics-server

```
vi metrics.yaml
```



```
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  - nodes/stats
  - namespaces
  - configmaps
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server-auth-reader
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: extension-apiserver-authentication-reader
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server:system:auth-delegator
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:metrics-server
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  ports:
  - name: https
    port: 443
    protocol: TCP
    targetPort: https
  selector:
    k8s-app: metrics-server
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      containers:
      - args:
        - --cert-dir=/tmp
        - --kubelet-insecure-tls
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        image: registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/metrics-server:v0.4.3
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 4443
          name: https
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /readyz
            port: https
            scheme: HTTPS
          periodSeconds: 10
        securityContext:
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
        volumeMounts:
        - mountPath: /tmp
          name: tmp-dir
      nodeSelector:
        kubernetes.io/os: linux
      priorityClassName: system-cluster-critical
      hostNetwork: true
      serviceAccountName: metrics-server
      volumes:
      - emptyDir: {}
        name: tmp-dir
---
apiVersion: apiregistration.k8s.io/v1
kind: APIService
metadata:
  labels:
    k8s-app: metrics-server
  name: v1beta1.metrics.k8s.io
spec:
  group: metrics.k8s.io
  groupPriorityMinimum: 100
  insecureSkipTLSVerify: true
  service:
    name: metrics-server
    namespace: kube-system
  version: v1beta1
  versionPriority: 100
```

然后执行

```
kubectl apply -f metrics.yaml
```

然后执行，查看结果

```
kubectl get pod -A
```

![image-20230122212103785](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301222121831.png)







#### 2.6.3 NFS介绍

网络文件系统(Network File System, NFS)，是基于内核的文件系统，nfs主要是通过网络实现服务器和客户端之间的数据传输，采用远程过程调用RPC（Romete Procedure Call）机制，让不同的机器节点共享文件目录。只需将nfs服务器共享的文件目录挂载到nfs客户端，这样客户端就可以对远程服务器上的文件进行读写操作。
  一个NFS服务器可以对应多个nfs客户端，基于RPC机制，用户可以像访问本地文件一样访问远端的共享目录文件

![image-20230123094809088](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301230948162.png)

通过RPC和NFS服务传输数据

![image-20230123094831091](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301230948141.png)

#### 2.6.4 如何重装K8S

k8s安装插件出现dial tcp 10.96.0.1:443: i/o timeout问题解析

出现这种问题的原因是因为创建k8s集群的时候，初始化主节点时网络地址段出现重叠导致的

初始化时apiserver-advertise-address=192.168.133.110，而pod-network-cidr=192.168.0.0/16，此时可以发现我的服务器的ip地址在k8s的集群网络地址段中，这就导致在分配网络的时候会出现问题，在安装组件的时候导致工作节点和主之前无法互访，导致安装失败。

改这个地方

```
--pod-network-cidr=192.169.0.0/16
```

必须要重装，看以下步骤

在master重装

```
kubeadm reset
```

删除残余文件

```
rm -rf /etc/kubernetes
rm -rf /var/lib/etcd/
rm -rf $HOME/.kube
```

然后初始化主节点

```
kubeadm init \
--apiserver-advertise-address=192.168.133.110 \
--control-plane-endpoint=k8smaster \
--image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images \
--kubernetes-version v1.20.9 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.169.0.0/16
```

然后安装网络组件

然后子节点也要执行kubeadm reset

然后重新

```
[root@k8smaster ~]# exportfs
/nfs/data     	<world>
[root@k8smaster ~]# ls
anaconda-ks.cfg  calico.yaml  metrics.yaml  pvc.yaml  sc.yaml
[root@k8smaster ~]# kubectl apply -f sc.yaml
storageclass.storage.k8s.io/nfs-storage created
deployment.apps/nfs-client-provisioner created
serviceaccount/nfs-client-provisioner created
clusterrole.rbac.authorization.k8s.io/nfs-client-provisioner-runner created
clusterrolebinding.rbac.authorization.k8s.io/run-nfs-client-provisioner created
role.rbac.authorization.k8s.io/leader-locking-nfs-client-provisioner created
rolebinding.rbac.authorization.k8s.io/leader-locking-nfs-client-provisioner created
```



![image-20230123100749536](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231007580.png)



### 2.7 安装KubeSphere

#### 2.7.1 下载核心文件

```
wget https://github.com/kubesphere/ks-installer/releases/download/v3.1.1/kubesphere-installer.yaml

wget https://github.com/kubesphere/ks-installer/releases/download/v3.1.1/cluster-configuration.yaml
```

#### 2.7.2 修改cluster-configuration

![image-20230123160442502](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231604653.png)

![image-20230123160616915](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231606972.png)

![image-20230123161053254](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231610310.png)

![image-20230123161358847](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231613898.png)

![image-20230123161559962](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231616017.png)

修改

```
vim cluster-configuration.yaml
```



#### 2.7.3 执行安装

```
kubectl apply -f kubesphere-installer.yaml

kubectl apply -f cluster-configuration.yaml

```

![image-20230123162833867](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231628919.png)



#### 2.7.4 查看安装进度

```
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l app=ks-install -o jsonpath='{.items[0].metadata.name}') -f

```

![image-20230123163150078](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231631142.png)

安装完毕

![image-20230123163943983](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231639036.png)

访问地址

http://192.168.133.110:30880

Account: admin
Password: P@88w0rd

要等待所有的pod都准备就绪，再登录

```
[root@k8smaster ~]# kubectl describe pod -n kubesphere-monitoring-system  prometheus-k8s-0
```

发现报错

```
Events:
  Type     Reason            Age                   From               Message
  ----     ------            ----                  ----               -------
  Warning  FailedScheduling  14m (x10 over 15m)    default-scheduler  0/3 nodes are available: 1 Insufficient cpu, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 1 node(s) had taint {node.kubernetes.io/disk-pressure: }, that the pod didn't tolerate.
  Warning  FailedScheduling  10m (x7 over 13m)     default-scheduler  0/3 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 2 node(s) had taint {node.kubernetes.io/disk-pressure: }, that the pod didn't tolerate.
  Normal   Scheduled         9m54s                 default-scheduler  Successfully assigned kubesphere-monitoring-system/prometheus-k8s-0 to k8sworker1
  Warning  FailedMount       7m51s                 kubelet            Unable to attach or mount volumes: unmounted volumes=[secret-kube-etcd-client-certs], unattached volumes=[prometheus-k8s-rulefiles-0 secret-kube-etcd-client-certs prometheus-k8s-token-f7c4f config config-out tls-assets prometheus-k8s-db]: timed out waiting for the condition
  Warning  FailedMount       5m35s                 kubelet            Unable to attach or mount volumes: unmounted volumes=[secret-kube-etcd-client-certs], unattached volumes=[secret-kube-etcd-client-certs prometheus-k8s-token-f7c4f config config-out tls-assets prometheus-k8s-db prometheus-k8s-rulefiles-0]: timed out waiting for the condition
  Warning  FailedMount       99s (x12 over 9m53s)  kubelet            MountVolume.SetUp failed for volume "secret-kube-etcd-client-certs" : secret "kube-etcd-client-certs" not found
  Warning  FailedMount       63s (x2 over 3m21s)   kubelet            Unable to attach or mount volumes: unmounted volumes=[secret-kube-etcd-client-certs], unattached volumes=[prometheus-k8s-token-f7c4f config config-out tls-assets prometheus-k8s-db prometheus-k8s-rulefiles-0 secret-kube-etcd-client-certs]: timed out waiting for the condition

```

报错：FailedMount挂载失败

解决etcd监控证书找不到问题

```
kubectl -n kubesphere-monitoring-system create secret generic kube-etcd-client-certs  --from-file=etcd-client-ca.crt=/etc/kubernetes/pki/etcd/ca.crt  --from-file=etcd-client.crt=/etc/kubernetes/pki/apiserver-etcd-client.crt  --from-file=etcd-client.key=/etc/kubernetes/pki/apiserver-etcd-client.key
```

![image-20230123180156870](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231801988.png)

删除



```
#查看istio-system命名空间下Evicted的pod
kubectl get pods  -n istio-system | grep Evicted

#删除Evicted的pod
kubectl get pods -n istio-system | grep Evicted |awk '{print $1}' |xargs kubectl -n istio-system delete pod
```

![image-20230123184214693](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231842740.png)



原因：

Kubernetes 节点上的资源会被 Pod 以及系统进程所使用 , 如果没有做任何限制的话 , 节点上的资源会被耗尽







## 3 多租户管理

![image-20230123205453425](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232054502.png)

### 3.1 创建用户

先创建人事--hr-zhang

![image-20230123193833553](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231938651.png)

密码：Jiang123

![image-20230123194020924](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231940987.png)

重新用人事的账号登录

![image-20230123194135496](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231941543.png)

创建企业空间管理员    boss-li

他可以创建集团公司的分公司（企业空间）

![image-20230123194357761](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231943819.png)

然后用boss-li登录，创建企业空间

![image-20230123194846381](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301231948446.png)

创建苏州公司的BOSS

然后继续创建其他用户

![image-20230123200649368](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232006448.png)



### 3.2 邀请用户加入企业空间

用boss-li账户进入苏州公司的企业空间

![image-20230123201745778](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232017871.png)

企业成员--邀请苏州的分公司经理做管理员

![image-20230123205029050](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232050125.png)

![image-20230123205209184](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232052245.png)



![image-20230123205248046](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232052105.png)

然后再用苏州分公司经理账户登录，邀请PM、DEV加入

![image-20230123205750712](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232057771.png)

![image-20230123210128685](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232101757.png)



### 3.3 PM 创建项目和组员

用项目经理账号登录

创建项目

![image-20230123212758778](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232127873.png)

![image-20230123212837683](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232128754.png)

邀请成员

![image-20230123212910240](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232129322.png)

![image-20230123212930575](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232129635.png)

![image-20230123213019082](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232130152.png)

权限等级 admin>operator>viewer



## 4 中间件部署

### 4.1 应用部署三要素

三要素：工作负载、POD数据挂载、服务

![image-20230123220321567](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232203640.png)

上面的部署、有状态副本集、守护进程集就是下面3个

![image-20230123220342641](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232203694.png)

部署中间件用有状态副本集

![image-20230123221959378](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232219433.png)

![image-20230123222016775](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232220826.png)



### 4.2部署Mysql

部署分析

![image-20230123222331190](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301232223255.png)

进入配置中心

![image-20230124213225449](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242132604.png)

![image-20230124213803447](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242138530.png)

mysql配置示例

```
[client]
default-character-set=utf8mb4
 
[mysql]
default-character-set=utf8mb4
 
[mysqld]
init_connect='SET collation_connection = utf8mb4_unicode_ci'
init_connect='SET NAMES utf8mb4'
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
```

![image-20230124214227481](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242142546.png)

创建完毕

![image-20230124214835344](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242148411.png)

创建存储卷

![image-20230124215940652](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242159714.png)

![image-20230124220909906](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242209973.png)

创建存储完毕

![image-20230124220931442](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242209507.png)

进入工作负载

![image-20230124221103683](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242211757.png)

![image-20230124221223883](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242212946.png)

![image-20230124221324834](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242213897.png)

添加mysql的容器镜像

![image-20230124221656531](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242216576.png)

![image-20230124222306843](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242223903.png)

使用默认端口

![image-20230124222754617](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242227665.png)



![image-20230124222349481](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242223529.png)

CPU1核，内存2G

添加环境变量

![image-20230124222535186](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242225237.png)

勾选同步主机时间

![image-20230124222607646](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242226689.png)

添加挂载

![image-20230124222949597](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242229661.png)

选择之前创建的卷

![image-20230124223018359](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242230411.png)

修改2个参数

![image-20230124223201720](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242232774.png)

继续挂载配置文件

![image-20230124223225404](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242232460.png)

修改2个参数

![image-20230124223354720](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242233771.png)

![image-20230124223422046](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242234110.png)

排查问题

![image-20230124230012181](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242300235.png)

```
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  18m   default-scheduler  0/3 nodes are available: 1 Insufficient memory, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 2 Insufficient cpu.
  Warning  FailedScheduling  18m   default-scheduler  0/3 nodes are available: 1 Insufficient memory, 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 2 Insufficient cpu.
```



K8S Pod 一直处于 Pending 状态
有几个原因可以阻止 Pod 运行，但我们将描述三个主要问题：

- 调度问题：无法在任何节点上调度 Pod。
- 镜像问题：下载容器镜像时出现问题。
- 依赖性问题：Pod 需要一个卷、Secret 或 ConfigMap 才能运行

```
kubectl describe node |grep -E '((Name|Roles):\s{6,})|(\s+(memory|cpu)\s+[0-9]+\w{0,2}.+%\))'
```

![image-20230124225530082](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301242255135.png)

解决办法：暂时加大虚拟机的内存和CPU



![image-20230125143000924](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251430072.png)

此时还不能外网访问

![image-20230125145918286](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251459350.png)

集群内部，直接通过应用的  【服务名.项目名】 直接访问。看DNS

```
  mysql -uroot -his-mysql-fgaq.his -p
```

![image-20230125150456930](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251504014.png)

查询命名空间

```
kubectl get ns
```

![image-20230125153445458](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251534519.png)

### 4.3 部署Mysql的负载均衡

#### 4.3.1 删除原来的Mysql的服务

![image-20230125153806566](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251538648.png)

![image-20230125153820773](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251538832.png)

删除时候不要勾选有状态副本集，那个是工作负载



#### 4.3.2 创建集群内部访问的服务

![image-20230125153926035](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251539107.png)

![image-20230125154059466](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251540533.png)

访问类型勾选如下，然后点击指定工作负载

![image-20230125154222494](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251542551.png)

![image-20230125154303352](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251543415.png)

添加3306端口

![image-20230125154722044](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251547107.png)

当前这个不能外网访问，只是集群内部访问

![image-20230125155128924](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251551007.png)



#### 4.3.3 创建mysql的外部访问服务

再次创建第二个服务，允许外网访问

![image-20230125160352110](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251603173.png)

![image-20230125160554007](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251605077.png)

勾选外部访问

![image-20230125160630874](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251606939.png)

![image-20230125160652490](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251606545.png)

测试

![image-20230125160849536](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251608596.png)

![image-20230125160909171](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251609222.png)





### 4.4部署Redis

架构图

![image-20230125161144009](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251611076.png)

```
#创建配置文件
## 1、准备redis配置文件内容
mkdir -p /mydata/redis/conf && vim /mydata/redis/conf/redis.conf


##配置示例
appendonly yes
port 6379
bind 0.0.0.0


#docker启动redis
docker run -d -p 6379:6379 --restart=always \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-v  /mydata/redis-01/data:/data \
 --name redis-01 redis:6.2.5 \
 redis-server /etc/redis/redis.conf
```





#### 4.4.1创建配置文件

创建配置文件

![image-20230125164159023](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251641093.png)

![image-20230125164227471](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251642534.png)

#### 4.4.2 创建工作负载

![image-20230125164546830](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251645900.png)



![image-20230125164916886](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251649958.png)

#### 4.4.3 添加存储和配置文件

![image-20230125165239656](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251652745.png)

添加配置文件

![image-20230125165356334](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251653398.png)

![image-20230125165519642](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251655715.png)

创建完毕

![image-20230125170543011](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251705080.png)

#### 4.4.4 创建服务

先删除原来的服务

![image-20230125171626641](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251716719.png)

先创建一个内部访问的服务

![image-20230125171911385](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251719461.png)

再创建一个外部访问的服务

![image-20230125172017684](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251720744.png)

![image-20230125172101389](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251721462.png)

勾选外网访问

![image-20230125172131166](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251721229.png)

用工具测试一下

![image-20230125172336520](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251723580.png)





### 4.5 部署Elasticsearch

```
# 创建数据目录
mkdir -p /mydata/es-01 && chmod 777 -R /mydata/es-01

# 容器启动
docker run --restart=always -d -p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms512m -Xmx512m" \
-v es-config:/usr/share/elasticsearch/config \
-v /mydata/es-01/data:/usr/share/elasticsearch/data \
--name es-01 \
elasticsearch:7.13.4

```

![image-20230125173504202](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251735267.png)

部署的关键在2个配置文件：elasticsearch.yml和jvm.options



### 4.6应用商店部署rabbitMq

进入应用商店，选择

![image-20230125173707392](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251737463.png)

点击部署

![image-20230125173900822](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251739894.png)

点击下一步

![image-20230125174028781](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251740852.png)

修改密码

![image-20230125174135697](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251741770.png)

创建中

![image-20230125174153866](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251741927.png)

编辑外部访问

![image-20230125174529709](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251745814.png)

![image-20230125174605382](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251746433.png)

创建完毕

![image-20230125174630952](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251746006.png)



### 4.7应用商店部署zookeeper

进入Helm网站https://helm.sh/

![image-20230125182341648](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251823713.png)

添加仓库位置

![image-20230125182458199](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251824251.png)

```bash
https://charts.bitnami.com/bitnami
```

用具有企业空间的账户重新登录

![image-20230125182651390](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251826464.png)

![image-20230125182851150](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251828203.png)

仓库建立成功

![image-20230125182917435](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251829491.png)

![image-20230125183501520](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251835607.png)

![image-20230125183514469](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251835539.png)

选择bitnami

![image-20230125183529838](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251835934.png)

![image-20230125183637051](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251836131.png)

![image-20230125183705051](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251837097.png)

创建成功

![image-20230125185035803](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251850853.png)

编辑外网访问

![image-20230125185253789](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251852850.png)

放开外网访问

![image-20230125185314620](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251853676.png)

成功

![image-20230125185332642](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301251853690.png)



## 5 若依SpringCloud本地部署

![image-20230126200429434](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262004587.png)

项目地址https://gitee.com/y_project/RuoYi-Cloud

先fork项目到自己的仓库



### 5.1 nacus启动

![image-20230126214447662](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262144753.png)

启动前先改一下配置application.properties

![image-20230126214607519](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262146562.png)

问题：

启动报错...Unknown column ‘encrypted_data_key‘ in ‘field list

原因：

数据库表 config_info、config_info_beta、his_config_info中需要新增字段 encrypted_data_key ，用来存储每一个配置项加密使用的秘钥。新版本的默认创建表的sql中已经添加该字段。

解决办法：

```
ALTER TABLE config_info ADD COLUMN encrypted_data_key text NOT NULL COMMENT "秘钥";
ALTER TABLE config_info_beta ADD COLUMN encrypted_data_key text NOT NULL COMMENT "秘钥";
ALTER TABLE his_config_info ADD COLUMN encrypted_data_key text NOT NULL COMMENT "秘钥";
```

Windows启动命令(standalone代表着单机模式运行，非集群模式):

```
 cd D:\dev\nacos-server-2.1.2\nacos\bin
 startup.cmd  -m standalone
```

账号：nacos

密码：nacos

![image-20230126223346960](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262233038.png)



### 5.2 数据库

新建数据库。

进入nacos修改配置文件

![image-20230126223723026](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262237085.png)



### 5.3 项目启动

安装nodejs

安装前端UI 执行

```
npm install --registry=https://registry.npmmirror.com
```

![image-20230126224427271](https://jiangteddy.oss-cn-shanghai.aliyuncs.com/img2/202301262244319.png)



## 6 SpringCloud云部署

### 6.1 上云准备和优化





### 6.2 迁移数据库





### 6.3 部署nacus







### 6.4 高可用部署







## 7 卸载

```
yum remove -y kubelet kubeadm kubectl
 
kubeadm reset -f
modprobe -r ipip
lsmod
rm -rf ~/.kube/
rm -rf /etc/kubernetes/
rm -rf /etc/systemd/system/kubelet.service.d
rm -rf /etc/systemd/system/kubelet.service
rm -rf /usr/bin/kube*
rm -rf /etc/cni
rm -rf /opt/cni
rm -rf /var/lib/etcd
rm -rf /var/etcd
```

