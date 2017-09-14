---
layout: post
title: ionic/angular 中使用 Highcharts
categories: ionic
description: ionic/angular 中使用 Highcharts
keywords: Html5, ionic, hybird, Highcharts
---

#### 首先需要下载 Highcharts 包
```
npm install highcharts --save
```
接下来就是编写代码了，首先打开 src/pages/home/home.html 在合适的位置添加一个 div 容器
```
<div id="container"></div>
```
然后打开 home.ts （和上面的 home.html 在同一个目录），引入 highcharts 模块，并在 ionViewDidLoad 函数里编写图表代码，实例代码如下：