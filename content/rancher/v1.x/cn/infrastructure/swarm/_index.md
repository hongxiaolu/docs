---
title: Swarm 
weight: 430
---


在Rancher中部署Swarm，您首先需要添加一个新的[环境]({{< baseurl >}}/rancher/v1.x/cn/configuration/environments/)。这个环境需要使用编排引擎为**Swarm**的[环境模版]({{< baseurl >}}/rancher/v1.x/cn/configuration/environments/#什么是环境模版)进行创建。

### 需求
* Docker 1.13 以上
* 端口 `2377` 以及 `2378` 需要能在主机之间相互访问。

### 创建一个 Swarm 环境

在左上角的环境的下拉菜单中，点击**环境管理**。通过点击**添加环境**去创建一个新的环境，需要填写**名称**，**描述**(可选)，并选择Swarm作为编排引擎的环境模版。如果启用了[访问控制]({{< baseurl >}}/rancher/v1.x/cn/configuration/access-control/)，您可以在环境中[编辑成员]({{< baseurl >}}/rancher/v1.x/cn/configuration/environments/#成员编辑)并选择他们的[成员角色]({{< baseurl >}}/rancher/v1.x/cn/configuration/environments/#成员角色)。所有被添加到成员列表的用户都能访问您的环境。

在创建Swarm环境后，您可以在左上角环境的下拉菜单中切换到您的环境，或者在环境管理页面中，在对应环境的下拉选项中点击**切换到此环境**。

> **注意:** Rancher支持多种容器编排引擎框架，但Rancher目前不支持在已有运行服务的环境里切换容器编排引擎。

### 启动 Swarm

在Swarm环境被创建后，在您添加一台主机到这个环境之前，[基础设施服务]({{< baseurl >}}/rancher/v1.x/cn/rancher-services/)是不会启动的。**Swarm**服务需要至少3个主机节点。[添加主机]({{< baseurl >}}/rancher/v1.x/cn/infrastructure/hosts/)的过程与其他编排引擎相同。一旦第一个主机被添加成功，Rancher将会自动启动基础设施服务，包括Swarm的组件(例如swarm与swarm-agent)。您可以在**Swarm**页面看到部署的过程。

> **注意:** 请不要手动对docker daemon使用`docker swarm`命令。这可能与Rancher管理Swarm集群产生冲突。

> **注意:** 只有Rancher的管理员或者环境的所有者才能够看到[基础设施服务]({{< baseurl >}}/rancher/v1.x/cn/rancher-services/)。

#### 在命令行中使用CLI

在Rancher的UI里，您可以方便的连接到能够运行`docker`或者`docker-compose`命令的容器中。通过这个命令行您可以控制Swarm集群。
