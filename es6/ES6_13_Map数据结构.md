# ES6_13_Map数据结构

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

```javascript
const data = {};
const element = document.getElementById('myDiv');

data[element] = 'metadata';
data['[object HTMLDivElement]'] // "metadata"
```

上面代码原意是将一个 DOM 节点作为对象`data`的键，但是由于对象只接受字符串作为键名，所以`element`被自动转为字符串`[object HTMLDivElement]`。

为了解决这个问题，ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。**如果你需要“键值对”的数据结构，Map 比 Object 更合适。**



####定义一个Map

没有参数，使用`set`添加值。

```javascript
const m = new Map();
m.set('k', 'val');
console.log(m.get('k')); //'val'
```



接受数组作为参数：

```javascript
const m = new Map([['k1', 'v1'], ['k2', 'v2']]);
console.log(m.get('k1')); //'v1'
console.log(m.get('k2')); //'v2'
```



事实上，不仅仅是数组，任何具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构（详见《Iterator》一章）都可以当作`Map`构造函数的参数。这就是说，`Set`和`Map`都可以用来生成新的 Map。

接受Set作为参数：

```javascript
const s = new Set([
  ['k1', 'v1'],
  ['k2', 'v2'],
]);
const m = new Map(s);
console.log(m.get('k1')); //'v1'
console.log(m.get('k2')); //'v2'
```



接受Map作为参数：

```javascript
const m = new Map([
  ['k1', 'v1'],
  ['k2', 'v2'],
]);
const m2 = new Map(m);
console.log(m2.get('k1'));
console.log(m2.get('k2'));
```



## Map属性

####`Map.prototype.size`

返回`Map`实例的成员总数。

```javascript
const m = new Map([
  ['k1', 'v1'],
  ['k2', 'v2'],
]);
console.log(m.size); //2
```



##Map方法

####`Map.prototype.set(key, value)`

`set`方法设置键名`key`对应的键值为`value`，然后返回整个 Map 结构。如果`key`已经有值，则键值会被更新，否则就新生成该键。

```javascript
const m = new Map();
m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
```

`set`方法返回的是当前的`Map`对象，因此可以采用链式写法。

```javascript
let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');
```

如果对同一个键多次赋值，后面的值将覆盖前面的值。

```javascript
const m = new Map();
m.set(1, 1);
m.set(1, 2);
m.set(1, 3);
console.log(m.get(1)); //3
```

如何判断键值是否相同？

* 如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值严格相等；
* `0`和`-0`就是一个键
* 布尔值`true`和字符串`"true"`则是两个不同的键
* `undefined`和`null`是两个不同的键
* `NaN`不严格相等于自身，但 Map 将其视为同一个键
* Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键

例子：

```javascript
const m = new Map([
  [NaN, 1]
]);
console.log(m.get(NaN)); //1
```

```javascript
const m = new Map([
  [0, 1]
]);
console.log(m.get(-0)); //1
```

```javascript
const m = new Map();
m.set({}, 1);
console.log(m.get({})); //undefined
```

```javascript
const m = new Map();
m.set(true, 1);
m.set('true', 2);
console.log(m.get(true)); //1
```

```javascript
const m = new Map();
const o = {};
m.set(o, 1);
m.set(o, 2);
m.set(o, 3);
console.log(m.get(o)); //3
```



####`Map.prototype.get(key)`

`get`方法读取`key`对应的键值，如果找不到`key`，返回`undefined`。

```javascript
const m = new Map();

const hello = function() {console.log('hello');};
m.set(hello, 'Hello ES6!') // 键是函数

m.get(hello)  // Hello ES6!
```

```javascript
const m = new Map();
console.log(m.get(1)); //undefined
```



####`Map.prototype.has(key)`

`has`方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

```javascript
const m = new Map();
m.set(1, '1');
console.log(m.has(1)); //true
console.log(m.has(2)); //false
```



#### `Map.prototype.delete(key)`

`delete`方法删除某个键，返回`true`。如果删除失败，返回`false`。

```javascript
const m = new Map();
m.set(1, '1');
m.delete(1);
console.log(m.has(1)); //false
console.log(m.has(2)); //false
```



#### `Map.prototype.clear()`

`clear`方法清除所有成员，没有返回值。

```javascript
const m = new Map([
  ['k1', 'v1'],
  ['k2', 'v2'],
]);
console.log(m.size); //2
m.clear();
console.log(m.size); //0
```



#### 遍历方法

- `Map.prototype.keys()`：返回键名的遍历器。
- `Map.prototype.values()`：返回键值的遍历器。
- `Map.prototype.entries()`：返回所有成员的遍历器。
- `Map.prototype.forEach()`：遍历 Map 的所有成员。

需要特别注意的是，Map 的遍历顺序就是插入顺序。

```javascript
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

```

Map `forEach`方法，与数组的`forEach`方法类似，也可以实现遍历。

```javascript
const m = new Map([
  ['k1', 'v1'],
  ['k2', 'v2'],
  ['k3', 'v3'],
]);
m.forEach((value, key, map) => {
  console.log(value);
  console.log(key);
  console.log(map);
});
```



结合数组的`map`方法、`filter`方法，可以实现 Map 的遍历和过滤（Map 本身没有`map`和`filter`方法）。

```javascript
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```



#### 扩展符

```javascript
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```

