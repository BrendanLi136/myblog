---
title: 七、DockerCompose
date: 2020-11-21 00:21:41
tags: Docker
categories: Docker
---

# Docker Compose

## 简介

docker compose来轻松高效的管理容器，定义运行多个容器。

> 官方介绍

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To learn more about all the features of Compose, see [the list of features](https://docs.docker.com/compose/#features).

Compose works in all environments: production, staging, development, testing, as well as CI workflows. You can learn more about each case in [Common Use Cases](https://docs.docker.com/compose/#common-use-cases).

Using Compose is basically a three-step process:

1. Define your app’s environment with a `Dockerfile` so it can be reproduced anywhere.
2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
3. Run `docker-compose up` and Compose starts and runs your entire app.

## 安装

```shell
# 官方
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 加速
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.5/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose


# 改变权限
sudo chmod +x /usr/local/bin/docker-compose

# 测试
docker-compose --version
```

## 体验

1. 创建项目目录 composteset	在composteset下创建文件

```shell
$ mkdir composetest
$ cd composetest
```

2. app.py		python应用，计数器，redis，flask

```shell
# 创建app.py
[root@baohua composetest]# vim app.py
[root@baohua composetest]# cat app.py
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

3. requirements.txt		依赖

```shell
# 创建 requirements.txt 文件
[root@baohua composetest]# vim requirements.txt 
[root@baohua composetest]# cat requirements.txt 
flask
redis
```

4. 创建Dockerfile

```shell
[root@baohua composetest]# vim Dockerfile
[root@baohua composetest]# cat Dockerfile 
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

5. docker-compose.yml

```yaml
[root@baohua composetest]# vim docker-compose.yml
[root@baohua composetest]# cat docker-compose.yml 
version: "3.8"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```



6. 测试启动
    docker-compose up

```shell
# 测试访问 
curl localhost:5000
```



## yaml 规则

docker-compose.yaml 核心

```yaml
# 三层
version: "" # 版本
services: # 服务
  服务1: web
	# 服务配置
	images
	build
	network
	...
  服务2: redis
    ...
  服务3: ...
 # 其他配置	网络/卷、全局规则
 volumes:
```



## 使用Dokcer Compose 搭建博客

[WordPress Document](https://docs.docker.com/compose/wordpress/)

```shell
[root@baohua build]# ls
composetest  idea  tomcat

# 创建 工作目录
[root@baohua build]# mkdir my_wordpress
[root@baohua build]# cd my_wordpress/
```

``` yaml
# 创建 docker-compose.yml 文件
[root@baohua my_wordpress]# vim docker-compose.yml 
[root@baohua my_wordpress]# cat docker-compose.yml 
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

```shell
# 运行 
[root@baohua my_wordpress]# docker-compose up -d
Pulling db (mysql:5.7)...
5.7: Pulling from library/mysql
852e50cd189d: Pull complete
29969ddb0ffb: Pull complete
a43f41a44c48: Pull complete
5cdd802543a3: Pull complete
b79b040de953: Pull complete
```

- 登录 ip : 8080,选择语言

![image-20201206140852868](./com-20201206140852868.png)

- 设置标题。建站

![image-20201206141839585](./com-20201206141839585.png)



- 登录即可

![image-20201206141916314](./com-20201206141916314.png)

![image-20201206142008311](./com-20201206142008311.png)



## 实战

- 编写java服务

```java
package com.example.composetest.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by Brendan Li on 2020/12/6 13:56
 * @author BaoHua
 */
@RestController
public class HelloController {
    @Autowired
    StringRedisTemplate stringRedisTemplate;

    @GetMapping("/hello")
    public String hello(){
        Long views = stringRedisTemplate.opsForValue().increment("views");
        return "welcome views: "+views;
    }
}
```

- 编写Dockerfile 文件

```dockerfile
FROM java:8

COPY *.jar /app.jar

CMD ["--server,port=8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"] 
```

- 编写docker-compose.yaml 文件

```yaml
version: '3.8'
services:
  baohuaapp:
    build: .
    image: baohuaapp
    depends_on:
      - redis
  redis:
    image: "redis:alpine"
```

可以使用 docker-compose up --build 使项目重新编译



# Docker Swarm

![image-20201206151220818](./com-20201206151220818.png)



# Docker Secret

# Docker config

# k8s