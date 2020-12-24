### StringExtend（字符串扩展）

ES6加强了对Unicode的支持，并且扩展了字符串对象。

### 字符的Unicode表示法

ES6 对这一点做出了改进，只要将码点放入大括号，就能正确解读该字符。

```javascript
"\u{20BB7}"
// "𠮷" 𠮷这个不是吉利的'吉'，它占4个字节，但是js只能识别2个字节的字，所以需要扩展写法
```

有了这种表示法之后，JavaScript共有6种方法可以表示一个字符。[懵逼]

```javascript
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```

## codePointAt()

```javascript
var s = "𠮷";

s.length // 2
s.charAt(0) // '' 无法读取
s.charAt(1) // '' 无法读取
s.charCodeAt(0) // 55362 读了前两个字节
s.charCodeAt(1) // 57271 读了后两个字节
// js只能处理两个字节的字符
// 所以ES6扩展版
s.codePointAt(0) // 134071 “𠮷”的十进制码点134071（即十六进制的20BB7）
s.codePointAt(1) // 57271 “𠮷”的后两个字节
s.codePointAt(2) // 97 “a”的十进制码点
// 转换为十六进制
s.codePointAt(0).toString(16) // "20bb7"
s.codePointAt(2).toString(16) // "61"
```

你可能注意到了，`codePointAt`方法的参数，仍然是不正确的。比如，上面代码中，字符`a`在字符串`s`的正确位置序号应该是1，但是必须向`codePointAt`方法传入2。解决这个问题的一个办法是使用`for...of`循环，因为它会正确识别32位的UTF-16字符。

```javascript
var s = '𠮷a';
for (let ch of s) {
  console.log(ch.codePointAt(0).toString(16));
}
// 20bb7
// 61
```

应用 `codePointAt`方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。

```javascript
function is32Bit(c) {
  return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false
```

## String.fromCodePoint()

ES5提供`String.fromCharCode`方法，用于从码点返回对应字符，但是这个方法不能识别32位的UTF-16字符（Unicode编号大于`0xFFFF`）。

```javascript
String.fromCharCode(0x20BB7)
// "ஷ"

// ES6提供了String.fromCodePoint方法，可以识别0xFFFF的字符，弥补了
// String.fromCharCode方法的不足。在作用上，正好与codePointAt方法相反。
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
```

## 字符串的遍历器接口

ES6为字符串添加了遍历器接口

```javascript
for (let codePoint of 'fo𠮷') {
  console.log(codePoint)
}
// "f"
// "o"
// "𠮷" 可以识别大于0xFFFF的码点
```

## at()

```javascript
'abc'.charAt(0) // "a" 返回字符串给定位置的字符
'𠮷'.charAt(0) // "\uD842" 出错

'𠮷'.at(0) // "𠮷" 正确
```

## normalize()

Unicode正规化

```javascript
'\u01D1'==='\u004F\u030C' //false
'\u01D1'.length // 1
'\u004F\u030C'.length // 2

'\u01D1'.normalize() === '\u004F\u030C'.normalize()
// true
```

## includes(), startsWith(), endsWith()

传统上，JavaScript只有`indexOf`方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6又提供了三种新方法。

- **includes()**：返回布尔值，表示是否找到了参数字符串。
- **startsWith()**：返回布尔值，表示参数字符串是否在源字符串的头部。
- **endsWith()**：返回布尔值，表示参数字符串是否在源字符串的尾部。

```javascript
var s = 'Hello world!';
s.startsWith('world', 6) // true 这三个方法都支持第二个参数，表示开始搜索的位置。
s.endsWith('Hello', 5) // true
```

## repeat()

`epeat`方法返回一个新字符串，表示将原字符串重复`n`次。

```javascript
'x'.repeat(3) // "xxx"
'na'.repeat(0) // ""
'na'.repeat(2.9) // "nana" 向下取整
'na'.repeat('na') // ""
'na'.repeat('3') // "nanana"
```

## padStart()，padEnd()

ES7推出了字符串补全长度的功能。

```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
```

```javascript
'abc'.padStart(10, '0123456789') // 太长了，截取
// '0123456abc'
'x'.padStart(4) // '   x' 不给填充内容，则补空
```

`padStart`的常见用途是为数值补全指定位数。

```javascript
'1'.padStart(10, '0') // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```

另一个用途是提示字符串格式。

```javascript
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

## 模板字符串

```javascript
`` // 两个反引号
```

## （了解）实例：模板编译

下面，我们来看一个通过模板字符串，生成正式模板的实例。[头大]

```javascript
var template = `
<ul>
  <% for(var i=0; i < data.supplies.length; i++) { %>
    <li><%= data.supplies[i] %></li>
  <% } %>
</ul>
`;
```

上面代码在模板字符串之中，放置了一个常规模板。该模板使用`<%...%>`放置JavaScript代码，使用`<%= ... %>`输出JavaScript表达式。

怎么编译这个模板字符串呢？

一种思路是将其转换为JavaScript表达式字符串。

```javascript
echo('<ul>');
for(var i=0; i < data.supplies.length; i++) {
  echo('<li>');
  echo(data.supplies[i]);
  echo('</li>');
};
echo('</ul>');
```

这个转换使用正则表达式就行了。

```javascript
var evalExpr = /<%=(.+?)%>/g;
var expr = /<%([\s\S]+?)%>/g;

template = template
  .replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')
  .replace(expr, '`); \n $1 \n  echo(`');

template = 'echo(`' + template + '`);';
```

然后，将`template`封装在一个函数里面返回，就可以了。

```javascript
var script =
`(function parse(data){
  var output = "";

  function echo(html){
    output += html;
  }

  ${ template }

  return output;
})`;

return script;
```

将上面的内容拼装成一个模板编译函数`compile`。

```javascript
function compile(template){
  var evalExpr = /<%=(.+?)%>/g;
  var expr = /<%([\s\S]+?)%>/g;

  template = template
    .replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')
    .replace(expr, '`); \n $1 \n  echo(`');

  template = 'echo(`' + template + '`);';

  var script =
  `(function parse(data){
    var output = "";

    function echo(html){
      output += html;
    }

    ${ template }

    return output;
  })`;

  return script;
}
```

`compile`函数的用法如下。

```javascript
var parse = eval(compile(template));
div.innerHTML = parse({ supplies: [ "broom", "mop", "cleaner" ] });
//   <ul>
//     <li>broom</li>
//     <li>mop</li>
//     <li>cleaner</li>
//   </ul>
```

### （了解）标签模板

```javascript
alert`123`
// 等同于
alert(123)

var a = 5;
var b = 10;

tag`Hello ${ a + b } world ${ a * b }`;
// 等同于
tag(['Hello ', ' world ', ''], 15, 50);
```

`tag`函数的第一个参数是一个数组，该数组的成员是模板字符串中那些没有变量替换的部分

`tag`函数的其他参数，都是模板字符串各个变量被替换后的值。

```javascript
function passthru(literals, ...values) {
  var output = "";
  for (var index = 0; index < values.length; index++) {
    output += literals[index] + values[index];
  }

  output += literals[index]
  return output;
}
```

“标签模板”的一个重要应用，就是过滤HTML字符串，防止用户输入恶意内容。

模板处理函数的第一个参数（模板字符串数组），还有一个`raw`属性。该数raw属性，保存的是转义后的原字符串。

```javascript
tag`First line\nSecond line`

function tag(strings) {
  console.log(strings.raw[0]);
  // "First line\\nSecond line"
}
```

## String.raw()

ES6还为原生的String对象，提供了一个`raw`方法。

`String.raw`方法，往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。

```javascript
String.raw`Hi\n${2+3}!`;
// "Hi\\n5!"

String.raw`Hi\u000A!`;
// 'Hi\\u000A!'

String.raw`Hi\\n` // 存在，则不处理
// "Hi\\n"
```