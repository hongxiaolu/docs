---
title: 1 — 准备离线镜像
weight: 1
---

<a id="step-1"></a>

## Image Sources

{{% tabs %}}
{{% tab "HA安装" %}}

Rancher HA安装需要使用来自3个源的镜像，将3个源合并到一个名为`rancher-images.txt`的文件中。

- **Rancher** - Rancher server所需的镜像。根据你需要安装的Rancher版本，通过[Rancher releases](https://github.com/rancher/rancher/releases)页面下载`rancher-images.txt`文件。
- **RKE** - `rke`安装Kubernetes需要的镜像。运行`rke`命令导出镜像列表到`rancher-images.txt`。

    ```plain
    rke config --system-images >> ./rancher-images.txt
    ```
- **Cert-Manager** - (可选)如果您选择使用Rancher自签名TLS证书进行安装，则需要[`cert-manager`](https://github.com/helm/charts/tree/master/stable/cert-manager)镜像。如果您使用自己的证书，则可以跳过此镜像。

    获取最新版本的`cert-manager` Helm chart并解析模板以获取镜像详细信息。

    ```plain
    helm fetch stable/cert-manager
    helm template ./cert-manager-<version>.tgz | grep -oP '(?<=image: ").*(?=")' >> ./rancher-images.txt
    ```
    对镜像列表进行排序，以检查镜像是否重复。

    ```plain
    sort -u rancher-images.txt -o rancher-images.txt
    ```

{{% /tab %}}
{{% tab "独立容器安装" %}}

单节点安装所需的所有镜像都可以在`rancher-images.txt`中找到。根据需要安装的版本，通过[Rancher releases](https://github.com/rancher/rancher/releases)页面下载`rancher-images.txt`。

{{% /tab %}}
{{% /tabs %}}

## Publish Images

> **注意** 这可能需要最多20GB的磁盘空间。

1. 浏览[Rancher releases page](https://github.com/rancher/rancher/releases)下载以下用于保存和发布镜像的工具。

    |文件名 | 说明 |
    | --- | --- |
    | `rancher-save-images.sh` | 此脚本拉取`rancher-images.txt`列表中所有的镜像，并将所有镜像保存为`rancher-images.tar.gz`文件。 |
    | `rancher-load-images.sh` | 此脚本导入`rancher-images.tar.gz`中的镜像并推送到你的私有镜像仓库。|

1. 在能访问互联网的主机中, 使用`rancher-save-images.sh`脚本获取`rancher-images.txt`镜像列表中的镜像，并打包为`rancher-images.tar.gz`。

    ```plain
    ./rancher-save-images.sh --image-list ./rancher-images.txt
    ```

1. 复制`rancher-load-images.sh`、`rancher-images.txt`、`rancher-images.tar.gz` 文件到可以访问私有镜像仓库的主机上。

1. (可选)如果你使用的是`harbor`镜像仓库，那么在上传镜像之前，需要创建仓库项目名称，否则无法上传。查看`rancher-images.txt`文件，可以看到有以下项目名:

    ```plain
    rancher
    library
    weaveworks
    minio
    munnerz
    jetstack
    ```
    >**注意** 项目名随版本更新可能随之变化,以实际`rancher-images.txt`文件为准。

    - 创建项目

    修改`harbor_url、harbor_username、harbor_password`，并保存以下命令为: `create_project.sh`。

    ```bash
    #!/bin/bash

    harbor_url=<harbor_url>
    harbor_username=<harbor_username>
    harbor_password=<harbor_password>

    auth_token=$(echo "${harbor_username}:${harbor_password}" | base64)
    list_project="rancher library weaveworks minio munnerz jetstack"
    for project in ${list_project};
    do
        curl -X POST "http://${harbor_url}/api/projects"  \
        -H "accept: application/json" \
        -H "authorization: Basic ${auth_token}" \
        -H "Content-Type: application/json"
        -d
        "{
            "project_name": "${project}",
            "metadata": {
                "public": "true"
            }
        }"
    done
    ```
    - 执行脚本: `bash create_project.sh`。

1. 项目创建好之后，执行以下命令进行镜像同步上传

    ```plain
    ./rancher-load-images.sh --image-list ./rancher-images.txt --registry <REGISTRY.YOURDOMAIN.COM:PORT>
    ```

## [下一步: Install Rancher](../install-rancher/)