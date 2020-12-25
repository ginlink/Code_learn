### 练习开发HYmall遇到的问题



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



