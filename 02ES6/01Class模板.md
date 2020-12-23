### Class模板

class本身声明的类的类型为function，之所以为什么称为模板

其本质跟ES5的类的实现方式是一致的，原型上添加方法

```javascript
class Animal{
    constuctor(name){
        this.name = name
    }
    toString(){
        ...
    }
}
```

#### 关于属性

属性分为原型属性和实例属性

this.name =>实例属性

Animal.protoname =>原型属性

```javascript
Animal.hasOwnProperty('name') // true
Animal.hasOwnProperty('protoname') // false

Animal.__proto__.hasOwnProperty('protoname') // true
```



#### 表达式函数名称

``` javascript
let name = 'getBack'
[name](){
	...
}
```

#### 不存在变量提升

 Class不存在变量提升（hoist），这一点与ES5完全不同。

```javascript
new Foo(); // ReferenceError
class Foo {}
```

### Class表达式

与函数一样，类也可以使用表达式的形式定义。

```javascript
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`MyClass`而不是`Me`，`Me`只在Class的内部代码可用，指代当前类。

```javascript
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
```

上面代码表示，`Me`只在Class内部有定义。

如果类的内部没用到的话，可以省略`Me`，也就是可以写成下面的形式。

```javascript
const MyClass = class { /* ... */ };
```

采用Class表达式，可以写出立即执行的Class。

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

### 严格模式

类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。

考虑到未来所有的代码，其实都是运行在模块之中，所以ES6实际上把整个语言升级到了严格模式。

### 继承

```javascript
class Dog extents Animal{
    constructor(name, color){
       	super(name)
        this.color = color
    }
    
    toString(){
        return this.color + super.toString()
    }
}
```

必须调用super()，否则爹都没有，哪来的儿子

不调用super() 则拿不到this对象

如果子类没有定义`constructor`方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有`constructor`方法。

```javascript
constructor(...args) {
  super(...args);
}
```

#### 类的prototype属性和__proto__属性

几句话：

* **proto是指向 构造该对象的原型** 

  ```javascript
  class Person{}
  let person = new Person()
  // 针对于实例person：它的proto为Person的prototype
  // 针对于类Person：它的proto为Object的prototype
  ```

* prototype是一个属性，且只有Function才有。这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。原型对象也有一个属性，叫做constructor，这个属性包含了一个指针，指回原构造函数。

额外阅读  [[彻底搞懂js里的__proto__和prototype到底有什么区别？](https://segmentfault.com/a/1190000016024573)]

#### Object.getPrototypeOf() 判断一个类是否继承了另一个类

`Object.getPrototypeOf`方法可以用来从子类上获取父类。

```javascript
Object.getPrototypeOf(ColorPoint) === Point
// true
```

因此，可以使用这个方法判断，一个类是否继承了另一个类。

#### super使用

* 作为函数时，`super()`只能用在子类的构造函数之中，用在其他地方就会报错。

* 作为对象时，super相当于A.prototype，可以访问A原型上的方法和属性

注意，`super`虽然代表了父类`A`的构造函数，但是返回的是子类`B`的实例，即`super`内部的`this`指的是`B`，因此`super()`在这里相当于`A.prototype.constructor.call(this)`。

A.prototype.constructor为父类

```javascript
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

上面代码中，`new.target`指向当前正在执行的函数。可以看到，在`super()`执行时，它指向的是子类`B`的构造函数，而不是父类`A`的构造函数。也就是说，`super()`内部的`this`指向的是`B`。

#### 实例的__proto__属性 修改父类原型的方法和属性

子类实例的__proto__属性的__proto__属性，指向父类实例的__proto__属性。也就是说，子类的原型的原型，是父类的原型。

```javascript
var p1 = new Point(2, 3);
var p2 = new ColorPoint(2, 3, 'red');

p2.__proto__ === p1.__proto__ // false
p2.__proto__.__proto__ === p1.__proto__ // true
```

上面代码中，`ColorPoint`继承了`Point`，导致前者原型的原型是后者的原型。

因此，通过子类实例的`__proto__.__proto__`属性，可以修改父类实例的行为。

```javascript
p2.__proto__.__proto__.printName = function () {
  console.log('Ha');
};

p1.printName() // "Ha"
```

上面代码在`ColorPoint`的实例`p2`上向`Point`类添加方法，结果影响到了`Point`的实例`p1`。

#### extends继承原生的构造函数

ES5无法通过.call方式来继承原生构造函数，如下：

- Boolean()

- Number()

- String()

- Array()

- Date()

- Function()

- RegExp()

- Error()

- Object()

但ES6 extends可以，因为 **ES6是先新建父类的实例对象`this`，然后再用子类的构造函数修饰`this`，使得父类的所有行为都可以继承。**

`extends`关键字不仅可以用来继承类，还可以用来继承原生的构造函数。因此可以在原生数据结构的基础上，定义自己的数据结构。下面就是定义了一个带版本功能的数组。

#### Class的取值函数（getter）和存值函数（setter） 装饰器descriptor

与ES5一样，在Class内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

上面代码中，`prop`属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

#### Class的静态方法

静态方法和属性直接挂载在类上的

Dog{print(){}} 静态方法

Dog{prototype:{print(){}}} 实例方法

#### Class的静态属性和实例属性

静态属性指的是Class本身的属性，即`Class.propname`，而不是定义在实例对象（`this`）上的属性。

```javascript
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```

上面的写法为`Foo`类定义了一个静态属性`prop`。

目前，只有这种写法可行，因为ES6明确规定，Class内部只有静态方法，没有静态属性。

ES7有了新的提案，将来有一天能够实现内部静态方法

## new.target属性 定义“抽象类”

`new`是从构造函数生成实例的命令。ES6为`new`命令引入了一个`new.target`属性，（在构造函数中）返回`new`命令作用于的那个构造函数。如果构造函数不是通过`new`命令调用的，`new.target`会返回`undefined`，因此这个属性可以用来确定构造函数是怎么调用的。

需要注意的是，子类继承父类时，`new.target`会返回子类。

利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。

```javascript
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}

var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

## Mixin模式的实现 合并类

Mixin模式指的是，将多个类的接口“混入”（mix in）另一个类。它在ES6的实现如下。

```javascript
function mix(...mixins) {
  class Mix {}

  for (let mixin of mixins) {
    copyProperties(Mix, mixin);
    copyProperties(Mix.prototype, mixin.prototype);
  }

  return Mix;
}

function copyProperties(target, source) {
  for (let key of Reflect.ownKeys(source)) {
    if ( key !== "constructor"
      && key !== "prototype"
      && key !== "name"
    ) {
      let desc = Object.getOwnPropertyDescriptor(source, key);
      Object.defineProperty(target, key, desc);
    }
  }
}
```

上面代码的`mix`函数，可以将多个对象合成为一个类。使用的时候，只要继承这个类即可。

```javascript
class DistributedEdit extends mix(Loggable, Serializable) {
  // ...
}
```



### 参考文献

```
[1] http://caibaojian.com/es6/class.html  ES6
```

