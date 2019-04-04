---
layout: post
title:  JS实现函数重载
categories: js
description:  JS实现函数重载
keywords: js
---



# 重载

做过后端的，可能有函数重载的概念

但是在JavaScript中不存在重载，如果方法名一样的话，后面的会把前面的覆盖掉，最后只保留一个方法的定义

不过我们可以根据传递的参数不一样，实现模拟重载的功能：

```
function sum(num) {
    if (typeof num === 'undefined') {
        return 0;
    }
    return num;
}
sum(100);
sum();

```
