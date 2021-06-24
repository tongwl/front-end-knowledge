# ES6_6_async_await

MDN参考文档：https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Asynchronous/Async_await#%E4%BD%BF%E7%94%A8_asyncawait_%E9%87%8D%E5%86%99_promise_%E4%BB%A3%E7%A0%81

阮一峰参考文档：https://es6.ruanyifeng.com/#docs/async

**简单来说，它们是基于promises的语法糖，使异步代码更易于编写和阅读。**

## async关键字

**`async`函数返回一个 Promise 对象。**

async的几种 写法：

```javascript
async function hello() { return "Hello" };
```

```javascript
let hello = async function() { return "Hello" };
```

```javascript
let hello = async () => { return "Hello" };
```

```javascript
private async hello() {
	return "Hello";
}
```

```javascript
const obj = {
  async hello() {
    return 'hello';
  }
}
```

`async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

例子1：

```javascript
async function f() {
  return 'hello world';
}

f().then(v => console.log(v)) // "hello world"
```

**例子2: async使用return返回即使是Error，也在then的第一次回调函数中获取**

```javascript
async function f() {
  return new Error('I am error');
}

f().then(err => console.log(err.message)) // "I am error"
```

**例子3: async使用的throw抛出的异常，则在then的第二个回调函数或则catch中获取**

```javascript
async function f() {
  throw new Error('I am error');
}

f().then(() => {}, err => console.log(err.message)) // "I am error"
f().catch(err => console.log(err.message)) // "I am error"
```

**`async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出(throw)错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。**

```javascript
public async test() {
    const a = await new Promise((resolve, reject) => {
      setTimeout(() => {
        console.log(1);
        resolve(2);
      });
    });
    console.log(a); //会等到a返回值，才执行
}

this.test().then(console.log); // undefined, 这里的then得到的值是看test的返回值


//执行结果的顺序是：1， 2，undefined
```

**`await`命令后面的 Promise 对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到。**

```javascript
async function f() {
  await Promise.reject('I am error');
}

f()
.then(v => console.log(v))
.catch(e => console.log(e)) // "I am error"
```

**如果await命令后面的Promise都是resolve状态，则async的返回值由return(或throw error、报错)决定，如果没有return则是undefined。**

```javascript
async function f() {
  await Promise.resolve('right');
}

f().then(v => console.log(v)) //undefined
```

```javascript
async function f() {
  await Promise.resolve('right');
  return 'I am right';
}

f().then(console.log) // "I am right"
```

**任何一个`await`语句后面的 Promise 对象变为`reject`状态，那么整个`async`函数都会中断执行，并且被catch捕获。**

```javascript
async function f() {
  await Promise.reject('I am error');
  const a = await Promise.resolve('I am right'); //不会执行到
  console.log(a); //不会执行到
}

f().then(() => {}, console.log) // "I am error"
f().catch(console.log) // "I am error"
```

**如果`await`后面的异步操作出错，那么等同于`async`函数返回的 Promise 对象被`reject`。**

```javascript
async function f() {
  	await new Promise(() => {
      throw new Error('I am error');
    })
    const result = await Promise.resolve('I am right'); //不会被执行到
    console.log(result); //不会被执行到
}

f().then(() => {}, res => console.log(res.message)) // "I am error"
f().catch(res => console.log(res.message)) // "I am error"
```

**不是await的reject并不会中断执行。**

```javascript
async function f() {
  Promise.reject('I am error'); //没有await的reject并不会终中断执行
  const result = await Promise.resolve('I am right');
  console.log(result); // "I am right"
}
```

**有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个`await`放在`try...catch`结构里面，这样不管这个异步操作是否成功，第二个`await`都会执行。**

```javascript
async function f() {
  	try {
      await Promise.reject('I am error');
    } catch(e) {
      console.log(e);  // "I am error"
    }
    const result = await Promise.resolve('I am right');
    console.log(result); // "I am right"
    return 'done';
}

f().then(console.log); // "done"

//依次输出 "I am error", "I am right", "done"
```

**另一种方法是`await`后面的 Promise 对象再跟一个`catch`方法，处理前面可能出现的错误。**

```javascript
async function f() {
  	const result1 = await Promise.reject('I am error1').catch(e => {
      console.log(e);  // "I am error1"
      return "I am error2";
    });
    console.log(result1); // "I am error2"
    const result = await Promise.resolve('I am right');
    console.log(result); // "I am right"
    return 'done';
}

f().then(console.log); // "done"

//依次输出 "I am error1", "I am error2", "I am right", "done"
```

**如果有多个`await`命令，可以统一放在`try...catch`结构中，当然当第一个报错了，同在try catch块后面的语句就不会执行了。**

```javascript
async function f() {
  	try {
      await Promise.reject('I am error1');
      await Promise.reject('I am error2'); //并不会被执行到
      await Promise.reject('I am error3'); //并不会被执行到
    } catch(e) {
      console.log(e);  // "I am error1"
    }
    const result = await Promise.resolve('I am right');
    console.log(result); // "I am right"
    return 'done';
}

f().then(console.log); // "done"

//依次输出 "I am error1", "I am error2", "I am right", "done"
```



**`await`命令只能用在`async`函数之中，如果用在普通函数，就会报错。**

**await只是暂停了当前异步函数内部的执行，并不会影响异步函数的外部执行。**

例子1：

```javascript
async function f() {
  	console.log(111);
}

f();
console.log(222);

//依次输出 111, 222
```

例子2：

```javascript
async function f() {
  	console.log(111);
  	const result = await 3333;
    console.log(result);
}

f();
console.log(222);

//依次输出 111, 222, 333
//有输出结果可知，await只是阻塞了异步函数内部的执行，并不会 影响异步函数的外部执行。
```

