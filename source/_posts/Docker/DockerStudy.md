---

title: 一、Docker学习

date: 2020-10-19 12:19:41

tags: Docker

categories: Docker

---

> Docker 学习

- Docker 概述

- Docker 安装

- Docker 命令

  - 镜像命令
  - 容器命令
  - 操作命令
  - ...

- Docker 镜像

- 容器数据卷

- DockerFile

- Docker网络原理

- IDEA整合Docker

- Docker Compose

- Docker Swarm

- CI\CD Jenkins

  

  

# Docker概述

## Why

环境配置十分麻烦，每台机器都要部署环境(集群Redis、ES、Hadoop)

发布一个项目(jar  + (Redis  Mysql  JDK  ES)) 项目能不能带上环境安装打包

Java -- jar(环境) --- 打包项目带上环境(镜像) --- (Docker仓库：商店) ---下载发布的镜像 --- 直接运行即可。



Docker的思想来源于集装箱！

核心思想：打包装箱。每个箱子都是互相隔离的。



## History

2010  `dotCloud` 公司成立 -- 做pass的云计算服务！LXC有关的容器技术！  将自己的技术（容器化技术）命名Docker！

`开源` 2014.4.9，Docker1.0发布

```shell
vm: linux centos原生镜像(一个电脑)  隔离，需要开多个虚拟机！ 
docker: 隔离,镜像(最核心的环境 mysql+jdk),运行镜像即可
```

>有关Docker

Docker官网	https://www.docker.com/ 

Docker文档    https://docs.docker.com/ 

Docker镜像	 https://hub.docker.com/  



## Can do

> 容器化技术

比较Dokcer和虚拟机技术的不同:

- 传统虚拟机，虚拟出一个硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件。
- 容器上的应用直接运行在宿主机的内核，容器是没有子的内核的，也没有虚拟我们的硬件，所以就轻便了
- 每个容器间是互相隔离，每个容器都有一个属于自己的文件系统，互不影响。

> DevOps （开发、运维）

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**

使用了Dokcer之后，部署应用就和搭积木一样！

项目打包为一个镜像，扩展 服务器A！ 服务器B

**更简单的系统运维**

在容器化之后，开发，测试环境都是高度一致的。

**更高效的计算资源利用**

Docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例！服务器的性能可以被压榨到极致。



# Docker安装

## Docker的基本组成

![](./study01.jpg)

**镜像（image）：**
docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像---> run ----> tomcat01容器（提供服务器）通过这个镜像可以创建多个容器（最终服务运行或者是项目运行就是在容器中的）

**容器（container）：**

docker利用容器技术，独立运行一个或者一个组应用。通过镜像来创建的。

启动，停止，删除，基本命令！

可以理解为一个简易的linux系统。

**仓库（repository）：**

仓库就是存镜像的地方！

仓库分为公有仓库和私有仓库

Docker Hub 、阿里云....

## 安装Docker

> 环境准备

1.linux基础

2.CentOS7

3.使用Xshell连接远程服务器进行操作

> 环境查看

```shell
# 系统内核是 3.10 以上的
[root@baohua ~]# uname -r
3.10.0-1062.1.1.el7.x86_64
```

```shell
# 系统配置
[root@baohua ~]# uname -r
3.10.0-1062.1.1.el7.x86_64
[root@baohua ~]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

帮助文档：

```shell
# 1.卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 2.安装需要的安装包
yum install -y yum-utils

# 3.设置仓库镜像
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo	#默认是国外镜像
 
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo	#阿里云镜像
    
# 更新yum软件包索引
yum makecache fast

# 4.安装docker相关依赖 docker-ce 社区版  ee 企业版
yum install docker-ce docker-ce-cli containerd.io

# 5.启动docker
systemctl start docker

# 6.使用docker version查看是否安装成功
```

![1598795960410](./study02.png)



```shell
# 7.hello-world
docker run hello-world
```

![1598780013729](./study03.png)



```shell
# 8.查看一下下载的hello-world镜像
[root@baohua ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        8 months ago        13.3kB
```

了解：卸载docker

```shell
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker

# /var/lib/docker	docker的默认工作路径！
```

## 阿里云镜像加速

配置使用：

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://woyd8e7o.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

## 回顾HelloWorld

run 的运行流程图

![1598796388009](./study04.png)

## 底层原理

Docker是什么工作的？

Docker是一个Client - Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问!

DockerServer 接收到  DockerClient的指令，就会执行这个命令!

![1598798096204](./study05.png)



**Docker为什么比VM快？**

1.Docker有着比虚拟机更少的抽象层

2.docker是利用宿主机的内核，vm需要的是Guest OS。

![](./study06.jpg)

所以说，新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机加载的是Guest OS，分钟级别的。而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级！

# Docker的常用命令

## 帮助命令

```shell
docker verison		# 显示docker的基本信息
docker info			# 显示docker的系统信息
docker 命令 --help   # 帮助命令
```

帮助文档地址： https://docs.docker.com/engine/reference/commandline/ 

## 镜像命令

**docker images**	查看所有本地的主机上的镜像

```shell
[root@baohua ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        9 months ago        13.3kB
# 解释
REPOSITORY	镜像的仓库源
TAG 		镜像的标签
IMAGE ID	镜像的id
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像的id
```

**docker search**	搜索镜像

```shell
[root@baohua ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation??  10072               [OK]                
mariadb                           MariaDB is a community-developed fork of MyS??  3692                [OK]     

# 可选项，通过搜索来过滤
--filter=stars=3000	 # 搜索出来的结果就是STARS大于3000的
[root@baohua ~]# docker search mysql --filter=stars=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation??  10072               [OK]                
mariadb             MariaDB is a community-developed fork of MyS??  3692                [OK]      
```

**docker pull**	下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@baohua ~]# docker pull mysql
Using default tag: latest	# 如果不写tag，默认是 latest
latest: Pulling from library/mysql
bb79b6b2107f: Pull complete 	# 分层下载，docker imag的核心 联合文件系统
49e22f6fb9f7: Pull complete 
842b1255668c: Pull complete 
9f48d1f43000: Pull complete 
c693f0615bce: Pull complete 
8a621b9dbed2: Pull complete 
0807d32aef13: Pull complete 
9eb4355ba450: Pull complete 
6879faad3b6c: Pull complete 
164ef92f3887: Pull complete 
6e4a6e666228: Pull complete 
d45dea7731ad: Pull complete 
Digest: sha256:86b7c83e24c824163927db1016d5ab153a9a04358951be8b236171286e3289a4	# 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest #真实地址

# 相等价
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本下载
[root@baohua ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
bb79b6b2107f: Already exists 
49e22f6fb9f7: Already exists 
842b1255668c: Already exists 
9f48d1f43000: Already exists 
c693f0615bce: Already exists 
8a621b9dbed2: Already exists 
0807d32aef13: Already exists 
6d2fc69dfa35: Pull complete 
56153548dd2c: Pull complete 
3bb6ba940303: Pull complete 
3e1888da91a7: Pull complete 
Digest: sha256:b3dc8d10307ab7b9ca1a7981b1601a67e176408be618fc4216d137be37dae10b
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

![1603020055478](./study07.png)

**docker rmi**	删除镜像！

```shell
[root@baohua ~]# docker rmi -f 镜像id  # 删除指定的镜像
[root@baohua ~]# docker rmi -f 镜像id 镜像id 镜像id 镜像id  # 删除多个镜像
[root@baohua ~]# docker rmi -f $(docker images -aq)		# 删除全部镜像
```

## 容器命令

**说明：有了镜像才可以创建容器，linux，下载一个cenos镜像来测试学习**

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="Name"	容器名字	tomcat01	tomcat02，用来区分容器
-d				后台方式运行
-it 			使用交互方式运行，进入容器查看内容
-p				指定容器的端口 -p 8080:8080
	-p	ip:主机端口:容器端口
	-p	主机端口:容器端口	(常用)
	-p	容器端口
	容器端口
-P				随机指定端口

# 测试，启动并进入容器
[root@baohua /]# docker run -it centos /bin/bash
[root@7e3fbd81db47 /]# ls	# 查看容器内的centos，基础版本，很多命令不完善！
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中退回主机
[root@7e3fbd81db47 /]# exit
exit
[root@baohua /]# ls
bin   CloudResetPwdAgentNew     CloudrResetPwdAgent  etc   HostGuardAgent_Linux64_V1.12.47.rpm.sha256  lib    lost+found  mnt  proc  run   srv  tmp  var
boot  CloudResetPwdUpdateAgent  dev                  home  hostguard_setup_config.dat                  lib64  media       opt  root  sbin  sys  usr
```

**列出所有运行的容器**

```shell
# docker ps 命令
		# 列出当前正在运行容器
-a		# 列出当前正在运行的容器+带出历史运行过的容器
-n=? 	# 显示最近创建的容器
-q		# 只显示容器的编号
[root@baohua /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@baohua /]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
7e3fbd81db47        centos              "/bin/bash"         3 minutes ago       Exited (0) 53 seconds ago                       amazing_perlman
d47feff9d60d        bf756fb1ae65        "/hello"            7 weeks ago         Exited (0) 7 weeks ago                          busy_matsumoto
```

**退出容器**

```shell
exit	# 直接停止容器并退出
Ctrl + P + Q #容器不停止退出
```

**删除容器**

```shell
docker rm 容器id					# 删除指定的容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)	 # 删除所有的容器
docker ps -a -q|xargs docker rm -f  # 删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id		# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id		# 停止当前正在运行的容器
docker kill 容器id		# 强制停止当前容器
```

## 常用其他命令

**后台启动容器**

```shell
# 命令	docker  run -d 容器名
[root@baohua /]# docker  run -d centos

# 问题docker ps，发现 centos 停止了

# 常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
docker logs -f -t --tail number 容器id 

# 自己编写一段shell脚本
[root@baohua /]# docker run -d centos /bin/sh -c "while true;do echo baohua;sleep 1;done"

[root@baohua /]# docker ps
CONTAINER ID        IMAGE                       
93253c31e8ab        centos          

# 显示日志
-tf # 显示日志
--tail number 显示日志条数
[root@baohua /]# docker logs -ft --tail 10 93253c31e8ab
```

**查看容器中的进程命** ps

```shell
# 命令 docker top 容器id
[root@baohua /]# docker top 93253c31e8ab
UID                 PID                 PPID                C                   STIME     
root                4006                3990                0                   20:28     
root                4492                4006                0                   20:33     
```

**查看镜像的元数据**

```shell
[root@baohua /]# docker inspect 93253c31e8ab
[
    {
        "Id": "93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57",
        "Created": "2020-10-24T12:28:29.06351677Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo baohua;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4006,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-10-24T12:28:29.321874046Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566",
        "ResolvConfPath": "/var/lib/docker/containers/93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57/hostname",
        "HostsPath": "/var/lib/docker/containers/93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57/hosts",
        "LogPath": "/var/lib/docker/containers/93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57/93253c31e8ab4d48150849df5df3c17bc5c9caf0452de0dd6190c1fdfdd01b57-json.log",
        "Name": "/nifty_lehmann",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/9380154604aff625203d598970a5c1eaece4ebeb5bb305c50df45b72530e9392-init/diff:/var/lib/docker/overlay2/6b853dc932a306b2bfdb53bbfc61b9e7895c642a4304070a65803cb2eb73b73a/diff",
                "MergedDir": "/var/lib/docker/overlay2/9380154604aff625203d598970a5c1eaece4ebeb5bb305c50df45b72530e9392/merged",
                "UpperDir": "/var/lib/docker/overlay2/9380154604aff625203d598970a5c1eaece4ebeb5bb305c50df45b72530e9392/diff",
                "WorkDir": "/var/lib/docker/overlay2/9380154604aff625203d598970a5c1eaece4ebeb5bb305c50df45b72530e9392/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "93253c31e8ab",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo baohua;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200809",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "44ee36be6bda419c280cd2959cb6f5f149e6f9c3d3a2de704a99dc0b084046f5",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/44ee36be6bda",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "5f02c1a8a57cc3216becb9a50bbfeb094686efff2ec9c1659072d6531fbac55e",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "657920809e64151f2662e4bbce86ab2072a2d90f3e66c150390a8aa26459625e",
                    "EndpointID": "5f02c1a8a57cc3216becb9a50bbfeb094686efff2ec9c1659072d6531fbac55e",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 通常容器都是使用后台方式运行，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id /bin/bash

# 测试
[root@baohua /]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
93253c31e8ab        centos              "/bin/sh -c 'while t??   16 minutes ago      Up 16 minutes                           nifty_lehmann
[root@baohua /]# docker exec -it 93253c31e8ab /bin/bash
[root@93253c31e8ab /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@93253c31e8ab /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 12:28 ?        00:00:00 /bin/sh -c while true;do echo baohua;sleep 1;done
root      1015     0  0 12:45 pts/0    00:00:00 /bin/bash
root      1036     1  0 12:45 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/s
root      1037  1015  0 12:45 pts/0    00:00:00 ps -ef

# 方式二
docker attach 容器id


[root@baohua ~]# docker attach 93253c31e8ab
正在执行当前的代码...

# docker exec		$ 进入容器后开启一个新的终端，可以在里面操作(常用)
# docker attach		$ 进入容器正在执行的终端，不会启动新的进程！
```

**从容器内拷贝文件到主机上**

```shell
# 查看当前主机目录下
[root@baohua home]# ls
baohua
[root@baohua home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
a552144f7d54        centos              "/bin/bash"         11 seconds ago      Up 11 seconds                           interesting_black

# 进入docker容器内部
[root@baohua home]# docker attach a552144f7d54
[root@a552144f7d54 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@a552144f7d54 /]# cd home
[root@a552144f7d54 home]# ls
# 在容器内部新建一个文件
[root@a552144f7d54 home]# touch test.java
[root@a552144f7d54 home]# ls
test.java
[root@a552144f7d54 home]# exit
exit
[root@baohua home]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
a552144f7d54        centos              "/bin/bash"         55 seconds ago      Exited (0) 3 seconds ago                       interesting_black

# 将文件拷贝到主机上
[root@baohua home]# docker cp a552144f7d54:/home/test.java /home
[root@baohua home]# ls
baohua  test.java

# 拷贝是一个手动过程，未来我们使用 -v 卷的技术，可以实现
```

# 小结

![](./study08.png)

![1603545368300](./study09.png)

![1603545432230](./study10.png)

![img](./study11.png)