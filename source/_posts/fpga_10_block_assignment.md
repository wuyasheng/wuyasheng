---
title: 阻塞赋值与非阻塞赋值
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: false
summary: 本文整理阻塞赋值与非阻塞赋值
categories: FPGA-详解
tags:
  - FPGA
  - 阻塞赋值
abbrlink: 2951364644
date: 2020-05-21 10:10:09
layout:
password:
---

## 阻塞赋值与非阻塞赋值

---

注 : 本文内容来整理摘抄至《FPGA自学笔记 -- 设计与验证》

### 概念解释

**阻塞赋值**，操作符为“=”，“阻塞”是指在进程语句（initial 和 always）中，当前的赋值语句会阻断其后语句的正常执行，也就是说后面的语句必须等到当前的赋值语句执行完毕才能执行。而且阻塞赋值可以看成是一步完成的，即：计算等号右边的值并同时赋给左边变量。
**非阻塞赋值**，操作符为“<=”，“非阻塞”是指在进程语句（initial 和 always）中，当前的赋值语句不会阻断其后语句的正常执行。

---

为了详细说明阻塞赋值与非阻塞赋值对综合之后电路的影响，下面以具体设计进行分析说明。其中端口列表均为下面所示，这部分代码不再重复。

```verilog
//本内容，端口定义等，在下文中不再重复说明
module block_nonblock(Clk,Rst_n,a,b,c,out);
    input Clk;
    input Rst_n;
    input a,b,c;
    output reg [1:0]out;
    //………………………………………// 此部分见下
endmodule
```



### 阻塞赋值实现方式

首先在时序电路中使用阻塞赋值的方式，描述一个加法器。这种方式实际生成的逻辑电路如图所示。

```verilog
reg [1:0] d;
always@(posedge Clk or negedge Rst_n)begin
    if(!Rst_n)
        out = 2'b0;
    else begin
        d = a + b ;
        out = d + c;
    end
end
```

<img src="/images/post_images/fpga_10_block_assignment/fpga_10_block_assignment_01.png">

现在把阻塞赋值的两条语句顺序颠倒一下，再次综合可以得到图11.2所示的逻辑电路。可以看出调整顺序后与不调整时生成的逻辑电路不一致。现结合实验原理给出详细解释，当执行 out = d + c 时，d 的数据此时并不是更新后 a+b 的数据，而是上一个 Clk 上升沿到来时d 的数据，这也就解释了为何还有一个 D 触发器的存在。通俗的讲阻塞，out 这条语句阻塞了 d 这条语句执行。对比图 11.1 的逻辑，由于 d 这条语句在 out 的前面，虽然使用了阻塞赋值但是相当于 out=a+b+c。

```verilog
reg [1:0] d;
always@(posedge Clk or negedge Rst_n)begin
    if(!Rst_n)
        out = 2'b0;
    else begin
        out = d + c;
        d = a + b;
    end
end
```

<img src="/images/post_images/fpga_10_block_assignment/fpga_10_block_assignment_02.png">



**注：由上可以看出，阻塞赋值语句之间的阻塞会导致综合生成的电路不同，逻辑不同**



### 非阻塞赋值实现方式

现在把赋值方式改为非阻塞赋值，进行综合后可以看到如图所示的逻辑电路。

```verilog
reg [1:0] d;
always@(posedge Clk or negedge Rst_n)begin
    if(!Rst_n)
        out <= 2'b0;
    else begin
        d <= a + b;
        out <= d + c;
    end
end
```

<img src="/images/post_images/fpga_10_block_assignment/fpga_10_block_assignment_03.png">

现在使用非阻塞方式，再次交换语句执行顺序，综合后实现的逻辑电路如图所示。这里由于采用的非阻塞赋值，因此交换语句的前后顺序并不会对最终生成的逻辑电路有实际影响。

```verilog
reg [1:0] d;
always@(posedge Clk or negedge Rst_n)begin
    if(!Rst_n)
        out <= 2'b0;
    else begin
        out <= d + c;
        d <= a + b;
    end
end
```

<img src="/images/post_images/fpga_10_block_assignment/fpga_10_block_assignment_04.png">



### 总结

对比 Verilog 语法中阻塞赋值和非阻塞赋值的区别。通过证明非阻塞赋值多种赋值顺序生产电路的唯一性，与阻塞赋值多种赋值顺序生成电路的不确定性，来展示使用非阻塞赋值对设计可预测性的重要意义。在今后的设计中会经常用到两种赋值方式，请多加思考其中区别及意义。
掌握可综合风格的 Verilog 模块编程的六个原则，可以为解决在综合后仿真中出现绝大多数的冒险竞争问题。

- 时序电路建模时，用非阻塞赋值；
- 锁存器电路建模时，用非阻塞赋值；
-  用 always 块建立组合逻辑模型时，用阻塞赋值；
- 在同一个 always 块中建立时序和组合逻辑电路时，用非阻塞赋值；
- 在同一个 always 块中不要既用非阻塞赋值又用阻塞赋值；
- 不要在一个以上的 always 块中为同一个变量赋值。



### 个人小结

阻塞赋值相当于用直线直接相连信号，信号瞬间输出，通过语句的先后顺序决定信号的执行顺序，阻塞赋值具有生成电路的不确定性

非阻塞赋值的信号，都是在敏感信号边沿触发，电路会先同意计算 <= 右边的值，当所有语句都计算完成时，在同一时刻将数据赋值给右边的变量，计算 <= 右边值得时候，所用的信号都是敏感信号边沿之前的数据，即上一时钟得到的数据。

​                 

​                   

[  完  ]