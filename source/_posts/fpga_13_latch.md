---
title: 锁存器综合相关问题
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理锁存器综合相关知识
categories: FPGA-详解
tags:
  - FPGA
  - latch
  - 锁存器
abbrlink: 3898341634
date: 2020-07-05 21:10:09
layout:
password:
---

## 锁存器综合相关问题

---

注 : 本文内容总结至[文章1](https://www.cnblogs.com/qiweiwang/archive/2011/03/15/1985095.html)、[文章2](https://zhuanlan.zhihu.com/p/80700130)



### 1 举例说明综合出锁存器

#### 代码A

```verilog
always @(*)begin
    if(d)
        a = b;
end
```

代码A解析：

这是一个always语句块构成的组合逻辑，其中缺少else分支。

当d = 1'b0时，综合工具会默认保持a的值，即生成锁存器。

<img src="/images/post_images/fpga_13_latch/fpga_13_latch_01">

#### 代码B

```verilog
always  @(*)begin
    if(d)
      a = b;
    else
      a = a;
end
```

代码B解析：

代码B虽然补全了else分支语句。但是，其代码依然有保持功能，即会生成锁存器。

#### 代码C

```verilog
always @ (b or d)
    case(d)
        2'b00: a = b >> 1;
        2'b11: c  =b >> 1;
    default:begin
                a = b;
                c = b;
            end
    endcase
```

代码C解析：

代码C在always语句块内使用了case语句，并且case语句中含有default分支。但是，我们可以发现，

d=2’b00时，没有说明c的赋值； d=2’b11时，没有说明a的赋值；

#### 代码D

```verilog
always @(b or d)begin
    a = b;
    c = b;
    case(d)
        2'b00: a = b >> 1;
        2'b11: c = b >> 1;
    endcase
end
```

代码D解析

代码D和代码C的不同是，它在always语句块的开始，对a和c进行了默认赋值。

这就类似于软件里面的初始化，当然只是类似而已。

从上面的综合结果，我们可以看出来，没有生成任何时序逻辑

#### 代码E 

```verilog
always@(b or d)begin
    case(d) //synopsys full_case
        2'b00: a = b >> 1;
        2'b11: c = b >> 1;
    endcase
end
```

代码E解析：

使用了//synopsys full_case 选项，

该选项：

1、防止综合工具将case语句综合成不必要的优先级电路，增大硬件开销

2、和default语句类似，补全case语句没有列出的情况。

3、但是 //synopsys full_case 会造成工具之间的移植性问题，该代码依然综合出了锁存器

4、可能会不好使，慎用

**解决always 描述组合逻辑时综合出锁存器的解决方法**

在组合逻辑中，容易综合出锁存器的语句是if和case，其实只要配对使用if...else;case用default就基本上可以避免锁存器；只有将组合逻辑所有的值都考虑到，并输出特定的值，避免存在考虑不到的输入导致系统认为信号需要保持的情况，就会综合生成锁存器；

**注：也就是说，组合逻辑是否会生成锁存器，其根本原因是该组合逻辑存在保持功能！**

---

### 2 综合锁存器解决方法

#### 2.1 if 综合锁存器

```verilog
//case1(有锁存器)：
module mux_latch
(
    input   [3:0] data,
    input   [1:0] valid,
    input   flag,
    output reg valid_data
);
always @ (*)begin
    if(valid==2'd0) valid_data = data[3];
    if(valid==2'd1) valid_data = data[2];
    if(valid==2'd2) valid_data = data[1];
    if(valid==2'd3) valid_data = data[0];
end
endmodule
```

解决办法1：加else与if配对

```verilog
always @ (*)begin
    if(valid==2'd0) valid_data = data[3];
    else if(valid==2'd1) valid_data = data[2];
    else if(valid==2'd2) valid_data = data[1];
    else if(valid==2'd3) valid_data = data[0];
    else valid_data = 1'b0;
end
```

解决办法2：赋初始值

```verilog
always @ (*)begin
    valid_data = 1'b0
    if(valid==2'd0) valid_data = data[3];
    if(valid==2'd1) valid_data = data[2];
    if(valid==2'd2) valid_data = data[1];
    if(valid==2'd3) valid_data = data[0];
end
```

#### 2.1 case 综合锁存器

```verilog
always @ (*)begin
    case(valid)
        2'b00 : begin if(flag) valid_data = data[0];end
        2'b01 : begin if(flag) valid_data = data[1];end
        2'b10 : begin if(flag) valid_data = data[2];end
        2'b11 : begin if(flag) valid_data = data[3];end
    endcase
end
```

直接加default，有锁存器，因为考虑不到的地方在于 flag 为 1’b0的时候，没有对应的输出，系统默认输出保持

```verilog
always @ (*)begin
    case(valid)
        2'b00 : begin if(flag) valid_data = data[0];end
        2'b01 : begin if(flag) valid_data = data[1];end
        2'b10 : begin if(flag) valid_data = data[2];end
        2'b11 : begin if(flag) valid_data = data[3];end
        default:valid_data=1'b0;
    endcase
end
```

解决办法1：赋初始值

```verilog
always @ (*)begin
    valid_data=1'b0;
    case(valid)
        2'b00 : begin if(flag) valid_data = data[0];end
        2'b01 : begin if(flag) valid_data = data[1];end
        2'b10 : begin if(flag) valid_data = data[2];end
        2'b11 : begin if(flag) valid_data = data[3];end
    endcase
end
```

解决办法2：加else

```verilog
always @ (*)begin
    case(valid)
        2'b00 : begin if(flag) valid_data = data[0];else valid_data = 1'b0;end
        2'b01 : begin if(flag) valid_data = data[1];else valid_data = 1'b0;end
        2'b10 : begin if(flag) valid_data = data[2];else valid_data = 1'b0;end
        2'b11 : begin if(flag) valid_data = data[3];else valid_data = 1'b0;end
    endcase
end
```

### 3 小结

综上所述：always 语句描述组合逻辑时，存在综合出锁存器的情况，原因在于存在 case 或 if 语句，其中条件考虑不完整，导致出现输出保持的现象，才会综合出锁存器；解决方法就是考虑所有的输入情况，将所有的输入情况对应的值都赋予输出，一般有，补充所有的 case 选项或者添加 default ，每一个 if 对应一个else，或者在最开始对输出赋值，当满足下方的 if 或 case 时进行改写，否则将初值输出。

​                      

​                       

[  完  ]