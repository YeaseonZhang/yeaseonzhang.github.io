---
title: 深入理解Flex属性
date: 2017-07-05 14:25:59
tags:
---

Flex 是 Flexible Box的缩写，意为“弹性布局”，可以简便、完整、响应式地实现各种页面布局。

Flex的出现就是为了解决传统布局的`display`属性 + `position`属性 + `float`属性的复杂性。

<!-- more -->

## 浏览器对于Flex的支持性

{% asset_img caniuse.png https://caniuse.com/ %}

可以看到浏览器对于Flex布局的支持性已经很好了，所以我们可以放心大胆的用在我们项目中。

## flex 属性

`flex: flex-grow | flex-shrink | flex-basis`

flex属性包含三个值：`flex-grow`、`flex-shrink`和`flex-basis`

+ `flex-grow`: 扩展比例
+ `flex-shrink`: 收缩比例
+ `flex-basis`: 伸缩基准值

首先我们简单写一个flex布局的Demo
> HTML

```
<div class="parent">
  <div class="child_1"></div>
  <div class="child_2"></div>
  <div class="child_3"></div>
</div>
```

> CSS

```
.child_1,
.child_2,
.child_3 {
  flex: 1;
  height: 200px;
}

.child_1 {
  background-color: rgba(255, 0, 0, .5);
}

.child_2 {
  background-color: rgba(0, 255, 0, .5);
}

.child_3 {
  background-color: rgba(0, 0, 255, .5);
}
```
设置父元素`display: flex`，其子元素就能使用flex布局，我们只是简单的为子元素使用了`flex: 1`就实现了三等分，而且会随着父元素的宽度变化而变化。这就是我们平时flex布局的简单用法，即各个子元素按照比例布局。

`flex: 1`其实是`flex: 1 1 0%`的简写属性，即伸缩比例都是1。下面我们就分别理解下三个属性值对于布局的影响。

## flex-basis 属性

`flex-basis`属性是伸缩的基准值，这个属性也是我们计算最终宽度的决定性因素。

通过子元素`flex-basis`属性和与父元素(容器)宽度值进行比较，会出现两种情况(忽略相等的情况)：
+ 子元素`flex-basis`属性和 < 父元素宽度
+ 子元素`flex-basis`属性和 > 父元素宽度

上面的两种情况就分别对应了`flex-grow`和`flex-shrink`属性生效的情况，也就是说当子元素的`flex-basis`属性宽度和小于父元素的宽度值时`flex-grow`生效，反之`flex-shrink`生效。

## flex-grow 属性

`flex-grow`属性是扩展比例，上面我们也谈到了当子元素的`flex-basis`总和小于父元素的宽度值时`flex-grow`生效。

现在我们就来改变CSS，满足这个前提条件。

> CSS

```
.parent {
  display: flex;
  width: 600px;
}

.child_1,
.child_2,
.child_3 {
  height: 200px;
}

.child_1 {
  flex: 1 0 150px;
  background-color: rgba(255, 0, 0, .5);
}

.child_2 {
  flex: 0 0 100px;
  background-color: rgba(0, 255, 0, .5);
}

.child_3 {
  flex: 1 0 150px;
  background-color: rgba(0, 0, 255, .5);
}
```
此时，各个元素的`flex-basis`和为`(150 + 100 + 150) = 400px`, 小于父元素的`600px`,我们就来分别计算每个子元素的宽度值。
```
可用空间 = 父元素width - 子元素flex-basis总和 => 600 - (150 + 100 + 150) = 200

单位扩展空间 = 可用空间/子元素flex-grow总和 => 200/(1 + 0 + 1) = 100
```
子元素的计算公式为`width = flex-basis + flex-grow * 单位扩展空间`

所以`child_1`宽度为`(150 + 1 * 100) = 250px`, `child_2`宽度为`(100 + 0 * 100) = 100px`, `child_3` 同 `child_1`。

## flex-shrink 属性

`flex-shrink` 属性是收缩比例，当子元素的`flex-basis`总和大于父元素的宽度值时`flex-grow`生效。

现在我们就修改CSS满足这个前提。

> CSS

```
.parent {
  display: flex;
  width: 600px;
}

.child_1,
.child_2,
.child_3 {
  /*flex: 1;*/
  height: 200px;
}

.child_1 {
  flex: 0 1 400px;
  background-color: rgba(255, 0, 0, .5);
}

.child_2 {
  flex: 0 1 200px;
  background-color: rgba(0, 255, 0, .5);
}

.child_3 {
  flex: 0 2 400px;
  background-color: rgba(0, 0, 255, .5);
}
```

当然啦，我们可以按照`flex-grow`的计算方法套用。

```
溢出空间 = 父元素width - 子元素flex-basis总和 => 600 - (400 + 200 + 400) = -400

单位收缩空间 = 溢出空间/子元素flex-shrink总和 => -400/(1 + 2 + 2) = -100
```
子元素的计算公式为`width = flex-basis + flex-shrink * 单位收缩空间`

所以`child_1`宽度为`(400 + 1 * (-100)) = 300px`, `child_2`宽度为`(200 + 1 * (-100)) = 100px`, `child_3` 同 `(400 + 2 * (-100)) = 200px`。

大功告成了？其实并没有实例情况并不是我们计算的那样`300px 100px 200px`，而是`285.72px 142.86px 171.42px`。

通过Google，发现了一种收缩因数的计算方法：
```
理想空间 = 子元素(flex-basis * flex-shrink)之和 => 400 * 1 + 200 * 1 + 400 * 2 = 1400

溢出空间 = 父元素width - 子元素flex-basis总和 => 600 - (400 + 200 + 400) = -400

收缩因数 = (flex-basis * flex-shrink) / 理想空间 => 400 / 1400 = 0.286; 200 / 1400 = 0.143; 800 / 1400 = 0.571
```
分别为每个子元素计算了收缩因数就能计算我们子元素的实际宽度，子元素的计算公式`width = flex-basis + 收缩因数 * 溢出空间`

所以`child_1`宽度为`400 + 0.286 * (-400) = 285.6`, `child_2`宽度为`200 + 0.143 * (-400) = 142.8`, `child_3`宽度为`400 + 0.571 * (-400) = 171.6`

现在我们计算出的值与浏览器渲染出的值基本上是相同的，我们对于`flex`属性的了解已经不再是单单的比例计算了，希望本文对你有所帮助。
