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

```javascript
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

```javascript
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

```javascript
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

```javascript
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

```javascript
p2-setTimeout: Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 2}
p3-setTimeout: Promise {[[PromiseStatus]]: "rejected", [[PromiseValue]]: 3}
```

## 状态不可逆性

```javascript
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

```javascript
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
reject: reject
```

## Promise中的异常处理

```javascript
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

## Promise.resolve()

`Promise.resolve()`语法：

```javascript
Promise.resolve(value);
```

+ `value`: 用来解析待返回`promise`对象的参数，既可以是一个`promise`对象，也可以是一个`thenable`（即带有`then`方法）。

```javascript
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(p1);
const p3 = new Promise((resolve, reject) => {
  resolve(1);
});

const p4 = new Promise((resolve, reject) => {
  resolve(p1);
});

// console.log(p1);
// console.log(p2);
// console.log(p3);
// console.log(p4);

console.log(p1 === p2);
console.log(p1 === p3);
console.log(p1 === p4);
console.log(p3 === p4);

p4.then((value) => {
  console.log('p4 = ', value);
});

p2.then((value) => {
  console.log('p2 = ', value);
});

p1.then((value) => {
  console.log('p1 = ', value);
})
```

`p1`: 接收了一个普通值`1`，所以会返回一个`resolved`状态的**Promise**对象，并且值为`1`。
`p2`: 接收了一个promise对象`p1`，会直接返回这个promise对象。
`p3`和`p4`: 通过`new`方式创建了一个新的promise对象。

所以，`p1 === p2`,`p3`和`p4`都是创建的新对象，所以自身和其他三个对象都不相等。

> 输出
```
true
false
false
false
```

但是后三个输出是：

```
p2 = 1
p1 = 1
p4 = 1
```
很有意思的是，明明是`p4`先执行的`then`方法，但是却是后输出的。

在定义完4个promise对象时，状态分别为：
```javascript
Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 1}
Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 1}
Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: 1}
Promise {[[PromiseStatus]]: "pending", [[PromiseValue]]: undefined}
```
很明显，`p4`是`pending`状态，与其他三个不同，因为`p4`的`resolve`中接收的参数是一个promise对象`p1`，`resolve`会对`p1`进行“拆箱”操作，这个过程是异步的。

**注**：把基本数据类型转换为对应的引用类型的操作称为装箱，把引用类型转换为基本的数据类型称为拆箱。

## resolve() & reject() 的区别

```JavaScript
const p1 = new Promise((resolve, reject) => {
  resolve(Promise.resolve('resolve'));
});

const p2 = new Promise((resolve, reject) => {
  resolve(Promise.reject('reject'));
});

const p3 = new Promise((resolve, reject) => {
  reject(Promise.resolve('resolve'));
});

p1.then((value) => {
  console.log('p1-resolve:', value);
}, (err) => {
  console.log('p1-reject:', err);
});

p2.then((value) => {
  console.log('p2-resolve:', value);
}, (err) => {
  console.log('p2-reject:', err);
});

p3.then((value) => {
  console.log('p3-resolve:', value);
}, (err) => {
  console.log('p3-reject:', err);
});
```

`resolve`方法和`reject`方法除了在状态上有区别，处理方式上也有区别，`resolve`方法上面提到了会对**promise对象**“拆箱”，但是`reject`方法不会。

`p3`没有“拆箱”操作，所以会最先输出，直接调用`reject`方法，输出`Promise.resolve('resolve')`对象
`p1`会“拆箱”得到`Promise.resolve('resolve')`这个**promise对象**的状态和值，调用`resolve`方法。
`p2`会“拆箱”得到`Promise.reject('reject')`这个**promise对象**的状态和值，因为得到的状态是`rejected`，所以会调用`reject`方法。

> 输出

```JavaScript
p3-reject: Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "resolve"}
p1-resolve: resolve
p2-reject: reject
```

## all() & race() & then() 区别

`Promise.all()`语法：

```javascript
Promise.all(iterable);
```

`Promise.race()`语法：

```javascript
Promise.race(iterable)
```

+ `iterable`: 可迭代对象，例如一个数组。

```javascript
let timerPromisefy = (delay) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(delay);
    }, delay);
  });
}

let timerPromisefyReject = (delay) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(delay);
    }, delay);
  });
}

console.time('Promise all');
Promise.all([
  timerPromisefy(1),
  timerPromisefy(7),
  timerPromisefy(10),
  timerPromisefy(9)
]).then((value) => {
  console.timeEnd('Promise all');
});

console.time('Promise then');
timerPromisefy(1).then(() => {
  return timerPromisefy(7)
}).then(() => {
  return timerPromisefy(10)
}).then(() => {
  return timerPromisefy(9)
}).then(() => {
  console.timeEnd('Promise then')
});

console.time('Promise race');
Promise.race([
  timerPromisefy(1),
  timerPromisefy(7),
  timerPromisefy(10),
  timerPromisefy(9)
]).then(value => {
  console.timeEnd('Promise race');
});
```

`Promise.all()`方法返回一个`Promise`，当`iterable`参数中的 *promise* 并行执行，当所有 *promise* 都已经 *resolve* 了，返回 *resolved* 状态。当传递的 *promise* 包含一个 *reject* ,则返回 *rejected* 状态。
如果`Promise.all()`返回 *resolved* , 那么执行时间取决于执行最最慢的那个 *promise*；如果`Promise.all()`返回 *rejected* , 执行时间取决于第一个返回 *rejected* 的执行时间。

`Promise.race()`方法返回一个`Promise`，当`iterable`参数中只要有一个 *promise* 状态被判定了，那么就返回该状态。
所以`Promise.race()`的执行时间取决于执行最快的那个 *promise*。

`Promise.then()`方法的执行时间，是每个链式调用总时间之和。

> 输出
```
Promise race: 2.3232421875ms
Promise all: 3.675048828125ms
Promise then: 31.32373046875ms
```
