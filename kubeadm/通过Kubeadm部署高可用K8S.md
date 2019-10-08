环境介绍：
---
+ CentOS： 7.6
+ Docker： 18.06.1-ce
+ Kubernetes： 1.13.4
+ Kuberadm： 1.13.4
+ Kuberlet： 1.13.4
+ Kuberctl： 1.13.4

部署介绍：
---
创建高可用首先先有一个 Master 节点，然后再让其他服务器加入组成三个 Master 节点高可用，然后再讲工作节点 Node 加入。下面将描述每个节点要执行的步骤：
+ Master01： 二、三、四、五、六、七、八、九、十一
+ Master02、Master03： 二、三、五、六、四、九
+ node01、node02： 二、五、六、九

集群架构：
---
![Image text](./pic/kubernetes-install-1002.jpg)

# kuberadm 简介
## Kuberadm 作用
Kubeadm 是一个工具，它提供了 kubeadm init 以及 kubeadm join 这两个命令作为快速创建 kubernetes 集群的最佳实践。

kubeadm 通过执行必要的操作来启动和运行一个最小可用的集群。它被故意设计为只关心启动集群，而不是之前的节点准备工作。同样的，诸如安装各种各样值得拥有的插件，例如 Kubernetes Dashboard、监控解决方案以及特定云提供商的插件，这些都不在它负责的范围。

相反，我们期望由一个基于 kubeadm 从更高层设计的更加合适的工具来做这些事情；并且，理想情况下，使用 kubeadm 作为所有部署的基础将会使得创建一个符合期望的集群变得容易。

## Kuberadm 功能
+ kubeadm init： 启动一个 Kubernetes 主节点
+ kubeadm join： 启动一个 Kubernetes 工作节点并且将其加入到集群
+ kubeadm upgrade： 更新一个 Kubernetes 集群到新版本
+ kubeadm config： 如果使用 v1.7.x 或者更低版本的 kubeadm 初始化集群，您需要对集群做一些配置以便使用 kubeadm upgrade 命令
+ kubeadm token： 管理 kubeadm join 使用的令牌
+ kubeadm reset： 还原 kubeadm init 或者 kubeadm join 对主机所做的任何更改
+ kubeadm version： 打印 kubeadm 版本
+ kubeadm alpha： 预览一组可用的新功能以便从社区搜集反馈

## 功能版本
|Area|Maturity Level|
|:-|:-|
|Command line UX|GA|
|Implementation|GA|
|Config file API|beta|
|CoreDNS|GA|
|kubeadm alpha subcommands|alpha|
|High availability|alpha|
|DynamicKubeletConfig|alpha|
|Self-hosting|alpha|

# 二、前期准备
## 1. 虚拟机分配说明
|IP地址|主机名|内存&CPU|角色|
|:-|:-|:-|:-|
|192.168.2.10|—|—|vip|
|192.168.2.11|k8s-master-01|2C & 2G|master|
|192.168.2.12|k8s-master-02|2C & 2G|master|
|192.168.2.13|k8s-master-03|2C & 2G|master|
|192.168.2.22|k8s-node-02|2c & 4G|node|
## 2. 各个节点端口占用
+ Master节点
|规则|方向|端口范围|作用|使用者|
|:-:|:-:|:-:|:-|:-|
|TCP|Inbound|6443*|Kubernetes API|server All|
|TCP|Inbound|2379-2380|etcd server|client API kube-apiserver, etcd|
|TCP|Inbound|10250|Kubelet API|Self, Control plane|
|TCP|Inbound|10251|kube-scheduler|Self|
|TCP|Inbound|10252|kube-controller-manager|Self|

+ Node节点
|规则|方向|端口范围|作用|使用者|
|:-:|:-:|:-:|:-|:-|
|TCP|Inbound|10250|Kubelet API|Self, Control|plane|
|TCP|Inbound|30000-32767|NodePort Services**|All|