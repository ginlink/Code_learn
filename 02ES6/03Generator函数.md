### Generator函数

Generator是一个生产器，而Generator函数就是用于制造生产器的函数

```javascript
function* gen(){
    let y = yield 1
    let z = 3 * yield y + 2
    ...
}
// 使用
let g = gen()
g.next() // 触发 { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next()
    
    
// 使用for of 来遍历，for of 会干事情，
for const i of gen()
```

### 对象可迭代

任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

由于Generator函数就是遍历器生成函数，因此可以把Generator赋值给对象的`Symbol.iterator`属性，从而使得该对象具有Iterator接口。

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
  yield 1;
  yield 2;
  yield 3;
};

[...myIterable] // [1, 2, 3]
```

### 同步Generator函数

```javascript
var arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  var length = a.length;
  for (var i = 0; i < length; i++) {
    var item = a[i];
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)) {
  console.log(f);
}
```

### 异步Generator函数

继发与并发？

先上结论 **只要await后面跟的是new Promise（无论是表达式，还是通过函数返回），那么就是继发，如果直接是一个promise对象，就是并发**

```javascript
function getFoo() {
  return new Promise((resole, reject) => {
    setTimeout(() => {
      // console.log('getFoo');
      resole('getFoo')
    }, 2001)
  })
}
function getBar() {
  return new Promise((resole, reject) => {
    setTimeout(() => {
      // console.log('getBar');
      resole('getBar')
    }, 2000)
  })
}

async function* getFooAsync(element) {
  console.log(element);
  yield await new Promise((resole) => {
    setTimeout(() => {
      resole('解决了')
    }, 2000)
  })
}

async function* gen() {

  // 并发？继发
  // for (let index = 0; index < 3; index++) {
  //   yield await getFoo()
  //   yield await getBar()
  // }

  // 并发？并发 好像用处不大
  // let FooPro = getFoo()
  // let BarPro = getBar()
  // for (let index = 0; index < 3; index++) {
  //   yield await FooPro
  //   yield await BarPro
  // }
  
  // 并发？无执行结果 因为如果在Generater函数内部，调用另一个Generator函数，默认情况下是没有效果的。这个就需要用到yield*语句，用来在一个Generator函数里面执行另一个Generator函数。还是无效 呜呜呜
  // let array = [1, 2, 3]
  // array.forEach(getFooAsync);
  // array.map(getFooAsync)
    
}

async function main() { // 注意这里是异步函数，因为内部使用了await

  for await (const i of gen()) {
    console.log(new Date().getTime()); // record time once
    console.log(i);

  }
}

console.log(new Date().getTime()); // start time
main() // start port
```

### yield*

```javascript
// 如果yield*后面跟着一个数组，由于数组原生支持遍历器，因此就会遍历数组成员。

function* gen(){
  yield* ["a", "b", "c"];
}

gen().next() // { value:"a", done:false }
// 上面代码中，yield命令后面如果不加星号，返回的是整个数组，加了星号就表示返回的是数组的遍历器对象。
```

`yield*`命令可以很方便地取出嵌套数组的所有成员。

```javascript
function* iterTree(tree) {
  if (Array.isArray(tree)) {
    for(let i=0; i < tree.length; i++) {
      yield* iterTree(tree[i]);
    }
  } else {
    yield tree;
  }
}

const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];

for(let x of iterTree(tree)) {
  console.log(x);
}
// a
// b
// c
// d
// e
```

## 作为对象属性的Generator函数

如果一个对象的属性是Generator函数，可以简写成下面的形式。

```javascript
let obj = {
  * myGeneratorMethod() {
    ···
  }
  // 等同于
  myGeneratorMethod: function* () {
    // ···
  }
};
```

### Gen作为对象

```javascript
function* gen() {
  this.a = 1;
  yield this.b = 2;
  yield this.c = 3;
}

function F() {
  return gen.call(gen.prototype);
}

var f = new F();

f.next();  // Object {value: 2, done: false}
f.next();  // Object {value: 3, done: false}
f.next();  // Object {value: undefined, done: true}

f.a // 1
f.b // 2
f.c // 3
```

### 状态机

调用一次更改一下状态

```javascript
var clock = function*() {
  while (true) {
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
};
```

## 应用

### （1）异步操作的同步化表达

Ajax是典型 的异步操作，通过Generator函数部署Ajax操作，可以用同步的方式表达。

```javascript
function* main() {
  var result = yield request("http://some.url");
  var resp = JSON.parse(result);
    console.log(resp.value);
}

function request(url) {
  makeAjaxCall(url, function(response){
    it.next(response);
  });
}

var it = main();
it.next();
```

下面是另一个例子，通过Generator函数逐行读取文本文件。

```javascript
function* numbers() {
  let file = new FileReader("numbers.txt");
  try {
    while(!file.eof) {
      yield parseInt(file.readLine(), 10);
    }
  } finally {
    file.close();
  }
}
```

### （2）控制流管理

如果有一个多步操作非常耗时，采用回调函数，可能会写成下面这样。

```javascript
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // Do something with value4
      });
    });
  });
});
```

上面代码已经把回调函数，改成了直线执行的形式，但是加入了大量Promise的语法。Generator函数可以进一步改善代码运行流程。

```javascript
function* longRunningTask(value1) {
  try {
    var value2 = yield step1(value1);
    var value3 = yield step2(value2);
    var value4 = yield step3(value3);
    var value5 = yield step4(value4);
    // Do something with value4
  } catch (e) {
    // Handle any error from step1 through step4
  }
}
```

然后，使用一个函数，按次序自动执行所有步骤。

```javascript
scheduler(longRunningTask(initialValue));

function scheduler(task) {
  var taskObj = task.next(task.value);
  // 如果Generator函数未结束，就继续调用
  if (!taskObj.done) {
    task.value = taskObj.value
    scheduler(task);
  }
}
```

下面，利用`for...of`循环会自动依次执行`yield`命令的特性，提供一种更一般的控制流管理的方法。

```javascript
let steps = [step1Func, step2Func, step3Func];

function *iterateSteps(steps){
  for (var i=0; i< steps.length; i++){
    var step = steps[i];
    yield step();
  }
}
```

### （3）部署Iterator接口

利用Generator函数，可以在任意对象上部署Iterator接口。

```javascript
function* iterEntries(obj) {
  let keys = Object.keys(obj);
  for (let i=0; i < keys.length; i++) {
    let key = keys[i];
    yield [key, obj[key]];
  }
}

let myObj = { foo: 3, bar: 7 };

for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}

// foo 3
// bar 7
```

### （4）作为数据结构

Generator可以看作是数据结构，更确切地说，可以看作是一个数组结构，因为Generator函数可以返回一系列的值，这意味着它可以对任意表达式，提供类似数组的接口。

```javascript
function *doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}
```

上面代码就是依次返回三个函数，但是由于使用了Generator函数，导致可以像处理数组那样，处理这三个返回的函数。

```javascript
for (task of doStuff()) {
  // task是一个函数，可以像回调函数那样使用它
}
```

实际上，如果用ES5表达，完全可以用数组模拟Generator的这种用法。

```javascript
function doStuff() {
  return [
    fs.readFile.bind(null, 'hello.txt'),
    fs.readFile.bind(null, 'world.txt'),
    fs.readFile.bind(null, 'and-such.txt')
  ];
}
```

上面的函数，可以用一模一样的for...of循环处理！两相一比较，就不难看出Generator使得数据或者操作，具备了类似数组的接口。



### 参考文献

[1] [http://caibaojian.com/es6/generator.html](http://caibaojian.com/es6/generator.html)

十分感谢此文献，收益颇丰