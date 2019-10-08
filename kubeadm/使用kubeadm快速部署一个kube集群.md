kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。
这个工具能通过两条指令完成一个kubernetes集群的部署：

```shell
# 创建一个 Master 节点 $ kubeadm init
 
# 将一个 Node 节点加入到当前集群中 $ kubeadm join < Master节点的IP和端口 >
```

# 1.安装要求
在开始之前，部署Kubernetes集群机器需要满足以下几个条件：
+ 一台或多台机器，操作系统 CentOS7.x-86_x64
+ 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
+ 集群中所有机器之间网络互通 可以访问外网，需要拉取镜像
+ 禁止swap分区

# 2.学习目标
1. 在所有节点上安装Docker和kubeadm
2. 部署Kubernetes Master
3. 部署容器网络插件
4. 部署 Kubernetes Node，将节点加入Kubernetes集群
5. 部署Dashboard Web页面，可视化查看Kubernetes资源

# 3. 准备环境
Kubernetse架构图
![Image text](./pic/kubernetes架构图.png)

```shell
关闭防火墙：
$ systemctl stop firewalld
$ systemctl disable firewalld
 
关闭selinux：
$ sed -i 's/enforcing/disabled/' /etc/selinux/config
$ setenforce 0
 
关闭swap：
$ swapoff -a  $ 临时
$ vim /etc/fstab  $ 永久
 
添加主机名与IP对应关系（记得设置主机名）： 
$ cat /etc/hosts
192.168.31.62 k8s-master
192.168.31.64 k8s-node1
192.168.31.66 k8s-node2
 
将桥接的IPv4流量传递到iptables的链： 
$ cat > /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

$ sysctl --system
```

# 4.所有节点安装Docker/kubeadm/kubelet
Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。 
## 4.1安装Docker
1. 如果你之前安装过 docker，请先删掉
```shell
sudo yum remove docker docker-common docker-selinux docker-engine
```
2. 安装一些依赖
```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
3. 根据你的发行版下载repo文件：
CentOS:
```shell
wget -O /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
```
4. 把软件仓库地址替换为TUNA：
```shell
sudo sed -i 's+download.docker.com+mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```
5. 最后安装：
```shell
sudo yum makecache fast
sudo yum install docker-ce
```