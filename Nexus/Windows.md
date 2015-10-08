[TOC]

## 安装

1. 前往 [Sonatype Nexus](http://www.sonatype.org/nexus/go/) 下载对应压缩包。

2. 直接解压。

3. 运行
	两种方式
	1. 命令行中运行 `bin` 下的 `nexus.bat` 文件直接启动 nexus
	2. 命令行中运行 `bin/jsw` 对应平台下的 `install-nexus.bat` 文件安装为系统服务

4. 在浏览器中输入　`http://localhost:8081/nexus` 检查安装是否完成。默认的用户名为 `admin`，密码为 `admin123`。

## 配置

### conf/nexus.properties

Nexus 的默认配置都保存在安装目录的 `conf` 文件夹下的　`nexus.properties` 文字中。

#### 修改默认上传路径

Nexus 的默认Jar包等工程上传路径为 `../sonatype-work/nexus`。如果想修改的话需要修改稿 `nexus.properties` 中的 `nexus-work` 条目。

####  修改端口与主机

`nexus.properties` 文件中的 `application-port` 和 `application-host` 条目。

