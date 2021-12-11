---
title: 二、Docker镜像
date: 2020-10-20 12:19:41
tags: Docker
categories: Docker
---

# Docker 镜像原理

### 镜像概念

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件

如何得到镜像

- 从仓库下载
- 朋友拷贝
- 自己制作

### Docker镜像加载原理

#### UnionFS（联合文件系统）

​	UnionS（联合文件系统）：Union文件系统（ UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的加，同时可以将不同目录挂载到同一个虛拟文件系统下（unite several directories into a single virtual filesystem）。 Union文件系统是 Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

#### Docker镜像加载原理

​	docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。
​	bootfs  (boot file system) 主要包含bootloader和kernel，bootloader 主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在 Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就存在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。
​	roorfs （root file system），在bootfs之上。包含的就是典型Linux系统中的 /dev ，/proc，/bin ，/etx 等标准的目录和文件。rootfs就是各种不同的操作系统发行版。比如Ubuntu，Centos等等。



 ![img](./img01.png) 

​	对于一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用Host（宿主机）的kernel，自己只需要提供rootfs就行了，由此可见对于不同的Linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。
​	对于安装虚拟机的压缩包都是很大的，而Docker的镜像却很小 

虚拟机是分钟级, 容器是秒级。

### 分层理解

![1605321237904](./img02.png)

​	 这样最大的好处就是资源共享，例如很多个镜像都从相同的 Base 镜像构建而来，而宿主机只需要在磁盘上保留一份 base 镜像，同时内存中也只需要加载一份 base 镜像，这样就可以为所有的人容器服务了，而且镜像的每一层都可以被共享 。

```shell
[root@baohua ~]# docker inspect redis
[
    {
        "Id": "sha256:62f1d3402b787aebcd74aaca5df9d5fe5e8fe4c0706d148a963c70d74a497e51",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:a0494b60a0bc6de161d26dc2d2f9d2f1c5435e86a9e5d48862a161131affa6bd"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-10-27T18:33:23.01070793Z",
        "Container": "765b5889ba2fc5524c9637bff2e95006deecd8686fbf6d604bce3d131170f4a7",
        "ContainerConfig": {
            "Hostname": "765b5889ba2f",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.9",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.9.tar.gz",
                "REDIS_DOWNLOAD_SHA=dc2bdcf81c620e9f09cfd12e85d3bc631c897b2db7a55218fd8a65eaa37f86dd"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:860b12ba2c6171de94abe2f78d1f166ed49b34b53a752db019cc369315940bfb",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "18.09.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.9",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.9.tar.gz",
                "REDIS_DOWNLOAD_SHA=dc2bdcf81c620e9f09cfd12e85d3bc631c897b2db7a55218fd8a65eaa37f86dd"
            ],
            "Cmd": [
                "redis-server"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:860b12ba2c6171de94abe2f78d1f166ed49b34b53a752db019cc369315940bfb",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 104244824,
        "VirtualSize": 104244824,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/20ba47aef6471e3ada49cc1b7b5871a9796bf5a39a863add5f723d2133a317cb/diff:/var/lib/docker/overlay2/968c3fb515aed51c3cff416f2c1ae9d94b824bec67636c10bc20e6ff41b10f2d/diff:/var/lib/docker/overlay2/80af422927ab90c36b1461647113759d7e0df90e4c98553e0fe40b9c14d0cee6/diff:/var/lib/docker/overlay2/49e19ce4e67f386a7e100e10a585742ba1421a48778ea827fed7d0ab5d934efa/diff:/var/lib/docker/overlay2/5aa271d01b3f256bb09f5e951d5631823801114763b13b11ce13434f0261e6b9/diff",
                "MergedDir": "/var/lib/docker/overlay2/3c956e7f8a71152f5ad5515dfc9e1c18dc22bcf67fee172baecc36fc6e8a800b/merged",
                "UpperDir": "/var/lib/docker/overlay2/3c956e7f8a71152f5ad5515dfc9e1c18dc22bcf67fee172baecc36fc6e8a800b/diff",
                "WorkDir": "/var/lib/docker/overlay2/3c956e7f8a71152f5ad5515dfc9e1c18dc22bcf67fee172baecc36fc6e8a800b/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:d0fe97fa8b8cefdffcef1d62b65aba51a6c87b6679628a2b50fc6a7a579f764c",
                "sha256:832f21763c8e6b070314e619ebb9ba62f815580da6d0eaec8a1b080bd01575f7",
                "sha256:223b15010c47044b6bab9611c7a322e8da7660a8268949e18edde9c6e3ea3700",
                "sha256:6a9976a8f40851f45dc8c68a04b130e90522f46bb7e8403c6e7eb4331674f213",
                "sha256:c875a9fc3ec72b140e325e1a1b3b57d299b91811e8288a07c6b788b0d7cba185",
                "sha256:d9364cb75b1a364fbcb97b2f51332fc012ae0321e18c3fd3811f5e5a9f8a2d0e"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

- 查看Layers分层

#### 理解

​	所有的 Docker镜像都起始于一个基础镜像层，进行修改或增加新的内容时，就会在当前的镜像层之上，创建新的镜像层。
​	举一个简单的例子，假如基于 Ubuntu linux16.04创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python包就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层该镜像当前已经包含3个镜像层，如下图所示（这只是一个用于演示的很简单的例子）

 ![img](./img03.png) 

​	 在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件 

 ![img](./img04.png) 

 上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。
下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版 

 ![img](./img05.png) 

​	这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。
​	Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外屐示为统一的文件系统
​	Linux上可用的存储引擎有AUFS、 Overlay2、 Device Mapper、Btfs以及ZFS 。顾名思义，每种存储引擎都基于 Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。
Docker在 Windows上仅支持 windowsfilter 一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW。
下图展示了与系统显示相同的三层镜像。所有镜像层堆并合并，对外提供统一的视图 

 ![img](./img06.png) 

#### 特点

Docker镜像都是只读的，当容器启动时新的可写层被加载到镜像的顶部，这一层就是我们通常说的容器层，容器之下的都叫镜像层

![1605321599722](./img07.png)



# commit 提交

```shell
docker commit 提交容器成为一个新版本

# 命令和git原理类似
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

- 测试

```shell
# 启动默认的tomcat 
[root@baohua ~]# docker run -it -p 8888:8080 tomcat

# 默认的tomcat没有webpp下的app ，拷贝disk下至webapp下
root@4b3e3d3f765a:/usr/local/tomcat# cp -r webapps.dist/* webapps/

# 将修改后的容器提交
[root@baohua ~]# docker commit -a="baohua" -m="add application" 4b3e3d3f765a tomcat008:1.0

# 提交为一个镜像！以后使用修改后的镜像即可。

```

![1605322715952](./img08.png)