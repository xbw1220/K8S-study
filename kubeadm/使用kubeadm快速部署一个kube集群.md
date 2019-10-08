kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。
这个工具能通过两条指令完成一个kubernetes集群的部署：

```shell
# 创建一个 Master 节点 $ kubeadm init
 
# 将一个 Node 节点加入到当前集群中 $ kubeadm join < Master节点的IP和端口 >
```

# 1.安装要求
---
在开始之前，部署Kubernetes集群机器需要满足以下几个条件：
+ 一台或多台机器，操作系统 CentOS7.x-86_x64
+ 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
+ 集群中所有机器之间网络互通 可以访问外网，需要拉取镜像
+ 禁止swap分区
