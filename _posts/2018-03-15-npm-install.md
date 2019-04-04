---
layout: post
title:  npm
categories: npm
description:  npm
keywords: npm
---

NPM（node package manager），通常称为node包管理器。顾名思义，它的主要功能就是管理node包，包括：安装、卸载、更新、查看、搜索、发布等。

npm的背后，是基于couchdb的一个数据库，详细记录了每个包的信息，包括作者、版本、依赖、授权信息等。它的一个很重要的作用就是：将开发者从繁琐的包管理工作（版本、依赖等）中解放出来，更加专注于功能的开发。


# 知识概括
1. npm的安装、卸载、升级、配置
1. npm的使用：package的安装、卸载、升级、查看、搜索、发布
1. npm包的安装模式：本地 vs 全局
1. package.json：包描述信息
1. package版本：常见版本声明形式


# npm的安装、卸载、升级、配置
##### 安装
随node.js一起安装
##### 查看npm版本
```
$ npm -v
```
##### 卸载
```
npm uninstall npm -g
```
##### 旧版本升级新版本
```
$ npm install npm -g
```

##### 使用淘宝 NPM 镜像
大家都知道国内直接使用 npm 的官方镜像是非常慢的，这里推荐使用淘宝 NPM 镜像。

淘宝 NPM 镜像是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

你可以使用淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm:
```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
这样就可以使用 cnpm 命令来代替 npm 命令：

$ cnpm install [name]
```
##### 查看安装信息
你可以使用以下命令来查看所有全局安装的模块：
```
$ npm list -g
```
##### 生成一个package.json文件
```
$ npm init
```
###### 根据package.json文件来安装所有项目中需要的依赖包
```
$ npm install
```
##### 其他常用命令
```
$ npm help
$ npm root
$ npm config
```


# 使用 npm 命令安装模块
npm 安装 Node.js 模块语法格式如下：
```
$ npm install <Module Name>@<version>
//例如安装 express 模块
$ npm install express
//安装特定的版本
$ npm install express@1.1.1
```

##### 全局安装与本地安装
npm 的包安装分为本地安装（local）、全局安装（global）两种，从敲的命令行来看，差别只是有没有-g而已，比如
```
npm install express          # 本地安装
npm install express -g     # 全局安装
```
本地安装
1. 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录。
2. 可以通过 require() 来引入本地安装的包。
全局安装
1. 将安装包放在 /usr/local 下或者你 node 的安装目录。
2. 可以直接在命令行里使

##### 卸载模块
我们可以使用以下命令来卸载 Node.js 模块。
```
$ npm uninstall express
```
卸载后，你可以到 /node_modules/ 目录下查看包是否还存在，或者使用以下命令查看：
```
$ npm ls
```
##### 更新模块
我们可以使用以下命令更新模块：
```
$ npm update express
```
##### 搜索模块
使用以下来搜索模块：
```
$ npm search express
```
##### 常用参数
```
//--save、-S参数意思是把模块的版本信息保存到dependencies（生产环境依赖）中，即package.json文件的dependencies字段中；
$ --save 或 -S
//--save-dev 、 -D参数意思是吧模块版本信息保存到devDependencies（开发环境依赖）中，即package.json文件的devDependencies字段中；
$ --save-dev 或 -D
//--save-exact 、 -E参数的意思是精确的安装指定版本的模块，package.json文件的dependencies字段里每个模块版本号前面的^不见了
$ --save-exact 或 -E
```
# package.json
package.json 位于模块的目录下，用于定义包的属性。
##### Package.json 属性说明

- name - 包名。

- version - 包的版本号。

- description - 包的描述。

- homepage - 包的官网 url 。

- author - 包的作者姓名。

- contributors - 包的其他贡献者姓名。

- dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。

- repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。

- main - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。

- keywords - 关键字
