---
title: 4 - Azure 集群
weight: 4
---

## 一、Azure部分操作

- Rancher对接AKS需要获取一下资源信息或操作
  - 订阅ID(Subscription ID)
  - 应用ID(Client ID)
  - 应用密钥(Client Secret)
  - RBAC 角色授权

### 1、获取订阅ID(Subscription ID)

1. 主页 -> 订阅 -> 查看订阅ID(Subscription ID)

    ![image](assets/HTB1CmtLe3aH3KVjSZFjq6AFWpXag.jpg)

### 2、获取应用ID(Client ID)

1. 首先需要注册一个应用(App resgistrations)

    主页 -> Azure Active Directory -> 应用注册(App resgistrations) -> 新注册(New registration)

    ![image](assets/HTB1I4NMe3mH3KVjSZKzq6z2OXXa8.jpg)

1. 输入应用名称，其余选项默认即可

    ![image](assets/HTB18TdMe2WG3KVjSZFPq6xaiXXaR.jpg)

1. 得到应用ID(Client ID)

    ![image](assets/HTB1_FNWeWWs3KVjSZFxq6yWUXXaB.jpg)

### 3、获取应用密钥(Client Secret)

1. 进去应用页面 -> 证书与密码页面

    ![11](assets/HTB1wqJOe79E3KVjSZFGq6A19XXa4.jpg)

1. 添加一个新客户端密码

    ![image](assets/HTB1obXNe25G3KVjSZPxq6zI3XXaP.jpg)

1. 得到应用密钥(Client Secret)，该密钥仅会显示一次，请注意保存

    ![11](assets/HTB16ThSe8Gw3KVjSZFwq6zQ2FXaP.jpg)

### 4、对应用进行角色授权

通过以上操作后已经获取到了创建Azure主机的一些凭证ID，但是还没有对应用进行角色授权，在创建的时候可能会出现400、403的错误信息，下面操作对应用进行角色绑定。

1. 添加角色分配

    主页 -> 订阅 -> 访问控制(IAM) -> 添加角色分配

    ![11](assets/HTB1h_lWeW1s3KVjSZFAq6x_ZXXa9.jpg)

1. 添加角色

    搜索应用 -> 根据所需要权限添加角色 -> 选中应用 -> 添加

    ![11](assets/HTB1knJNe.GF3KVjSZFmq6zqPXXa7.jpg)

1. 至此，Azure上面的操作完成，接下来进行Rancher页面操作

## 二、Rancher部分操作

### 1、添加云凭证

1. 点击右上角头像，点击`云凭证`

    ![11](assets/HTB1bGdOe2WG3KVjSZFgq6zTspXai.jpg)

1. 输入刚刚获取的到三个ID信息

    ![11](assets/HTB1Dy8Oe.WF3KVjSZPhq6xclXXar.jpg)

### 2、添加主机模版

1. 注意这里的可用区一定要是账号订阅有权限创建的可用区，不然会出现400、403的返回状态码

    ![11](assets/HTB1T34Pe8WD3KVjSZFsq6AqkpXan.jpg)

1. 至此，对接Azure云主机全部完成，接下来就可以使用该主机模版创建集群.

1. 依次进入`全局\集群`，接着点击`添加集群`

1. 点击`来自云服务商提供的主机`中的`Azure`

    ![image-20190705151301619](assets/image-20190705151301619.png)

1. 设置集群名称、成员

    ![image-20190705151806165](assets/image-20190705151806165.png)

1. 主机池设置

    如果生产环境，为了避免组件之间的影响，可以把几种角度主机独立开来。

    ![image-20190705152624160](assets/image-20190705152624160.png)

1. 其他选择根据需要进行调整

1. 最后点击`创建`
