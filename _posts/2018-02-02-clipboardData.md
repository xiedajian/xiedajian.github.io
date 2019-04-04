---
layout: post
title:  js 剪切板的用法(clipboardData.setData)
categories: js
description:  js 剪切板的用法(clipboardData.setData)
keywords: clipboardData , clipboardData
---


经常看到这样一种效果：就是单击一个按钮，就将某个区域内的内容，复制到了剪切板中。其实这个功能实现起来也不难，核心就是用到了window子对象clipboardData的一个方法：setData()
语法：
```
    clipboardData.setData(sDataFormat, sData)
```
参数：
- sDataFormat：要复制的内容的格式；
- sData：要复制的内容。

返回值：复制成功返回true；失败返回false。

例如
```
<script>
function copy(id){
  var id;
  var text=document.all(id).innerText
  if (clipboardData.setData("text",text)){
    alert("复制成功!")
  }else{
    alert("复制失败!")
  }
}
</script>
```

#### clipboardData 对象

提供了对剪贴板的访问。
三个方法 
- 1.clearData(sDataFormat) 删除剪贴板中指定格式的数据。 
- 2.getData(sDataFormat) 从剪贴板获取指定格式的数据。 
- 3.setData(sDataFormat, sData) 给剪贴板赋予指定格式的数据。返回 true 表示操作成功。
**兼容问题：出于安全性问题，只有IE支持！！！**
##### 常见用法1：禁止复制
```
 <script>
        //网页禁止复制  body oncopy事件中 设置 return false；
        //oncopy、onpase事件：复制、粘贴事件，可用于多数控件
        // div 没有oncopy事件，  body 与 文本框有这个事件
        function forbidCopy() {
            window.alert("网页的内容，自能看，不能动！");
            return false;
        }
 </script>
<body  oncopy="forbidCopy();" >
    输入密码：
    <input type="text" oncopy="window.alert('禁止复制!');return false;" />
    再输入一边密码：
    <input type="text" onpaste="window.alert('禁止粘贴!');return false;" />
</body>
```


##### 常见用法2：点击复制指定标记中的内容 
```
<script type="text/javascript">
function copyText(obj) { 
var rng = document.body.createTextRange(); 
rng.moveToElementText(obj); 
rng.scrollIntoView(); 
rng.select(); 
rng.execCommand("Copy"); 
rng.collapse(false); 
alert("复制成功!"); 
} 
function oCopy(id){
    var obj=document.getElementById(id);
    obj.select();
     js=obj.createTextRange();
     js.execCommand("Copy");
     alert("代码已经被成功复制！");
}
</script>

//以下是代码片段：
<span id="tbid">http://pmp.www.jb51.net</span>   
<a href="#" onclick="copyText(document.all.tbid)">点击复制</a>
<span id="tbid2">http://www.www.jb51.net/pmp</span>   
<a href="#" onclick="oCopy(tbid2)">点击复制</a>
```


##### 常见用法3：复制内容后附加信息
```
 </script>
        function SetCopyContent() {
            window.event.returnValue = false;
            var content = window.clipboardData.getData("Text") + "/r/n";
            content += "本资源来自简书 " + this.location.href;
            window.clipboardData.setData('Text', content);
            alert("复制成功，请粘贴到你的QQ/MSN上推荐给你的好友");
        }
 </script>
<body  oncopy="SetCopyContent();" >
</body>
```
