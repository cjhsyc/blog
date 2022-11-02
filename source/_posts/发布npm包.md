---
title: 发布npm包
date: 2022-08-06 17:15:13
categories: 前端工程化
tags:
  - npm
  - package.json
---

# 注册账号

- 在[npm官网](https://www.npmjs.com/)注册

- 登录账号（需要npm镜像源为npm官方源）

  ```
  npm config get registry //查看当前镜像源
  nrm use npm //使用npm官方镜像源（如果安装了nrm）
  npm config set registry https://registry.npmjs.org/ //使用npm官方镜像
  npm login //登录（输入账号密码、邮箱和验证码）
  npm adduser //效果同上
  ```

- 查看当前账号

  ```
  npm whoami
  ```

# package.json

`name`和`version`是必须的

```json
{
  "name": "xxx",  //包名（要避免与npm上已有的包重名）
  "version": "0.0.1", //版本号
  //"private": true, //为true时，npm将拒绝发布该程序包
  "type": "module", //使用es模块加载（默认值为commonjs）
  "keywords": [], //关键词
  "repository": { //关联的远程仓库
    "type": "git",
    "url": "https://github.com/xxx/xxx.git" //远程仓库url
  },
  "files": [ //指定要发布的文件夹或文件（默认发布所有文件，package.json始终会被发布）
    "dist"
  ],
  "main": "./dist/xxx.umd.js", //指定通过name被导入时的入口文件
  "module": "./dist/xxx.es.js", //指定通过name被es模块导入时的入口文件
  "types": "./dist/types/index.d.ts", //类型声明文件
  "exports": { //定义导出的内容（main和module的替代品）
    ".": {
      "types": "./dist/types/index.d.ts",
      "import": "./dist/icons.es.js",
      "require": "./dist/icons.umd.js"
    }
  },
  "license": "MIT" //开源协议
}
```

# 发布

- 发布前需切换至官方镜像源

  ```
  npm publish
  // 再次发布前需要将version版本号加一
  ```

- 当发布`name`以`@`开头的包时，`npm`默认发布私有包（私有包需要收费），使用如下命令发布为公开包

  ```
  npm publish --access public
  ```

- 发布beta版本（修改version，如`0.0.1-beta.1`）

  ```
  npm publish --tag=beta
  ```

- 发布成功后登录npm官网即可看到自己发布的包

# 作废和撤销

- 作废npm包，表示不在维护更新

  ```
  npm deprecate <package-name> "<message>"
  ```

- 撤销发布包（只能删除24小时内发布的最后一个版本）

  ```
  npm unpublish <package-name> --force
  ```

  
