[TOC]

## 安装

如果是安装在 Docker 中，请先执行本节最后的 Docker 安装环境

下载 Redis

``` bash
wget http://download.redis.io/releases/redis-3.0.5.tar.gz
```

解压文件

``` bash
tar -xzf redis-3.0.5.tar.gz -C /opt
```

执行安装

``` bash
mv /opt/redis-3.0.5 /opt/redis
cd /opt/redis
make
```

### Docker 环境

如果将 Redis 安装在 Docker 中，则需要先安装以下软件

安装 wget

```bash
yum -y install wget
```

安装 gcc

```bash
yum -y install gcc make
```

安装 tcl

```bash
yum -y install tcl
```


### 验证安装

创建 log 目录

```bash
mkdir -p /var/log/redis/
touch /var/log/redis/redis-server.log
```

复制配置文件

```bash
mkdir -p /etc/redis
cp redis.conf /etc/redis/redis.conf
```

修改 `redis.conf`，将 `daemonize` 值改为 `yes`，将 `logfile` 改为 `/var/log/redis/redis-server.log`。

启动服务器

``` bash
src/redis-server /etc/redis/redis.conf
```

启动客户端

``` bash
src/redis-cli
```

测试存取数据功能

``` bash
127.0.0.1:6379> set foo bar
OK
127.0.0.1:6379> get foo
"bar"
```

### 集群配置

步骤

1. 安装 Ruby, zlib

2. 安装 Ruby Redis 客户端

```bash
gem install redis
```

3. 创建存放6个服务器的数据的目录

```bash
mkdir 7001 7002 7003 7004 7005 7006
```

4. 复制配置文件

```bash
cp redis.conf 7001/
```

5. 修改配置文件

```
daemonize yes
port 7001
logfile "/var/log/redis/7001.log"
dir /opt/redis/7001
cluster-enabled yes
cluster-config-file /opt/redis/7001/nodes.conf
cluster-node-timeout 15000
cluster-migration-barrier 1
cluster-require-full-coverage yes
```

6. 继续修改其它 5 个服务器的配置

7. 建立必要的文件夹

```bash
mkdir -p /var/log/redis
```

8. 分别启动 6 个服务器

```bash
/opt/redis/src/redis-server /opt/redis/7001/redis.conf
```

9. 初始化集群

```bash
/opt/redis/src/redis-trib.rb create --replicas 1 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 
```

`--replicas` 后跟的数字表示一个 Master 下有几个 Slaver

检查集群状态

```bash
/opt/redis/src/redis-trib.rb check 127.0.0.1:7001
```

使用客户端连接集群

```bash
/opt/redis/src/redis-cli -c -p 127.0.0.1:7001
```