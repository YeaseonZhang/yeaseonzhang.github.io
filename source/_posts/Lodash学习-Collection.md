---
title: Lodash学习-Collection
date: 2017-01-18 16:44:19
tags: Lodash
---

上篇文章，我们已经把关于Array部分的API学习了一遍，现在我们来开始下一篇**Collection**。

<!-- more -->

下列API的**`collection`**参数指代`Array | Object`两种类型。

### `_.countBy`
+ `_.countBy(collection, [iteratee=_.identity])`

创建一个`key-value`的对象，`key`是通过将`collection`按照`iteratee`规则迭代得到的，对应的`value`则是，这个`key`值出现了N次，`value`就是N。也就是迎合了API的**count**的意思。

例子：
```
_.countBy([6.1, 4.2, 6.3], Math.floor);
// => { '4': 1, '6': 2 }
 
// The `_.property` iteratee shorthand.
_.countBy(['one', 'two', 'three'], 'length');
// => { '3': 2, '5': 1 }
```
解释第一个例子吧，`[6.1, 4.2, 6.3]`分别执行`Math.floor`得到`[6, 4, 6]`,其中`4`出现1次，`6`出现2次,所以结果是`{ '4': 1, '6': 2 }`。

### `_.every`
+ `_.every(collection, [predicate=_.identity])`

如果`collection`全部元素满足`predicate`条件，返回`true`；否则只要出现不满足的，就返回`false`。`predicate`调用三个参数`value, index, array`。

例子：
```
_.every([true, 1, null, 'yes'], Boolean);
// => false
 
var users = [
  { 'user': 'barney', 'age': 36, 'active': false },
  { 'user': 'fred',   'age': 40, 'active': false }
];
 
// The `_.matches` iteratee shorthand.
_.every(users, { 'user': 'barney', 'active': false });
// => false
 
// The `_.matchesProperty` iteratee shorthand.
_.every(users, ['active', false]);
// => true
 
// The `_.property` iteratee shorthand.
_.every(users, 'active');
// => false
```
### `_.filter`
+ `_.filter(collection, [predicate=_.identity])`

遍历`collection`全部元素，返回满足`predicate`条件的元素组成的新数组。`predicate`调用三个参数`value, index, array`。

例子:
```
var users = [
  { 'user': 'barney', 'age': 36, 'active': true },
  { 'user': 'fred',   'age': 40, 'active': false }
];
 
_.filter(users, function(o) { return !o.active; });
// => objects for ['fred']
 
// The `_.matches` iteratee shorthand.
_.filter(users, { 'age': 36, 'active': true });
// => objects for ['barney']
 
// The `_.matchesProperty` iteratee shorthand.
_.filter(users, ['active', false]);
// => objects for ['fred']
 
// The `_.property` iteratee shorthand.
_.filter(users, 'active');
// => objects for ['barney']
```

### `_.find`
+ `_.find(collection, [predicate=_.identity], [fromIndex=0])`

与上面`_.filter`不同的是`_.find`只返回第一个匹配的元素，可以通过`fromIndex`指定查找位置，默认`fromIndex=0`。如果没有匹配的，返回`undefined`。

例子：
```
var users = [
  { 'user': 'barney',  'age': 36, 'active': true },
  { 'user': 'fred',    'age': 40, 'active': false },
  { 'user': 'pebbles', 'age': 1,  'active': true }
];
 
_.find(users, function(o) { return o.age < 40; });
// => object for 'barney'
 
// The `_.matches` iteratee shorthand.
_.find(users, { 'age': 1, 'active': true });
// => object for 'pebbles'
 
// The `_.matchesProperty` iteratee shorthand.
_.find(users, ['active', false]);
// => object for 'fred'
 
// The `_.property` iteratee shorthand.
_.find(users, 'active');
// => object for 'barney'
```

### `_.findLast`
+ `_findLast(collection, [predicate=_.identity], [fromIndex=collection.length-1])`

`_.findLast`与`_find`方法不同的是从右到左查找，`fromIndex`默认值`collection.length-1`。

例子:
```
_.findLast([1, 2, 3, 4], function(n) {
  return n % 2 == 1;
});
// => 3
```

### `_flatMap`
+ `_.flatMap(collection, [iteratee=_.identity])`

`collection`全部元素迭代执行`iteratee`，将得到的元素组合成一个**flattened**数组（我理解的就是变成N-1维数组），`iteratee`调用三个参数`value, index|key, collection`。

例子：
```
function duplicate(n) {
  return [n, n];
}
 
_.flatMap([1, 2], duplicate);
// => [1, 1, 2, 2]
```
解释一下，`[1, 2]`调用`duplicate`得到`[[1, 1], [2, 2]]`，通过`_.flatMap`使其扁平化`[1, 1, 2, 2]`。

### `_.flatMapDeep`
+ `_.flatMapDeep(collection, [iteratee=_.identity])`

这个方法是`_.flatMap`的升级版，会把多维数组变成一维数组。

例子：
```
function duplicate(n) {
  return [[[n, n]]];
}
 
_.flatMapDeep([1, 2], duplicate);
// => [1, 1, 2, 2]
```

### `_.flatMapDepth`
+ `_.flatMapDepth(collection, [iteratee=_.identity], [depth=1])`

这个是可以指定降维次数`_.flatMapDeep`的版本。`depth`降维次数默认为1。

例子：
```
function duplicate(n) {
  return [[[n, n]]];
}
 
_.flatMapDepth([1, 2], duplicate, 2);
// => [[1, 1], [2, 2]]
```

### `_.forEach`
+ `_.forEach(collection, [iteratee=_.identity])`

对`collection`每个元素执行`iteratee`方法，`iteratee`可以调用三个参数`value, index|key, collection`，当`collection`是数组时第二个参数为`index`，当`collection`是对象时第二个参数为`key`。
`iteratee`函数可以通过显式返回`false`提前退出迭代。

返回值：返回`collection`本身

例子：
```
_.forEach([1, 2], function(value) {
  console.log(value);
});
// => Logs `1` then `2`.
 
_.forEach({ 'a': 1, 'b': 2 }, function(value, key) {
  console.log(key);
});
// => Logs 'a' then 'b' (iteration order is not guaranteed).
```

### `_.forEachRight`
+ `_.forEachRight(collection, [iteratee=_.identity])`

和`_.forEach`方法的区别，`collection`元素从右到左执行`iteratee`。

例子：
```
_.forEachRight([1, 2], function(value) {
  console.log(value);
});
// => Logs `2` then `1`.
```

### `_.groupBy`
+ `_.groupBy(collection, [iteratee=_.identity])`

对`collection`元素执行`iteratee`方法，得到`key`，`value`就是该元素。`iteratee`方法调用一个参数`value`。

返回值：返回`key-value`组成的新对象

例子：
```
_.groupBy([6.1, 4.2, 6.3], Math.floor);
// => { '4': [4.2], '6': [6.1, 6.3] }
 
// The `_.property` iteratee shorthand.
_.groupBy(['one', 'two', 'three'], 'length');
// => { '3': ['one', 'two'], '5': ['three'] }
```

### `_.includes`
+ `_.incluede(collection, value, [fromIndex=0])`

检查`value`是否在`collection`中，`fromIndex`指定检查的位置，默认是`0`。存在返回`true`，不存在返回`false`。

例子：
```
_.includes([1, 2, 3], 1);
// => true
 
_.includes([1, 2, 3], 1, 2);
// => false
 
_.includes({ 'a': 1, 'b': 2 }, 1);
// => true
 
_.includes('abcd', 'bc');
// => true
```

### `_.invokeMap`
+ `_.invokeMap(collection, path, [args])`

为`collection`每个元素调用`path`方法，返回调用后的结果组成的新数组。`args`参数将会提供给被调用的方法。

例子：
```
_.invokeMap([[5, 1, 7], [3, 2, 1]], 'sort');
// => [[1, 5, 7], [1, 2, 3]]
 
_.invokeMap([123, 456], String.prototype.split, '');
// => [['1', '2', '3'], ['4', '5', '6']]
```

### `_.keyBy`
+ `_.keyBy(collection, [iteratee=_.identity])`

返回一个`key-value`对象，`key`是`collection`每个元素执行`iteratee`后的结果，对应的`value`是最后一个生成该`key`的`collection`值。`iteratee`调用一个参数`value`。

例子:
```
var array = [
  { 'dir': 'left', 'code': 97 },
  { 'dir': 'right', 'code': 100 },
  { 'dir': 'right', 'code': 99}
];
 
_.keyBy(array, 'dir');
// => { 'left': { 'dir': 'left', 'code': 97 }, 'right': { 'dir': 'right', 'code': 99 } }
```

### `_.map`
+ `_.map(collection, [iteratee=_.identity])`

这个就比较简单了，为`collection`的每个元素执行`iteratee`方法，得到的结果映射成一个新的数组。

例子：
```
function square(n) {
  return n * n;
}
 
_.map([4, 8], square);
// => [16, 64]
 
_.map({ 'a': 4, 'b': 8 }, square);
// => [16, 64] (iteration order is not guaranteed)
 
var users = [
  { 'user': 'barney' },
  { 'user': 'fred' }
];
 
// The `_.property` iteratee shorthand.
_.map(users, 'user');
// => ['barney', 'fred']
```

### `_.orderBy`
+ `_.orderBy(collection, [iteratees=[_.identity], [orders])`

这个方法很像`_.sortBy`，不过`_.orderBy`允许指定排序方式`iteratees`。`orders`默认是`asc`（升序），也可以指定为`desc`，返回一个新的有序的数组。

例子：
```
var users = [
  { 'user': 'fred',   'age': 48 },
  { 'user': 'barney', 'age': 34 },
  { 'user': 'fred',   'age': 40 },
  { 'user': 'barney', 'age': 36 }
];
 
// Sort by `user` in ascending order and by `age` in descending order.
_.orderBy(users, ['user', 'age'], ['asc', 'desc']);
// => objects for [['barney', 36], ['barney', 34], ['fred', 48], ['fred', 40]]
```
这个例子，排序关键字有两个`user`和`age`，两个`for`循环，排序完成一个，在做另一个排序。


### `_.partition`
+ `_.partition(collection, [predicate=_.identity])`

将`collection`分成两组，一组是执行`predicate`返回`true`，另一组是返回`false`。返回的结果应该是一个二维数组。

例子：
```
var users = [
  { 'user': 'barney',  'age': 36, 'active': false },
  { 'user': 'fred',    'age': 40, 'active': true },
  { 'user': 'pebbles', 'age': 1,  'active': false }
];
 
_.partition(users, function(o) { return o.active; });
// => objects for [['fred'], ['barney', 'pebbles']]
 
// The `_.matches` iteratee shorthand.
_.partition(users, { 'age': 1, 'active': false });
// => objects for [['pebbles'], ['barney', 'fred']]
 
// The `_.matchesProperty` iteratee shorthand.
_.partition(users, ['active', false]);
// => objects for [['barney', 'pebbles'], ['fred']]
 
// The `_.property` iteratee shorthand.
_.partition(users, 'active');
// => objects for [['fred'], ['barney', 'pebbles']]
```

### `_.reduce`
+ `_.reduce(collection, [iteratee=_.identity], [accumulator])`

该方法作为一个累加器，把数组中的每个值(从左到右)执行`iteratee`方法开始缩减，最终变成一个值。如果`accumulator`没有给出，`collection`的第一个元素被当做初始值。
`iteratee`调用四个参数`accumulator, value， index|key, collection`。

例子：
```
_.reduce([1, 2], function(sum, n) {
  return sum + n;
}, 0);
// => 3
 
_.reduce({ 'a': 1, 'b': 2, 'c': 1 }, function(result, value, key) {
  (result[value] || (result[value] = [])).push(key);
  return result;
}, {});
// => { '1': ['a', 'c'], '2': ['b'] } (iteration order is not guaranteed)
```

### `_.reduceRight`
+ `_.reduceRight(collection, [iteratee=_.identity], [accumulator])`

这个方法与`_.reduce()`方法不同的是从右到左计算。

例子：
```
var array = [[0, 1], [2, 3], [4, 5]];
 
_.reduceRight(array, function(flattened, other) {
  return flattened.concat(other);
}, []);
// => [4, 5, 2, 3, 0, 1]
```

### `_.reject`
+ `_.reject(collection, [predicate=_.identity])`

这个方法与`_.filter`相反，返回`collection`执行`predicate`返回`false`条件的元素组成的新数组。

例子：
```
var users = [
  { 'user': 'barney', 'age': 36, 'active': false },
  { 'user': 'fred',   'age': 40, 'active': true }
];
 
_.reject(users, function(o) { return !o.active; });
// => objects for ['fred']
 
// The `_.matches` iteratee shorthand.
_.reject(users, { 'age': 40, 'active': true });
// => objects for ['barney']
 
// The `_.matchesProperty` iteratee shorthand.
_.reject(users, ['active', false]);
// => objects for ['fred']
 
// The `_.property` iteratee shorthand.
_.reject(users, 'active');
// => objects for ['barney']
```

### `_.sample`
+ `_.sample(collection)`

返回`collection`中随机的一个元素。

例子：
```
_.sample([1, 2, 3, 4]);
// => 2
```

### `_.sampleSize`
+ `_.sampleSize(collection, [n=1])`

返回`collection`中随机的`n`个数，默认`n=1`。

例子：
```
_.sampleSize([1, 2, 3], 2);
// => [3, 1]
 
_.sampleSize([1, 2, 3], 4);
// => [2, 3, 1]
```

### `_.shuffle`
+ `_.shuffle(collection)`

把`collection`元素的顺序随机打乱，返回打乱后的`collection`。

例子：
```
_.shuffle([1, 2, 3, 4]);
// => [4, 1, 3, 2]
```

### `_.size`
+ `_.size(collection)`

返回`collection`的`length`,`collection`可以是`Array|Object|string`。

例子：
```
_.size([1, 2, 3]);
// => 3
 
_.size({ 'a': 1, 'b': 2 });
// => 2
 
_.size('pebbles');
// => 7
```

### `_.some`
+ `_.some(collection, [predicate=_.identity])`

对`collection`元素执行`predicate`，返回布尔值,迭代过程遇到返回`false`就停止。`predicate`调用三个参数`value, index|key, collection`。

例子：
```
_.some([null, 0, 'yes', false], Boolean);
// => true
 
var users = [
  { 'user': 'barney', 'active': true },
  { 'user': 'fred',   'active': false }
];
 
// The `_.matches` iteratee shorthand.
_.some(users, { 'user': 'barney', 'active': false });
// => false
 
// The `_.matchesProperty` iteratee shorthand.
_.some(users, ['active', false]);
// => true
 
// The `_.property` iteratee shorthand.
_.some(users, 'active');
// => true
```

### `_.sortBy`
+ `_.sortBy(collection, [iteratee=[_.identity]])`

按照`iteratee`规则对`collection`进行排序。

例子:
```
var users = [
  { 'user': 'fred',   'age': 48 },
  { 'user': 'barney', 'age': 36 },
  { 'user': 'fred',   'age': 40 },
  { 'user': 'barney', 'age': 34 }
];
 
_.sortBy(users, [function(o) { return o.user; }]);
// => objects for [['barney', 36], ['barney', 34], ['fred', 48], ['fred', 40]]
 
_.sortBy(users, ['user', 'age']);
// => objects for [['barney', 34], ['barney', 36], ['fred', 40], ['fred', 48]]
```

