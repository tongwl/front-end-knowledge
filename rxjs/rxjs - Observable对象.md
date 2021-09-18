# Rxjs - Observable对象



1.

在angular中，可以用 import的方式导入rxjs操作符等，例如：`import {interval} from 'rxjs';`，`import take from 'rxjs/operators';`。

而在原生的js中，我们可能直接使用 `<script src="https://unpkg.com/@reactivex/rxjs@version/dist/global/rxjs.umd.js">`的方式引入rxjs，引用的方式像`rxjs.interval(500).pipe(rxjs.operators.take(4).subscribe(console.log));`，为了方便移植，我们可以用以下方法来解决：

```javascript
const {interval} = rxjs;
const {take} = rxjs.operators;

//以下代码就可以完全复制到angular中。
interval(500).pipe(take(4)).subscribe(console.log);
```



2.

Observable对象如何转换成Promise对象?

[官网参考][https://rxjs.dev/deprecations/to-promise]

Observable对象区别于Promise对象，Observable有可能异步接受0个，1个或则多个结果，而Promise只会接受一个结果(resolve)。

**所以存在一个问题，Observable的第几个结果应该转换成Promise呢？**

从RxJS7开始，我们有3个转换方法：

* toPromise()
* lastValueFrom()
* firstValueFrom()

RxJS7更加推荐使用lastValueFrom()和firstValueFrom()。

1. **老方法：toPromise()***
   **toPromise()实际上取的是Observable的是最后一个值，注意，这里的最后一个值有可能是subscriber.next()，也可能是subscriber.error()。**
   例子：

   ```javascript
   of(1, 2, 3).toPromise().then(console.log); //3
   ```

   例子：

   ```javascript
   const observable = new Observable(subscriber => {
     subscriber.next(1);
     subscriber.next(2);
     setTimeout(() => {
       subscriber.next(3);
       subscriber.error('I am error.');
     }, 3000);
   });
   observable.toPromise().then(res => {
     console.log('不会被调用到');
     console.log(res);
   }).catch(err => {
     console.log('error被调用');
     console.log(err); //I am error.
   });
   ```

   例子：

   ```js
   const observable = new Observable(subscriber => {
     subscriber.next(1);
     subscriber.next(2);
     setTimeout(() => {
       subscriber.next(3);
       subscriber.complete();
       subscriber.error('I am error.');
     }, 3000);
   });
   observable.toPromise().then(res => { //3秒后调用
     console.log('next会被调用到');
     console.log(res); //3
   }).catch(err => {
     console.log('不会被调用');
     console.log(err);
   });
   ```

2. 新方法：lastValueFrom() --参考官网

3. 新方法：firstValueFrom() --参考官网



3.

Promise对象如何转化成Observable对象？

使用from操作符。

```javascript
imort { from } from 'rxjs/operators';
from(Promise对象);
```



## Observable

####简单例子：

```javascript
import { Observable } from 'rxjs';

const observable = new Observable(function subscribe(subscriber) {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});

console.log('before subscribe');
observable.subscribe({
  next(x) {
    console.log('got value ' + x);
  },
  error(err) { console.error('something wrong occurred: ' + err); },
  complete() {console.log('done');},
});
console.log('after subscribe');

//得到的结果
before subscribe
got value 1
got value 2
got value 3
after subscribe
got value 4
done
```

**Function**, **Generator**, **Promise**, **Observable**对比

* Function - 同步返回一个结果
* Generator - 迭代返回0或多个结果
* Promise - 异步返回一个结果
* Observable - 可以同步或异步地返回零到（潜在的）无限值。



####Observable和function一样，都是惰性运算

如果我们不去调用一个函数，这个函数内部并不会执行。

```javascript
function test() {
	console.log('我不会执行到');
}

function test2() {
	console.log('我会执行到');
}
test2();
```

**同理对于Observable对象，如果我们不subscribe一个Observable对象，Obserable对象内部并不会执行。**

```javascript
new Observable(function subscribe(subscriber) {
  console.log('我并不会执行到');
});

new Observable(subscriber => {
      console.log('我会被执行到');
    }).subscribe();

new Observable(subscriber => {
      console.log('我会被执行到');
    }).subscribe(undefined);

new Observable(subscriber => {
  console.log('我会被执行到');
}).subscribe(() => {});

new Observable(subscriber => {
  console.log('我会被执行到');
  //@ts-ignore
}).subscribe({});
```



####Observable 能够同步或异步地传递值。

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  setTimeout(() => {
    subscriber.next(2);
  });
});
console.log('start');
observable.subscribe({
  next(v) {console.log(v);}
});
console.log('end');

//得到的结果
start
1
end
2
```



#### 如何创建一个Observable对象

* new Observable(subscriber => {});
* 使用**`of`**, **`from`**, **`interval`** 等operator操作符创建。

#####1.使用new Observable方式创建Observable对象。

Observable构造函数接收一个subscribe回调函数，回调函数带有一个subscriber参数。

```javascript
new Observable(subscriber => {
  subscriber.next(1);
  subscriber.unsubscribe();
  subscriber.next(2);
});
```

**重点说一说subscriber参数：**

subscriber是Subscriber对象实例，Subscriber构造函数有多个方法：**`next`**, **`complete`**, **`error`**, **`unsubscribe`**, 因为Subscriber构造函数继承于Subscription对象，所以Subscriber内部的unsubscribe方法是调用了Subscription的unsubscribe方法(super.unsubscribe())。

- "next": 发送一个值：Number, a String, an Object 等类型，它是最重要的类型，代表数据传递给a subscriber
- "error" notification: 发送 a JavaScript Error or exception，会触发observer的error方法
- "complete" notification: 不发送任何值，仅仅代表完成，会触发observer的complete方法
- ~~"unsubscribe": 不发送任何值，表示取消订阅~~

unsubscribe不怎么使用。

**重点：**

**在 Observable Execution 中，可以传递零到无限的 Next 通知。 如果发送了 Error 或 Complete 或 Unsubscribe通知，则此后无法发送任何其他通知。**

例子：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error() {console.log('error')}
});

//得到的结果
1
2
3
```

例子：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error() {console.log('error')}
});

//得到的结果
1
2
3
complete
```

例子：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  setTimeout(() => {
    subscriber.next(3);
  }, 3000);
  subscriber.complete();
});
observable.subscribe(res => {
  console.log(res);
}, () => {}, () => {
  console.log('complete');
});

//得到的结果
1
2
complete
```

例子：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.error('I am error');
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error(err) {console.log(err)}
});

//得到的结果
1
2
3
I am error
```

例子4：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.error('I am error');
  subscriber.next(4); //该值并不会被传递给Observer对象
  subscriber.complete(); //并不会被传递给Observer对象的complete方法
  console.log('我还是能被执行');
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error(err) {console.log(err)}
});

//得到的结果
1
2
3
I am error
我还是能被执行
```

例子5：

```javascript
const observable = new Observable(subscriber => {
  try {
    subscriber.next(1);
    subscriber.next(2);
    subscriber.next(3);
    subscriber.complete();
  } catch (e) {
    subscriber.error(e); // delivers an error if it caught one
  }
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error(err) {console.log(err)}
});

//得到的结果
1
2
3
complete
```

例子6：

```javascript
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.unsubscribe();
  subscriber.next(4); //该值并不会被传递给Observer对象
  subscriber.complete(); //并不会被传递给Observer对象的complete方法
});

observable.subscribe({
  next(n) {console.log(n)},
  complete() {console.log('complete')},
  error(err) {console.log(err)},
});

//得到的结果
1
2
3
```

#### 如何订阅一个Observable对象

```javascript
observable.subscribe(console.log);
```

使用`.subscribe`来订阅。我们在使用new Observable()定义一个Observable对象的时候，传入的回调函数有时候这样写:

```javascript
const observable = new Observable(function subscribe(subscriber) {
  subscriber.next(1);
});
observable.subscribe(console.log); //这里的.subscribe与上述new Observale(function subscribe(..))名字对应哦
```



### 如何取消订阅

**重要点：一般Subscription对象才能unsubscribe(),**

Observable.subscribe()订阅的返回结果就是Subscription对象。

因为Observable的执行可能是无限的，我们可能想要在有限的次数就结束它，此时就需要unsubscribe()。

当你订阅时，你会得到一个 Subscription，它代表正在进行的执行。 只需调用 unsubscribe() 即可取消执行。



## Observer

