# ES6_8_class

#####重点：

1. **申明一个类可以是类表达式，也可以是类声明。**

   **类声明：**

   ```javascript
   class Test {
     constructor() {
     }
   }
   ```

   **类表达式：**

   ```javascript
   const Test = class {
     constructor() {
     }
   }
   console.log(Test.name); //Test
   ```

   ```javascript
   const Test = class InnerNameTest {
     constructor() {
     }
   
     getClassName() {
       const {log} = console;
       log(InnerNameTest.name);
     }
   }
   const t = new Test();
   t.getClassName();
   ```

2. **函数声明和类声明之间的一个重要区别在于, 函数声明会提升，类声明不会。**

   ```javascript
   //不会报错
   const tongwl = new User("tongwl", 18);
   function User(name, age) {
     this.name = name;
     this.age = age;
   }
   ```

   ```javascript
   const tongwl = new User('tongwl', 18); //报ReferenceError：错
   class User {
     constructor(user, age) {
       this.user = user;
       this.age = age;
     }
   }
   ```

   类表达式则由let, const, var决定

   ```javascript
   console.log(User); //报ReferenceError错
   const User = class {
     constructor() {
     }
   }
   ```

   ```javascript
   console.log(User); //undefined
   var User = class {
     constructor() {
     }
   }
   ```

3. **类内部是执行在严格模式下的。比如，构造函数，静态方法，原型方法，getter和setter都在严格模式下执行。**

4. **static定义静态方法，属性。**

   ```javascript
   class Point {
       constructor(x, y) {
           this.x = x;
           this.y = y;
       }
       
       static displayName = "Point";
   
       static distance(a, b) {
           const dx = a.x - b.x;
           const dy = a.y - b.y;
           return Math.hypot(dx, dy);
       }
   }
   
   const p1 = new Point(5, 5);
   const p2 = new Point(10,10);
   p1.displayName;
   // undefined
   p1.distance;
   // undefined
   
   console.log(Point.displayName);
   // "Point"
   console.log(Point.distance(p1, p2));
   // 7.0710678118654755
   ```

5. **私有变量 `#`开头。**

   ```javascript
   class User {
     #user;
     #age;
     constructor(user, age) {
       this.#user = user;
       this.#age = age;
     }
   
     getUserInfo() {
       console.log(`${this.#user} : ${this.#age}`);
     }
   }
   
   const tong = new User('tongwl', 18);
   tong.getUserInfo();
   console.log(tong["#user"]); //undefined
   console.log(tong["#age"]); //undefined
   ```

6. **定义在类中的方法其实是属于原型对象(prototype)的方法，定义在类中的属性却是实例的属性。**

7. **class本质上是一个函数。**

   ```javascript
   class User {}
   console.log(typeof User); //"function"
   console.log(User === User.prototype.constructor); //true
   ```

8. **class不能当做普通函数执行，构造函数可以。**

   ```javascript
   class A {
   }
   A(); //报错
   ```

   ```javascript
   function A() {
   }
   A(); //正确
   ```

9. **`constructor()`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor()`方法，如果没有显式定义，一个空的`constructor()`方法会被默认添加。**

   ```javascript
   class Point {
   }
   
   // 等同于
   class Point {
     constructor() {}
   }
   ```

10. **取值函数（getter）和存值函数（setter）**

    ```javascript
    class P {
      constructor(username) {
        this["#username"] = username;
      }
    
      get username() {
        console.log('getting');
        return this["#username"];
      }
    }
    
    const p = new P('tongwl');
    console.log(p.username);
    
    //先后输出getting, tongwl
    ```

    ```javascript
    class Parent {
      private _username;
      set username(username) {
        this.sendToMetrics()
        this._username = username;
      }
    
      get username() {
        return this._username;
      }
    
      private sendToMetrics() {
        console.log(`send to metrics, username change: ${this._username}`);
      }
    }
    
    const p = new Parent();
    p.username = 'tongwl';
    ```

11. **属性表达式**

    ```javascript
    const methodName = 'getArea';
    const prop = 'username';
    
    class Square {
      constructor(length) {
        // ...
      }
      
      [prop] = 'tongwl';
    
      [methodName]() {
        // ...
      }
      
    }
    
    const s = new Square();
    s.getArea();
    console.log(s.username);
    ```

12. 

      


## 传统构造函数

```javascript
//定义构造函数
function Person(name, age) {
  this.name = name;
  this.age = age;
}

//设置原型对象的方法
Person.prototype.sayHello = function() {
  console.log(`${this.name} say hello!`);
}

//创建对象
const lily = new Person('lily', 18);
lily.sayHello();

//lily.__proto__ 指向原型对象，Person.prototype也指向原型对象
console.log(lily.__proto__ === Person.prototype); // true

//prototype对象的constructor指向类本身
console.log(Person.prototype.constructor === Person); // true
```



## Class语法

```javascript
class Point {
  // ...
}
typeof Point // "function"
Point === Point.prototype.constructor // true
```

使用的时候，也是直接对类使用`new`命令，跟构造函数的用法完全一致。

```javascript
class Bar {
  doStuff() {
    console.log('stuff');
  }
}

const b = new Bar();
b.doStuff() // "stuff"
```

构造函数的`prototype`属性，在 ES6 的“类”上面继续存在。**事实上，class里面定义的所有方法都定义在类的`prototype`对象上面。**

```javascript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

上面代码中，`constructor()`、`toString()`、`toValue()`这三个方法，其实都是定义在`Point.prototype`上面。

因此，在类的实例上面调用方法，其实就是调用原型上的方法。

```javascript
class B {}
const b = new B();

b.constructor === B.prototype.constructor // true
```

由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign()`方法可以很方便地一次向类添加多个方法。

```javascript
class Point {
  constructor(){
    // ...
  }
}

Object.assign(Point.prototype, {
  toString(){},
  toValue(){}
});
```

**用class定义的类和构造函数定义的类的区别**

**1. class定义的方法都是不可枚举的；构造函数定义的方法是可枚举的**

```javascript
class Point {
  constructor(x, y) {
    // ...
  }

  toString() {
    // ...
  }
}

Object.keys(Point.prototype)
// []
Object.getOwnPropertyNames(Point.prototype)
// ["constructor","toString"]
```

```javascript
    class Point {
      constructor(x, y) {
        // ...
      }

      toString() {
        // ...
      }
    }

    //@ts-ignore
    Point.prototype.sayHello = () => {
      console.log('hello');
    }

    console.log(Object.keys(Point.prototype));
    // ["sayHello"]
    console.log(Object.getOwnPropertyNames(Point.prototype));
    // ["constructor","toString", "sayHello"]
```

**2.class不能当做普通函数执行，构造函数可以**

```javascript
class A {
}

A(); //报错
```

```javascript
function A() {
}
A(); //正确	
```

### constructor 方法

`constructor()`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor()`方法，如果没有显式定义，一个空的`constructor()`方法会被默认添加。

```javascript
class Point {
}

// 等同于
class Point {
  constructor() {}
}
```

上面代码中，定义了一个空的类`Point`，JavaScript 引擎会自动为它添加一个空的`constructor()`方法。

**`constructor()`方法默认返回实例对象（即`this`）**，完全可以指定返回另外一个对象。

```javascript
class Foo {
  constructor() {
    return Object.create(null);
  }
}

new Foo() instanceof Foo
// false
```

上面代码中，`constructor()`函数返回一个全新的对象，结果导致实例对象不是`Foo`类的实例。



### 类的实例

生成类的实例的写法，与 ES5 完全一样，也是使用`new`命令。前面说过，如果忘记加上`new`，像函数那样调用`Class`，将会报错。

```javascript
class Point {
  // ...
}

// 报错
var point = Point(2, 3);

// 正确
var point = new Point(2, 3);
```

**定义在类中的方法其实是属于原型对象(prototype)的方法，定义在类中的属性却是实例的属性。**

**定义在类中的方法其实是属于原型对象(prototype)的方法，定义在类中的属性却是实例的属性。**

**定义在类中的方法其实是属于原型对象(prototype)的方法，定义在类中的属性却是实例的属性。**

```javascript
class Point {
  username;
  age;
  constructor(username, age) {
    this.username = username;
    this.age = age;
  }

  getInfo() {
    console.log(`username: ${this.username}, age: ${this.age}`);
  }
}

const p = new Point('tongwl', 18);

console.log(Point.hasOwnProperty('username')); //false
console.log(Point.hasOwnProperty('age')); //false
console.log(Point.hasOwnProperty('getInfo')); //false

console.log(Point.prototype.hasOwnProperty('username')); //false
console.log(Point.prototype.hasOwnProperty('age')); //false
console.log(Point.prototype.hasOwnProperty('getInfo')); //true

console.log(p.hasOwnProperty('username')); //true
console.log(p.hasOwnProperty('age')); //true
console.log(p.hasOwnProperty('getInfo')); //false
```



\_\_proto\_\_是各大厂商具体实现时添加的私有属性，我们可以使用`实例.__proto__`去获取原型对象，但是不推荐。

```typescript
class Point {
}

const p1 = new Point();
const p2 = new Point();
//@ts-ignore
console.log(p1.__proto__ === p2.__proto__); //true
```

生产环境中，我们可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

```typescript
class Point {
}

const p1 = new Point();
const p2 = new Point();
console.log(Object.getPrototypeOf(p1) === Object.getPrototypeOf(p2)); //true
```



### 取值函数（getter）和存值函数（setter）

```typescript
class Parent {
  private _username;
  set username(username) {
    this.sendToMetrics()
    this._username = username;
  }

  get username() {
    return this._username;
  }

  private sendToMetrics() {
    console.log(`send to metrics, username change: ${this._username}`);
  }
}

const p = new Parent();
p.username = 'tongwl';
```



### 属性表达式

类的属性名(包括方法名)，可以采用表达式。

```javascript
const methodName = 'getArea';
const prop = 'username';

class Square {
  constructor(length) {
    // ...
  }
  
  [prop] = 'tongwl';

  [methodName]() {
    // ...
  }
  
}

const s = new Square();
s.getArea();
console.log(s.username);
```

上面代码中，`Square`类的方法名`getArea`，是从表达式得到的。



### Class 表达式

与函数一样，类也可以使用表达式的形式定义。

```javascript
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`Me`，但是`Me`只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用`MyClass`引用。

```javascript
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```

上面代码表示，`Me`只在 Class 内部有定义。

如果类的内部没用到的话，可以省略`Me`，也就是可以写成下面的形式。

```javascript
const MyClass = class { /* ... */ };
```

采用 Class 表达式，可以写出立即执行的 Class。

```javascript
let person = new class {
  constructor(name) {
    this.name = name;
  }

  sayName() {
    console.log(this.name);
  }
}('张三');

person.sayName(); // "张三"
```

上面代码中，`person`是一个立即执行的类的实例。	



### 注意点

**（1）严格模式**

类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。

**（2）不存在提升**

类不存在变量提升（hoist），这一点与 ES5 完全不同。

```javascript
new Foo(); // ReferenceError
class Foo {}
```

上面代码中，`Foo`类使用在前，定义在后，这样会报错，因为 ES6 不会把类的声明提升到代码头部。这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。

```javascript
{
  let Foo = class {};
  class Bar extends Foo {
  }
}
```

上面的代码不会报错，因为`Bar`继承`Foo`的时候，`Foo`已经有定义了。但是，如果存在`class`的提升，上面代码就会报错，因为`class`会被提升到代码头部，而`let`命令是不提升的，所以导致`Bar`继承`Foo`的时候，`Foo`还没有定义。

**（3）name 属性**

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被`Class`继承，包括`name`属性。

```javascript
class Point {}
Point.name // "Point"
```

`name`属性总是返回紧跟在`class`关键字后面的类名。

~~**（4）Generator 方法**~~

如果某个方法之前加上星号（`*`），就表示该方法是一个 Generator 函数。

```javascript
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}

for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```

上面代码中，`Foo`类的`Symbol.iterator`方法前有一个星号，表示该方法是一个 Generator 函数。`Symbol.iterator`方法返回一个`Foo`类的默认遍历器，`for...of`循环会自动调用这个遍历器。

**5）this 的指向**

类的方法内部如果含有`this`，它默认指向类的实例。但是，必须非常小心，一旦单独使用该方法，很可能报错。

```javascript
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }

  print(text) {
    console.log(text);
  }
}

const logger = new Logger();
const { printName } = logger;
printName(); // TypeError: Cannot read property 'print' of undefined
```

上面代码中，`printName`方法中的`this`，默认指向`Logger`类的实例。但是，如果将这个方法提取出来单独使用，`this`会指向该方法运行时所在的环境（由于 class 内部是严格模式，所以 this 实际指向的是`undefined`），从而导致找不到`print`方法而报错。

一个比较简单的解决方法是，在构造方法中绑定`this`，这样就不会找不到`print`方法了。

```javascript
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }

  // ...
}
```

另一种解决方法是使用箭头函数。

```javascript
class Obj {
  constructor() {
    this.getThis = () => this;
  }
}

const myObj = new Obj();
myObj.getThis() === myObj // true
```

箭头函数内部的`this`总是指向定义时所在的对象。上面代码中，箭头函数位于构造函数内部，它的定义生效的时候，是在构造函数执行的时候。这时，箭头函数所在的运行环境，肯定是实例对象，所以`this`会总是指向实例对象。

~~还有一种解决方法是使用`Proxy`，获取方法的时候，自动绑定`this`。~~

```javascript
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}

const logger = selfish(new Logger());
```



### 静态方法

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

上面代码中，`Foo`类的`classMethod`方法前有`static`关键字，表明该方法是一个静态方法，可以直接在`Foo`类上调用（`Foo.classMethod()`），而不是在`Foo`类的实例上调用。如果在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例。

```javascript
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
```

上面代码中，静态方法`bar`调用了`this.baz`，这里的`this`指的是`Foo`类，而不是`Foo`的实例，等同于调用`Foo.baz`。另外，从这个例子还可以看出，**静态(属性)方法可以与非静态(属性)方法重名。**

**父类的静态方法，可以被子类继承。**

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

上面代码中，父类`Foo`有一个静态方法，子类`Bar`可以调用这个方法。

**静态方法也是可以从`super`对象上调用的。**

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
  static classMethod() {
    return super.classMethod() + ', too';
  }
}

Bar.classMethod() // "hello, too"
```





###实例属性的新写法

实例属性除了定义在`constructor()`方法里面的`this`上面，也可以定义在类的最顶层。

```javascript
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

上面代码中，实例属性`this._count`定义在`constructor()`方法里面。另一种写法是，这个属性也可以定义在类的最顶层，其他都不变。

```javascript
class IncreasingCounter {
  _count = 0;
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

上面代码中，实例属性`_count`与取值函数`value()`和`increment()`方法，处于同一个层级。这时，不需要在实例属性前面加上`this`。

这种新写法的好处是，所有实例对象自身的属性都定义在类的头部，看上去比较整齐，一眼就能看出这个类有哪些实例属性。

```javascript
class foo {
  bar = 'hello';
  baz = 'world';

  constructor() {
    // ...
  }
}
```

上面的代码，一眼就能看出，`foo`类有两个实例属性，一目了然。另外，写起来也比较简洁。



###静态属性

静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。

```javascript
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```

上面的写法为`Foo`类定义了一个静态属性`prop`。

目前，只有这种写法可行，**因为 ES6 明确规定，Class 内部只有静态方法，没有静态属性**。现在有一个[提案](https://github.com/tc39/proposal-class-fields)提供了类的静态属性，写法是在实例属性的前面，加上`static`关键字。

```javascript
class MyClass {
  static myStaticProp = 42;

  constructor() {
    console.log(MyClass.myStaticProp); // 42
  }
}
```

这个新写法大大方便了静态属性的表达。

```javascript
// 老写法
class Foo {
  // ...
}
Foo.prop = 1;

// 新写法
class Foo {
  static prop = 1;
}
```

上面代码中，老写法的静态属性定义在类的外部。整个类生成以后，再生成静态属性。这样让人很容易忽略这个静态属性，也不符合相关代码应该放在一起的代码组织原则。另外，新写法是显式声明（declarative），而不是赋值处理，语义更好。





## ~~私有方法和私有属性~~

参考学习：https://es6.ruanyifeng.com/#docs/class

