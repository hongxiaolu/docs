---
title: 1 - 自定义集群
weight: 1
---

自定义安装kubernetes集群，适用于拥有内部虚拟机、内部物理主机，或者没有提供kubernetes云服务的云服务器，通过自定义安装方式来快速安装kubernetes集群。

## 一、主机和端口需求

查看[基础环境配置]({{< baseurl >}}/rancher/v2.x/cn/install-prepare/basic-environment-configuration/)和[端口需求]({{< baseurl >}}/rancher/v2.x/cn/install-prepare/references/)获取具体信息。

## 二、添加集群

1. 进入`全局/集群`视图，再点击`添加集群`

    ![image-20180820133743148](_index.assets/image-20180820133743148.png)

    ![image-20180820133126613](_index.assets/image-20180820133126613.png)

1. 选择`自定义`

    ![image-20180820133900819](_index.assets/image-20180820133900819.png)

1. 设置`集群名称`

    ![image-20180820133931984](_index.assets/image-20180820133931984.png)

1. `成员与角色`

    集群成员对应了Rancher中的一个真实的用户，角色则表示此用户具有的集群权限。要想在创建集群时添加成员和对应的成员角色，需要首先在全局下添加用户和集群角色。[用户](../../users)和[角色](../../security/roles)

1. 集群选项

    - `kubernetes版本`

        每个Rancher发行版对应了不同的kubernetes版本，可根据需求进行选择；

    - `网络组件`

        目前Rancher支持三种网络组件：flannel、calico、canal。canal支持基于`项目`的网络隔离，可根据际需求选择是否开启，flannel支持Windows(实验阶段)。

    - `云提供商`

        根据主机所属的云平台，选择对应的云提供商。选择对应的云提供商，可以对接公有云上的一些基础设施，比4层负载均衡、存储。

1. `显示高级选项`

    在集群选项页右下角可以看到高级选项按钮

1. `私有镜像仓库`

    在全局系统设置中有个默认私有仓库，那个私有仓库是全局性的。如果设置后，安装任何集群都将从那个仓库拉取镜像。这个私有镜像仓库是集群层的，只作用于当前创建的集群。

1. `授权集群访问地址`

    K8S通过ssl来通信认证，而生成ssl证书时需要与域名绑定。所以在创建集群的时候为K8S集群预先设置一个访问地址，当Rancher Server无法访问时，可以通过kubectl连接此地址去管理K8S集群。

1. `高级集群选项`

    - `Nginx Ingress`

        Rancher中默认支持Nginx Ingress，v2.0.7开始支持根据自定义是否开启，默认开启。

    - `NodePort端口范围`

        采用NodePort网络模式时，Pod映射的宿主机端口，默认30000-32767

    - `Metrics服务监控`

        服务监控指标，`v2.0.7`开始支持根据自定义是否开启，默认开启。

    - `Pod安全策略`

        根据需求选择启用或者禁止，如要启用，需现在`全局| 安全 |Pod安全策略`中创建策略，默认禁止。

    - `主机Docker版本`

        当前版本，经过严格测试的Docker有三个版本：18.06.x, 18.09.x，如果设置为`需要支的版本`,主机的Docker版本需要为三个版本其中之一，如果版本不一致将无法安装K8S集群。默认设置`允不受支持的版本`,对于生产环境建议选择`需要支持的版本`。

    - `Docker根目录`

        如果docker root目录非默认，需在此指定；

    - `Etcd快照存储`

        可以选择Etcd快照存储路径，默认只存本地，也可以存储到s3存储；

    - `Etcd快照轮换`

        开启Etcd快照轮换后，将控制Etcd快照副本数据与快照创建周期；

1. 最后点击`下一步`。

1. 自定义主机运行命令

    - `主机角色`

        在K8S的架构中，必须至少有一个etcd、Control、Worker，三种角色可以运行在同一台主机上。要保证集群的高可用，那么需要保证有多个etcd、Control实例并且运行在不同主机上。因为etcd数据同步机制，etcd节点数需要为奇数个，比如1、3、5，具体查看[etcd集群容错表]({{< baseurl >}}/rancher/v2.x/cn/install-prepare/basic-environment-configuration/#10-etcd集群容错表)。所以要保证ETCD高可用运行，那至少需要有三个节点来运行etcd服务。

    - `高级选项`

        在高级选项中，可以指定节点的IP地址。对于内网环境的单IP主机，可以忽略此设置；如果是多IP主机，以通过此设置来指定主机的访问IP。

    - `主机标签`

        标签可用于主机的识别和应用的调度，可以在添加节点的时候为其指定。

        假设目前只有一台主机，可按以下方式选择：

        ![image-20180820172356088](_index.assets/image-20180820172356088.png)

    - `最后点击点击右侧的复制按钮`

        ![image-20180820172514578](_index.assets/image-20180820172514578.png)

1. ssh登录到准备添加到K8S集群的节点，粘贴并运行上一步复制的命令。

1. 最后点击`完成`