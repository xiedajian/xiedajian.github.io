---
layout: post
title:  laravel-admin安装
categories: ios
description:  laravel-admin安装
keywords: laravel , laravel-admin
---


laravel-admin 是一个可以快速帮你构建后台管理的工具，它提供的页面组件和表单元素等功能，能帮助你使用很少的代码就实现功能完善的后台管理功能。
[中文文档](http://laravel-admin.org/docs/#/zh/)

# [安装](http://laravel-admin.org/docs/v1.4/#/zh/installation?id=%e5%ae%89%e8%a3%85)

首先确保安装好了`laravel`，并且数据库连接设置正确。
```
Laravel 5.1, 已经不维护了
composer require encore/laravel-admin "1.1.*"

Laravel 5.2
composer require encore/laravel-admin "1.2.*"

Laravel 5.3
composer require encore/laravel-admin "1.3.*"

Laravel 5.4
composer require encore/laravel-admin "1.4.*"

Laravel 5.5
composer require encore/laravel-admin "1.5.*"
```
> 当前版本(1.5)需要安装`PHP 7+`和`Laravel 5.5`, 如果你使用更早的版本，请参考文档: [1.4](http://laravel-admin.org/docs/v1.4/#/zh/)

然后运行下面的命令来发布资源：
```
php artisan vendor:publish --provider="Encore\Admin\AdminServiceProvider"
```
在该命令会生成配置文件config/admin.php，可以在里面修改安装的地址、数据库连接、以及表名，建议都是用默认配置不修改。

然后运行下面的命令完成安装：
```
php artisan admin:install
```
启动服务后，在浏览器打开 http://localhost/admin/ ,使用用户名 admin 和密码 admin登陆.

### [配置文件](http://laravel-admin.org/docs/#/zh/installation?id=%e9%85%8d%e7%bd%ae%e6%96%87%e4%bb%b6)

安装完成之后，`laravel-admin`所有的配置都在`config/admin.php`文件中。
### [后台项目文件](http://laravel-admin.org/docs/#/zh/installation?id=%e5%90%8e%e5%8f%b0%e9%a1%b9%e7%9b%ae%e6%96%87%e4%bb%b6)

安装完成之后，后台的安装目录为`app/Admin`，之后大部分的后台开发编码工作都是在这个目录下进行。

```
app/Admin
├── Controllers
│   ├── ExampleController.php
│   └── HomeController.php
├── bootstrap.php
└── routes.php
```
`app/Admin/routes.php`文件用来配置后台路由。

`app/Admin/bootstrap.php` 是`laravel-admin`的启动文件, 使用方法请参考文件里面的注释.

`app/Admin/Controllers`目录用来存放后台控制器文件，该目录下的`HomeController.php`文件是后台首页的显示控制器，`ExampleController.php`为实例文件。

### [静态文件](http://laravel-admin.org/docs/#/zh/installation?id=%e9%9d%99%e6%80%81%e6%96%87%e4%bb%b6)

后台所需的前端静态文件在`/public/vendor/laravel-admin`目录下.

