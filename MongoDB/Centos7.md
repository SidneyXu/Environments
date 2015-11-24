[TOC]

## 安装

### 通过 yum 安装

新建文件

``` bash
vim /etc/yum.repos.d/mongodb-org-3.0.repo
```

内容为

``` 
[mongodb-org-3.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.0/x86_64/
gpgcheck=0
enabled=1
```

执行安装

``` bash
sudo yum install -y mongodb-org
```

安装完后各默认配置文件路径

- 配置文件：`/etc/mongod.con`

- 数据文件：`/var/lib/mongo`

- 日志文件：`/var/log/mongodb`

### 通过压缩包安装

下载 MongoDB

``` bash
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.0.7.tgz
```

解压文件

``` bash
tar -zxvf mongodb-linux-x86_64-rhel70-3.0.7.tgz
```

### 验证安装

在命令行中执行 `mongo --version` 验证是否安装成功。



## 配置及使用

设置开机启动

``` bash
sudo /usr/sbin/chkconfig mongod on
```

启动 MongoDB 服务端

``` bash
sudo systemctl enable mongod
```

查看启动状态

``` bash
ps aux |grep mongodb
sudo systemctl status mongod
```

打开端口

``` bash
sudo firewall-cmd --zone=public --add-port=27017/tcp --permanent
sudo firewall-cmd --reload
```



## 集群搭建

为了使用方便，采用压缩包的方式进行安装。

### 副本集

**示例文件夹结构**

- **mongodb** 放置下载的压缩包  mongodb-linux-x86_64-rhel70-3.0.7.tgz 的目录
  - **data/db**
  - **data/db2**
  - **data/db3**
  - **data/db4**
  - **data/arbiter**
  - **log**

此次搭建的副本集共有四个数据节点，一个仲裁节点。

配置副本集时应该尽可能使用奇数节点，如果节点数为偶数的话，就必须有一个仲裁节点来参与投票才能保证故障恢复功能的正常运行。

步骤

1. 定位到 `mongodb` 目录
   
2. 建立数据目录和日志目录
   
   ``` bash
   mkdir -p data/db
   mkdir -p data/db2
   mkdir -p data/db3
   mkdir -p data/db4
   mkdir -p data/arbiter
   mkdir log
   ```
   
3. 重命名解压后的  `mongodb-linux-x86_64-rhel70-3.0.7` 目录
   
   ``` bash
   mv mongodb-linux-x86_64-rhel70-3.0.7 mongodb
   ```
   
4. 编辑配置文件
   
   将 yum 方式生成的 mongod.conf 复制一份
   
   ``` bash
   cd mongodb
   mkdir conf
   cp /etc/mongod.conf .
   mv mongod.conf db.conf
   vim db.conf
   ```
   
5. 修改 `db.conf`
   
   ``` 
   # 日志
   systemLog:
    destination: file
    logAppend: true
    path: /home/tiger/dev/mongodb/log/db.log
   
   # 数据文件
   storage:
    dbPath: /home/tiger/dev/mongodb/data/db
    journal:
      enabled: true
   
   # Pid
   processManagement:
    fork: true
    pidFilePath: /home/tiger/dev/mongodb/db.pid
   
   # 对外端口
   net:
    port: 27017
    bindIp: 127.0.0.1
   
   # 副本集
   replication:
     replSetName: node
   ```
   
6. 拷贝 db.conf
   
   ``` bash
   cp db.conf db2.conf
   cp db.conf db3.conf
   cp db.conf db4.conf
   cp db.conf arbiter.conf
   ```
   
   修改四个配置文件的 systemLog 的 path，storage 的 dbPath，processManagement 的 pidFilePath，net 的 port。
   
7. 启动 MongoDB 各服务端
   
   ``` bash
   ./mongodb/bin/mongod -f mongodb/conf/db.conf
   ./mongodb/bin/mongod -f mongodb/conf/db2.conf
   ./mongodb/bin/mongod -f mongodb/conf/db3.conf
   ./mongodb/bin/mongod -f mongodb/conf/db4.conf
   ./mongodb/bin/mongod -f mongodb/conf/arbiter.conf
   ```
   
8. 使用客户端连接到其中一个节点
   
   ``` bash
   ./mongodb/bin/mongo
   ```
   
9. 初始化副本集
   
   ``` bash
   config_test = {
      "_id": "node",
      "members": [
          {
              "_id": 0,
              "host": "localhost:27017",
              "priority": 2
          },
          {
              "_id": 1,
              "host": "localhost:27018",
              "priority": 1
          },
          {
              "_id": 2,
              "host": "localhost:27019",
              "priority": 1
          },
          {
              "_id": 3,
              "host": "localhost:27020",
              "priority": 1
          },
          {
              "_id": 4,
              "host": "localhost:27021",
              "arbiterOnly": true
          }
      ]
   }
   rs.initiate(config_test)
   ```
   
   其中，`arbiterOnly` 表示是否是仲裁节点，`priority` 用于设置权重，更高的权重会被选为主节点。
   
10. 验证副本集状态
    
    成功完成以上操作进行初始化后，重新执行 mongo 后命令行控制符会由 `>` 变为 `node:PRIMARY>`，这表示当前节点为活动节点，或者变为 `node:SECONDARY>` 表示此为非活动节点。
    
    查看副本集状态
    
    ``` bash
    rs.status()
    ```
    
    查看当前节点是否为活动节点
    
    ``` bash
    db.isMaster()
    ```

### 分片

1. 在搭建好副本集后新增一个配置节点 conf
   
2. 修改配置文件
   
   ``` 
   # 分片
   sharding
   clusterRole: configsvr or shardsvr
   ```
   
   数据节点的 `clusterRole` 设为 `shardsvr`，配置节点的 `clusterRole` 设为 `configsvr`。
   
   新增路由进程的配置文件
   
   ``` 
   sharding:
   configDB: localhost:27022
   chunkSize: 1
   ```
   
   `configDB` 指向配置节点的地址
   
3. 启动数据节点
   
   ``` bash
   ./mongodb/bin/mongod -f mongodb/conf/db.conf
   ./mongodb/bin/mongod -f mongodb/conf/db2.conf
   ./mongodb/bin/mongod -f mongodb/conf/db3.conf
   ./mongodb/bin/mongod -f mongodb/conf/db4.conf
   ```
   
4. 启动配置节点
   
   ``` bash
   ./mongodb/bin/mongod -f mongodb/conf/config.conf
   ```
   
5. 启动路由节点
   
   ``` bash
   ./mongodb/bin/mongos -f mongodb/conf/route.conf
   ```
   
6. 客户端连接到路由进程
   
   ``` bash
   ./mongodb/bin/mongo localhost:27020/admin
   ```
   
7. 添加到分片
   
   ``` bash
   sh.addShard("node/localhost:27017")
   ```
   
   以上 `node` 为副本集名，添加主节点后 MongoDB 会自动关联到其对应的副节点和仲裁节点。
   
8. 指定分片的数据库
   
   ``` bash
   db.runCommand( { enablesharding : "test"})
   ```
   
9. 指定片键
   
   ``` bash
   db.runCommand( { shardcollection : "test.cities", key : {name : 1}})
   ```
   
10. 测试分片
    
    导入测试数据，验证所有数据所在的数据库
    
    ``` bash
    mongoimport -h localhost:27020 --db test --collection cities \ --type json mongo_cities1000.json
    ```



## 参考资料

- [install-mongodb-on-red-hat](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/)
- [install-mongodb-on-centos-7](https://devops.profitbricks.com/tutorials/install-mongodb-on-centos-7)

