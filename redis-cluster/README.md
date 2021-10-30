# Redis Cluster

> [Redis cluster tutorial en](https://redis.io/topics/cluster-tutorial)
> [Redis cluster tutorial 中文文档](http://www.redis.cn/topics/cluster-tutorial.html) 相对官方文档滞后，建议查看官方英文文档

使用 docker-compose 本地部署Redis Cluster集群

## 文件说明

* `redis-cluster-config.sh` 脚本批量生成配置文件，端口号 7001~7006
* `redis-cluster.tmpl` 配置文件模板，脚本根据该模板生成每个节点的配置文件(redis.conf)

## 搭建集群

执行脚本，批量生成配置文件，生成的配置文件如下：

```bash
❯ tree
.
├── 7001
│   ├── conf
│   │   └── redis.conf
│   └── data
├── 7002
│   ├── conf
│   │   └── redis.conf
│   └── data
├── 7003
│   ├── conf
│   │   └── redis.conf
│   └── data
├── 7004
│   ├── conf
│   │   └── redis.conf
│   └── data
├── 7005
│   ├── conf
│   │   └── redis.conf
│   └── data
├── 7006
│   ├── conf
│   │   └── redis.conf
│   └── data
├── Makefile
├── README.md
├── docker-compose.yml
├── redis-cluster-config.sh
└── redis-cluster.tmpl
```

Redis 5.x 以上使用 `redis-cli` 命令，官方举例：

```bash
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 \
127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
--cluster-replicas 1
```

Redis 3.x and 4.x 使用`redis-trib` 命令，官方举例：

```bash
./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 \
127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
```

我自己的 docker 环境使用以下命令搭建集群，`192.168.1.11` 为本机 IP

```bash
docker exec -it redis7001 redis-cli -p 7001 -a 123456 --cluster create 192.168.1.11:7001 192.168.1.11:7002 192.168.1.11:7003 192.168.1.11:7004 192.168.1.11:7005 192.168.1.11:7006 --cluster-replicas 1
```

启动集群日志如下：

```bash
❯ docker exec -it redis7001 redis-cli -p 7001 -a 123456 --cluster create 192.168.1.11:7001 192.168.1.11:7002 192.168.1.11:7003 192.168.1.11:7004 192.168.1.11:7005 192.168.1.11:7006 --cluster-replicas 1
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 192.168.1.11:7005 to 192.168.1.11:7001
Adding replica 192.168.1.11:7006 to 192.168.1.11:7002
Adding replica 192.168.1.11:7004 to 192.168.1.11:7003
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: 129f476d497cbfb74713d888cd4a62c35c8688b1 192.168.1.11:7001
   slots:[0-5460] (5461 slots) master
M: 5d4794358f246f018f9a8a2a7fbbe3e575ba360a 192.168.1.11:7002
   slots:[5461-10922] (5462 slots) master
M: 54ae806fa3660ecbb0b96cfafdab76ae31cc1ab6 192.168.1.11:7003
   slots:[10923-16383] (5461 slots) master
S: 6ad01e62f0b05355a68ef880a599a9ebc68c3672 192.168.1.11:7004
   replicates 129f476d497cbfb74713d888cd4a62c35c8688b1
S: af56f202748337589e2c0f2287e352173a9fd3b6 192.168.1.11:7005
   replicates 5d4794358f246f018f9a8a2a7fbbe3e575ba360a
S: a0e6de323f5992c2dddfd1270fe60f8b639804bc 192.168.1.11:7006
   replicates 54ae806fa3660ecbb0b96cfafdab76ae31cc1ab6
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 192.168.1.11:7001)
M: 129f476d497cbfb74713d888cd4a62c35c8688b1 192.168.1.11:7001
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: a0e6de323f5992c2dddfd1270fe60f8b639804bc 192.168.1.11:7006
   slots: (0 slots) slave
   replicates 54ae806fa3660ecbb0b96cfafdab76ae31cc1ab6
S: 6ad01e62f0b05355a68ef880a599a9ebc68c3672 192.168.1.11:7004
   slots: (0 slots) slave
   replicates 129f476d497cbfb74713d888cd4a62c35c8688b1
M: 5d4794358f246f018f9a8a2a7fbbe3e575ba360a 192.168.1.11:7002
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: af56f202748337589e2c0f2287e352173a9fd3b6 192.168.1.11:7005
   slots: (0 slots) slave
   replicates 5d4794358f246f018f9a8a2a7fbbe3e575ba360a
M: 54ae806fa3660ecbb0b96cfafdab76ae31cc1ab6 192.168.1.11:7003
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

## 集群命令

* `cluster nodes` 集群节点信息
* `cluster slots` slots 信息
* `cluster info` 集群信息

---
