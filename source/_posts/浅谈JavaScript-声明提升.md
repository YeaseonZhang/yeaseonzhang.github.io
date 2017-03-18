---
title: 浅谈JavaScript--声明提升
date: 2017-02-28 20:06:37
tags: JavaScript
---

**JavaScript**是一种动态语言，不同于**C**、**Java**等静态语言先编译后执行，

所以代码中的执行顺序并不像你看到的那样执行，有个词你需要知道**声明提升**，

下面我们就来聊聊声明提升。

<!-- more -->

### 抛砖
```
a = 1;
var a;
console.log(a); // 1 
```
结果是`1`，不是`undefined`。虽然在`var a;`在`a = 1;`后面，但是存在声明提升，等价于：
```
var a;
a = 1;
console.log(a); 
```
### 再抛一块
```
console.log(a); // undefined
var a = 1;
```
为什么这次结果就是`undefined`了，声明同样提升了，但是。。。等下再告诉你，上面的代码等价于：
```
var a;
console.log(a); // undefined
a = 2;
```
也就是说声明虽然提升了，但是赋值操作（执行）被留在了本身的位置。

### 原理

引擎在对解释js的代码的时候，首先进行的是编译。
找到所有的声明，并用合适的作用域把它们关联起来。

so，`var a = 2`，js会将其看成两个声明`var a; a = 2;`

### 第三块砖
```
foo();

function foo() {
  console.log(a); // undefined
  var a = 2;
}
```
这个就很好理解了吧，那么你回答下一个
```
foo();

var foo = function bar() {
  console.log(a);
  var a = 2;
}
```
先别说你的答案，估计你也猜错了，不是`undefined`也不是`2`；

因为还没执行到`bar()`，`foo()`就已经报错了`TypeError`，函数声明可以提升，但是函数表达式的声明不能提升。

即使是具名函数表达式，在名称标识符在赋值之前也无法在作用域中使用：
```
foo();	// TypeError;
bar();	// ReferenceError;

var foo = function bar() {
  console.log(a);
  var a = 2;
}
```
其实经过提升之后，代码变成了：
```
var foo;

foo();	// TypeError;
bar();	// ReferenceError;

foo = function () {
  var bar = function () {
    console.log(a);
    var a = 2;
  }
}
```

### 优先原则
```
foo();	// 1

var foo;

function foo () {
  console.log(1);
}

foo = function () {
  console.log(2);
}
```
输出的是1，而不是2。
```
var foo;
function foo () {...}
```
这两个都是声明，但是在声明中函数会首先被提升(`var foo`同时被忽略了)。也就变成了：
```
function foo() {
  console.log(1);
}
foo();	// 1

foo = function () {
  console.log(2);
}
```

**注**： 重复声明，后面函数声明会覆盖前面的。
```
foo();	// 3

function foo() {
  console.log(1);
}

foo = function () {
  console.log(2);
}

function foo() {
  console.log(3);
}
```

### 总结

为了避免踩坑，我们要做到**先声明，避免重复声明**。

> 参考：《你不知道的JavaScript》
