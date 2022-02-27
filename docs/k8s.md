# K8s 学习笔记

## K8s的安装与部署

准备四台虚拟机，使用centos7操作系统，搭建集群系统。

![image-20220609104049893](http://img.trivial.top/img/image-20220609104049893.png)

先搭建好一台虚拟机的环境，其他机器克隆就行。

### 开启虚拟机

虚拟机参数：

- 网络使用NAT模式
- 内存2g以上
- 2cpu (不然后面会报错)
- 硬盘我配的30g

![image-20220609104311773](http://img.trivial.top/img/image-20220609104311773.png)

如果网络连接不成功，可以配置试试这篇博客https://blog.csdn.net/duyuanjun123/article/details/119833105

>  第一次我的网络一直连不通是因为初始化系统时我没把网络连接开开在初始化操作系统的时候注意一下。

**在虚拟机中使用ip addr命令查看虚拟机ip**

![image-20220609104906146](http://img.trivial.top/img/image-20220609104906146.png)

因为虚拟机操作多少有点不方便，所以使用xshell或者fianlshell 连接

<img src="http://img.trivial.top/img/image-20220609105008915.png" alt="image-20220609105008915" style="zoom: 67%;" />

<img src="http://img.trivial.top/img/image-20220609105026097.png" alt="image-20220609105026097" style="zoom:67%;" />

### 配置yum源

**连接成功配置一下yum源**

1. 下载wget命令
   
   ```shell
   yum install wget -y  
   ```

2. 备份旧的yum仓库源
   
   ```java
   cd  /etc/yum.repos.d
   mkdir  repobak  # 创建文件夹，保存备份文件
   mv *.repo   repobak  #备份repo文件到文件
   ```

3. 清理旧包
   
   ```shell
   ~yum clean all
   ```

4. 下载新的阿里的yum源仓库，阿里的开源镜像：https://developer.aliyun.com/mirror/
   
   ```shell
   wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
   ```

5. 继续下载第二个仓库 epel仓库
   
   ```shell
   wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
   ```

6. 生成yum源缓存并更新yum源
   
   ```shell
   yum makecache
   yum update
   ```

### 下载docker

```shell
# 1、卸载旧的版本
yum remove docker \
                docker-client \
                docker-client-lastest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docekr-engine

# 2、需要的安装包
yum install -y yum-utils

# 3、设置镜像仓库 （阿里云镜像）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 

# 更新yum软件包索引
    yum makecache fast

# 4、安装docker相关的内容 docker-ce 社区办 ee企业版 
    yum install docker-ce docker-ce-cli containerd.io

# 5、启动docker
    systemctl start docker

# 6、测试docker是否启动成功
    docker -version
```

### 安装Kubernetes

**配置安装源地址**

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

如下出现错误

```
[Errno -1] repomd.xml signature could not be verified for kubernetes
```

则是 repo 的 gpg 验证不通过导致的，可以修改`repo_gpgcheck=0`跳过验证。

**最后，安装Kubernetes的关键组件。**

```shell
setenforce o

yum install -y kubelet kubeadm kubectl
# 启动kubelet、kubeadm、kubectl服务 kubeadm将使用kubelet服务以容器的方式部署和启动Kubernetes的主要服务，所以需要先启动kubelet服务。
systemctl enable kubelet && systemctl start kubelet
```

### 克隆虚拟机

<img src="http://img.trivial.top/img/image-20220609110301026.png" alt="image-20220609110301026" style="zoom:67%;" />

克隆出node1节点

### Master的安装和配置

所有节点关闭Selinux、iptables、swap分区

```shell
systemctl stop firewalld
systemctl disable firewalld
iptables -F && iptables -X && iptables -F -t nat && iptables -X -t nat
iptables -P FORWARD ACCEPT
swapoff -a
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
setenforce 0
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```

**拉取kubernetes默认配置**

```shell
kubeadm config print init-defaults > init-config.yaml
```

**打开该文件查看，发现配置的镜像仓库如下：**

```shell
imageRepository: k8s.gcr.io
```

该镜像仓库如果连不上，可以用国内的镜像代替：*imageRepository: registry.aliyuncs.com/google_containers*

打开init-config.[yaml](https://so.csdn.net/so/search?q=yaml&spm=1001.2101.3001.7020)，然后进行相应的修改，可以指定kubernetesVersion版本，pod的选址访问等。

**kubernetes镜像拉取**

```shell
kubeadm config images pull --config=init-config.yaml
```

采用国内镜像的方案，由于coredns的标签问题，会导致拉取coredns:v1.8.4拉取失败，这时候我们可以手动拉取，并自己打标签。失败信息如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/e63e45b520cc49878f13e1dfac16a62c.png#pic_center)

**解决方案：**手动拉取镜像

从docker hub上手动拉取镜像：

```shell
docker pull registry.aliyuncs.com/google_containers/coredns:1.8.4
```

修改标签：

```shell
# 重命名
docker tag registry.aliyuncs.com/google_containers/coredns:1.8.4 registry.aliyuncs.com/google_containers/coredns:v1.8.4
# 删除原有镜像
docker rmi registry.aliyuncs.com/google_containers/coredns:1.8.4
```

以下是镜像默认的标签：v1.8.4 ，而在镜像中的标签是1.8.4，所以会导致拉取失败。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4d6e6e4adf93410495ee1cbaf7d1db0d.png#pic_center)

**运行kubeadm init安装master节点**

```shell
kubeadm init --apiserver-advertise-address 192.168.10.3 --apiserver-bind-port=6443 --pod-network-cidr=10.244.0.0/16  --service-cidr=10.96.0.0/12 --kubernetes-version=1.24.1 --image-repository registry.aliyuncs.com/google_containers
```

如下报错解决方案

1. CPU数量问题 将虚拟机cpu数量设置为2即可

```shell
the number of available CPUs 1 is less than the required 2
```

2. ```shell
   [ERROR CRI]: container runtime is not running: output: time="2021-08-17T22:43:15+08:00" level=fatal msg="getting status of runtime: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
   ```

解决方案：

```shell
rm /etc/containerd/config.toml
systemctl restart containerd
```

> **kubeadm init**安装失败后需要重新执行，此时要先执行**kubeadm reset**命令。

若出现下面问题

```
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
```

解决办法

```shell
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

再次尝试安装出现如下问题

```shell
The HTTP call equal to ‘curl -sSL http://localhost:10248/healthz’ failed with error: Get “http://localhost:10248/healthz”: dial tcp [::1]:10248: connect: connection refused.
```

![image-20220609151052051](http://img.trivial.top/img/image-20220609151052051.png)

通过systemctl status kubelet 可以看到kubelet 挂掉了。

```shell
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/usr/lib/systemd/system/kubelet.service; enabled; vendor preset: disabled)
  Drop-In: /usr/lib/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: activating (auto-restart) (Result: exit-code) since 四 2022-06-09 15:36:26 CST; 146ms ago
     Docs: https://kubernetes.io/docs/
  Process: 12064 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=1/FAILURE)
 Main PID: 12064 (code=exited, status=1/FAILURE)
```

网上百度问题有很多种

**第一种 docker 和 kubelet 的驱动不一致**

查看docker驱动

```perl
docker info|grep Driver
```

​    Cgroup Driver: cgroupfs

查看kubelet驱动

```shell
systemctl show --property=Environment kubelet |cat
```

![img](https://img-blog.csdnimg.cn/c9c67625651047b4a9da7720ff7fd385.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbWF5aV94aWFvY2h1YW4=,size_20,color_FFFFFF,t_70,g_se,x_16)

修改docker驱动，查看/etc/docker/daemon.json文件，没有的话，手动创建，添加以下内容

```shell
{
  "registry-mirrors": ["https://ogeydad1.mirror.aliyuncs.com"], "exec-opts": ["native.cgroupdriver=systemd"]
}
```

重启docker

```shell
systemctl daemon-reload

systemctl restart docker
```

重启kubelet

```shell
systemctl daemon-reload

systemctl restart kubelet
```

**第二种 swap分区没有关** 

使用 `swapoff -a `命令关闭所有分区

还有可能是防火墙没有关

```shell
systemctl stop firewalld
systemctl disable firewalld
```

接着试初始化,出现如下报错

```shell
[kubelet-check] Initial timeout of 40s passed.

Unfortunately, an error has occurred:
    timed out waiting for the condition

This error is likely caused by:
    - The kubelet is not running
    - The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled)

If you are on a systemd-powered system, you can try to troubleshoot the error with the following commands:
    - 'systemctl status kubelet'
    - 'journalctl -xeu kubelet'

Additionally, a control plane component may have crashed or exited when started by the container runtime.
To troubleshoot, list all containers using your preferred container runtimes CLI.
Here is one example how you may list all running Kubernetes containers by using crictl:
    - 'crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock ps -a | grep kube | grep -v pause'
    Once you have found the failing container, you can inspect its logs with:
    - 'crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock logs CONTAINERID'
error execution phase wait-control-plane: couldn't initialize a Kubernetes cluster
To see the stack trace of this error execute with --v=5 or higher
```

接着看日志发现kubelet报错

```shell
master kubelet[15135]: E0521 21:07:57.697075   15135 kubelet.go:2419] "Error getting node" err="node
```

查博客得知，应该是版本太高了，v1.24 已经弃用了dockershim ,换成v1.23.0 试试https://blog.csdn.net/weixin_66536807/article/details/124903478

```shell
# 删除kubeadm、kubelet、kubectl
yum remove kubeadm、kubelet、kubectl

# 安装1.23.0 版本
yum install -y kubeadm-1.23.0-0  kubelet-1.23.0-0 kubectl-1.23.0-0 --disableexcludes=kubernetes
```

```shell
kubeadm init --apiserver-advertise-address 192.168.10.3 --apiserver-bind-port=6443 --pod-network-cidr=10.244.0.0/16  --service-cidr=10.96.0.0/12 --kubernetes-version=1.23.0 --image-repository registry.aliyuncs.com/google_containers
```

![image-20220609173745304](http://img.trivial.top/img/image-20220609173745304.png)

**成功！！！**

```shell
# 根据提示创建文件夹，执行命令
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Node 节点安装和配置

- **node节点所在的机器也要按照前置环境的步骤走一遍（我是之前创建有快照，直接按着快照复制了一个node）**

- **安装像master那样增加kubernetes的yum源**

**安装kubelet 和 kubeadm**

```shell
yum install -y kubeadm-1.23.0-0  kubelet-1.23.0-0 --disableexcludes=kubernetes
```

**运行以下命令，并设置开机启动：**

```shell
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```

**执行join命令：**

```shell
# 该命令来自master安装成功后的最后两行信息
kubeadm join 192.168.10.3:6443 --token 7adbpw.vph00nljcfvjf3t0 \
    --discovery-token-ca-cert-hash sha256:d28a79426cf1f5e92edcb8f48a9bd70d4a9ff9f1e231519f28600cfeaa91452a
```

出现bug

kubetcl \*join\* node节点加入集群 \*卡住\*，无响应

在kubectl join xxxx 的最后加入一个参数`--v=2`

```shell
# 该命令来自master安装成功后的最后两行信息
kubeadm join 192.168.10.3:6443 --token 7adbpw.vph00nljcfvjf3t0 \
    --discovery-token-ca-cert-hash sha256:d28a79426cf1f5e92edcb8f48a9bd70d4a9ff9f1e231519f28600cfeaa91452a -- v=2
```

默认token有效期为24小时，当过期之后，该token就不可用了。这时就需要重新创建token，可以直接使用命令快捷生成：

```shelll
kubeadm token create --print-join-command
```

又出现bug

```shell
k8s.io/dns/pkg/dns/dns.go:150: Failed to list *v1.Service: Get https://10.96.0.1:443/api/v1/services?resourceVersion=0: dial tcp 10.96.0.1:443: getsockopt: no route to host
```

搜了老半天找到解决办法

```shell
# 回到kubernees-maser  依次输入列命令
systemctl stop kubelet
systemctl stop docker
iptables --flush
iptables -tnat --flush
systemctl start kubelet
systemctl start docker
```

又出现bug

```shell
The HTTP call equal to 'curl -sSL http://localhost:10248/healthz' failed with error: Get "http://localhost:10248/healthz": dial tcp [::1]:10248: connect: connection refused.
```

这个是之前的bug，我忘记配置了 docker和Kubernetes 的驱动不一致导致的

还是不管用 后来感觉好像是swap 没关掉试了试 swapoff -a 终于成功了。

此时，在master节点上执行*kubectl get nodes*能看到该node节点表示成功，此时状态还是**NOT Ready**。

**安装网络插件**

```shell
  # 安装Calico CNI插件
  kubectl apply -f "https://docs.projectcalico.org/manifests/calico.yaml"
  # 安装weave插件
  kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

再次查看状态发现还是有两个是处于containerCreating状态

![image-20220610092946989](http://img.trivial.top/img/image-20220610092946989.png)

使用命令查看详细信息 kubectl describe pod coredns-6d8c4cb4d-qmp6g --namespace=kube-system

```
Events:
  Type     Reason                  Age                   From               Message
  ----     ------                  ----                  ----               -------
  Normal   Scheduled               20m                   default-scheduler  Successfully assigned kube-system/coredns-6d8c4cb4d-qmp6g to master
  Warning  FailedCreatePodSandBox  20m                   kubelet            Failed to create pod sandbox: rpc error: code = Unknown desc = [failed to set up sandbox container "bf1732938b8edbad4c1e768934e616f50e1496e933efd0880b80226645d3e627" network for pod "coredns-6d8c4cb4d-qmp6g": networkPlugin cni failed to set up pod "coredns-6d8c4cb4d-qmp6g_kube-system" network: error getting ClusterInformation: Get "https://[10.96.0.1]:443/apis/crd.projectcalico.org/v1/clusterinformations/default": x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes"), failed to clean up sandbox container "bf1732938b8edbad4c1e768934e616f50e1496e933efd0880b80226645d3e627" network for pod "coredns-6d8c4cb4d-qmp6g": networkPlugin cni failed to teardown pod "coredns-6d8c4cb4d-qmp6g_kube-system" network: error getting ClusterInformation: Get "https://[10.96.0.1]:443/apis/crd.projectcalico.org/v1/clusterinformations/default": x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")]
  Normal   SandboxChanged          4m55s (x71 over 20m)  kubelet            Pod sandbox changed, it will be killed and re-created
```

出现个问题

搜博客 发现**kubeadm reset**命令执行后的输出结果：

```shell
The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
```

然后根据上面的提示把/etc/cni/net.d目录删掉，然后重新执行kubeadm init命令就可以了。

验证k8s集群是否安装完成

- 执行==*kubectl get pods --all-namespaces*==看pod状态是否都正确
- 如果发现错误状态的pod，可以执行==*kubectl --namespace=kube-system describe pod <pod_name>*==来查看错误原因。

> 附：  

删除工作节点：`kubectl delete nodes k8s-node1`

修改hostname ：`hostnamectl set-hostname newname`

查看kubelet 日志 ：journalctl -xefu kubelet

## Pod

### Pod 的基本操作

#### 创建pod

**1. 先定义模板文件**

>  examplepod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: examplepod
spec:
  containers:
  - name: examplepod-container
    image: busybox
    imagePullPolicy: IfNotPresent
    command: ['sh','-c']
    args: ['echo"Hello Kubernetes"; sleep 3600']
```

模板的参数含义:

> - apiversion表示使用的API版本。v1表示使用Kubernetes API的稳定版本。
> - kind表示要创建的资源对象，这里使用关键字Pod。
> - metadata表示该资源对象的元数据。一个资源对象可拥有多个元数据，其中一项是name，它表示当前资源的名称。
> - spec表示该资源对象的具体设置。其中containers表示容器的集合，这里只设置了一个容器，该容器的属性如下。
>   - name:要创建的谷器名称。
>   - image:容器的镜像地址。
>   - imagePullPolicy:镜像的下载策略，支持3种imagePullPolicy，如下所示。
>     - Always:不管镜像是否存在都会进行一次拉取。
>     - Never:不管镜像是否存在都不会进行拉取。
>     - ifNotPresent:只有镜像不存在时，才会进行拉取
>   - command:容器的启动命令列表（不配置的话，使用镜像内部的命令）。
>   - args:启动参数列表（在本例中是输出文字“HelloKubernetes !”并休眠3600s ) 。

运行以下命令，通过模板创建Pod。

```shell
kubectl apply -f  examplepod.yaml
```

创建成功后，可通过以下命令查询当前运行的所有Pod.

```shell
kubectl get pod
```

#### 查询pod

Pod创建后，最常用的功能就是查询。可以用以下命令查询Pod的状态。

```yaml
kubectl get pod {pod名称}
```

还可以在查询命令中带上参数-w，以对Pod状态进行持续监控。只要Pod发生了变化，就会在控制台中输出相应信息。命令如下。

```shell
kubectl get pod {Pod名称} -w
```

![image-20220610152151704](http://img.trivial.top/img/image-20220610152151704.png)

另外，还可以在查询命令中带上-o wide参数，输出Pod的更多概要信息（如调度到哪台机器上，Pod本身的虚拟IP等信息）。命令如下。

```shell
kubectl get pod {Pod名称} -o wide
```

![image-20220610152322356](http://img.trivial.top/img/image-20220610152322356.png)

get命令除了可以显示简要的运行信息外，还可以输出完整信息。它支持多种格式的输出，如可以用yaml和Json方式输出，命令如下。

```shell
kubectl get pod examplepod --output yaml
kubectl get pod examplepod --output json
```

一般情况下，如果要查询Pod更详细的信息（包括状态、生命周期和执行情况等），除了将其输出为yaml或json格式，还可以用describe命令查看详情，格式如下。

```shell
kubectl describe pods {pod名称}
```

该命令会输出比较全面的信息，包括资源的基本信息、容器信息、准备情况、存储卷信息及相关的事件列表。在资源部署时如果遇到问题，可以使用此命令查看详情，分析部署错误的原因。

如果要查询Pod本身输出的日志信息，还可以使用logs命令，格式如下。

```shell
kubectl logs {pod名称}
```

![image-20220610152852319](http://img.trivial.top/img/image-20220610152852319.png)

#### 修改pod

可以用replace命令来修改原先设置的Pod属性，命令格式如下。

```shell
kubectl replace -f { pod模板路径}
```

修改之前示例中定义的Pod，使它输出“Hello Kubernetesreplaced!”。先打开examplepod.yml文件。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: examplepod
spec:
  containers:
  - name: examplepod-container
    image: busybox
    imagePullPolicy: IfNotPresent
    command: ['sh','-c']
    args: ['echo "Hello Kubernetes replace"; sleep 3600']
```

>  提示:Pod有很多属性无法修改，比如containers的image属性，spec下的activeDeadline seconds、tolerations属性等。如果一定要修改，则需要加上--force参数，相当于重新创建Pod，命令如下。

```shell
kubectl replace -f {pod模板路径}―-force
```

#### 删除pod

Pod的删除非常简单，只要执行以下命令即可。

```shell
kubectl delete pod { Pod名称}
```

![image-20220610153859714](http://img.trivial.top/img/image-20220610153859714.png)

另外，还可以基于模板文件删除资源，如以下命令所示。

```shell
kubectl delete -f{模板文件名称}
```

### Pod 模板详解

在之前创建Pod的示例中，我们使用了基本的Pod模板来定义资源，但Pod模板包含的内容不仅只有示例中的那些，还可以定义非常丰富的内容。
以下是Pod模板的主要内容及对应说明。目前只需要大致了解有这些属性就足够了，不需要把每个都完全理解，在以后的章节中将逐一进行演示。

```yaml
apiversion : v1 #版本，必填，v1代表稳定版本
kind: pod #类型，必填，Pod
metadata: #元数据，表示资源的标识信息
    name: string #元数据，必填，Pod的名字
    namespace: string #元数据，Pod的命名空间
    labels: #元数据，标签列表
    - key: value #元数据，可定义多个标签的键/值对
    annotations: #元数据，自定义注解列表
    - key: value #元数据，可定义多个标签的键/值对
spec: 
    containers: #Pod中的容器列表，必填，可以有多个容器
    - name: string #容器名称，必填
      image: string #容器中的镜像地址，必填
      imagePullPolicy: [Always/Never|IfNotPresent] #获取镜像的策略Always表示下载镜像;
        #ifNotPresent表示优先使用本地镜像，否则下载镜像;Never表示仅使用本地镜像
    command: [ string ] #容器的启动命令列表（(不配置的话，使用镜像内部的命令)
    args : [ string ] #启动命令参数列表
    workingDir: string #容器的工作目录
    volumeMounts: #挂载到容器内部的存储卷设置
    - name: string #为了引用Pod定义的共享存储卷的名称，要用volumes[]部分定义的卷名
      mountPath: string #存储卷在容器内挂载的绝对路径，应少于512个字符
      readOnly: boolean #是否为只读模式
      ports: #容器需要暴露的端口号列表
    - name: string #端口名称
      containerPort: int #容器要暴露的端口
      hostPort: int #容器所在主机监听的端口（把容器暴露的端口映射到宿主机的端口)
      protocol: string #端口协议，支持TCP和UDP，默认为TCP
    env: #容器运行前要设置的环境变量列表
    - name: string #环境变量名称
      value: string #环境变量值
    resources: #资源限制和请求的设置
        limits: #资源限制的设置
            cpu: string #CPU的限制，单位为CPU内核数。将用于docker run --cpu-quota #也可以使用小数，例如0.1，0.1等价于表达式100m，表示100milicpu
            memory: strina #内存限制，单位可以为 MiB/GiB/MB/GB (1MiB=1024×1024B，#1MB=1000×1000B），将用于docker run --memory参数
            requests: #资源请求的设置
            cpu: String #CPU请求，容器启动时的初始可用数量，将用于docker run --cpu-shares参数
            memory : string
            #内存请求，容器启动时的初始可用数量
        livenessProbe: #Pod内容器健康检查的设置，当探测几次无响应后将自动重启该容器，
        #检查方法有exec、httpGet和tcpsocket，对一个容器只要设置一种方法即可
            exec: #通过exec方式来检查Pod内各容器的健康状况
            command: [String] #exec方式需要指定的命令或脚本
            httpGet: #通过httpGet方式来检查Pod中各容器的健康状况，需要指定path、port
                path: stringport: 
                numberhost: string
                scheme : stringhttpHeaders :
                - name : string
                  value: string
            tcpsocket :
            #通过tcpsocket检查Pod中各容器的健康状况
                port: number
                initialDelaySeconds: 0 #容器启动完成后，首次探测的时间（单位为秒)
                timeoutseconds: 0 #对容器进行健康检查时探测等待响应的超时时间(单位为秒，默认为1s)
                periodseconds: 0 #对容器监控检查的定期探测时间设置（单位为秒），默认10s一次
                successThreshold: 0
                failureThreshold: 0
                securityContext: #安全配置
                    privileged : false
            restartPolicy: [Always |Never |OnFailure] #Pod的重启策略，Always表示不管以何种方式终止运行，kubelet都将重启;OnFailure表示只有pod以非0码退出才重启;Never表示不再重启该Pod
            nodeselector: object #节点选择，设置nodeselector表示将该Pod调度到包含这个标签的节点上，以key: value格式来指定
            imagePullSecrets: #拉取镜像时使用的secret名称，以key:secretkey格式指定
            - name: string
            hostNetwork : false #是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
          volumes :
         #在该Pod上定义共享存储卷列表
        - name: string
          #共享存储卷名称
          emptyDir: { }
          #类型为emptyDir的存储卷，与Pod有相同生命周期的一个临时目录，为空值
          hostPath: #类型为hostPath的存储卷，将会挂载Pod所在宿主机的目录
              path: string #Pod所在宿主机的目录，该目录将在容器中挂载
          secret: #类型为secret的存储卷，在容器内部挂载集群中预定义的secret对象
            secretName: string
            items:
            - key: string
                 path : string
          configMap: #类型为configMap的存储卷，挂载预定义的configMap对象到容器内部
            name : string
            items:
            - key : string
              path: string
```

如果要了解一个正在运行的Pod的配置，可以通过以下命令来获取。

```shell
kubectl get pod {pod名称}-o yaml
```

### pod 与容器

#### Pod创建容器的方式

之前描述的Pod模板和Docker-Compose 配置非常相似，但Pod模板涉及其他部署参数的设定，相对更复杂。先排除与容器无关的配置参数，在模板的Containers部分，指明容器的部署方式。在部署过程中，会转换成对应的容器运行时( container runtime）命令，例如，对于Docker，会转换成类似于Docker run的命令。

在最开始的例子中，yml文件内容如下。

```shell
apiVersion: v1
kind: Pod
metadata:
  name: examplepod
spec:
  containers:
  - name: examplepod-container
    image: busybox
    imagePullPolicy: IfNotPresent
    command: ['sh','-c']
    args: ['echo"Hello Kubernetes"; sleep 3600']
```

在Kubernetes将Pod调度到某个节点后，kubelet会调用容器运行时（本例中为Docker），执行如下所示的命令。

```shell
docker run --name examplepod-container busybox sh -c 'echo"Hello Kubernetes !"; sleep 3600'
```

>  提示: command和args设置会分别覆盖原Docker镜像中定义自EntryPoint 与CMD，在使用时请务必注意以下规则。

- 如果没有在模板中提供command或args，则使用Docker镜像中定义的默认值运行。
- 如果在模板中提供了command，但未提供args，则仅使用提供的command。Docker镜像中定义的默认的EntryPoint和默认的命令都将被忽略。
- 如果只提供了args，则Docker镜像中定义的默认的EntryPoint将与所提供的args组合到一起运行。
- 如果同时提供了command和args，Docker镜像中定义的默认的EntryPoint和命令都将被忽略。所提供的command和args将会组合到一起运行。

同样，在Pod模板的Container设置中的各项信息，在运行时都会转换为类似的容器命令来执行。Container的基础信息的设置如下所示。

```yaml
containers: #Pod中的容器列表，必填，可以有多个容器
- name: string #容器的名称，必填
  image: string #容器中的镜像地址，必填
  imagePullPolicy: [Always |Never| IfNotPresent] #获取镜像的策略。Always表示下载镜像;
  #ifNotPresent表示优先使用本地镜像，否则下载镜像;Never表示仅使用本地镜像
  command: [ string] #容器的启动命令列表（(不配置的话，使用镜像内部的命令)
  args: [ string ] #启动命令参数列表
  workingDir: string #容器的工作目录
  volumeMounts: #挂载到容器内部的存储卷设置
  - name: string #为了引用Pod定义的共享存储卷的名称，要用 volumes []部分定义的卷名
    mountPath: string #存储卷在容器内挂载的绝对路径，应少于512个字符
    readOnly: boolean #是否为只读模式
    ports: #容器需要暴露的端口号列表
    - name: string #端口名称
      containerPort: int #容器要暴露的端口
      hostPort: int #容器所在主机监听的端口（把容器暴露的端口映射到宿主机的端口)
      protocol: string #端口协议，支持TCP和UDP，默认为TCP
    env: #容器运行前要设置的环境变量列表
    - name: string #环境变量名称
    value: string #环境变量值
```

容器中关键的基本信息（如name、image、imagePullPolicy、command、args）在之前的示例中已经演示并讲解过，接下来将演示另外3组——volumeMounts、ports、env。

##### **1. volumesMounts 配置信息**

容器运行时通常会提供一些机制来将存储附加到容器上。例如，Docker有两种容器机制:一种是数据卷( data volume) ，它可以将容器内的文件或目录映射到宿主机上的文件或目录中，其命令格式为$docker run -v/{主机的目录}:/{映射到容器的目录}{镜像名称};另一种是数据卷容器(data volume container)，不过其本质使用的还是数据卷，这种容器一般用在一组相关的容器中，用于专门处理数据存储以供其他容器挂载。

不管是数据卷还是数据卷容器，其存留时间通常超过其他容器的生命周期。由于生命周期不同步，因此实现起来非常缺乏灵活性。

为了解决这些问题，Kubernetes在数据卷的基础上，又新增加一套自己的存储卷(volume抽象机制。**该机制不仅允许Pod中的所有容器方便地共享数据，还允许存储卷与Pod中的其他容器保持完全一致的生命周期。**

首先，创建examplepodforvolumemount.yml文件。

```shell
apiversion: v1
kind: Pod
metadata :
  name: examplepodforvolumemount
spec:
  containers:
  - name: containerforwrite
    image: busybox
    imagePullPolicy: ifNotPresent
    command: ['sh', '-c']
    args: [ 'echo "test data ! " >/write_dir/data; sleep 3600']
    volumeMounts:
      - name: filedata
        mountPath: /write_dir- 
  - name: containerforread
    image: busybox
    imagePullPolicy: ifNotPresent
    command: ['sh','-c']
    args: ['cat / read_dir/ data; sleep 3600']
    volumeMounts :
    - name: filedata
      mountPath: /read_dir
  volumes:
    - name: filedata
      emptyDir: {}
```

​    在本例中，我们创建了两个容器。一个是containerforwrite，它向数据卷写入数据，会向/write dir/data文件写入“test data!”文本。容器内的数据卷地址为/write_dir，它引用的存储卷为filedata。

​    另一个容器是containerforread，他会从/read_dir/ data文件中读取文本，并将其输出到控制台（后续可以通过日志查询方式读取输出到控制台的文本）。容器内的数据卷地址为/read_dir，它引用的存储卷为filedata。

​    本例中还创建了一个存储卷，其名称为filedata，这个名称会被容器设置中的数据卷所引用。存储卷的类型是emptyDir，它是最基础的类型，表示纯净的空目录，其生命周期和所属的Pod完全一致（后续会讲解更多的种类）。对于例子中的两个容器，虽然数据卷地址不同(一个是/write_dir，一个是/read_dir)，但因为它们都是映射到同一个空目录下的，所以本质上仍在同一个文件夹内进行操作。

执行命令，创建pod

```shell
kubectl apply -f examplepodforvolumemount.yml
```

通过以下命令，查看Pod的运行情况，READY 2/2表示两个容器都已成功运行。

```shell
kubectl get pods examplepodforvolumemount
```

此时可以通过logs命令，查看Pod中containerforread容器的日志。

```shell
kubectl logs examplepodforvolumemount containerforread
```

可以看到，containerforread容器已经读取到在containerforwrite容器中写入的文本，并已将其输出到控制台

##### 2.ports配置信息

容器运行时通常会提供一些机制以将容器端口暴露出来，并映射到主机的端口上，以便其他人能通过“主机IP:端口”访问容器所提供的服务，例如，Docker的命令$ docker run -p {宿主机端口}:{容器端口}{镜像名称}。同样，Pod模板中也提供了这个功能。为了通过例子进行演示，首先，创建examplepodforport.yml文件。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: examplepodforport
spec:
  containers:
  - name: containerforport
    image: nginx
    ports:
    - name: portfornginx
      containerPort: 80
      hostPort: 8081
      protocol: TCP
```

在本例中，Nginx镜像中默认定义的对外提供服务的端口为80。通过**containerPort属性，我们将80端口暴露出来**，再通过
**hostPort属性将其映射到宿主机的端口8081上**，以便通过“主机IP:端口”访问容器所提供的服务，其中**protocol为端口协议，支持TCP和UDP，默认为TCP。**

执行以下命令，创建Pod。

```yaml
kubectl apply -f examplepodforport. yml
```

通过以下命令，查看Pod的运行情况，直到状态变为Running。

```shell
kubectl get pods examplepodforport
```

Pod创建完成后，执行以下命令，查看Pod具体被分配到哪台Node上。

```shell
kubectl describe pods examplepodforport
```

![image-20220610174224061](http://img.trivial.top/img/image-20220610174224061.png)

访问 192.168.10.4::8081 就可以访问到nginx的欢迎页面

> 注意:以上案例仅为了说明Kubernetes是如何创建容器的，这种类似于Docker直接映射到主机端口的方式，在Kubernetes中强烈不推荐。Pod只是一个运行服务的实例，随时可能在一个Node上停止，而在另一个Node上以新的IP地址启动新的Pod，因此它不能以稳定的IP地址和端口号提供服务。若要稳定地提供服务，则需要服务发现和负载均衡能力。Kubernetes提供了Service抽象机制，在后续的章节中会详述。

3. env配置信息
   
   容器运行时通常还会提供一些机制来输入可动态配置的一些环境变量，以供容器中的应用程序使用。如在Docker中，配置环境变量的命令为$ docker run --env {变量1}={值1 } --env {变量2}={值2} ...{镜像名称}。同样，Pod模板中也提供了这个功能，为了通过例子进行演示，首先，创建examplepodforenv.yml文件。
   
   ```shell
   apiVersion: v1
   kind: Pod
   metadata:
     name: examplepodforport
   spec:
     containers:
     - name: containerforport
       image: nginx
       imagePullPolicy: IfNotPresent
       env:
       - name: parameter1
         value: "good morning"
       - name: parameter2
         value: "good night"
       command: ['sh','-c']
   args: ['echo "${parameter1} ${parameter2}"; sleep 3600']
   ```

在模板中定义了一个名为containerforenv的容器，向它传入了两个环境变量:其中一个名为parameter1，值为good
morning !;另一个变量名为parameter2，值为good night !。在本例中，将通过在容器中执行命令的方式，将传入的两个环境变量拼接到一起并输出到日志。

#### Pod 组织容器的方式

​    Pod的设计初衷在于同时运行多个共同协作的进程（作为容器来运行）。Pod中的各个容器总是作为一个整体，同时调度到某台Node上，容器之间可以共享资源、网络环境和依赖，并拥有相同的生命周期。

​    当然，在同一个Pod中同时运行和管理多个容器，是一种相对高级的用法，只在容器必须要紧密配合进行协作的时候才使用此模式。

##### 1. 容器如何组成一个Pod

Pod只是一种抽象，并不是一个真正的物理实体，表示一组相关容器的逻辑划分。每个Pod都包含一个或一组密切相关的业务容器，除此之外，每个Pod都还有一个称为“根容器”的特殊Pause容器

![image-20220610175512344](http://img.trivial.top/img/image-20220610175512344.png)

## 储存与配置

### 网络储存—— nfs 安装配置及挂载

#### 安装NFS

首先我们在k8s的3台服务器上都安装[nfs](https://so.csdn.net/so/search?q=nfs&spm=1001.2101.3001.7020)服务,执行命令如下：

```shell
#所有机器安装
yum install -y nfs-utils
```

在主节点进行如下的操作：

```shell
echo "/nfs/data/ *(insecure,rw,sync,no_root_squash)" > /etc/exports
```

 上面的命令表示我们准备在**master节点暴露/nfs/data这个目录,而且是以非安全的方式，读写的权限**暴露出去

 在master节点创建要暴露的文件夹：

```java
mkdir -p /nfs/data
```

启动rpc远程绑定同步目录服务,并且是开机自启：

```shell
systemctl enable rpcbind --now
```

下面启动nfs服务器并且使配置生效：

```shell
systemctl enable nfs-server --now
#配置生效
exportfs -r
```

 使用nfs命令检查暴露的目录：

```java
exportfs
```

**在从节点进行如下的操作，让从节点同步主节点的目录：**

在两个从节点使用如下命令进行检查主节点提供了哪些目录可以同步的，是一个检查命令

```shell
showmount -e 192.168.10.3
```

 下面在两个从节点创建挂载目录：

```shell
#执行以下命令挂载 nfs 服务器上的共享目录到本机路径 /nfs/data
mkdir -p /nfs/data
```

上面的/nfs/data目录不一定也要和主节点一样,可以是/nfs/test都行

下面使用挂载命令，同步主节点的目录：

```shell
mount -t nfs 192.168.10.3:/nfs/data /nfs/data
```

上面的192.168.10.3是主节点的私有ip地址

**下面进行测试**

 首先我们在master节点的/nfs/data/目录下创建文件

![img](https://img-blog.csdnimg.cn/cfc790b2b13147e7b027a78b0fb409a2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA54ix5LiK5Y-j6KKL55qE5aSp56m6,size_11,color_FFFFFF,t_70,g_se,x_16)

上面可以发现主节点我们创建了test111文件，下面我们到从节点查看：

![](http://img.trivial.top/img/image-20220620170422615.png)

**下面使用创建一个Deployment来测试挂载nginx**
exampledeployfornfs.yml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: exampledeployfornfs
spec:
  replicas: 2
  selector:
    matchLabels:
      example: examplefornfs
  template:
    metadata:
      labels:
        example: examplefornfs
    spec:
      containers:
      - name: containerfornfs
        image: busybox
        imagePullPolicy: IfNotPresent
        command: ['sh','-c']
        args: ['echo"The host is ${hostname}">> /dir/data;sleep 3600']
        volumeMounts:
        - name: nfsdata
          mountPath: /dir
      volumes:
      - name: nfsdata
        nfs:
          path: /nfs/data/testDir
          server: 192.168.10.3
```

**我们需要在/nfs/data目录下提前将testDir目录建好**

**kubectl apply -f deploty-nginx.yaml**

创建后可以通过$ kubectl get deploy命令查看启动状态

![image-20220620173808234](http://img.trivial.top/img/image-20220620173808234.png)

接下来，验证这两个Pod是否都读写了同一个存储卷上的同一个文件。在本例中，我们先读取第一个Pod，即exampledeployfornfs-87896f598-2fnrq，使用以下命令进入Pod内部的命令界面。

```shell
kubectl exec -ti exampledeployfornfs-7d78b97455-n2l9d -- /bin/sh
```

接下来，执行以下命令，输出在存储卷中写入的文件内容。

```shell
 cat /dir/data
```

可以看到，由Deployment控制器生成的两个Pod都已经成功地将信息写入同一个存储卷的同一个文件中

![image-20220621090513334](http://img.trivial.top/img/image-20220621090513334.png)

其实不管哪个Pod，它们都直接引用NFS服务器上的文件，在所有的编辑操作中也都直接处理NFS服务器上的文件

由于网络存储卷使用的是不同于Kubernetes的额外系统，因此从使用角度来说，网络存储卷存在两个问题。

- 存储卷数据清理问题，需要人工清理。

- 在Pod模板中需要配置所使用存储的细节参数，于是与所使用的存储方案产生高度耦合。若基础设施和应用配置之间没有分离，则

不利于维护。

### 持久储存卷

#### PV与PVC

PV表示持久存储卷，定义了Kubernetes集群中可用的存储资源，其中包含存储资源实现的细节，如包含如何使用NFS/iSCSI/GlusterFS/RDB/azureDisk/flocker 等资源的具体设置。

PVC表示持久存储卷的申请，是由用户发起的对存储资源的请求。申请中只包含请求资源的大小和读写访问模式，无须关注具体的资源实现细节，Kubernetes会自动为其绑定符合条件的PV。

#### PV 和 PVC 基本操作

##### 创建PV

创建 examplefornfspv.yml

```shell
apiVersion : v1
kind: PersistentVolume
metadata:
  name: examplefornfspv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadwriteMany

  persistentVolumeReclaimPolicy: Recycle
  storageClassName: examplenfs
  nfs:
    path: /nfs/data
    server: 192.168.10.3
```

**参数解释**

> capacity：表示PV的容量，通过storage子属性可以指定占用的具体存储资源（如NFS）的大小，在本例中设定为1Gi。
> 
> accessModes：定义 PV 对具体存储资源（如 NFS）的访问模式。一共有 3种访问模式，分别为ReadWriteOnce（该卷可以被单个节点以读写模式挂载），ReadOnlyMany（该卷可以被多个节点以只读模式挂载），ReadWriteMany（该卷可以被多个节点以读写模式挂载）。在本例中使用ReadWriteMany。
> 
> persistentVolumeReclaimPolicy：表示当删除PVC时，PV资源的回收策略。一共有3种策略，分别为Retain（保留）、Recycle（自动回收）、Delete（自动删除）。当前只有NFS和hostPath支持Recycle策略，AWSEBS、GCE PD、Azure Disk和Cinder卷支持Delete策略。在本例中使用Recycle。
> 
> storageClassName：表示PV资源的描述性分类名称，例如，可以使用“ssd”“slowdisk”等具备分类的描述性名称。后续在创建PVC时可以引用这个名称来绑定PV。
> 
> nfs：表示该PV使用NFS服务器作为具体的存储资源，server和path属性为之前网络存储卷示例中配置的NFS服务器及共享目录。

接下来，执行以下命令，创建PV。

```shell
kubectl apply -f examplefornfspv.yml
```

```shell
kubectl get pv
```

![image-20220621092141077](http://img.trivial.top/img/image-20220621092141077.png)

可以以下命令，可以查询PV资源的详情。

```shell
 kubectl describe pv {PV名称}
```

##### 创建PVC

编写文件examplefornfspvc.yml

```shell
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: examplefornfspvc
spec:
 accessModes:
     - ReadWriteMany
 storageClassName: "examplenfs"
 resources:
     requests:
        storage: 500Mi
```

PVC创建完成后，可以通过以下命令查询PVC资源

```shell
kubectl get pvc
```

PVC已成功创建，其STATUS属性为

Bound，表示已成功绑定到符合PVC资源申请条件的PV上

![image-20220621094209253](http://img.trivial.top/img/image-20220621094209253.png)

此时如果再通过$ kubectl get pv命令查看已创建的PV，可以发现其STATUS属性由之前的Available变为Bound，CLAIM属性由空值变为刚才创建的PVC

![image-20220621094243349](http://img.trivial.top/img/image-20220621094243349.png)

PVC创建完成后，为了定义Pod并使用PVC引用的资源，首先，创建exampledeployforpvc.yml文件。

```shell
apiVersion: apps/v1
kind: Deployment
metadata:
 name: exampledeployforpvc
spec:
 replicas: 2
 selector:
  matchLabels:
    example: exampleforpvc
 template:
  metadata:
    labels:
      example: exampleforpvc
  spec:
    containers:
    - name: containerforpvc
      image: busybox
      imagePullPolicy: IfNotPresent
      command: ['sh', '-c']
      args: ['echo "The host is ${HOSTNAME}" > /dir/dataforpvc;sleep 3600']
      volumeMounts:
      - name: pvcdata
        mountPath: /dir
    volumes:
    - name: pvcdata
      persistentVolumeClaim:
        claimName: examplefornfspvc
```

​    本例中创建的名为containerforpvc的容器用于向存储卷写入数据，容器内的存储卷映射地址为/dir，它引用的存储卷为pvcdata。容器启动后会以追加方式（使用了echo ...>>...命令）向/dir/dataforpvc文件写入文本，这段代码中使用$(hostname)环境变量获取主机名称，对于Pod中的容器，获取到的是Pod名称。由于Deployment控制器拥有多个Pod，因此通过这种方式可在同一个文件下由多个Pod写入多行信息。

接下来，执行以下命令，创建Deployment控制器。

```shell
kubectl apply -f exampledeployforpvc.yml
```

创建后可以通过$ kubectl get deploy命令查看启动状态

![image-20220621095554468](http://img.trivial.top/img/image-20220621095554468.png)

执行 kubectl get pod -o wide 看到Deployment 控制器共创建了两个pod,分别位于两个不同的机器机器上

![image-20220621095859516](http://img.trivial.top/img/image-20220621095859516.png)

在本例中，PVC所绑定的PV引用中NFS服务器的共享目录为/nfs/data。在NFS服务器上执行$ cat /nfs/data/dataforpvc，可输出NFS共享目录下的文件内容。

![image-20220621101355431](http://img.trivial.top/img/image-20220621101355431.png)

#### PV的解绑与回收

在之前的示例中已经将exampledeployforpvc绑定到唯一的PV——exampledeployforpv上，如果此时再创建一个新的PVC，会发生什么情况呢？

```shell
vim examplefornfspvc2.yml
```

```shell
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: examplefornfspvc2
spec:
 accessModes:
 - ReadWriteMany
 storageClassName: "examplenfs"
 resources:
 requests:
 storage: 500Mi
```

PVC创建完成后，可以通过 kubectl get pvc命令查询PVC资源。

![image-20220621102014527](http://img.trivial.top/img/image-20220621102014527.png)

可以看到examplefornfspvc2的STATUS属性为Pending，这表示PVC一直处于挂起状态，没有找到合适的PV资源

​    虽然examplefornfspv定义的空间为1GiB，而后面定义的两个PVC都各自只申请了500MiB的资源，但PV和PVC只能一对一绑定，不能一对多绑定，所以examplefornfspvc2无法申请到合适的PV资源。要使用examplefornfspvc2，要么再创建一个新的PV资源，要么就让之前的PVC和PV资源解除绑定

此时我们可以执行以下命令，先删除之前创建的PVC资源

```shell
 kubectl delete pvc examplefornfspvc
```

执行删除命令后，执行$ kubectl get pvc ，发现examplefornfspvc2状态有 pending 变为 Bound 

![image-20220621144334463](http://img.trivial.top/img/image-20220621144334463.png)

因为之前PV定义的回收策略（persistentVolumeReclaimPolicy）为Recycle，这表示自动回收，所以解绑后会清理PVC在PV上写入的内容。此时如果再执行$ cat /nfs/data/ dataforpvc，可以看到文件已不存在

![image-20220621144751064](http://img.trivial.top/img/image-20220621144751064.png)

如果自动回收失败，则PV的STATUS属性将变为Failed，这表示暂时无法使用。如果之前PV定义的回收策略是Retain，则删除PVC后资源不会自动回收。此时/data/k8snfs/dataforpvc文件依然存在，而PV的STATUS属性将变为Released，因此依然不能重新绑定其他PVC，除非重新创建PV。

#### StorageClass

之前介绍了PV及PVC的使用方式，从中可以发现，这是一种静态创建PV的方法，先要创建各种固定大小的PV，而这些PV都是手动创建的，过程非常麻烦。有时开发人员在申请PVC资源时，不一定有匹配条件的PV可用，这又带来了新的问题。

为了解决这类问题，Kubernetes提供了StorageClass抽象来动态创建PV，StorageClass大大简化了PV的创建过程。当申请PVC资源时，如果匹配到满足条件的StorageClass，就会自动为PVC创建对应大小的PV并进行绑定。

StorageClass是通过存储分配器（provisioner）来动态分配PV的，但是Kubernetes官方内置的存储分配器并不支持NFS，所以需要额外安装NFS存储分配器。

NFS 存储分配器的安装过程并不复杂。首先，执行以下命令，下载 NFS 存储分配器的deployment.yaml配置。

```shell
$ wget  https://raw.githubusercontent.com/Kubernetes-incubator/external-storage/master/nfs-client/deploy/deployment.yaml
```

修改文件部分配置

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nfs-client-provisioner
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
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: fuseim.pri/ifs
              # nfs 服务器ip
            - name: NFS_SERVER
              value: 192.168.10.3
              #nfs 共享目录
            - name: NFS_PATH
              value: /nfs/data
      volumes:
        - name: nfs-client-root
          nfs:
          # nfs 服务器ip
            server: 192.168.10.3
           #nfs 共享目录
            path: /nfs/data
```

接下来，执行以下命令，创建NFS存储分配器的相关资源。

```yaml
 kubectl apply -f deployment.yml
```

![image-20220621165556433](http://img.trivial.top/img/image-20220621165556433.png)发f

发现没启动起来，使用kubectl describe deployment nfs-client-provisioner 查看详细信息

![image-20220621165702293](http://img.trivial.top/img/image-20220621165702293.png)

发现启动卡在启动 ScalingReplicaSet 这一步了，使用kubectl get rs 发现确实有一个rs资源

![image-20220621165826163](http://img.trivial.top/img/image-20220621165826163.png)

使用kubectl describe rs nfs-client-provisioner-6876d65556

![image-20220621170012681](http://img.trivial.top/img/image-20220621170012681.png)

发现确实error了

应该是没创建 serviceaccount 

编写 serviceaccount.yaml

```shell
apiVersion: v1
kind: ServiceAccount
metadata:
 name: nfs-client-provisioner
```

kubectl apply -f serviceAccount.yaml 查看 deploy 启动成功

![image-20220621170952303](http://img.trivial.top/img/image-20220621170952303.png)

如果Kubernetes集群已启用RBAC或正在运行OpenShift，则必须为NFS存储分配器授权。直接执行以下命令即可。

```shell
kubectl apply -f https://raw.githubusercontent.com/Kubernetes-incubator/external-storage/master/nfs-client/deploy/class.yaml
```

安装完成后可以创建StorageClass了。首先，创建managed-nfs-storage.yml文件。

```shell
 vim managed-nfs-storage.yml
```

然后，在文件中填入以下内容并保存文件

```shell
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
 name: managed-nfs-storage
provisioner: fuseim.pri/ifs
parameters:
 archiveOnDelete: "false"
```

该模板的主要含义如下。

> - apiVersion表示使用的API版本，storage.k8s.io/v1表示使用Kubernetes API的稳定版本。
> - kind表示要创建的资源对象，这里使用关键字StorageClass。
> - metadata中的name属性定义了当前资源的名称。
> - provisioner表示存储分配器的名称。这里需要使用之前在Deployment模板中配置的PROVISIONER_NAME，即fuseim.pri/ifs。
> - parameters表示该资源对象的参数。若archiveOnDelete为false，表示与之关联的PVC在删除时，它所绑定的PV不会被存储分配器保留；若为true，则相反。

接下来，执行`kubectl apply -f StorageClass.yml`，创建StorageClass。

![image-20220621173039156](http://img.trivial.top/img/image-20220621173039156.png)

StorageClass 创建完成后就可以创建 PVC 了。首先，创建exampleforstorageclass.yml文件。

```shell
vim exampleforstorageclass.yml
```

```shell
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: exampleforstorageclass
spec:
 accessModes:
  - ReadWriteMany
 storageClassName: "managed-nfs-storage"
 resources:
  requests:
   storage: 500Mi
```

使用kubectl get pvc 查看pvc状态

![image-20220623195518687](http://img.trivial.top/img/image-20220623195518687.png)

发现一直处于pending 状态，StorageClass并没有分配pv给pvc。

感觉是分配器出了问题，看下 nfs-client-provisioner pod的日志

![image-20220623200427325](http://img.trivial.top/img/image-20220623200427325.png)

```shell
provision "default/exampleforstorageclass" class "managed-nfs-storage": unexpected error getting claim reference: selfLink was empty, can't make reference
```

确实出问题了，查了下百度。

selfLink was empty 在k8s集群 v1.20之前都存在，在v1.20之后被删除，需要在`/etc/kubernetes/manifests/kube-apiserver.yaml` 添加参数 增加 `- --feature-gates=RemoveSelfLink=false`

```yaml
spec:
  containers:
  - command:
    - kube-apiserver
    - --feature-gates=RemoveSelfLink=false
```

添加之后使用kubeadm部署的集群会自动加载部署pod

```
kubeadm安装的apiserver是Static Pod，它的配置文件被修改后，立即生效。
Kubelet 会监听该文件的变化，当您修改了 /etc/kubenetes/manifest/kube-apiserver.yaml 文件之后，kubelet 将自动终止原有的 kube-apiserver-{nodename} 的 Pod，并自动创建一个使用了新配置参数的 Pod 作为替代。
如果您有多个 Kubernetes Master 节点，您需要在每一个 Master 节点上都修改该文件，并使各节点上的参数保持一致。
```

这里需注意如果api-server启动失败 需重新在执行一遍

```shell
kubectl apply -f /etc/kubernetes/manifests/kube-apiserver.yaml
```

配置完之后再使用 kubectl get pvc 

![image-20220623200938804](http://img.trivial.top/img/image-20220623200938804.png)

pvc STATUS变为Bound

通过$ kubectl get pv命令进行查看，可以找到自动创建的动态PV资源，其名称是随机生成的。

![image-20220623211743935](http://img.trivial.top/img/image-20220623211743935.png)

此时可通过$ kubectl describe pv命令查看详情。可以看到该动态PV在NFS服务器的共享目录上创建了一个专属于它的子目录，/nfs/data/default-exampleforstorageclass-pvc-fea6a408-4040-4408-b984-295553225137

![image-20220623211817813](http://img.trivial.top/img/image-20220623211817813.png)

此时再创建Deployment控制器，让它使用刚才创建的PVC资源。首先，通过以下命令创建exampledeployforstorageclass.yml文件。

```shell
 kubectl apply -f exampledeployforstorageclass.yml
```

在本例中，PVC所绑定的动态PV在NFS服务器的共享目录上创建了一个专属子文件夹，/nfs/data/default-exampleforstorageclass-pvc-fea6a408-4040-4408-b984-295553225137，查看目录，确实有写入·的文件

![image-20220623212512582](C:/Users/value/AppData/Roaming/Typora/typora-user-images/image-20220623212512582.png)

查看文件，正是pod写入的内容

![image-20220623212304150](http://img.trivial.top/img/image-20220623212304150.png)

接下来，先删除deployment  kubectl delete exampledeployforstorageclass（必须保证），再删除PVC。先执行$ kubectl delete pvc exampleforstorageclass命令，然后再执行$ kubectl get pv命令获取PV，可以发现，当删除PVC时，它所绑定的动态PV也会自动删除
