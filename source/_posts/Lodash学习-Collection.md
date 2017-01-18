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


