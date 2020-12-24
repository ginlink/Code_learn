### 链表

![linkedlist](F:\mData\nh_temp\01web\02Notes\03DataCtructure.md\img\linkedlist.png)

```javascript
const WrapLinkedList = function () {

  function Node(item) {
    this.item = item
    this.next = null
  }
  class LinkedList {
    constructor() {
      this.head = null
      // this.head = new Node('head')
      this.length = 0
    }

    /**
     * 追加元素
     * @author nhh
     * @date 24/12/2020
     * @param {*} element
     * @memberof LinkedList
     */
    append(element) {
      let node = new Node(element)

      if (this.length === 0) {
        this.head = node
        this.length++
        return
      }

      // let index = this.length // 不需要索引值
      let current = this.head // 缓存头部节点
      // 查找最后一个node
      while (current.next) {
        current = current.next
      }

      current.next = node
      this.length++
    }

    /**
     * 删除指定位置的元素
     * @author nhh
     * @date 24/12/2020
     * @param {*} position
     * @returns {*}  
     * @memberof LinkedList
     */
    removeAt(position) {
      if (this.length === 0) {
        console.log('the linkedList is empty');
        return
      }
      if (position < 0 || position >= this.length) {
        console.log('wram(removeAt)(overflow the length)');
        return null
      }

      let current, previous
      current = this.head
      if (position === 0) {
        this.head = current.next
        this.length--
        return current
      }

      let index = 0
      while (index < position) {
        previous = current
        current = current.next
        index++
      }

      previous.next = current.next
      this.length--
      return current
    }

    /**
     * 在任意位置插入元素
     * @author nhh
     * @date 24/12/2020
     * @param {*} position
     * @param {*} element
     * @memberof LinkedList
     */
    insert(position, element) {
      if (position < 0 || position > this.length) {
        console.log('wram(insert)(overflow the length)');
        return
      }

      let node = new Node(element)
      let current = this.head
      let previous = null

      if (position === 0) {
        node.next = this.head
        this.head = node
        this.length++
        return
      }

      let index = 0
      // while (index++ < position) {
      while (index < position) {
        index++ // 这里index++与写在表达式上面是等效的，先表达式再++
        previous = current
        current = current.next
        // index++
      }

      node.next = current
      previous.next = node
      this.length++
    }

    /**
     * 打印函数
     * @author nhh
     * @date 24/12/2020
     * @memberof LinkedList
     */
    print() {
      if (this.length < 1) {
        console.log('the linkedList is empty');
        return
      }

      let current = this.head
      while (current) {
        console.log(current.item);
        current = current.next
      }
    }

  }

  return LinkedList
}

module.exports = WrapLinkedList
```

### 双向链表

```javascript
const WrapDoubleLinkedList = function () {

  function Node(element) {
    this.item = element
    this.next = null
    this.prev = null
  }

  class DoubleLinkedList {
    constructor() {
      this.head = null
      this.tail = null
      this.length = 0
    }

    insert(position, element) {
      if (position < 0 || position > this.length) {
        console.log('wram(insert)(overflow the length)');
        return false
      }

      let node = new Node(element)
      let current = this.head
      let previous = null

      if (position === 0) {
        if (!this.head) {
          this.head = node
          this.tail = node
        } else {
          node.next = current
          this.head.prev = node
          this.head = node
        }
      } else if (position === this.length) {
        // 如果在最后一个位置后面添加元素，则需要特殊加工，因为tail指向需要修改
        node.prev = this.tail
        this.tail.next = node
        this.tail = node
      } else {

        let index = 0
        while (index++ < position) { // 注意是i++，先表达式再++
          previous = current
          current = current.next
        }

        node.next = current
        previous.next = node
        current.prev = node
        node.prev = previous
      }

      this.length++
      return true
    }

    append(element) {
      this.insert(this.length, element)
    }

    remove() {
      return this.removeAt(this.length - 1)
    }

    removeAt(position) {
      if (position < 0 || position >= this.length) {
        console.log('wram(insert)(overflow the length)');
        return false
      }

      let current = this.head
      let previous = null
      if (position === 0) {
        this.head = current.next

        // 如果只有一个元素，则更新tail为Null
        if (this.length === 1) {
          this.tail = null
        } else {
          this.head.prev = null
        }
      } else if (position === this.length - 1) {
        // 要改变tail，所以要单独处理
        current = this.tail
        this.tail = current.prev
        this.tail.next = null
      } else {

        let index = 0
        while (index++ < position) {
          previous = current
          current = current.next
        }
        previous.next = current.next
        current.next.prev = previous
      }

      this.length--

      return current.item
    }

    print(mode) {
      mode = mode || 0
      let len = this.length
      if (len === 0) {
        console.log('the linkedList is empty');
        return
      }

      let current = null
      if (mode === 0) {
        // 头遍历
        current = this.head
        while (current) {
          console.log(current.item);
          current = current.next
        }
      } else {
        // 尾遍历
        current = this.tail
        while (current) {
          console.log(current.item);
          current = current.prev
        }
      }
    }
  }
}
```

