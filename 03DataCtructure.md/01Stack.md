### Stack 栈

特点：后进先出

直接上例子

```javascript
const NStack = function () {
  const items = new WeakMap() // 用于实现私有变量，但是子类也无法访问

  class NStack {
    constructor(initval) {
      // this[items] = []
      items.set(this, initval)
      // NStack.items.set(this, initval)
    }

    pop() {
      if (items.get(this) < 1) throw Error('no more items')
      let popval = items.get(this).pop()
      return popval
    }
    push(item) {
      items.get(this).push(item)
      // NStack.items.get(this).push(item)
    }
    isEmpty() {
      return items.get(this).length === 0
    }

    // 尽量不这么写，内部数据
    // getItems(){
    //   return this[items]
    // }
  }

  // NStack.items = items

  // return new NStack()
  return NStack
}()

// 导出
module.exports = NStack
```

应用：进制转换器

```javascript
/**
 * 进制转换器，支持从十进制转到其他进制
 * @author nhh
 * @date 24/12/2020
 * @param {Number} decNum 原始值
 * @param {Number} base 进制数 默认-2
 */
function divideFromTen(decNum, base) {
  if (decNum == undefined) throw Error('decNum is must')
  base = base || 2

  const nStack = new NStack()
  let binaryString = ''
  let rem = 0

  while (decNum) {
    rem = Math.floor(decNum % base)
    nStack.push(rem)
    decNum = Math.floor(decNum / base)
  }

  while (!nStack.isEmpty()) {
    binaryString += nStack.pop()
  }

  return binaryString
}
```