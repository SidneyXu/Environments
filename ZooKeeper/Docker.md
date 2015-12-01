[TOC]

## 单机配置

Dockerfile

```
FROM        centos:7
MAINTAINER "SidneyXu" <siriuseddy@gmail.com>

RUN     yum -y install java-1.7.0-openjdk wget
RUN     wget http://mirrors.cnnic.cn/apache/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz \
        && tar -xzf zookeeper-3.4.6.tar.gz -C /opt \
        && mv /opt/zookeeper-3.4.6 /opt/zookeeper \
        && cp /opt/zookeeper/conf/zoo_sample.cfg /opt/zookeeper/conf/zoo.cfg \
        && mkdir -p /tmp/zookeeper

ENV JAVA_HOME /usr/lib/jvm/jre-1.7.0-openjdk

EXPOSE 2181 2888 3888

WORKDIR /opt/zookeeper

VOLUME ["/opt/zookeeper/conf", "/tmp/zookeeper"]

ENTRYPOINT ["/opt/zookeeper/bin/zkServer.sh"]
CMD ["start-foreground"]
```

创建镜像

```bash
docker build -t sidneyxu/zk:0.0.1 .
```

运行 Zookeeper 容器

```bash
docker run --name myzk -p 2181:2181 2888:2888 3888:3888 -d sidneyxu/zk:0.0.1
```

运行其它应用并连接 Zookeeper 容器

```bash
docker run --link myzk -t -i --name link-zk centos:7 /bin/bash
```

在其它应用中使用客户端测试连接

```bash
env
/opt/zookeeper/bin/zkCli.sh -server $MYZK_PORT_2181_TCP_ADDR:$MYZK_PORT_2181_TCP_PORT
```

成功后会看到控制台输出 `Welcome to ZooKeeper!` 表示运行正常。



