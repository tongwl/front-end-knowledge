# instanceof操作符

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。

```javascript
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
const auto = new Car('Honda', 'Accord', 1998);

console.log(auto instanceof Car);
// expected output: true

console.log(auto instanceof Object);
// expected output: true
```

```javascript
class Father {}
class Child extends Father {}
const lily = new Child();
console.log(lily instanceof Child); //true
console.log(lily instanceof Father); //true
```

