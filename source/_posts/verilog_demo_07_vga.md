---
title: Verilog Demo_7 VGA
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 VGA驱动控制方法
categories: FPGA-例程
tags:
  - Verilog
  - VGA
abbrlink: 1828281056
date: 2020-03-28 10:27:09
password:
---

## Verilog  VGA驱动

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
module vga_driver(
    input           vga_clk,      //VGA驱动时钟
    input           rst_n,    //复位信号
    //VGA接口                          
    output          vga_hs,       //行同步信号
    output          vga_vs,       //场同步信号
    output  [15:0]  vga_rgb,      //红绿蓝三原色输出

    input   [15:0]  pixel_data,   //像素点数据
    output          data_req  ,   //请求像素点颜色数据输入 
    output  [10:0]  pixel_xpos,   //像素点横坐标
    output  [10:0]  pixel_ypos    //像素点纵坐标    
    );                             

//parameter define  
/*
//640*480 60FPS_25MHz
parameter  H_SYNC   =  10'd96;    //行同步
parameter  H_BACK   =  10'd48;    //行显示后沿
parameter  H_DISP   =  10'd640;   //行有效数据
parameter  H_FRONT  =  10'd16;    //行显示前沿
parameter  H_TOTAL  =  10'd800;   //行扫描周期

parameter  V_SYNC   =  10'd2;     //场同步
parameter  V_BACK   =  10'd33;    //场显示后沿
parameter  V_DISP   =  10'd480;   //场有效数据
parameter  V_FRONT  =  10'd10;    //场显示前沿
parameter  V_TOTAL  =  10'd525;   //场扫描周期
*/

//1024*768 60FPS_65MHz
parameter  H_SYNC   =  11'd136;   //行同步     
parameter  H_BACK   =  11'd160;   //行显示后沿
parameter  H_DISP   =  11'd1024;  //行有效数据
parameter  H_FRONT  =  11'd24;    //行显示前沿
parameter  H_TOTAL  =  11'd1344;  //行扫描周期  注意位宽长度,需要11位的位宽

parameter  V_SYNC   =  11'd6;     //场同步
parameter  V_BACK   =  11'd29;    //场显示后沿
parameter  V_DISP   =  11'd768;   //场有效数据
parameter  V_FRONT  =  11'd3;     //场显示前沿
parameter  V_TOTAL  =  11'd806;   //场扫描周期

//reg define                                     
reg  [10:0] cnt_h;               
reg  [10:0] cnt_v;

//wire define
wire       vga_en;

//*****************************************************
//**                    main code
//*****************************************************
//VGA行场同步信号
assign vga_hs  = (cnt_h <= H_SYNC - 1'b1) ? 1'b0 : 1'b1;
assign vga_vs  = (cnt_v <= V_SYNC - 1'b1) ? 1'b0 : 1'b1;

//使能RGB565数据输出
assign vga_en  = (((cnt_h >= H_SYNC+H_BACK) && (cnt_h < H_SYNC+H_BACK+H_DISP))
                 &&((cnt_v >= V_SYNC+V_BACK) && (cnt_v < V_SYNC+V_BACK+V_DISP)))
                 ?  1'b1 : 1'b0;
                 
//RGB565数据输出                 
assign vga_rgb = vga_en ? pixel_data : 16'd0;

//请求像素点颜色数据输入                
assign data_req = (((cnt_h >= H_SYNC+H_BACK-1'b1) && (cnt_h < H_SYNC+H_BACK+H_DISP-1'b1))
                  && ((cnt_v >= V_SYNC+V_BACK) && (cnt_v < V_SYNC+V_BACK+V_DISP)))
                  ?  1'b1 : 1'b0;

//像素点坐标                
assign pixel_xpos = data_req ? (cnt_h - (H_SYNC + H_BACK - 1'b1)) : 10'd0;
assign pixel_ypos = data_req ? (cnt_v - (V_SYNC + V_BACK - 1'b1)) : 10'd0;

//行计数器对像素时钟计数
always @(posedge vga_clk or negedge rst_n) begin         
    if (!rst_n)
        cnt_h <= 10'd0;                                  
    else begin
        if(cnt_h < H_TOTAL - 1'b1)                                               
            cnt_h <= cnt_h + 1'b1;                               
        else 
            cnt_h <= 10'd0;  
    end
end

//场计数器对行计数
always @(posedge vga_clk or negedge rst_n) begin         
    if (!rst_n)
        cnt_v <= 10'd0;                                  
    else if(cnt_h == H_TOTAL - 1'b1) begin
        if(cnt_v < V_TOTAL - 1'b1)                                               
            cnt_v <= cnt_v + 1'b1;                               
        else 
            cnt_v <= 10'd0;  
    end
end

endmodule 
```



[  完  ]



