### 队列

特点：先进先出，排队

```javascript
/**
 * 队列基类，内部私有变量定义方法为_items，所以文件名中有Pub
 * @author nhh
 * @date 24/12/2020
 * @class BaseQueue
 */
class BaseQueue {
  constructor(initval) {
    initval = initval || []
    this._items = initval
  }

  isEmpty() {
    return this._items.length === 0
  }

  enqueue(item) {
    this._items.push(item)
  }

  dequeue() {
    return this._items.shift()
  }

  front() {
    return this._items[0]
  }

  size() {
    return this._items.length
  }
}

module.exports = BaseQueue
```

### 优先级队列

军人优先、学生优先等

```javascript
const BaseQueue = require('./32QueuePub');

const WrapPriorityQueue = function () {

  function QueueElement(item, priority) {
    this.item = item
    this.priority = priority
  }

  /**
   * 优先级队列 1>2(优先级)
   * @author nhh
   * @date 24/12/2020
   * @class PriorityQueue
   * @extends {BaseQueue}
   */
  class PriorityQueue extends BaseQueue {
    constructor(inival) {
      super(inival)
    }

    enqueue(item, priority) {
      let cQueueElement = new QueueElement(item, priority)
      let added = false

      for (let i = 0; i < this._items.length; i++) {
        const queueElement = this._items[i];
        if (cQueueElement.priority < queueElement.priority) {
          this._items.splice(i, 0, cQueueElement)
          added = true
          break
        }
      }

      if (!added) {
        this._items.push(cQueueElement)
      }
      // for (const queueElement of this._items) { } //不能用for of，因为需要记录下标
    }

    print() {
      while (!this.isEmpty()) {
        let cQueueElement = this.dequeue()
        console.log(('name:' + cQueueElement.item + ', priority:' + cQueueElement.priority))
      }
    }
  }

  return PriorityQueue
}()

module.exports = WrapPriorityQueue
```

### 循环队列

击鼓传花

```javascript
const BaseQueue = require("./32QueuePub");

class LoopQueue extends BaseQueue {
  constructor(initval) {
    super(initval)
  }

  *loop(num) {
    // 只要队列有元素，就转
    while (this.size() > 1) {
      // 转起来
      for (let i = 0; i < num; i++) {
        this.enqueue(this.dequeue())
      }
      yield 1 // 每次转完，让外部处理
    }
  }
}
// 使用 这是一个击鼓传花的案例
let namelist = ['liming', 'wanger', 'zhangsan', 'wangerma', 'yijiel', 'liuliwei']
let queue = new LoopQueue(namelist)

let gen = queue.loop(10)

for (const i of gen) {
  let eliminated = queue.dequeue()
  console.log(eliminated + ' is eliminated');
}

console.log(queue.front()); // 看一下剩下的元素
```

