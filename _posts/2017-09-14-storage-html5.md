---
layout: post
title: 前端数据本地存储介绍
categories: js
description: Html5及混编App本地数据存储
keywords: Html5, ionic, hybird, storage
---
###### 前言
最近想为女朋友写一个记账的app，因为要进行数据分类统计，得出每个月，每年的开销，以及各种类型支付所占的比例，且需要保证App能够离线使用又可以换机使用，就涉及到了本地数据的存储和数据与服务器的数据同步。       

本文介绍本地数据存储的选型。简单总结一些查询到的关于本地数据存储的技术。

## 控制台展示前端存储
###### Chrome：




###### 前端离线存储目前支持 Cookie、localStorage、sessionStorage、IndexedDB、Web SQL Database以及FileSystem.


## 本地存储概览
###### 好处（一切为了提升用户体验 :D）：


- 方便网页的加载，避免取回数据前页面一片空白，如果不需要最新数据也可以减少向服务端请求的次数，从而减少用户等待从服务器获取数据的时间；
- 网络状态不佳时仍可以显示离线数据。

###### 1）本地数据存储：一般存储的都是数据

- Cookie：浏览器均支持，容量为4KB，用起来超级麻烦，而且限制极大；不适合
- IE用户数据（UserData）：仅IE支持
- Web存储机制（Web Storage）：H5，容量为5M。可以做key value 的存储，对于需要有些关系处理的数据，需要自己做逻辑处理，比较麻烦
   - localStorage
   - sessionStorage ：其实算不上离线存储，因为网页关闭了，存储就会丢弃。 
   - globalStorage（Firefox独有，Firefox13起就不再支持）
- 数据库存储：
    - IndexDB：IndexedDB其实就是前段的nosql数据库。
    - Web SQL：关系数据库，需要注意的是，HTML5会放弃Web SQL

######  2）离线存储（应用缓存）：一般存储的是网页
- Cache Storage：定义在 Service Worker 的规范中，配合 Service Worker 进行离线缓存
- Application Cache：在定稿的 HTML 5.1 中被拿到了



## 对主要前端存储方式的详解和比较
#### 1. 小容量的 cookie
##### 1.1 说明
HTTP Cookie，最初用于在客户端存储会话信息。该标准要求服务器对任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分，其中包含会话信息。浏览器会存储这样的会话信息，并在这之后，通过为每个请求添加 Cookie HTTP 头将信息发送回服务器。
###### 【举例如下】：

![image](/images/posts/js/storage-cookie-header.png)

#####  1.2 使用


在 JavaScript 中可以通过 document.cookie 设置字段和进行访问。
```
// 设置 cookie 字段
document.cookie = 'name=Lucy';

// 更好的设置方式：
document.cookie = encodeURIComponent('age') + '=' + encodeURIComponent(25);

// 访问 cookie
document.cookie

// 删除 cookie（设置存储有效时长为过去时间）
var date = new Date();
date.setDate(date.getDate() + '设置时长');
document.cookie = 'name=Lucy;expires=' + date.toUTCString();
```


![image](/images/posts/js/storage-cookie-js-use.png)

#####  1.3 访问限制及存储时长

cookie 可以设置访问域，在设置 cookie 的时候，设定了 cookie 的访问域名为一个顶级域名，则可以达到几个子域名共享 cookie 的效果，如腾讯网 www.qq.com 与微信网页版 wx.qq.com 共享了 pac_uid，以及不同页面的购物车共享。
如果设定了 cookie 的超时时间，则 cookie 将在到期的时候失效。
如果没有设定超时时间，则是 session 级别的（注意上图 Expires/Max-Age 一栏），在退出浏览器时，该 cookie 将消失。
cookie 的 session 不同于 sessionStorage 的 session，cookie 的 session 是指在未关闭浏览器的情况下，所有的 tab 级别的页面或新开，或刷新，均属于一个 session。

#####  1.4 cookie 的优缺点

优点：

可控制过期时间，使其不会长期有效
可扩展、可用性比较好，可跨域共享
可加密减少cookie被破解的可能性
兼容性好
缺点：

数量和长度有限制
在请求头上带着数据安全性差
#####  1.5 应用场景

一般非到不得已，不要在 cookie 里面存东西，更不要存储重要和敏感的数据。如果要存储的话，建议存储一些同步访问页面的时候必须要被带到服务端的信息。

客户端登录，用于保存用户信息。如“下次自动登录”的选项，勾选之后下次就不需要重复验证。通过 cookie 可以保存用户的 id。
创建购物车。可以实现不同页面之间的数据同步（同一个域名下是可以共享cookie的），同时在提交订单的时候又会把这些cookie传到后台。
跟踪用户行为。例如百度联盟会通过cookie记录用户的偏好信息，从而推荐个性化推广信息，如页面上的小广告。这是可以禁用的，也是cookie的缺点之一。


#### 2. Web Storage：仅存储于客户端

定义了两种用于存储数据的对象：sessionStorage 和 localStorage，后两者是Storage的实例。

注意：Storage 类型只能存储字符串。非字符串的数据在存储之前会被转换成字符串。

![image](/images/posts/js/webstorage.png)

#####  2.1 目的及两个主要目标

- 克服由 cookie 带来的一些限制，当数据需要被严格控制在客户端上时，无须持续地将数据发回服务器。
- （目标）提供一种在 cookie 之外存储会话数据的途径；
- （目标）提供一种存储大量可以跨会话存在的数据的机制。

#####  2.2 访问方式

这两个对象在浏览器中都是以 windows 对象属性的形式存在，在 JavaScript 中可以直接通过 sessionStorage 和 localStorage 访问。

##### 2.3 Storage 的实例方法

- clear：删除所有值，Firefox中没有实现
- getItem(name)：根据指定的名字 name 获取对应的值
- key(index)：获得 index 处的值的名字
- removeItem(name)：删除由 name 指定的名值对儿
- setItem(name, value)：为指定的 name 设置一个对应的值

##### 2.4 sessionStorage 和 localStorage 介绍

短暂的 sessionStorage

###### 1) 使用：


![image](/images/posts/js/storage-sessionStorage.png)

######  2) 访问限制：

同源策略：不同于cookie，sessionStorage访问限制更高，只有当前设定sessionStorage的域下才能访问；
单标签页：两个tab（相同域）之间不能互通；
在新开的tab下或者关闭本tab之后再打开，也不能访问之前写下的sessionStorage；
刷新本tab可以访问。
###### 3) 特点及应用场景：

主要用于仅针对会话的小段数据的存储。
建议存储一些当前页面刷新需要存储，且不需要在tab关闭时候留下的信息。
可以用来检测用户是否是刷新进入的页面，如音乐播放器恢复播放进度条的功能。
非常适合单页应用程序，可以方便在各业务模块进行传值。
持久的 localStorage（隐身窗口例外）

1) 使用： 同 sessionStorage

2) 访问限制：

同源策略：同 sessionStorage，要访问同一个 localStorage，页面必须来自同一个域名（子域名无效），使用同一种协议，在同一个端口上；
localStorage 设定后，刷新或新开 tab 是可以访问到的，关闭浏览器重新打开原先tab也可访问。
3) 特点及应用场景：

持久保存客户端数据，数据保留到通过JavaScript删除或者用户清除浏览器缓存。
如果有一些数据，服务器难以承载其压力，但又要与用户的信息绑定，可以使用 localStorage 存储一些状态，这样既能缓解服务器压力，也可以存储用户的数据。
数据比较大的临时保存方案。如在线编辑文章时的自动保存。
多页面访问共同数据。sessionStorage只适用于同一个标签页，localStorage相比而言可以在多个标签页中共享数据。

###### localStorage 与 sessionStorage 的区别总结

H5的两种存储技术的最大区别就是生命周期
localStorage是本地存储，存储期限不限；
sessionStorage是会话存储，页面关闭数据就会丢失。
sessionStorage 有单标签页限制，localStorage 则没有。

##### 2.5 Storage 存储类型说明

Storage 类型只能存储字符串。如果存储的是对象，可以将对象序列化为字符串再存入。（以下以 localStorage 进行说明，sessionStorage 同样适用）

##### 2.6 Storage 大小限制及检测

Web Storage 的限制因浏览器而异。一般来说，对存储空间大小的限制都是以每个来源（协议、域和端口）为单位的，即每个来源都有固定大小的空间用于保存自己的数据。对于 localStorage 而言，大多数桌面浏览器会设置每个来源 5MB 的限制。对 sessionStorage 的限制也是因浏览器而异。

有关 Web Storage 的限制，参考：Web Storage Support Test

对于 Storage 的大小检测，可以将 localStorage 和 sessionStorage 序列化，然后查看其字节数：



##### 2.7 storage 事件

对 Storage 对象进行任何操作，都会在文档上触发 storage 事件，该事件的 event 对象有以下属性：

domain：发生变化的存储空间的域名
key：设置或者删除的键名
newValue：如果是设置值，则是新值；如果是删除键，则是null
oldValue：键被更改之前的值
使用时需要检测 WebKit 是否支持 storage 事件。

##### 2.8 Web Storage 和 Cookie 的区别总结

Web Storage 是为了更大容量存储设计的，而Cookie的大小是受限的。

cookie 在每次请求一个新的页面的时候都会被发送过去，在浏览器和服务器间来回传递，这样无形中浪费了带宽。

cookie 可以设定访问域，在同源窗口中可以共享，而 web storage 受同源策略限制。

但是Cookie也是不可以或缺的：Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。


#### 3. 数据库：IndexDB 与 Web SQL


##### 3.1 两者的特点

（本小节详细内容请参考：聊一聊前端存储那些事儿）

websql 像关系型数据库，使用 sql 语句进行操作。
indexdb 像 nosql，直接使用 js 方法操作数据。

访问：indexdb 和 websql 与 web storage 一致，均是在创建数据库的域名下才能访问。且不能指定访问域名。

存储时间：这两位的存储时间也是永久，除非用户清除数据，可以用作长效的存储。

大小限制：理论上讲，这两种存储的方式是没有大小限制的。然而indexeddb的数据库超过50M的时候浏览器会弹出确认。基本上也相当于没有限制了。

性能测试：indexeddb查询少量数据花费差不多20MS左右。大量数据的情况下，相对耗时会变长一些，但是也就在30MS左右，也是相当给力了，10W数据+，毕竟nosql。
而 websql 的效率也不错，10w+数据，简单查询一下，只花费了20MS左右。


##### 3.2 IndexDB 特点

它的数据不是保存在表中，而是保存在对象存储空间中。
创建对象存储空间时，需要定义一个键，然后就可以添加数据。
可以使用游标在对象存储空间中查询特定的对象。
而索引则是为了提高查询速度而基于特定的属性创建的。
说明：indexDB 目前兼容性还不是很好，Web SQL 虽然已经过时，但是其兼容性却非常好，几乎是移动端均可用（兼容性对比查看请移步：http://caniuse.com/#search=websql 以及 http://caniuse.com/#search=indexdb）。

因此，能用 indexdb，就用 indexdb，因为其代表了未来的发展方向，如果不能使用尽量使用 websql 进行代替。

##### 3.3 IndexDB 异步 API

IndexDB 设计的操作完全是异步的。因此，大多数操作会以请求的方式进行，但这些操作会在后期执行，如果成功则返回结果，如果失败则返回错误。差不多每一次 IndexDB 操作，都需要你注册 onerror 或 onsuccess 事件处理程序，以确保适当地处理结果。

![image](/images/posts/js/storage-IndexDB.png)

打开数据库时，实质上返回了一个DB对象，该对象存在于 result 中。


##### 3.4 Web SQL Database

需要注意的是，HTML5已经会放弃Web SQL Database

放弃的原因如下：

>This document was on the W3C Recommendation track but specification work has stopped. The specification reached an impasse: all interested implementors have used the same SQL backend (Sqlite), but we need multiple independent implementations to proceed along a standardisation path. 
大概意思是： 
>该文件是W3C推荐标准，但规范的制定工作已经停止。该规范陷入僵局：所有感兴趣的实现者都使用了相同的SQL后端（SQLite的），但我们需要多个独立的实现沿着规范化的路径进行。 
翻译参考：http://www.zhihu.com/question/41951041 
有关标准参考： https://www.w3.org/TR/webdatabase/


如果你掌握过mysql，orancle,mssql等关系型数据库，对于websql的理解还是比较容易的。

websql的关键技术点分为：

测试浏览器支持
创建数据库
创建表格
插入数据等

用到的几个核心方法：

openDatabase openDatabaseSycn 创建数据库

transaction起事务
executeSql执行sql语句

详情可以参考：http://blog.csdn.net/netcy/article/details/52188531


##### 3.5 应用场景：

当我们是在做一个离线应用，或者webapp的时候，可以考虑使用本地数据库中存取数据。如果不存大量的数据的话，其实localStorage就够用了。亦或者，你想把一张用户的皮肤图片之类的大量数据存入客户端缓存起来，localStorage已经不够用了的话，也可以尝试一下websql与indexeddb。



> ######  参考： http://www.cnblogs.com/Ruth92/p/6384057.html?utm_source=itdadao&utm_medium=referral
> ###### web sql参考:http://blog.csdn.net/netcy/article/details/52188531
> ###### IndexedDB参考：http://www.cnblogs.com/dolphinX/p/3415761.html
> ###### IndexedDB API：https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API

