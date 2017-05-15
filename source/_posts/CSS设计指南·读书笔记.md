---
title: CSS设计指南·读书笔记
date: 2017-05-14 13:30:08
tags: CSS 笔记
---

{% asset_img stylin_with_css.jpg CSS设计指南 %}

前端开发中，JS很重要，但CSS也不能轻视，我们来一场CSS学习之旅。

<!-- more -->

## CSS 工作原理

### 特殊的上下文选择符

#### 子选择符  >
`标签1 > 标签2`

**标签2**必须是**标签1**的子元素，反过来说，**标签1**是**标签2**的父元素。

#### 紧邻同胞选择符 +
`标签1 + 标签2`

**标签2**必须紧跟在其同胞**标签1**的后面。

#### 一般同胞选择符 ~
`标签1 ~ 标签2`

**标签2**必须跟在其同胞**标签1**后面。

#### 通用选择符 *

通用选择符`*`是一个通配符，它匹配任何元素。不过，一般在使用`*`选择符时，都会同时使用另一个选择符，比如：
```
p * {
	color: red;
}
```
这样只会把`p`包含的所有元素的文本变成红色。

还可以用它构成**非子选择符**，比如：
```
section * a {
	font-size: 14px;
}
```
所有`section`孙子元素`a`都会被选中，至于`a`的父元素是什么，没有关系。

### ID和类选择符

#### ID 属性
用于页内导航ID
```
<a href="#first">First title</a>

<h2 id="first">First title</h2>
```
`href`属性以`#`开头，`#first`就是一个页面的锚点，会与使用了CSS选择符里面相同的`id="first"`元素做匹配，就会把页面定位到`h2#first`的位置，这也就是为什么CSS的`id`选择器在页面中只能使用一次。

### 属性选择符

#### 属性名选择符
`标签名[属性名]`

选择任何带有`属性名`的`标签名`

#### 属性值选择符
`标签名[属性名='属性值']`

**注**：在HTML5中，`属性值`的引号可以不加，但是推荐添加。

### 伪类
伪类这个叫法源自它们与类相似，但实际上没有类会附加到标记中的标签上。
+ **UI伪类**会在HTML元素处于某个状态时，为该元素应用CSS样式。
+ **结构化伪类**会在标记中存在某个结构上的关系时，为相应元素应用CSS样式。

#### UI伪类

##### 链接伪类
+ Link，未点击状态
+ Visited，点击过这个链接
+ Hover，鼠标悬停在这个链接上
+ Active，链接正在被点击（鼠标在元素上按下，还没有释放）

```
a:link { color: black; }
a:visited { color: gray; }
a:hover { text-decoration: none; }
a:active { color: red; }
```
按照`LoVe HA`的顺序使用它们，否则浏览器可能不会显示预期结果。
> 一个冒号（`:`）表示伪类，两个冒号（`::`）表示CSS3新增伪类。

##### :focus 伪类
`e:focus`

> 在后续例子中，e表示任何元素，不再以文字表示

获得焦点时，`:focus`伪类生效
```
input:focus { border: 1px solid blue; }
```

##### :target 伪类
`e:target`

如果用户点击一个指向页面中其他元素的链接，则那个元素就是**target**，可以用`:target `伪类选中它。

```
<a href="#more_info">More Information</a>

<h2 id="more_info">This is the information you are looking for.</h2>

// CSS
#more_info:target { background: #eee }
```
当用户点击链接转向**ID**为`more_info`的元素时，为该元素添加`#eee`背景。

#### 结构化伪类

##### :first-child 和 :last-child
`e:first-child`
`e:last-child`

`:first-child`代表一组同胞元素中的第一个元素，而`:last-child`代表最后一个。
```
// css
ol.results li:first-child {
  color:blue;
}

// html
<ol class="results">
  <li>My Fast Pony</li>
  <li>Steady Trotter</li>
  <li>Slow Ol' Nag</li>
</ol>
```
第一个`<li>`就会被选中，**My Fast Pony**就会变成蓝色。

##### :nth-child
`e:nth-child(n)`

> n 表示一个数值（也可以是 odd 或 even)
```
li:nth-child(3) {...}
```

### 伪元素
伪元素就是文档中若有实无的元素。

#### ::first-letter 伪元素
`e::first-letter`

```
p::first-letter {
  font-size: 300%;
}
```
上面的CSS样式就可以得到段落首字母放大的效果。

#### ::first-line 伪元素
`e::first-line`

可以选中文本段落的第一行。

#### ::before 和 ::after 伪元素
`e::before`
`e::after`

可用于在特定元素前面或后面添加特殊内容。
```
<p class="age">25</p>

// css
p.age::before {
  content: 'Age: ';
}
p.age::after {
  content: ' years.';
}
```
就能得到结果：
`Age: 25 years.`

### 层叠

#### 计算特指度
下面具体讲讲怎么计算选择符的特指度。首先，有一个简单的计分规则，即每个选择符都要按下面的“**ICE**”公式计算三个值：
`I - C - E` (ID Class Element)

针对这个公式的计分方法如下：
1. 选择符中有一个ID，就在**I**的位置上加1
2. 选择符中有一个类，就在**C**的位置上加1
3. 选择符中有一个元素名，就在**E**的位置上加1
4. 得到一个三位数

```
p                                    0-0-1 特指度=1
p.largetext                          0-1-1 特指度=11
p#largetext                          1-0-1 特指度=101
body p#largetext                     1-0-2 特指度=102
body p#largetext ul.mylist           1-1-3 特指度=113
body p#largetext ul.mylist li        1-1-4 特指度=114
```

### 规则声明

#### 数字值

`em` 和 `ex` 都是字体大小的单位，但在CSS中，它们作为长度单位适用于任何元素。
+ `em`，它表示一种字体中字母**M**的宽度，因此大小取决于字体
+ `ex`，表示给定字体中字母**x**的高度，不包括字母上、下突出的部分


#### 颜色值

##### 颜色名
+ aqua    浅绿色
+ black   黑色
+ blue    蓝色
+ fuchsia 紫红色
+ gray    灰色
+ green   绿色
+ lime    黄绿色
+ maroon  褐红色
+ navy    深蓝色
+ olive   茶青色
+ purple  紫色
+ red     红色
+ sliver  银色
+ teal    青色
+ white   白色
+ yellow  黄色

以上就是**W3C**定义了16个颜色的关键字。

## 定位元素

### 盒子有多大

盒模型结论一：没有（就是没有设置`width`的）宽度的元素始终会扩展到填满其父元素的宽度为止。添加水平边框、内边距和外边距，
会导致内容宽度减少，减少量等于水平边框、内边距和外边距的和。

盒模型结论二：为设定了宽度的盒子添加边框、内边距和外边距，会导致盒子扩展得更宽。实际上，盒子的`width`属性设定的只是盒子内容区的宽度，
而非盒子要占据的水平宽度。

CSS3新增的**box-sizing**属性，强制转换盒子模型。


### 浮动与清除

#### 围住浮动元素的三种方法

+ 为父元素添加`overflow:hidden`
+ 同时浮动父元素`float: left/right`
+ 添加非浮动的清除元素或者给父元素添加`clearfix`类

```
// HTML
<section class="clearfix">
  <img src="images/rubber_duck.jpg">
  <p>It's fun to float.</p>
</section>
<footer>Here is the footer element...</footer>

// CSS
.clearfix:after {
  content: '.';
  display: block;
  height: 0;
  visibility:hidden;
  clear:both;
}
```


### 背景

#### 背景属性

CSS规定的与背景相关的属性：
+ `background-color`
+ `background-image`
+ `background-repeat`
+ `background-position`
+ `background-size`
+ `background-attachment`
+ `background`
+ `background-clip`
+ `background-origin`
+ `background-break`

#### 背景渐变

##### 渐变点

渐变点就是渐变方向上的点，可以在这些点上设定颜色和不透明度。
```
.gradient1 {
  background:linear-gradient(#64d1dd, #fff 50%, #64d1dd);
}
.gradient2 {
  background:linear-gradient(#e86a43 20%, #fff 50%, #e86a43 80%);
}
```

第一个例子在50%处包含一个渐变点。注意，开始和结束位置如果没有声明，则默认为0% 和 100%。

> 如果不是使用百分比或其他值声明渐变点的位置，则三种颜色会均匀分布于整个渐变，其实际位置是0%、50%和100%。

##### 放射性渐变

```
.gradient1 {
  background: -webkit-radial-gradient(#fff, #64d1dd, #70aa25);
}
.gradient2 {
  background: -webkit-radial-gradient(circle, #fff, #64d1dd, #e86a43);
}
.gradient3 {
  background: -webkit-radial-gradient(50px 30px, circle, #fff, #64d1dd, #4947ba);
}
```
三个三色放射性渐变。第一个是默认的填满图形渐变，第二个是圆形渐变，第三个是指定位置的圆形渐变

## 字体和文本

### 字体

网页中的字体的三个来源：
+ 用户机器中安装的字体
+ 保存在第三方网站上的字体
+ 保存在你的Web服务器上的字体，通过`@font-face`引入。

与字体样式相关的6个属性：
+ `font-family`
+ `font-size`
+ `font-style`
+ `font-weight`
+ `font-variant`
+ `font`

#### 字体族 `font-family`

通用的字体类：
+ `serif`，也就是衬线字体，在每个字符笔画的末尾会有一些装饰线
+ `sans-serif`，无衬线字体，字符笔画的末尾没有装饰线
+ `monospace`，等宽字体
+ `cursive`，草书体或者手写体
+ `fantasy`，不能归入其他类的字体

#### 字体大小 `font-size`

浏览器样式表在设定所有元素的字体大小时，使用的都是相对单位`em`。默认情况下，`1em`等于16像素，这也是`font-size`的基准大小。

##### 绝对字体大小

设定绝对字体大小时，也是可以使用关键字值，比如`x-small`、`medium`、`x-large`等等。

##### 相对字体大小

使用百分比、`em`或`rem`等设置字体大小。如果给某个元素设定了相对字体大小，则该元素的字体大小要相对于最近的"被设定过字体大小"的祖先元素来确定。

如果想使用`em`，但有需要设定具体的像素大小，可以通过把`body`的`font-size`设定为`62.5%`。这样就把基准大小从16像素改为10像素（16 * 62.5% = 10）。这样em与像素之间的换算关系就更加明显了，1em 等于 10像素。

##### 关于`rem`单位

CSS3新增了一个相对单位`rem`（root em）。与`em` 区别在于使用rem 为元素设定字体大小时，仍然是相对大小，但相对于的只是HTML根元素。

#### 字体样式 `font-style`

+ `italic` 斜体
+ `oblique` 倾斜体
+ `normal` 正体

#### 字体变化 `font-variant`

+ `small-caps`
+ `normal`

`small-caps`会导致所有小写英文字母变成小型大写字母（就是大小与小写字母相同的大写字母）。

#### 字体 `font`

`font`属性是一个简写形式，但是使用这个简写需要遵守两条规则，否则浏览器无法正确解释声明的值。
**规则一**：必须声明`font-size`和`font-family`的值。
**规则二**：所有值必须按如下顺序声明
1. `font-weight`、`font-style`和`font-variant`部分先后
2. 然后是`font-size`
3. 最后是`font-family`

### 文本属性

CSS的文本属性：

+ `line-height`
+ `letter-spacing`
+ `text-align`
+ `text-decoration`
+ `text-indent`
+ `text-transform`
+ `word-spacing`
+ `vertical-align`

#### 文本缩进 `text-indent`

`text-indent`属性设定行内盒子相对于包含元素的起点。默认情况下，这个起点就是包含元素的左上角。

**注**：有个继承问题，需要说明一下
这里有一个非常重要的问题，必须请读者注意：text-indent 是可以被子元素继承的。如果你在一个div 上设定了text-indent 属性，那么div 中的所有段落都会继承该缩进值。然而，与所有继承的CSS 值一样，这个缩进并不是祖先元素中设定的值，而是计算的值。下面举一个例子说明。
假设有一个400 像素宽的div，包含的文本缩进5%，则缩进的距离是20 像素（400 的5%）。在这个div 中有一个200 像素宽的段落。作为子元素，它继承父元素的text-indent 值，所以它包含的文本也缩进。但继承的缩进是多少呢？不是5%，而是20 像素。也就是说，子元素继承的是根据父元素宽度计算得到的缩进值。结果，虽然段落只有父元素一半宽，但其中的文本也会缩进20 像素。这样可以确保无论段落多宽，它们的缩进距离都一样。当然在子元素上重新设定text-indent 属性，可以覆盖继承的值。

#### 字符间距 `letter-spacing`

`letter-spacing`为正值时增大字符间距，为负值时缩小间距。

**注**：无论设定字体大小时使用的是什么单位，设定字符间距一定要用**相对单位**，以便字间距能随字体大小同比例变化。

#### 单词间距 `word-spacing`

单词间距与字符间距很相似，区别在于只调整单词间距，而不影响字符间距。

#### 文本装饰 `text-decoration`

+ `underline` 下划线
+ `overline` 上划线
+ `line-through` 中划线
+ `blink` 闪烁效果
+ `none`

#### 文本对齐 `text-align`

+ `left` 左对齐
+ `right` 右对齐
+ `center` 居中
+ `justify` 两端对齐

#### 行高 `line-height`

CSS通过`line-height`属性实现了印刷行业中常说的加铅条，铅条在活字排版时代用于在文本行之间增加间距。

#### 文本转换 `text-transform`

+ `none`
+ `uppercase` 大写
+ `lowercase` 小写
+ `capitalize` 单词首字母大写

`text-transform`属性用于转换元素中文本的大小写。

#### 垂直对齐 `vertical-align`

+ `sub`
+ `super`
+ `top`
+ `middle`
+ `bottom`
+ 任意长度值

`vertical-align` 以基线为参照上下移动文本，但这个属性只影响行内元素。如果你想在垂直方向上对齐块级元素，必须把其`display` 属性设定为`inline`。

## 页面布局

### 布局的基本概念

多栏布局有三种基本的实现方法：固定宽度、流动、弹性。

### 三栏-中栏流动布局

{% asset_img threecol.png 三栏布局 %}

```
<div id="main_wrapper">
  <header>

  </header>
  <div id="threecolwrap">
    <div id="twocolwrap">
      <nav>

      </nav>
      <article>

      </article>
    </div>
    <aside>

    </aside>
  </div>
  <footer>

  </footer>
</div>

// sass
* {
  margin: 0;
  padding: 0;
}
body {
  font: 1em helvetica, arial, sans-serif;
}

div#main_wrapper {
  min-width: 600px;
  max-width: 1100px;
  margin: 0 auto;
  header {
    padding: 5px 10px;
    background: #3f7ccf;
  }
  div#threecolwrap {
    float: left;
    width: 100%;
    div#twocolwrap {
      float: left;
      width: 100%;
      margin-right: -210px;
      nav {
        float: left;
        width: 150px;
        background: #f00;
        padding: 20px 0;
        & > * {
          margin: 0 10px;
        }
      }
      article {
        width: auto;
        margin-left: 150px;
        margin-right: 210px;
        background: #eee;
        padding: 20px 0;
        & > * {
          margin: 0 20px;
        }
      }
    }
    aside {
      float: left;
      width: 210px;
      background: #ffed53;
      padding: 20px 0;
      & > * {
        margin: 0 10px;
      }
    }
  }
  footer {
    clear: both;
    width: 100%;
    text-align: center;
    background: #000;
  }
}
```

## 界面组件

### 弹出层

#### 堆叠上下文和 z-index

`z-index`值较大的元素，在堆叠层次中位于`z-index`值较小的元素上方。`z-index`属性值可以是0到任意大的数值，默认值是`auto`即为0。

不过`z-index`只对脱离了普通流的元素才有效，也就是`position`为`absolute`、`relative`或者`fixed`定位才可以。

## CSS3实战

### 页眉

+ 盒阴影 `box-shadow`

`box-shadow: 4px 4px 5px 8px #aaa inset;`

`box-shadow`属性的这几个值分别代表：水平偏移量、垂直偏移量、模糊量、扩展量、颜色和阴影位置（默认边框外部，即`outset`）。

+ 水平居中

**注**：不能给`display: inline-block`元素设定外边距为`auto`，所以无法通过`margin: 0 auto`实现居中。只能通过为父元素添加`text-align: center`实现居中。

个人理解`display: inline-block`元素就是行内元素，不能像块状元素那样定位。

+ 垂直居中

如果想要在一个固定高度的元素垂直居中一行文本，可以把这一行文本的`line-height`设定为该元素的高度。
```
text-align: center; /* 水平居中 */
line-height: xx px; /* 垂直居中： 行高=容器高度 */
```

如果垂直居中其他元素，比如图片，可以将容器的`display: table-row`，再设定(只对单元格有效)`vertical-align: middle`。
```
display: table-cell; /* 使用表格的行为 */
vertical-align: middle; /* 垂直居中 */
text-align: center; /* 水平居中 */
```

### 专题区

+ 文本阴影 `text-shadow`

`text-shadow: 4px 4px 5px #aaa;`

`text-shadow` 这几值的含义按顺序分别是：水平偏移量、垂直偏移量、模糊量和颜色。与盒阴影不同的是，文本阴影没有扩展量。

### 图书区

#### CSS3变换
`transform`属性能够调用函数，调用不同的变换函数可以实现不同的形式的变换，而通过传入的参数可以控制变换的结果。

`transform: 函数名()`
+ `scale`：用于放大或者缩小元素
+ `rotate`：根据指定的度数旋转元素
+ `skew`：让元素在X轴和Y轴方向倾斜
+ `translate`：根据指定的距离沿X轴和Y轴平移对象

## 响应式设计

### 媒体查询

媒体类型
+ all：匹配所有设备
+ handled：匹配手持设备
+ print：匹配分页媒体或打印预览模式下的屏幕
+ screen：匹配彩色计算机屏幕

媒体特性
+ `min-device-width` 和 `max-device-width`：匹配设备屏幕的尺寸
+ `min-width` 和 `max-width`：匹配视口的宽度，例如浏览器窗口宽度
+ `orientation`：匹配设备是横屏还是竖屏。

+ `<link>`标签的`media`属性
通过在`<link>`标签的`media`属性中指定条件，有选择的加载样式表。
```
<link rel="stylesheet" media="print" href="css/print_styles.css" />
<link rel="stylesheet" media="screen and (max-width:568px)" href="css/ipone_styles.css" />
```

## 附录

### 条件注释

小于等于**IE8**加载
```
<!--[if lte IE 8]> <!-- IE 条件注释 -->
<link src="ie_only.css" rel="stylesheet" />
<![endif]-->
```
