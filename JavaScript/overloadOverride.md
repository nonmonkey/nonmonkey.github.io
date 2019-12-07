## 重载(Overload)
> 允许在同一范围中声明几个功能类似的**同名**函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同，也就是说用同一个函数完成不同的功能。这就是重载函数。重载函数常用来实现功能类似而所处理的数据类型不同的问题。不能只有函数返回值类型不同。

但是js没有这种因为参数不同而功能不同的函数，js没有真正意义上的重载，但是可以根据参数的个数不同来模拟：

```javascript
function overloadFunc() {
  switch(arguments.length){
    case 0:
      func1(arguments.length)
      break;
    case 1:
      func2(arguments.length)
      break;
    default:
      func(arguments.length)
      break;
  }
}

function func1(n) {
  console.log(`func1方法有1个参数。`)
}
function func2(n) {
  console.log(`func2函数有2个参数。`)
}
function func(n) {
  console.log(`func函数有${n + 1}个参数。`)
}
```

## 重写(Override)
> 子类可继承父类中的方法，而不需要重新编写相同的方法。但有时子类并不想原封不动地继承父类的方法，而是想作一定的修改，这就需要采用方法的重写。方法重写又称方法覆盖。

**1.es5实现方法重写:**
```javascript
function Animal(name, age) {
  this.name = name || 'animal';
  this.age = age || 0;
}
Animal.prototype.changeName = function(name) {
  this.name = name || '';
  return this;
}

function Cat() {
  Animal.apply(this, arguments);
}
// Cat继承Animal
Cat.prototype = Object.create(Animal.prototype);
Cat.prototype.constructor = Cat;
// 重写changeName方法
Cat.prototype.changeName = function() {
  this.name = 'This is an all-around cat!!!';
  return this;
};

var cat = new Cat('Tom', 79);
console.log('cat:', cat);
```

**2.es6 class实现方法重写:**
```javascript
class Animal {
  constructor(name = '', age = 0) {
    this.name = name;
    this.age = age;
  }

  changeName(name = '') {
    this.name = name;
    return this;
  }
}

class Cat extends Animal {
  constructor(...arg) {
    super(...arg)
  }

  changeName() { // 重写changeName方法
    this.name = 'This is an all-around cat!!!';
    return this;
  }
}

const cat = new Cat('Tom', 79);
console.log('cat:', cat);
```
