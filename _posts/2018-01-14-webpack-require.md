---
layout: post
title:  webpack解惑：require的五种用法
categories: webpack
description:  webpack解惑：require的五种用法
keywords: webpack , require
---


webpack中可以写commonjs格式的require同步语法，可以写AMD格式的require回调语法，还有一个require.ensure，以及webpack自己定义的require.include，再加上ES6的import语法，这么多岂不是会把人给搞乱。本篇就来梳理一下这些require各自的特点，以及都在什么场景下使用。
## commonjs同步语法
经典的commonjs同步语法如下：
```
var a = require('./a');
a.show();
```
此时webpack会将a.js打包进引用它的文件中。这是最普遍的情形，不必赘述。

##  commonjs异步加载

在commonjs中有一个Modules/Async/A规范，里面定义了require.ensure语法。webpack实现了它，作用是可以在打包的时候进行代码分片，并异步加载分片后的代码。用法如下：

```
require.ensure([], function(require){
    var list = require('./list');
    list.show();
});

```
此时list.js会被打包成一个单独的chunk文件，大概长这样：

> 1.fb874860b35831bc96a8.js

可读性比较差。我在上一篇结尾也提到了，给它命名的方式，那就是给require.ensure传递第三个参数，如：

```
require.ensure([], function(require){
    var list = require('./list');
    list.show();
}, 'list');
```
这样就能得到你想要的文件名称：

> list.fb874860b35831bc96a8.js

你也可以传入像"question/list"这样带层级的名字，这样webpack会按照层级给你创建[文件夹](http://www.07net01.com/tags-%E6%96%87%E4%BB%B6%E5%A4%B9-0.html)。

需要注意的是，如果你在require.ensure的函数中引用了两个以上的模块，webpack会把它们打包在一起，比如：

```
require.ensure([], function(require){
    var list = require('./list');
    list.show();
    var edit = require('./edit');
    edit.display();
}, 'list_and_edit');
```

list.js和edit.js将会被打包成一个文件，并命名为list_and_edit.js。这就需要根据你的实际情况来衡量了，如果你不希望打包在一起，只能写两个require.ensure分别引用这两个文件。

多说一句，这种思维其实我是很不喜欢的，在编码阶段却要对打包的事情做出决策，明显违背了职责分离原则。
## commonjs预加载懒执行
在上面的用法中，我们给require.ensure的第一个参数传了空数组，实际上这里是可以接收模块名称的，作用就是实现预加载懒执行。用法如下：
```
require.ensure(['./list'], function(require){
    var list = require('./list');
    list.show();
});
```
给require.ensure的第一个参数传了['./list']，执行到这里的时候list.js会被[浏览器](http://www.07net01.com/tags-%E6%B5%8F%E8%A7%88%E5%99%A8-0.html)下载下来，但是并不会执行list.js模块中的代码，也就是webpack官网说的，不会进行evaluate。真正进行evaluate的时候是到了后面这句var list = require('./list');这就是所谓的懒执行。

写在函数中的多个模块会被打包在一起，这一点和上面没有区别。另外，写在数组中的模块也会跟他们打包在一起，不管你有没有手动执行。

这种写法也是有点别扭的，像是commonjs和AMD的结合体，而且一个模块名称还要写两次，真是不够优雅。所以webpack自己定义了一个方法，能够实现预加载。

## webpack自带的require.include
require.include是webpack自己提供的，并没有什么规范做后台，所以是个小角色。它可以实现上面是预加载功能，而不用把模块写在数组中，用法如下：
```
require.ensure([], function(require){
    require.include('./list');//此处只加载不执行
});
```
据webpack官网文档介绍，require.include还有一个作用是能把子模块中的公共部分，提取到父模块中，比如child1和child2都引用了list.js这个模块，那么如果在parent中include了list.js，那么子模块中的就会被删掉，相当于提升到了父模块中。（这里所谓的父子关系是指引用关系）

这个方法官方也是一笔带过，看来也是一个鸡肋的东西，用处不大。因为我发现require.include的返回值是undefined，也就是说，如果你想使用模块，姿势是这样的：
```
require.ensure([], function(require){
    require.include('./preview'); //加载
    let p = require('./preview'); //执行
    p.getUrl(); //使用
}, 'pre');
```
## AMD异步加载
webpack既支持commonjs规范也支持AMD规范，这就意味着AMD的经典语法是可以正常使用的，如：
```
require(['./list'], function(list){
    list.show();
});
```
当然，这样写的话list.js也是被单独打包成一个文件的。与上面类似，如果你在这里写了多个模块，那么这些模块都会被打包成一个文件，如：
```
require(['./list', './edit'], function(list, edit){
    list.show();
    edit.display();
});
```
list.js和edit.js会被打包在一起。不同的是，AMD的方式无法传入第三个参数当文件名，所以得不到很好看的文件。

## ES6 import
这年头不用ES6都不好意思跟人打招呼。所以我们的代码中，又会多一种模块引入语法，那就是import。import会被转化为commonjs格式或者是AMD格式，所以不要把它认为是一种新的模块引用方式。babel默认会把ES6的模块转化为commonjs规范的，你也不用费劲再把它转成AMD了。

所以如下写法是等价的：
```
import list from './list';
//等价于
var list = require('./list');
```
不过这两种写法只需选一种，避免在代码中同时使用两种，否则会造成混淆。

## 总结

以上把require的用法捋了一遍，明白了各自用法的区别之后，我们就可以在[项目](http://www.07net01.com/tags-%E9%A1%B9%E7%9B%AE-0.html)中进行选择了。我觉得最佳选择是往commonjs方向靠拢，想尝试ES6的话就用import代替commonjs同步语法即可。

因此，代码中保持以下两种风格就好：
```
//可打包在一起的同步代码，使用import语法
import list from './list';
 
//需要独立打包、异步加载的代码，使用require.ensure
require.ensure([], function(require){
    var list = require('./list');
});
```
很显然，你在写代码的时候还是需要对打包结果进行决策，这是我不喜欢webpack的原因。gulp那样多好，编码就是编码，编译就是编译，分开来。不过这就是webpack以模块为核心的打包方式的特点吧，仁者见仁，只要团队内做一个约定，也不会打的一塌糊涂。

转自[https://www.cnblogs.com/laneyfu/p/6262321.html](https://www.cnblogs.com/laneyfu/p/6262321.html)













