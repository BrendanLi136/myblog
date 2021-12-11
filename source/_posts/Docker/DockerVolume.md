---
title: 四、Docker容器卷
date: 2020-10-24 20:20:41
tags: Docker
categories: Docker
---
# 容器数据卷

## 什么是容器数据卷

数据？如果数据都在容器中，那么容器删除，数据就会丢失！==需求：数据持久化==

MySQL，容器删除了，删库跑路! ==需求：MySQL可以存储在本地！==

容器之间可以有一个数据共享的技术！ Docker容器中产生的数据，同步在本地！
这就是卷技术！目录的挂载，将我们容器内的目录挂载在Linux上！

![vol-20201121123120552](./vol-20201121123120552.png)

**总结：容器的持久化和同步操作！容器间也可以数据共享！**



## 使用数据卷

> 方式一： 直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录:容器内目录

# 测试
[root@baohua home]# docker run -it -v /home/volumTest:/home centos /bin/bash

# 启动后可以通过docker inspect 容器id
```

![vol-20201121122142500](./vol-20201121122142500.png)

> 经测试，文件时进行同步的。

> 停止容器后，修改本地文件，容器内会自动同步！



## 实战：安装Mysql

思考: MySQL的数据持久化的问题！

```shell
# 获取镜像
[root@baohua home]# docker pull mysql:5.7

# 运行容器，需要做数据挂载！ # 安装启动Mysql，需要配置密码的!!!
# 官方
$ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#测试
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名字
[root@baohua ~]# docker run -d -p 200:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root --name mysql01 mysql:5.7

# 启动成功后，使用工具链接测试
# 连接至 200端口 --- 与 容器内的3306映射。

# 在本地测试创建一个数据库，查看一下映射的路径是否ok！
```

假设将容器删除

![vol-20201121142345405](./vol-20201121142345405.png)

发现，挂载在本地的数据卷依旧没有丢失，这就实现了容器数据持久化功能！



## 具名和匿名挂载

```shell
# 匿名挂载
[root@baohua home]# docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的 volume 的情况
[root@baohua home]# docker volume ls
DRIVER              VOLUME NAME
local               189f1f3f577e609d593ad7c56d80f6a3d267d7189570e09701762aa39997e4ad

# 这种就是匿名挂载。 在 -v 只写了容器内的路径，没有写容器外的路径！

# 具名挂载
[root@baohua home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
9b86f2e3ad4ef676930ca90b5fb973dadeb190deb9f0c1aa279d37a23d3e0f47
[root@baohua home]# docker volume ls
DRIVER              VOLUME NAME
local               189f1f3f577e609d593ad7c56d80f6a3d267d7189570e09701762aa39997e4ad
local               84161df8b6b10bc022ab111009964bb03c6c30ec90939cad373046803f6e4e08
local               95301b1df13ea11fcf560237fdaeed3b1d61ed3cf1fe949c68660b9b296eb800
local               juming-nginx

# 通过 -v 卷名：容器内路径  
# 查看一下这个卷
```

![vol-20201121144201102](./vol-20201121144201102.png)

所有的docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/`

通过具名挂载可以方便的找到我们的卷

```shell
# 如果确定具名挂载还是匿名挂载
-v 容器内路径				# 匿名挂载
-v 卷名:容器内路径			   # 具名挂载
-v /宿主机路径:容器内路径		 # 指定路径挂载
```

拓展：

```shell
# 通过 -v 容器内路径:ro   rw 改变读写权限
ro	readonly # 只读
rw	readwrite # 可读可写

# 一旦设置了容器权限。容器对挂载出来的内容就有限定了
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内无法操作!
```

## 初始Dockerfile

Dockerfile 就是用来构建docker 镜像的构建文件！ 命令脚本！

通过这个脚本可以生产镜像，镜像是一层一层的，脚本一个个的命令，每一层都是一个命令。

```shell
# 创建一个Dockerfile文件，名字可以随机，建议Dockerfile
# 文件中内容	指定（大写） 参数
[root@baohua docker-volume-test]# pwd
/home/docker-volume-test
[root@baohua docker-volume-test]# ls
dockerfile1
[root@baohua docker-volume-test]# cat dockerfile1 
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "end---"
CMD /bin/bash
[root@baohua docker-volume-test]# 
```

```shell
# 这里的每个镜像都是每一层
-f file路径
-t Name and optionally a tag in the 'name:tag' 镜像的名字
[root@baohua docker-volume-test]# docker build -f /home/docker-volume-test/dockerfile1 -t baohua/centos:1.0 .
```

![vol](./vol-20201121151714680.png)

启动自己的容器

![vol-20201121152039094](./vol-20201121152039094.png)

![vol-20201121152258989](./vol-20201121152258989.png)

查看卷挂载的路径

![vol-20201121152525142](./vol-20201121152525142.png)

> 经测试，可以同步文件出去

## 数据卷容器

多个容器间同步数据！

```shell
# 启动两个容器
```

![vol-20201121153228773](./vol-20201121153228773.png)

![vol](./vol-20201121153708997.png)

`docker01 的目录数据同步在docker02中 ,经过测试，删除docker01，docker02的数据不会删除`

结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是一旦持久化到了本地，这个时候，本地的数据是不会删除的!