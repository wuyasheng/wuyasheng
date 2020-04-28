---
title: Verilog Demo_7 VGA
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理 VGA驱动控制方法
categories: ☸ FPGA
tags:
  - Verilog
  - VGA
abbrlink: 1828281056
date: 2020-03-28 10:27:09
password:
---

## Verilog  VGA驱动控制

---

注 : 本文内容来自整理摘抄至原文 [参考文1](https://www.cnblogs.com/mingmingruyue99/p/7201998.html)、[参考文2](https://blog.csdn.net/u013793399/article/details/51319235)、以及明德扬的大道至简书，计数器法部分章节

### VGA驱动原理

**原理：**VGA驱动显示器用的是**扫描的方式**，一般是逐行扫描。 

逐行扫描是扫描从屏幕左上角一点开始，从左像右逐点扫描，每扫描完一行,电子束回到屏幕的左边下一行的起始位置，在这期间，CRT对电子束进行消隐，每行结束时，用行同步信号进行同步； 

当扫描完所有的行，形成一帧后，用场同步信号进行场同步，并使扫描回到屏幕左上方，同时进行场消隐,开始下一帧。 

<img src="/images/post_images/verilog_demo_07_vga/vga_1.png">

VGA接口的驱动（简单的D/A转换）（需要进行阻抗匹配）

<img src="/images/post_images/verilog_demo_07_vga/vga_2.png">

以下是vs 和 hs的时序简图。 

<img src="/images/post_images/verilog_demo_07_vga/vga_3.png">

VGA各个标准在时间段的参数

<img src="/images/post_images/verilog_demo_07_vga/vga_4.png">

**方案：**

分别使用两个计数器记录行数与列数，根据时序要求在对应的计数时间段内，对时序进行相应的操作，以实现对图像的输出。

### 代码（简单的驱动-未加入地址的输出）

```verilog
module VGA(		//（可以加上对地址的输出，从而从其他的模块获取像素点的颜色，再进行输出）
    input           clk     ,
    input           rst_n   ,
    input   [23:0]  data_in ,
    output          hs      ,
    output          vs      ,
    output  [23:0]  data_out,
    output  [9:0]   pixel_x ,
    output  [9:0]   pixel_y  
);
    //参数定义
    parameter       H_SYNC  =   96;
    parameter       H_BACK  =   48;
    parameter       H_DISP  =   640;
    parameter       H_FRONT =   16;
    parameter       H_TOTAL =   800;

    parameter       V_SYNC  =   2;
    parameter       V_BACK  =   33;
    parameter       V_DISP  =   480;
    parameter       V_FRONT =   10;
    parameter       V_TOTAL =   525;

    reg [9:0]   cnt0,cnt1;
    wire        add_cnt0,end_cnt0;
    wire        add_cnt1,end_cnt1;
    wire        data_req;
    //对时钟进行计数
    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            cnt0 <= 0;
        end
        else if(add_cnt0)begin
            if(end_cnt0)
                cnt0 <= 0;
            else
                cnt0 <= cnt0 + 1;
        end
    end
    assign add_cnt0 = 1'b1;
    assign end_cnt0 = add_cnt0 && cnt0== H_TOTAL - 1;
    //对行周期进行计数
    always @(posedge clk or negedge rst_n)begin 
        if(!rst_n)begin
            cnt1 <= 0;
        end
        else if(add_cnt1)begin
            if(end_cnt1)
                cnt1 <= 0;
            else
                cnt1 <= cnt1 + 1;
        end
    end
    assign add_cnt1 = end_cnt0;
    assign end_cnt1 = add_cnt1 && cnt1== V_TOTAL - 1;

    assign  data_req=   (cnt0 >= H_SYNC + H_BACK) && 
        (cnt0 <  H_TOTAL - H_FRONT) &&
        (cnt1 >= V_SYNC + V_BACK) &&
        (cnt1 <  V_TOTAL - V_FRONT);
    assign  pixel_x = data_req ? cnt1 - V_SYNC - V_BACK : 10'd0;
    assign  pixel_y = data_req ? cnt0 - H_SYNC - H_BACK : 10'd0;

    assign  hs      = (cnt0 < H_SYNC) ? 1'b0 : 1'b1;
    assign  vs      = (cnt1 < V_SYNC) ? 1'b0 : 1'b1;

    assign  data_out = data_in;
endmodule
```



[  完  ]



