[TOC]

## 安装

### 安装 Ruby

1. 前往 [Ruby Installer](http://rubyinstaller.org/downloads/) 下载 Ruby 的安装包。

2. 直接运行安装包，安装时不要忘了勾选 "Add Ruby executables to your PATH"，否则就需要手动配置环境变量。

3. 输入 `ruby -v` 验证配置是否正确。

### 安装 Dev Kit

1. 继续在 [Ruby Installer](http://rubyinstaller.org/downloads/) 网页下载 DEVELOPMENT KIT 压缩包。

2. 解压后打开命令行定位到解压目录

3. 运行 `ruby dk.rb init` 后目录下会自动创建 `config.yml` 文件

4. 打开 `config.yml` 检查其是否包含之前安装的Ruby路径，如果没有的话需要手动追加。

5. 执行 `ruby dk.rb install` 完成安装。

### 安装 Bundler

1. 替换 Gem 为淘宝源

2. 执行 `gem install bundler` 安装 bundler.

## 配置

### 替换 Gem 为淘宝源

国内由于上面大人物的原因，Gem 被墙，只有替换为淘宝源后才能正常使用。

步骤

命令行下执行以下代码

```bash
gem sources --remove https://rubygems.org/
gem sources -a http://ruby.taobao.org/
```

然后执行 `gem sources -l` 检查是否只有淘宝源一个，如果还有其它源的话继续执行以上命令删除其它源。

### 替换 Bundle 为淘宝源

两种方法

第一种，修改 Gemfile 文件，将 `source 'http://rubygems.org'` 替换为 `source 'http://ruby.taobao.org'`。

第二种，安装前执行 `bundle config mirror.https://rubygems.org https://ruby.taobao.org`。





