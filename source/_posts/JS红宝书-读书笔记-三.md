---
title: JS红宝书-读书笔记(三)
date: 2017-04-27 00:23:52
tags: JavaScript 笔记
---

以下是“JS红宝书”，读书笔记的第三篇。

<!-- more -->

### 错误处理与调试
---

#### 错误处理

##### try-catch 语句
```
try {
	// 可能出错的代码
} catch (err) {
	// 处理发生的错误
}
```
+ `finally`子句

只要代码中包含`finially`子句，无论`try`还是`catch`语句中的`return`语句都将被忽略。

+ 错误类型
	+ `Error`
	+ `EvalError`
	+ `RangeError`
	+ `ReferenceError`
	+ `SyntaxError`
	+ `TypeError`
	+ `URIError`
```
try {
	someFunction();
} catch (error) {
	if (error instanceof TypeError) {
		//...
	} else {
		//
	}
}
```

##### 抛出错误
与`try-catch` 语句相配的还有一个`throw` 操作符，用于随时抛出自定义错误。抛出错误时，必须要给`throw` 操作符指定一个值，这个值是什么类型，没有要求。
```
throw 12345;
throw "Hello world!";
throw true;
throw { name: "JavaScript"};
```
遇到`throw`操作符时，代码会立即停止执行。只有当`try-catch`语句捕获到被抛出值，代码才会继续执行

+ 自定义错误类型

可以利用原型链通过继承`Error`创建自定义错误类型。需要为新创建的错误类型指定`name`和`message`属性
```
function CustomError (message) {
	this.name = 'CustomError';
	this.message = message;
}

CustomError.prototype = new Error();

throw new CustomError('Error msg');
```

##### Error事件
任何没有通过`try-catch`处理的错误都会触发`window`对象的`error`事件。

在任何Web浏览器中，`onerror`事件处理程序都不会创建`event`对象，但它可以接受三个参数：错误消息、错误所在的URL和行号。

要指定onerror 事件处理程序，必须使用如下所示的DOM0 级技术，它没有遵循“DOM2 级事件”的标准格式（`addEventListener`）。
```
window.onerror = function(message, url, line){
	alert(message);
};
```
只要发生错误，无论是不是浏览器生成的，都会触发`error`事件，然后让浏览器的默认机制发挥作用，这时候我们需要阻止浏览器的默认行为（`return false`）。
```
window.onerror = function (message, url, line) {
	console.log(message);
	retrun false;
}
```

##### 常见的错误类型

+ 类型转换错误
+ 数据类型错误
+ 通信错误

在数据检测的时候，基本类型的值应该使用`typeof`来检测，对象的值应该使用`instanceof`。

### JSON
---

#### JSON对象
`JSON`对象有两个方法：`stringify`和`parse()`。
```
var book = {
	title: "Professional JavaScript",
	authors: [
		"Nicholas C. Zakas"
	],
	edition: 3,
	year: 2011
};
var jsonText = JSON.stringify(book);
```
以上就把`Javascript`对象序列化为一个`JSON`字符串（没有空格和缩进）
```
{"title":"Professional JavaScript","authors":["Nicholas C. Zakas"],"edition":3,"year":2011}
```
如果传给`JSON.parse()`的字符串不是有效的`JSON`，会抛出错误。

##### 序列化选项
`JSON.stringify()`除了要序列化的`JS`对象外，还可以接受两个参数，一个是过滤器（数组或函数），第二个参数是一个选项，表示是都在`JSON`字符串中保留缩进。

+ 过滤结果

```
var book = {
	"title": "Professional JavaScript",
	"authors": [
		"Nicholas C. Zakas"
	],
	edition: 3,
	year: 2011
};
var jsonText = JSON.stringify(book, ["title", "edition"]);
```
第二个参数中包含两个字符串`"title", "edition"`，所以只会返回对应的属性
```
{"title":"Professional JavaScript","edition":3}
```
过滤器为函数
```
var book = {
	"title": "Professional JavaScript",
	"authors": [
		"Nicholas C. Zakas"
	],
	edition: 3,
	year: 2011
};
var jsonText = JSON.stringify(book, function(key, value){
	switch(key){
		case "authors":
			return value.join(",")
		case "year":
			return 5000;
		case "edition":
			return undefined;
		default:
			return value;
	}
});
```
**注**：返回`undefined`删除该属性，上例的`edition`属性就会被删除。

+ 字符串缩进

`JSON.stringify()`方法的第三个参数用于控制结果中的缩进和空白符。可以是数字，表示缩进的空格数；也可以是字符串，将该字符串作为缩进的表示。

+ `toJSON()`方法

##### 解析选项
`JSON.parse()`方法也可以接受第二参数，该参数是一个函数（被称为还原函数），传入函数的参数均为`key, value`。

如果还原函数返回`undefined`，则表示要从结果中删除响应的键。

### Ajax与Comet
---

#### XMLHttpRequest 对象

##### XHR的用法
+ `open('method', 'url', bool)`：第三个参数表示是否异步发送
+ `send()`：接受一个参数作为请求主体发送的数据，如果不需要则传入`null`

`XHR`对象的属性
+ `responseText`：作为相应主体被返回的文本
+ `responseXML`：如果相应的内容类型是`"text/xml"`或`"application/xml"`，这个属性中将包含这响应数据的XML DOM文档
+ `status`：响应的HTTP状态
+ `statusText`：HTTP状态的说明

同步请求
```
xhr.open("get", "example.txt", false);
xhr.send(null);
if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
	alert(xhr.responseText);
} else {
	alert("Request was unsuccessful: " + xhr.status);
}

```
+ `readyState`：表示请求/响应过程的阶段
	+ 0：未初始化，尚未调用`open()`方法
	+ 1：启动，调用了`open()`方法，尚未调用`send()`方法
	+ 2：发送，调用了`send()`方法，尚未接收到响应。
	+ 3：接收，接收到部分响应数据
	+ 4：完成，已经接收到全部响应数据

```
var xhr = createXHR();
xhr.onreadystatechange = function(){
	if (xhr.readyState == 4){
		if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
			alert(xhr.responseText);
		} else {
			alert("Request was unsuccessful: " + xhr.status);
		}
	}
};
xhr.open("get", "example.txt", true);
xhr.send(null);
```
+ `abort()`：在接收到响应之前通过该方法取消异步请求。
建议调用这个方法之后，对`XHR`对象进行解引用操作。


##### HTTP 头部信息
默认情况下，在发送`XHR`请求的同时，还会发送下列头部信息：
+ `Accept`：浏览器能够处理的内容类型
+ `Accept-Charset`：浏览器能够显示的字符集
+ `Accept-Encoding`：浏览器能够处理的压缩编码
+ `Accept-Language`：浏览器当前设置的语言
+ `Connection`：浏览器与服务器之间连接的类型
+ `Cookie`： 当前页面的 Cookie
+ `Host`：发出请求的页面所在的域
+ `Referer`：发出请求的页面的URI
+ `User-Agent`：浏览器的用户代理

自定义请求头部信息，使用`setRequestHeader()`方法，该方法接受两个参数：头部字段的名称和头部字段的值。

要成功发送请求头部信息，必须在调用`open()`方法之后且调用`send()`方法之前调用`serRequestHeader()`。
```
var xhr = createXHR();
xhr.onreadystatechange = function(){
	// ...
};
xhr.open("get", "example.php", true);
xhr.setRequestHeader("MyHeader", "MyValue");
xhr.send(null);
```
**注**建议使用自定义的头部字段名称，不要使用浏览器正常发送的字段名称，否则有可能会影响服务器的响应。有的浏览器允许开发人员重写默认的头部信息，但有的浏览器则不允许这样做。

调用`XHR`对象的`getResponseHeader()`方法，接受一个参数：头部字段名称。就能取得相应的响应头部信息。
调用`getAllResponseHeaders()`方法可以取得包含所有头部信息的字符串。

##### GET请求
使用`GET`请求经常会发生一个错误，就是查询字符串的格式有问题。查询字符串中每个参数的名称和值都必须使用`encodeURIComponent()`进行编码，然后才能放到URL的末尾。
```
function addURLParam(url, name, value) {
	url += (url.indexOf("?") == -1 ? "?" : "&");
	url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
	return url;
}

var url = "example.php";
//添加参数
url = addURLParam(url, "name", "Nicholas");
url = addURLParam(url, "book", "Professional JavaScript");
//初始化请求
xhr.open("get", url, false);
```

##### POST请求
如果我们希望用XHR模仿表单提交，需要将`Content-Type`头部信息设置为`application/x-www-form-urlencoded`(表单提交的内容类型)
```
function submitData(){
	var xhr = createXHR();
	xhr.onreadystatechange = function(){
		if (xhr.readyState == 4){
			if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
				alert(xhr.responseText);
			} else {
				alert("Request was unsuccessful: " + xhr.status);
			}
		}
	};
	xhr.open("post", "postexample.php", true);
	xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
	var form = document.getElementById("user-info");
	xhr.send(serialize(form));
}
```

#### XMLHttpRequest 2 级

##### FormData
`FormData`为序列化表单以及创建于表单格式相同的数据提供了便利。
```
var data = new FormData();
data.append('name', 'Yeaseon');
```
`append`方法可以将表单的字段和值，传入`FormData`对象中。也可以预先填入表单中的字段：
```
var data = new FormData(document.form[0]);
```
`FormData`的方便就在于不必手动修改XHR对象的请求头部。

##### 超时设定
`XHR`对象添加了一个`timeout`属性，表示请求在等待多少毫秒之后终止。如果规定时间内浏览器没有收到响应，就会触发`timeout`事件，进而调用`ontimeout`事件处理程序。
```
var xhr = createXHR();
xhr.onreadystatechange = function(){
	// ...
};
xhr.open("get", "timeout.php", true);
xhr.timeout = 1000; //将超时设置为1 秒钟
xhr.ontimeout = function(){
	alert("Request did not return in a second.");
};
xhr.send(null);
```
超时之后请求终止，但是此时的`readyState`可能已经变为了`4`，就意味着会调用`onreadystatechange`事件。

可是，如果在超时终止请求之后再访问`status` 属性，就会导致错误。为避免浏览器报告错误，可以将检查`status` 属性的语句封装在一个`try-catch `语句当中。

##### overrideMimeType()方法
用于重写`XHR`响应的`MIME`类型。因为返回响应的`MIME` 类型决定了`XHR` 对象如何处理它，所以提供一种方法能够重写服务器返回的`MIME` 类型是很有用的。
```
var xhr = createXHR();
xhr.open("get", "text.php", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

##### 进度事件
有以下6个进度事件：
+ `loadstart`：在接收到响应数据的第一个字节触发
+ `progress`：在接收响应期间持续不断地触发
+ `error`：在请求发生错误时触发
+ `abort`：在因为调用`abort()`方法而终止连接时触发
+ `load`：在接收到完整的响应数据时触发
+ `loadend`：在通信完成或者触发`error`、`abort`，或`load`事件后触发

**progress**事件
`onprogress`事件处理程序会接收到一个`event`对象，`target`属性指向`XHR`对象，包含着三个额外的属性：
+ `lengthComputable`：表示进度信息是否可用的布尔值
+ `position`：表示已经接受的字节数
+ `totalSize`：表示根据`Content-Length`响应头部确定的预期字节数。

#### 跨资源共享

##### IE对CORS的实现

微软在IE8中引入了`XDR`类型，类似与`XHR`对象，两者的不同之处：
+ cookie不会随请求发送，也不会随响应返回
+ 只能设置请求头部信息中的`Content-Type`字段
+ 不能访问响应头部信息
+ 只支持`GET`和`POST`请求

请求返回之后，就会触发`load`事件，响应数据也会保存在`responseText`属性中：
```
var xdr = new XDomainRequest();
xdr.onload = function () {
	console.log(xdr.responseText);
};
xdr.onerror = function(){
	alert("An error occurred.");
};
xdr.open('get', 'http://..../xxx/');
xdr.send(null);
```
在请求返回之前可以调用`abort()`方法终止请求。
```
xdr.abort();
```
`XDR`对象也支持`timeout`属性以及`ontimeout`事件处理程序
```
var xdr = new XDomainRequest();
xdr.onload = function(){
	alert(xdr.responseText);
};
xdr.onerror = function(){
	alert("An error occurred.");
};
xdr.timeout = 1000;
xdr.ontimeout = function(){
	alert("Request took too long.");
};
xdr.open("get", "http://www.somewhere-else.com/page/");
xdr.send(null);
```
为了支持`POST`请求，`XDR`对象提供了`contentType`属性，用来表示发送数据的格式。
```
var xdr = new XDomainRequest();
xdr.onload = function () {
	//
}
xdr.onerror = function () {
	//
}
xdr.open('post', 'http://www.somewhere-else.com/page/');
xdr.contentType = 'application/x-www-form-urlencoded';
xdr.send('name1=value1&name2=value2');
```

##### 其他浏览器对CORS的实现
与IE中的`XDR`对象不同，通过跨域`XHR`对象可以访问`status`和`statusText`属性，并且支持同步请求。同时也有一些限制：
+ 不能使用`setRequestHeader()`设置自定义头部
+ 不能发送和接收`cookie`
+ 调用`getAllResponseHeaders()`方法总会返回空字符串
```
var xhr = createXHR();
xhr.onreadystatechange = function(){
	if (xhr.readyState == 4){
		if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
			alert(xhr.responseText);
		} else {
			alert("Request was unsuccessful: " + xhr.status);
		}
	}
};
xhr.open("get", "http://www.somewhere-else.com/page/", true);
xhr.send(null);
```

#### 其他跨域技术

##### 图像Ping
```
var img = new Image();
img.onload = img.onerror = function(){
	alert("Done!");
};
img.src = "http://www.example.com/test?name=Nicholas";
```

##### JSONP
`JSONP`是**JSON with padding**的简写。`JSONP`只不过时被包含在函数调用中的`JSON`：
```
callback({"name": "Yeaseon"});
```
`JSONP`由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般是请求中指定的。下面是一个经典的`JSONP`请求
```
http://freegeoip.net/json/?callback=handleResponse
```
这里指定的回调函数的名字叫做`handleResponse`。

`JSONP`是通过动态`<script>`元素来使用的，使用时可以为`src`属性指定一个跨域`URL`。

```
function handleResponse(response){
	alert("You’re at IP address " + response.ip + ", which is in " +
		response.city + ", " + response.region_name);
}
var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```

##### 服务器发送事件

**SSE**支持短轮询、长轮训和HTTP流，而且能在断开连接时自动确定何时重新连接。

1. SSE API
要预订新的事件流，首先要创建一个新的`EventSource`对象，并传入一个入口点：
```
var source = new EventSource('myevents.php');
```
传入的URL必须与创建对象的页面同源。
`EventSource`的实例有一个`readyState`属性：`0`表示正连接到服务器，`1`表示打开了连接，`2`表示关闭了连接。
`EventSource`实例还有三个事件：
+ `open`：在建立连接时触发
+ `message`：在从服务器接收到新事件时触发
+ `error`：在无法建立连接时触发

服务器发回的数据以字符串形式保存在`event.data`中。
默认情况下，`EventSource`对象会保持与服务器的活动连接。如果想强制立即断开连接并且不在重新连接，可以调用`close()`方法。

##### Web Sockets
由于 Web Sockets 使用了自定义的协议,所以 URL 模式也略有不同。未加密的连接不再是 http:// ,而是 ws:// ;加密的连接也不是 https:// ,而是 wss:// 。
1. Web Sockets API
创建一个WebSockets实例对象：
```
var socket = new WebSocket("ws://www.example.com/server.php");
```
WebSocket也有一个表示当前状态的`readyState`属性：
+ `WebSocket.OPENING (0)` ：正在建立连接
+ `WebSocket.OPEN (1)`：已经建立连接
+ `WebSocket.CLOSING (2)`：正在关闭连接
+ `WebSocket.CLOSE (3)`：已经关闭连接

2. 发送和接收数据

向服务器发送数据，使用`send()`方法并传入任意字符串：
```
var socket = new WebSocket('ws:// www.example.com/server.php');
socket.send('Hello World');
```
Web Sockets只能发送纯文本数据，对于复杂的数据结构，在发送之前，必须进行序列化。
```
var message = {
	time: new Date(),
	text: 'Hello world',
	clientId: 'adfalsf39'
};

socket.send(JSON.stringify(message));
```
当服务器向客户端发来消息时，WebSocket对象就会触发`message`事件。这个`message`事件与其他传递消息的协议类似，也是把返回的数据保存在`event.data`属性中。
```
socket.onmessage = function (event) {
 var data = event.data;
 // ....
}
```
与`send()`类似，`event.data`中返回的数据也是字符串，需要手工解析这些数据。

3. 其他事件

WebSocket对象还有其他三个事件，在连接生命周期的不同阶段触发：
+ `open`：在成功建立连接时触发
+ `error`：在发生错误时触发，连接不能持续
+ `close`：在连接关闭时触发

WebSocked对象不支持DOM 2级事件监听：
```
var socket = new WebSocket("ws://www.example.com/server.php");
socket.onopen = function(){
	alert("Connection established.");
};
socket.onerror = function(){
	alert("Connection error.");
};
socket.onclose = function(){
	alert("Connection closed.");
};
```

### 高级技巧
---

#### 高级函数

##### 安全的类型检测
用于区分原生和非原生**JavaScript**对象，通过`Object.prototype.toString()`。
```
function isArray(value){
	return Object.prototype.toString.call(value) == "[object Array]";
}

function isFunction(value){
	return Object.prototype.toString.call(value) == "[object Function]";
}

function isRegExp(value){
	return Object.prototype.toString.call(value) == "[object RegExp]";
}
```

##### 作用域安全的构造函数
防止构造函数内`this`指针的指向被改变（指向`window`）
```
function Person (name, age, job) {
	if (this instanceof Person) {
		this.name = name;
		this.age = age;
		this.job = job;
	} else {
		return new Person(name, age, job);
	}
}
```

##### 惰性载入函数
```
function createXHR(){
	if (typeof XMLHttpRequest != "undefined"){
		return new XMLHttpRequest();
	} else if (typeof ActiveXObject != "undefined"){
		if (typeof arguments.callee.activeXString != "string"){
			var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],
				i,len;
			for (i=0,len=versions.length; i < len; i++){
				try {
					new ActiveXObject(versions[i]);
					arguments.callee.activeXString = versions[i];
					break;
				} catch (ex){
					//跳过
				}
			}
		}
		return new ActiveXObject(arguments.callee.activeXString);
	} else {
		throw new Error("No XHR object available.");
	}
}
```
第一种改法：
```
function createXHR () {
	if (typeof XMLHttpRequest != 'undefined') {
		createXHR = function () {
			return new XMLHttpRequest();
		};
	} else if (typeof ActiveXObjext != 'undefined') {
		createXHR = function () {
			if (typeof arguments.callee.activeXString != 'string') {
				var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],
					i,len;
				for (i = 0; len = versions.length; i < len; i++) {
					try {
						new ActiveXObject(versions[i]);
						arguments.callee.activeXString = versions[i];
						break;
					} catch (e) {
						// skip
					}
				}
			}
			return new ActiveXObject(arguments.callee.activeXString);
		};
	} else {
		createXHR = function () {
			throw new Error('No XHR object available.');
		}
	}
	return createXHR();
}
```
第二种改法：
```
var createXHR = (function () {
	if (typeof XMLHttpRequest != 'undefined') {
		return function () {
			return new XMLHttpRequest();
		};
	} else if (typeof ActiveXObjext != 'undefined') {
		return function () {
			if (typeof arguments.callee.activeXString != 'string') {
				var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"],
					i,len;
				for (i = 0; len = versions.length; i < len; i++) {
					try {
						new ActiveXObject(versions[i]);
						arguments.callee.activeXString = versions[i];
						break;
					} catch (e) {
						// skip
					}
				}
			}
			return new ActiveXObject(arguments.callee.activeXString);
		};
	} else {
		return function () {
			throw new Error('No XHR object available.');
		}
	}
})();
```
##### 函数绑定
`bind()`函数，语法如下：
```
function bind (fn, context) {
	return function () {
		return fn.apply(context, arguments);
	}
}
```

### 离线应用与客户端存储
---

#### 离线检测
`navigator.onLine`属性可以判断设备否能访问网络。

HTML5定义两个事件：`online`和`offline`，当网络状态变化时，分别触发这两个事件：
```
EventUtil.addHandler(window, 'online', function () {
	console.log('online');
});
EventUtil.addHandler(window, 'offline', function () {
	console.log('offline');
});
```

#### 数据存储

##### Web存储机制
Web Storage规范包含两种对象的定义：`sessionStorage`和`globalStorage`。这两个对象在支持的浏览器中都是以`windows`对象属性的形式存在。

1. `Storage`类型

`Storage`类型提供最大的存储空间来存储名值对。
+ `clear()`：删除所有值
+ `getItem(name)`：根据指定的名字`name`获取对应的值
+ `key(index)`：获得`index`位置处的值的名字
+ `removeItem(name)`：删除由`name`指定的名值对
+ `setItem(name, value)`：为指定的`name`设置一个对应的值

2. `sessionStorage`对象

`sessionStorage`对象存储特定于某个会话的数据，也就是该数据只保持到浏览器关闭。存储在`sessionStorage`中的数据可以跨越页面刷新而存在。
```
//使用方法存储数据
sessionStorage.setItem("name", "Nicholas");
//使用属性存储数据
sessionStorage.book = "Professional JavaScript";

//使用方法读取数据
var name = sessionStorage.getItem("name");
//使用属性读取数据
var book = sessionStorage.book;

//使用delete 删除一个值——在WebKit 中无效
delete sessionStorage.name;
//使用方法删除一个值
sessionStorage.removeItem("book");
```
可以通过结合`length`属性和`key()`方法来迭代`sessionStorage`中的值：
```
for (var i = 0, len = sessionStorage.length; i < len; i++) {
	var key = sessionStorage.key(i);
	var value = sessionStorage.getItem(key);
	console.log(key + ' = ' + value);
}
```
还可以使用`for-in`循环来迭代`sessionStorage`中的值：
```
for (var key in sessionStorage) {
	var value = sessionStorage.getItem(key);
	console.log(key + ' = ' + value);
}
```

3. `globalStorage`对象

这个对象的目的是跨越会话存储数据，，但有特定的访问限制。要使用`globalStorage`，首先要指定哪些域可以访问该数据。
```
// 保存数据
globalStorage['wrox.com'].name = 'Yeaseon';

// 获取数据
var name = globalStorage['wrox.com'].name;
```
上例，访问的是针对域名`wrox.com`的存储空间。`globalStorage`对象不是`Storage`的实例，
而具体的`globalStorage['wrox.com']`才是。这个存储空间对于`wrox.com`及其所有子域都是可以访问的。
```
globalStorage["www.wrox.com"].name = "Yeaseon";
globalStorage["www.wrox.com"].book = "Professional JavaScript";
globalStorage["www.wrox.com"].removeItem("name");
var book = globalStorage["www.wrox.com"].getItem("book");
```

4. `localStorage`对象

`localStorage`对象是HTML5规范中作为持久保存客户端数据的方案，并且取代`globalStorage`。要访问同一个`localStorage`对象，页面必须来自同一个域名（子域名无效），必须同源。
```
//使用方法存储数据
localStorage.setItem("name", "Nicholas");
//使用属性存储数据
localStorage.book = "Professional JavaScript";

//使用方法读取数据
var name = localStorage.getItem("name");
//使用属性读取数据
var book = localStorage.book;
```

5. `storage`事件

对`Storage`对象进行任何修改，都会在文档上触发`storage`事件。这个事件的`event`对象有以下属性。
+ `domain`：发生变化的存储空间的域名
+ `key`：设置或删除的键名
+ `newValue`：如果是设置值，则是新值；如果是删除键，则是`null`
+ `oldValue`：键被更改之前的值

在这四个属性中，IE8 和Firefox 只实现了`domain` 属性。在撰写本书的时候，WebKit 尚不支持
`storage` 事件


##### IndexedDB
Indexed Database API，简称**IndexedDB**，是在浏览器中保存结构化数据的一种数据库。**IndexedDB**设计的操作完全是异步进行。
```
var indexedDB = window.indexedDB || window.msIndexedDB || window.mozIndexedDB || window.webkitIndexedDB;
```
1. 数据库

**IndexedDB**就是一个数据库，它最大的特色就是使用对象保存数据，而不是使用表来保存数据。

`indexDB.open()`，传入一个数据库参数。如果该数据库存在就会发送一个打开它的请求；如果该数据库不存在，就会发送一个创建并打开它的请求。请求会返回一个`IDBRequest`对象，这个对象上可以添加`onerror`和`onsuccess`事件处理程序。
```
var request, database;

request = indexedDB.open('admin');
request.onerror = function (event) {
	console.log(event.target.errorCode);
};
request.onsuccess = function (event) {
	database = event.target.result;
};
```
`event.target`都指向`request`对象，因此他们可以互换使用。
发生错误了，`event.target.errorCode`中将会保存一个错误码：
+ `IDBDatebaseException.UNKNOWN_ERR(1)`：意外错误
+ `IDBDatebaseException.NON_TRANSIENT_ERR(2)`：操作不合法
+ `IDBDatebaseException.NOT_FOUND_ERR(3)`：未发现要操作的数据库
+ `IDBDatebaseException.CONSTRAINT_ERR(4)`：违反了数据库约束
+ `IDBDatebaseException.DATA_ERR(5)`：提供给事务的数据不能满足要求
+ `IDBDatebaseException.NOT_ALLOWED_ERR(6)`：操作不合法
+ `IDBDatebaseException.TRANSACTION_INACTIVE_ERR(7)`：试图重用已完成的事务
+ `IDBDatebaseException.ABORT_ERR(8)`：请求中断
+ `IDBDatebaseException.READ_ONLY_ERR(9)`：试图在只读模式下写入或修改数据
+ `IDBDatebaseException.TIMEOUT_ERR(10)`：在有效时间内未完成操作
+ `IDBDatebaseException.QUOTA_ERR(11)`：磁盘空间不足

指定数据库版本号，通过`setVersion()`方法：
```
if (database.version != '1.0') {
	request = database.setVersion('1.0')；
	request.onerror = function (event) {
		console.log(event.target.errorCode);
	};
	request.onsuccess = function (event) {
		console.log(''Database name: ' + database.name + ', Version: ' + database.version);
	}
} else {
	console.log(''Database name: ' + database.name + ', Version: ' + database.version);
}
```

2. 对象存储空间

假设要保存的用户记录由用户名、密码等组成，那么保存一条记录的对象应该类似：
```
var user = {
	username: '007',
	firstname: 'James',
	lastname: 'Bond',
	password: 'foo'
}
```
如果使用`username`属性作为这个对象存储空间的键，这个`username`必须全局唯一，而且大部分时候都要通过这个键来访问数据。
```
var store = db.createObjectStore('users', { keyPath: 'username' });
```
其中第二个参数中的`keyPath`属性，就是空间中将要保存的对象的一个属性，而这个属性将作为存储空间的键来使用。

通过`add()`或`put()`方法来向存储空间添加数据。着两个方法都接收一个参数，就是要保存的对象。
```
//users 中保存着一批用户对象
var i=0,
	request,
	requests = [],
	len = users.length;
while(i < len){
	request = store.add(users[i++]);
	request.onerror = function(){
		//处理错误
	};
	request.onsuccess = function(){
		//处理成功
	};
	requests.push(request);
}
```

3. 事务

在数据库对象上调用`transaction()`可以创建事务，任何时候，只要想读取或修改数据，都要通过事务来组织所有操作。
```
// 创建事务
var transaction = db.transaction();
```
可以传入要访问的一或多个对象存储空间。
```
var transaction = db.transaction('users');

var transaction = db.transaction(['users', 'anotherStore']);
```
前面这些事务都是以**只读**方式访问数据。要修改访问方式，必须在创建事务时传入第二个参数，这个参数表示访问模式：
+ `IDBTransaction.READ_ONLY(0)`：只读
+ `IDBTransaction.READ_WRITE(1)`：读写
+ `IDBTransaction.VERSION_CHANGE(2)`：改变

在Chrome中叫做`webkitIDBTransaction`，可以使用一下代码兼容：
```
var IDBTransaction = window.IDBTransaction || window.webkitIDBTransaction;
```
这样就能方便的指定`transaction()`第二个参数:
```
var transaction = db.transaction('users', IDBTransaction.READ_WRITE);
```

取得事务的索引后，使用`objectStore()`方法并传入存储空间的名称，就可以访问指定的存储空间。然后通过如下方法操作对象：
+ `add()`
+ `put()`
+ `get()`
+ `delete()`
+ `clear()`

```
var request = db.transaction('users').objectStore('users').get('007');
request.onerror = function (event) {
	console.log('Did not get the object!');
};
request.onsuccess = function (event) {
	var result = event.target.result;
	console.log(result.firstName);  // 'James'
};
```
也可以针对事务对象本身进行事件处理，存在两个事件`onerror`，`oncomplete`：
```
transaction.onerror = function (event) {
	// 整个事务都被取消了
}
transaction.oncomplete = function (event) {
	// 整个事务都成功完成了
}
```
**注**：在`oncomplete`事件的事件对象中访问不到`get()`请求返回的数据，必须在`onsuccess`事件中处理。

4.  键范围

```
var IDBKeyRange = window.IDBKeyRange || window.webkitIDBKeyRange;
```
有四种定义键范围的方法：
+ `only()`：取得指定对象的键
+ `lowerBound()`：指定结果集的下界
+ `upperBound()`：指定结果集的上界
+ `bound()`：同时指定上、下界
```
var onlyRange = IDBKeyRange.only("007");

//从键为"007"的对象开始，然后可以移动到最后
var lowerRange = IDBKeyRange.lowerBound("007");
//从键为"007"的对象的下一个对象开始，然后可以移动到最后
var lowerRange = IDBKeyRange.lowerBound("007", true);

//从头开始，到键为"ace"的对象为止
var upperRange = IDBKeyRange.upperBound("ace");
//从头开始，到键为"ace"的对象的上一个对象为止
var upperRange = IDBKeyRange.upperBound("ace", true);

//从键为"007"的对象开始，到键为"ace"的对象为止
var boundRange = IDBKeyRange.bound("007", "ace");
//从键为"007"的对象的下一个对象开始，到键为"ace"的对象为止
var boundRange = IDBKeyRange.bound("007", "ace", true);
//从键为"007"的对象的下一个对象开始，到键为"ace"的对象的上一个对象为止
var boundRange = IDBKeyRange.bound("007", "ace", true, true);
//从键为"007"的对象开始，到键为"ace"的对象的上一个对象为止
var boundRange = IDBKeyRange.bound("007", "ace", false, true);
```

### 新型的API
---

#### Page Visibility API
Page Visibility API 是为了让开发人员知道页面是否对用户可见推出的。
+ `document.hidden`：表示页面是否隐藏的布尔值。
+ `document.visibilityState`
	+ 页面在后台标签页中或浏览器最小化
	+ 页面在前台标签页中
	+ 实际的页面已经隐藏，但用户可以看到页面的预览
	+ 页面在屏幕外执行预渲染处理
+ `visibilitychange`事件：当文档可见性发生改变时，触发该事件。

#### Geolocation API
Geolocation API 在浏览器中的实现是`navigator.geolocation`对象。
+ `getCurrentPosition()`

调用这个方法就会触发请求用户共享地理定位信息的对话框。这个方法接收三个参数：成功回调，可选的失败回调和可选的选项对象。

`成功回调`会接收到一个`Position`对象参数，该对象有两个属性:`coords`和`timestamp`。

`coords`对象中包含于位置相关的信息：
+ `latitude`：十进制度数表示的纬度
+ `longitude`：十进制度数表示的经度
+ `accuracy`：经纬度坐标的精度，以米为单位
```
navigator.geolocation.getCurrentPosition(function (position) {
	drawMapCenteredAt(position.coords.latitude, position.coords.longitude);
});
```

`失败回调`在被调用的时候也会接受到一个参数，这个参数是一个对象，包含连个属性：`message`和`code`。`code`保存着一个数值，表示错误的类型：用户拒绝共享（1）、位置无效（2）或者超时（3）。
```
navigator.geolocation.getCurrentPosition(function (position) {
	drawMapCenteredAt(position.coords.latitude, position.coords.longitude);
}, function (error) {
	console.log('Error code:' + error.code);
	console.log('Error message:' + error.message);
});
```

第三个参数是一个`可选对象`，用于设定信息的类型。可以设置的选项有三个：
+ `enableHightAccuracy`：布尔值，表示必须尽可能使用最准确定的位置信息
+ `timeout`：以毫秒数表示的等待位置信息的最长时间
+ `maximumAge`：表示上一次取得的坐标信息的有效时间，以毫秒表示，如果时间到则重新取得新坐标信息

```
navigator.geolocation.getCurrentPosition(function (position) {
	drawMapCenteredAt(position.coords.latitude, position.coords.longitude);
}, function (error) {
	console.log('Error code:' + error.code);
	console.log('Error message:' + error.message);
}, {
	enableHighAccuracy: true,
	timeout: 5000,
	maximumAge: 25000
});
```

#### File API
File API 在表单中的文件输入字段的基础上，又添加了一些直接访问文件信息的接口。HTML5在DOM中为文件输入元素添加了一个`files`集合。每个**File**对象都有下列只读属性。
+ `name`：本地文件系统的文件名
+ `size`：文件的字节大小
+ `type`：字符串，文件的MIME类型
+ `lastModifiedDate`：字符串，文件上一次修改的时间

##### FileReader 类型
FileReader 类型实现的是一种异步文件读取机制。可以把`FileReader`想象成`XMLHttpRequest`。
+ `readAsText(file, encoding)`：以纯文本形式读取文件，将读取到的文本保存在`result`属性中，第二个参数用于指定编码类型（可选）
+ `readAsDataURL(file)`：读取文件并将文件以数据URI形式保存在`result`属性中
+ `readAsBinaryString(file)`：读取文件并将一个字符串保存在`result`属性中，字符串中的每个字符表示一字节
+ `readAsArrayBuffer(file)`：读取文件并将一个包含文件内容的`ArrayBuffer`保存在`result`属性中

由于读取过程是异步的，所以`FileReader`提供了三个事件：
+ `progress`
+ `error`
+ `load`

`progress`事件，每50ms就会触发一次，通过事件对象可以获得与`XHR`的`progress`事件相同的信息：
+ `lengthComputable`
+ `loaded`
+ `total`

由于种种原因无法读取文件，都会触发`error`事件，相关信息都会保存到`FileReader`的`error`属性中。`error.code`即错误码：
+ `1`：为找到文件
+ `2`：安全性错误
+ `3`：读取中断
+ `4`：文件不可读
+ `5`：编码错误

文件加载成功后会触发`load`事件。

```
var filesList = document.getElementById('files-list');
EventUtil.addHandler(filesList, 'change', function (event) {
	var info = '',
		output = document.getElementById('output'),
		progress = document.getElementById('progress'),
		files = EventUtil.getTarget(event).files,
		type = 'default',
		reader = new FileReader();

	if (/image/.test(files[0].type)) {
		reader.readAsDateURL(files[0]);
		type = 'image';
	} else {
		reader.readAsText(files[0]);
		type = 'text';
	}

	reader.onerror = function () {
		output.innerHTML = 'Could not read file, error code is ' + reader.error.code;
	};

	reader.onprogress = function () {
		if (event.lengthComputable) {
			progress.innerHTML = event.loaded + '/' + event.total;
		}
	};

	reader.onload = function () {
		var html = '';
		switch (type) {
			case 'image':
				html = '<img src=\"' + reader.result + '\">';
				break;
			case 'text':
				html = reader.result;
				break;
		}
		output.innerHTML = html;
	};
});
```

##### 读取拖放的文件
从桌面上把文件拖放到浏览器中会触发`drop` 事件。而且可以在`event.dataTransfer. files`中读取到被放置的文件，当然此时它是一个`File` 对象，与通过文件输入字段取得的`File` 对象一样。
```
var droptarget = document.getElementById('droptarget');

function handleEvent(event) {
	var info = '',
		output = document.getElementById('output');
		files, i, len;
	EventUtil.preventDefault(event);
	if (event.type == 'drop') {
		files = event.dataTransfer.files;  //转换成File对象
		i = 0;
		len = files.length;

		while (i < len) {
			info += files[i].name + ' (' + files[i].type + ', ' + files[i].size + ' bytes)<br>';
			i++;
		}
		output.innerHTML = info;
	}
}
// 阻止默认事件，只有 drop 事件会被处理
EventUtil.addHandler(droptarget, "dragenter", handleEvent);
EventUtil.addHandler(droptarget, "dragover", handleEvent);
EventUtil.addHandler(droptarget, "drop", handleEvent);
```

##### 使用XHR上传文件
创建一个`FormDate`对象，通过它调用`append()`方法并传入相应的`File`对象作为参数，再把`FormData`对象传递给`XHR`的`send()`方法。
```
var droptarget = document.getElementById('droptarget');

function handleEvent(event) {
	var info = '',
		output = document.getElementById('output'),
		data, xhr,
		files, i, len;

	EventUtil.preventDefault(event);

	if (event.type == 'drop') {
		data = new FormData();
		files = event.dataTransfer.files;
		i = 0;
		len = files.length;

		while (i < len) {
			data.append('file' + i, files[i]);
			i++;
		}

		xhr = new XMLHttpRequest();
		xhr.open('post', 'FileAPIExapleUpload.php', true);
		xhr.onreadystatechange = function () {
			if (xhr.readyState == 4) {
				console.log(xhr.responseText);
			}
		};
		xhr.send(data);
	}
}
EventUtil.addHandler(droptarget, "dragenter", handleEvent);
EventUtil.addHandler(droptarget, "dragover", handleEvent);
EventUtil.addHandler(droptarget, "drop", handleEvent);
```
