---
title: 微信浏览器Bug
date: 2017-03-06 18:19:13
tags: 微信 input change
---

不是一个笑话，微信浏览器真的有Bug。

一个我觉得不应该出Bug的地方，它居然有**Bug**，真的很坑。

<!-- more -->

### 问题描述

在微信浏览器中做上传文件的功能，发现iOS微信浏览器正常工作，但是Android微信浏览器`input[file]`不能触发`change`事件。

### 思考

出于“自省”，我觉得是我代码写的有问题，我是使用**Vue2**调用的`change`事件。

```
<input type="file" accept="*/*" name="file" @change="fileChange()"/>
```

第一反应就是，微信内置浏览器不支持**Vue2**的`@change`事件。

然后我就通过原生JS的写法监听`change`事件：

```
document.getElementsByName('file')[0].addEventListener('change', fileChange);
```

这样修改之后还是不能触发`change`事件，又改写了另一种原生写法：

```
<input type="file" accept="*/*" name="file" onchange="fileChange()"/>
```

不出所料，这样写还是没什么效果。

之后就是疯狂的**Google**，发现也有人遇到类似的问题，但是没有给出实质性的解决方案。

### 转机

发现知乎上有关于[微信内置的浏览器如何上传文件？](https://www.zhihu.com/question/21452742)的讨论。

也在其中找到了根本的解决原因。

{% asset_img zhihu.png 知乎 %}

这位兄台说，`accept="*/*"`上传图片，在**6.3.30**版本不能触发`change`事件了，证明之前是可行。

微信浏览器团队在升级中引入了**Bug**，从*2016-11-14*至今也没修复。

然后我就将我的`accept`更改为`accept="image/*"`果然就可行，鉴定是**Bug**无误。

但是我不仅要上传图片，任意格式都要支持啊，我果断把`accept`属性去掉了，测试居然所有类型都能支持上传了。

当初为什么要手贱写上`accept`属性，不然就不会搞到崩溃。

可是，仔细想想如果没有遇到这个**Bug**，也不会了解到微信内置浏览器(X5内核)也会出现这种想不到的**Bug**。

### 结语

谁的程序都会出错，遇到问题就应该一个个的排除，总会找到解决问题的办法。




