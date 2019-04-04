---
layout: post
title:  编写CommonJS, AMD, CMD 和 原生 JS都可以使用的插件
categories: web
description:  编写CommonJS, AMD, CMD 和 原生 JS都可以使用的插件
keywords: 编写CommonJS , CMD
---


最近几年，我们可以选择的Javascript组件的生态系统一直在稳步增长。虽然陡增的选择范围是极好的，但当组件混合匹配使用时就会出现很尴尬的局面。开发新手们会很快发现不是所有组件都能彼此“和平相处”。

为了解决这个问题，两种竞争关系的模块规范AMD和CommonJS问世了，它们允许开发者遵照一种约定的沙箱化和模块化的方式来写代码，这样就能避免“污染生态系统”。
## Javascript模块化
在了解这些规范之前，还是先了解一下什么是模块化。

模块化是指在解决某一个复杂问题或者一系列的杂糅问题时，依照一种分类的思维把问题进行系统性的分解以之处理。模块化是一种处理复杂系统分解为代码结构更合理，可维护性更高的可管理的模块的方式。可以想象一个巨大的系统代码，被整合优化分割成逻辑性很强的模块时，对于软件是一种何等意义的存在。对于软件行业来说：解耦软件系统的复杂性，使得不管多么大的系统，也可以将管理，开发，维护变得“有理可循”。

还有一些对于模块化一些专业的定义为：模块化是软件系统的属性，这个系统被分解为一组高内聚，低耦合的模块。那么在理想状态下我们只需要完成自己部分的核心业务逻辑代码，其他方面的依赖可以通过直接加载被人已经写好模块进行使用即可。

首先，既然是模块化设计，那么作为一个模块化系统所必须的能力：

- 定义封装的模块。
- 定义新模块对其他模块的依赖。
- 可对其他模块的引入支持。
好了，思想有了，那么总要有点什么来建立一个模块化的规范制度吧，不然各式各样的模块加载方式只会将局搅得更为混乱。那么在JavaScript中出现了一些非传统模块开发方式的规范 CommonJS的模块规范，AMD（Asynchronous Module Definition），CMD（Common Module Definition）等。

## CommonJS
CommonJS是服务器端模块的规范，Node.js采用了这个规范。

根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。

CommonJS 有三个全局变量 module、exports 和 require。但是由于 AMD 也有 require 这个全局变量，故不使用这个require变量来进行检测。

如果想要对外提供接口的话，可以将接口绑定到 exports （即 module.exports） 上。
```
function MyModule() {
    // ...
}

if(typeof module !== `undefined` && typeof exports === `object`) {
    module.exports = MyModule;
}
```
CommonJS 加载模块是同步的，所以只有加载完成才能执行后面的操作。像Node.js主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 AMD CMD 解决方案。

## AMD
AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义".

由于不是JavaScript原生支持，使用AMD规范进行页面开发需要用到对应的库函数，也就是大名鼎鼎RequireJS，实际上AMD 是 RequireJS 在推广过程中对模块定义的规范化的产出

AMD设计出一个简洁的写模块API：
**模块定义define(id?, dependencies?, factory);**
第一个参数 id 为字符串类型，表示了模块标识，为可选参数。若不存在则模块标识应该默认定义为在加载器中被请求脚本的标识。如果存在，那么模块标识必须为顶层的或者一个绝对的标识。
第二个参数，dependencies ，是一个当前模块依赖的，已被模块定义的模块标识的数组字面量。
第三个参数，factory，是一个需要进行实例化的函数或者一个对象。

通过参数的排列组合，这个简单的API可以从容应对各种各样的应用场景，如下所述。
```
//3个参数，定义一个名为 ‘alpha’ 的模块，依赖于require，exports，beta这三个模块
define("alpha", [ "require", "exports", "beta" ], function( require, exports, beta ){
    export.verb = function(){
        return beta.verb();
        // or:
        return require("beta").verb();
    }
});

//2个参数，定义一个匿名模块，依赖于require，exports，beta这三个模块
define([ "require", "exports", "beta" ], function( require, exports, beta ){
    export.verb = function(){
        return beta.verb();
        // or:
        return require("beta").verb();
    }
});

//1个参数,定义一个匿名模块，不依赖其他模块
define(function(){
    return 'hello world';
});
```
也可定义定义数据对象模块
```
//1个参数,定义一个定义数据对象模块
define({
    users: [],
    members: []
});

//等价于
define(function(){
    return{
        users: [],
        members: []
    }
});

```

除了define外，AMD还保留一个关键字require。require 作为规范保留的全局标识符，可以实现为 module loader，也可以不实现。

**模块加载require([module], callback)**
```
require(['math'], function(math) {
　math.add(2, 3);
});
```

AMD 规范中，define 函数同样有一个公有属性 define.amd。

AMD 中的参数便是这个模块的依赖。那么如何在 AMD 中提供接口呢？它是返回一个对象，这个对象就作为这个模块的接口，故我们可以这样写：

```
function MyModule() {
    // ...
}

if(typeof define === `function` && define.amd) {
    define(function() { return MyModule; });
}
```
## CMD
CMD 即Common Module Definition通用模块定义，CMD规范是国内发展出来的，就像AMD有个requireJS，CMD有个浏览器的实现SeaJS，SeaJS要解决的问题和requireJS一样，只不过在模块定义方式和模块加载（可以说运行、解析）时机上有所不同 

语法 
Sea.js 推崇一个模块一个文件，遵循统一的写法 
define(id?, deps?, factory) 
因为CMD推崇

- 一个文件一个模块，所以经常就用文件名作为模块id
- CMD推崇依赖就近，所以一般不在define的参数中写依赖，在factory中写

factory是一个函数，有三个参数，function(require, exports, module)

- require 是一个方法，接受 模块标识 作为唯一参数，用来获取其他模块提供的接口：require(id)
- exports 是一个对象，用来向外提供模块接口
- module 是一个对象，上面存储了与当前模块相关联的一些属性和方法

不考虑多了一层函数外，格式和Node.js是一样的：使用require获取依赖模块，使用exports导出API。
```
// 定义模块  myModule.js
define(function(require, exports, module) {
    var a = require('a'),
          b = require('b');

    exports.action = function() {};
} );

// 加载模块
seajs.use(['myModule.js'], function(my){
      ...
});
```
有人说CMD是同步的，其实CMD是异步加载的，CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的

## CMD 与 AMD 区别
最明显的区别就是在模块定义时对依赖的处理不同

- 对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）。

- CMD推崇依赖就近，只有在用到某个模块的时候再去require ,AMD推崇依赖前置。
```
//AMD
define(['./a','./b'], function (a, b) {
 
    //依赖一开始就写好
    a.test();
    b.test();
});
 
//CMD
define(function (requie, exports, module) {
     
    //依赖可以就近书写
    var a = require('./a');
    a.test();
     
    ...
    //软依赖
    if (status) {
     
        var b = requie('./b');
        b.test();
    }
});
```
这种区别各有优劣，只是语法上的差距，而且requireJS和SeaJS都支持对方的写法

AMD和CMD最大的区别是对依赖模块的执行时机处理不同，注意不是加载的时机或者方式不同

同样都是异步加载模块，AMD在加载模块完成后就会执行改模块，所有模块都加载执行完后会进入require的回调函数，执行主逻辑，这样的效果就是依赖模块的执行顺序和书写顺序不一定一致，看网络速度，哪个先下载下来，哪个先执行，但是主逻辑一定在所有依赖加载完成后才执行

CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的

这也是很多人说AMD用户体验好，因为没有延迟，依赖模块提前执行了，CMD性能好，因为只有用户需要的时候才执行的原因

## 原生js 标签的方式加载
没什么多说的，其实就是挂载到 window 下

## 编写通用的格式
```
(function (window, factory) {
    if (typeof exports === 'object') {
     
        module.exports = factory();
    } else if (typeof define === 'function' && define.amd) {
     
        define(factory);
    } else {
     
        window.eventUtil = factory();
    }
})(this, function () {
    // module ...
});
```
参考jquery，echarts可以写的更简单一些
```
(function (global, factory) {
	typeof exports === 'object' && typeof module !== 'undefined' ? factory(exports) :
	typeof define === 'function' && define.amd ? define(['exports'], factory) :
	(factory((global.D3MakeSvgPicClass = {})));
}(this, (function (exports) {
    //// module ...
    exports.D3MakeSvgPicClass = MakeSvgPicClass;
 })));
```

依赖jquery的库的通用写法案例
```
; (function (factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD. Register as an anonymous module.
    define(['jquery'], factory);
  } else if (typeof module === 'object' && module.exports) {
    // Node/CommonJS
    module.exports = function (root, jQuery) {
      if (jQuery === undefined) {
        // require('jQuery') returns a factory that requires window to
        // build a jQuery instance, we normalize how we use modules
        // that require this pattern but the window provided is a noop
        // if it's defined (how jquery works)
        if (typeof window !== 'undefined') {
          jQuery = require('jquery');
        }
        else {
          jQuery = require('jquery')(root);
        }
      }
      factory(jQuery);
      return jQuery;
    };
  } else {
    // Browser globals
    factory(jQuery);
  }
}(function (jQuery) {

    // 库的内容主体

 }));
```


参考资料：[https://www.jianshu.com/p/bd4585b737d7](https://www.jianshu.com/p/bd4585b737d7)
参考资料：[https://www.cnblogs.com/zzsdream/p/5158968.html](https://www.cnblogs.com/zzsdream/p/5158968.html)
