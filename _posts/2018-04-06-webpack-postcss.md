---
layout: post
title:  webpack-postcss
categories: webpack
description:  webpack-postcss
keywords: webpack , postcss
---


## css自动加载前缀

CSS3是目前作为一个前端必须要掌握的技能，但是由于现在好多浏览器还是不兼容CSS3，所以前端需要多写很丑很难看的前缀代码；以前都是边查Can I Use ，边添加，这样很麻烦，现在配置一个插件[postcss](https://github.com/postcss/postcss-loader)就可以搞定；

PostCSS是一个CSS的处理平台，它可以帮助你的CSS实现更多的功能，但是今天我们就通过其中的一个加前缀的功能，初步了解一下PostCSS。

安装：

```
npm install --save-dev postcss-loader autoprefixer
```

在根目录下，建立一个postcss.config.js文件：

```
module.exports = {
    plugins:[
        require('autoprefixer')
    ]
}
```

这就是对postCSS一个简单的配置，引入了autoprefixer插件。让postCSS拥有添加前缀的能力，它会根据 can i use 来增加相应的css3属性前缀。

在webpack.config.js中配置Loader:

```
{
    test: /\.css$/,
    use: extractTextPlugin.extract({
        fallback: 'style-loader',
        use: [
            { loader: 'css-loader', 
                options: { importLoaders: 1 } 
            },
            'postcss-loader'
        ]
    })

}
```

## 消除多余CSS

随着项目的进展，编写的CSS会越来越多，有时候需求更改，带来DOM结构的更改，造成CSS的冗余，所以为了减少CSS文件的体积，需要消除冗余的CSS；使用PurifyCSS可以大大减少CSS冗余；这个插件必须配合extract-text-webpack-plugin来使用；

安装：

```
npm install --save-dev purifycss-webpack purify-css
```

引入glob：

因为需要同步检查HTML模板，所以需要引入node的glob对象使用，在webpack.config.js文件头部引入

```
const glob = require('glob');
```

引入purifycss-webpack:

```
const PurifyCssPlugin = require('purifycss-webpack');
```

配置plugins：

```
plugins:[
    new PurifyCssPlugin({
        paths:glob.sync(path.join(__dirname,'src/*.html'))
    })
]
```

参考地址：

*   [https://github.com/zhuangZhou/webpack_demo](https://github.com/zhuangZhou/webpack_demo)
*   [http://jspang.com/2017/09/16/webpack3-2/#15webpackbabel](http://jspang.com/2017/09/16/webpack3-2/#15webpackbabel)

