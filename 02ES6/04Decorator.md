### Decorator（装饰器）

修饰器（Decorator）是一个函数，用来修改类的行为。这是ES7的一个[提案](https://github.com/wycats/javascript-decorators)，目前Babel转码器已经支持。

我也把它叫加工器，对类的进一步加工。

修饰器对类的行为的改变，是**代码编译时发生的**，而不是在运行时。这意味着，修饰器能在编译阶段运行代码。

### 给类添加属性

**添加静态属性**

```javascript
function testable(target) {
  target.isTestable = true;
}

@testable
class MyTestableClass {}

console.log(MyTestableClass.isTestable) // true
```

利用闭包，还可以传参

```javascript
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}

@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```

**添加实例属性**

```javascript
function testable(target) {
  target.prototype.isTestable = true; // 向prototype上添加即可
}
```

### 给类添加方法

```javascript
function mixins(...list) { // 参数解构
  return function (target) {
    Object.assign(target.prototype, ...list) // ***重点
  }
}
const Foo = {
  foo() { console.log('foo') }
};

@mixins(Foo)
class MyClass {}

let obj = new MyClass();
obj.foo() // 'foo'
```

### 方法的修饰

```javascript
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}

/**
 * @param {*} target 所要修饰的目标对象
 * @param {*} name 所要修饰的属性名
 * @param {*} descriptor 该属性的描述对象
 */
function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction,
  //   enumerable: false,
  //   configurable: true,
  //   writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}
```

## （了解）为什么修饰器不能用于函数？

修饰器只能用于类和类的方法，不能用于函数，因为存在函数提升。

```javascript
var readOnly = require("some-decorator");

@readOnly
function foo() {
}

/// 由于函数提升实际执行顺序为
var readOnly;

@readOnly
function foo() {
}

readOnly = require("some-decorator");
```

## core-decorators.js

[core-decorators.js](https://github.com/jayphelps/core-decorators.js)是一个第三方模块，提供了几个常见的修饰器，通过它可以更好地理解修饰器。

**（1）@autobind**

`autobind`修饰器使得方法中的`this`对象，绑定原始对象。

**（2）@readonly**

`readonly`修饰器使得属性或方法不可写。

**（3）@override**

`override`修饰器检查子类的方法，是否正确覆盖了父类的同名方法，如果不正确会报错。

**（4）@deprecate (别名@deprecated)**

`deprecate`或`deprecated`修饰器在控制台显示一条警告，表示该方法将废除。

**（5）@suppressWarnings**

`suppressWarnings`修饰器抑制`decorated`修饰器导致的`console.warn()`调用。但是，异步代码发出的调用除外。

**descriptor是什么？**

## 使用修饰器实现自动发布事件 （没看懂）

我们可以使用修饰器，使得对象的方法被调用时，自动发出一个事件。

```javascript
import postal from "postal/lib/postal.lodash";

export default function publish(topic, channel) {
  return function(target, name, descriptor) {
    const fn = descriptor.value;

    descriptor.value = function() {
      let value = fn.apply(this, arguments);
      postal.channel(channel || target.channel || "/").publish(topic, value);
    };
  };
}
```

上面代码定义了一个名为`publish`的修饰器，它通过改写`descriptor.value`，使得原方法被调用时，会自动发出一个事件。它使用的事件“发布/订阅”库是[Postal.js](https://github.com/postaljs/postal.js)。

它的用法如下。

```javascript
import publish from "path/to/decorators/publish";

class FooComponent {
  @publish("foo.some.message", "component")
  someMethod() {
    return {
      my: "data"
    };
  }
  @publish("foo.some.other")
  anotherMethod() {
    // ...
  }
}
```

以后，只要调用`someMethod`或者`anotherMethod`，就会自动发出一个事件。

```javascript
let foo = new FooComponent();

foo.someMethod() // 在"component"频道发布"foo.some.message"事件，附带的数据是{ my: "data" }
foo.anotherMethod() // 在"/"频道发布"foo.some.other"事件，不附带数据
```

## Mixin

在修饰器的基础上，可以实现`Mixin`模式。所谓`Mixin`模式，就是对象继承的一种替代方案，中文译为“混入”（mix in），意为在一个对象之中混入另外一个对象的方法。

简单的混入

```javascript
const Foo = {
  foo() { console.log('foo') }
};

class MyClass {}

Object.assign(MyClass.prototype, Foo);

let obj = new MyClass();
obj.foo() // 'foo'
```

## Trait

Trait也是一种修饰器，效果与Mixin类似，但是提供更多功能，比如防止同名方法的冲突、排除混入某些方法、为混入的方法起别名等等。

下面采用[traits-decorator](https://github.com/CocktailJS/traits-decorator)这个第三方模块作为例子。这个模块提供的traits修饰器，不仅可以接受对象，还可以接受ES6类作为参数。

```javascript
import { traits } from 'traits-decorator';

class TFoo {
  foo() { console.log('foo') }
}

const TBar = {
  bar() { console.log('bar') }
  foo() { console.log('foo in TBar') }
};

@traits(TFoo, TBar::excludes('foo', 'bar')::alias({foo: 'aliasFoo'})) // excludes排除这个方法，alias给这个方法起别名
class MyClass { }

let obj = new MyClass();
obj.foo() // foo
obj.bar() // bar
```

## Babel转码器的支持

目前，Babel转码器已经支持Decorator。

首先，安装`babel-core`和`babel-plugin-transform-decorators`。由于后者包括在`babel-preset-stage-0`之中，所以改为安装`babel-preset-stage-0`亦可。

```bash
$ npm install babel-core babel-plugin-transform-decorators
```

然后，设置配置文件`.babelrc`。

```javascript
{
  "plugins": ["transform-decorators"]
}
```

这时，Babel就可以对Decorator转码了。

脚本中打开的命令如下。

```javascript
babel.transform("code", {plugins: ["transform-decorators"]})
```

Babel的官方网站提供一个[在线转码器](https://babeljs.io/repl/)，只要勾选Experimental，就能支持Decorator的在线转码。

### 参考文献

[1] [http://caibaojian.com/es6/decorator.html](http://caibaojian.com/es6/decorator.html)