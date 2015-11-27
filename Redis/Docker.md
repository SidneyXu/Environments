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

其它应用连接 Redis 服务器容器

```bash
docker run -link myredis -ti centos:7 /bin/bash
```

