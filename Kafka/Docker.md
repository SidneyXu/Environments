[TOC]

## 单机配置

Dockerfile

```
FROM        centos:7
MAINTAINER "SidneyXu" <siriuseddy@gmail.com>

RUN     yum -y install java-1.7.0-openjdk wget
RUN     wget http://apache.fayea.com/kafka/0.8.2.0/kafka_2.11-0.8.2.0.tgz \
        && tar -xzf kafka_2.11-0.8.2.0.tgz -C /opt \
        && mv /opt/kafka_2.11-0.8.2.0 /opt/kfk

ENV JAVA_HOME /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.91-2.6.2.1.el7_1.x86_64/jre
ENV KAFKA_HOME /opt/kfk

EXPOSE 9092

WORKDIR /opt/kfk

VOLUME ["/opt/kfk/config"]

ENTRYPOINT ["/opt/kfk/bin/kafka-server-start.sh"]
CMD ["/opt/kfk/config/server.properties"]
```

创建镜像

```bash
docker build -t sidneyxu/kfk:0.0.1 .
```

运行 Kafka 容器

```bash
docker run --name mykfk -p 9092:9092 -d sidneyxu/kfk:0.0.1
```

