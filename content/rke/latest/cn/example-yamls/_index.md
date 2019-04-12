---
title: 9 - 示例配置
weight: 9
---

有许多不同的[配置选项]({{< baseurl >}}/rke/v0.1.x/cn/config-options/)可以在集群配置文件中为RKE设置。以下是一些示例配置:

>**Rancher 2用户注意事项** 如果在创建[Rancher Launched Kubernetes]({{< baseurl >}}/rancher/v2.x/en/cluster-provisioning/rke-clusters/)时使用[配置文件]({{< baseurl >}}/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/#config-file)配置集群选项，则`kube_api`、`kube_controller`服务名称应仅包含`下划线`。这仅适用于`Rancher v2.0.5和v2.0.6`。

## 最小`cluster.yml`示例

```yaml
nodes:
    - address: 1.2.3.4
      user: ubuntu
      role:
        - controlplane
        - etcd
        - worker
```

## 完整 `cluster.yml` 示例

```yaml
nodes:
    - address: 1.1.1.1
      user: ubuntu
      role:
        - controlplane
        - etcd
      ssh_key_path: /home/user/.ssh/id_rsa
      port: 2222
    - address: 2.2.2.2
      user: ubuntu
      role:
        - worker
      ssh_key: |-
        -----BEGIN RSA PRIVATE KEY-----

        -----END RSA PRIVATE KEY-----
    - address: example.com
      user: ubuntu
      role:
        - worker
      hostname_override: node3
      internal_address: 192.168.1.6
      labels:
        app: ingress

# 如果设置为true，则可以使用不受支持的Docker版本
ignore_docker_version: false

# 集群等级的SSH私钥(private key)
## 如果节点未配置SSH私钥，RKE将会以此私钥去连接集群节点
ssh_key_path: ~/.ssh/test

# 使用SSH agent来提供SSH私钥
## 需要配置环境变量`SSH_AUTH_SOCK`指向已添加私钥的SSH agent
ssh_agent_auth: false

# 私有仓库
## 当设置`is_default: true`后，构建集群时会自动在配置的私有仓库中拉取镜像
## 如果使用的是DockerHub镜像仓库，则可以省略`url`或将其设置为`docker.io`
## 如果使用内部公开仓库，则可以不用设置用户名和密码

private_registries:
    - url: registry.com
      user: Username
      password: password
      is_default: true

# 堡垒机
## 如果集群节点需要通过堡垒机跳转，那么需要为RKE配置堡垒机信息
bastion_host:
    address: x.x.x.x
    user: ubuntu
    port: 22
    ssh_key_path: /home/user/.ssh/bastion_rsa
# or
#   ssh_key: |-
#     -----BEGIN RSA PRIVATE KEY-----
#
#     -----END RSA PRIVATE KEY-----

# 设置Kubernetes集群名称
cluster_name: mycluster

# 定义kubernetes版本.
## 目前, 版本定义需要与rancher/types defaults map相匹配: https://github.com/rancher/types/blob/master/apis/management.cattle.io/v3/k8s_defaults.go#L14
## 如果同时定义了kubernetes_version和system_images中的kubernetes镜像，则system_images配置将优先于kubernetes_version
kubernetes_version: v1.10.3-rancher2

## 如果没有指定system_images，那么system_images镜像版本与kubernetes_version对应的镜像相同
# 默认Tags: https://github.com/rancher/types/blob/master/apis/management.cattle.io/v3/k8s_defaults.go)
system_images:
    kubernetes: rancher/hyperkube:v1.10.3-rancher2
    etcd: rancher/coreos-etcd:v3.1.12
    alpine: rancher/rke-tools:v0.1.9
    nginx_proxy: rancher/rke-tools:v0.1.9
    cert_downloader: rancher/rke-tools:v0.1.9
    kubernetes_services_sidecar: rancher/rke-tools:v0.1.9
    kubedns: rancher/k8s-dns-kube-dns-amd64:1.14.8
    dnsmasq: rancher/k8s-dns-dnsmasq-nanny-amd64:1.14.8
    kubedns_sidecar: rancher/k8s-dns-sidecar-amd64:1.14.8
    kubedns_autoscaler: rancher/cluster-proportional-autoscaler-amd64:1.0.0
    pod_infra_container: rancher/pause-amd64:3.1

services:
    etcd:
      # if external etcd is used
      # path: /etcdcluster
      # external_urls:
      #   - https://etcd-example.com:2379
      # ca_cert: |-
      #   -----BEGIN CERTIFICATE-----
      #   xxxxxxxxxx
      #   -----END CERTIFICATE-----
      # cert: |-
      #   -----BEGIN CERTIFICATE-----
      #   xxxxxxxxxx
      #   -----END CERTIFICATE-----
      # key: |-
      #   -----BEGIN PRIVATE KEY-----
      #   xxxxxxxxxx
      #   -----END PRIVATE KEY-----
      # Rancher 2用户注意事项：如果在创建Rancher Launched Kubernetes时使用配置文件配置集群，则`kube_api`服务名称应仅包含下划线。这仅适用于Rancher v2.0.5和v2.0.6。
      extra_args:
        # 修改空间配额为$((4*1024*1024*1024))，默认2G,最大8G
        quota-backend-bytes: '4294967296'
    kube-api:
      # cluster_ip范围
      ## 这必须与kube-controller中的service_cluster_ip_range匹配
      service_cluster_ip_range: 10.43.0.0/16
      # NodePort映射的端口范围
      service_node_port_range: 30000-32767
      # Pod安全策略
      pod_security_policy: false
      # kubernetes API server扩展参数
      ## 这些参数将会替换默认值
      extra_args:
        # 启用审计日志到标准输出
        audit-log-path: "-"
        # 增加删除workers的数量
        delete-collection-workers: 3
        # 将日志输出的级别设置为debug模式
        v: 4
    # Rancher 2用户注意事项：如果在创建Rancher Launched Kubernetes时使用配置文件配置集群，则`kube_controller`服务名称应仅包含下划线。这仅适用于Rancher v2.0.5和v2.0.6。
    kube-controller:
      # Pods_ip范围
      cluster_cidr: 10.42.0.0/16
      # cluster_ip范围
      ## 这必须与kube-api中的service_cluster_ip_range相同
      service_cluster_ip_range: 10.43.0.0/16
      extra_args:
        ## 控制器定时与节点通信以检查通信是否正常，周期默认5s
        node-monitor-period: '5s'
        ## 当节点通信失败后，再等一段时间kubernetes判定节点为notready状态。
      	## 这个时间段必须是kubelet的nodeStatusUpdateFrequency(默认10s)的N倍，
      	## 其中N表示允许kubelet同步节点状态的重试次数，默认40s。
        node-monitor-grace-period: '20s'
        ## 再持续通信失败一段时间后，kubernetes判定节点为unhealthy状态，默认1m0s。
        node-startup-grace-period: '30s'
        ## 再持续失联一段时间，kubernetes开始迁移失联节点的Pod，默认5m0s。
        pod-eviction-timeout: '1m'
    kubelet:
    	# 集群搜索域
      cluster_domain: cluster.local
     	# 内部DNS服务器地址
      cluster_dns_server: 10.43.0.10
      # 禁用swap
      fail_swap_on: false
      # 扩展变量
      extra_args:
        ## 修改节点最大Pod数量
        max-pods: "250"
        ## 密文和配置映射同步时间，默认1分钟
        sync-frequency: '3s'
        ## Kubelet进程可以打开的文件数（默认1000000）,根据节点配置情况调整
        max-open-files: '2000000'
        ## 与apiserver会话时的并发数，默认是10
        kube-api-burst: '30'
        ## 与apiserver会话时的 QPS,默认是5
        kube-api-qps: '15'
        ## kubelet默认一次拉取一个镜像，设置为false可以同时拉取多个镜像，
        ## 前提是存储驱动要为overlay2，对应的Dokcer也需要增加下载并发数
        serialize-image-pulls: 'false'
        ## 拉取镜像的最大并发数，registry-burst不能超过registry-qps ，
        ## 仅当registry-qps大于0(零)时生效，(默认10)。如果registry-qps为0则不限制(默认5)。
        registry-burst: '10'
        registry-qps: '0'
      # 可以选择定义额外的卷绑定到服务
      extra_binds:
        - "/usr/libexec/kubernetes/kubelet-plugins:/usr/libexec/kubernetes/kubelet-plugins"   
# 目前，只支持x509验证
## 你可以选择创建额外的SAN(主机名或IP)以添加到API服务器PKI证书。
## 如果要为control plane servers使用负载均衡器，这很有用。
authentication:
    strategy: x509
    sans:
      - "10.18.160.10"
      - "my-loadbalancer-1234567890.us-west-2.elb.amazonaws.com"
# Kubernetes认证模式
## Use `mode: rbac` 启用 RBAC
## Use `mode: none` 禁用 认证
authorization:
    mode: rbac
# 如果要设置Kubernetes云提供商，需要指定名称和配置
cloud_provider:
    name: aws
# Add-ons是通过kubernetes jobs来部署。 在超时后，RKE将放弃重试获取job状态。以秒为单位。
addon_job_timeout: 30
# 有几个网络插件可以选择：`flannel、canal、calico`，Rancher2默认canal
network:
    plugin: canal
# 目前只支持nginx ingress controller
## 可以设置`provider: none`来禁用ingress controller
ingress:
    provider: nginx
# 所有add-on都必须指定命名空间
addons: |-
    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-nginx
      namespace: default
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80

addons_include:
    - https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/rook-operator.yaml
    - https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/rook-cluster.yaml
    - /path/to/manifest
```