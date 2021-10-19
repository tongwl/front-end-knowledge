# ES6_12_Set

重点

* `Set`本身是一个构造函数，用来生成 Set 数据结构。

* `Set`的成员的值都是唯一的。

  

ES6 提供了新的数据结构 Set。它类似于数组，**但是成员的值都是唯一的，没有重复的值。**

`Set`本身是一个构造函数，用来生成 Set 数据结构。

`Set`函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。



#### 定义一个Set

没有参数，使用`add`添加值。

```javascript
const s = new Set();
s.add(1);
s.add(1);
s.add(1);
s.add(2);
console.log(s.size); //2
```

接受数组作为参数：

```javascript
//例子1
const s = new Set([1,2,3]);
console.log(s.size); //3

//例子2
const s = new Set([1,2,2,2,2]);
console.log([...s]); //[1,2]
```

接受类数组作为参数：

```javascript
//例子1
function test() {
  const s = new Set(arguments);
  console.log(s.size); //3
}
test(1,2,3);

//例子2
const d = document.querySelectorAll('div');
const s = new Set(d);
console.log(s.size); //5
```

接受字符串作为参数：

```javascript
const s = new Set('abcd');
console.log(s.size); //4
```



#### Set结构不会添加重复的值

Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是向 Set 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。

例子1：

```javascript
console.log(NaN === NaN); //false
console.log(Object.is(NaN, NaN)); //true

const s = new Set();
s.add(NaN);
s.add(NaN);
s.add(NaN);
s.add(NaN);
s.add(NaN);
console.log(s.size); //1
```

例子2：

```javascript
//{}是不同的对象
const s = new Set();
s.add({});
s.add({});
s.add({});
console.log(s.size); //3
```

例子3：

```javascript
//以下添加的对一个obj对象
const obj = {};
const s = new Set();
s.add(obj);
s.add(obj);
s.add(obj);
console.log(s.size); //1
```



#### 将Set转换为数组

```javascript
//例子1
const s = new Set([1,2,2,2,2]);
console.log([...s]); //[1,2]

//例子2
const s = new Set([1,2,2,2,2]);
console.log(Array.from(s)); //[1,2]
```



#### 利用Set数组去重

```javascript
const arr = [1,1,2,2,3];
[...new Set(arr)]; //[1,2,3]
Array.from(new Set(arr));//[1,2,3]
```



####利用Set字符串去重

```javascript
[...new Set('aabbccd')].join('') //"abcd"
```



## Set属性

####`Set.prototype.size`

返回`Set`实例的成员总数。

```javascript
const s = new Set([1, 2]);
console.log(s.size); //2
```



## Set方法

####`Set.prototype.add(value)`

添加某个值，**返回 Set 结构本身。**

```javascript
const s = new Set([1, 2]);
s.add(3).add(4).add(5);
console.log(s.size); //5
```



####`Set.prototype.delete(value)`

删除某个值，返回一个布尔值，表示删除是否成功。

```javascript
const s = new Set([1, 2]);
console.log(s.delete(3)); //false
console.log(s.delete(1)); //true
console.log(s); //Set(1) {2}
console.log(s.size); //1
```



#### `Set.prototype.has(value)`

返回一个布尔值，表示该值是否为`Set`的成员。

```javascript
const s = new Set([1,2,3,4]);
console.log(s.has(1)); //true
console.log(s.has(5)); //false
```



####`Set.prototype.clear()`

清除所有成员，没有返回值。

```javascript
const s = new Set([1,2,3,4]);
console.log(s.size); //4
s.clear();
console.log(s.size); //0
```



## Set遍历方法

**需要特别指出的是，`Set`的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。**

###`keys()`，`values()`，`entries()`

`keys`方法、`values`方法、`entries`方法返回的都是遍历器对象（详见《Iterator 对象》一章）。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以`keys`方法和`values`方法的行为完全一致。

#### `Set.prototype.keys()`

返回键名的遍历器(不是数组，是Interator)

####`Set.prototype.values()`

返回键值的遍历器(不是数组，是Interator)

####`Set.prototype.entries()`

返回键值对的遍历器(不是数组，是Interator)



例子：

```javascript
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```

Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的`values`方法。

```javascript
Set.prototype[Symbol.iterator] === Set.prototype.values
// true
```

这意味着，可以省略`values`方法，直接用`for...of`循环遍历 Set。

```javascript
let set = new Set(['red', 'green', 'blue']);

for (let x of set) {
  console.log(x);
}
// red
// green
// blue
```



### `forEach()`

Set 结构的实例与数组一样，也拥有`forEach`方法，用于对每个成员执行某种操作，没有返回值。

```javascript
let set = new Set([1, 4, 9]);
set.forEach((value, key) => console.log(key + ' : ' + value))
// 1 : 1
// 4 : 4
// 9 : 9
```

上面代码说明，`forEach`方法的参数就是一个处理函数。该函数的参数与数组的`forEach`一致，依次为键值、键名、集合本身（上例省略了该参数）。这里需要注意，Set 结构的键名就是键值（两者是同一个值），因此第一个参数与第二个参数的值永远都是一样的。

另外，`forEach`方法还可以有第二个参数，表示绑定处理函数内部的`this`对象。





#### Set的应用

去除数组重复数据

```javascript
const arr = [1, 2, 2, 3, 3, 4];
console.log([...new Set(arr)]); //[1,2,3,4]
console.log(Array.from(new Set(arr))); //[1,2,3,4]
```



数组的`map`和`filter`方法也可以间接用于 Set 了。

```javascript
let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// 返回Set结构：{2, 4, 6}

let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// 返回Set结构：{2, 4}
```



因此使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。

```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// （a 相对于 b 的）差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```
