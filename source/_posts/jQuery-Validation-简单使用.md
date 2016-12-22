---
title: jQuery Validation 简单使用
date: 2016-12-22 17:46:19
tags: jQuery Plugin
---

`jQuery.validation`是一个十分易用的表单验证插件，官方文档也比较详细。很适合刚刚上手`jQuery`的web前端开发者，接下来介绍我个人的使用心得。

<!--more-->

### 相关资料
+ [官网](https://jqueryvalidation.org/)
+ [Github](https://github.com/jzaefferer/jquery-validation)
+ [菜鸟教程](http://www.runoob.com/jquery/jquery-plugin-validate.html)

### 使用方法
前面的资料中也有很详尽的使用方法，可以看完我写的作为深入学习的资料。

#### 引入`jquery.validate.js`，还需要引入有依赖关系的`jquery.js`
```
<script src="yourDir/jquery.js"></scirpt>
<script src="yourDir/jquery.validate.js"></scirpt>
```

#### 将校验规则写到JS文件中
```
$('#yourForm').validate({
	rules: {
		key1: {
			validateMethod1: true,
			validateMethod2: false,
			...
		},
		key2: "validateMethod3"
	},
	messages: {
		key1: {
			validateMethod1: "Tips1",
			validateMethod2: "Tips2"
		},
		key2: "Tips3"
	}
})
```
以上就是最基本的一个表单验证的雏形了，也很容易看懂。不过还是稍微说一下，为`<form id="yourForm"></form>`绑定一个表单验证。
```
$('#yourForm').validate({
	// do something you want
})
```
和其他`jQuery`插件一样，在上面函数内部自定义你的配置。
最基本的有`rules、messages`，分别是**验证规则**，和验证未通过时的**提示信息**。
+ `key` 就是表单元素的`name`值
+ `rules`中的`vaildateMethod`可以设置为`true/false`来设置开启/关闭该方法的验证。如果该表单元素只有一个规则，也可以像`key2`那样直接指出。
+ `messages`将对应规则的**提示信息**列出来，就可以在验证未通过时提示用户。

#### 官方提供的`validateMethod`能满足基本的开发需要。

+ `required`: 必须输入的字段。
+ `remote:"check.php"`: 使用 ajax 方法调用 check.php 验证输入值。
+ `email`: 必须输入正确格式的电子邮件。
+ `date`: 必须输入正确格式的日期。日期校验 ie6 出错，慎用。
+ `dateISO`: 必须输入正确格式的日期（ISO），例如：2009-06-23，1998/01/22。只验证格式，不验证有效性。
+ `number`: 必须输入合法的数字。
+ `digits`: 必须输入整数。
+ `creditcard`: 必须输入合法的信用卡号。
+ `equalTo:"#field"`: 必输入值必须和 `#field` 相同。

还有一些对于输入长度的，在此就不提及了。
也可以使用`jQuery.validator.addMethod()`添加自定义验证方法[API](https://jqueryvalidation.org/jQuery.validator.addMethod/)，接下来详细介绍。

#### 其他配置
除了`rules`、`messages`还有其他一些常用的配置项。
```
$('#addBookmarkForm').validate({
    rules: {
        //
    },
    messages: {
        //
    },

    errorClass: 'modalError',
    errorElement: 'div',
    errorPlacement: function (error, element) {
        error.insertAfter(element.parent());
    },

    submitHandler: function () {
        // Your method
    }
})
```
+ `errorClass`:  错误信息的类名。
+ `errorElement`: 修改错误信息提示消息的标签，默认是`<label>`。
+ `errorPlacement`: 修改错误信息显示的位置。这是一个回调函数，`error`参数也就是`errorElement`，`element`参数是验证的表单。通过修改插入的位置，实现不同的样式效果。
+ `submitHandler`: 当所有验证规则通过后执行的回调函数，做ajax还是别的什么，取决于你的业务场景。

#### 触发方式
说了这么半天，还没有谈到怎么触发表单验证，其实很简单就是`submit`，你可以用多种方式实现`submit`。
+ `type="submit"`的`<button>`或者`<input>`
+ `$('#yourForm').submit()`

#### 增加自定义验证规则
先贴一段我自己业务需要的验证规则
```
validateMethod: function () {
  var regEx1 = new RegExp(/[&\(\\\"\'\%\)]/);
   var regEx2 = new RegExp(/^(http|https):\/\/.{1,250}/);
   var regEx3 = new RegExp(/^\/\/\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\/.{1,255}$/);
   var regEx4 = new RegExp(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/);

   jQuery.validator.addMethod('bookmarkRule1', function (value, element, parans) {
       return this.optional( element ) || !regEx1.test(value);
   }, 'URL should not include char & ( ) \\ % \" \' ');

   jQuery.validator.addMethod('bookmarkRule2', function (value, element, parans) {
       if ($('#type').val() == '0') {
           return this.optional( element ) || regEx2.test(value);
       } else {
           return true;
       }
   }, 'Please input correct Web URL!');  

   jQuery.validator.addMethod('bookmarkRule3', function (value, element, parans) {
       if ($('#type').val() == '1') {
           return this.optional( element ) || regEx3.test(value);
       } else {
           return true;
       }
   }, 'Please input correct File URL!');  

   jQuery.validator.addMethod('bookmarkRule4', function (value, element, parans) {
       if ($('#type').val() == '2') {
           return this.optional( element ) || regEx4.test(value);
       } else {
           return true;
       }
   }, 'Please input correct Desktop!');
}
```
我相信你根据**API**，就能写出自己需要的验证规则。

### 结语
希望看完本文章对你能有所帮助。