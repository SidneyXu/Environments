[TOC]

## 安装

下载 Redis

``` bash
wget http://download.redis.io/releases/redis-3.0.5.tar.gz
```

解压文件

``` bash
tar -zxvf redis-3.0.5.tar.gz
```

执行安装

``` bash
cd redis-3.0.5
make
```

### 验证安装

修改 `redis.conf`，将 `daemonize` 值改为 `true`。

启动服务器

``` bash
src/redis-server redis.conf
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



