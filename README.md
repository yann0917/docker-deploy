# docker-deploy

方便日常开发的docker-compose

> [docker-compose install](https://github.com/docker/compose)
>
> [ohmyzsh docker-compose](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/docker-compose)

## 清单

* [Archery](archery/docker-compose.yml) SQL 审查平台
* [Yearning](/yearning/docker-compose.yml) SQL审核平台
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
* [学习强国](xuexi/docker-compose.yml) 学习强国
* [alist](alist/docker-compose.yml) 一个支持多存储的文件列表程序，使用 Gin 和 Solidjs
* [message-pusher](message-pusher/docker-compose.yml) 搭建专属于你的消息推送服务，支持多种消息推送方式，支持 Markdown，仅单可执行文件，开箱即用
* [Yearning](/yearning/docker-compose.yml) SQL审核平台

## 安装后注意的事项

docker配置文件地址(linux): /etc/docker/daemon.json

### 镜像

这边提供一些中国常用的镜像，[阿里云的镜像地址在这里获取](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

```bash
{
  "registry-mirrors": [
      "https://hub-mirror.c.163.com",
      "https://mirror.baidubce.com",
      "https://docker.mirrors.ustc.edu.cn/",
      "https://reg-mirror.qiniu.com",
      "https://registry.docker-cn.com"
  ]
}
```

### docker的ip网段

docker进程启动会创建个虚拟网卡 docker0

```bash
ip addr

6: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:b7:db:e2:61 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

如果你的服务器的局域网的网段也是 172.17.0.1/16网段, 那就和docker0这个网卡的网段冲突了.
你本来2个局域网的能连通的机器, 启动docker后, 可能就连不通了!
可以通过路由表来看.(mac没有ip命令, 需要brew install iproute2mac)
```bash
ip route
或者
netstat -nr
```

改docker的网段是在配置文件里面: /etc/docker/daemon.json
改完记得重启下

```bash
"bip": "10.50.0.1/16"
```

### docker-compose的网段

docker有自己的docker0虚拟网卡, docker-compose启动的一组也有虚拟网卡.
一样也会发生网段冲突的情况
修改方式有2种
1. docker的配置文件中改, 不要和docker0网段冲突
改完要重启docker
```bash
"default-address-pools" : [
        {
          "base" : "10.11.0.0/16",
          "size" : 24
        }
      ]
```

2. docker-compose.yaml中改

```bash
...省略一些
elasticsearch:
    image: elasticsearch:7.1.0
    container_name: elasticsearch
    restart: always
    environment:
      - TZ=Asia/Shanghai
      - "cluster.name=elasticsearch" #集群名称为elasticsearch
      - "discovery.type=single-node" #单节点启动
      - "ES_JAVA_OPTS=-Xms2048m -Xmx2048m" #jvm内存分配为512MB
      - "ELASTIC_PASSWORD=elastic123"
    ulimits:  #生产环境需要配置的系统配置
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - /opt/es/data:/usr/share/elasticsearch/data
      - /opt/es/plugins:/usr/share/elasticsearch/plugins
      - /opt/es/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
      - /opt/es/logs:/user/share/elasticsearch/logs
    ports:
      - 9200:9200
    logging:
      driver: "json-file"
      options:
        max-size: '500m'
        max-file: '3'
    networks:
      - es7net
      
networks:
  es7net:
    driver: bridge
    ipam:
      driver: default
      config:
      - subnet: 10.88.12.0/24
        gateway: 10.88.12.1
```

你如果需要控制每个服务的ip可以这样写

```bash
version: '3.3'
networks:
    study_net:
        ipam:
            driver: default
            config:
                - subnet: 172.66.1.0/24
services:
    web:
        networks:
            study_net:
                ipv4_address: 172.66.1.100
    db:
        networks:
            study_net:
                ipv4_address: 172.66.1.200
```

### 日志的配置

容器运行中会产生大量日志, 不想容器占用空间越来越大, 就要控制日志大小
分为2个, 单docker容器和docker-compose
1. docker容器
   docker配置文件里面改
```bash
"log-opts": {"max-size":"500m", "max-file":"3"},
```

2. docker-compose
    docker-compose.yaml里面改
```yaml
    ports:
      - 9200:9200
    logging:
      driver: "json-file"
      options:
        max-size: '500m'
        max-file: '3'
    networks:
      - es7net
```

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
