---
title: Verilog Demo_6 步进驱动
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 步进电机控制方法
categories: FPGA-例程
tags:
  - Verilog
  - 步进电机
abbrlink: 3094755046
date: 2020-03-28 09:27:09
password:
---

## Verilog  步进电机

---

注 : 本文内容来自整理摘抄至原文 [参考文1](https://www.cnblogs.com/kongtiao/archive/2011/08/16/2140528.html)

### 步进电机驱动控制方法

1、步进电机概述

　　步进电机是一种能够将电脉冲信号转换成角位移或线位移的机电元件，它实际上是一种单相或多相同步电动机。单相步进电动机有单路电脉冲驱动，输出功率一般很小，其用途为微小功率驱动。多相步进电动机有多相方波脉冲驱动，用途很广。使用多相步进电动机时，单路电脉冲信号可先通过脉冲分配器转换为多相脉冲信号，在经功率放大后分别送入步进电动机各相绕组。每输入一个脉冲到脉冲分配器，电动机各相的通电状态就发生变化，转子会转过一定的角度（称为步距角）。正常情况下，步进电机转过的总角度和输入的脉冲数成正比；连续输入一定频率的脉冲时，电动机的转速与输入脉冲的频率保持严格的对应关系，不受电压波动和负载变化的影响。由于步进电动机能直接接收数字量的输入，所以特别适合于微机控制。

2、步进电机的种类

　　目前常用的步进电机有三类：

　　（1）反应式步进电动机（VR）：它的结构简单，生产成本低，步距角可以做的相当小，但动态性能相对较差。

　　（2）永磁式步进电动机（PM）：它的出力大，动态性能好；但步距角一般比较大。

　　（3）混合步进电动机（HB）：它综合了反应式和永磁式两者的优点，步距角小，出力大，动态性能好，是性能较好的一类步进电动机。

　　如果还想做更深入的了解，自行查找相关资料。

3、步进电机控制的实现

　　我们实验中所使用的步进电机为四相步进电机。转子小齿数为64。系统中采用四路I/O进行并行控制，FPGA直接发出多相脉冲信号，在通过功率放大后，进入步进电机的各相绕组。这样就不再需要脉冲分配器。脉冲分配器的功能可以由纯软件的方法实现。

<img src="/images/post_images/verilog_demo_06_step_motor/step_motor_1.png">

四相步距电机的控制方法有四相单四拍，四相单、双八拍和四相双四拍三种控制方式。步距角的计算公式为：

<img src="/images/post_images/verilog_demo_06_step_motor/step_motor_2.png">

其中：m为相数，控制方法是四相单四拍和四相双四拍时C为1，控制方法是四相单、双八拍时C为2，Zk为转子小齿数。本系统中采用的是四相单、双八拍控制方法，所以步距角为360°/512。但步进电机经过一个1/8的减速器引出，实际的步距角应为360°/512/8。

　　试验中使用EXI/O的高四位控制四相步进电机的四个相。按照四相单、双八拍控制方法，电机正转时的控制顺序为A→AB→B→BC→C→CD→D→DA。EXI/O的高四位的值参见表1.1-正转的时序。

<img src="/images/post_images/verilog_demo_06_step_motor/step_motor_3.png">

反转时，只要将控制信号按相反的顺序给出即可。

　　步进电机的频率不能太快，也不能太慢。在200Hz附近最好。频率太快是转动不起来的。

注：为什么步进电机高于一定速度就无法启动：

　　步进电机有一个技术参数：空载启动频率，即步进电机在空载情况下能够正常启动的脉冲频率，如果脉冲频率高于该值，电机不能正常启动，可能发生丢步或堵转。在有负载的情况下，启动频率应更低。如果要使电机达到高速转动，脉冲频率应该有加速过程，即启动频率较低，然后按一定加速度升到所希望的高频（电机转速从低速升到高速）。

### 代码（）

```verilog
module step_motor(
    input				clk,
    input				rst_n,
    input				dir,
    input				StepEnable, 
    output	reg [3:0]	StepDrive
);
    //50MHz分频250Hz
    localparam	[19:0]	COUNT_MAX = 20'd200000;

    reg		[19:0]	cnt;
    reg		[2:0] 	state; 
    wire	add_cnt,end_cnt;	

    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            cnt <= 20'd0;
        end
        else if(add_cnt)begin
            if(end_cnt)
                cnt <= 20'd0;
            else
                cnt <= cnt + 1'b1;
        end
        else
            cnt	<= 20'd0;
    end

    assign add_cnt = StepEnable == 1'b1;       
    assign end_cnt = add_cnt && cnt== COUNT_MAX - 1'b1;   
    //状态切换
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            state	<= 3'd0;
        else if(end_cnt)
            if(dir)
                state	<= state + 1'b1;
        else
            state	<= state - 1'b1;
        else
            state	<= state;
    end	

    always@(posedge clk)begin
        if(StepEnable)begin
            case(state)
                3'b000 :    StepDrive <= 4'b0001 ; 
                3'b001 :    StepDrive <= 4'b0011 ; 
                3'b010 :    StepDrive <= 4'b0010 ; 
                3'b011 :    StepDrive <= 4'b0110 ; 
                3'b100 :    StepDrive <= 4'b0100 ; 
                3'b101 :    StepDrive <= 4'b1100 ; 
                3'b110 :    StepDrive <= 4'b1000 ; 
                3'b111 :    StepDrive <= 4'b1001 ;  
                default:	StepDrive <= 4'b0000 ; 
            endcase
        end
        else
            StepDrive	<= StepDrive;
    end	
endmodule
```



[  完  ]