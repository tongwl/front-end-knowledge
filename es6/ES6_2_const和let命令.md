#ES6_2_const和let命令



##ES5的两种声明方式

`var`, `function`


## ES6的六种声明方式

`var`, `function`, `let`, `const`, `class`, `import`



## var声明

1. 用 `var` 声明的变量的作用域是它当前的执行上下文(**全局作用域/函数作用域，并不包括if, for, try/catch等代码块**)，它可以是嵌套的函数，或者对于声明在任何函数外的变量来说是全局。**如果你重新声明一个 JavaScript 变量，它将不会丢失其值。**

   ````js
   var a = 1;
   var a;
   console.log(a); //1
   ````

   ```javascript
   var a = 1;
   var a = undefined;
   console.log(a); //undefined
   ```

   ```js
   var a = 1;
   var a = 2;
   console.log(a); //2
   ```

2. 非严格模式下，当赋值给未声明的变量, 则执行赋值后, 该变量会被隐式地创建为全局变量(它将成为全局对象的属性)；
   严格模式下("use strict")，当赋值给未声明的变量则报错(ReferenceError 异常)。
   *注意：只有执行到该句的时候才会报错，如果不执行就不会报错。*

   例子1：

   ```js
   function test() {
     a = 1;  //会隐式的将a变量变成全局变量
   }
   test();
   console.log(a); //1
   ```
   
   例子2：
   
   ```js
   "use strict"
   function test() {
     a = 1; //抛出 ReferenceError 异常
   }
   test(); //执行到这里的时候，函数内a = 1;会报错
   ```
   
3. 直接调用一个未被声明过的变量，会报错。(typeof除外)

   ```js
   console.log(a);                // 抛出ReferenceError。
   console.log('still going...'); // 打印"still going..."。
   ```

   ```js
   var a;
   console.log(a);                // 打印"undefined"或""（不同浏览器实现不同）。
   console.log('still going...'); // 打印"still going..."。
   ```

4.  用var声明的变量是它所在上下文环境的不可配置属性，非声明变量是可配置的（如非声明变量可以被删除）。

   ```js
   var a = 1;
   b = 2;
   
   delete this.a; // 在严格模式（strict mode）下抛出TypeError，其他情况下执行失败并无任何提示。
   delete this.b;  //严格模式下也会抛出异常
   
   console.log(a, b); // 抛出ReferenceError。
   // 'b'属性已经被删除。
   ```

5. **变量提升**

   **注意：变量的提升只是提升了变量声明，并不会提升该变量的初始化(即赋值操作)**

   ````js
   function test() {
     console.log(a); //undefined
     var a = 1;
     console.log(a); //1
   }
   test();
   ````

6. 多个变量的声明

   ```js
   var a = 0, b = 0;
   ```

   

   ```js
   var a = "A";
   var b = a;
   
   // 等效于：
   
   var a, b = a = "A";
   ```

   

   ```js
   "use strict"
   var x = y, y = 'A';
   console.log(x + y); // undefinedA
   ```

   在这里，`x` 和 `y` 在代码执行前就已经创建了，而赋值操作发生在创建之后。当"`x = y`"执行时，`y` 已经存在，所以不抛出`ReferenceError`，并且它的值是undefined`。所以 `x` 被赋予 undefined 值。然后，`y` 被赋予'A'。于是，在执行完第一行之后，`x === undefined && y === 'A'` 才出现了这样的结果。
   
   ```js
   var x = 0;
   
   function f(){
     var x = y = 1; // x在函数内部声明，y不是！
   }
   f();
   
   console.log(x, y); // 0, 1
   // x 是全局变量。
   // y 是隐式声明的全局变量。 
   ```

   

## let声明

1. let声明变量的作用范围是**代码块{}**，可以是全局，函数(方法)，if/for/try catch等代码块。

   ```js
   "use strict";
   
   {
     var a = 1;  //被声明成全局变量
     let b = 2;  //仅仅在代码块中有效的变量
   }
   console.log(a);
   console.log(b); //Uncaught ReferenceError: b is not defined
   ```
   
   for循环的计数器很适合用let命令：
   
   ```js
   let a = [];
      for(var i = 0; i < 10; i++) { //var i = 0;相当于全局定义
        a[i] = function() {
          console.log(i);
        }
      }
   a[6](); //10
   ```
   
   ```js
   let a = [];
      for(let i = 0; i < 10; i++) { //let i = 0; 在for循环代码块定义了i
        a[i] = function() {
          console.log(i);   //这里调用了i，实际上形成了闭包
        }
      }
   a[6](); //6
   ```

   

2. 另外，`for`循环还有一个特别之处，**就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。**

   ```javascript
   for (let i = 0; i < 3; i++) { //设置循环变量部分作为父作用域，循环体代码块作为了一个子作用域
     let i = 'abc';
     console.log(i);
   }
   // abc
   // abc
   // abc
   ```

   上面代码正确运行，输出了 3 次`abc`。这表明函数内部的变量`i`与循环变量`i`不在同一个作用域，有各自单独的作用域。

   

3. **区别于var声明，let命令不存在变量提升。**

   ```javascript
   // var 的情况
   console.log(foo); // 输出undefined
   var foo = 2;
   
   // let 的情况
   console.log(bar); // 报错ReferenceError
   let bar = 2;
   ```

   ```javascript
   var tmp = new Date();
   
   function f() {
     console.log(tmp);
     if (false) {
       var tmp = 'hello world';  //即使在if(false)里面也会存在变量提升
     }
   }
   
   f(); // undefined
   ```

   

4. 暂时性死区的特点(temporal dead zone，简称 TDZ)。

   **在代码块内，如果存在使用`let`命令声明的变量，那么这个变量在变量声明(语句)前都是不可用的。这在语法上，称为“暂时性死区”**（temporal dead zone，简称 TDZ）。（其实也包括了上面的第3小点: let命令不存在变量提升）

   ```javascript
   if (true) {
     // TDZ开始
     tmp = 'abc'; // ReferenceError
     console.log(tmp); // ReferenceError
   
     let tmp; // TDZ结束
     console.log(tmp); // undefined
   
     tmp = 123;
     console.log(tmp); // 123
   }
   ```

   **在es6之前，使用typeof的操作都是安全的，即任何时候都不会报错。**

   ```js
   console.log(typeof a); //undefined
   var b = null;
   console.log(typeof b); // object
   ```

   **但是从es6开始，let的暂时性死区特点意味着typeof不再是一个百分百安全的操作了。**

   ```javascript
   typeof x; // ReferenceError
   let x;
   ```

   上面代码中，变量`x`使用`let`命令声明，所以在声明之前，都属于`x`的“死区”，只要用到该变量就会报错。因此，`typeof`运行时就会抛出一个`ReferenceError`。

   作为比较，如果一个变量根本没有被声明，使用`typeof`反而不会报错。

   ```javascript
   typeof undeclared_variable // "undefined"
   ```

   另外，下面的代码也会报错，与`var`的行为不同。

   ```javascript
// 不报错
   var x = x;
   
   // 报错
   let x = x;
   // ReferenceError: x is not defined
   ```
   
   上面代码报错，也是因为暂时性死区。**使用`let`声明变量时，只要变量在还没有声明完成前使用，就会报错**。上面这行就属于这个情况，在变量`x`的声明语句还没有执行完成前，就去取`x`的值，导致报错”x 未定义“。

   下面的代码就不会报错。

   ```js
let x;
   x = x;
   ```
   
   

5. 在同一个作用域，不允许重复声明。(let和var和const不能同时声明同一个变量。)

   ```js
   //报错，即使func()没有被执行
   function func() {
     let a = 10;
     var a = 1;
   }
   
   //报错，即使func()没有被执行
   function func() {
     let a = 10;
     let a = 1;
   }
   ```

   ```js
   function func(arg) {
     let arg;
   }
   func() // 报错
   
   function func(arg) {
     {
       let arg;
     }
   }
   func() // 不报错
   ```

   
   
6. var会与window相映射（会挂一个属性），而let不与window相映射。

## 块级作用域

1. ES5只有全局作用域和函数作用域，ES6之后新增了块级作用域。

2. ES6允许块级作用域的任意嵌套。

   ```javascript
   {{{{
     {let insane = 'Hello World'}
     console.log(insane); // 报错
   }}}};
   ```

   ```javascript
   {{{{
     let insane = 'Hello World';
     {let insane = 'Hello World'}
   }}}};
   ```

   块级作用域的出现，实际上使得获得广泛应用的匿名立即执行函数表达式（匿名 IIFE）不再必要了。

   ```javascript
   // IIFE 写法
   (function () {
     var tmp = ...;
     ...
   }());
   
   // 块级作用域写法
   {
     let tmp = ...;
     ...
   }
   ```

3. **ES6 的块级作用域必须有大括号，如果没有大括号，JavaScript 引擎就认为不存在块级作用域。**

   ```javascript
   // 第一种写法，报错
   if (true) let x = 1;
   
   // 第二种写法，不报错
   if (true) {
     let x = 1;
   }
   ```

   上面代码中，第一种写法没有大括号，所以不存在块级作用域，而`let`只能出现在当前作用域的顶层，所以报错。第二种写法有大括号，所以块级作用域成立。

4. 了解略读：块级作用域与函数声明

   函数能不能在块级作用域之中声明？这是一个相当令人混淆的问题。
   **结论：考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。**

   ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。

   ```javascript
   // 情况一
   if (true) {
     function f() {}
   }
   
   // 情况二
   try {
     function f() {}
   } catch(e) {
     // ...
   }
   ```

   上面两种函数声明，根据 ES5 的规定都是非法的。

   但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，因此上面两种情况实际都能运行，不会报错。

   ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于`let`，在块级作用域之外不可引用。

   ```javascript
   function f() { console.log('I am outside!'); }
   
   (function () {
     if (false) {
       // 重复声明一次函数f
       function f() { console.log('I am inside!'); }
     }
   
     f();
   }());
   ```

   上面代码在 ES5 中运行，会得到“I am inside!”，因为在`if`内声明的函数`f`会被提升到函数头部，实际运行的代码如下。

   ```javascript
   // ES5 环境
   function f() { console.log('I am outside!'); }
   
   (function () {
     function f() { console.log('I am inside!'); }
     if (false) {
     }
     f();
   }());
   ```

   ES6 就完全不一样了，理论上会得到“I am outside!”。因为块级作用域内声明的函数类似于`let`，对作用域之外没有影响。但是，如果你真的在 ES6 浏览器中运行一下上面的代码，是会报错的，这是为什么呢？

   ```javascript
   // 浏览器的 ES6 环境
   function f() { console.log('I am outside!'); }
   
   (function () {
     if (false) {
       // 重复声明一次函数f
       function f() { console.log('I am inside!'); }
     }
   
     f();
   }());
   // Uncaught TypeError: f is not a function
   ```

   上面的代码在 ES6 浏览器中，都会报错。

   原来，如果改变了块级作用域内声明的函数的处理规则，显然会对老代码产生很大影响。为了减轻因此产生的不兼容问题，ES6 在[附录 B](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)里面规定，浏览器的实现可以不遵守上面的规定，有自己的[行为方式](http://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6)。

   - 允许在块级作用域内声明函数。
   - 函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部。
   - 同时，函数声明还会提升到所在的块级作用域的头部。

   注意，上面三条规则只对 ES6 的浏览器实现有效，其他环境的实现不用遵守，还是将块级作用域的函数声明当作`let`处理。

   根据这三条规则，浏览器的 ES6 环境中，块级作用域内声明的函数，行为类似于`var`声明的变量。上面的例子实际运行的代码如下。

   ```javascript
   // 浏览器的 ES6 环境
   function f() { console.log('I am outside!'); }
   (function () {
     var f = undefined;
     if (false) {
       function f() { console.log('I am inside!'); }
     }
   
     f();
   }());
   // Uncaught TypeError: f is not a function
   ```

   **考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。**如果确实需要，也应该写成函数表达式，而不是函数声明语句。

   ```javascript
   // 块级作用域内部的函数声明语句，建议不要使用
   {
     let a = 'secret';
     function f() {
       return a;
     }
   }
   
   // 块级作用域内部，优先使用函数表达式
   {
     let a = 'secret';
     let f = function () {
       return a;
     };
   }
   ```



## const声明

1. `const`声明一个只读的常量。一旦声明，常量的值就不能改变。

   ```javascript
   const PI = 3.1415;
   PI // 3.1415
   
   PI = 3;
   // TypeError: Assignment to constant variable.
   ```

2. `const`一旦声明变量，就必须立即初始化，不能留到以后赋值。

   ```javascript
   const foo;
   // SyntaxError: Missing initializer in const declaration
   ```

   上面代码表示，对于`const`来说，只声明不赋值，就会报错。

3. `const`和`let`同样的特点：

   * 只在声明所在的块级作用域内有效。
   * 声明的变(常)量不能提升
   * 同样存在暂时性死区
   * 不可重复声明

4. `const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。声明一个对象(包括数组)为常量的时候，要知道`const`只能保证这个指针指向的对象地址是不能改变的，至于对象内部的结构，就不能控制了。

   ```javascript
   const foo = {};
   
   // 为 foo 添加一个属性，可以成功
   foo.prop = 123;
   foo.prop // 123
   
   // 将 foo 指向另一个对象，就会报错
   foo = {}; // TypeError: "foo" is read-only
   ```

   ```javascript
   const a = [];
   a.push('Hello'); // 可执行
   a.length = 0;    // 可执行
   a = ['Dave'];    // 报错
   ```

   **如果真的想将对象冻结，应该使用`Object.freeze`方法。**

   ```javascript
   const foo = Object.freeze({});
   
   // 常规模式时，下面一行不起作用；
   // 严格模式时，该行会报错
   foo.prop = 123;
   ```

   上面代码中，常量`foo`指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

   除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

   ```javascript
   var constantize = (obj) => {
     Object.freeze(obj);
     Object.keys(obj).forEach( (key, i) => {
       if ( typeof obj[key] === 'object' ) {
         constantize( obj[key] );
       }
     });
   };
   ```



## 顶层对象的说明

顶层对象，在浏览器环境指的是`window`对象，在 node 指的是`global`对象。**ES5 之中，顶层对象的属性与全局变量是等价的。**

```javascript
window.a = 1;
a // 1

a = 2;
window.a // 2
```

上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

顶层对象的属性与全局变量挂钩，被认为是 JavaScript 语言最大的设计败笔之一。这样的设计带来了几个很大的问题，首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。另一方面，`window`对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

**ES6 为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。**

```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

上面代码中，全局变量`a`由`var`命令声明，所以它是顶层对象的属性；全局变量`b`由`let`命令声明，所以它不是顶层对象的属性，返回`undefined`。

