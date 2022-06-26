# ES6_14_iterator

* 一个对象如果要具备可被`for...of`循环调用的 Iterator 接口，就必须在`Symbol.iterator`的属性上部署遍历器生成方法（原型链上的对象具有该方法也可）。

`iterator` 翻译为`遍历器`或`迭代器`

参考文档：
https://zhuanlan.zhihu.com/p/124965356

https://es6.ruanyifeng.com/#docs/iterator

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#%E8%BF%AD%E4%BB%A3%E5%99%A8%E7%A4%BA%E4%BE%8B

## 概念

JavaScript 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。**任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。**

Iterator 的作用有三个：

* 一是为各种数据结构，提供一个统一的、简便的访问接口；
* 二是使得数据结构的成员能够按某种次序排列；
* 三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

Iterator 的遍历过程是这样的:

* （1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，**遍历器对象本质上，就是一个指针对象。**
* （2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。
* （3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。
* （4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。

下面是一个模拟`next`方法返回值的例子:

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```

上面代码定义了一个`makeIterator`函数，它是一个遍历器生成函数，作用就是返回一个遍历器对象。对数组`['a', 'b']`执行这个函数，就会返回该数组的遍历器对象（即指针对象）`it`。

指针对象的`next`方法，用来移动指针。开始时，指针指向数组的开始位置。然后，每次调用`next`方法，指针就会指向数组的下一个成员。第一次调用，指向`a`；第二次调用，指向`b`。

`next`方法返回一个对象，表示当前数据成员的信息。这个对象具有`value`和`done`两个属性，`value`属性返回当前位置的成员，`done`属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用`next`方法。

总之，调用指针对象的`next`方法，就可以遍历事先给定的数据结构。

由于 Iterator 只是把接口规格加到数据结构之上，所以，遍历器与它所遍历的那个数据结构，实际上是分开的，完全可以写出没有对应数据结构的遍历器对象，或者说用遍历器对象模拟出数据结构。下面是一个无限运行的遍历器对象的例子。

```javascript
var it = idMaker();

it.next().value // 0
it.next().value // 1
it.next().value // 2
// ...

function idMaker() {
  var index = 0;

  return {
    next: function() {
      return {value: index++, done: false};
    }
  };
}
```



## Symbol.iterator

`[Symbol.iterator]`的值是字符串`@@iterator`。

Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环（详见下文）。当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。

一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）。

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，**一个数据结构只要具有`Symbol.iterator`属性，就可以认为是"可遍历的"。**`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内（参见《Symbol》一章）。

| 属性                | 值                                                           |
| :------------------ | :----------------------------------------------------------- |
| `[Symbol.iterator]` | 一个无参数的函数，其返回值为一个符合[迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#迭代器协议)的对象。 |

####迭代器协议

只有实现了一个拥有以下语义（semantic）的 `**next()**` 方法，一个对象才能成为迭代器：

| 属性   | 值                                                           |
| :----- | :----------------------------------------------------------- |
| `next` | 一个无参数函数，返回一个应当拥有以下两个属性的对象：<br /><br />`done`（boolean）如果迭代器可以产生序列中的下一个值，则为 `false`。（这等价于没有指定 `done` 这个属性。）如果迭代器已将序列迭代完毕，则为 `true`。这种情况下，<br /><br /><br />`value` 是可选的，如果它依然存在，即为迭代结束之后的默认返回值。`value`迭代器返回的任何 JavaScript 值。done 为 true 时可省略。<br /><br /><br />`next()` 方法必须返回一个对象，该对象应当有两个属性： `done` 和 `value`，如果返回了一个非对象值（比如 `false` 或 `undefined`），则会抛出一个 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 异常（`"iterator.next() returned a non-object value"`）。 |

#### 普通对象部署Iterator接口

```javascript
const obj = {
  [Symbol.iterator]() { //迭代器函数
    return {
      index: 0,
      next() { //内置next方法，返回{value,done}对象
        return this.index < 5 ? {value: this.index++} : {done: true}
      },
    }; //返回迭代器对象
  }
}

for(let val of obj) {
  console.log(val);
}
```



## 原生具备 Iterator 接口的数据结构

* Array
* Map
* Set
* String
* 类数组
* 函数的 arguments 对象(也是类数组)
* NodeList 对象

下面的例子是数组的`Symbol.iterator`属性。

```javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```





## 调用 Iterator 接口的场合

**（1）解构赋值**

对数组和 Set 结构进行解构赋值时，会默认调用`Symbol.iterator`方法。

```javascript
let set = new Set().add('a').add('b').add('c');

let [x,y] = set;
// x='a'; y='b'

let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```

**（2）扩展运算符**

扩展运算符（...）也会调用默认的 Iterator 接口。

```javascript
// 例一
var str = 'hello';
[...str] //  ['h','e','l','l','o']

// 例二
let arr = ['b', 'c'];
['a', ...arr, 'd']
// ['a', 'b', 'c', 'd']
```

上面代码的扩展运算符内部就调用 Iterator 接口。

实际上，这提供了一种简便机制，可以将任何部署了 Iterator 接口的数据结构，转为数组。也就是说，**只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组。**

```javascript
let arr = [...iterable];
```

我们可以通过提供自己的 `@@iterator` 方法，重新定义迭代行为：

```javascript
//默认行为
const s = new String('abc');
console.log(...s); //"a" "b" "c"


//重定义迭代器
const str = new String('abc');
str[Symbol.iterator] = function() {
  return {
    next() {
      if (this.init) {
        this.init = false;
        return {
          value: 'def',
          done: false
        }
      } else {
        return {
          done: true
        }
      }
    },
    init: true,
  }
}
console.log(...str); //"edf"
```

**（3）yield\***

`yield*`后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

```javascript
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```

**（4）其他场合**

由于数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，其实都调用了遍历器接口。下面是一些例子。

- for...of

- Array.from()

- Map(), Set(), WeakMap(), WeakSet()（比如`new Map([['a',1],['b',2]])`）

- Promise.all()

- Promise.race()

  

  (5) 对于类数组的对象（key值为数字索引，包含lenth属性），我们可以直接将数组的遍历器对象赋值给对象，代码如下：

  ```javascript
  let obj = {
    0 : "a",
    1 : "b",
    2 : "c",
    length :3,
  }
  obj[Symbol.iterator] = Array.prototype[Symbol.iterator];
  for(let i of obj){
    console.log(i); // a b c
  }
  ```

  



##接受可迭代对象的内置 API

很多 API 接受可迭代对象作为参数，例如：

- [`new Map([iterable])`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)
- [`new WeakMap([iterable])`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
- [`new Set([iterable])`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
- [`new WeakSet([iterable])`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

```javascript
new Map([[1, 'a'], [2, 'b'], [3, 'c']]).get(2); // "b"

let myObj = {};

new WeakMap([
    [{}, 'a'],
    [myObj, 'b'],
    [{}, 'c']
]).get(myObj);             // "b"

new Set([1, 2, 3]).has(3); // true
new Set('123').has('2');   // true

new WeakSet(function* () {
    yield {}
    yield myObj
    yield {}
}()).has(myObj);           // true
```

#### 另外，还有…

- [`Promise.all(iterable)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)
- [`Promise.race(iterable)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)
- [`Array.from(iterable)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/from)