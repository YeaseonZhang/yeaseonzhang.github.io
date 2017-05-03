---
title: JS红宝书·读书笔记
date: 2017-04-27 10:24:55
tags: JavaScript 笔记
---

{% asset_img book.jpg JavaScript高级程序设计 %}

花了半个多月的时间，终于又把“JS红宝书”又撸了一遍。

第一次读“JS红宝书”还是**2015**年初学JS的时候，那时候只是把语法部分读了一遍，还有一些浏览器相关知识做了下了解，大概也就读了半本的样子，
就开始了用JS进行开发了，在成长的道路上遇见了**JQuery**，当时真的是感觉到**JQuery**太友好了，慢慢放下了原生开发。

现在呢，更多的时候是在用框架进行开发，越来越觉得自己的JS基础很缺乏，然后就开启了“JS红宝书”二刷之路。

下面就把书中自己觉得重要的、没有掌握的知识整理出来。因为我觉得还是会三刷“JS红宝书”，希望把这本700多页的书越读越薄，勉励。

<!-- more -->

## 在HTML中使用JavaScript
---

### `async`
加载外部脚本文件，通知浏览器立即下载，异步执行。

### `noscript`元素
`noscript`标签显示条件：
+ 浏览器不支持脚本
+ 浏览器支持脚本，但是脚本被禁用

## 基本概念
---

### 语法
#### 标识符
+ 第一个字符必须是一个字母、下划线或者一个美元符号
+ 其他字符可以是字母、下划线、美元或者数字

#### 严格模式
支持严格模式的浏览器包括：IE10+、Firefox4+、Safari 5.1+、Opera 12+和Chrome。

### 数据类型

#### undefined
对未初始化的变量执行`typeof`操作会返回`undefined`值，而对于未声明的变量执行`typeof`操作同样会返回`undefined`值。

#### null
```
typeof null	// -> object
```
**undefined**值派生自`null`值。
```
console.log(null == undefind)	// -> true
```

#### isFinite()
测试一个数值是不是无穷值。

`Number.NEGATIVE_INFINITY`：负无穷
`Number.POSITION_INFINITY`：正无穷

#### NaN
在ECMAScript中，任何数值除以0会返回`NaN`。

`isNaN()`接受一个参数，确定这个参数是否"不是数值"。

#### 数值转换
`Number()`
+ 如果是`null`，返回`0`
+ 如果是`undefined`，返回`NaN`

`parseInt()`
在ES5 中不支持解析八进制的能力。
```
parseInt('070');	// -> 70 not 56
```
通过第二个参数，指定转换基数（进制）默认十进制。

#### 字符串
+ ECMAScript中的字符串是不可变的
+ `toString()`

在调用数值的`toString`方法，可以传递一个参数：输出数值的基数。没有`toString`方法的则返回该值的字面量
```
var num = 10;
console.log(num.toString(2));	// -> '1010'
```

#### object类型
Object类型的属性方法：
+ constructor
+ hasOwnProperty(propertyName)
+ isPrototypeOf(obj)
+ propertyIsEnumerable(propertyName)
+ toLocalString()
+ toString()
+ valueOf()

### 操作符
+ `++` or `--`

前置与后置的区别
```
var num1 = 2;
var num2 = 20;
var num3 = --num1 + num2;	// 21
var num4 = num1 + num2;		// 21

var num5 = 2;
var num6 = 20;
var num7 = num5-- + num6;	// 22
var num8 = num5 + num6;		// 21
```

+ 一元加操作符用于强制类型转换，隐式`Number()`效果

#### for-in 语句

`for-in`语句是一种精确的迭代语句，可以用来枚举对象的属性。

通过`for-in`循环输出的属性名的顺序是不可预测的。

如果要迭代的对象的变量值为`null`或`undefined`，`for-in`语句会抛出错误。**ES5**更正了这一行为，不再抛出错误，只是不再执行循环体。

**建议**：在是使用`for-in`循环之前，先检查对象值是不是`null`或者`undefined`。

## 变量、作用域和内存问题
---

### 基本类型和引用类型

#### 复制变量值
+ 复制基本类型值，这两个变量相互独立，互不影响。
+ 复制引用类型（对象），值引用是一个指针，改变其中一个对象，会影响另一个对象。

#### 传递参数
```
function setName(obj) {
	obj.name = "Nicholas";
}
var person = new Object();
setName(person);
alert(person.name); //"Nicholas"
```
以上代码中创建一个对象，并将其保存在了变量`person` 中。然后，这个变量被传递到`setName()`函数中之后就被复制给了`obj`。在这个函数部，`obj` 和`person` 引用的是同一个对象。换句话说，即使这个变量是按值传递的，`obj` 也会按引用来访问同一个对象。于是，当在函数内部为`obj` 添加`name`属性后，函数外部的`person` 也将有所反映；因为`person` 指向的对象在堆内存中只有一个，而且是全局对象。有很多开发人员错误地认为：在局部作用域中修改的对象会在全局作用域中反映出来，就说明参数是按引用传递的。为了证明对象是按值传递的，我们再看一看下面这个经过修改的例子：
```
function setName(obj) {
	obj.name = "Nicholas";
	obj = new Object();
	obj.name = "Greg";
}
var person = new Object();
setName(person);
alert(person.name); //"Nicholas"
```
这个例子与前一个例子的唯一区别，就是在`setName()`函数中添加了两行代码：一行代码为`obj`重新定义了一个对象，另一行代码为该对象定义了一个带有不同值的`name`属性。在把`person `传递给`setName()`后，其`name` 属性被设置为"Nicholas"。然后，又将一个新对象赋给变量`obj`，同时将其`name`属性设置为"Greg"。如果`person` 是按引用传递的，那么`person` 就会自动被修改为指向其`name `属性值为"Greg"的新对象。但是，当接下来再访问`person.name` 时，显示的值仍然是"Nicholas"。这说明即使在函数内部修改了参数的值，但原始的引用仍然保持未变。实际上，当在函数内部重写`obj `时，这个变量引用的就是一个局部对象了。**而这个局部对象会在函数执行完毕后立即被销毁。**

#### 检测类型

虽然在检测基本数据类型时`typeof` 是非常得力的助手，但在检测引用类型的值时，这个操作符的用处不大。通常，我们并不是想知道某个值是对象，而是想知道它是什么类型的对象。为此，ECMAScript提供了`instanceof` 操作符。

#### 延长作用域

+ `try-catch`语句中的`catch`块
+ `with`语句

### 小结

+ 基本类型值在内存中占据固定大小的空间，因此被保存在栈内存中；
+ 从一个变量向另一个变量复制基本类型的值，会创建这个值的一个副本；
+ 引用类型的值是对象，保存在堆内存中；
+ 包含引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针；

## 引用类型
---

### Array类型

#### 检测数组
```
if (value instanceof Array) {

}
```

ECMAScript5新增了 `Array.isArray()`方法
```
if (Array.isArray(value)) {

}
```

#### sort方法
该方法有缺陷，`sort()`方法会调用每个数组项的`toString()`转型方法，然后比较字符串进行排序。
```
var values = [0, 1, 5, 10, 15];
values.sort();
alert(values); //0,1,10,15,5
```
因此`sort()`方法接受一个比较函数作为参数。
```
function compare(value1, value2) {
	if (value1 < value2) {
		return -1;
	} else if (value1 > value2) {
		return 1;
	} else {
		return 0;
	}
}

var values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); //0,1,5,10,15
```

#### splice方法

`splice`方法始终返回一个数组，该数组包含了从原始数组中删除的项。

```
var colors = ["red", "green", "blue"];
var removed = colors.splice(0,1); // 删除第一项
alert(colors); // green,blue
alert(removed); // red，返回的数组中只包含一项

removed = colors.splice(1, 0, "yellow", "orange"); // 从位置1 开始插入两项
alert(colors); // green,yellow,orange,blue
alert(removed); // 返回的是一个空数组

removed = colors.splice(1, 1, "red", "purple"); // 插入两项，删除一项
alert(colors); // green,red,purple,orange,blue
alert(removed); // yellow，返回的数组中只包含一项
```

#### 迭代方法

ECMAScript5为数组定义了5个迭代方法。
+ `every()`: 对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回true。
+ `filter()`: 对数组中的每一项运行给定函数，返回该函数会返回true 的项组成的数组。
+ `forEach()`: 对数组中的每一项运行给定函数。这个方法没有返回值。
+ `map()`: 对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
+ `some()`: 对数组中的每一项运行给定函数，如果该函数对任一项返回true，则返回true。

#### 归并方法

ECMAScript 5 还新增了两个归并数组的方法。
+ `reduce()`
+ `reduceRight()`
`reduce()`和`reduceRight()`的函数接收4 个参数：前一个值、当前值、项的索引和数组对象。
```
var values = [1, 2, 3, 4, 5];
var sum = values.reduce((prev, cur, index, array) => {
	return prev + cur;
});
console.log(sum);
```
### RegExp类型

#### 正则表达式中的元字符
`( [ { \ ^ $ | ) ? * + . ] }`

**注**：匹配元字符必须转义

#### `RegExp` 构造函数
接受两个参数： 一个是要匹配的字符串模式，另一个是可选的标志字符串。
```
var pattern1 = /[bc]at/i;
// 等价于
var pattern2 = new RegExp('[bc]at', 'i');
```
**注**：由于RegExp构造函数的模式是字符串，所以在某些情况下要对字符串进行双重转义，所有元字符都必须双重转义。例如`\n`在字符串中被转义为`\\n`，而在正则表达式字符串中就会变成`\\\\n`。

#### `RegExp`实例方法

##### `exex()`方法

该方法是专门为捕获组而设计的。`exec()`接受一个参数，即要应用模式的字符串，然后返回包含第一个匹配项信息的数组；或者在没有匹配项的情况下返回`null`。返回的数组虽然是Array 的实例，但包含两个额外的属性：`index` 和`input`。其中，`index` 表示匹配项在字符串中的位置，而`input` 表示应用正则表达式的字符串。在数组中，第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串（如果模式中没有捕获组，则该数组只包含一项）。
```
var text = "mom and dad and baby";
var pattern = /mom( and dad( and baby)?)?/gi;

var matches = pattern.exec(text);
alert(matches.index); // 0
alert(matches.input); // "mom and dad and baby"
alert(matches[0]); // "mom and dad and baby"
alert(matches[1]); // " and dad and baby"
aler t(matches[2]); // " and baby"
```

对于`exec()`方法而言，即使在模式中设置了全局标志（`g`），它每次也只会返回一个匹配项。在不设置全局标志的情况下，在同一个字符串上多次调用`exec()`将始终返回第一个匹配项的信息。而在**设置**全局标志的情况下，每次调用`exec()`则都会在字符串中继续查找新匹配项。

##### `test()`方法

接受一个字符串参数。在模式与该参数匹配的情况下返回`true`；否则，返回`false`。
```
var text = "000-00-0000";
var pattern = /\d{3}-\d{2}-\d{4}/;

if (pattern.test(text)){
	alert("The pattern was matched.");
}
```

RegExp实例继承的`toLocaleString()`和`toString()`方法都会返回正则表达式的字面量，与创建正则表达式的方式无关。
```
var pattern = new RegExp("\\[bc\\]at", "gi");
alert(pattern.toString()); // /\[bc\]at/gi
alert(pattern.toLocaleString()); // /\[bc\]at/gi
```

### Function类型

#### 函数声明与函数表达式
解析器会率先读取函数声明，并使其在执行任何代码之前可用；至于函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被解释执行。
```
// ok
alert(sum(10,10));
function sum(num1, num2){
	return num1 + num2;
}

// unexpected identifier(意外标识符)
alert(sum(10,10));
var sum = function(num1, num2){
	return num1 + num2;
};
```

**注**：要访问函数的指针而不执行函数的话，必须去掉函数名后的那对圆括号。

#### 函数内部属性
+ `arguments`
+ `this`

`arguments`具有一个`callee`属性，该属性是一个指针，指向拥有这个`arguments`对象的函数。
```
function factorial(num){
	if (num <=1) {
		return 1;
	} else {
		return num * factorial(num-1)
	}
}
```
等价于
```
function factorial(num){
	if (num <=1) {
		return 1;
	} else {
		return num * arguments.callee(num-1)
	}
}
```
达到一种解耦的效果。

ECMAScript 5也规范了一个函数对象属性：`caller`(看着很像`callee`)，这个属性中保存着调用当前函数的函数的引用，如果实在全局作用域中调用当前函数，它的值为`null`。
```
function outer(){
	inner();
}
function inner(){
	alert(inner.caller);
}
outer();
```
`inner.caller`指向`outer()`。为了实现更松散的耦合，也可以通过`argument.callee.caller`来访问相同的信息。
```
function outer() {
	inner();
}
function inner() {
	alert(arguments.callee.caller);
}
outer();
```

**注**：当函数在**严格模式**下运行时，访问`arguments.callee` 会导致错误。ECMAScript 5 还定义了`arguments.caller `属性，但在严格模式下访问它也会导致错误，而在非严格模式下这个属性始终是`undefined`。定义这个属性是为了分清`arguments.caller` 和函数的`caller` 属性。以上变化都是为了加强这门语言的安全性，这样第三方代码就不能在相同的环境里窥视其他代码了。
**严格模式**还有一个限制：不能为函数的`caller` 属性赋值，否则会导致错误。

#### 函数属性和方法

每个函数都包含两个属性:
+ `length`: 表示函数希望接收的命名参数的个数
+ `prototype`: 保存实例方法

每个函数都包含两个非继承而来的方法：
+ `apply()`
+ `call()`
这两个方法的用途都是在特定的作用域中调用函数，实际上等于设置函数体内`this` 对象的值。首先，`apply()`方法接收两个参数：一个是在其中运行函数的作用域，另一个是参数数组。其中，第二个参数可以是`Array` 的实例，也可以是`arguments `对象。
`call()`方法与`apply()`方法的作用相同，它们的区别仅在于接收参数的方式不同。对于`call()`方法而言，第一个参数是`this` 值没有变化，变化的是其余参数都直接传递给函数。换句话说，在使用`call()`方法时，传递给函数的参数必须逐个列举出来。

**注**：在严格模式下，未指定环境对象而调用函数，则`this` 值不会转型为`window`。除非明确把函数添加到某个对象或者调用`apply()`或`call()`，否则this 值将是`undefined`。

在非严格模式下，`call`、`apply`的第一个参数传递为`null`或`undefined`时，函数体内的`this`会指向默认的宿主对象，在浏览器中则是`window`。

**ECMAScript 5**定义了一个方法`bind()`，这个方法会创建一个函数的实例，其`this`值会被绑定到传给`bind()`函数的值。

### 基本包装类型

使用`new`调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的。
```
var value = '25';
var number = Number(value);	// 转型函数
console.log(typeof number);	// 'number'

var obj = new Number(value); // 构造函数
console.log(typeof obj);	// 'object'
```

#### Number类型
Number类型的`toString()`方法很特别，可以传递一个表示基数的参数。
```
var num = 10;
alert(num.toString()); //"10"
alert(num.toString(2)); //"1010"
alert(num.toString(8)); //"12"
alert(num.toString(10)); //"10"
alert(num.toString(16)); //"a"
```

#### String类型
##### 字符方法
+ `charAt()`
+ `charCodeAt()`

```
var stringValue = "hello world";

alert(stringValue.charAt(1)); //"e"
alert(stringValue.charCodeAt(1)); //输出字符编码"101"
```
##### 字符串操作方法
+ `concat()`
+ `slice()`
+ `substr()`
+ `substring()`

这些方法对原字符均没有任何影响。

```
var stringValue = "hello ";
var result = stringValue.concat("world", "!");
alert(result); //"hello world!"

var stringValue = "hello world";
alert(stringValue.slice(3)); //"lo world"
alert(stringValue.substring(3)); //"lo world"
alert(stringValue.substr(3)); //"lo world"
alert(stringValue.slice(3, 7)); //"lo w"
alert(stringValue.substring(3,7)); //"lo w"
alert(stringValue.substr(3, 7)); //"lo worl"

// 参数是负值的情况下，它们的行为就不尽相同了。
// 其中，slice()方法会将传入的负值与字符串的长度相加，
// substr()方法将负的第一个参数加上字符串的长度，而将负的第二个参数转换为0。
// 最后，substring()方法会把所有负值参数都转换为0。
alert(stringValue.slice(-3)); //"rld"
alert(stringValue.substring(-3)); //"hello world"
alert(stringValue.substr(-3)); //"rld"
alert(stringValue.slice(3, -4)); //"lo w"
alert(stringValue.substring(3, -4)); //"hel"
alert(stringValue.substr(3, -4)); //""（空字符串）
```

##### 字符串位置方法
+ `indexOf()`
+ `lastIndexOf()`

两个方法的第二个参数，表示从字符串中哪个位置开始搜索。

##### trim()方法

**ECMAScript 5**方法

#####  字符串转换大小写方法
+ `toLowerCase()`
+ `toLocaleLowerCase()`
+ `toUpperCase()`
+ `toLocaleUpperCase()`

##### 字符串的模式匹配方法
+ `match()`
+ `search()`
+ `replace()`
+ `split()`

`match()`方法，在字符串上调用这个方法，本质上和调用`RegExp`的`exec()`方法相同。`match()`方法只接受一个参数，要么是一个正则表达式，要么是一个**RegExp**对象。
```
var text = 'cat, bat, sat, fat';
var pattern = /.at/;

// 等价于 pattern.exec(text)
var matches = text.match(pattern);
alert(matches.index); //0
alert(matches[0]); //"cat"
alert(pattern.lastIndex); //0
```

`search()`方法的参数与`match()`方法相同，该方法返回字符串中第一个匹配项的索引，没有匹配项返回`-1`；个人认为`serch()`就是正则版的`indexOf()`。
```
var text = "cat, bat, sat, fat";
var pos = text.search(/at/);
aler t(pos); //1
```

**ECMAScript**提供了`replace()`方法，该方法接受两个参数，第一个参数可以是**RegExp**对象或者是一个字符串，第二个参数可以是一个字符串或者一个函数。
```
var text = "cat, bat, sat, fat";
var result = text.replace("at", "ond");
alert(result); //"cond, bat, sat, fat"

result = text.replace(/at/g, "ond");
aler t(result); //"cond, bond, sond, fond"
```
| 字符序列 | 替换文本 |
| : - : | : -- : |
| `$$` | `$` |
| `$&` | 匹配整个模式的子字符串。RegExp.lastMatch |
| `$'` | 匹配子字符串之前的字符串。RegExp.leftContext |
| $` | 匹配的子字符串之后的字符串。 RegExp.rightContext |
| `$n` | 匹配第n个捕获组的子字符串 n: 0~9 |
| `$nn` | 匹配第nn个捕获组的子字符串 nn: 01~99 |

```
var text = "cat, bat, sat, fat";
result = text.replace(/(.at)/g, "word ($1)");
alert(result); //word (cat), word (bat), word (sat), word (fat)
```

`split()`方法可以基于指定的分隔符(字符串 or RegExp对象)将一个字符串分割成多个子字符串，并将结构放在一个数组中。可以接受可选的第二个参数，用于指定数组的大小。
```
var colorText = "red,blue,green,yellow";
var colors1 = colorText.split(","); //["red", "blue", "green", "yellow"]
var colors2 = colorText.split(",", 2); //["red", "blue"]
var colors3 = colorText.split(/[^\,]+/); //["", ",", ",", ",", ""]
```

##### localeCompare()方法

比较两个字符串，并返回下列值中的 一个：
+ 如果字符串在字母表中应该排在字符串参数之前，则返回一个负数
+ 如果字符串等于字符串参数，则返回0；
+ 如果字符串在字母表中应该排在字符串参数之后，则返回一个正数

```
var stringValue = "yellow";
alert(stringValue.localeCompare("brick")); //1
alert(stringValue.localeCompare("yellow")); //0
alert(stringValue.localeCompare("zoo")); //-1
```

##### fromCharCode()方法

这个方法的任务是接收一个或多个字符编码，然后将它们转换成一个字符串。相当于`charCodeAt()`反操作。
```
alert(String.fromCharCode(104, 101, 108, 108, 111)); //"hello"
```

#### Math 对象
+ `min()`
+ `max()`
+ `ceil()`
+ `floor()`
+ `round()`
+ `random()`

## 面向对象的程序设计

### 理解对象

#### 属性类型
##### 数据类型
+ `[[Configurable]]`: 表示能否通过`delete`删除属性从而重新定义属性，能够修改属性的特性，或者能否把属性修改为访问器属性
+ `[[Enumerable]]`: 表示能否通过`for-in`循环返回属性
+ `[[Writable]]`: 表示能否修改属性的值
+ `[[Value]]`: 包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。默认值`undefined`

要修改属性默认的特性，必须使用**ECMAScript 5**的`Object.defineProperty()`方法。这个方法接受三个参数：属性所在对象，属性名和一个描述符对象。其中描述符对象的属性值必须是：`configurable`、`enumerable`、`writable`和`value`。设置其中一个或多个。
```
var person = {};
Object.defineProperty(person, 'name', {
	writable: false,
	value: 'Yeaseon'
});
```
`Object.defineProperty()`方法不能对`configurable: false`的对象进行修改。

##### 访问器属性
+ `[[Configurable]]`: 表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。
+ `[[Enumerable]]`: 表示能否通过`for-in`循环返回属性。
+ `[[Get]]`: 在读取属性时调用的函数，默认`undefined`
+ `[[Set]]`: 在写入属性时调用的函数，默认`undefined`

```
var book = {
	_year: 2004,
	edition: 1
};
Object.defineProperty(book, "year", {
	get: function(){
		return this._year;
	},
	set: function(newValue){
		if (newValue > 2004) {
			this._year = newValue;
			this.edition += newValue - 2004;
		}
	}
});
book.year = 2005;
alert(book.edition); //2
```

#### 读取属性的特性
**ECMAScript 5**的`Object.getOwnPropertyDescriptor()`方法，可以取得给定属性的描述符。该方法接收两个参数：属性所在的对象和要读取器描述符的属性名称，返回值是对象。


### 创建对象

#### 工厂模式
```
function createPerson(name, age, job){
	var o = new Object();
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function(){
		alert(this.name);
	};
	return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```

#### 构造函数模式
```
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function(){
		alert(this.name);
	};
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

#### 原型模式
```
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
	alert(this.name);
};

var person1 = new Person();
person1.sayName(); //"Nicholas"
```
`Person.prototype.constructor`会指向`Person`，`person1`并没有直接连接到构造函数`Person`。

{% asset_img prototype-1.png %}

+ 可以通过`isPrototypeOf()`方法来确定对象之间是否存在原型关系。从本质上讲，`[[Prototype]]`指向调用`isPrototypeOf()`方法的对象`Person.prototype`，则会返回`true`。

```
alert(Person.prototype.isPrototypeOf(person1)); //true
```
在**ECMAScript 5**增加了`Object.getPrototypeOf()`方法，该方法返回`[[Prototype]]`的值。
```
alert(Object.getPrototypeOf(person1) == Person.prototype); //true
alert(Object.getPrototypeOf(person1).name); //"Nicholas"
```

**注**: 虽然可以通过对象实例`person1`访问保存在原型中的值，但却不能重写原型中的值。
```
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
	alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

person1.name = "Greg"; //实质是在实例上增加一个name属性
alert(person1.name); //"Greg"——来自实例
alert(person2.name); //"Nicholas"——来自原型
```
可以通过`delete`删除实例属性，从而继续访问原型中的属性。
```
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
	alert(this.name);
};
var person1 = new Person();

person1.name = "Greg";
alert(person1.name); //"Greg"——来自实例

delete person1.name;
alert(person1.name); //"Nicholas"——来自原型
```

+ `hasOwnProperty()`方法可以检测一个属性是不是存在于实例，是则返回`true`。

{% asset_img prototype-2.png %}

+ `in`操作符
（prop in obj）通过`in`操作符可以判定对象是否有该属性，不论是本身含有还是原型含有，都返回`true`。
可以通过`in`配合`hasOwnProperty()`确定该属性是存在于对象中还是原型中：
```
function detectProperty(obj, name) {
	if (name in obj) {
		obj.hasOwnProperty(name) ? '在对象中' : '在原型中';
	} else {
		console.log('不含有该属性');
	}
}
```

+ **ECMAScript 5**`Object.keys()`方法可以取得对象上所有可枚举的实例属性。

```
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
	alert(this.name);
};
var keys = Object.keys(Person.prototype);
alert(keys); //"name,age,job,sayName"

var p1 = new Person();
p1.name = "Rob";
p1.age = 31;
var p1keys = Object.keys(p1);
alert(p1keys); //"name,age"
```

+ `Object.getOwnPropertyNames`会得到所有实例属性，不论是否可枚举。

```
var keys = Object.getOwnPropertyNames(Person.prototype);
alert(keys); //"constructor,name,age,job,sayName"
```

简化`Person.prototype`写法：
```
function Person(){
}
Person.prototype = {
	name : "Nicholas",
	age : 29,
	job: "Software Engineer",
	sayName : function () {
		alert(this.name);
	}
};
```
这样写有一个缺陷，`constructor`属性则会等于`Object`，我们需要手动设置`constructor`。
```
function Person(){
}
Person.prototype = {
	constructor : Person,
	name : "Nicholas",
	age : 29,
	job: "Software Engineer",
	sayName : function () {
		alert(this.name);
	}
};
```
但这同时也会导致`constructor`的`[[Enumerable]]`特性变成了`true`，默认情况下是`false`。再修改下写法：
```
function Person(){
}
Person.prototype = {
	name : "Nicholas",
	age : 29,
	job : "Software Engineer",
	sayName : function () {
		alert(this.name);
	}
};

Object.defineProperty(Person.prototype, "constructor", {
	enumerable: false,
	value: Person
});
```

**原型重写**会导致构造函数与最初原型之间的联系切断。
```
function Person(){
}
var friend = new Person();
Person.prototype = {  //重写
	constructor: Person,
	name : "Nicholas",
	age : 29,
	job : "Software Engineer",
	sayName : function () {
		alert(this.name);
	}
};
friend.sayName(); //error
```
{% asset_img prototype-3.png %}

#### 结合使用构造函数和原型模式
用构造函数模式定义实例属性，用原型模式定义方法和共享属性。
```
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.friends = ["Shelby", "Court"];
}
Person.prototype = {
	constructor : Person,
	sayName : function(){
		alert(this.name);
	}
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.friends.push("Van");
alert(person1.friends); //"Shelby,Count,Van"
alert(person2.friends); //"Shelby,Count"
alert(person1.friends === person2.friends); //false
alert(person1.sayName === person2.sayName); //true
```

### 继承
构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型的内部指针。

#### 原型链
```
function SuperType () {
	this.property = true;
}

SuperType.prototype.getSuperValue = function () {
	return this.property;
};

function SubType () {
	this.subproperty = false;
}

SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function () {
	return this.subproperty;
};

var instance = new SubType();
console.log(instance.getSuperValue());  // true
```

{% asset_img prototype-4.png %}

##### `instanceof`操作符

用来确定原型和实例之间的关系。
```
alert(instance instanceof Object); //true
alert(instance instanceof SuperType); //true
alert(instance instanceof SubType); //true
```
第二种方式就是`isPrototypeOf()`方法，只要原型链中出现过的原型，都可以说是该原型链所派生的实例的原型。
```
alert(Object.prototype.isPrototypeOf(instance)); //true
alert(SuperType.prototype.isPrototypeOf(instance)); //true
alert(SubType.prototype.isPrototypeOf(instance)); //true
```

## 函数表达式

由于有声明提升的存在，定义函数不要放在条件表达式中
```
if (condition) {
	function sayHi () {
		console.log('Hi');
	}
} else {
	function sayHi () {
		console.log('Yo');
	}
}
```
在**ECMAScript**中属于无效语法，在不同浏览器中修正的做法并不一致。推荐的写法，如下：
```
var sayHi;

if (condition) {
	sayHi = function () {
		console.log('Hi');
	}
} else {
	sayHi = function () {
		console.log('Yo');
	}
}
```
这种函数表达式不存在声明提升，所以OK。

### 递归
#### 函数作用域链
当某个函数被调用时，会创建一个执行环境及相应的作用域链。然后，使用`arguments`和其他命名参数的值来初始化函数的活动对象。在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位...，直到作用域终点的全局执行环境。


### 闭包
```
function createFunctions () {
	var result = new Array();
	for (var i=0; i < 10; i++){
		result[i] = function(){
			return i;
		};
	}
	return result;
}
```

```
function createFunction () {
	var result = new Array();
	for (var i = 0; i < 10; i++) {
		result[i] = function (num) {
			return function () {
				return num;
			};
		}(i);
	}
	return result;
}
```

**注**：在闭包中使用`this`对象可能会导致一些问题。匿名函数的执行环境具有全局性，因此其`this`对象通常指向`window`。
```
var name = 'The window';

var obj = {
	name: 'my object',
	getNameFunc: function () {
		return function () {
			return this.nam;
		}
	}
}

console.log(obj.getNameFunc()());  // The Window (非严格模式)
```

### 模仿块级作用域
#### 块级作用域
```
(function () {
	// 这里是块级作用域
})();
```

## BOM
---

### window 对象

#### 全局作用域

抛开全局变量会成为`window`对象的属性不谈，定义全局变量与在`window`对象上直接定义属性还是有一点差别：**全局变量不能通过`delete`操作符删除，而直接定义在`window`对象上的定义的属性可以**。


#### 窗口位置

获得窗口左边和上边的位置。
```
var leftPos = (typeof window.screenLeft == 'number') ? window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == 'number') ? window.screenTop : window.screenY;
```
Firefox支持`screenX`和`screenY`，其他浏览器均支持`screenLeft`、`screenTop`。

但是还是需要注意一个问题：在`IE` `Opera`中，`screenLeft  screenTop` 保存的的是可见区域的距离，也就是我们浏览器中不包含工具栏的区域与屏幕的距离；在`Chrome`、`Firefox`和`Safari`中`screenY`或`screenTop`返回的是整个浏览器窗口相对于屏幕坐标的值。

#### 窗口大小

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

#### 导航
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

### location 对象
`location`对象的属性
+ `hash`
+ `host`
+ `hostname`：与`host`不同的是，不带端口号
+ `href`
+ `pathname`: 返回URL中的目录和（或）文件名
+ `port`
+ `protocol`
+ `search`：返回URL的查询字符串，这个字符串`?`开头

### navigator 对象
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


## DOM
---

### 节点层次

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

#### Document类型
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

#### Element类型
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

#### Text类型
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

### DOM 操作技术

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

#### 操作表格
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

## DOM 扩展
---

### 选择符 API
+ `querySelector()`方法

`querySelector()`方法接受一个**CSS**选择符，返回与该模式匹配的**第一个元素**，若没有，返回`null`。

可以通过`Document`类型调用，也可以通过`Element`类型调用，后者只会在该元素后代元素的范围内查找匹配的元素。

+ `querySelectorAll()`方法

`querySelectorAll()`方法返回的是所有匹配的元素，是一个`NodeList`实例。

+ `matchesSelector()`方法

为`Element`类型新增的一个方法，接受一个参数`CSS`选择符，如果调用元素与该选择符匹配，返回`true`，否则返回`false`。

### 元素遍历

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

### HTML5
+ `getElementsByClassName()`方法
+ `classList`属性，这个属性是新集合类型`DOMTokenList`的实例。
	+ `add(value)`
	+ `contains(value)`
	+ `remove(value)`
	+ `toggle(value)`

```
div.classList.remove("user");
```
#### 焦点管理

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

#### `HTMLDocument`的变化
	+ `readyState`属性有两个值，`loading`和`complete`
```
if (document.readyState == 'complete') {
	// 加载完成
}
```

#### `document.charset`字符集属性

#### `data-`自定义数据属性
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

#### `innerHTML`属性

在读模式下，`innerHTML` 属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应
的HTML 标记。在写模式下，`innerHTML` 会根据指定的值创建新的DOM树，然后用这个DOM树完全
替换调用元素原先的所有子节点

#### `outerHTML`属性

在读模式下，`outerHTML` 返回调用它的元素及所有子节点的HTML 标签。在写模式下，`outerHTML`
会根据指定的HTML 字符串创建新的DOM 子树，然后用这个DOM子树完全替换调用元素。

#### `insertAdjacentHTML()`方法
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

#### `scrollIntoView()`方法

`scrollIntoView`方法可以在所有HTML元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。如果这个方法传入`true`作为参数，或者不传参数，那么窗口滚动之后就会让调用元素的顶部与视口顶部 尽可能平齐，如果传入`false`，调用元素会尽可能全部出现在视口中，不过顶部不一定平齐。
```
// 让元素可见
document.form[0].scrollIntoView();
```

### 专有扩展

#### 插入文本

+ `innerText`属性
+ `outerText`属性

#### 滚动

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

## DOM2 和 DOM3
---

### 样式

### 元素大小

#### 偏移量
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

#### 客户区大小
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

#### 滚动大小
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

#### 确定元素大小
+ `getBoundingClientRect()`方法，会返回一个矩形对象，包含`left top right bottom`四个属性。这些属性给出了元素在页面中相对于视口的位置。

### 遍历

#### NodeIterator
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

## 事件
---

### 事件流

#### 事件冒泡

IE的事件流叫做**事件冒泡**，即事件开始时由最具体的元素接受，然后逐级向上传播到较为不具体的节点。

#### 事件捕获

Netscape 团队提出的事件流叫做**事件捕获**，事件捕获的用意在于在事件到达预定目标之前捕获它。

#### DOM事件流

“DOM2级事件”规定的事件流包括三个阶段：`事件捕获阶段`、`处于目标阶段`和`事件冒泡阶段`。

### 事件处理程序

#### DOM0 级事件处理程序

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

#### DOM2 级事件处理程序
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

#### IE事件处理程序
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

### 事件对象
在触发DOM上的某个事件时，会产生一个事件对象`event`。

#### DOM中的事件对象

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

在事件处理程序内部，对象`this` 始终等于`currentTarget` 的值，而`target` 则只包含事件的实际目标。如果直接将事件处理程序指定给了目标元素，则`this`、`currentTarget` 和`target` 包含相同的值。
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

#### 跨浏览器的事件对象
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

### 事件类型

#### UI 事件
+ `load`事件
+ `unload`事件
+ `resize`事件
+ `scroll`事件

#### 焦点事件
+ `blur`事件：失去焦点
+ `focus`事件：获得焦点

#### 鼠标与滚动事件
+ `click`事件
+ `dbclick`事件
+ `mousedown`事件：按下鼠标
+ `mouseenter`事件：光标移入
+ `mouseleave`事件：光标移出
+ `mousemove`事件：鼠标在元素内部移动重复触发
+ `mouseout`事件：在鼠标指针位于一个元素上方，然后用户将其移入另一个元素时触发。又移入的另一个元素可能位于前一个元素的外部，也可能是这个元素的子元素
+ `mouseover`事件：在鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触发
+ `mouseup`事件：释放鼠标按钮时触发

页面上的所有元素都支持鼠标事件。除了`mouseenter` 和`mouseleave`，所有鼠标事件都会冒泡，也可以被取消，而取消鼠标事件将会影响浏览器的默认行为。

只有在同一个元素上相继触发`mousedown` 和`mouseup` 事件，才会触发`click` 事件；如果`mousedown` 或`mouseup` 中的一个被取消，就不会触发`click` 事件。

#### 触摸设备
iOS和Android设备的相关事件：
+ 不支持`dbclick`事件。双击浏览器窗口会放大画面
+ 轻击可单击元素会触发`mousemove`事件。。如果此操作会导致内容变化，将不再有其他事件发生；如果屏幕没有因此变化，那么会依次发生`mousedown`、`mouseup` 和`click` 事件。轻击不可单击的元素不会触发任何事件。可单击的元素是指那些单击可产生默认操作的元素（如链接），或者那些已经被指定了`onclick` 事件处理程序的元素。
+ `mousemove`事件也会触发`mouseover`和`mouseout`事件
+ 两个手指放在屏幕上且页面随手指移动而滚动时会触发`mousewheel`和`scroll`事件。

#### HTML5事件
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

#### 设备事件
+ `orientationchange`事件：横竖屏，有三个值： -90 ，0， 90

#### 触摸与手势事件
+ 触摸事件
  + `touchstart`
  + `touchmove`
  + `touchend`
  + `touchcancel`
+ 手势事件
  + `gesturestart`
  + `gesturechange`
  + `gestureend`

### 内存和性能
#### 事件委托

例如在`<ul>`为添加一个`click`事件，所有`<li>`子元素点击事件都会冒泡到`<ul>`上。


## 表单脚本
---

### 表单基础知识

#### 提交表单
```
<input type="submit" value="Submit Form">
```

#### 重置表单
```
<input type="reset" value="Reset Form">
```

#### 表单字段
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

### 文本框脚本
#### 过滤输入
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

#### HTML5约束验证API
##### 输入模式

HTML5为文本字段新增了`pattern`属性。
```
<input type="text" pattern="\d+" name="count">
```

##### 检测有效性

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

##### 禁用验证
```
<form method="post" action="signup.php" novalidate>
	<!--这里插入表单元素-->
</form>
```

## HTML5 脚本编程
---

### 跨文档消息传递
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

### 原生拖放

#### 拖放事件

拖动某个元素时，将依次触发的事件：
+ `dragstart`
+ `drag`
+ `dragend`

当某个元素被拖动到一个有效的放置目标时，会依次触发下列事件：
+ `dragenter`
+ `dragover`
+ `dragleave`（离开）或`drag`（放进去了）

#### dataTransfer对象

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

#### drapEffect 与 effectAllowed

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

#### 可拖动
HTML5为所有元素规定了`draggable`属性，表示元素是否可以拖动。只有图像和链接的`draggable`默认是`true`
```
<!-- 让这个图像不可以拖动 -->
<img src="smile.gif" draggable="false" alt="Smiley face">
<!-- 让这个元素可以拖动 -->
<div draggable="true">...</div>
```

#### 其他成员
HTML5规定了`dateTransfer`对象还应该包含下列方法和属性。
+ `addElement(element)`
+ `clearData(format)`
+ `setDragImage(element, x, y)`
+ `type`

## 错误处理与调试
---

### 错误处理

#### try-catch 语句
```
try {
	// 可能出错的代码
} catch (err) {
	// 处理发生的错误
}
```
##### `finally`子句

只要代码中包含`finially`子句，无论`try`还是`catch`语句中的`return`语句都将被忽略。

##### 错误类型
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

#### 抛出错误
与`try-catch` 语句相配的还有一个`throw` 操作符，用于随时抛出自定义错误。抛出错误时，必须要给`throw` 操作符指定一个值，这个值是什么类型，没有要求。
```
throw 12345;
throw "Hello world!";
throw true;
throw { name: "JavaScript"};
```
遇到`throw`操作符时，代码会立即停止执行。只有当`try-catch`语句捕获到被抛出值，代码才会继续执行

##### 自定义错误类型

可以利用原型链通过继承`Error`创建自定义错误类型。需要为新创建的错误类型指定`name`和`message`属性
```
function CustomError (message) {
	this.name = 'CustomError';
	this.message = message;
}

CustomError.prototype = new Error();

throw new CustomError('Error msg');
```

#### Error事件
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

#### 常见的错误类型

+ 类型转换错误
+ 数据类型错误
+ 通信错误

在数据检测的时候，基本类型的值应该使用`typeof`来检测，对象的值应该使用`instanceof`。

## JSON
---

### 解析与序列化

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

#### 序列化选项
`JSON.stringify()`除了要序列化的`JS`对象外，还可以接受两个参数，一个是过滤器（数组或函数），第二个参数是一个选项，表示是都在`JSON`字符串中保留缩进。

##### 过滤结果

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

##### 字符串缩进

`JSON.stringify()`方法的第三个参数用于控制结果中的缩进和空白符。可以是数字，表示缩进的空格数；也可以是字符串，将该字符串作为缩进的表示。

##### `toJSON()`方法

#### 解析选项
`JSON.parse()`方法也可以接受第二参数，该参数是一个函数（被称为还原函数），传入函数的参数均为`key, value`。

如果还原函数返回`undefined`，则表示要从结果中删除响应的键。

## Ajax与Comet
---

### XMLHttpRequest 对象

#### XHR的用法
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


#### HTTP 头部信息
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

#### GET请求
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

#### POST请求
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

### XMLHttpRequest 2 级

#### FormData
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

#### 超时设定
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

#### overrideMimeType()方法
用于重写`XHR`响应的`MIME`类型。因为返回响应的`MIME` 类型决定了`XHR` 对象如何处理它，所以提供一种方法能够重写服务器返回的`MIME` 类型是很有用的。
```
var xhr = createXHR();
xhr.open("get", "text.php", true);
xhr.overrideMimeType("text/xml");
xhr.send(null);
```

#### 进度事件
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

### 跨资源共享

#### IE对CORS的实现

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

#### 其他浏览器对CORS的实现
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

### 其他跨域技术

#### 图像Ping
```
var img = new Image();
img.onload = img.onerror = function(){
	alert("Done!");
};
img.src = "http://www.example.com/test?name=Nicholas";
```

#### JSONP
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

#### 服务器发送事件

**SSE**支持短轮询、长轮训和HTTP流，而且能在断开连接时自动确定何时重新连接。

##### SSE API
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

#### Web Sockets
由于 Web Sockets 使用了自定义的协议,所以 URL 模式也略有不同。未加密的连接不再是 http:// ,而是 ws:// ;加密的连接也不是 https:// ,而是 wss:// 。
##### Web Sockets API
创建一个WebSockets实例对象：
```
var socket = new WebSocket("ws://www.example.com/server.php");
```
WebSocket也有一个表示当前状态的`readyState`属性：
+ `WebSocket.OPENING (0)` ：正在建立连接
+ `WebSocket.OPEN (1)`：已经建立连接
+ `WebSocket.CLOSING (2)`：正在关闭连接
+ `WebSocket.CLOSE (3)`：已经关闭连接

##### 发送和接收数据

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

##### 其他事件

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

## 高级技巧
---

### 高级函数

#### 安全的类型检测
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

#### 作用域安全的构造函数
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

#### 惰性载入函数
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
#### 函数绑定
`bind()`函数，语法如下：
```
function bind (fn, context) {
	return function () {
		return fn.apply(context, arguments);
	}
}
```

## 离线应用与客户端存储
---

### 离线检测
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

### 数据存储

#### Web存储机制
Web Storage规范包含两种对象的定义：`sessionStorage`和`globalStorage`。这两个对象在支持的浏览器中都是以`windows`对象属性的形式存在。

##### `Storage`类型

`Storage`类型提供最大的存储空间来存储名值对。
+ `clear()`：删除所有值
+ `getItem(name)`：根据指定的名字`name`获取对应的值
+ `key(index)`：获得`index`位置处的值的名字
+ `removeItem(name)`：删除由`name`指定的名值对
+ `setItem(name, value)`：为指定的`name`设置一个对应的值

##### `sessionStorage`对象

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

##### `globalStorage`对象

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

##### `localStorage`对象

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

##### `storage`事件

对`Storage`对象进行任何修改，都会在文档上触发`storage`事件。这个事件的`event`对象有以下属性。
+ `domain`：发生变化的存储空间的域名
+ `key`：设置或删除的键名
+ `newValue`：如果是设置值，则是新值；如果是删除键，则是`null`
+ `oldValue`：键被更改之前的值

在这四个属性中，IE8 和Firefox 只实现了`domain` 属性。在撰写本书的时候，WebKit 尚不支持
`storage` 事件


#### IndexedDB
Indexed Database API，简称**IndexedDB**，是在浏览器中保存结构化数据的一种数据库。**IndexedDB**设计的操作完全是异步进行。
```
var indexedDB = window.indexedDB || window.msIndexedDB || window.mozIndexedDB || window.webkitIndexedDB;
```
##### 数据库

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

##### 对象存储空间

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

##### 事务

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

##### 键范围

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

## 新型的API
---

### Page Visibility API
Page Visibility API 是为了让开发人员知道页面是否对用户可见推出的。
+ `document.hidden`：表示页面是否隐藏的布尔值。
+ `document.visibilityState`
	+ 页面在后台标签页中或浏览器最小化
	+ 页面在前台标签页中
	+ 实际的页面已经隐藏，但用户可以看到页面的预览
	+ 页面在屏幕外执行预渲染处理
+ `visibilitychange`事件：当文档可见性发生改变时，触发该事件。

### Geolocation API
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

### File API
File API 在表单中的文件输入字段的基础上，又添加了一些直接访问文件信息的接口。HTML5在DOM中为文件输入元素添加了一个`files`集合。每个**File**对象都有下列只读属性。
+ `name`：本地文件系统的文件名
+ `size`：文件的字节大小
+ `type`：字符串，文件的MIME类型
+ `lastModifiedDate`：字符串，文件上一次修改的时间

#### FileReader 类型
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

#### 读取拖放的文件
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

#### 使用XHR上传文件
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
