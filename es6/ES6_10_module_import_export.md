# ES6_10_module_import_export

重点记录：

*  ES6 模块是编译时加载(静态加载)
*  ES6 的模块自动采用严格模式，不管你有没有在模块头部加上`"use strict";`
* `export`和`import`的变量可以使用关键字**`as`**重命名
* `export`语句输出的接口，是动态的
* `export`和`import`只能出现在模块顶层(但可以是任何位置)，不能处于块级作用域。
* `import`命令引入的变量都是只读的，但是，如果`import`的是一个对象，改写对象的属性是允许的。
* `import`命令具有提升效果，会提升到整个模块的头部首先执行。
* 由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。
* `import`语句是 Singleton 模式，也就是说不管import多少次，实际上被import的那个模块只会执行一次。
* 一个moudle文件只允许一个default存在。



## export命令

####`export`可以导出变量，函数，类等。

写法1：

```javascript
export const name = 'tong';
export const age = 18;
export function test() {}
export class Father {}
```

写法2：

```javascript
const name = 'tong';
const age = 18;
function test() {}

export {
  name,
  age,
  test
}
```



####注意：`export`建立的是与变量之间的联系，直接`export`一个值是会报错的。

错误写法：

```javascript
// 报错
export 1;

// 报错
var m = 1;
export m; //实质上export的值就是1

//报错
function func() {}
export func;

//报错
class Father {}
export Father;
```

正确写法：

```javascript
//正确
export var m = 1;

//正确
var m = 1;
export {m};

//正确
var n = 1;
export {n as m};

//正确
export function func() {}

//正确
function func() {}
export {func};

//正确
export class Father {}

//正确
class Father {}
export {Father};
```

`export function` vs `default function` 正确写法

```javascript
//export.js
export function test() {
  console.log('I am right');
}

//import.js
import {test} from './test';
test() {
  test();
},
```

vs

```javascript
//export.js
export default function test() {
  console.log('I am right');
}

//import.js
import test from './test';
test() {
  test();
},
```



####使用`as`重命名

```javascript
const name = 'tong';
const age = 18;

export {
  name as userName,
  age as userAge,
}
```



####`export`语句输出的值，是动态的。

> 这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是值的缓存，不存在动态更新。 	

例子1：

export.js

```javascript
let num = 0;
setInterval(() => {
  num++;
}, 1000);

export { num };
```

import.js

```javascript
import { num } from './test';

excute() {
  setInterval(() => {
    console.log(num); //依次输出1, 2, 3, 4, 5...
  }, 1000);
}
```

例子2：

export.js

```javascript
let a = 1;
export {a};
a++;
```

import.js

```javascript
import {a} from './test';
test() {
  console.log(a); //输出2
},
```

上面输出2，因为export输出的值是动态的，当import.js import它的时候，a值已经是2了。



#### export只能出现在模块顶层(的任何位置)，如果处于块级作用域就会报错。

错误写法

```javascript
//错误写法
function test() {
  export let num = 0;
}

//错误写法
{
  export let sum = 0;
}
```



#### export default

```javascript
//正确写法
const a = 1;
export default a; //相当于直接export了1

//正确写法
export default 1;

//正确写法
export default function foo() {
  console.log('foo');
}

//正确写法
const test = () => console.log(1);
export default test;

// 正确写法
function foo() {
  console.log('foo');
}
export default foo;

//错误写法
export default const a = 1;

//错误写法
export default const test = () => console.log(1);
```

import引用的时候不需要`{}`，直接取一个名字即可。

```javascript
// export-default.js
export default function () {
  console.log('foo');
}

//import.js
import foo from './test'; //foo可以是任何名
test() {
  foo();
},
```

**本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法都是有效的。**

```javascript
//import.js
function sayHello() {
  console.log('hello');
}
export {sayHello as default};

//export.js
import {default as foo} from './test';
test() {
  foo();
}
```

```javascript
//import.js
function sayHello() {
  console.log('hello');
}
export {sayHello as default};

//export.js
import foo from './test';
test() {
  foo();
}
```

```javascript
//import.js
export default function sayHello() {
  console.log('hello');
}

//export.js
import foo from './test';
test() {
  foo();
}
```

```javascript
//import.js
export default function sayHello() {
  console.log('hello');
}

//export.js
import {default as foo} from './test';
test() {
  foo();
}
```

正是因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句(函数申明是可以的)。

```javascript
// 正确
export var a = 1;

// 正确
var a = 1;
export default a;

// 正确
export default 42;

//正确
export function sayHello() {
  console.log('hello');
}

// 正确
export default function sayHello() {
  console.log('hello');
}

// 错误
export default var a = 1;

// 错误
export 42;
```

**一个moudle文件只允许一个default存在。**

```javascript
//错误
export default 1;
export default 2;
```



##import命令

####使用大括号接受正常的变量

```javascript
//export.js
export const val = 1;

// import.js
import { val } from './export.js';
```



#### 不使用大括号的方式来接受default变量，且名字可以是任意合法变量

```javascript
//export.js
const val = 1;
export default val;

// import.js
import otherName from './export.js';
```



#### 模块的整体加载

```javascript
import * from './module';
```

例子：

```javascript
//import.js
const username = 'tong';
const age = 18;
export {
  username,
  age
}

//export.js
import {username, age} from './test';
test() {
  console.log(username); //"tong"
  console.log(age); //18
},
```

等同于

```javascript
//import.js
const username = 'tong';
const age = 18;
export {
  username,
  age
}

//export.js
import * as module from './test';
test() {
  console.log(module.username); //"tong"
  console.log(module.age); //18
},
```



#### 使用`as`关键字重命名

```javascript
import { lastName as surname } from './profile.js';
```



#### `import`命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口。

```javascript
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;
```

####但是如果是对象，是可以修改其属性的。

```javascript
//export.js
export let obj = {
  name: "tong",
  age: 18
};


//import.js
import { obj } from './test';
excute() {
  console.log(obj.name); //"tong"
  obj.name = 'lily';
  console.log(obj.name); // "lily"
}
```

对象的属性可以被成功改写，并且其他模块也可以读到改写后的值。不过，这种写法很难查错，**建议凡是输入的变量，都当作完全只读，不要轻易改变它的属性。**



####`import`命令具有提升效果，会提升到整个模块的头部首先执行。

```javascript
foo();

import { foo } from 'my_module';
```

上面的代码不会报错，因为`import`的执行早于`foo`的调用。这种行为的本质是，`import`命令是编译阶段执行的，在代码运行之前。



#### 由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构是错误的

```javascript
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

上面三种写法都会报错，因为它们用到了表达式、变量和`if`结构。在静态分析阶段，这些语法都是没法得到值的。



####`import`语句会执行所加载的模块，因此可以有下面的写法。

```javascript
import 'lodash';
```

上面代码仅仅执行`lodash`模块，但是不输入任何值。

例子：

```javascript
//import.js
const user  = 'tong';
export {user};
let a = 1;
let b = 2;
let c = a + b;
console.log(c); 
console.log('import被执行'); 


//export.js
import './test';


//依次输出3和'import被执行'
```



####`import`语句是 Singleton 模式，尽管一个module文件被多次import，但是实际只执行一次。

例子1：

```javascript
//import.js
const user  = 'tong';
export {user};
let a = 1;
let b = 2;
let c = a + b;
console.log(c); 
console.log('import被执行'); 


//export.js
import './test';
import './test';
import './test';
import './test';
import './test';
import './test';
import './test';

//只会输出一次的3和'import被执行'
```

上面代码加载了两次`lodash`，但是只会执行一次。

例子2：

```javascript
import { foo } from 'my_module';
import { bar } from 'my_module';

// 等同于
import { foo, bar } from 'my_module';
```



##export和import的复合写法

如果在一个模块之中，先输入后输出同一个模块，`import`语句可以与`export`语句写在一起。

```javascript
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```

上面代码中，`export`和`import`语句可以结合在一起，写成一行。但需要注意的是，写成一行以后，`foo`和`bar`实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用`foo`和`bar`。

模块的接口改名和整体输出，也可以采用这种写法。

```javascript
// 接口改名
export { foo as myFoo } from 'my_module';

// 整体输出
export * from 'my_module';
```

**默认接口的写法如下。**

```javascript
export { default } from 'foo';
```

具名接口改为默认接口的写法如下。

```javascript
export { es6 as default } from './someModule';

// 等同于
import { es6 } from './someModule';
export default es6;
```

同样地，默认接口也可以改名为具名接口。

```javascript
export { default as es6 } from './someModule';
```

ES2020 之前，有一种`import`语句，没有对应的复合写法。

```javascript
import * as someIdentifier from "someModule";
```

[ES2020](https://github.com/tc39/proposal-export-ns-from)补上了这个写法。

```javascript
export * as ns from "mod";

// 等同于
import * as ns from "mod";
export {ns};
```



## 其他

####一个模块文件可以export多条内容，import文件也可以import多种类型。

例子1：

```javascript
//export.js
export const a = 1;
export function test() {
  console.log('test');
}
export default 3;
const c = 2;
export {c};

//import.js
import age, {user, func, address} from './test';

test() {
  console.log(age); //18
  console.log(user); //'tong'
  console.log(address); //'hangzhou'
  func(); //'func'
}
```

例子2：

```javascript
//export.js
export const a = 1;
export function test() {
  console.log('test');
}
export default 3;
const c = 2;
export {c};

//import.js
import age, * as user from './test';

test() {
  console.log(age); //18
  console.log(user.user); //'tong'
  console.log(user.address); //'hangzhou'
  user.func(); //'func'
},
```



####export两个不同名，但是同一个值的接口。

```javascript
export function each(obj, iterator, context) {
  // ···
}

export { each as forEach };
```





## import()

https://es6.ruanyifeng.com/#docs/module#import

es2020新增

