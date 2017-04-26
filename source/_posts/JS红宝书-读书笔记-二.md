---
title: JS红宝书-读书笔记(二)
date: 2017-04-26 23:31:25
tags: JavaScript 笔记
---

以下是“JS红宝书”，读书笔记的第二篇。

<!-- more -->

### BOM
---

抛开全局变量会成为`window`对象的属性不谈，定义全局变量与在`window`对象上直接定义属性还是有一点差别：**全局变量不能通过`delete`操作符删除，而直接定义在`window`对象上的定义的属性可以**。

#### 窗口

##### 窗口位置

获得窗口左边和上边的位置。
```
var leftPos = (typeof window.screenLeft == 'number') ? window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == 'number') ? window.screenTop : window.screenY;
```
Firefox支持`screenX`和`screenY`，其他浏览器均支持`screenLeft`、`screenTop`。

但是还是需要注意一个问题：在`IE` `Opera`中，`screenLeft  screenTop` 保存的的是可见区域的距离，也就是我们浏览器中不包含工具栏的区域与屏幕的距离；在`Chrome`、`Firefox`和`Safari`中`screenY`或`screenTop`返回的是整个浏览器窗口相对于屏幕坐标的值。

##### 窗口大小
IE9+、Firefox、Safari、Opera和Chrome均提供了4个属性`innerWidth`、`innerHeight`、`outerWidth`和`outerHeight`。

+ IE9+、Safari和Firefox中，`outerWidth`和`outerHeight`返回浏览器窗口本身的尺寸，而`innerWidth`和`innerHeight`则表示该容器中页面视图区的大小（减去边框宽度）
+ Chrome中，`inner*`和`outer*`返回相同的值，即视口大小而非浏览器窗口的大小。
+ 在IE、Firefox、Safari、Opera和Chrome中，都能通过`document.documentElement.clientWidth`和`document.documentElement.clientHeight`中保存了页面视口信息。

获取页面视口大小
```
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

+ `resizeTo()`接受浏览器窗口的新宽度和新高度
+ `resizeBy()`接受新窗口与原窗口的宽度和高度差。

这两个方法可能被浏览器禁用。

##### 窗口导航
如果是浏览器内置的屏蔽程序组织的弹出窗口，那么`window.open()`很可能会返回`null`。
```
var newWindow = window.open('https://www.google.com.hk', '_blank');
if (newWindow == null) {
	console.log('The popup was blocked!');
}
```
如果是浏览器扩展或其他程序组织的弹出窗口，那么`window.open()`通常会抛出一个错误。
```
var blocked = false;

try {
	var newWindow = window.open('https://www.google.com.hk', '_blank');
	if (newWindow == null) {
		blocked = true;
	}
} catch (ex) {
	blocked = true;
}

if (blocked) {
	console.log('The popup was blocked');
}
```

#### location 对象
`location`对象的属性
+ `hash`
+ `host`
+ `hostname`：与`host`不同的是，不带端口号
+ `href`
+ `pathname`: 返回URL中的目录和（或）文件名
+ `port`
+ `protocol`
+ `search`：返回URL的查询字符串，这个字符串`?`开头

#### navigator 对象
`location`对象的属性
+ `appCodeName`: 浏览器的名称，通常都是Mozilla
+ `appMinorVersion`：此版本信息
+ `appName`: 完整的浏览器名称
+ `appVersion`：浏览器的版本
+ `buildID`：浏览器编译版本
+ `cookieEnabled`：表示`cookie`是否可用
+ `cpuClass`：客户端计算机中使用的CPU类型
+ `javaEnabled()`：表示当前浏览器中是否启用了java
+ `language`: 浏览器的主语言
+ `mimeTypes`：浏览器中注册的MIME类型数组
+ `onLine`：表示浏览器是都连接到因特网
+ `oscpu`：客户端计算机的操作系统或使用的CPU
+ `platform`：浏览器所在的系统平台
+ `plugins`：浏览器中安装的插件信息的数组
+ `preference()`：设置用户的首选项
+ `systemLanguage`：操作系统的语言
+ `userAgent`：浏览器的用户代理字符串


### DOM
---

#### Node类型

每个节点都有一个`nodeType`属性，用于表明节点的类型。
+ `Node.ELEMENT_NODE(1)`
+ `Node.ATTRIBUTE_NODE(2)`
+ `Node.TEXT_NODE(3)`
+ `Node.CDATA_SECTION_NODE(4)`
+ `Node.ENTITY_REFERENCE_NODE(5)`
+ `Node.ENTITY_NODE(6)`
+ `Node.PROCESSING_INSTRUCTION_NODE(7)`
+ `Node.COMMENT_NODE(8)`
+ `Node.DOCUMENT_NODE(9)`
+ `Node.DOCUMENT_TYPE_NODE(10)`
+ `Node.DOCUMENT_FRAGMENT_NODE(11)`
+ `Node.NOTATION_NODE(12)`

为了确保跨浏览器兼容，将`nodeType`属性与数字值进行比较：
```
if (someNode.nodeType == 1) {
	console.log('Node is an element');
}
```

+ `nodeName`属性
```
if (someNode.nodeType == 1) {
	var value = someNode.nodeName;  // nodeName的值是元素的标签名
}
```

##### 节点关系

+ `childNodes`属性

每个节点都有一个`childNodes`属性，其中保存着一个`NodeList`对象，该对象是一种类数组对象。

+ `parentNode`属性

每个节点都有一个`parentNode`属性，该属性指向文档树中的父节点。包含在`childNodes`列表中的每个节点相互都是兄弟节点。使用`previousSibling`和`nextSibling`属性，可以访问其他兄弟节点。

**注**：列表中第一个节点的`previousSibling`属性值为`null`，同理列表中最后一个节点的`nextSibling`属性也是`null`。父节点的`firstChild`和`lastChild`属性分别指向其`childNodes`列表中的第一个和最后一个节点。如果不存在则为`null`。

`hasChildNodes()`方法在节点包含一个或多个子节点的情况下返回`true`，比查询`childNodes.length`更简便。

最后一个属性`ownerDocument`，该属性指向表示整个文档的文档节点（root），直接返回根节点不需要一层层向上回溯。

##### 操作节点

+ `appendChild()`

用于向`childNodes`列表的末尾添加一个节点。
```
var returnedNode = someNode.appendChild(newNode);
alert(returnedNode == newNode); //true
alert(someNode.lastChild == newNode); //true
```
任何DOM节点不可能同时出现在多个位置。
```
//someNode 有多个子节点
var returnedNode = someNode.appendChild(someNode.firstChild);
alert(returnedNode == someNode.firstChild); //false
alert(returnedNode == someNode.lastChild); //true
```

+ `insertBefore()`

把节点放在指定位置，该方法接受两个参数：要插入的节点和作为参考的节点。插入节点后，被插入的节点会变成参照节点的前一个兄弟节点。参照节点是`null`的话，`insertBefore`与`appendChild`执行相同的操作，都插入列表末尾。
```
//插入后成为最后一个子节点
returnedNode = someNode.insertBefore(newNode, null);
alert(newNode == someNode.lastChild); //true

//插入后成为第一个子节点
var returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
alert(returnedNode == newNode); //true
alert(newNode == someNode.firstChild); //true

//插入到最后一个子节点前面
returnedNode = someNode.insertBefore(newNode, someNode.lastChild);
alert(newNode == someNode.childNodes[someNode.childNodes.length-2]); //true
```

+ `replaceChild()`

替换节点，接受两个参数：要插入的节点和要替换的节点。
```
//替换第一个子节点
var returnedNode = someNode.replaceChild(newNode, someNode.firstChild);

//替换最后一个子节点
returnedNode = someNode.replaceChild(newNode, someNode.lastChild);
```

+ `removeChild()`

移除节点，接受一个参数：要被移除的节点。
```
//移除第一个子节点
var formerFirstChild = someNode.removeChild(someNode.firstChild);

//移除最后一个子节点
var formerLastChild = someNode.removeChild(someNode.lastChild);
```

+ `cloneNode()`

复制节点，接受一个布尔值，表示是否深复制。复制后返回的节点没有父节点，可以通过插入等操作手动指定。
```
var deepList = myList.cloneNode(true);
alert(deepList.childNodes.length); //3（IE < 9）或7（其他浏览器）

var shallowList = myList.cloneNode(false);
alert(shallowList.childNodes.length); //0
```
**注**：`cloneNode`方法不会复制DOM节点的`js`属性。IE存在一个bug，它会复制事件处理程序。

+ `normalize()`

稍后讨论

以上方法的返回值，都是被操作的节点。

##### Document类型
`Document`节点具有下列特征：
+ `nodeType`的值为9
+ `nodeName`的值为`#document`
+ `nodeValue`的值为`null`
+ `parentNode`的值为`null`
+ `ownerDocument`的值为`null`
+ 其子节点可能是一个`DocumentType`（最多一个）、`Element`（最多一个）、`ProcessingInstruction`或`Comment`

```
// 通过`documentElement`属性访问<html>元素
var html = document.documentElement;
// 访问 <body> 元素
var body = document.body;
// <!DOCTYPE>
var doctype = document.doctype;
// <title>
var title = document.title;
// 完整 url
var url = document.URL;
// domain 域名
var domain = document.domain;
// 取得来源页面的URL(也就是导航到这页的页面)
var referrer = document.referrer;
```

查找元素的方法：
+ `document.getElementById()`
+ `document.getElementsByTagName()`
+ `document.getElementsByName()`

文档写入：
+ `document.write()`
+ `document.writeln()`在字符串尾加换行符(\n)
```
<script type="text/javascript">
	document.write("<script type=\"text/javascript\" src=\"file.js\">" + "<\/script>");
</script>
```

##### Element类型
`Element`类型提供了对元素标签名、子节点及特性的访问。
+ `nodeType`的值为1
+ `nodeName`的值为元素的标签名
+ `nodeValue`的值为`null`
+ `parentNode`可能是`Document`或`Element`
+ 其子节点可能是`Element`、`Text`、`Comment`、`ProcessingInstruction`、`CDATASection`或`EntityReference`

访问元素的标签名，可以使用`nodeName`属性，也可以使用`tagName`属性，后者更直观。
```
<div id="myDiv"></div>

var div = document.getElementById("myDiv");
alert(div.tagName); //"DIV"
alert(div.tagName == div.nodeName); //true
```
操作特性的方法：
+ `getAttribute()`
+ `setAttribute()`
+ `removeAttribute()`

`attributes`属性

`Element`类型是使用`attributes`属性的唯一一个DOM节点属性。`attributes`属性包含一个`NamedNodeMap`。元素的每一个特性都由一个`Attr`节点表示，每个节点都保存在`NamedNodeMap`对象中。

`NamedNodeMap`对象的方法：
+ `getNamedItem(name)`：返回`nodeName`属性等于`name`的节点
+ `removeNamedItem(name)`：从列表中移除`nodeName`属性等于`name`的节点
+ `setNamedItem(node)`：向列表中添加节点，以节点的`nodeName`属性为索引
+ `item(pos)`：返回位于数字`pos`位置处的节点

`attributes`属性中包含一系列节点，每个节点的`nodeName`就是特性的名称，而节点`nodeValue`就是特性的值。
```
var id = element.attributes.getNamedItem('id').nodeValue;
// 简写
var id = element.attributes['id'].nodeValue;
```

创建元素
`document.createElement()`方法可以创建新元素，这个方法接受一个参数（标签名）
```
var div = document.createElement('div');
```

##### Text类型
文本节点由`Text`类型表示，包含的是可以照字面解释的纯文本内容。纯文本中可以包含转义后的HTML字符，但不能包含HTML代码。
+ `nodeType`的值为3
+ `nodeName`的值为`#text`
+ `nodeValue`的值为节点所包含的文本
+ `parentNode`是一个`Element`

操作节点中的文本：
+ `appendData(text)`：将`text`添加到节点的末尾
+ `deleteData(offset, count)`：从`offset`指定的位置开始删除`count`个字符
+ `insertData(offset, text)`：在`offset`指定的位置插入`text`
+ `replaceData(offset, count, text)`：用`text`替换从`offset`指定的位置开始到`offset+count`为止的文本
+ `splitText(offset)`：从`offset`指定的位置将当前文本分成两个文本节点
+ `substringData(offset, count)`：提取从`offset`指定的位置开始到`offset+count`为止处的字符串。

在向DOM文档中插入文本之前，应该先对其进行`HTML`编码

创建文本节点
+ `document.createTextNode()`
```
var textNode = document.createTextNode("<strong>Hello</strong> world!");
```

#### DOM 操作技术

使用函数实现加载外部`JS`文件
```
function loadScript(url) {
	var script = document.createElement('script');
	script.type = 'text/javascript';
	script.src = url;
	document.body.appendChild(script);
}
loadScirpt('xx.js');
```
IE将`<script>`视为一个特殊的元素，不允许DOM访问其子节点。不过可以使用`<script>`元素的`text`属性指定`JS`代码。

##### 操作表格
```
// create table
var table = document.createElement('table');
table.border = 1;
table.width = '100%';

// create tbody
var tbody = document.createElement('tbody');
table.appendChild(tbody);

// create row1
var row1 = document.createElement('tr');
tbody.appendChild(row1);

var cell1_1 = document.createElement('td');
cell1_1.appendChild(document.createTextNode('Cell 1,1'));
row1.appendChild(cell1_1);

var cell2_1 = document.createElement('td');
cell2_1.appendChild(document.createTextNode('Cell 2,1'));
row1.appendChild(cell2_1);

// create row2
var row2 = document.createElement('tr');
tbody.appendChild(row2);

var cell1_2 = document.createElement('td');
cell1_2.appendChild(document.createTextNode('Cell 1,2'));
row1.appendChild(cell1_2);

var cell2_2 = document.createElement('td');
cell2_2.appendChild(document.createTextNode('Cell 2,2'));
row1.appendChild(cell2_2);

document.body.appendChild(table);
```

### DOM 扩展
---

#### 选择符 API
+ `querySelector()`方法

`querySelector()`方法接受一个**CSS**选择符，返回与该模式匹配的**第一个元素**，若没有，返回`null`。

可以通过`Document`类型调用，也可以通过`Element`类型调用，后者只会在该元素后代元素的范围内查找匹配的元素。

+ `querySelectorAll()`方法

`querySelectorAll()`方法返回的是所有匹配的元素，是一个`NodeList`实例。

+ `matchesSelector()`方法

为`Element`类型新增的一个方法，接受一个参数`CSS`选择符，如果调用元素与该选择符匹配，返回`true`，否则返回`false`。

#### 元素遍历

+ `childElementCount`：返回子元素（不包含文本节点和注释）的个数
+ `firstElementChild`：指向第一个子元素
+ `lastElementChild`：指向最后一个子元素
+ `previousElementSibling`：指向前一个兄弟元素
+ `nextElementSibling`：指向后一个兄弟元素

不同于前面的返回**节点**的方法。
```
// 节点版本
var i,
	len,
	child = element.firstChild;
while(child != element.lastChild){
	if (child.nodeType == 1){ //检查是不是元素
		processChild(child);
	}
	child = child.nextSibling;
}
// 元素版本
var i,
	len,
	child = element.firstElementChild;
while(child != element.lastElementChild){
	processChild(child); //已知其是元素
	child = child.nextElementSibling;
}
```

#### HTML5
+ `getElementsByClassName()`方法
+ `classList`属性，这个属性是新集合类型`DOMTokenList`的实例。
	+ `add(value)`
	+ `contains(value)`
	+ `remove(value)`
	+ `toggle(value)`
```
div.classList.remove("user");
```
+ 焦点管理

`document.activeElement`属性，始终会引用DOM中前端获得了焦点的元素。
```
var button = document.getElementById("myButton");
button.focus();
alert(document.activeElement === button); //true
```
`document.hasFocus()`方法，可以确定文档是否获得了焦点。
```
var button = document.getElementById("myButton");
button.focus();
alert(document.hasFocus()); //true
```

+ `HTMLDocument`的变化
	+ `readyState`属性有两个值，`loading`和`complete`
```
if (document.readyState == 'complete') {
	// 加载完成
}
```

+ `document.charset`字符集属性

+ `data-`自定义数据属性
```
<div id="myDiv" data-appId="12345" data-myname="Nicholas"></div>

var div = document.getElementById("myDiv");

//取得自定义属性的值
var appId = div.dataset.appId;
var myName = div.dataset.myname;

//设置值
div.dataset.appId = 23456;
div.dataset.myname = "Michael";
```

+ `innerHTML`属性

在读模式下，`innerHTML` 属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应
的HTML 标记。在写模式下，`innerHTML` 会根据指定的值创建新的DOM树，然后用这个DOM树完全
替换调用元素原先的所有子节点
+ `outerHTML`属性

在读模式下，`outerHTML` 返回调用它的元素及所有子节点的HTML 标签。在写模式下，`outerHTML`
会根据指定的HTML 字符串创建新的DOM 子树，然后用这个DOM子树完全替换调用元素。

+ `insertAdjacentHTML()`方法
插入元素的新增方法，接受两个参数，插入的位置和要插入的HTML文本，第一个参数的值：
	+ `'beforebegin'`
	+ `'afterbegin'`
	+ `'beforeend'`
	+ `'afterend'`
```
//作为前一个同辈元素插入
element.insertAdjacentHTML("beforebegin", "<p>Hello world!</p>");

//作为第一个子元素插入
element.insertAdjacentHTML("afterbegin", "<p>Hello world!</p>");

//作为最后一个子元素插入
element.insertAdjacentHTML("beforeend", "<p>Hello world!</p>");

//作为后一个同辈元素插入
element.insertAdjacentHTML("afterend", "<p>Hello world!</p>");
```

+ `scrollIntoView()`方法

`scrollIntoView`方法可以在所有HTML元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。如果这个方法传入`true`作为参数，或者不传参数，那么窗口滚动之后就会让调用元素的顶部与视口顶部 尽可能平齐，如果传入`false`，调用元素会尽可能全部出现在视口中，不过顶部不一定平齐。
```
// 让元素可见
document.form[0].scrollIntoView();
```

#### 专有扩展

+ 插入文本
	+ `innerText`属性

	+ `outerText`属性

+ 滚动
	+ `scrollIntoViewIfNeeded(alignCenter)`：只有在当前元素不可见的情况下，才滚动浏览器或窗口或容器元素最终让它可见。如果当前元素在视口中可见，这个方法什么也不做。
	+ `scrollByLines(lineCount)`：将元素的内容滚动指定的行高，`lineCount`值可以是正值，也可以是负值。
	+ `scrollByPages(pageCount)`：将元素的内容滚动指定的页面高度，具体高度由元素的高度决定。

`scrollIntoView()`和`scrollIntoViewIfNeeded()`的作用对象是元素的容器，而`scrollByLines()`和`scrollByPages()`影响的则是元素自身。
```
//在当前元素不可见的时候，让它进入浏览器的视口
document.images[0].scrollIntoViewIfNeeded();

//将页面主体往回滚动1 页
document.body.scrollByPages(-1);
```

### DOM2 和 DOM3
---

#### 元素大小

+ 偏移量
	+ `offsetHeight`：元素在垂直方向上占用的空间大小。包括元素的高度，（可见的）水平滚动条的高度，上边框高度和下边框高度
	+ `offsetWidth`：元素在水平方向上占用的空间大小。包括元素的宽度，（可见的）垂直滚动条的宽度，左边框宽度和右边框宽度
	+ `offsetLeft`：元素的左外边框至包含元素的左内边框之间的像素距离。
	+ `offsetTop`：元素的上外边框至包含元素的上内边框之间的像素距离。

{% asset_img offset-1.png %}

其中，`offsetLeft`和`offsetTop`属性与包含元素有关，包含元素的引用保存在`offsetParent`属性中。`offsetParent`属性不一定与`parentNode`的值相等。
```
// 元素上偏移
function getElementLeft (ele) {
	var actualLeft = ele.offsetLeft;
	var current = ele.offsetParent;

	while (current !== null) {
		actualLeft += current.offsetLeft;
		current = current.offsetParent;
	}
	return actualLeft;
}
// 元素左偏移同理
```
一般来说，页面中所有的元素都被包含在几个`<div>`元素中，而这些`<div>`元素的`offsetParent`又是`<body>`元素，所以`getElementLeft()`与`getElementTop()`会返回与`offsetLeft`和`offsetTop`相同的值。

+ 客户区大小
	+ `clientWidth`：元素内容区宽度加上左右内边距宽度
	+ `clientHeight`: 元素内容区高度加上上下内边距高度

{% asset_img offset-2.png %}

```
function getViewport(){
	if (document.compatMode == "BackCompat"){  // IE7之前
			return {
				width: document.body.clientWidth,
				height: document.body.clientHeight
			};
		} else {
			return {
				width: document.documentElement.clientWidth,
				height: document.documentElement.clientHeight
			};
		}
}
```

+ 滚动大小
**滚动大小**，指的是包含滚动内容的元素的大小。有些元素（`<html>`），即使没有执行任何代码也能自动添加滚动条；但另外一些元素，则需要通过**CSS**的`overflow`属性设置才能滚动。
	+ `scrollHeight`：在没有滚动条的情况下，元素内容的总高度
	+ `scrollWidth`：在没有滚动条的情况下，元素内容的总宽度
	+ `scrollLeft`：被隐藏在内容区域左侧的像素数，通过设置这个属性可以改变元素的滚动位置
	+ `scrollTop`：被隐藏在内容区域上方的像素数，通过设置这个属性可以改变元素的滚动位置

{% asset_img scroll-1.png %}

`scrollWidth`和`scrollHeight`主要用于确定元素内容的实际大小。

`scrollWidth` 和`scrollHeight` 与`clientWidth` 和`clientHeight` 之间的关系？
+ **Firefox**中这两组属性始终相等，但大小代表的是文档内容区域的实际尺寸，非视口尺寸
+ **Opera Safari Chrome**中这两组属性有区别，其中`scrollWidth` 和`scrollHeight` 等于视口大小，而`clientWidth` 和`clientHeight` 等于文档内容区域的大小。
+ **IE（在标准模式）**中的这两组属性不相等，其中`scrollWidth` 和`scrollHeight` 等于文档内容区域的大小，而`clientWidth` 和`clientHeight` 等于视口大小。

通过`scrollLeft` 和`scrollTop` 属性既可以确定元素当前滚动的状态，也可以设置元素的滚动位置。在元素尚未被滚动时，这两个属性的值都等于0。如果元素被垂直滚动了，那么`scrollTop` 的值会大于0，且表示元素上方不可见内容的像素高度。如果元素被水平滚动了，那么`scrollLeft` 的值会大于0，且表示元素左侧不可见内容的像素宽度。这两个属性都是可以设置的，因此将元素的`scrollLeft` 和`scrollTop` 设置为0，就可以重置元素的滚动位置。下面这个函数会检测元素是否位于顶部，如果不是就将其回滚到顶部。
```
function scrollToTop(element){
	if (element.scrollTop != 0){
		element.scrollTop = 0;
	}
}
```

+ 确定元素大小
	+ `getBoundingClientRect()`方法，会返回一个矩形对象，包含`left top right bottom`四个属性。这些属性给出了元素在页面中相对于视口的位置。

#### 遍历

##### NodeIterator
可以使用`document.createNodeIterator()`方法创建它的新实例，接受4个参数。
+ `root`：想要作为搜索起点的树中的节点
+ `whatToShow`：表示要访问哪些节点的数字代码
+ `filter`：是一个`NodeFilter`对象，或者一个表示应该接受还是拒绝某种特定节点的函数
+ `entityReferenceExpansion`：布尔值，表示是否要扩展实体引用。

`whatToShow`这个参数的值以常量形式在`NodeFilter`类型中定义：
+ `NodeFilter.SHOW_ALL`
+ `NodeFilter.SHOW_ELEMENT`
+ `NodeFilter.SHOW_ATTRIBUTE`
+ `NodeFilter.SHOW_TEXT`
+ `NodeFilter.SHOW_CDATA_SECTION`
+ `NodeFilter.SHOW_ENTITY_REFERENCE`
+ `NodeFilter.SHOW_ENTITYE`
+ `NodeFilter.SHOW_PROCESSING_INSTRUCTION`
+ `NodeFilter.SHOW_COMMENT`
+ `NodeFilter.SHOW_DOCUMENT`
+ `NodeFilter.SHOW_DOCUMENT_TYPE`
+ `NodeFilter.SHOW_DOCUMENT_FRAGMENT`
+ `NodeFilter.SHOW_NOTATION`

### 事件
---

#### 事件流

+ 事件冒泡

IE的事件流叫做**事件冒泡**，即事件开始时由最具体的元素接受，然后逐级向上传播到较为不具体的节点。

+ 事件捕获

Netscape 团队提出的事件流叫做**事件捕获**，事件捕获的用意在于在事件到达预定目标之前捕获它。

+ DOM事件流

“DOM2级事件”规定的事件流包括三个阶段：`事件捕获阶段`、`处于目标阶段`和`事件冒泡阶段`。

#### 事件处理程序

+ DOM0 级事件处理程序

每个元素（包括`window`和`document`）都有自己的事件处理程序，这些属性通常全部小写。
```
var btn = document.getElementById('myBtn');
btn.onclick = function () {
	console.log('clicked');
}
```
DOM 0级方法指定的事件处理程序被认为是元素的方法，因此，这个时候的事件处理程序是在元素的作用域中运行，也就是说程序中的`this`可以引用当前元素。
```
var btn = document.getElementById('myBtn');
btn.onclick = function () {
	console.log(this.id);  // 'myBtn'
}
```
以这种方式添加的事件处理程序会在事件流的**冒泡阶段**被处理。

+ DOM2 级事件处理程序
	+ `addEventListener()`
	+ `removeEventListener()`

定义了两个方法用于处理指定和删除事件处理程序的操作。所有的DOM节点中都包含这两个方法，接受三个参数：**事件名**、**事件处理程序**和**布尔值**。最后这个布尔值如果是`true`，表示在捕获阶段调用事件处理程序；`false`表示在冒泡阶段调用事件处理程序，默认是`false`。

通过`addEventListener()`添加的事件处理程序只能使用`removeEventListener()`来移除。如果通过`addEventListener()`添加的匿名函数将无法移除。
```
btn.addEventListener('click', function () {  //匿名函数
	...
})
```

**注**：大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段（`false`），这样可以最大限度地兼容各种浏览器。最好只在需要在事件到达目标之前截获它的时候将事件处理程序添加到捕获阶段。如果不是特别需要，我们不建议在事件捕获阶段注册事件处理程序。

+ IE事件处理程序
	+ `attachEvent()`
	+ `detachEvent()`

这两个方法接受两个参数：事件名（带`on`）和事件处理函数。
```
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function(){
	alert("Clicked");
});
```
**注**：在IE 中使用`attachEvent()`与使用DOM0 级方法的主要区别在于事件处理程序的作用域。
+ DOM0 级作用域是其所属元素
+ `attachEvent()`方法的作用域是全局（`this === window`）
```
var btn = document.getElementById("myBtn");
btn.attachEvent("onclick", function(){
	alert("Clicked");
});
btn.attachEvent("onclick", function(){
	alert("Hello world!");
});
```
**注**：与DOM方法不同的是，这些事件处理程序不是以添加它们的顺序执行，而是以相反的顺序被触发。单击这个例子中的按钮，首先看到的是"Hello world!"，然后才是"Clicked"。

#### 事件对象
在触发DOM上的某个事件时，会产生一个事件对象`event`。

+ DOM中的事件对象

`event`对象成员
| 属性/方法 | 类型 | 读/写 | 说明 |
| : - : | : -- : | : -- : | : -- : | : -- : |
| `bubbles` | `Boolean` | 只读 | 表明事件是否冒泡 |
| `cancelable` | `Boolean` | 只读 | 表明是否可以取消事件的默认行为 |
| `currentTarget` | `Element` | 只读 | 其事件处理程序当前正在处理事件的那个元素 |
| `defaultPrevented` | `Boolean` | 只读 | 为`true`表示已经调用`preventDefault()` |
| `detail` | `Integer` | 只读 | 与事件相关的细节信息 |
| `eventPhase` | `Integer` | 只读 | 调用事件处理程序的阶段：1 捕获，2 处于目标，3 冒泡 |
| `preventDefault()` | `Function` | 只读 | 取消事件的默认行为。如果`cancelable` 是`true`，则可以使用这个方法 |
| `stopImmediatePropagation()` | `Function` | 只读 | 取消事件的进一步冒泡或捕获，同时阻止任何事件处理程序被调用 |
| `stopPropagation()` | `Function` | 只读 | 取消事件的进一步捕获或冒泡。如果`bubbles`为`true`，则可以使用这个方法 |
| `target` | `Element` | 只读 | 事件的目标 |
| `trusted` | `Boolean` | 只读 | 为`true`表示事件是浏览器生成，`false`是开发人员创建 |
| `type` | `String` | 只读 | 被触发的事件类型 |
| `view` | `AbstractView` | 只读 | 与事件关联的抽象视图。等同于发生事件的`window`对象 |

在事件处理程序内部，对象`this` 始终等于`currentTarget` 的值，而`target` 则只包含事件的实
际目标。如果直接将事件处理程序指定给了目标元素，则`this`、`currentTarget` 和`target` 包含相同
的值。
```
document.body.onclick = function(event){
	alert(event.currentTarget === document.body); //true
	alert(this === document.body); //true
	alert(event.target === document.getElementById("myBtn")); //true
};
```
调用`event`方法
```
var link = document.getElementById("myLink");
link.onclick = function(event){
	event.preventDefault();
};
```

+ 跨浏览器的事件对象
```
var EventUtil = {
	addHandler: function(element, type, handler){
		//省略的代码
	},
	getEvent: function(event){
		return event ? event : window.event;
	},
	getTarget: function(event){
		return event.target || event.srcElement;
	},
	preventDefault: function(event){
		if (event.preventDefault){
			event.preventDefault();
		} else {
			event.returnValue = false;
		}
	},
	removeHandler: function(element, type, handler){
		//省略的代码
	},
	stopPropagation: function(event){
		if (event.stopPropagation){
			event.stopPropagation();
		} else {
			event.cancelBubble = true;
		}
	}
};
```

#### 事件类型

+ UI 事件
	+ `load`事件
	+ `unload`事件
	+ `resize`事件
	+ `scroll`事件
+ 焦点事件
	+ `blur`事件：失去焦点
	+ `focus`事件：获得焦点
+ 鼠标与滚动事件
	+ `click`事件
	+ `dbclick`事件
	+ `mousedown`事件：按下鼠标
	+ `mouseenter`事件：光标移入
	+ `mouseleave`事件：光标移出
	+ `mousemove`事件：鼠标在元素内部移动重复触发
	+ `mouseout`事件：在鼠标指针位于一个元素上方，然后用户将其移入另一个元素时触发。又移入的另
一个元素可能位于前一个元素的外部，也可能是这个元素的子元素
	+ `mouseover`事件：在鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触
发
	+ `mouseup`事件：释放鼠标按钮时触发

页面上的所有元素都支持鼠标事件。除了`mouseenter` 和`mouseleave`，所有鼠标事件都会冒泡，也可以被取消，而取消鼠标事件将会影响浏览器的默认行为。

只有在同一个元素上相继触发`mousedown` 和`mouseup` 事件，才会触发`click` 事件；如果`mousedown` 或`mouseup` 中的一个被取消，就不会触发`click` 事件。

**触摸设备**
iOS和Android设备的相关事件：
1. 不支持`dbclick`事件。双击浏览器窗口会放大画面
2. 轻击可单击元素会触发`mousemove`事件。。如果此操作会导致内容变化，将不再有其他事件发生；如果屏幕没有因此变化，那么会依次发生`mousedown`、`mouseup` 和`click` 事件。轻击不可单击的元素不会触发任何事件。可单击的元素是指那些单击可产生默认操作的元素（如链接），或者那些已经被指定了`onclick` 事件处理程序的元素。
3. `mousemove`事件也会触发`mouseover`和`mouseout`事件
4. 两个手指放在屏幕上且页面随手指移动而滚动时会触发`mousewheel`和`scroll`事件。


+ HTML5事件
	+ `contextmenu`事件
	+ `beforeunload`事件
	+ `DOMContentLoaded`事件
	+ `readystatechange`事件
		+ `uninitialized`未初始化
		+ `loading`
		+ `loaded`
		+ `interactive`:可以操作对象，但还没有完全加载
		+ `complete`
	+ `hashchange`事件

+ 设备事件
	+ `orientationchange`事件：横竖屏，有三个值： -90 ，0， 90

+ 触摸与手势事件
	+ 触摸事件
		+ `touchstart`
		+ `touchmove`
		+ `touchend`
		+ `touchcancel`
	+ 手势事件
		+ `gesturestart`
		+ `gesturechange`
		+ `gestureend`


#### 内存和性能
+ 事件委托

例如在`<ul>`为添加一个`click`事件，所有`<li>`子元素点击事件都会冒泡到`<ul>`上。

+ 移除事件处理程序

### 表单脚本
---

#### 表单基础
##### 提交表单
```
<input type="submit" value="Submit Form">
```

##### 重置表单
```
<input type="reset" value="Reset Form">
```

##### 表单字段
每个表单都有`elements`属性，该属性是表单中所有表单元素的集合。
```
var form = document.getElementById("form1");
//取得表单中的第一个字段
var field1 = form.elements[0];
//取得名为"textbox1"的字段
var field2 = form.elements["textbox1"];
//取得表单中包含的字段的数量
var fieldCount = form.elements.length;
```

#### 文本框脚本
##### 过滤输入
屏蔽特定的字符，需要检测`keypress`事件对应的字符编码。
```
EventUtil.addHandler(textbox, 'keypress', function (event) {
	event = EventUtil.getEvent(event);
	var target = EventUtil.getTarget(event);
	var charCode = EventUtil.getCharCode(event);

	if (!/\d/.test(String.fromCharCode(charCode))) {
		EventUtil.preventDefault(event);
	}
})
```

##### HTML5约束验证API
+ 输入模式

HTML5为文本字段新增了`pattern`属性。
```
<input type="text" pattern="\d+" name="count">
```

+ 检测有效性

使用`checkValidity()`方法可以检测表单中的某个字段是否有效。是否有效的判断依据是一些`<input>`的约束条件。
```
if (document.forms[0].elements[0].checkValidity()){
	//字段有效，继续
} else {
	//字段无效
}
```
也可以检测整个表单是否有效
```
if(document.forms[0].checkValidity()){
	//表单有效，继续
} else {
	//表单无效
}
```

+ 禁用验证
```
<form method="post" action="signup.php" novalidate>
	<!--这里插入表单元素-->
</form>
```

### HTML5 脚本编程
---

#### 跨文档消息传递
**跨文档消息传送**（cross-document messaging）简称XDM。其核心方法是`postMessage()`方法。

`postMessage()`方法接受两个参数：一条消息和一个表示消息接收方来自哪个域的字符串。
```
// 注意：所有支持XDM的浏览器也支持iframe的`contentWindow`属性
var iframeWindow = document.getElementById('myframe').contentWindow;
iframeWindow.postMessage('A secret', 'https://yeasoenzhang.github.io');
```
尝试向内嵌框架中发送一条消息，并指定框架中的文档必须来源于`https://yeasonzhang.github.io`域。

接收到**XDM**消息时，会触发`window`对象的`message`事件，这个事件是以异步形式触发。
传递的`onmessage`处理程序的事件对象包含三个重要信息：
+ `data`：作为`postMessage()`第一个参数传入的字符串数据
+ `origin`：发送消息的文档所在的域。
+ `source`：发送消息的文档的`window`对象的代理。

```
EventUtil.addHandler(window, "message", function(event){
	//确保发送消息的域是已知的域
	if (event.origin == "https://yeasonzhang.github.io"){
		//处理接收到的数据
		processMessage(event.data);
		//可选：向来源窗口发送回执
		event.source.postMessage("Received!", "http://p2p.wrox.com");
	}
});
```
XDM 还有一些怪异之处。首先，`postMessage()`的第一个参数最早是作为“永远都是字符串”来实现的。但后来这个参数的定义改了，改成允许传入任何数据结构。可是，并非所有浏览器都实现了这一变化。为保险起见，使用`postMessage()`时，最好还是只传字符串。如果你想传入结构化的数据，最佳选择是先在要传入的数据上调用`JSON.stringify()`，通过`postMessage()`传入得到的字符串，然后再在`onmessage` 事件处理程序中调用`JSON.parse()`。

#### 原生拖放

##### 拖放事件

拖动某个元素时，将依次触发的事件：
+ `dragstart`
+ `drag`
+ `dragend`

当某个元素被拖动到一个有效的放置目标时，会依次触发下列事件：
+ `dragenter`
+ `dragover`
+ `dragleave`（离开）或`drag`（放进去了）

##### dataTransfer对象

`dataTransfer`对象，它是事件对象的一个属性，用于被拖动元素向放置目标传递**字符串格式**的数据。该对象有两个主要方法:
+ `getData()`
+ `setData()`
```
//设置和接收文本数据
event.dataTransfer.setData("text", "some text");
var text = event.dataTransfer.getData("text");

//设置和接收URL
event.dataTransfer.setData("URL", "http://www.wrox.com/");
var url = event.dataTransfer.getData("URL");
```
不过，保存在`dataTransfer`对象中的数据只能在`drap`事件处理程序中读取。如果在`ondrop` 处理程序中没有读到数据，那就是`dataTransfer` 对象已经被销毁，数据也丢失了。

##### drapEffect 与 effectAllowed

`dateTransfer`对象有两个属性：
+ `dropEffect`
+ `effectAllowed`

`dropEffect`，属性可以知道被拖动的元素能够执行那种放置行为。
+ `none`：不能放在这里
+ `move`：应该把拖放的元素移动到放置目标
+ `copy`：应该把拖动的元素复制到放置目标
+ `link`：表示放置目标会打开拖动的元素

要使用`dropEffect`属性，必须在`ondragenter`事件处理程序中针对放置目标来设置。

`effectAllowed`属性表示允许拖动元素的哪种`dropEffect`。
+ `uninitialized`：没有给被拖动的元素放置任何放置行为
+ `none`：被拖动的元素不能有任何行为
+ `copy`：只允许值为`copy`的`dropEffect`
+ `link`：只允许值为`link`的`dropEffect`
+ `move`：只允许值为`move`的`dropEffect`
+ `copyLink`：允许值为`copy`和`link`的`dropEffect`
+ `copyMove`：允许值为`copy`和`move`的`dropEffect`
+ `linkMove`：允许值为`link`和`move`的`dropEffect`
+ `all`: 允许任意`dropEffect`

必须在`ondragstart` 事件处理程序中设置`effectAllowed` 属性。

##### 可拖动
HTML5为所有元素规定了`draggable`属性，表示元素是否可以拖动。只有图像和链接的`draggable`默认是`true`
```
<!-- 让这个图像不可以拖动 -->
<img src="smile.gif" draggable="false" alt="Smiley face">
<!-- 让这个元素可以拖动 -->
<div draggable="true">...</div>
```

##### 其他成员
HTML5规定了`dateTransfer`对象还应该包含下列方法和属性。
+ `addElement(element)`
+ `clearData(format)`
+ `setDragImage(element, x, y)`
+ `type`
