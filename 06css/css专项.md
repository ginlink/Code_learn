### 一、CSS

### 01 如何让很多的文字(p标签)只显示一行，且后面显示...

例如：

```js
啊啊啊啊啊奥军所绿多扩付军绿扩所大军付绿军
啊啊啊啊啊奥军所绿多扩付军绿扩所大军付绿军
啊啊啊啊啊奥军所绿多扩付军绿扩所大军付绿军
=>
啊啊啊啊啊奥军所绿多扩付军绿扩所大军付绿军...
```

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

### 01 css常见居中

```css
text-align: center; /* 让行内元素居中 */

margin: 0 auto;  /* 让块级元素居中 */ 那对行内块生效吗？不生效，因为行内块属于行内元素
```

### 01 弹性盒子的居中





### 02 各种元素display

元素呈现方式：行内元素，块级元素，弹性盒子元素(行内块)



### 02 定位position

定位：默认流式布局, static, relative, absolute, fixed

问题1: z-index什么时候生效？

  relative, absolute, fixed下都生效

问题2: 



### 03 原生js获取、修改css样式

说明两点：

​	获取：除IE用getComputedStyle(obj, '')

​	修改：用div.style.color = '#eee'

注意一点：div.style只能用于行内样式的读取

直接上兼容代码

```js
function getStyle(obj,name){
    if (obj.currentStyle) {  // 兼容IE8
        return obj.currentStyle[name];
    }else{
        return getComputedStyle(obj,false)[name];
    }
}
```

### 05 offsetWidth, clientWidth, scrollWidth innerWidth宽度

```css
元素：
offsetWidth: content + padding + border
clientWidth: content + padding
scrollWidth: 元素内容真实的宽度，内容不超出盒子高度时为盒子的clientWidth
窗口：
innerWidth: 浏览器窗口可视区宽度（不包括浏览器控制台、菜单栏、工具栏） 
```

盒子：content+padding+border, 而margin在盒子外部

[javascript中的offsetWidth、clientWidth、innerWidth及相关属性方法](https://blog.csdn.net/qq_33036599/article/details/81224346)

