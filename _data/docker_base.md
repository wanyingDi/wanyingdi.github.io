---
title: Docker
subtitle: Docker 基础
layout: page
show_sidebar: false
menubar: menus
---

### 1. Docker基本概念
***
#### 1.1 Docker 镜像
- Docker 镜像（Image），就相当于是一个 root 文件系统。  
- 镜像是分层存储的架构，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。

#### 1.2 Docker 容器
- 镜像和容器 (Container) 之间的关系就像类和实例。  
- 容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里。  
- **分层存储**：每一个容器运行时，以镜像为基础层，在其上创建一个当前容器的存储层。容器消亡时，容器存储层也随之消亡。**所有文件写入操作，应该使用 Volume 存储卷。**

#### 1.3 Docker Registry
- 解释：在其他服务器只用镜像，一个集中存储，分发镜像的服务。  
- 我们可以通过<仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。  
- 官方的 docker hub, 可以直接pull使用。
- 私有的 Docker Registry：之后可以自己搭建尝试。

### 2. 安装docker（ubuntu）
***
1. 卸载旧版本的docker  
```
sudo apt-get remove docker docker-engine docker.io
```  
2. ubuntu 16.04 + 安装
    1. Docker CE 默认使用 overlay2 存储层驱动,无需手动配置。
    2. 使用国内源并使用apt安装
    ```
    # 1.添加使用https传输的软件包
    sudo apt-get update
    sudo apt-get install apt-transport-https ca-certificates \
        curl software-properties-common
    # 2.添加国内源
    curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
    # 3.向 /etc/apt/source.list 中添加 docker 软件源
    sudo add-apt-repository \
    "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    # 4.安装 docker CE
    sudo apt-get install docker-ce
    ```
    3. 使用脚本安装  
    ```
    curl -fsSL get.docker.com -o get-docker.sh
    sudo sh get-docker.sh --mirror Aliyun
    ```
3. 启动 Docker CE  
```
sudo systemctl enable docker
sudo systemctl start docker
```
4. 建立 docker 用户组  
    1. 默认情况下，docker 命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。
    2. 使用 docker 的用户加入 docker 用户组。
    ```
    # 建立 docker 组
    sudo groupadd docker
    # 将当前用户加入 docker 组
    sudo usermod -aG docker $USER
    ```
5. 安装国内镜像加速( ubuntu 16.04)
    1. 对于使用 systemd 的系统，在 /etc/docker/daemon.json 中写入如下内容：
    ```
    {
        "registry-mirrors": [
            "https://registry.docker-cn.com"
        ]
    }
    ```
    2. 重启服务
    ```
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```
    3. 检查加速器是否生效  
    命令行执行 docker info, 如果出现 Redistry Mirrors:  https://registry.docker-cn.com/ 就说明成功

