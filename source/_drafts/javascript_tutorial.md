---
title: JavaScript 学习笔记
author: yasheng
img: /medias/featureimages/amba.jpg
toc: true
mathjax: true
summary: 本文整理JavaScript
categories: ☸ 前端
tags:
  - JavaScript
  - 前端
abbrlink: 3470541821
date: 2020-05-20 15:27:09
password:
layout:
---


## JavaScript 学习笔记

---

 注 : 本文内容学习至[W3school](https://www.w3school.com.cn/index.html)，由个人整理

## JavaScript 简介

**JavaScript 是属于 HTML 和 Web 的编程语言。**

- JavaScript 能够改变 HTML 内容
- JavaScript 能够改变 HTML 属性
- JavaScript 能够改变 HTML 样式 (CSS)
- JavaScript 能够隐藏 HTML 元素
- JavaScript 能够显示 HTML 元素

## JavaScript 笔记

### JavaScript  使用

#### 1、规范

在 HTML 中，JavaScript 代码必须位于 `<script>` 与 `</script>` 标签之间。

```javascript
<script>
    document.getElementById("demo").innerHTML = "我的第一段 JavaScript";
</script>
```

**注释：**旧的 JavaScript 例子也许会使用 **type** 属性：`<script type="text/javascript">`。
**注释：**type 属性不是必需的。JavaScript 是 HTML 中的默认脚本语言。

#### 2、位置

**JavaScript位置**  可以放在 `<head>`、`<body>`、`外部`

**提示：**把脚本置于 `<body>`元素的底部，可改善显示速度，因为脚本编译会拖慢显示。

#### 3、外部js脚本

可以在` <head> `或 `<body> `中放置外部脚本引用。

```javascript
//外部脚本引用，该脚本的表现与它被置于 <script> 标签中是一样的。
<script src="myScript.js"> </script> 
```

**注释：**外部脚本不能包含`<script>`标签。

#### 4、外部js脚本优点

- 分离了 HTML 和代码
- 使 HTML 和 JavaScript 更易于阅读和维护
- 已缓存的 JavaScript 文件可加速页面加载

如需向一张页面添加多个脚本文件 - 请使用多个 script 标签：

```javascript
<script src="myScript1.js"></script>
<script src="myScript2.js"></script>
```

### JavaScript 输出

**JavaScript 不提供任何内建的打印或显示函数。**

JavaScript 能够以不同方式“显示”数据：

- 使用 window.alert() 写入警告框
- 使用 document.write() 写入 HTML 输出
- 使用 innerHTML 写入 HTML 元素
- 使用 console.log() 写入浏览器控制台

#### 1 使用 innerHTML

如需访问 HTML 元素，JavaScript 可使用 document.getElementById(id) 方法。

id 属性定义 HTML 元素。innerHTML 属性定义 HTML 内容：

```html
<!DOCTYPE html>
<html>
    <body>
        <h1>我的第一张网页</h1>
        <p>我的第一个段落</p>
        <p id="demo"></p>
        <script>
            document.getElementById("demo").innerHTML = 5 + 6;
        </script>
    </body>
</html> 
```

#### 2 使用 document.write()

出于测试目的，使用 document.write() 比较方便：

**注意：**在 HTML 文档完全加载后使用 **document.write()** 将**删除所有已有的 HTML**：

#### 3 使用 window.alert()

您能够使用警告框来显示数据：

```html
<!DOCTYPE html>
<html>
    <body>
        <h1>我的第一张网页</h1>
        <p>我的第一个段落</p>
        <script>
            window.alert(5 + 6);
        </script>
    </body>
</html> 
```

#### 4 使用 console.log()

在浏览器中，您可使用 console.log() 方法来显示数据。

请通过 F12 来激活浏览器控制台，并在菜单中选择“控制台”。

### JavaScript 语法

JavaScript 语句由以下构成：**值、运算符、表达式、关键词和注释。**

#### 1 分号

分号分隔 JavaScript 语句、在每条可执行的语句之后添加分号；

#### 2 JavaScript 空白字符

JavaScript 会忽略多个空格。您可以向脚本添加空格，以增强可读性。

#### 3 JavaScript 行长度和折行

为了达到最佳的可读性，程序员们常常喜欢把代码行控制在 80 个字符以内。

如果 JavaScript 语句太长，对其进行折行的最佳位置是某个运算符：

```java
document.getElementById("demo").innerHTML =
 "Hello Kitty.";
```

#### 4 JavaScript 代码块

JavaScript 语句可以用花括号（{...}）组合在代码块中。代码块的作用是定义一同执行的语句。您会在 JavaScript 中看到成块组合在一起的语句：

```javascript
function myFunction() {
    document.getElementById("demo").innerHTML = "Hello Kitty.";
    document.getElementById("myDIV").innerHTML = "How are you?";
}
```

#### 5 JavaScript 关键词

| 关键词        | 描述                                              |
| :------------ | :------------------------------------------------ |
| break         | 终止 switch 或循环。                              |
| continue      | 跳出循环并在顶端开始。                            |
| debugger      | 停止执行 JavaScript，并调用调试函数（如果可用）。 |
| do ... while  | 执行语句块，并在条件为真时重复代码块。            |
| for           | 标记需被执行的语句块，只要条件为真。              |
| function      | 声明函数。                                        |
| if ... else   | 标记需被执行的语句块，根据某个条件。              |
| return        | 退出函数。                                        |
| switch        | 标记需被执行的语句块，根据不同的情况。            |
| try ... catch | 对语句块实现错误处理。                            |
| var           | 声明变量。                                        |

**注释：**JavaScript 关键词指的是保留的单词。保留词无法用作变量名。

#### 6 JavaScript 值

JavaScript 语句定义两种类型的值：**混合值**和**变量值**。

**JavaScript 字面量**：应该是常量

```javascript
15.90
10011
"Bill Gates"
'Bill Gates' 
```

**JavaScript 变量**

变量用于存储数据值。JavaScript 使用 **var** 关键词来声明变量。**=** 号用于为变量*赋值*。

```javascript
var x;
x = 7;
```

#### 7 JavaScript 运算符

JavaScript 使用算数运算符（+ - * /）来计算值

#### 8 JavaScript 表达式

表达式是值、变量和运算符的组合，计算结果是值。

#### 9 JavaScript 注释

并非所有 JavaScript 语句都被“执行”。

双斜杠 // 或 /* 与 **/* 之间的代码被视为*注释*。

注释会被忽略，不会被执行：

```javascript
var x = 7;   // 会执行
// var x = 8;   不会执行 单行注释
/* 多行注释
多行注释
多行注释 */
```

#### 10 JavaScript 标识符

标识符是名称。

在 JavaScript 中，标识符用于命名变量（以及关键词、函数和标签）。在大多数编程语言中，合法名称的规则大多相同。

在 JavaScript 中，首字符必须是字母、下划线（-）或美元符号（$）。连串的字符可以是字母、数字、下划线或美元符号。

**提示：**数值不可以作为首字符。这样，JavaScript 就能轻松区分标识符和数值。

**JavaScript 对大小写敏感**：所有 JavaScript 标识符*对大小写敏感*。变量 lastName 和 lastname，是两个不同的变量。

#### 11 JavaScript 命名规则

下划线：

```
first_name, last_name, master_card, inter_city.
```

驼峰式大小写（Camel Case）：

```
FirstName, LastName, MasterCard, InterCity.
```

**注释：**JavaScript 中不能使用连字符。它是为减法预留的。



### JavaScript 变量及运算符

能够通过 var 声明变量

```javascript
var person = "Bill Gates", carName = "porsche", price = 15000;
```

算术运算符

| 运算符 | 描述       |
| :----- | :--------- |
| +      | 加法       |
| -      | 减法       |
| *      | 乘法       |
| /      | 除法       |
| %      | 系数       |
| ++     | 递加       |
| --     | 递减       |
| **     | 幂（新加） |

赋值运算符

| 运算符 | 例子   | 等同于    |
| :----- | :----- | :-------- |
| =      | x = y  | x = y     |
| +=     | x += y | x = x + y |
| -=     | x -= y | x = x - y |
| *=     | x *= y | x = x * y |
| /=     | x /= y | x = x / y |
| %=     | x %= y | x = x % y |

比较运算符

| 运算符 | 描述           |
| :----- | :------------- |
| ==     | 等于           |
| ===    | 等值等型       |
| !=     | 不相等         |
| !==    | 不等值或不等型 |
| >      | 大于           |
| <      | 小于           |
| >=     | 大于或等于     |
| <=     | 小于或等于     |
| ?      | 三元运算符     |

逻辑运算符

| 运算符 | 描述   |
| :----- | :----- |
| &&     | 逻辑与 |
| \|\|   | 逻辑或 |
| !      | 逻辑非 |

位运算符

| 运算符 | 描述         | 例子    | 等同于       | 结果 | 十进制 |
| :----- | :----------- | :------ | :----------- | :--- | :----- |
| &      | 与           | 5 & 1   | 0101 & 0001  | 0001 | 1      |
| \|     | 或           | 5 \| 1  | 0101 \| 0001 | 0101 | 5      |
| ~      | 非           | ~ 5     | ~0101        | 1010 | 10     |
| ^      | 异或         | 5 ^ 1   | 0101 ^ 0001  | 0100 | 4      |
| <<     | 零填充左位移 | 5 << 1  | 0101 << 1    | 1010 | 10     |
| >>     | 有符号右位移 | 5 >> 1  | 0101 >> 1    | 0010 | 2      |
| >>>    | 零填充右位移 | 5 >>> 1 | 0101 >>> 1   | 0010 | 2      |

### JavaScript 数据类型

**字符串值，数值，布尔值，数组，对象。**

JavaScript 拥有动态类型。这意味着相同变量可用作不同类型：

```verilog
var length = 7;                             // 数字
var lastName = "Gates";                      // 字符串
var y = true;								//布尔
var cars = ["Porsche", "Volvo", "BMW"];         // 数组
var x = {firstName:"Bill", lastName:"Gates"};    // 对象 
```

### JavaScript 函数

**函数定义**

```javascript
function myFunction(p1, p2) {
    return p1 * p2;              // 该函数返回 p1 和 p2 的乘积
}
```

**函数调用**

函数中的代码将在其他代码调用该函数时执行：

- 当事件发生时（当用户点击按钮时）
- 当 JavaScript 代码调用时
- 自动的（自调用）

**函数返回**

当 JavaScript 到达 return 语句，函数将停止执行。

如果函数被某条语句调用，JavaScript 将在调用语句之后“返回”执行代码。

函数通常会计算出*返回值*。这个返回值会返回给调用者：

```javascript
var x = myFunction(7, 8);        // 调用函数，返回值被赋值给 x
function myFunction(a, b) {
    return a * b;                // 函数返回 a 和 b 的乘积
}
```

**注意：**

实例：访问没有 () 的函数将返回函数定义：

```javascript
function toCelsius(fahrenheit) {
    return (5/9) * (fahrenheit-32);
}
document.getElementById("demo").innerHTML = toCelsius;
```

JavaScript 对象

```javascript
var person = {
  firstName: "Bill",
  lastName : "Gates",
  id       : 678,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

### JavaScript 事件

```html
<!DOCTYPE html>
<html>
    <body>
        <h1>JavaScript 事件</h1>
        <button onclick="this.innerHTML=Date()">时间是？</button>
    </body>
</html>
```



| 事件        | 描述                         |
| :---------- | :--------------------------- |
| onchange    | HTML 元素已被改变            |
| onclick     | 用户点击了 HTML 元素         |
| onmouseover | 用户把鼠标移动到 HTML 元素上 |
| onmouseout  | 用户把鼠标移开 HTML 元素     |
| onkeydown   | 用户按下键盘按键             |
| onload      | 浏览器已经完成页面加载       |



### JavaScript 字符串及方法-略

### JavaScript 数字及方法-略

toString()

### JavaScript 数组及方法-略

排序，数组迭代方法

### JavaScript 日期及格式-简略

```javascript
var d = new Date(); //当前日期和时间创建新的日期对象：
```

还可以自定义时间

日期获取方法

| 方法              | 描述                                 |
| :---------------- | :----------------------------------- |
| getDate()         | 以数值返回天（1-31）                 |
| getDay()          | 以数值获取周名（0-6）                |
| getFullYear()     | 获取四位的年（yyyy）                 |
| getHours()        | 获取小时（0-23）                     |
| getMilliseconds() | 获取毫秒（0-999）                    |
| getMinutes()      | 获取分（0-59）                       |
| getMonth()        | 获取月（0-11）                       |
| getSeconds()      | 获取秒（0-59）                       |
| getTime()         | 获取时间（从 1970 年 1 月 1 日至今） |

日期设置方法

| 方法              | 描述                                         |
| :---------------- | :------------------------------------------- |
| setDate()         | 以数值（1-31）设置日                         |
| setFullYear()     | 设置年（可选月和日）                         |
| setHours()        | 设置小时（0-23）                             |
| setMilliseconds() | 设置毫秒（0-999）                            |
| setMinutes()      | 设置分（0-59）                               |
| setMonth()        | 设置月（0-11）                               |
| setSeconds()      | 设置秒（0-59）                               |
| setTime()         | 设置时间（从 1970 年 1 月 1 日至今的毫秒数） |



### JavaScript 数学方法-略

能够使用数学常量及基本函数计算公式

产生随机数等

### JavaScript 逻辑比较-略

### JavaScript 正则表达式-略

https://www.w3school.com.cn/js/js_regexp.asp

### JavaScript 异常

```JavaScript
<p id="demo"></p>
<script>
    try {
        adddlert("欢迎访问！");
    }
catch(err) {
    document.getElementById("demo").innerHTML = err.message;
}
</script>
```

### JavaScript 作用域

**全局 JavaScript 变量**

函数之外声明的变量，会成为*全局变量*。

全局变量的作用域是*全局的*：网页的所有脚本和函数都能够访问它。

**自动全局**

如果您为尚未声明的变量赋值，此变量会自动成为*全局*变量。

这段代码将声明一个全局变量 carName，即使在函数内进行了赋值。



### JavaScript 严格模式

严格模式使我们更容易编写“安全的” JavaScript。

严格模式把之前可接受的“坏语法”转变为真实的错误。

举例来说，在普通的 JavaScript 中，错打变量名会创建新的全局变量。在严格模式中，此举将抛出错误，这样就不可能意外创建全局变量。

在普通 JavaScript 中，如果向不可写属性赋值，开发者不会得到任何错误反馈。

在严格模式中，向不可写的、只能读取的、不存在的属性赋值，或者向不存在的变量或对象赋值，将抛出错误。

### JavaScript -简约省略部分，W3School可查找对应部分





### JavaScript HTML DOM

当网页被加载时，浏览器会创建页面的文档对象模型（*D*ocument *O*bject *M*odel）。

*HTML DOM* 模型被结构化为*对象树*：

**对象的 HTML DOM 树**

![image-20200520165152247](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200520165152247.png)

通过这个对象模型，JavaScript 获得创建动态 HTML 的所有力量：

- JavaScript 能改变页面中的所有 HTML 元素
- JavaScript 能改变页面中的所有 HTML 属性
- JavaScript 能改变页面中的所有 CSS 样式
- JavaScript 能删除已有的 HTML 元素和属性
- JavaScript 能添加新的 HTML 元素和属性
- JavaScript 能对页面中所有已有的 HTML 事件作出反应
- JavaScript 能在页面中创建新的 HTML 事件

### JavaScript - HTML DOM 方法

**HTML DOM 方法是您能够（在 HTML 元素上）执行的动作。**

**HTML DOM 属性是您能够设置或改变的 HTML 元素的值。**

#### getElementById 方法

访问 HTML 元素最常用的方法是使用元素的 id。

在上面的例子中，getElementById 方法使用 id="demo" 来查找元素。

#### innerHTML 属性

获取元素内容最简单的方法是使用 innerHTML 属性。

innerHTML 属性可用于获取或替换 HTML 元素的内容。

innerHTML 属性可用于获取或改变任何 HTML 元素，包括 `<html>` 和 `<body>`。



### JavaScript HTML DOM 文档

#### 查找元素

| 方法                                    | 描述                   |
| :-------------------------------------- | :--------------------- |
| document.getElementById(*id*)           | 通过元素 id 来查找元素 |
| document.getElementsByTagName(*name*)   | 通过标签名来查找元素   |
| document.getElementsByClassName(*name*) | 通过类名来查找元素     |

#### 改变 HTML 元素

| 方法                                       | 描述                   |
| :----------------------------------------- | :--------------------- |
| element.innerHTML = *new html content*     | 改变元素的 inner HTML  |
| element.attribute = *new value*            | 改变 HTML 元素的属性值 |
| element.setAttribute(*attribute*, *value*) | 改变 HTML 元素的属性值 |
| element.style.property = *new style*       | 改变 HTML 元素的样式   |

#### 添加和删除元素

| 方法                              | 描述             |
| :-------------------------------- | :--------------- |
| document.createElement(*element*) | 创建 HTML 元素   |
| document.removeChild(*element*)   | 删除 HTML 元素   |
| document.appendChild(*element*)   | 添加 HTML 元素   |
| document.replaceChild(*element*)  | 替换 HTML 元素   |
| document.write(*text*)            | 写入 HTML 输出流 |

#### 添加事件处理程序

| 方法                                                     | 描述                            |
| :------------------------------------------------------- | :------------------------------ |
| document.getElementById(id).onclick = function(){*code*} | 向 onclick 事件添加事件处理程序 |



### JavaScript 查找 HTML 元素

通常，通过 JavaScript，您需要操作 HTML 元素。

为了达成此目的，您需要首先找到这些元素。有好几种完成此任务的方法：

- 通过 id 查找 HTML 元素
- 通过标签名查找 HTML 元素
- 通过类名查找 HTML 元素
- 通过 CSS 选择器查找 HTML 元素
- 通过 HTML 对象集合查找 HTML 元素



