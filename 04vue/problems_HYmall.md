### 练习开发HYmall遇到的问题

最新接口地址：baseURL = "http://152.136.185.210:7878/api/m5"
看清楚哦，不要漏掉了/api/m5，也是接口的一部分
看一下我下面两个接口的使用方法，建议先在浏览器测试，学会接口的使用，再去修改自己的代码。
还请大家遵守规则，不要破坏规则，否则不再提供接口，并且这样才会给老师更多的动力创造更多优质的课程给大家。



### vscode代码提示问题

描述：

利用vueCLI建立项目，在vue.config.js配置别名后，vscode无路径提示。

解决方法：两步

在项目根目录建立vue.config.js，用于vuecli加载。这一步是让vue项目能够识别名。

```js
const path = require('path');//引入path模块
function resolve(dir) {
  return path.join(__dirname, dir)//path.join(__dirname)设置绝对路径
}
module.exports = {
  chainWebpack: (config) => {
    config.resolve.alias
      .set('@', resolve('./src')) // 其实我只用得上这个@
      .set('components', resolve('./src/components'))
      .set('assets', resolve('./src/assets'))
      .set('common', resolve('./src/common'))
      .set('network', resolve('./src/network'))
      .set('views', resolve('./src/views'))
    //set第一个参数：设置的别名，第二个参数：设置的路径
  }
}
```

vs安装插件，Path Autocomplete，这个插件和Path Intellisense可以共存

然后打开它的扩展设置，点击setting.json。这一步是让vscode能够识别路径。

```js
{
    "path-autocomplete.excludedItems": {
        "**/*.js": {
            "when": "**/*.ts"
        },
        "**/*.map": {
            "when": "**"
        },
        "**/{.git,node_modules}": {
            "when": "**"
        }
    },
    "path-autocomplete.pathMappings": {
        "@": "${folder}/src",
        "components": "${folder}/src/components",
        "views": "${folder}/src/views",
        "network": "${folder}/src/network",
        "common": "${folder}/src/common",
        "assets": "${folder}/src/assets",
    }
}
```

##### 注意

在标签中要使用~assets/...，前面要加上一个~

```html
<img src='~assets/...'>
```

而在js代码中，则无需。

```js
import xxx from('assets/...')
```

##### 其他方法

下面方法对我**不适用**

vs安装插件，Path Intellisense

然后打开它的扩展设置，点击setting.json。

```js
{
    "path-intellisense.mappings": { 
      "@": "${workspaceRoot}/src", 
      "components": "${workspaceRoot}/src/components", 
    }
}
```

##### 感谢

[1] [vscode 引用路径的别名设置](https://blog.csdn.net/DxCaesar/article/details/104277013?utm_medium=distribute.pc_relevant_bbs_down.none-task--2~all~first_rank_v2~rank_v29-9.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task--2~all~first_rank_v2~rank_v29-9.nonecase)

### vue-swiper插件

https://github.com/surmon-china/vue-awesome-swiper

用这个插件要注意：

她使用分两步：

1. 全局vue注册，插件

2. 需要用到哪个功能还需要再导入，

   ```js
   // 从swiper中导入分页，自动播放
   import SwiperCore, { Pagination, Autoplay } from "swiper";
   
   // 向swiper注册这些功能modules
   SwiperCore.use([Pagination, Autoplay]);
   
   // 再设置属性
   {
       swiperOptions: {
           pagination: {
               el: ".swiper-pagination",
           },
           autoplay: true,
           loop: true,
       },
   }
   ```

   https://www.swiper.com.cn/api/autoplay/16.html

### axios

https://github.com/axios/axios

导入->使用

```js
const originAxios = require('axios');
const axios = (options) => {
  return new Promise((resolve, reject) => {
    const baseURL = 'http://123.207.32.32:8000
    const timeout = 5000

    const instance = originAxios.create({
      baseURL,
      timeout
    })

    // 两个拦截，请求和响应
    instance.interceptors.request.use((config) => {
      // 一些处理
      return config
    }, (err) => {
      // 请求拦截出错
      return err
    })

    instance.interceptors.response.use((res) => {
      // 一些处理
      return res.data
    }, (err) => {
      // 请求拦截出错
      if (err && err.response) {
        switch (err.response.status) {
          case 400:
            err.message = '请求错误'
            break
          case 401:
            err.message = '未授权的访问'
            break
        }
      }
      return err
    })

    // 请求
    instance(options).then((res) => {
      resolve(res)
      // }, (err) => {
      //   reject(err)
    }).catch((err) => {
      reject(err)
    })
  })
}

export default axios
```

### Vue路由开启keep-alive缓存页面

[1] [Vue路由开启keep-alive缓存页面](https://www.cnblogs.com/wangyunhui/p/8178392.html)

[2] [keep-alive的深入理解与使用(配合router-view缓存整个路由页面)](https://segmentfault.com/a/1190000010546663)



### GIT

手动关闭vscode的git功能

首选项->搜索git->找到是否启用git，关闭





### 当页面不出效果的检查步骤

1.看组件是否生成

2.看有无此元素（DOM）

3.看看此组件是否存在数据

​	父子通讯容易造成名字不同，导致数据传不过来，所以VueDevTools很好用，看一下组件中data，props有无数据。



### Vue基础

基础简单，但是也最容易出错

##### v-for

v-for用在谁身上，谁就被重复渲染。



### Css相关

##### a标签清除默认样式

```css
.rcm_item a {
  text-decoration: none;
  color: #333;
}
.rcm_item a:hover,
a:visited,
a:link,
a:active {
  color: #333;
}
```

##### 视口技巧

```css
/* position: relative; */  这里如果不用视口高度，则出现下页面随着上页面划出div 
height: 100vh;
```

##### z-index作用范围一定要设置布局为relative等

默认流式布局z-index不生效，css还得好好学。



##### 让多余文字截断

  text-overflow: ellipsis;



##### 为什么子绝父相才能实现before中显示效果

因为如果是原来，是流式布局，会显示在下方

```css
.collect:{
  position: relative;
}
.good-info .collect::before {
  content: "";
  position: absolute;
  left: -15px;
  top: 0;
  width: 14px;
  height: 14px;
  background: url("~assets/img/common/collect.svg") 0 0/14px 14px;
```





### 箭头函数跟这个函数有什么区别？

在写vue钩子的时候，不要用箭头函数，否则拿不到this

```js
export default {
     mounted() {  // 函数增强写法，拿得到this
        console.log(this);
    },
    mounted: function () {  // 常规函数，拿得到this
        console.log(this);
    },
    mounted: () => {  // 拿不到this，undefined，因为箭头函数有自己作用域 不知道这个this指向谁？
        console.log(this);
    }, 
}  	
```



