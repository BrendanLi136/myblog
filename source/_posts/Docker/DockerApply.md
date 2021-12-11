---
title: 三、Docker实战
date: 2020-10-23 16:19:41
tags: Docker
categories: Docker
---
# Docker 安装Nginx

```shell
# 1. 搜索镜像
[root@baohua ~]# docker search nginx
NAME          DESCRIPTION                   STARS           OFFICIAL            AUTOMATED
nginx         Official build of Nginx.      13951           [OK]   

# 2. 下载镜像
docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
bb79b6b2107f: Pull complete 
111447d5894d: Pull complete 
a95689b8e6cb: Pull complete 
1a0022e444c2: Pull complete 
32b7488a3833: Pull complete 
Digest: sha256:ed7f815851b5299f616220a63edac69a4cc200e7f536a56e421988da82e44ed8
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

# 3. 运行测试

#  -d 后台运行
# --name 给容器命名
# -p 宿主机端口，容器内部端口
[root@baohua ~]# docker run -d --name nginx01 -p 3344:80 nginx
7da41ee1789c912de270afdf481683fecd6be97ec4d2990595a9c3845f3e58a1
[root@baohua ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
7da41ee1789c        nginx               "/docker-entrypoint.??   4 seconds ago       Up 3 seconds        0.0.0.0:3344->80/tcp   nginx01
[root@baohua ~]# curl localhost:3344

# 进入容器
[root@baohua ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
7da41ee1789c        nginx               "/docker-entrypoint.??   8 minutes ago       Up 8 minutes        0.0.0.0:3344->80/tcp   nginx01
[root@baohua ~]# docker exec -it nginx01 /bin/bash
root@7da41ee1789c:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@7da41ee1789c:/# cd /etc/nginx
```

端口暴露的概念

![1604325264690](./apply01.png)



> 思考：每次改动nginx配置文件，都需要进入容器内部。十分麻烦，要可以在外部提供一个映射路径，达到容器外部修改文件，容器内部自动修改。 -v 数据卷



# Docker 安装tomcat

```shell
# 官方使用
docker run -it --rm tomcat:9.0

# 以前后台启动，停止了容器还在，可以查到。 docker run -it --rm 一般用来测试，用完即删除

# 下载再启动

# 启动运行
docker run -it -p 8888:8080 --name tomcat01 tomcat:9.0

# 测试访问

# 进入容器
[root@baohua ~]# docker exec -it tomcat01 /bin/bash

```

> 问题：1.linux命令少了，2.没有webapps  阿里云镜像的原因，默认是最小的镜像，所有不必要的都剔除
>
> 保证最小可运行的环境！

# Docker 部署 es+kibana

```shell
# es 暴露的端口很多
# es 十分的耗内存
# es 的数据一般放置在安全目录！挂载
# --net somenetwork ？ 网络配置

# 启动 elasticsearch
$ docker run -d --name elasticsearch  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 卡住， docker stats 查看cpu的状态


```

```shell
# 增加内存的限制，修改配置文件 -e 环境配置
$ docker run -d --name elasticsearch  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS='-Xms64m -Xmx512m' elasticsearch:7.6.2


```

> 问题: 如何通过kibana连接过去

# 可视化

- portainer 

```shell
docker run -d -p 9000:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

- Rancher (CI/CD )

**什么是portainer ？**

Docker 图形化界面管理工具！提供一个后台面板供我们操作。

通过 ip:9000访问

- 登录界面，初始化密码即可

![1605320165889](./apply02.png)

- 选择本地的即可

  ![1605320287569](./apply03.png)

- 可视化界面

  ![1605320420644](./apply04.png)

