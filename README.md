# docker-deploy

方便日常开发的docker-compose

> [docker-compose install](https://github.com/docker/compose)
>
> [ohmyzsh docker-compose](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/docker-compose)

## 清单

* [elk](elk/docker-compose.yaml) ELK 日志系统
* [gocron](gocron/docker-compose.yml) 使用Go语言开发的轻量级定时任务集中调度和管理系统
* [graylog](graylog/docker-compose.yml) graylog 日志系统
* [hyperf](hyperf/docker-compose.yml) 基于 swoole 的 PHP 框架
* [jaeger](jaeger/docker-compose.yml) 链路追踪工具
* [jenkins](jenkins/docker-compose.yml) 持续集成工具
* [livego](livego/docker-compose.yml) 直播服务器
* [nocodb](nocodb/docker-compose.yml) 数据库转电子表格
* [nps](nps/docker-compose.yml) 轻量级、高性能、功能强大的内网穿透代理服务器
* [rabbitmq](rabbitmq/docker-compose.yml) MQ
* [traefik](traefik/docker-compose.yml) 负载均衡

## docker 常用命令

```bash
# 列出本地主机上的镜像
docker images

# 查看镜像<nginx>可用版本
docker search nginx

# 启动容器，下面的命令输出一个 “Hello World”，之后终止容器
docker run ubuntu:18.04 /bin/echo 'Hello world'

# 启动一个 bash 终端，允许用户进行交互
docker run -t -i ubuntu:18.04 /bin/bash

# 守护态运行
docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"

# 查看容器列表
docker container ls # 或 docker ps

# 查看log
docker container logs [container ID or NAMES]

# 启动容器
docker container start

# 终止容器
docker container stop

# 重启容器
docker container restart

# 查看终止状态的容器
docker container ls -a

# 进入容器
docker exec -it <container_id> bash

# 删除容器
docker container rm  <container_id>/<image_name>
docker rm

# 清理所有处于终止状态的容器
docker container prune

# 删除所有容器
docker stop `docker ps -q -a` | xargs docker rm

# 删除所有标签为none的镜像
docker images|grep \<none\>|awk '{print $3}'|xargs docker rmi

# 查找容器IP地址
docker inspect 容器名或ID | grep "IPAddress"

# 创建网段, 名称: mynet, 分配两个容器在同一网段中 (这样子才可以互相通信)
docker network create mynet
docker run -d --net mynet --name container1 my_image
docker run -it --net mynet --name container1 another_image
```

清除无效的none镜像，并非所有的none镜像都是无效的`docker images -a`

```bash
docker rmi $(docker images -f "dangling=true" -q)

删除所有镜像的记录: docker ps -a|awk '{print $1}'|xargs docker rm
```

清除 volume

```bash
docker volume rm $(docker volume ls -qf dangling=true)
```

## 删除docker images中为none的镜像

```bash
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm
docker images|grep none|awk '{print $3 }'|xargs docker rmi
```

## 清除 docker.qcow2 文件

```bash
./clean-docker-for-mac.sh mysql:5.7.20 docker:dind nginx:alpine consul redis laradock_mysql laradock_redis laradock_workspace laradock_php-fpm laradock_nginx
```

---
