---
title: nvm管理nodejs版本
date: 2022-07-23 14:43:29
categories: [node]
tags:
- node
- nodejs
- nvm
- npm
- nrm
---

# nvm

## 安装

地址：[nvm-windows](https://github.com/coreybutler/nvm-windows/releases)

选择`nvm-setup.exe`下载安装，安装时设置nvm安装目录（D:\nvm）和nodejs的链接目录（D:\nodejs）。

需要将`D:\nodejs`添加到环境变量

## nvm镜像源

配置淘宝镜像源（cmd运行）

```
nvm node_mirror https://npmmirror.com/mirrors/node/
nvm npm_mirror https://npmmirror.com/mirrors/npm/
```

# nvm管理nodejs

需管理员身份运行cmd

- 查看当前所有nodejs版本

  ```
  nvm ls
  ```

- 安装指定版本nodejs（只指定大版本时默认安装该大版本的最新版）

  ```
  nvm install 16
  nvm install 16.16.0
  ```

- 使用指定版本的nodejs（需指定完整的版本号）

  ```
  nvm use 16.16.0
  ```

- 查看当前node和npm的版本

  ```
  node -v
  npm -v
  ```

- 切换npm全局安装位置（需将`D:/nodejs/npm_global`添加至环境变量）

  ```
  npm config set prefix "D:/nodejs/npm_global"
  npm config set cache "D:/nodeks/npm_cache"
  ```

# nrm

- 全局安装nrm，管理npm镜像

  ```
  npm i -g nrm
  ```

- 使用淘宝镜像

  ```
  nrm ls
  nrm use taobao
  ```

