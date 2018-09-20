---
title: vue-awesome-swiper 踩坑
date: 2018-09-20 22:27:20
tags: vue组件
---

实际业务中经常会有轮播展示的需求，一般我们会选择名气最大的**swiper**，在vue中与之对应的就是**vue-awesome-swiper**，但是在使用中我们还是会遇到一些“坑”。

<!-- more -->

###  **swiper** 设置`display: none;`的坑

{% asset_img 效果图.png %}

时间轴是一个**swiper**，下面的商品也是一个**swiper**。切换时间轴，下面的商品也会切换。最初的想法就是每个时间段设置一个对应的商品**swiper**，然后通过判断点击的**时间点**，去改变`display`显示不同的商品**swiper**。由于高度有限只展示了两行商品**swiper**。

{% asset_img 实际布局.png 实际布局 %}

但是这样存在一个问题，就是切换“时间”，第二次点击同一“时间”，商品**swiper**会滚动到最后的位置。

解决该问题的方案就是不通过`display`去控制显示与否，通过`height: 0; overflow: hidden`去隐藏，通过设置真实高度去显示。

### `loop: true` 循环轮播

设置`loop: true`也会存在问题，就是前后复制的`slide`节点，只是显示层的复制了**dom**元素，并没有将事件给复制到节点上，导致点击事件无效。如果`slide`内部多个点击事件，不建议开启`loop: true`。

单个事件的话，可以通过监听`slide`层级的点击事件，然后通过`realIndex`（切记是`realIndex`，不是`activeIndex`），去判断哪个`slide`进行了点击。

但是这种处理方式并不优雅，对于`slide`内部是一个组件的形式不够友好，内部的状态需要通过外部的事件去处理。希望`vue-awesome-swiper`以后会改进这个问题吧。

参考： [那些年，那些坑--swiper loop：true引发绑定dom的click事件无效及解决方案](https://segmentfault.com/a/1190000015327048)
