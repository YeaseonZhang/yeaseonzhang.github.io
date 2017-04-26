---
title: JS红宝书 -- 读书笔记
date: 2017-04-26 17:44:41
tags: JavaScript 笔记
---

{% asset_img book.jpg JavaScript高级程序设计 %}

花了半个多月的时间，终于又把“JS红宝书”又撸了一遍。

第一次读“JS红宝书”还是**2015**年初学JS的时候，那时候只是把语法部分读了一遍，还有一些浏览器相关知识做了下了解，大概也就读了版本的样子，
就开始了用JS进行开发了，在成长的道路上遇见了**JQuery**，当时真的是感觉到**JQuery**太友好了，慢慢放下了原生开发。

现在呢，从当初的一只小菜鸟慢慢变成了大菜鸟，越来越觉得自己的JS基础很缺乏，然后就开启了“JS红宝书”二刷之路。

下面就书中自己觉得重要的、没有掌握的知识整理出来。因为我觉得还是会三刷“JS红宝书”，希望把这本700多页的书越读越薄，勉励。

<!-- more -->

### 在HTML中使用JavaScript

#### `async`
加载外部脚本文件，通知浏览器立即下载，异步执行。

#### `noscript`
`noscript`标签显示条件：
+ 浏览器不支持脚本
+ 浏览器支持脚本，但是脚本被禁用

### 基本概念

#### 标识符
+ 第一个字符必须是一个字母、下划线或者一个美元符号
+ 其他字符可以是字母、下划线、美元或者数字

#### 严格模式
支持严格模式的浏览器包括：IE10+、Firefox4+、Safari 5.1+、Opera 12+和Chrome。

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

#### 操作符
+ `++` or `--`

前置与后置的区别
```
var num1 = 2;
var num2 = 20;
var num3 = --num1 + num2;	// 21
var num4 = num1 + num2;		// 21

var num5 = 2;
var num6 = 20;
var num7 = num1-- + num2;	// 22
var num8 = num1 + num2;		// 21
```

+ 一元加操作符用于强制类型转换，隐式`Number()`效果

#### for-in 语句

`for-in`语句是一种精确的迭代语句，可以用来枚举对象的属性。

通过`for-in`循环输出的属性名的顺序是不可预测的。

如果要迭代的对象的变量值为`null`或`undefined`，`for-in`语句会抛出错误。**ES5**更正了这一行为，不再抛出错误，只是不再执行循环体。

**建议**：在是使用`for-in`循环之前，先检查对象值是不是`null`或者`undefined`。

### 变量、作用域和内存问题

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

#### 小结

+ 基本类型值在内存中占据固定大小的空间，因此被保存在栈内存中；
+ 从一个变量向另一个变量复制基本类型的值，会创建这个值的一个副本；
+ 引用类型的值是对象，保存在堆内存中；
+ 包含引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针；

### 引用类型

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

#### `*`splice方法

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
#### RegExp类型

##### 正则表达式中的元字符
`( [ { \ ^ $ | ) ? * + . ] }`

**注**：匹配元字符必须转义

##### `RegExp` 构造函数
接受两个参数： 一个是要匹配的字符串模式，另一个是可选的标志字符串。
```
var pattern1 = /[bc]at/i;
// 等价于
var pattern2 = new RegExp('[bc]at', 'i');
```
**注**：由于RegExp构造函数的模式是字符串，所以在某些情况下要对字符串进行双重转义，所有元字符都必须双重转义。例如`\n`在字符串中被转义为`\\n`，而在正则表达式字符串中就会变成`\\\\n`。

#### `RegExp`实例方法

+ `exex()`方法

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

+ `test()`方法

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

#### 基本包装类型

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
1. 字符方法
+ `charAt()`
+ `charCodeAt()`
```
var stringValue = "hello world";

alert(stringValue.charAt(1)); //"e"
alert(stringValue.charCodeAt(1)); //输出字符编码"101"
```
2. 字符串操作方法
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

3. 字符串位置方法
+ `indexOf()`
+ `lastIndexOf()`

两个方法的第二个参数，表示从字符串中哪个位置开始搜索。

4. trim()方法

**ECMAScript 5**方法

5.  字符串转换大小写方法
+ `toLowerCase()`
+ `toLocaleLowerCase()`
+ `toUpperCase()`
+ `toLocaleUpperCase()`

6. 字符串的模式匹配方法
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
| `$`` | 匹配的子字符串之后的字符串。 RegExp.rightContext |
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

7. localeCompare()方法

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

8. fromCharCode()方法

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

### 面向对象的程序设计

#### 属性类型
1. 数据类型
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

2. 访问器属性
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


#### 创建对象

##### 工厂模式
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

##### 构造函数模式
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

##### 原型模式
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

##### 结合使用构造函数和原型模式
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

#### 继承
构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型的内部指针。

##### 原型链
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

{% asset_img prototype-4.png %})

+ `instanceof`操作符

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

### 函数表达式

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

##### 函数作用域链
当某个函数被调用时，会创建一个执行环境及相应的作用域链。然后，使用`arguments`和其他命名参数的值来初始化函数的活动对象。在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位...，直到作用域终点的全局执行环境。


##### 闭包
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

##### 块级作用域
```
(function () {
	// 这里是块级作用域
})();
```
