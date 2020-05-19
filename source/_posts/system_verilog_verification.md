---
title: SystemVerilog 笔记
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理SystemVerilog笔记
categories: ☸ FPGA
tags:
  - FPGA
  - SystemVerilog
password: 1df1854015e31ca286d015345eaff29a6c6073f70984a3a746823d4cac16b075
abbrlink: 3095056096
date: 2020-05-18 15:27:09
layout:
---


## SystemVerilog 学习笔记

---

 注 : 本文内容来自书籍《SystemVerilog 验证 -- 测试平台编写指南》

## 第一章：验证导论

硬件验证语言相比于硬件描述语言的性质：

(1)受约束的随机激励生成
(2)功能覆盖率。
(3)更高层次的结构,尤其是面向对象的编程。
(4)多线程及线程间的通信。
(5)支持HDL数据类型,例如 Verilog的四状态数值。
(6)集成了事件仿真器,便于对设计施加控制。

**测试平台的基本功能**

测试平台的用途在于确定待测设计的正确性。包含下列步骤:
(1)产生激励。
(2)把激励施加到DUT上。
(3)捕捉响应。
(4)检验正确性
(5)对照整个验证目标测算进展情况。
有些步骤是测试平台自动完成的,有些则需要手工操作。而你选择的方法学则决定了上述步骤如何展开。

**带着所有层次的验证测试平台**

![image-20200518145726756](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518145726756.png)



## 第二章：数据结构

System Verilog引进了一些新的数据类型它们具有如下优点
(1)双状态数据类型:更好的性能,更低的内存消耗。
(2)队列、动态和关联数组:减少内存消耗,自带搜索和分类功能。
(3)类和结构:支持抽象数据结构
(4)联合和合并结构:允许对同一数据有多种视图(view)。
(5)字符串:支持内建的字符序列。
(6)枚举类型:方便代码编写,增加可读性。

### 2.1 内建数据类型

#### 2.1.1 逻辑类型

System Verilog对经典的reg数据类型进行了改进,使得它除了作为一个变量以外,还可以被连续赋值、门单元和模块所驱动。为了与寄存器类型相区别,这种改进的数据类型被称为logic。任何使用线网的地方均可以使用logic,但要求logic不能有多个结构性的驱动例如在对双向总线建模的时候。此时,需要使用线网类型,例如wire, System Verilog会对多个数据来源进行解析以后确定最终值。

**logic示例**

```systemverilog
module logic_data_type(input logic rst_h);
    parameter CYCLE = 20;
    logic q,a_l,d,clk,rst_l
    initial begin
        clk = 0;
        //过程赋值
        forever #(CYCLE/2) clk = ~clk;
    end
    assign rst_l = ~rst_h;
    
    not n1(q_l,q)
    my_dff d1(q, d, clk, rst_l);

endmodule
```

由于logic类型只能有一个驱动,所以你可以使用它来查找网单中的漏洞。把你所有的信号都声明为logic而不是reg或wire,如果存在多个驱动,那么编译时就会出现错误。当然,有些信号你本来就希望它有多个驱动,例如双向总线,这些信号就需要被定义成线网类型,例如wire

#### 2.1.2 双状态数据类型

相比四状态数据类型, System Verilog引人的双状态数据类型有利于提高仿真器的性能并减少内存的使用量。最简单的双状态数据类型是`bit`,它是无符号的。另四种带符号的双状态数据类型是`byte`, `short int`,`int`和 `long int`

| 类型      | 解释                     |
| --------- | ------------------------ |
| bit       | 1位双状态数据            |
| byte      | 8位**有符号**双状态数据  |
| short int | 16位**有符号**双状态数据 |
| int       | 32位**有符号**双状态数据 |
| long int  | 64位**有符号**双状态数据 |

![image-20200518151238658](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518151238658.png)

**注意是有符号数据类型**

![image-20200518151343060](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518151343060.png)

### 2.2 定宽数组

#### 2.2.1 定宽数组定义及初始化

Verilog要求在声明中必须给出数组的上下界。因为几乎所有数组都使用0作为索引下界,所以 System Verilog允许只给出数组宽度的便捷声明方式,跟C语言类似

![image-20200518151609983](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518151609983.png)

可以通过在变量名后面指定维度的方式来创建多维定宽数组。例2.5创建了几个维的整数数组,大小都是8行4列,最后一个元素的值被设置为1。多维数组在Verilog2001中已经引入,但这种紧凑型声明方式却是新的。

![image-20200518151557375](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518151557375.png)

#### 2.2.2 常亮数组

例2.7示范了如何使用常量数组,即一个单引号加大括号来初始化数组(注意这里的单引号并不同于编译器指引或宏定义中的单引号)。你可以一次性地为数组的部分或所有元素赋值。在大括号前标上重复次数可以对多个元素重复赋值,还可以为那些没有显式赋值的元素指定一个缺省值

![image-20200518151812880](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518151812880.png)

#### 2.2.3 数组的基本操作 for/foreach

操作数组的最常见的方式是使用for或 foreach循环。在例2.8中,被声明为for循环内的局部变量。 System verilog的size函数返回数组的宽度。在 foreach循环中,只需要指定数组名并在其后面的方括号中给出索引变量, System Verilog便会自动遍历数组中的元素。索引变量将自动声明,并只在循环内有效

![image-20200518152030763](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152030763.png)

注意在例2.9中,对多维数组使用 foreach的语法可能会与你设想的有所不同。使用时并不是像[订[门这样把每个下标分别列在不同的方括号里,而是用逗号隔开后放在同一个方括号里,像[i,j]

![image-20200518152147025](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152147025.png)

如果你不需要遍历数组中的所有维度,可以在 foreach循环里忽略掉它们。例2.11,把一个二维数组打印成一个方形的阵列。它在外层循环中遍历第一个维度,然后在内层循环中遍历第二个维度

![image-20200518152332608](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152332608.png)

![image-20200518152420783](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152420783.png)

#### 2.2.4 数组的基本操作 复制/比较

直接使用 == 

#### 2.2.5 同时使用位下标和数组下标

![image-20200518152726970](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152726970.png)

#### 2.2.6 合并数组

声明合并数组时,合并的位和数组大小作为数据类型的一部分必须在变量名前面指定。数组大小定义的格式必须是[msb:1sb],而不是[size]。例2.15中的变量 bytes是个有4个字节的合并数组,使用单独的32比特的字来存放,如图2.2所示。

![image-20200518152911534](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518152911534.png)

### 2.3 动态数组

在仿真时动态调整数据宽度，减少资源损耗

动态数组在声明时使用空的下标 []。这意味着数组的宽度不在编译时给出,而在程序运行时再指定。数组在最开始时是空的,所以你必须调用new]操作符来分配空间,同时在方括号中传递数组宽度。可以把数组名传给new[]构造符,并把已有数组的值复制到新数组里,如例2.17所示。

![image-20200518153220356](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518153220356.png)

### 2.4 队列

​		System verilog引进了一种新的数据类型—队列,它结合了链表和数组的优点。队列与链表相似,可以在一个队列中的任何地方增加或删除元素,这类操作在性能上的损失比动态数组小得多,因为动态数组需要分配新的数组并复制所有元素的值。队列与数组相似,可以通过索引实现对任一元素的访问,而不需要像链表那样去遍历目标元素之前的所有元素。
​		队列的声明是使用带有美元符号的下标匀]。队列元素的编号从0到S。例2.19示范了如何使用方法( method在队列中增加和删除元素。注意队列的常量( literal)只有大括号而没有数组常量中开头的单引号。

​		System Verilog的队列类似于标准模板库( standard template library)中的双端队列你通过增加元素来创建队列, System Verilog会分配额外的空间以便你能够快速插入新元素。当元素增加到超过原有空间的容量时, System verilog会自动分配更多的空间。其结果是,你可以扩大或缩小队列,但不用像动态数组那样在性能上付出很大代价, System Verilog会随时记录闲置的空间。注意不要对队列使用构造函数new[]。

![image-20200518153546225](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518153546225.png)

你可以使用字下标串联来替代方法。如果把$放在一个范围表达式的左边,那么$将代表最小值,例如[s:2]就代表[0:2]。如果$放在表达式的右边,则代表最大值,如例2.20中初始化块的第一行里的[1:5就表示[1:2]

### 2.5 关联数组

p54页

### 2.6 链表

虽然 System Verilog提供了链表,但应该避免使用它

### 2.7 数组的方法

#### 2.7.1 数组的缩减方法

基本的数组缩减方法是把一个数组编减成一个值,如例2.23所示。最常用的缩减方法是sum,它对数组中的所有元素求和。

```
on.sum	//on是数组，sum是求和方法
```

#### 2.7.2 数组定位方法

![image-20200518154053738](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518154053738.png)

使用 foreach循环固然可以实现数组的完全搜索,但是如果使用 System Verilog的定位方法,则只需一个操作便可完成。表达式with可以指示 System Verilog如何进行搜索,如例2.26所示

![image-20200518154203130](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518154203130.png)

#### 2.7.3数组的排序

![image-20200518154248243](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518154248243.png)

![image-20200518154318831](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518154318831.png)

### 2.8 选择存储类型
根据下列方面选择最好的数据存储方式

1）灵活性
2）存储器用量
3）速度
4）排序
5）选择最优的数据结构

### 2.9 使用 typedef创建新的类型

![image-20200518154855587](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518154855587.png)

### 2.10 创建用户自定义结构

使用`struct`创建用户自定义结构

![image-20200518155038749](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155038749.png)

### 2.11 类型装换

静态转换，动态转换，流操作符

![image-20200518155145887](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155145887.png)

### 2.12 枚举类型

p69页

![image-20200518155335063](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155335063.png)

**这里,使用后缀“e”来表示枚举类型。**

![image-20200518155415624](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155415624.png)

### 2.13 常量 

![image-20200518155532300](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155532300.png)

### 2.14 字符串

![image-20200518155705611](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155705611.png)

### 2.15 表达式的位宽

![image-20200518155828527](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518155828527.png)



## 第三章：过程语句和子程序

### 3.1 过程语句

System Verilog从C和C十十中引入了很多操作符和语句。你可以在for循环中定义循环变量,它的作用范围仅限于循环内部,从而有助于避免一些代码漏洞。自动递增符++”和自动递减符“--”既可以作为前缀,也可以作为后缀。如果在 begin或fork语句中使用标识符,那么在相对应的end或join语句中可以放置相同的标号,这使得程序块的首尾匹配更加容易。你也可以把标识符放在 System Verilog的其他结束语句里,例如endmodule、 endtask、 endfunction以及本书将介绍的其他语句。例3.1展示了一些新的语法结构

![image-20200518160249477](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518160249477.png)

**读取文件**

![image-20200518160334174](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518160334174.png)

### 3.2 任务、函数、void函数

在 Verilog中,任务(task)和函数( function)之间有很明显的区别,其中最重要的一点是,任务可以消耗时间而函数不能。函数里面不能带有诸如#100的时延语句或诸如⑧( posedge clock)、wait( ready)的阻塞语句,也不能调用任务。另外, verilog中的函数必须有返回值,并且返回值必须被使用,例如用到赋值语句中。

如果你有一个不消耗时间的 SystemVerilog任务,你应该把它定义成void函数,这种函数没有返回值。这样它就能被任何任务或函数所调用。从最大灵活性的角度考虑,所有用于调试的子程序都应该定义成void函数而非任务,以便于被任何其他任务或函数所调用。例3.3可以输出状态机的当前状态值。

![image-20200518160529114](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518160529114.png)

### 3.3 任务函数概述

System Verilog在任务和函数上做了一些小改进,使得它们看起来更像C或C++的程序。一般情况下,不带参数的子程序在定义或调用时并不需要带空括号()。为清楚起见,本书对此种情形的子程序将全部带括号。

![image-20200518160627052](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200518160627052.png)



### 3.4 子程序参数

P77页详解

### 3.5 子程序返回

p82页详解

### 3.6 局部数据存储

p84页详解

### 3.7 时间值

可以指定一个确定的时间，不用 timescale





