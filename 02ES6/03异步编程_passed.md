*******
**此内容整理至03Generator函数.md**

********

## Generator函数 生产器

### 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（coroutine），意思是多个线程互相协作，完成异步任务。

协程有点像函数，又有点像线程。它的运行流程大致如下。

- 第一步，协程A开始执行。
- 第二步，协程A执行到一半，进入暂停，执行权转移到协程B。
- 第三步，（一段时间后）协程B交还执行权。
- 第四步，协程A恢复执行。

上面流程的协程A，就是异步任务，因为它分成两段（或多段）执行。

举例来说，读取文件的协程写法如下。

```javascript
function *asyncJob() {
  // ...其他代码
  var f = yield readFile(fileA);
  // ...其他代码
}
```

上面代码的函数`asyncJob`是一个协程，它的奥妙就在其中的`yield`命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield`命令是异步两个阶段的分界线。

**协程遇到`yield`命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。**它的最大优点，就是代码的写法非常像同步操作，如果去除yield命令，简直一模一样。



### 如何判断继发还是并发？

先上结论 **只要await后面跟的是new Promise（无论是表达式，还是通过函数返回），那么就是继发，如果直接是一个promise对象，就是并发**

当然并发推荐用Promise.all([pro1, pro2])来进行

```javascript
async function gen() {

  console.time('test')
  console.log('——————————————');
  // yield 1
  // yield 2
  let step1 = new Promise((resolve) => {
    setTimeout(() => {
      resolve('解决了111')
      // return '111'
    }, 2000)
  })
  let step2 = new Promise((resolve) => {
    setTimeout(() => {
      resolve('解决了222')
      // return '111'
    }, 2000)
  })

  // 下面继发? 并发
  // let result = await step1
  // let result2 = await step2
  // let [result, result2] = await Promise.all([step1, step2])

  // 下面继发? 并发
  // let result = await function(){
  //   return step1
  // }()
  // let result2 = await function(){
  //   return step2
  // }()

  // 下面继发? 继发
  // let result = await new Promise((resolve) => {
  //   setTimeout(() => {
  //     resolve('解决了111')
  //     // return '111'
  //   }, 2000)
  // })
  // console.log(result);
  // let result2 = await new Promise((resolve) => {
  //   setTimeout(() => {
  //     resolve('解决了222')
  //     // return '111'
  //   }, 2000)
  // })


  console.log(result)
  console.log(result2)
  console.log('end')

  console.timeEnd('test')
}
```



### 参考

```javascript
[1]异步编程 http://caibaojian.com/es6/async.html
```

