---
title: Verilog Demo_2 边沿检测
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理边沿检测方法
categories: ☸ FPGA
tags:
  - Verilog
  - 边沿检测
abbrlink: 567716388
date: 2020-03-27 19:27:09
password:
---

## Verilog  边沿检测

---

注 : 本文内容来自整理摘抄至原文[参考文1](https://blog.csdn.net/lg2lh/article/details/8104551)、[参考文2](https://www.cnblogs.com/wangyabin121/p/5638413.html)、[参考文3](https://www.cnblogs.com/xiaoxie2014/p/4157835.html)

### 边沿检测的基本方法

脉冲边沿的特性：两侧的电平发生了变化

<img src="/images/post_images/verilog_demo_02_edge_cap/clipboard.png">

思路：设计两个或多个一位的寄存器，用来接收被检测的信号，系统时钟来一次记一次输入信号，如果用了两个寄存器直接异或就可以了。

​		如果不为0，则发生了边沿，再拼接，拼接顺序假定是{先进reg，后进reg}，则若先进reg=1，后进位0，则是下降沿，即{先进reg，后进reg}=2’b10。

​		同理相反{先进reg，后进reg}=2’b01，则为上升沿。如果用了多个寄存器则可以更好地检测，防止干扰脉冲。

注：在异步信号边沿检测电路中，至少需要采用三级寄存器来实现，在对系统稳定性要求较高的数字系统中，可以采用更多级的寄存器来减小亚稳态发生概率，提高系统稳定性。

### 方法1  (可能存在亚稳态的状态)

```verilog
module edge_cap(
    input    clk, 
    input    rst_n,
    input    pulse,
    output   pos_edge,
    output   neg_edge   
);
    //定义两个寄存器，移位寄存输入数据
    reg pulse_r1, pulse_r2;
    //移位寄存器定义
    always @ (posedge clk or negedge rst_n)begin
        if(!rst_n) 
            begin
            pulse_r1 <= 1'b0;
            pulse_r2 <= 1'b0;
        end
        else begin
            pulse_r1 <= pulse;
            pulse_r2 <= pulse_r1;
        end
    end
    //判断边沿条件
    assign pos_edge = (pulse_r1 && ~pulse_r2) ?1:0;
    assign neg_edge = (~pulse_r1 && pulse_r2) ?1:0; 
endmodule
```

### 方法2  (改进1-暂存亚稳态)

```verilog
module edge_cap(
    input    clk, 
    input    rst_n,
    input    pulse,
    output   pos_edge,
    output   neg_edge
);
    //定义三个寄存器，移位寄存输入数据
    reg pulse_r1, pulse_r2, pulse_r3;
    //移位寄存器定义
    always @ (posedge clk or negedge rst_n)begin
        if(!rst_n) begin
            pulse_r1 <= 1'b0;
            pulse_r2 <= 1'b0;
            pulse_r3 <= 1'b0;
        end
        else begin
            pulse_r1 <= pulse;
            pulse_r2 <= pulse_r1;
            pulse_r3 <= pulse_r2;
        end
    end
    //判断边沿条件
    assign pos_edge = (pulse_r2 && ~pulse_r3) ?1:0;
    assign neg_edge = (~pulse_r2 && pulse_r3) ?1:0; 
endmodule
```

### 方法3  (特权同学-过滤毛刺)

```verilog
module edge_cap(
    input    clk, 
    input    rst_n,
    input    pulse,
    output   pos_edge,
    output   neg_edge
);
    //定义三个寄存器，移位寄存输入数据
    reg pulse_r0,pulse_r1,pulse_r2,pulse_r3;
    //移位寄存器定义
    always @ (posedge clk or negedge rst_n) begin
        if(!rst_n) begin
            pulse_r0<= 1'b0;
            pulse_r1<= 1'b0;
            pulse_r2<= 1'b0;
            pulse_r3<= 1'b0;
        end
        else begin
            pulse_r0<= pulse;
            pulse_r1<= pulse_r0;
            pulse_r2<= pulse_r1;
            pulse_r3<= pulse_r2;
        end
    end
    //判断边沿条件
    //这种方法可以滤除20-40ns的毛刺
    assign neg_edge = pulse_r3 & pulse_r2 & ~pulse_r1 & ~pulse_r0;
    assign pos_edge = ~pulse_r3 & ~pulse_r2 & pulse_r1 & pulse_r0;
endmodule
```



[  完  ]