---
title: CSS单位全解
date: 2017-11-13 21:33:07
tags: css
---

## 对于css单位的认识

对于网页布局的单位，只知道`px`是仅仅不够的，还需要知道其他几个比较常见的单位，特别是css3推出的一些新的度量单位。

<!-- more -->

## em

`em`是一种相对单位，它相对于父元素的字体大小。

`em`常用于存在缩放需求时使用，比如在多行文本段落中，如果行高设置为`line-height: 18px`，如果文字的大小因为缩放发生改变，行高值是不会随之改变的，一直都是`18px`，如果将行高设置成一种相对值`line-height: 1.2em`，那么此时的行高值就会随着字体的大小改变而变化。

因为`em`是相对于父元素的字体大小，所以该单位存在明显的嵌套层级关系，浏览器默认的字体大小是`16px`，因此`1em = 16px`，需要注意的是**chrome**浏览器能够显示的最小字体是`12px`，当字体大小设置小于`12px`字体将按照`12px`显示。

<p data-height="265" data-theme-id="0" data-slug-hash="MOowbV" data-default-tab="jjresult" data-user="YeaseonZhang" data-embed-version="2" data-pen-title="em" class="codepen">See the Pen <a href="https://codepen.io/YeaseonZhang/pen/MOowbV/">em</a> by YeaseonZhang (<a href="https://codepen.io/YeaseonZhang">@YeaseonZhang</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## rem

`rem`(**root em**)是一种相对单位，和`em`不同点是`rem`相对于根元素`html`的字体大小。

利用这个特性，我们常常使用`rem`单位进行移动端页面的布局。

{% asset_img rem.png rem浏览器兼容性 %}

<p data-height="265" data-theme-id="0" data-slug-hash="YEQmjJ" data-default-tab="result" data-user="YeaseonZhang" data-embed-version="2" data-pen-title="rem" class="codepen">See the Pen <a href="https://codepen.io/YeaseonZhang/pen/YEQmjJ/">rem</a> by YeaseonZhang (<a href="https://codepen.io/YeaseonZhang">@YeaseonZhang</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assetsi/embed/ei.js"></script>

现在移动端设计稿的尺寸大多为以iPhone6为基准的`750px`，首先将页面划分为100份，每一份的宽度为`npx`, 设置`1rem = 10n`，所以以`750px`为基准的话，每份就是`7.5px`，`1rem`就对应`75px`。但这仅仅只能适配宽度为`750px`的设备，所以通过加载页面时动态计算设备的尺寸来，修改`html`的`font-size`值，就能实现页面缩放适配。

最简单设置rem基准值的方法。
```js
document.addEventListener('DOMContentLoaded', function (e) {
  var rem = window.innerWidth / 10 + 'px';
  document.getElementsByTagName('html')[0].style.fontSize = rem;
})
```
使用**scss**将`px`转换为`rem`值
```scss
@function px2rem ($px) {
  $rem: 75px;
  @return ($px / $rem) + rem;
}
```

## vh/vw
