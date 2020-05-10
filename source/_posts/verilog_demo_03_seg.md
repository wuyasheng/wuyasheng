---
title: Verilog Demo_3 数码管
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理数码管显示驱动
categories: ☸ FPGA
tags:
  - Verilog
  - 数码管
abbrlink: 40981759
date: 2020-03-27 21:27:09
password:
---

## Verilog  数码管显示

---

注 : 本文内容来自整理摘抄至原文[参考文1](https://blog.csdn.net/lyqdy/article/details/50777569?locationNum=16&fps=1)、[参考文2](https://blog.csdn.net/weixin_39603637/article/details/80037183)、[参考文3](https://blog.csdn.net/li_qcxy/article/details/8305768)、[参考文4](https://www.cnblogs.com/kongtiao/archive/2011/07/23/2114618.html)

### 数码管驱动方法

**原理：**一般来说，多个数码管的连接并不是把每个数码管都独立的与可编程逻辑器件连接，而是把所有的LED管的输入连在一起。如图所示。数码管一般包括共阳极数码管以及共阴极数码管。两种数码管的驱动方式不同。

<img src="/images/post_images/verilog_demo_03_seg/seg.png">

**方案：**

​		这样做的好处有两点：一是节约了器件的IO口；其二是降低了功耗。每次向LED写数据时，通过片选选通其中一个LED，然后把数据写入该LED管，因此每个时刻只有一个LED管是亮的。为了能持续看见LED上面的显示内容，必须对LED管进行扫描，即依次并循环地点亮各个LED管。利用人眼的视觉暂停效应，在一定的扫描频率下，人眼就会看见好几个LED一起点亮。每个LED的功耗较大，如果所有的LED一起点亮，其功耗较大。利用扫描的方式，每个时刻只有LED管是亮的，可以大大的减少功耗。

　　扫描频率大小不许合适才能有很好的效果。如果太小，而每个LED开启的时间大于人眼的视觉暂停时间，那么会产生闪烁现象。而扫描频率太大，则会造成LED的频繁开启和关断，大大增加LED功耗（开启和关断的时刻功耗很大）。一般来说，稍描频率选在50Hz比较合适。

### 数码管驱动代码代码

```verilog
//四位七段数码管扫描显示（共阳极）
module seg(
    input			clk, 
    input			rst_n,
    input		[7:0]	d1, 
    input		[7:0]	d2, 
    input		[7:0]	d3, 
    input		[7:0]	d4, 
    output reg 	[7:0]	seg,
    output reg 	[3:0]	sel
);
    //扫描频率:50Hz
    parameter [19	:0]	update_interval = 20'd249999;
    //参数定义
    reg 	[7	:0] dat;
    reg 	[1	:0] cur_sel;
    reg		[19	:0] selcnt;
    //扫描计数，选择位
    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)
            selcnt	<= 20'd0;
        else if(selcnt == update_interval)
            selcnt	<= 20'd0;
        else
            selcnt	<= selcnt	+ 1'b1;
    end
    //选择位
    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)
            cur_sel	<= 2'b00;
        else if(selcnt == update_interval)
            cur_sel	<= cur_sel	+ 1'b1;
        else
            cur_sel	<= cur_sel;
    end
    //切换扫描位选线和数据
    always @(*)begin
        case (cur_sel)
            2'b00: begin dat = d1; sel = 4'b0111; end
            2'b01: begin dat = d2; sel = 4'b1011; end
            2'b10: begin dat = d3; sel = 4'b1101; end
            2'b11: begin dat = d4; sel = 4'b1110; end
        endcase
    end
    //更新段码
    always @(*)begin
        seg[0] = ~dat[7]; 		//dp
        case (dat[6:0])
            7'h00 	: seg[7:1] = 7'b0000001;	//0
            7'h01 	: seg[7:1] = 7'b1001111;	//1
            7'h02 	: seg[7:1] = 7'b0010010;	//2
            7'h03 	: seg[7:1] = 7'b0000110;	//3
            7'h04 	: seg[7:1] = 7'b1001100;	//4
            7'h05 	: seg[7:1] = 7'b0100100;	//5
            7'h06 	: seg[7:1] = 7'b0100000;	//6
            7'h07 	: seg[7:1] = 7'b0001111;	//7
            7'h08 	: seg[7:1] = 7'b0000000;	//8
            7'h09 	: seg[7:1] = 7'b0000100;	//9
            7'h30 	: seg[7:1] = 7'b0000001;	//'0'
            7'h31 	: seg[7:1] = 7'b1001111;	//'1'
            7'h32 	: seg[7:1] = 7'b0010010;	//'2'
            7'h33 	: seg[7:1] = 7'b0000110;	//'3'
            7'h34 	: seg[7:1] = 7'b1001100;	//'4'
            7'h35 	: seg[7:1] = 7'b0100100;	//'5'
            7'h36 	: seg[7:1] = 7'b0100000;	//'6'
            7'h37 	: seg[7:1] = 7'b0001111;	//'7'
            7'h38 	: seg[7:1] = 7'b0000000;	//'8'
            7'h39 	: seg[7:1] = 7'b0000100;	//'9'
            default : seg[7:1] = 7'b0110000; 	//E-rror
        endcase
    end
endmodule
```



[  完  ]



