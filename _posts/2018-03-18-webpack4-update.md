---
layout: post
title:  webpack4升级
categories: webpack
description: webpack4升级
keywords: webpack4
---


### webpack-cli 
现在cli需要单独进行安装
```
 npm install webpack-cli -D
```

### 生产模式和开发模式
事实上，如果你注意npm run build的输出，你会看到一个很好的警告：

The ‘mode’ option has not been set. Set ‘mode’ option to ‘development’ or ‘production’ to enable defaults for this environment.（“mode”选项尚未设置。 将“mode”选项设置为“development”或“production”以启用此环境的默认值。）


打开package.json并填入脚本部分，如下所示：
```
  "scripts": {
    "dev": "webpack --mode development",
    "build": "webpack --mode production",
  },
```
### splitChunks
4.0中已经删除`CommonsChunkPlugin`，替换成了`splitChunks`，[这里有相关介绍内容](https://medium.com/webpack/webpack-4-code-splitting-chunk-graph-and-the-splitchunks-optimization-be739a861366)

解决方案： 去除 new webpack.optimize.CommonsChunkPlugin，修改为
```
optimization: {
    runtimeChunk: {
        name: "manifest"
    },
    splitChunks: {
        cacheGroups: {
            commons: {
                test: /[\\/]node_modules[\\/]/,
                name: "vendor",
                chunks: "all"
            }
        }
    }
}
```
optimization参数介绍：
```
optimization: {
    splitChunks: {
      chunks: "initial",         // 必须三选一： "initial" | "all"(默认就是all) | "async"
      minSize: 0,                // 最小尺寸，默认0
      minChunks: 1,              // 最小 chunk ，默认1
      maxAsyncRequests: 1,       // 最大异步请求数， 默认1
      maxInitialRequests: 1,    // 最大初始化请求书，默认1
      name: () => {},              // 名称，此选项课接收 function
      cacheGroups: {                 // 这里开始设置缓存的 chunks
        priority: "0",                // 缓存组优先级 false | object |
        vendor: {                   // key 为entry中定义的 入口名称
          chunks: "initial",        // 必须三选一： "initial" | "all" | "async"(默认就是异步)
          test: /react|lodash/,     // 正则规则验证，如果符合就提取 chunk
          name: "vendor",           // 要缓存的 分隔出来的 chunk 名称
          minSize: 0,
          minChunks: 1,
          enforce: true,
          maxAsyncRequests: 1,       // 最大异步请求数， 默认1
          maxInitialRequests: 1,    // 最大初始化请求书，默认1
          reuseExistingChunk: true   // 可设置是否重用该chunk（查看源码没有发现默认值）
        }
      }
    }
  },
```
