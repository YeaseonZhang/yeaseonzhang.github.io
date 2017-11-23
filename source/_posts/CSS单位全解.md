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

`vh/vw`单位类似于百分比单位不同之处在于`vh/vw`单位的布局不依赖于父级的宽高，而是相对于视口的宽高。
`1vh`等于1%的视口高度，`1vw`等于1%的视口宽度。若视口宽度是`750px`，那么`1vw`就是`7.5px`。

{% asset_img v-unit.png 视口单位兼容性 %}

目前移动端对于视口单位基本全面支持。

<p data-height="705" data-theme-id="0" data-slug-hash="JOvLOy" data-default-tab="result" data-user="YeaseonZhang" data-embed-version="2" data-pen-title="v-unit" class="codepen">See the Pen <a href="https://codepen.io/YeaseonZhang/pen/JOvLOy/">v-unit</a> by YeaseonZhang (<a href="https://codepen.io/YeaseonZhang">@YeaseonZhang</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

**注**：不同浏览器在获取视口的方法不同
IE9+、Firefox、Safari、Opera和Chrome均提供了4个属性`innerWidth`、`innerHeight`、`outerWidth`和`outerHeight`。

+ IE9+、Safari和Firefox中，`outerWidth`和`outerHeight`返回浏览器窗口本身的尺寸，而`innerWidth`和`innerHeight`则表示该容器中页面视图区的大小（减去边框宽度）
+ Chrome中，`inner*`和`outer*`返回相同的值，即视口大小而非浏览器窗口的大小。
+ 在IE、Firefox、Safari、Opera和Chrome中，都能通过`document.documentElement.clientWidth`和`document.documentElement.clientHeight`中保存了页面视口信息。

获取页面视口大小
```js
var pageWidth = window.innerWidth,
  pageHeight = window.innerHeight;

if (typeof pageWidth != 'number') {
  if (document.compatMode == 'CSS1Compat') { // 浏览器标准模式
    pageWidth = document.documentElement.clientWidth;
    pageHeight = document.documentElement.clientHeight;
  } else {  // IE6 混杂模式
    pageWidth = document.body.clientWidth;
    pageHeight = document.doby.clientHeight;
  }
}
```

## vmin/vmax

`vmin`是指`vh`和`vw`中较小的那一个的大小，当然`vmax`就是`vh`和`vw`中较大的那一个。

例如，浏览器视口宽`1100px`、高`700px`，那么`1vmin = 7px; 1vmax = 11px`；如果浏览器视口宽`800px`，高`1080px`，那么`1vmin = 8px; 1vmax = 10.8px`

很有意思的是，使用这个单位的时候我们并不关心宽高，而是按照大小来区分，所以在移动端中的应用会比较多。

```css
.box {
  height: 100vmin;
  width: 100vmin;
}
```
{% asset_img vmin.png %}

```css
.box {
  height: 100vmax;
  width: 100vmax;
}
```
{% asset_img vmax.png %}

## ch 和 ex

`ch`和`ex`单位都是基于当前字体的特定单位。

`ch`单位，被定义为当前字体`0`字符的宽度。所以如果你使用的是等宽字体，那么你就可以直接定义一个盒子能够容纳多少个字符。

<p data-height="265" data-theme-id="0" data-slug-hash="EbLLPQ" data-default-tab="result" data-user="YeaseonZhang" data-embed-version="2" data-pen-title="ch" class="codepen">See the Pen <a href="https://codepen.io/YeaseonZhang/pen/EbLLPQ/">ch</a> by YeaseonZhang (<a href="https://codepen.io/YeaseonZhang">@YeaseonZhang</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

`ex`单位，被定义为当前字体`x`字符高度。这个单位通常用于排版微调，确保精确控制。

<p data-height="265" data-theme-id="0" data-slug-hash="WXJJMR" data-default-tab="css,result" data-user="YeaseonZhang" data-embed-version="2" data-pen-title="ex" class="codepen">See the Pen <a href="https://codepen.io/YeaseonZhang/pen/WXJJMR/">ex</a> by YeaseonZhang (<a href="https://codepen.io/YeaseonZhang">@YeaseonZhang</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## 参考资料
+ [7 CSS Units You Might Not Know About](https://webdesign.tutsplus.com/articles/7-css-units-you-might-not-know-about--cms-22573)

+ [網頁樣式表 CSS提示以及技巧](https://www.w3.org/Style/Examples/007/units.zh_HK.html)
