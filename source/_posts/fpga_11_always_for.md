---
title: always块中for循环
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: false
summary: 本文整理always块中for循环相关问题
categories: FPGA-详解
tags:
  - FPGA
  - for循环
abbrlink: 2807001703
date: 2020-05-24 10:10:09
layout:
password:
---

## always块中for循环相关问题整理

---

注 : 本文内容来自个人整理总结

### 组合逻辑 for 循环

组合逻辑的 for 循环，会组成庞大的组合逻辑电路，会将 for 中的各个情况全部综合出来，组合逻辑中的for循环，综合出来的是组合逻辑电路，运行速度快。

**举例：选出最大值**

```verilog
integer i;
always @ (*) begin
    for (i = 0; i <= 7; i = i + 1)
        begin
            if(data_r[i] > num)
                num = data_r[i];
        end  
    num_r = num;         
end
assign data_out = num_r;

```

运算结果会实时得出结果，运行速度快



### 时序逻辑 for 循环

#### 1 初始化数值

当对数组进行初始化为固定数值时，或者直接输出确定的数值时（数值与时序无关，只与当前的输入值有关），会综合成组合逻辑，当时钟沿到来时，组合逻辑运行，结果输出很快，不需要等下一个时钟沿，即可得到结果。

```verilog
//初始化赋值
module  always_for(
    //system signals
    input				clk ,
    input				rst_n ,
    output	reg	[7:0]	data_out
);
    integer i ;
    reg [7:0] r0 [255:0];
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)begin
            for (i = 0; i <= 50; i = i + 1)begin
                r0[i] <= 8'd0;
            end 
        end
        else begin
            for (i = 0; i <= 40; i = i + 1)begin
                data_out <= r0[i];
            end    
        end    
    end
endmodule

```

<img src="/images/post_images/fpga_11_always_for/fpga_11_always_for_01.png">

#### 2 时序逻辑 - 非阻塞赋值

使用 for 循环搭建时序逻辑，每到来一个时钟上升沿，for 循环会执行一步，以此类推 

```verilog
module  always_for(
    //system signals
    input				clk ,
    input				rst_n ,
    output	reg	[7:0]	data_out
);
    integer i;
    reg [7:0] num;
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            data_out <= 8'd0;
        else 
            for (i = 0; i <= 10; i = i + 1)
                data_out <= data_out + 1'b1;    
    end
endmodule

```

下图是仿真结果

<img src="/images/post_images/fpga_11_always_for/fpga_11_always_for_02.png">



由上图可得 for 循环中，当需要综合成时序逻辑时（赋值时会用到上一个时钟周期的结果），for 循环，每一个时钟只运行一步，而且会一直运行下去，此时应当注意逻辑是否依然正确

for 循环中的 i 只是作为 辅助综合的变量，当综合完成后，i 的值保持不变，逻辑当中的执行结果已经由综合后的变量代替。在综合 for 循环期间，会损耗大量逻辑单元，为了生成每一个 i 值对应的逻辑。



#### 3 时序逻辑 - 阻塞赋值

注意：时序逻辑时使用 非阻塞赋值，但是当使用阻塞赋值时，会出现下列情况

```verilog
module  always_for(
    //system signals
    input				clk ,
    input				rst_n ,
    output	reg	[7:0]	data_out
);
    integer i;
    reg [7:0] num;
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            data_out <= 8'd0;
        else 
            for (i = 0; i <= 10; i = i + 1)
                data_out = data_out + 1'b1;    
    end
endmodule
```

<img src="/images/post_images/fpga_11_always_for/fpga_11_always_for_03.png">

由于使用了阻塞赋值逻辑，for 循环会由组合逻辑执行结束，组合逻辑执行速度快，会在一瞬间完成整个 for 循环，因此仿真结果如上图所示



**注：**而且for循环如果没有控制的话将一直执行下去，



### 摘抄 - 总结

摘自：https://www.cnblogs.com/xd-elegant/p/4520920.html

在Verilog中除了在Testbench（仿真测试激励）中使用for循环语句外，在Testbench中for语句在生成激励信号等方面使用较普遍，但在RTL级编码中却很少使用for循环语句。主要原因就是for循环会被综合器展开为所有变量情况的执行语句，每个变量独立占用寄存器资源，每条执行语句并不能有效地复用硬件逻辑资源，造成巨大的资源浪费。简单的说就是：for语句循环几次，就是将相同的电路复制几次，因此循环次数越多，占用面积越大，综合就越慢。

在RTL硬件描述中，遇到类似的算法，推荐的方法是先搞清楚设计的时序要求，做一个reg型计数器。在每个时钟沿累加，并在每个时钟沿判断计数器情况，做相应的处理，能复用的处理模块尽量复用，即使所有的操作不能复用，也采用case语句展开处理。



可以参考：[关于always块内for循环的执行方式](https://www.cnblogs.com/shaonianpi/p/9432226.html)

​                      

[  完  ]

