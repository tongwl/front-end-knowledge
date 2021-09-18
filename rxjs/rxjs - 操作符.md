# rxjs - 操作符

```
of()
from()
interval()
first()
last()
take()
map()
mapTo()
concat()
concatAll()
concatMap()
merge()
mergeAll()
mergeMap()
switchMap()
range()
repeat()
delay()
fromEvent()
defer()
```



```
1.如何合并observable1(next(1))和observable2(next2)?
2.如何合并observable1(next(observable1_1))和observable2(observable2_1)?
```





##操作符说明

操作符分两类，一种是**Pipeable Operators**，意思就是可以进行管道链接式操作：传入一个Observable对象，进过operator处理之后得到一个新的Observable对象，操作符可以通过pipe()函数链接起来，操作符本身也是一个函数，所以也可以用函数层层调用的方式链接起来(但是不推荐)。另外一种是**Creation Operators**，用于创建Observable对象，例如of(), from(), interval()等。
使用函数方式进行链接：`op4()(op3()(op2()(op1()(obs))))`. 缺点是可读性太差，所以我们一般使用pipe来链接：
`obs.pipe(op1(), op2(), op3(), op4())`;

例子：

```javascript
//函数嵌套调用方式
first()(map((x: number) => {return x ** 2})(of(1, 2, 4))).subscribe(console.log); // 1

//使用pipe方式更加清晰，简洁
of(1,2,4).pipe(
  map(x => x ** 2),
  first()
).subscribe(console.log); // 1
```



## of()

创建一个Observable对象：将of的arguments参数转换为Observable队列。

```javascript
of(1,2,3).subscribe(console.log); //1, 2, 3
```



## from()

创建一个Observable对象：将数组，类数组对象，**Promise**，iterable object, or an Observable-like object 转换为Observable对象。

```javascript
//数组
from([1,2,3]).subscribe(console.log); //1,2,3


//类数组
function t(cc, tt, ee) {
  //arguments是一个类数组对象
  from(arguments).subscribe(console.log); // 11, 22, 33
}
t(11, 22, 33);

//Promise
from(new Promise(resolve => resolve(1))).subscribe(console.log); //1
```



## interval()

创建一个interval的Observable对象，从0开始。

```javascript
//从0开始，每隔1秒输入0,1,2,3,4...
interval(1000).subscribe(console.log); //0，1，2，3，4...
```



## first()

取Observable对象的第一个

```javascript
 of(1,2,3).pipe(first()).subscribe(console.log); //1
```



##last()

取Observable对象的最后一个

```javascript
of(1,2,3).pipe(last()).subscribe(console.log); //3
```



##take()

取Observable对象的前几个

```javascript
//从0开始，取0,1,2,3  4个值之后就停止。
interval(1000).pipe(take(4)).subscribe(console.log); //0,1,2,3
```



## map()

类似于数组map，将Observable对象内部遍历处理，得到一个新的Observable

```javascript
of(1, 2, 3).pipe(
  map(res => res ** 2)
).subscribe(console.log); // 1 4 9
```



## mapTo()





## concat()

官网： https://rxjs.dev/api/index/function/concat
参考2：https://rxjs-cn.github.io/learn-rxjs-operators/operators/combination/concat.html

**特性：合并多个Observable，并且按照顺序输出。后续的Observable只会等前面的Observable结束才开始。**

>  Creates an output Observable which sequentially emits all values from the first given Observable and then moves on to the next.

按照顺序，前一个 observable 完成了再订阅下一个 observable 并发出值。**concat()其实就是concurrent为1的merge操作符。**

前一个Observable没有完成之前，后续的Observable并不会被执行，如果前一个Observable是无限量的，那么后面的Observable永远不会被执行到。

***

concat既有静态方法，也~~有实例方法~~。推荐使用静态方法。

静态方法：

```javascript
import { concat } from 'rxjs';
concat(observable1, observable2);
```

~~实例方法：(不推荐使用了)~~

```javascript
import { concat } from 'rxjs/operators';
observable1.pipe(concat(observable2));
```

***

例子：

```javascript
import { concat, interval, range } from 'rxjs';
import { take } from 'rxjs/operators';

const timer = interval(1000).pipe(take(4));
const sequence = range(1, 10);
const result = concat(timer, sequence);
result.subscribe(x => console.log(x));

// results in:
// 0 -1000ms-> 1 -1000ms-> 2 -1000ms-> 3 -immediate-> 1 ... 10
```



例子：

Concatenate the same Observable to repeat it.

```javascript
import { concat, interval } from 'rxjs';
import { take } from 'rxjs/operators';
 
const timer = interval(1000).pipe(take(2));
 
concat(timer, timer) // concatenating the same Observable!
.subscribe(
  value => console.log(value),
  err => {},
  () => console.log('...and it is done!')
);
 
// Logs:
// 0 after 1s
// 1 after 2s
// 0 after 3s
// 1 after 4s
// "...and it is done!" also after 4s
```

如果要concat重复的Observable，我们也可以使用`repeat`操作符。



例子：

> If any Observable in chain errors, instead of passing control to the next Observable, `concat` will error immediately as well. 

**如果前一个Observable对象error了，那么后面的Observable不会被执行了。**

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.error('I am error');
});
const timer = interval(1000).pipe(take(4));
concat(observable, timer).subscribe({
  next(res) {
    console.log(`res: ${res}`);
  },
  error(err) {console.log(err)},
  complete() {console.log('complete')},
});

//Logs
res: 1
res: 2
res: 3
I am error
```



例子：

```javascript
// 发出 1,2,3
const sourceOne = of(1, 2, 3);
// 发出 4,5,6
const sourceTwo = of(4, 5, 6);

// 延迟3秒，然后发出
const sourceThree = sourceOne.pipe(delay(3000));
// sourceTwo 要等待 sourceOne 完成才能订阅
const example = concat(sourceThree, sourceTwo);
// 输出: 1,2,3,4,5,6
const subscribe = example.subscribe(val =>
  console.log('Example: Delayed source one:', val)
);
```



例子：

前面的Observable必须要complete，后续的才能进行。

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
});
const sequence = range(2, 10);
concat(observable, sequence).subscribe(console.log); //只会输出1，sequence并不会被执行，因为observable并没有complete
```

```js
// RxJS v6+
import { interval, of, concat } from 'rxjs';

// 当 source 永远不完成时，随后的 observables 永远不会运行
const source = concat(interval(1000), of('This', 'Never', 'Runs'));
// 输出: 0,1,2,3,4....
const subscribe = source.subscribe(val =>
  console.log(
    'Example: Source never completes, second observable never runs:',
    val
  )
// 输出: 0,1,2,3,4....
const subscribe = source.subscribe(val => console.log('Example: Source never completes, second observable never runs:', val));
```



## concatAll()

**特性：通过按顺序连接内部 Observable 将高阶 Observable 转换为一阶 Observable。**

> Converts a higher-order Observable into a first-order Observable by concatenating the inner Observables in order.

concatAll是concurrent为1的mergeAll操作符，以下摘自源码：

```javascript
export function concatAll<T>(): OperatorFunction<ObservableInput<T>, T> {
  return mergeAll<T>(1);
}
```

例子：

```javascript
const source = interval(1000).pipe(take(5));
source.pipe(
  map(x => of(x)),
).subscribe(console.log); //打印出的是Observable对象
```

```javascript
const source = interval(1000).pipe(take(5));
source.pipe(
  map(x => of(x)),
).subscribe({
  next(res) {
    res.subscribe(console.log); //每隔一秒输出0,1,2,3,4
  }
});
```

```javascript
const source = interval(1000).pipe(take(5));
source.pipe(
  map(x => of(x)),
  concatAll(),  //将map返回的Observable用concatAll转换，subscribe的结果就是实际的x值了
).subscribe(console.log); //每隔一秒输出0,1,2,3,4
```



例子2：

concatAll不仅可以打平Observable对象，也可以打平Promise对象。

```javascript
const timer = interval(1000).pipe(take(5));
timer.pipe(
  map(x => new Promise(resolve => resolve(x)))
).subscribe(console.log); //每隔1秒得到一个promise
```

```javascript
const timer = interval(1000).pipe(take(5));
timer.pipe(
  map(x => new Promise(resolve => resolve(x))),
  concatAll()
).subscribe(console.log); //每隔1秒打印0,1,2,3,4
```



例子3：

**牢记，不管是concat()还是concatAll()，它都是按照顺序进行的**：

```javascript
// RxJS v6+
import { take, concatAll } from 'rxjs/operators';
import { interval, of } from 'rxjs/observable/interval';

const obs1 = interval(1000).pipe(take(5));
const obs2 = interval(500).pipe(take(2));
const obs3 = interval(2000).pipe(take(1));
// 发出3个 observables
const source = of(obs1, obs2, obs3);
// 按顺序订阅每个内部 obserable，前一个完成了再订阅下一个
const example = source.pipe(concatAll());
/*
  输出: 0,1,2,3,4,0,1,0
  怎么运行的...
  订阅每个内部 observable 并发出值，当一个完成了才订阅下一个
  obs1: 0,1,2,3,4 (complete)
  obs2: 0,1 (complete)
  obs3: 0 (complete)
*/

const subscribe = example.subscribe(val => console.log(val));
```



## concatMap()

官网： https://rxjs.dev/api/operators/concatMap
参考2：https://rxjs-cn.github.io/learn-rxjs-operators/operators/transformation/concatmap.html

#####函数签名: `concatMap(project: function, resultSelector: function): Observable`

```javascript
concatMap = map() + concatAll()
```

> Projects each source value to an Observable which is merged in the output Observable, in a serialized fashion waiting for each one to complete before merging the next.

将值映射成内部 observable，并按顺序订阅和发出。

concatMap是concurrent为1的mergeMap操作符，以下摘自源码：

```javascript
export function concatMap<T, R, O extends ObservableInput<any>>(
  project: (value: T, index: number) => O,
  resultSelector?: (outerValue: T, innerValue: ObservedValueOf<O>, outerIndex: number, innerIndex: number) => R
): OperatorFunction<T, ObservedValueOf<O>|R> {
  return mergeMap(project, resultSelector, 1);
}
```

例子：

##### 演示 `concatMap` 和`mergeMap`之间的区别

 注意 `concatMap` 和 `mergeMap`之间的区别。 因为 `concatMap` 之前的一个内部 observable 完成后才会订阅下一个， source 中延迟 2000ms 值会先发出。 对比的话，`mergeMap`会立即订阅所有内部 observables， 延迟少的 observable (1000ms) 会先发出值，然后才是 2000ms 的 observable 。

```js
// RxJS v6+
import { of } from 'rxjs';
import { concatMap, delay, mergeMap } from 'rxjs/operators';

// 发出延迟值
const source = of(2000, 1000);
// 将内部 observable 映射成 source，当前一个完成时发出结果并订阅下一个
const example = source.pipe(
  concatMap(val => of(`Delayed by: ${val}ms`).pipe(delay(val)))
);
//以下结果一定是等到2000ms的那个执行完，才会来执行1000ms
// 输出: With concatMap: Delayed by: 2000ms, With concatMap: Delayed by: 1000ms
const subscribe = example.subscribe(val =>
  console.log(`With concatMap: ${val}`)
);

// 展示 concatMap 和 mergeMap 之间的区别
//mergeMap则是谁快就执行谁
const mergeMapExample = source
  .pipe(
    // 只是为了确保 meregeMap 的日志晚于 concatMap 示例
    delay(5000),
    mergeMap(val => of(`Delayed by: ${val}ms`).pipe(delay(val)))
  )
  .subscribe(val => console.log(`With mergeMap: ${val}`));
```



例子：

concatMap不仅可以打平Observable对象，也可以打平Promise对象。

```js
const source = of(2000, 1000);
const example = source.pipe(
  concatMap(v => new Promise(resolve => setTimeout(() => {resolve(v)}, v)))
);
//1000一定会得到2000执行结束才会执行
example.subscribe(console.log); //输出结果2000 1000
```



例子：

**resultSelector 已经不再被推荐使用，请再内部使用map代替。**

~~第二个参数resultSelector的用法：~~（不推荐使用了）

```js
// RxJS v6+
import { of } from 'rxjs';
import { concatMap } from 'rxjs/operators';

// 发出 'Hello' 和 'Goodbye'
const source = of('Hello', 'Goodbye');
// 使用 promise 的示例
const examplePromise = val => new Promise(resolve => resolve(`${val} World!`));
// 返回结果前，第一个参数的结果将传递给第二个参数选择器函数
const example = source.pipe(
  concatMap(val => examplePromise(val), result => `${result} w/ selector!`)
);
// 输出: 'Example w/ Selector: 'Hello w/ Selector', Example w/ Selector: 'Goodbye w/ Selector'
const subscribe = example.subscribe(val =>
  console.log('Example w/ Selector:', val)
);
```

## merge()

官网： https://rxjs.dev/api/index/function/merge
参考2：https://rxjs-cn.github.io/learn-rxjs-operators/operators/combination/merge.html

**特性：合并多个Observable，同时发出来自每个Observable的值。**

> Creates an output Observable which concurrently emits all values from every given input Observable.

创建一个输出 Observable，它同时发出来自每个给定输入 Observable 的所有值。

语法示例：

```javascript
const merged = merge(timer1, timer2, timer3);
const merged2 = merge(timer1, timer2, timer3, 2); //2代表允许多少个同时发出
```

***

如果产生值的顺序是首要考虑的，那么可以设置concurrent为1，当然最好使用 [concat](https://rxjs-cn.github.io/learn-rxjs-operators/operators/combination/concat.html) 来代替！

***

merge既有静态方法，也~~有实例方法~~。推荐使用静态方法。

静态方法：

```
import { merge } from 'rxjs';
merge(observable1, observable2, observable3);
```

~~实例方法：(不推荐使用了)~~

```javascript
import { merge } from 'rxjs/operators';
observable1.pipe(merge(observable2, observable3));
```

***

例子：

```javascript
const timer1 = interval(1000).pipe(take(4));
const timer2 = interval(1000).pipe(take(4), delay(1000));
//第一秒输出0
//第二秒输出1,0
//第三秒输出2,1
//第四秒输出3,2
//第五秒输出3
merge(timer1, timer2).subscribe(console.log);
```

例子：

Merge together 3 Observables, but only 2 run concurrently.

merge 3个Observable对象，但是设置concurrent为2，也就是同一时间最多只有两个Observable同步，第三个以及后面的一直等待前面两个有一个完成。

```ts
import { merge, interval } from 'rxjs';
import { take } from 'rxjs/operators';
 
const timer1 = interval(1000).pipe(take(10));
const timer2 = interval(2000).pipe(take(6));
const timer3 = interval(500).pipe(take(10));
const concurrent = 2; // the argument
const merged = merge(timer1, timer2, timer3, concurrent);
merged.subscribe(x => console.log(x));
 
// Results in the following:
// - First timer1 and timer2 will run concurrently
// - timer1 will emit a value every 1000ms for 10 iterations
// - timer2 will emit a value every 2000ms for 6 iterations
// - after timer1 hits its max iteration, timer2 will
//   continue, and timer3 will start to run concurrently with timer2
// - when timer2 hits its max iteration it terminates, and
//   timer3 will continue to emit a value every 500ms until it is complete
```



## mergeAll()

官网： https://rxjs.dev/api/operators/mergeAll
参考2：https://rxjs-cn.github.io/learn-rxjs-operators/operators/combination/mergeall.html

#####函数签名: `mergeAll(concurrent: number): Observable`

**特性：将高阶 Observable 转换为一阶 Observable。合并多个Observable，同时发出来自每个Observable的值。(合并多个，谁快谁执行。)**

和concatAll()的区别是，concatAll()是按照顺序执行，前一个不complete，后一个就进行不下去了；而mergeAll是谁快谁执行。

例子：

```javascript
const source = interval(1000).pipe(take(5));
source.pipe(
  map(x => of(x)),
  mergeAll(),  //将map返回的Observable用mergeAll转换，subscribe的结果就是实际的x值了
).subscribe(console.log); //每隔一秒输出0,1,2,3,4
```

例子2：

mergeAll不仅可以打平Observable对象，也可以打平Promise对象。

```js
const source = of(3,2,1);
source.pipe(
  map(r => new Promise(resolve => {
    setTimeout(() => {
      resolve(r);
    }, r * 1000);
  })),
  mergeAll()
).subscribe(console.log);//先后输出1,2,3
```

例子3：

```js
const source1 = interval(1000).pipe(take(6));
const source2 = interval(2000).pipe(take(2));
const source3 = interval(3000).pipe(take(2));
of(source1, source2, source3).pipe(mergeAll()).subscribe(console.log); //先后输出0, 1,0, 2,0, 3,1, 4, 5,1
```



##mergeMap()

官网： https://rxjs.dev/api/operators/mergeMap
参考2：https://rxjs-cn.github.io/learn-rxjs-operators/operators/transformation/mergemap.html

#####函数签名:  `mergeMap(project: function: Observable, resultSelector: function: any, concurrent: number): Observable`

```javascript
mergeMap = map() + mergeAll()
```

> Projects each source value to an Observable which is merged in the output Observable.

将值映射成内部 observable，并同步订阅和发出。(谁快谁先)

例子：

```js
// RxJS v6+
import { of } from 'rxjs';
import { mergeMap } from 'rxjs/operators';

// 发出 'Hello'
const source = of('Hello');
// 映射成 observable 并将其打平
const example = source.pipe(mergeMap(val => of(`${val} World!`)));
// 输出: 'Hello World!'
const subscribe = example.subscribe(val => console.log(val));
```

例子：

mergeMap不仅可以打平Observable对象，也可以打平Promise对象。

```js
const source = of(2000, 1000);
const example = source.pipe(
  mergeMap(v => new Promise(resolve => setTimeout(() => {resolve(v)}, v)))
);
example.subscribe(console.log); //输出结果1000 2000
```

例子：

**resultSelector 已经不再被推荐使用，请再内部使用map代替。**
~~第二个参数resultSelector的用法：~~(不推荐使用了)

```js
// RxJS v6+
import { of } from 'rxjs';
import { concatMap } from 'rxjs/operators';

// 发出 'Hello' 和 'Goodbye'
const source = of('Hello', 'Goodbye');
// 使用 promise 的示例
const examplePromise = val => new Promise(resolve => resolve(`${val} World!`));
// 返回结果前，第一个参数的结果将传递给第二个参数选择器函数
const example = source.pipe(
  concatMap(val => examplePromise(val), result => `${result} w/ selector!`)
);
// 输出: 'Example w/ Selector: 'Hello w/ Selector', Example w/ Selector: 'Goodbye w/ Selector'
const subscribe = example.subscribe(val =>
  console.log('Example w/ Selector:', val)
);
```



## switchMap()



##range()



##repeat()



## delay()



## fromEvent()



## defer()

defer接受一个函数作为参数，当defer产生的Observable对象被订阅的时候，defer函数参数就会被调用，预期这个函数会返回另一个Observable对象。

```javascript
const a= () => Rx.Observable.of(1,2,3);
Rx.Observable.defer(a).subscribe(x=>console.log(x));
//1,2,3
```







## 1.如何合并observable1(next(1))和observable2(next2)?

使用concat()/merge()/...连接

```js
const observable = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(1);
    subscriber.complete();
  }, 2000);
});
const observable2 = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(2);
    subscriber.complete();
  }, 1000);
});
concat(observable, observable2).subscribe(console.log); //先后输出1,2
```

```js
const observable = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(1);
    subscriber.complete();
  }, 2000);
});
const observable2 = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(2);
    subscriber.complete();
  }, 1000);
});
merge(observable, observable2).subscribe(console.log); //先后输出2,1
```



使用of()和concatAll()/mergeAll()/...

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.complete(); //必须要写的，不然observable2不会执行
});
const observable2 = new Observable(subscriber => {
  subscriber.next(2);
  subscriber.complete();
});
of(observable, observable2).pipe(concatAll()).subscribe(console.log); //输出1,2
```

```javascript
const observable = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(1);
    subscriber.complete();
  }, 2000);
});
const observable2 = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(2);
    subscriber.complete();
  }, 1000);
});
of(observable, observable2).pipe(mergeAll()).subscribe(console.log); //先输出2，在输出1
```



## 2.如何合并observable1(next(observable1_1))和observable2(observable2_1)?

concat() + concatAll()

merge() + mergeAll()

```js
const observable = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(new Observable(subscriber1 => {
      subscriber1.next(1);
      subscriber1.complete(); //必须写
    }));
    subscriber.complete(); //必须写
  }, 2000);
});
const observable2 = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(new Observable(subscriber1 => {
      subscriber1.next(2);
      subscriber1.complete();
    }));
    subscriber.complete();
  }, 1000);
});
concat(observable, observable2).pipe(concatAll()).subscribe(console.log); //先后输出1,2
```

```js
const observable = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(new Observable(subscriber1 => {
      subscriber1.next(1);
      subscriber1.complete();
    }));
    subscriber.complete();
  }, 2000);
});
const observable2 = new Observable(subscriber => {
  setTimeout(() => {
    subscriber.next(new Observable(subscriber1 => {
      subscriber1.next(2);
      subscriber1.complete();
    }));
    subscriber.complete();
  }, 1000);
});
merge(observable, observable2).pipe(mergeAll()).subscribe(console.log); //先后输出2,1
```

