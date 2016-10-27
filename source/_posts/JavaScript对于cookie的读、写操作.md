---
title: JavaScript对于cookie的读、写操作
date: 2016-10-27 16:58:52
tags: [JavaScipt, cookie]
---


前端开发中，不可避免的总要接触`session`、`cookie`这些网络数据打交道进行一些操作。
所以说作为一个前端er,
如果不会操作`cookie`，怎么好意思呐。
接下来，我们就来讲讲对于`cookie`的读写...

<!--more-->
## `cookie`的操作

### 写入`cookie`

```  
function setCookie(cName, cValue, days) {  
    var expires = new Date();  
    expires.setTime(expires.getTime() + parseInt(days) * 24 * 60 * 60 * 1000);  
    document.cookie = cName + "=" + escape(cValue) + ";expires=" + expires.toGMTString()+";path=/;domain=xxx.cn";  
};  
```
这个函数也十分简单,我们可以设置`cookie`的名字、值以及有效期。
**注**：我们并没有重写这个`cookie`，这是添加了我们设置的`cookie`。
+ `expires`: cookie的过期时间，注意这里要使用格林威治时间
+ `path`: 这个参数表示cookie保存的路径，如果没有给出的话会保存为当前站点的，如果给出值"/"的话会保存到当前虚拟目录
+ `domain`: 这个参数有点类似于session的保存路径，默认情况下保存在当前客户端,也可以自定义到其他位置

### 读取`cookie`

```       
function getCookie(name){    
    var arr = document.cookie.match(new RegExp("(^| )"+name+"=([^;]*)(;|$)"));  
    if(arr != null) 
	    return decodeURI(arr[2]);
    return null;  
} 
```
说一说，上面的正则表达式吧。
```
new RegExp("(^| )"+name+"=([^;]*)(;|$)")
```
+ `^`以什么开头，所以`(^| )"+name+"`就是以`"name"`或者`" name"`开头的
+ `[^;]*`匹配除了`;`以外的任意字符
+ `$`以什么结尾，所以`(;|$)`就是以`";"`或者`""`结尾
+ `decodeURI()`函数可对`encodeURI()` 函数编码过的 URI 进行解码

上述例子，还使用了一个技巧就是`()`进行分组，得到的正则表达式结果就是一个`Array`类型，每个`()`包括的部分都是数组的一部分，`index`从1开始。
+ `arr[0]`是匹配的整个结果。
+ `arr[1]`是`""`或者`" "`
+ `arr[2]`是我们要获取`name`的`value`值
+ `arr[4]`是是`""`或者`";"`

## 结语

介绍JS操作`cookie`的文章网上也有很多，本文不胜详尽，只是列出了我熟悉的一部分。
