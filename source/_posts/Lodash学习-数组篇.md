---
title: Lodash学习--数组篇
date: 2016-11-08 14:46:33
tags: lodash
---

**Lodash**是一个具有一致接口、模块化、高性能等特性的 JavaScript 工具库。还有一个类似的库是**underscore**，不过**underscore**不能按需加载。
朋友说：这种工具库，平时都是先用先写的。
不过我觉得，还是很有必要学习一下的，看下源码的实现。
本文主要是对**Lodash**的API,用自己理解的方式做一下说明。可能理解有误，不过还是要记录下来，当再用的时候有据可查。

<!--more-->

### `_.chunk`
+ `_.chunk(array, [size=1])`

将数组进行分块，按照`size`指定的长度，默认长度`1`

### `_.compact`
+ `_.compact(array)`

剔除数组中没有意义的值，比如`false, null, 0, "", undefined 和 NaN` 

### `_.concat`
+ `_.concat(array, [values])`

创建一个新的数组来保存原始数组，增加值/数组之后的结果

例子：
```
var array = [1];
var other = _.concat(array, 2, [3], [[4]]);
 
console.log(other);
// => [1, 2, 3, [4]]
 
console.log(array);
// => [1]
```

###  `_.difference`
+ `_.difference(array, [values])`

这个函数就比较复杂了，就想看一下源码，发现嵌套的函数太多。就投机取巧直接测试吧。这个函数，大概的作用就是将`array`和`[values]`进行比较，将`array`中比`[values]`中多出的值，保存到一个新的数组中。

例子：
```
//官网就比较敷衍了
_.difference([2, 1], [2, 3]);
// => [1]

//下面是我做的一些测试
_.difference([1, 1, "1", 2, 2], [1]);
// => ["1", 2, 2]
// 只要array中比[values]中多出的值，都会返回，不管个数出现了几次
```

### `_.differenceBy`
+ `_.differenceBy(array, [values], [iteratee=_.identity])`

这个方法就是在`_.difference`方法的基础上，增加了一个参数。反正是看了一会，才看明白这个第三个参数，怎么工作的。

例子：
```
_.differenceBy([2.1, 1.2], [2.3, 3.4], Math.floor);
// => [1.2]
 
// The `_.property` iteratee shorthand.
_.differenceBy([{ 'x': 2 }, { 'x': 1 }], [{ 'x': 1 }], 'x');
// => [{ 'x': 2 }]
```
第一个官方例子，就是看了半天没看懂。我以为是先`_.difference`在对得到的结果进行`Math.floor`运算，其实是想错了。 如果这么工作的话，就没必要设计`_.differenceBy`了，直接用`_.difference.xx()`就可以，所以我一开始的想当然是有问题的。
正确地工作步骤是，对前两个参数，分别执行第三个参数过滤，然后再比较找出`array`中比`[values]`中多出的部分，然后返回这些多余部分的原始值的一个数组。
就拿第一个说吧，执行过滤之后是`[2,1]`和`[2,3]`，应该是返回`[1]`的原始值`[1.2]`，就酱。

### `_.differenceWith`
+ `_.differenceWith(array, [values], [comparator])`

没太看懂。。。


### `_.drop`
+ `_.drop(array, [n=1])`

我理解的是抛弃前n个数组元素，返回剩下的数组元素，默认抛弃第一个。

例子：
```
_.drop([1, 2, 3]);
// => [2, 3]
 
_.drop([1, 2, 3], 2);
// => [3]
 
_.drop([1, 2, 3], 5);
// => []
 
_.drop([1, 2, 3], 0);
// => [1, 2, 3]
```

### `_.dropRight`
+ `_.dropRight(array, [n=1])`

`_.dropRight`和`_.drop`功能是一样的，就是`_.drop`是从后往前抛弃n个数组元素，默认抛弃最后一个。

例子：
```
_.dropRight([1, 2, 3]);
// => [1, 2]
 
_.dropRight([1, 2, 3], 2);
// => [1]
 
_.dropRight([1, 2, 3], 5);
// => []
 
_.dropRight([1, 2, 3], 0);
// => [1, 2, 3]
```

### `_.dropRightWhile`
+ `_.dropRightWhile(array, [predicate=_.identity])`

从尾端查询数组`array`，第一个不满足`predicate`条件的元素开始截取数组。
参数`predicate`提供的是一个属性名称，就通过提供的参数使用`_.property`方法返回一个回调函数。
参数`predicate`提供的是一个对象，就用`_.matches`方法匹配相同的元素，相同返回`true`，不同返回`false`。
参数`predicate`也可以提供一个函数，该函数有三个参数`value, index, array`

`_.dropRightWhile`这个函数还牵扯到另外两个函数，`_.property`和`_.matches`。

例子： 
```
var users = [
  { 'user': 'barney',  'active': true },
  { 'user': 'fred',    'active': false },
  { 'user': 'pebbles', 'active': false }
];
 
_.dropRightWhile(users, function(o) { return !o.active; });
// => objects for ['barney']
 
// The `_.matches` iteratee shorthand.
_.dropRightWhile(users, { 'user': 'pebbles', 'active': false });
// => objects for ['barney', 'fred']
 
// The `_.matchesProperty` iteratee shorthand.
_.dropRightWhile(users, ['active', false]);
// => objects for ['barney']
 
// The `_.property` iteratee shorthand.
_.dropRightWhile(users, 'active');
// => objects for ['barney', 'fred', 'pebbles']
```

### `_.dropWhile`
+ `_.dropWhile(array, [predicate=_.identity])`

这个方法与上面`_.dropRightWhile`不同之处，是从数组的首端开始查询。

### `_.fill`
+ `_.fill(array, value, [start=0], [end=array.length])`

将`value`填充到`array`中，`start`默认为0，`end`默认为`array.length`。这个就比较好理解了。

例子：
```
var array = [1, 2, 3];
 
_.fill(array, 'a');
console.log(array);
// => ['a', 'a', 'a']
 
_.fill(Array(3), 2);
// => [2, 2, 2]
 
_.fill([4, 6, 8, 10], '*', 1, 3);
// => [4, '*', '*', 10]
```

### `_.findIndex`
+ `_.findIndex(array, [predicate=_.identity], [fromIndex=0])`

返回满足`predicate`条件的一个`array`数组的`index`，也可以指定从哪里开始查询，没找到满足条件的返回`-1`

例子：
```
var users = [
  { 'user': 'barney',  'active': false },
  { 'user': 'fred',    'active': false },
  { 'user': 'pebbles', 'active': true }
];
 
_.findIndex(users, function(o) { return o.user == 'barney'; });
// => 0
 
// The `_.matches` iteratee shorthand.
_.findIndex(users, { 'user': 'fred', 'active': false });
// => 1
 
// The `_.matchesProperty` iteratee shorthand.
_.findIndex(users, ['active', false]);
// => 0
 
// The `_.property` iteratee shorthand.
_.findIndex(users, 'active');
// => 2
```

### `_.findLastIndex`
+ `_.findLastIndex(array, [predicate=_.identity], [fromIndex=array.length -1])`

和`_.findIndex`基本相同，不过`_.findLastIndex`是从尾部往首部开始查找。

例子：
```
var users = [
  { 'user': 'barney',  'active': true },
  { 'user': 'fred',    'active': false },
  { 'user': 'pebbles', 'active': false }
];
 
_.findLastIndex(users, function(o) { return o.user == 'pebbles'; });
// => 2
 
// The `_.matches` iteratee shorthand.
_.findLastIndex(users, { 'user': 'barney', 'active': true });
// => 0
 
// The `_.matchesProperty` iteratee shorthand.
_.findLastIndex(users, ['active', false]);
// => 2
 
// The `_.property` iteratee shorthand.
_.findLastIndex(users, 'active');
// => 0
```

### `_.flatten`
+ `_.flatten(array)`

这个函数的作用是将`array`减少一个维度。

例子： 
```
_.flatten([1, [2, [3, [4]], 5]]);
// => [1, 2, [3, [4]], 5]
```

### `_.flattenDeep`
+ `_.flattenDeep(array)`

相当于递归执行`_.flatten`，最终将`array`变成一维数组。

例子：
```
_.flattenDeep([1, [2, [3, [4]], 5]]);
// => [1, 2, 3, 4, 5]
```

### `_.flattenDepth`
+ `_.flattenDepth(array, [depth=1])`

相当于指定执行`_.flattenDepth``depth`次，默认`depth`为1。

例子：
```
var array = [1, [2, [3, [4]], 5]];
 
_.flattenDepth(array, 1);
// => [1, 2, [3, [4]], 5]
 
_.flattenDepth(array, 2);
// => [1, 2, 3, [4], 5]
```

### `_.fromPairs`
+ `_.fromPairs(pairs)`

将`pairs`键值对转换成一个对象。

例子：
```
_.fromPairs([['a', 1], ['b', 2]]);
// => { 'a': 1, 'b': 2 }

_.fromPairs([['a', 1], ['b', 2], ['c', ['d', 4]]]);
// => { 'a': 1, 'b': 2, 'c': [ 'd', 4 ] }
```

### `_.head`
+ `_.head(array)`

返回`array`的第一个元素，别名`_.first`

例子：
```
_.head([1, 2, 3]);
// => 1
 
_.head([]);
// => undefined

_.head([[1, 4], 2, 3]);
// => [1, 4]
```

### `_.last`
+ `_.last(array)`

返回`array`的最后一个元素。

例子： 
```
_.last([1, 2, 3]);
// => 3
```

### `_.nth`
+ `_.nth(array, [n=0])`

获取指定`index`的`array`数组元素。

例子：
```
var array = ['a', 'b', 'c', 'd'];
 
_.nth(array, 1);
// => 'b'
 
_.nth(array, -2);
// => 'c';
```

### `_.tail`
+ `_.tail(array)`

返回去除第一个元素的数组。

例子：
```
_.tail([1, 2, 3]);
// => [2, 3]
```

### `_.indexOf`
+ `_.indexOf(array, value, [fromIndex=0])`

在`array`中查找`value`，返回找到的第一个匹配的`index`，没找到则返回`-1`，第三个参数`fromIndex`指定查找的起始位置，默认为0；

例子：
```
_.indexOf([1, 2, 1, 2], 2);
// => 1

_.indexOf([1, 2, 1, 2], 3);
// => -1
 
// Search from the `fromIndex`.
_.indexOf([1, 2, 1, 2], 2, 2);
// => 3
```

### `_.lastIndexOf`
+ `_.lastIndexOf(array, value, [fromIndex=array.length-1])`

和`_.indexOf`方法一样，不过是从尾部开始查找。

例子：
```
_.lastIndexOf([1, 2, 1, 2], 2);
// => 3
 
// Search from the `fromIndex`.
_.lastIndexOf([1, 2, 1, 2], 2, 2);
// => 1
```

### `_.initial`
+ `_.initial(array)`

去除`array`最后一个元素，并返回。

例子：
```
_.initial([1, 2, 3]);
// => [1, 2]

_.initial([1, 2, 3, [4, 5]]);
// => [1, 2, 3]
```

### `_.intersection`
+ `_.intersection([arrays])`

取出各数组中全等的元素，使用`SameValueZero`方式平等比较。

例子：
```
_.intersection([2, 1], [2, 3]);
// => [2]

_.intersection([1, 2], [4, 2], [2, 1]);
// => [2]
```

### `_.intersectionBy`
+ `_.intersectionBy([arrays], [iteratee=_.identity])`

`_.intersectionBy`就是在`_.intersection`的基础上接受了一个`iteratee`迭代器，生成了一个比较的标准，类似于`_.differenceBy`。

例子：
```
_.intersectionBy([2.1, 1.2], [2.3, 3.4], Math.floor);
// => [2.1]
 
// The `_.property` iteratee shorthand.
_.intersectionBy([{ 'x': 1 }], [{ 'x': 2 }, { 'x': 1 }], 'x');
// => [{ 'x': 1 }]
```

### `_.intersectionWith`
+ `_.intersectionWith([arrays], [comparator])`

这个函数和`_.differenceWith`差不多，一样没太看懂。
先略过。

### `_.join`
+ `_.join(array, [separator=','])`

将`array`转换成字符串类型并通过`separator`分隔开，默认使用`,`分隔。

例子：
```
_.join(['a', 'b', 'c'], '~');
// => 'a~b~c'

_.join(['a', 'b', 'c', ['d', 'e']], '-');
// => 'a-b-c-d,e'
```

### `_.pull`
+ `_.pull(array, [values])`

移除`array`中所有的指定`values`，需要注意的是这个函数会对原始`array`做修改。

例子：
```
var array = ['a', 'b', 'c', 'a', 'b', 'c'];
 
_.pull(array, 'a', 'c');
console.log(array);
// => ['b', 'b']
```

### `_.pullAll`
+ `_.pullAll(array, values)`

`_.pullAll`方法应该是`_.pull`方法的升级，这个方法是在**Lodash 4.0.0**中提出的。

例子：
```
var array = ['a', 'b', 'c', 'a', 'b', 'c'];
 
_.pullAll(array, ['a', 'c']);
console.log(array);
// => ['b', 'b']
```

### `_.pullAllBy`
+ `_.pullAllBy(array, values, [iteratee=_.identity])`

`_.pullAllBy`方法很像`_.pullAll`方法，除了可以接受一个迭代器`iteratee`，为每一个数组元素执行迭代器并生成一个比较的标准，这个迭代器调用一个参数`value`。
**注**：原始数组改变

例子：
```
var array1 = [{ 'x': 1 }, { 'x': 2 }, { 'x': 3 }, { 'x': 1 }];
 
_.pullAllBy(array1, [{ 'x': 1 }, { 'x': 3 }], 'x');
console.log(array);
// => [{ 'x': 2 }]

var array2 = [{ 'x': 1 }, { 'x': 2 }, { 'x': 3 }, { 'x': 1 }, { 'y': 4}];
 
_.pullAllBy(array, [{ 'x': 1 }], 'y');
console.log(array);
// => [{ 'y': 4 }]
```

### `_.pullAllWith`
+ `_.pullAllWith(array, values, [comoarator])`

这个跳过。。。

### `_.pullAt`
+ `_.pullAt(array, [indexes])`

移除相应`index`的元素，返回被移除元素的数组。
**注**：原始数组改变

例子：
```
var array = ['a', 'b', 'c', 'd'];
var pulled = _.pullAt(array, [1, 3]);
 
console.log(array);
// => ['a', 'c']
 
console.log(pulled);
// => ['b', 'd']
```

### `_.remove`
+ `_.remove(array, [predicate=_.identity])`

移除所有`predicate`返回**真**的数组元素，并返回被移除的数组元素。`predicate`调用三个参数`value, index, array`。

例子：
```
var array = [1, 2, 3, 4];
var evens = _.remove(array, function(n) {
  return n % 2 == 0;
});
 
console.log(array);
// => [1, 3]
 
console.log(evens);
// => [2, 4]
```

### `_.reverse`
+ `_.reverse(array)`

这个就比较简单了，是一个反序排列的方法，也会对原始方法进行更改

例子：
```
var array = [1, 2, 3];
 
_.reverse(array);
// => [3, 2, 1]
 
console.log(array);
// => [3, 2, 1]
```

### `_.slice`
+ `_.slice(array, [start=0], [end=array.length])`

对数组进行分割。

例子：
```
var array = [1, 2, 3];
 
_.slice(array, 1, 2);
// => [2]
```

### `_.sortedIndex`
+ `_.sortedIndex(array, value)`
向一个有序数组中插入一个`value`，将返回这个值插入之后的有序位置。（使用二分查找）

例子：
```
_.sortedIndex([30, 50], 40);
// => 1

_.sortedIndex([30, 50], 30);
// => 0
```

### `_.sortedIndexBy`
+ `_.sortedIndexBy(array, value, [iteratee=_.identity])`

凡是带`By`的方法方法，都是这种结构的函数。
`_.sortIndexBy`比`_.sortIndex`方法多一个参数，接收一个迭代器`iteratee`去计算排序，这个`iteratee`调用一个参数`value`。

例子：
```
var objects = [{ 'x': 4 }, { 'x': 5 }];
 
_.sortedIndexBy(objects, { 'x': 4 }, function(o) { return o.x; });
// => 0
 
// The `_.property` iteratee shorthand.
_.sortedIndexBy(objects, { 'x': 4 }, 'x');
// => 0
```

### `_.sortedIndexOf`
+ `_.sortedIndexOf(array, value)`

这个方法很像`_.indexOf`，`_.sortedIndexOf`是对一个有序数组进行二分查找。

例子：
```
_.sortedIndexOf([4, 5, 5, 5, 6], 5);
// => 1
```

### `_.sortedLastIndex`
+ `_.sortedLastIndex(array, value)`

这个方法很像`_.sortedIndex`，这个方法在保持有序的前提下会把`value`插进最大的那个位置。

例子：
```
_.sortedLastIndex([4, 5, 5, 5, 6], 5);
// => 4
```

### `_.sortedLastIndexBy`
+ `_.sortedLastIndexBy(array, value, [iteratee=_.identity])`

这个方法很像`_.sortedLastIndex`，只不过多了一个参数`iteratee`,这个迭代器为每个元素值计算他们的排序，这个迭代器调用一个参数`value`。返回应该被插入后的数组下标。

例子：
```
var objects = [{ 'x': 4 }, { 'x': 5 }];
 
_.sortedLastIndexBy(objects, { 'x': 4 }, function(o) { return o.x; });
// => 1
 
// The `_.property` iteratee shorthand.
_.sortedLastIndexBy(objects, { 'x': 4 }, 'x');
// => 1
```

### `_.sortedLastIndexOf`
+ `_.sortedLastIndexOf(array, value)`

这个方法很像`_.lastIndexOf`，只不过它对一个有序数组进行二分查找。

例子：
```
_.sortedLastIndexOf([4, 5, 5, 5, 6], 5);
// => 3
```

### `_.sortedUniq`
+ `_.sortedUniq(array)`

这个方法很像`_.uniq`，这个方法是为了有序数组设计且优化的，返回一个去重的数组。

例子：
```
_.sortedUniq([1, 1, 2]);
// => [1, 2]
```

### `_.sortedUniqBy`
+ `_.sortedUniqBy(array, [iteratee])`

这个方法很像`_.uniqBy`，它返回经过`iteratee`计算之后，去除重复值，只返回重复值的第一个原值和不重复值组成的有序数组。

例子：
```
_.sortedUniqBy([1.1, 1.2, 2.3, 2.4], Math.floor);
// => [1.1, 2.3]
```

### `_.take`
+ `_.take(array, [n=1])`

创建一个分割后的数组，从`array`数组的开始到第`n`个元素。

例子：
```
_.take([1, 2, 3]);
// => [1]
 
_.take([1, 2, 3], 2);
// => [1, 2]
 
_.take([1, 2, 3], 5);
// => [1, 2, 3]
 
_.take([1, 2, 3], 0);
// => []
```

### `_.takeRight`
+ `_.takeRight(array, [n=1])`

创建一个分割后的数组，从`array`数组的结尾开始，分割`n`个元素出来。

例子：
```
_.takeRight([1, 2, 3]);
// => [3]
 
_.takeRight([1, 2, 3], 2);
// => [2, 3]
 
_.takeRight([1, 2, 3], 5);
// => [1, 2, 3]
 
_.takeRight([1, 2, 3], 0);
// => []
```

### `_.takeRightWhile`
+ `_.takeRightWhile(array, [predicate=_.identity])`

同样是从`array`结尾开始分割数组，不过是通过`predicate`控制，直到返回**falsey**停止。`predicate`调用三个参数`value, index, array`

例子：
```
var users = [
  { 'user': 'barney',  'active': true },
  { 'user': 'fred',    'active': false },
  { 'user': 'pebbles', 'active': false }
];
 
_.takeRightWhile(users, function(o) { return !o.active; });
// => objects for ['fred', 'pebbles']
 
// The `_.matches` iteratee shorthand.
_.takeRightWhile(users, { 'user': 'pebbles', 'active': false });
// => objects for ['pebbles']
 
// The `_.matchesProperty` iteratee shorthand.
_.takeRightWhile(users, ['active', false]);
// => objects for ['fred', 'pebbles']
 
// The `_.property` iteratee shorthand.
_.takeRightWhile(users, 'active');
// => []
```

### `_.takeWhile`
+ `_.takeWhile(array, [predivate=_.identity])`

是从`array`开头开始分割数组，不过是通过`predicate`控制，直到返回**falsey**停止。`predicate`调用三个参数`value, index, array`

例子：
```
var users = [
  { 'user': 'barney',  'active': false },
  { 'user': 'fred',    'active': false},
  { 'user': 'pebbles', 'active': true }
];
 
_.takeWhile(users, function(o) { return !o.active; });
// => objects for ['barney', 'fred']
 
// The `_.matches` iteratee shorthand.
_.takeWhile(users, { 'user': 'barney', 'active': false });
// => objects for ['barney']
 
// The `_.matchesProperty` iteratee shorthand.
_.takeWhile(users, ['active', false]);
// => objects for ['barney', 'fred']
 
// The `_.property` iteratee shorthand.
_.takeWhile(users, 'active');
// => []
```

### `_.union`
+ `_.union([arrays])`

创建一个没有重复值的数组，组合所有被传入的数组元素。

例子：
```
_.union([2], [1, 2]);
// => [2, 1]
```

### `_.unionBy`
+ `_.unionBy([arrays], [iteratee=_.identity])`

通过`iteratee`对每个元素值进行执行，生成一个唯一性的标准，并选择第一个出现的值，作为要返回的值，去除重复的元素。`iteratee`调用一个参数`value`。

例子：
```
_.unionBy([2.1], [1.2, 2.3], Math.floor);
// => [2.1, 1.2]
 
// The `_.property` iteratee shorthand.
_.unionBy([{ 'x': 1 }], [{ 'x': 2 }, { 'x': 1 }], 'x');
// => [{ 'x': 1 }, { 'x': 2 }]
```

### `_.unionWith`
+ `_.unionWith([arrays], [comparator])`

这个没太看懂。。。

例子：
```
var objects = [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }];
var others = [{ 'x': 1, 'y': 1 }, { 'x': 1, 'y': 2 }];
 
_.unionWith(objects, others, _.isEqual);
// => [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }, { 'x': 1, 'y': 1 }]
```

### `_.uniq`
+ `_.uniq(array)`

数组去重。

例子：
```
_.uniq([2, 1, 2]);
// => [2, 1]
```

### `_.uniqBy`
+ `_.uniqBy(array, [iteratee=_.identity])`

这个方法是有条件的数组去重，通过`iteratee`迭代器生成一个唯一性的标准。`iteratee`调用一个参数`value`.

例子：
```
_.uniqBy([2.1, 1.2, 2.3], Math.floor);
// => [2.1, 1.2]
 
// The `_.property` iteratee shorthand.
_.uniqBy([{ 'x': 1 }, { 'x': 2 }, { 'x': 1 }], 'x');
// => [{ 'x': 1 }, { 'x': 2 }]
```

### `_.uniqWith`
+ `_.uniqWith(array, [comparator])`

没太分清`_.uniqWith`和`_.uniqBy`之间有什么区别。`_.uniqWith`传入的是一个比较器。`comparator`调用两个参数`arrVal, othVal`。

例子：
```
var objects = [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }, { 'x': 1, 'y': 2 }];
 
_.uniqWith(objects, _.isEqual);
// => [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }]
```

### `_.without`
+ `_.without(array, [values])`

创建一个新的数组，去除所有传入的`values`。

例子：
```
_.without([2, 1, 2, 3], 1, 2);
// => [3]
```

### `_.xor`
+ `_.xor([arrays])`

**xor**就是异或，相同为0，不同为1，1为`true`，应该被返回。
创建一个唯一值的数组，返回被给数组之间对称差（没有交集的部分）的元素。结果值的顺序由它们在数组中出现的顺序确定。

例子：
```
_.xor([2, 1, 4], [2, 3, 5]);
// => [1, 4, 3, 5]

_.xor([2, 2, 3], [4, 4, 5, 6]);
// => [2, 3, 4, 5, 6]
```

### `_.xorBy`
+ `_.xorBy([arrays], [iteratee=_.identity])`

有条件的`_.xor`方法，和所有`_.xxBy`方法一样，接收一个`iteratee`方法生成一个标准，`iteratee`接受一个参数`value`。

例子：
```
_.xorBy([2.1, 1.2], [2.3, 3.4], Math.floor);
// => [1.2, 3.4]
 
// The `_.property` iteratee shorthand.
_.xorBy([{ 'x': 1 }], [{ 'x': 2 }, { 'x': 1 }], 'x');
// => [{ 'x': 2 }]
```

### `_.xorWith`
+ `_.xorWith([arrays], [comparator])`

这个方法类似于`_.xor`，除了它接受比较器`comparator`，它被调用来比较数组的元素。结果值的顺序由它们在数组中出现的顺序确定。`comparator`调用两个参数`arrVal，othVal`。

例子：
```
var objects = [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }];
var others = [{ 'x': 1, 'y': 1 }, { 'x': 1, 'y': 2 }];
 
_.xorWith(objects, others, _.isEqual);
// => [{ 'x': 2, 'y': 1 }, { 'x': 1, 'y': 1 }]
```

### `_.zip`
+ `_.zip([arrays])`

创建一个元素组数组，把每个传入数组，第一个元素组合到一起，第二个元素组合在一起，以此类推。

例子：
```
_.zip(['a', 'b'], [1, 2], [true, false]);
// => [['a', 1, true], ['b', 2, false]]

_.zip(['a', 'b'], [1], [true, false]);
// => [['a', 1, true], ['b', undefined, false]]
```
### `_.unzip`
+ `_.unzip(array)`

就是把`_.zip`函数打包好的，或是元素组数组，对其进行解包。

例子：
```
var zipped = _.zip(['a', 'b'], [1, 2], [true, false]);
// => [['a', 1, true], ['b', 2, false]]
 
_.unzip(zipped);
// => [['a', 'b'], [1, 2], [true, false]]
```

### `_.zipObject`
+ `_.zipObject([props=[]], [values=[]])`

这个方法很像`_.fromPairs`，`_.zipObject`接受两个数组，一个属性数组和一个相应的对应值数组。

例子：
```
_.zipObject(['a', 'b'], [1, 2]);
// => { 'a': 1, 'b': 2 }

_.zipObject(['a', 'b'], [1]);
// => { 'a': 1, 'b': undefined }
```

### `_.zipObjectDeep`
+ `_.zipObjectDeep([props=[]], [values=[]])

这个方法像`_.zipObject`方法一样，不过它支持属性路径`property paths`

例子：
```
_.zipObjectDeep(['a.b[0].c', 'a.b[1].d'], [1, 2]);
// => { 'a': { 'b': [{ 'c': 1 }, { 'd': 2 }] } }
```

### `_.zipWith`
+ `_.zipWith([arrays], [iteratee=_.identity])`

这个方法像类似`_.zip`，接受一个迭代器`iteratee`去指定怎么如何组合分组值。这个迭代器为每个组的元素调用`...group`，还是看例子比较直观。

例子：
```
_.zipWith([1, 2], [10, 20], [100, 200], function(a, b, c) {
  return a + b + c;
});
// => [111, 222]
```

### `_.unzipWith`
+ `_.unzipWith(array, [iteratee=_.identity])`

这个方法很像`_.unzip`，它接受一个迭代器`iteratee`去指定怎样重组组合值。`iteratee`调用一个参数`...group`。

例子：
```
var zipped = _.zip([1, 2], [10, 20], [100, 200]);
// => [[1, 10, 100], [2, 20, 200]]
 
_.unzipWith(zipped, _.add);
// => [3, 30, 300]
```
