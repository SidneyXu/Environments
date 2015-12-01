[TOC]

## 单机配置

基础镜像：[官方 redis](https://hub.docker.com/_/redis/)

拉取镜像

```bash
docker pull redis:3.0.5
```

运行 Redis 服务器容器

```bash
docker run --name myredis -p 6379:6379 -d redis:3.0.5
```

运行其它应用并连接 Redis 服务器容器

```bash
docker run --link myredis -t -i centos:7 /bin/bash
```

在其它应用中使用客户端测试连接

```bash
env
redis-cli -h $MYREDIS_PORT_6379_TCP_ADDR -p $MYREDIS_PORT_6379_TCP_PORT
```

## 集群配置



