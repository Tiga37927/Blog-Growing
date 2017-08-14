---
title: 阿里云部署nodejs项目
id: 20170813
tags:
  - nodejs
  - center os
categories:
  - 其他
  - 服务器

date: 2017-08-14 03:36:45
---
### 安装nodejs

* wget https://nodejs.org/dist/v6.9.5/node-v6.9.5-linux-x64.tar.xz

* tar xvf node-v6.9.5-linux-x64.tar.xz

* 切换到 root 用户，全局配置nodejs

  ``` javascript
  vi /etc/profile
  在最下面加入
  # node （注释作用）
  export NODE_HOME=/usr/local/src/node
  export PATH=$PATH:$NODE_HOME/bin  
  export NODE_PATH=$NODE_HOME/lib/node_modules 
  :wq （保存并退出）
  source /etc/profile （使配置文件生效）
  ```

* 配置npm淘宝镜像

  ``` javascript
  npm config set registry https://registry.npm.taobao.org 
  npm info underscore	// 有输出即可
  ```


### 安装配置nginx

