---
title: Promise深入理解
date: 2017-12-07 16:48:57
tags: JavaScript
---

## 什么是**Promise**

**Promise**的核心理念是一个异步操作的结果，**Promise**包含两部分
+ `[[PromiseStates]]`
+ `[[PromiseValue]]`

**Promise**状态三种可能：
+ `pending`: 悬而未决
+ `resolved`: 决定
+ `rejected`: 拒绝

{% asset_img state.png Promise状态%}

<!-- more -->

## 异步性

```
const p = new Promise((resolve, reject) => {
  resolve('success');
});

p.then((value) => {
  console.log(value);
});

console.log('Called first ?');
```
**Promise**的`then()`具有异步性，当执行到`.then()`部分，这部分会自动进入到**Promise**的异步事件队列，不会阻塞同步代码的执行，所以`Called first?`先输出。

> 输出结果

```
Called first ?
success
```

## 立即执行性

```
const p = new Promise((resolve, reject) => {
  console.log('Create a promise');
  resolve('success');
});

p.then((value) => {
  console.log(value);
});

console.log('After new Promise');
```

从**Promise**的**异步性**，我们可以推断出，`After new Promise`，会先于`then()`方法中的输出。同时**Promise**的**立即执行性**，定义了`promise`定义的同时就会立即执行，并不依赖于`then()`的调用。而且与函数明显不同，函数需要主动调用才会执行。

> 输出结果

```
Create a promise
After new Promise
success
```

## Promise的状态

```
const p1 = new Promise((resolve, reject) => {
  resolve(1);
});

const p2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(2);
  }, 500);
});
const p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(3);
  }, 500);
});

console.log(p1);
console.log(p2);
console.log(p3);

setTimeout(() => {
  console.log('p2-setTimeout:', p2);
}, 501);

setTimeout(() => {
  console.log('p3-setTimeout:', p3);
}, 501);

p1.then((value) => {
  console.log('p1-then:', value);
});

p2.then((value) => {
  console.log('p2-then:', value);
});

p3.catch((err) => {
  console.log('p3-catch', err);
});

```

当**Promise**创建完成时，处于 *pending* 状态；
当**Promise**执行了`resolve`方法，**Promise**对象的状态会变成 *resolved* 状态；
当**Promise**执行了`reject`方法，**Promise**对象的状态会变成 *rejected* 状态；

{% asset_img timeline.png 执行顺序%}

> 先输出

```
Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 1}
Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```

>然后输出

```
p1-then: 1
```
500ms之后，`p2`和`p3`的**Promise**状态被执行，`then`被触发，输出：
```
p2-then: 2
p3-catch 3
```

> 最后会输出:

```
p2-setTimeout: Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 2}
p3-setTimeout: Promise {[[PromiseStatus]]: "rejected", [[PromiseValue]]: 3}
```

## 状态不可逆性

```
const p1 = new Promise((resolve, reject) => {
  resolve('p1 success 1');
  resolve('p1 success 2');
});

const p2 = new Promise((resolve, reject) => {
  resolve('p2 success');
  reject('p2 reject');
});

p1.then((value) => {
  console.log(value);
});

p2.then((value) => {
  console.log(value);
}, (err) => {
  console.log(err);
});
```
**Promise**一旦变成`resolved`或是`rejected`，这个状态就不能再次变化，这就是**Promise**的不可逆性。

> 输出

```
p1 success 1
p2 success
```

## 链式调用

```
const p = new Promise(((resolve, reject) => {
  resolve(1);
}));

p.then((value) => {
  console.log(value);
  return value * 2;
}).then((value) => {
  console.log(value);
}).then((value) => {
  console.log(value);
  return Promise.resolve('resolve');
}).then((value) => {
  console.log(value);
  return Promise.reject('reject');
}).then((value) => {
  console.log('resolve: ', value);
}, (err) => {
  console.log('reject: ', err);
});
```

**Jquery**对象链式调用是执行`jquery`方法之后，会继续返回`jquery`对象；类似这个原理，**Promise**对象的`then`方法会返回一个新的`Promise对象`，这样就可以继续调用`then`方法。同样`then`方法中的两个参数还是`fulfilled`对象和`rejected`对象。

{% asset_img chain.png 链式调用 %}

当`return`一个值或者`Promise.resolve()`时，状态为 *resolved*
当`throw`一个异常或者`return` `Promise.reject()`,状态为 *rejected*

**注**: 当`then()`方法中没有`return`时，会默认返回`undefined`，状态为 *resolved*

> 输出

```
1
2
undefined
resolve
reject:  reject
```

## Promise中的异常处理

```
const p1 = new Promise((resolve, reject) => {
  foo.bar();
  resolve(1);
});

p1.then((value) => {
  console.log('p1 then value: ', value);
}, (err) => {
  console.log('p1 then err: ', err);
}).then((value) => {
  console.log('p1 then then value: ', value);
}, (err) => {
  console.log('p1 then then err: ', err);
});

var p2 = new Promise((resolve, reject) => {
  resolve(2);
});

p2.then((value) => {
  console.log('p2 then value: ', value);
  foo.bar();
}, (err) => {
  console.log('p2 then err: ', err);
}).then((value) => {
  console.log('p2 then then value: ', value);
}, (err) => {
  console.log('p2 then then err: ', err);
  return 1;
}).then((value) => {
  console.log('p2 then then then value: ', value);
}, (err) => {
  console.log('p2 then then then err: ', err);
});
```

**Promise**中的异常会交给`then`方法中的第二个回调函数处理，一旦处理完成，会继续返回一个`Promise`对象给后续`then`方法。

可以看到输出是`p1`和`p2`交替输出的，这个并不一定是交替输出，取决于执行情况，也可能是`p2`先输出。

> 输出

```
p1 then err:  ReferenceError: foo is not defined
p2 then value:  2
p1 then then value:  undefined
p2 then then err:  ReferenceError: foo is not defined
p2 then then then value:  1
```
