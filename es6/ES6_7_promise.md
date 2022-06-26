# ES6_7_promise

Promise两大特点：

+ 对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。
+ 一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：**从**`pending`**变为`fulfilled`和从`pending`变为`rejected`**。**只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为** **resolved（已定型）**。**如果改变已经发生了，你再对**`Promise`**对象添加回调函数，也会立即得到这个结果。**这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

Promise的缺点：

+ 无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。
+ 如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。
+ 当处于`pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

例子1：

```javascript
const p =new Promise((resolve,reject) => {
  //做一些异步操作
  window.setTimeout(()=> {
    console.log('执行完成');
    resolve('I am resolve');   //会被p.then捕获
  },2000);
});

p.then(response => {
  console.log(response);  //I am resolve
});
```

例子2：

```javascript
const p = new Promise((resolve, reject) => {
  window.setTimeout(() => {
    console.log('执行完成');
    reject('I am reject');  //可以被then的第二个参数catch到
  }, 2000);
});

p.then((response) => {
  console.log('执行不到');
  console.log(response);
}, (response) => {
  console.log('执行到了');
  console.log(response); // I am reject
});
```

例子3：

```javascript
var p =new Promise((resolve,reject) => {
  //做一些异步操作
  window.setTimeout(()=> {
    console.log('执行完成');
    reject('I am reject');   //可以被p.catch catch到
  },2000);
});

p.catch(error => {
  console.log(error);  //I am reject
});
```

例子4：

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise1');
  resolve(1);
  console.log('Promise2');
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise1
// Promise2
// Hi!
// resolved
```

简单总结：

* **resolve会被then的第一个回调函数接收到。**
* **reject和异常(throw new Error()等)会被then的第二个回调函数或则catch方法接收到。**
* **如果then的第二个函数和catch方法都写了，那么只会执行then的第二个回调函数，catch里面不会执行，如果紧跟着是then，则会执行。（除非then里面报错了，就会在catch里面执行）。**
* **catch还有一个作用就是抓取异常，包括then方法里面的异常， then方法指定的回调函数，如果运行中抛出错误，也会被catch方法捕获。**

Promise的出现就是为了解决链式地狱的问题。

```javascript
function runAsync() {
  return new Promise((resolve,reject) => {
    window.setTimeout(() => {
      resolve('我是第一个');
    }, 2000);
  });
}

function runAsync2() {
  return new Promise((resolve,reject) => {
    window.setTimeout(() => {
      resolve('我是第二个');
    }, 2000);
  });
}

function runAsync3() {
  return new Promise((resolve,reject) => {
    window.setTimeout(() => {
      resolve('我是第三个');
    }, 2000);
  });
}

runAsync().then(response => {
  console.log(response);  //我是第一个
  return runAsync2();
}).then(response => {  //response由上个then里面的then而来
  console.log(response);  //我是第二个
  return runAsync3();
}).then(response => {
  console.log(response);  //我是第三个
});

//then里面的return不一定返回promise对象，也可以直接是数据，例子：
runAsync().then(response => {
  console.log(response);  //我是第一个
  return runAsync2();
}).then(response => {  //response由上个then里面的then而来
  console.log(response);  //我是第二个
  return 1111;         //也可以直接返回数据
}).then(response => {
  console.log(response);  //1111
});

```

promise对象中返回的值除了正常的值外，还可以是另外一个Promise。

```javascript
const p1 = new Promise(function (resolve, reject) {
  // ...
});

const p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
})
```

上面代码中，`p1`和`p2`都是 Promise 的实例，但是`p2`的`resolve`方法将`p1`作为参数，即一个异步操作的结果是返回另一个异步操作。

注意，这时`p1`的状态就会传递给`p2`，也就是说，`p1`的状态决定了`p2`的状态。如果`p1`的状态是`pending`，那么`p2`的回调函数就会等待`p1`的状态改变；如果`p1`的状态已经是`resolved`或者`rejected`，那么`p2`的回调函数将会立刻执行。

```javascript
const p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error('fail')), 3000)
})

const p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000)
})

p2
  .then(result => console.log(result))
  .catch(error => console.log(error))
// Error: fail
```

上面代码中，`p1`是一个 Promise，3 秒之后变为`rejected`。`p2`的状态在 1 秒之后改变，`resolve`方法返回的是`p1`。由于`p2`返回的是另一个 Promise，导致`p2`自己的状态无效了，由`p1`的状态决定`p2`的状态。所以，后面的`then`语句都变成针对后者（`p1`）。又过了 2 秒，`p1`变为`rejected`，导致触发`catch`方法指定的回调函数。

由上面的例子可知：**promise嵌套promise，在父promise没有报错，没有reject的情况下，整个promise返回的结果和状态(resolve or reject)由子promise决定。**

注意，调用`resolve`或`reject`并不会终结 Promise 的参数函数的执行。

```javascript
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
```



## Promise.prototype.then()

**`then`方法的第一个参数是`resolved`状态的回调函数，第二个参数是`rejected`状态的回调函数，它们都是可选的。**

```javascript
new Promise(resolve => resolve('right')).then(console.log); // "right"
new Promise(resolve => resolve('right')).then(console.log, undefined); // "right"
new Promise(resolve => resolve('right')).then(console.log, null); // "right"
new Promise((resolve, reject) => reject('err')).then(() => {}, console.log); // "err"
new Promise((resolve, reject) => reject('err')).then(undefined, console.log); // "err"
new Promise((resolve, reject) => reject('err')).then(null, console.log); // "err"
```

**`then`方法返回的是一个新的`Promise`实例（注意，不是原来那个`Promise`实例）。**因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

```javascript
getJSON("/posts.json").then(function(json) {
  return json.post;
}).then(function(post) {
  // ...
});
```

采用链式的`then`，可以指定一组按照次序调用的回调函数。这时，前一个回调函数，有可能返回的还是一个`Promise`对象（即有异步操作），这时后一个回调函数，就会等待该`Promise`对象的状态发生变化，才会被调用。

```javascript
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function (comments) {
  console.log("resolved: ", comments);
}, function (err){
  console.log("rejected: ", err);
});
```

上面代码中，第一个`then`方法指定的回调函数，返回的是另一个`Promise`对象。这时，第二个`then`方法指定的回调函数，就会等待这个新的`Promise`对象状态发生变化。如果变为`resolved`，就调用第一个回调函数，如果状态变为`rejected`，就调用第二个回调函数。

**then的返回结果决定了后续then/catch的走向。**

```typescript
ngOnInit(): void {
  this.test().then(res => {
    console.log('success'); //会执行到
    console.log(res);
  }, err => {
    console.log('err');   //不会执行到
    console.log(err);
  });
}

private test() {
  return this.http
    .get('assets/tsconfig.json').toPromise().then(() => {
      return 1;
    });
}
```

```javascript
ngOnInit(): void {
  this.test().then(res => {
    console.log('success'); //会执行到
    console.log(res);
  }, err => {
    console.log('err');   //不会执行到
    console.log(err);
  });
}

private test() {
  return this.http
    .get('assets/tsconfig.json').toPromise().then(() => {
      return Promise.resolve(1);
    });
}
```

```javascript
ngOnInit(): void {
  this.test().then(res => {
    console.log('success'); //不会执行到
    console.log(res);
  }, err => {
    console.log('err');   //会执行到
    console.log(err);
  });
}

private test() {
  return this.http
    .get('assets/tsconfig.json').toPromise().then(() => {
      return Promise.reject('error');
    });
}
```

```javascript
ngOnInit(): void {
  this.test().then(res => {
    console.log('success'); //不会执行到
    console.log(res);
  }, err => {
    console.log('err');   //会执行到
    console.log(err);
  });
}

private test() {
  return this.http
    .get('assets/tsconfig.json').toPromise().then(() => {
      throw new Error('error');
    });
}
```



##Promise.prototype.catch()

catch方法是then(undefined, rejection)的别名，用于指定发生错误时的回调函数。catch不同于then的第二个回调函数，catch也能够抓取then的回调函数的运行中抛出的错误，同时catch具有冒泡性质，错误会一直向后传递，直到被捕获为止，也就是说：错误总是会被下一个catch语句捕获到。例子：

```javascript
function test1() {
  return new Promise((resolve, reject) => {
     throw new Error('error message');
     console.log('我是不会被执行到的');   //不会执行
  });
}
test1().catch(err => {
  console.log(err);
});
```

**reject(new Error())和 throw new Error()效果很类型，但是他们有他们的不同，reject(new Error())后面的代码还能够执行；但是throw new Error()后面的代码就不会执行了。**

```javascript
function test() {
  return new Promise((resolve, reject) => {
    reject(new Error('我是error'));
    console.log('我还能被执行');
  });
}

test().then(null, err => {
  console.log(err);
});
 
//vs
 
function test(){
  return new Promise((resolve, reject) => {
    throw new Error('我是error');
    console.log('我不会被执行');
  });
}

test().then(null, err => {
  console.log(err);
});
```

**Promise的状态一旦改变，就永久保持该状态，不会再变了。**

```javascript
function test() {
  return new Promise((resolve, reject) => {
    resolve('aa');
    console.log('会被执行1');
    reject('bb');
    console.log('会被执行2');
    reject(new Error('error'));
    console.log('会被执行3');
    throw new Error('error');
    console.log('不会被执行');
  });
}
test().then(response => {
  console.log('response会被执行');
  console.log(response);  //aa  因为Promise 的状态一旦改变，就永久保持该状态，不会再变了。
}, error => {
  console.log('error不会被执行');
  console.log(error);
});
```

如果 Promise 状态已经变成`resolved`，再抛出错误是无效的。(**Promise 的状态一旦改变，就永久保持该状态，不会再变了。**)

```javascript
const promise = new Promise(function(resolve, reject) {
  resolve('ok');
  console.log('会执行');  //会被执行
  throw new Error('err'); //会被执行，但是不会抛出异常
  console.log('不会执行');  //不会被执行
});
promise
  .then(function(value) { console.log(value) })
  .catch(function(error) { console.log(error) });
// ok
```

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获。

```javascript
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```

**一般来说，不要在`then()`方法里面定义 Reject 状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。catch 可以捕获前面`then`方法执行中的错误，也更接近同步的写法（`try/catch`）。因此，建议总是使用`catch()`方法，而不使用`then()`方法的第二个参数。**

