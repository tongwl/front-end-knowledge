# ES6_9_class_extends

ES6中class继承的关键字是`extends`.

1.**子类必须在constructor方法中调用super方法**，否则新建实例的时候报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。**如果不调用`super`方法，子类就得不到`this`对象。**

```javascript
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor() {
  }
}

let cp = new ColorPoint(); // ReferenceError
```

如果子类没有定义constructor方法，这个方法会被默认添加。

例子：

```javascript
class ColorPoint extends Point {
}

// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}
```

```javascript
class Father {
  constructor(user, age) {
    this.user = user;
    this.age = age;
  }

  getUserInfo() {
    return `${this.user}:${this.age}`;
  }
}
class Child extends Father {
  constructor() {
    super(); //传入空
  }
}
const c = new Child("tongwl", 18);
console.log(c.getUserInfo()); //undefined:undefined
```

```javascript
class Father {
  constructor(user, age) {
    this.user = user;
    this.age = age;
  }

  getUserInfo() {
    return `${this.user}:${this.age}`;
  }
}
class Child extends Father {
  constructor(user, age) {
    super(user, age);
  }
}
const c = new Child("tongwl", 18);
console.log(c.getUserInfo()); //tongwl:18
```

```javascript
const Father = class {
  constructor(prop1, prop2) {
    this.prop1 = prop1;
    this.prop2 = prop2;
  }

  toString() {
    return `${this.prop1} : ${this.prop2}`;
  }
}

class Child extends Father {}

const c = new Child('tong', 'wl');
console.log(c.toString()); //tong : wl
```



2.ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

ES6中，子类实例的构建，基于父类实例，所以：**在子类的构造函数中，只有调用`super`函数之后，才可以使用`this`关键字，否则会报错。**

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

class ColorPoint extends Point {
  constructor(x, y, color) {
    this.color = color; // ReferenceError
    super(x, y);
    this.color = color; // 正确
  }
}
```



3.**父类的静态方法，也会被子类继承。**

```javascript
class A {
  static hello() {
    console.log('hello world');
  }
}

class B extends A {
}

B.hello()  // hello world
```



4.Object.getPrototypeOf()

`Object.getPrototypeOf`方法可以用来从子类上获取父类。

```javascript
class Point {}
class ChildPoint extends Point {}
console.log(Object.getPrototypeOf(ChildPoint) === Point); //true
```



5.`super`这个关键字既可以当做函数使用，也可以当作对象使用。在两种情况下，它的用法完全不同。

* super作为函数调用

  * super作为函数调用，**ES6要求：子类的constructor必须执行super函数一次。**

  * 注意，`super`虽然代表了父类`A`的构造函数，但是返回的是子类`B`的实例，即`super`内部的`this`指的是`B`的实例，**因此`super()`在这里相当于`A.prototype.constructor.call(this)`。**

    new.target指向正在执行的函数，可以用new.target验证。

    ```javascript
    const Father = class {
      constructor() {
        console.log(new.target.name);
      }
    }
    class Child extends Father {
        constructor() {
          super();
        }
    }
    new Child(); //Child
    new Father(); //Father
    ```

    

  * **作为函数时，`super()`只能用在子类的构造函数之中，用在其他地方就会报错。**

    ```javascript
    class A {}
    
    class B extends A {
      m() {
        super(); // 报错
      }
    }
    ```

* **super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。**

  ```javascript
  class Father {
    sayHello() {
      console.log("hello");
    }
  
  
    static sayHello() {
      console.log("static hello");
    }
  }
  class Child extends Father {
    constructor() {
      super();
      super.sayHello();
    }
  
    sayHello() {
      super.sayHello();
    }
  }
  const c = new Child(); //hello
  Child.sayHello(); //static hello
  ```

* super指向父类的原型对象，所以定义在父类的属性(或方法)是无法通过super的调用的。

  ```javascript
  class Father {
    constructor() {
      this.user = "tongwl"; //绑定在Father的实例对象上
    }
  
    getUser() { //绑定在Father.prototype上
      return this.user;
    }
  }
  
  class Child extends Father {
    get m() {
      return super.user;
    }
  }
  
  const c = new Child();
  console.log(c.m); //undefined
  console.log(c.getUser()); //"tongwl"
  ```

  如果属性定义在父类的原型对象上，就可以取到。

  ```javascript
  class Father {}
  Father.prototype.user = "tongwl";
  class Child extends Father {
    constructor() {
      super();
    }
    get me() {
      return super.user;
    }
  }
  const c = new Child();
  console.log(c.me); //"tongwl"
  ```

  

* **ES6 规定，在子类普通方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类实例。**

  ```javascript
  class Father {
    constructor() {
      this.user = "tong";
    }
  
    getUser() {
      console.log(this.user);
    }
  }
  
  class Child extends Father {
    constructor() {
      super();
      this.user = "lily";
    }
  
    getMe() {
      super.getUser();
    }
  }
  
  const c = new Child();
  c.getMe(); //"lily"
  ```

  上面`super.getUser()`实际上执行的是`super.getUser.call(this)`。

  由于`this`指向子类实例，所以如果通过`super`对某个属性赋值，这时`super`就是`this`，赋值的属性会变成子类实例的属性。

  ```javascript
  class Father {
    constructor() {
      this.user = "tong";
    }
  }
  class Child extends Father {
    constructor() {
      super();
      this.user = "lily";
      super.user = "wei"; //相当于this.user = "wei";
      console.log(super.user); //undefined
      console.log(this.user); //"wei"
    }
  }
  const c = new Child();
  ```

  super作为对象，用在静态方法中，super指向父类，而不是父类的原型对象。

  ```javascript
  class Parent {
    static myMethod(msg) {
      console.log('static', msg);
    }
  
    myMethod(msg) {
      console.log('instance', msg);
    }
  }
  
  class Child extends Parent {
    static myMethod(msg) {
      super.myMethod(msg);
    }
  
    myMethod(msg) {
      super.myMethod(msg);
    }
  }
  
  Child.myMethod(1); // static 1
  
  var child = new Child();
  child.myMethod(2); // instance 2
  ```

  另外，在子类的静态方法中通过`super`调用父类的方法时，方法内部的`this`指向当前的子类，而不是子类的实例。

  ```javascript
  class A {
    constructor() {
      this.x = 1;
    }
    static print() {
      console.log(this.x);
    }
  }
  
  class B extends A {
    constructor() {
      super();
      this.x = 2;
    }
    static m() {
      super.print();
    }
  }
  
  B.x = 3;
  B.m() // 3
  ```

  注意，使用`super`的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错。

  ```javascript
  class A {}
  
  class B extends A {
    constructor() {
      super();
      console.log(super); // 报错
    }
  }
  ```

  上面代码中，`console.log(super)`当中的`super`，无法看出是作为函数使用，还是作为对象使用，所以 JavaScript 引擎解析代码的时候就会报错。这时，如果能清晰地表明`super`的数据类型，就不会报错。

* 最后，由于对象总是继承其他对象的，**所以可以在任意一个对象中，使用`super`关键字。**

  ```javascript
  var obj = {
    toString() {
      return "MyObject: " + super.toString();
    }
  };
  
  obj.toString(); // MyObject: [object Object]
  ```

  
