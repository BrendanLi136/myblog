---
title: 六、DockerNet
date: 2020-11-19 00:19:41
tags: Docker
categories: Docker
---

# Docker 网络

## 理解Docker0

> 测试

![net-20201128142202091](./net-20201128142202091.png)

三个网络

```shell
# 问题：docker 如何处理容器访问的？
```

```shell
[root@baohua ~]# docker run -d -P --name tomcat01 tomcat

#查看容器的内部网路地址 ip addr ， 发现容器启动的时候会得到一个 eth0@if113 的ip地址，docker分配的。
[root@baohua ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
112: eth0@if113: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
       
# linux 可以ping通 docker 内部。
[root@baohua ~]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.068 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.046 ms
```

> 原理

1.  每启动一个docker 容器，docker 就会给docker容器分配一个ip，只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是veth-pair技术！

 `再次测试 ip addr`

![image-20201128144043900](./image-20201128144043900.png)

`再启动一个容器测试`,发现又多了一对网卡

![image-20201128144513823](./image-20201128144513823.png)

```shell
# 发现容器带来的网卡，都是一对对的
# veth-pair 就是一堆的虚拟设备接口，他们都是成对出现的，一端连着协议，一端彼此相连。
# 正因为这个特性，veth-pair 充当一个桥梁，连接着各种虚拟网络设备的
# OpenStac，Docker 容器之间的连接，OVS的连接，都是	veth-pair的技术。
```

2. 测试tomcat01 和 tomcat02 是否可以ping 通

```shell
[root@baohua ~]# docker exec -it tomcat02 ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.083 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.053 ms

# 结论，容器之间是可以互相ping 通的。
```

![image-20201128151118502](./image-20201128151118502.png)

结论：tomcat01 和 tomcat02 是公用的一个路由器，docker0

所有的容器不指定网络的情况下，都是docker0 路由的，docker会给我们的容器分配一个默认的可用IP

> 小结

Docker 使用的是Linux的桥接，宿主机是一个Docker 容器的网桥 docker0

![image-20201128152416923](./image-20201128152416923.png)

Docker 中的所有的网络接口都是虚拟的，虚拟的转发效率高。

只要容器删除，对应网桥一对就没了！

![image-20201128154036638](./image-20201128154036638.png)

## --link

> 思考场景，编写微服务 database url = ip；项目不重启，ip换掉了，是否可以用名字来进行访问容器。

```shell
[root@baohua ~]# docker exec -it tomcat01 ping tomcat02
ping: tomcat02: Name or service not known

# 如何解决这个问题！
# 通过 --link 可以解决了网络连通问题。
[root@baohua ~]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
11c83adc5d6b0e6c1285a047f1ac815fdd4fd52f24a86455cc25a50211d78bf2
[root@baohua ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.080 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.054 ms

#反向可以ping通吗
[root@baohua ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
```

其实tomcat03就是在本地配置了tomcat02的配置！

```shell
# 可以通过inspect来查看tomcat03，发现
  "Links": [
                "/tomcat02:/tomcat03/tomcat02"
            ],
            
# 也可通过hosts配置
[root@baohua ~]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	tomcat02 adc1a9fe5eb1
172.17.0.4	11c83adc5d6b
```

 --link 就是在hosts配置中增加了一个172.17.0.3	tomcat02 adc1a9fe5eb1

> 现在不推荐使用docker0
>
> docker0问题：它不支持容器名连接访问！



## 自定义网络

查看所有的docker网路

```shell
[root@baohua ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
657920809e64        bridge              bridge              local
c5e0580fd4d4        host                host                local
df9959eef401        none                null                local
```

**网络模式**

- bridge：桥接  docker(默认)
- none：不配置网络
- host：和宿主机共享网络
- container：容器网络联通！(局限很大)

**测试**

```shell
# 直接启动的容器默认加 --net bridge ，这个就是docker0
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点： 默认；域名不能访问，--link可以打通

# 自定义网络
# --driver bridge 桥接模式
# --subnet 192.168.0.0/16 子网	# 192.168.0.2 - 192.168.255.255
# --gateway 192.168.0.1 网关
[root@baohua ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
0051a2fa2c6543e1499461926e6477336043698856ebb5a5c05ac141f498ab25
[root@baohua ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
657920809e64        bridge              bridge              local
c5e0580fd4d4        host                host                local
0051a2fa2c65        mynet               bridge              local
df9959eef401        none                null                local
```

自己的网络就创建好了

![image-20201128160509490](./image-20201128160509490.png)

```shell
[root@baohua ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
769200544dc16a570edd1383cf81b7bdff2be6249073f436db29a6b462020b60
[root@baohua ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
ecc9189ce0fb6cf2984a48c6e8a9eb4089a648df4c4a9452ee92dc35cb694a4d

# 可以通过ip地址ping通
[root@baohua ~]# docker exec -it tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.075 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.058 ms
64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.071 ms
^C
--- 192.168.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.058/0.068/0.075/0.007 ms

# 也可通过名字ping 通
[root@baohua ~]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.038 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.053 ms
^C
--- tomcat-net-02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.038/0.045/0.053/0.010 ms
```

自定义的网络docker都已经帮我们维护好了对应的关系，推荐这样使用。

好处：

redis - 不同的集群不同的网络，保证集群是安全和健康的。



## 网络连通

![image-20201128162105535](./image-20201128162105535.png)

![image-20201128162209246](./image-20201128162209246.png)

```shell
# 测试打通 tomcat01 - mynet
[root@baohua ~]# docker network connect mynet tomcat01

# 连通之后就是将tomcat01放到了 mynet网络下

# 一个容器两个ip地址
# 阿里云服务 	公网ip，私网ip
[root@baohua ~]# docker network inspect mynet
```

![image-20201128162650714](./image-20201128162650714.png)

```shell
# tomcat01 可以连通
[root@baohua ~]# docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.065 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.054 ms
^C
--- tomcat-net-01 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.054/0.059/0.065/0.009 ms

# 02没有打通
[root@baohua ~]# docker exec -it tomcat02 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```



## 实战Redis部署集群

1. 创建redis网络

```shell
docker network create redis --subnet 172.38.0.0/16
```

![img](./2020102714234234.png)

2. 创建集群

- 使用 shell 脚本创建 6 个 redis 容器，并配置

```shell

for port in $(seq 1 6); \
do \
mkdir -p /data/redis/node-${port}/conf
touch /data/redis/node-${port}/conf/redis.conf
cat << EOF >/data/redis/node-${port}/conf/redis.conf
port 6379 
bind 0.0.0.0
cluster-enabled yes 
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.18.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
```

 **查看是否创建成功**

![img](./20201027142840723.png)

3. 通过配置文件启动 redis 容器

```shell
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /data/redis/node-1/data:/data \
-v /data/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.18.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
```

**同理，其余五个节点类似**

六个节点启动成功如下图

![img](./2020102714434912.png)



进入 redis-01

```shell
docker exec -it redis-1 /bin/sh
/data # ls
appendonly.aof  nodes.conf
/data # redis-cli --cluster create 172.18.0.11:6379 172.18.0.12:6379 172.18.0.13:6379 172.18.0.14:6379 172.18.0.15:6379 172.18.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.18.0.15:6379 to 172.18.0.11:6379
Adding replica 172.18.0.16:6379 to 172.18.0.12:6379
Adding replica 172.18.0.14:6379 to 172.18.0.13:6379
M: 3d12668a4061034de1ce96008a03b2c12487c0b0 172.18.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8 172.18.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: b4ec70894415549f06c280a45b02c88405c3426c 172.18.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 2b9514f61f37ea9e26d01fa520c1ce17a578aa5b 172.18.0.14:6379
   replicates b4ec70894415549f06c280a45b02c88405c3426c
S: 5ddcae45a54ddc06969b322239882606f2aca042 172.18.0.15:6379
   replicates 3d12668a4061034de1ce96008a03b2c12487c0b0
S: 302a8edb642d38121bc3fbcf44ac83686e1f2405 172.18.0.16:6379
   replicates 3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
....
>>> Performing Cluster Check (using node 172.18.0.11:6379)
M: 3d12668a4061034de1ce96008a03b2c12487c0b0 172.18.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8 172.18.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 302a8edb642d38121bc3fbcf44ac83686e1f2405 172.18.0.16:6379
   slots: (0 slots) slave
   replicates 3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8
S: 5ddcae45a54ddc06969b322239882606f2aca042 172.18.0.15:6379
   slots: (0 slots) slave
   replicates 3d12668a4061034de1ce96008a03b2c12487c0b0
M: b4ec70894415549f06c280a45b02c88405c3426c 172.18.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 2b9514f61f37ea9e26d01fa520c1ce17a578aa5b 172.18.0.14:6379
   slots: (0 slots) slave
   replicates b4ec70894415549f06c280a45b02c88405c3426c
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

如上，集群创建成功

```shell
进入集群查看相关信息
/data # redis-cli -c
127.0.0.1:6379> CLUSTER INFO
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:524
cluster_stats_messages_pong_sent:507
cluster_stats_messages_sent:1031
cluster_stats_messages_ping_received:502
cluster_stats_messages_pong_received:524
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:1031
127.0.0.1:6379> CLUSTER NODES
3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8 172.18.0.12:6379@16379 master - 0 1603776351398 2 connected 5461-10922
302a8edb642d38121bc3fbcf44ac83686e1f2405 172.18.0.16:6379@16379 slave 3ada3b418581f12ae5ae3fba9dfbf6ff6b0572a8 0 1603776351000 6 connected
5ddcae45a54ddc06969b322239882606f2aca042 172.18.0.15:6379@16379 slave 3d12668a4061034de1ce96008a03b2c12487c0b0 0 1603776351599 5 connected
3d12668a4061034de1ce96008a03b2c12487c0b0 172.18.0.11:6379@16379 myself,master - 0 1603776350000 1 connected 0-5460
b4ec70894415549f06c280a45b02c88405c3426c 172.18.0.13:6379@16379 master - 0 1603776350591 3 connected 10923-16383
2b9514f61f37ea9e26d01fa520c1ce17a578aa5b 172.18.0.14:6379@16379 slave b4ec70894415549f06c280a45b02c88405c3426c 0 1603776351000 4 connected
127.0.0.1:6379> 
```

**测试**

- 我们来设个值，再将存值的主机停掉，然后查看是否可以获取到key

```shell
127.0.0.1:6379> SET a b
-> Redirected to slot [15495] located at 172.18.0.13:6379
OK
————————————————————————————————————————————————————————————————————————————
[root@web conf]# docker stop redis-3
redis-3
—————————————————————————————————————————————————————————————————————————————
# 停掉获取key的主机后，再次进入集群，重新获取key
[root@web conf]# docker exec -it redis-1 /bin/sh
/data # get a
/bin/sh: get: not found
/data # redis-cli -c
127.0.0.1:6379> get a
-> Redirected to slot [15495] located at 172.18.0.14:6379
"b"
```

如上发现主机宕机，从机会自动升级为主节点！



## SpringBoot 微服务打包Docker镜像

1、构架spingboot项目

2、打包应用

3、编写Dokcerfile

```shell
FROM java:8

COPY demo-0.0.1-SNAPSHOT.jar /app.jar

CMD ["server.port = 8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

4、构建镜像

5、发布运行！

