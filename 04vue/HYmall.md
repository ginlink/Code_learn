### HYmall（电商）

最新接口地址：baseURL = http://152.136.185.210:7878/api/m5



|      | 进度 | Detial时间 |
| ---- | ---- | ---------- |
|      |      |            |
|      |      |            |
|      |      |            |
|      |      |            |



### 滚动框架

i-scroll=> [*better-scroll*](https://github.com/ustbhuangyi/better-scroll)

##### Install

```shell
npm install better-scroll -S # install 2.x，with full-featured plugin.

npm install @better-scroll/core # only CoreScroll
import BetterScroll from 'better-scroll'

let bs = new BetterScroll('.wrapper', {
  movable: true,
  zoom: true
})

import BScroll from '@better-scroll/core'

let bs = new BScroll('.wrapper', {})
```

##### 01 注意一定要开启点击事件

```js
this.scroll = new BScroll(wrapper, {
    click: true,
});
```

##### 02 又一个坑 上拉加载

\[BScroll warn]: EventEmitter has used unknown event type: "pullingDown"

报错原因：没有安装pullup这个插件
解决：

```bash
npm install @better-scroll/pull-up@next --save
```

```javascript
import BScroll from '@better-scroll/core'
import Pullup from '@better-scroll/pull-up'

BScroll.use(Pullup)  // 注册插件

new BScroll(document.querySelector(".wrap"), {
      probeType: 3,
      pullUpLoad: true  // 设置启用插件
});
```

https://blog.csdn.net/qfrex/article/details/107835723



### 图片懒加载

```js
// 导包Lazyload
// 懒加载注册
Vue.use(Lazyload, {
  preLoad: 1,
  loading: require('@/assets/img/common/placeholder.png')
});
```



### 一、CSS

### 01 如何让很多的文字(p标签)只显示一行，且后面显示...

用white-space + text-overflow + overflow(让边界的文字隐藏)

1 **white-space**

规定段落中的文本不进行换行：

```
p{
	white-space: nowrap
}
```

2 **css3:text-overflow** 

css3:text-overflow 属性规定当文本溢出包含元素时发生的事情。

| 值       | 描述                                 | 测试                                                         |
| :------- | :----------------------------------- | :----------------------------------------------------------- |
| clip     | 修剪文本。                           | [测试](https://www.w3school.com.cn/tiy/c.asp?f=css_text-overflow) |
| ellipsis | 显示省略符号来代表被修剪的文本。     | [测试](https://www.w3school.com.cn/tiy/c.asp?f=css_text-overflow&p=2) |
| *string* | 使用给定的字符串来代表被修剪的文本。 |                                                              |

### 02 如何让很多的图片分列显示？

用flex布局 + flex-wrap: wrap + 图片父元素宽度设置+ 图片宽度设置

解释：

用flex布局：让内部元素呈列显示

flex-wrap: wrap ：为了不让多余元素显示在一行，就包裹一下，显示在容器下面

图片父元素宽度设置：50%==2列，33.33%==3列，依次

图片宽度：100%为了让图片填充整个父窗口

### 03 如何让fixed布局的宽度占满整个屏幕的宽度？

```css
{
    position: fixed;
    left: 0;  /* 当然也可以用width: 100% */
    right: 0;
}
```

### 04 回忆Bscroll容器是如何在屏幕中间显示的？

注意自己没有用到height，这里的自己表示Bscroll

1. 定位：让bs的父元素设置relative，自己设置absolute，让后left:0; right:0;

2. 挤自己：top: 44px; bottom:49px; 把自己挤到中间去

3. 视口：让bs的父元素设置100个视口，height: 100vh; 如果不设置值，则盒子塌陷，如果设置px值，则无效(待确认)

### 05 如何画一个三角形？

考察border

transparent表示透明，三边透明一遍亮

```css
{
    width: 0px;  /* 宽度一定为0 */
    boder-width: 200px;
    boder-style: solid;
    boder-color: transparent #00F transparent transparent
        
}
```



   


### 二、Vue

### 00 HYmall开发流程是什么?

tabbar-> 

​	Home(navbar-> bscroll-> swiper-> recommend-> feature-> tabControl-> goods-> backUp)

### 导入vue组件的方法

```js
const App = ()=> import('@/app/App')  // 路由组件懒加载

import App from '@/app/App'  // 直接加载
```



### 00 axios如何封装？

```js
// 导包 axios
const originAxios = import('axios')

// 根据不同业务需求(服务器地址变更)，可以配置、导出多个axios
const axios = (options)=>{
    
    return new Promise((resolve, reject)=>{
        // 创建公共部分
        const instance = originAxios.create({
            baseURL: '',
            timeOut: 3000,
        })
        
        // 拦截，中间件
        instance.interceptors.request.use((config)=>{
            // 进行一些操作
            return config
        })
        instance.interceptors.response.use((res)=>{
            // 进行一些操作
            return res
        })
        
        instance(options).then((res)=>{
            resolve(res)
        }).catch((err)=>{
            reject(err)
        })
        
    })
}
const axios1 = ()=>{}  // 类似于axios配置

export axios
export axios1
```



### 00 商品列表数据结构如何理解？

```js
goods:{
    'pop': {page: 1, list: []},
    'new': {page: 1, list: []},
    'sell': {page: 1, list: []},
}
```

为什么不直接用一个goods变量接收，或者用多个对象pop、new、sell接收呢？

这样设计有三个好处：

	1. 可以拿到page，每个good都有自己的page，方便请求下一页数据(+1即可)
	2. 数据统一，可以通过type来区分不同数据，例如type=='pop'及代表pop数据
	3. 可以用数字来对应pop、new、sell，正好切换title的时候有一个currentIndex

### 01 $bus是个什么？

事件总线

作用：相当于vuex，只不过她是传递事件，处理事件的。

用法：

```js
(发送) this.$bus.$emit('aaa'，index)
(接收) this.$bus.$on('aaa', (index)=>{})
(注册) Vue.prototype.$bus = new Vue()
```

### 02 Bscroll 计算高度bug如何解决？

思想：由于bs里面有大量图片(且为异步数据)，则会出现计算可滚动高度错误。解决方法就是给每个图片添加**load事件**，发给Home(通过$bus，也叫事件总线)，让Home去调用Scroll的refresh()方法，重新计算高度。

这里就涉及到一个**防抖函数**

```js
function debounce(callback, delay){
    let timer = null
    
    return (...argv)=>{  // 必须为箭头函数，否则内部this指向错误 
        				// argv是为了给回调函数传递参数
        if(timer) clearTimeOut(timer)
        timer = setTimeOut(()=>{
            callback.apply(this, argv)
        }, delay)
    }
}
// 怎么用呢？
// 1 在要防抖的外部把防抖函数拿到
const refresh = debunce(callback, 300)

// 2 再将refresh放入抖动、反复执行的方法中
// 比如监听一个刷新事件，这个事件可能会反复、频繁的调用，防抖主要靠内部的timer变量
this.$bus.$on('refresh', ()=>{
    refresh()
})
```

### 099 注意props默认参数问题

如果props默认参数类型为{} or []

则要这样：

```js
{
    type: Object,
    default: ()=>{return {}}  // 默认值为一个函数，否则浅拷贝
}	
```

### 099 Bscroll参数汇总

```js
{
    cick: true,  // 是否允许响应点击事件
    probeType: 3,   // 记录滚动数字方式，3-滚动，且滚性数字
    pullUpLoad: true,  // 允许上拉加载事件
}
```

### 三、JS

### 00 箭头函数this是谁？

箭头中this就是上下文环境的this，且无法通过bind三兄弟修改

普通中this就是调用者

### 01 bind, call, apply

这三个是什么 ？ 我称bind三兄弟

她们可以改变调用函数的内部this指向

```js
const obj=()=>{
    console.log(this)
}
const arr = new Array()
arr.bind(obj)  // 不能传参
arr.call(obj, aaa)  // 只能传一个
arr.apply(obj, aaa, bbb, ccc, [...])  // 可以传无数个
```





