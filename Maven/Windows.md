[TOC]

## 安装

1. 前往 [Apache Maven Project](http://maven.apache.org/download.cgi) 下载 Zip 包

2. 放到任意路径解压

3. 配置环境变量
```
M2_HOME=G:\env\apache-maven-3.3.3
PATH=%PATH%;%M2_HOME%\bin;
```
4. 输入 `mvn -version` 验证配置是否正确

## 配置

### 修改仓库位置

Maven 默认仓库为 `C:\Users\用户名\.m2\repository`,

修改方式

1. 修改 Maven 安装目录下的 `conf\settings.xml` 文件。
在 `<localRepository>` 注解的地方添加以下代码
```
<localRepository>G:\resp\maven</localRepository>
```
2. 在环境变量中添加 `M2_REPO=G:\resp\maven`。


