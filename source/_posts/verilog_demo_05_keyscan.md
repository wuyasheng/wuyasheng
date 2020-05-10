---
title: Verilog Demo_5 按键扫描
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理按键扫描驱动方法
categories: ☸ FPGA
tags:
  - Verilog
  - 按键扫描
abbrlink: 1246795167
date: 2020-03-28 08:27:09
password:
---

## Verilog  按键扫描

---

注 : 本文内容来自整理摘抄至原文[参考文1](https://www.cnblogs.com/kongtiao/archive/2011/07/21/2112112.html)、[参考文2](https://www.cnblogs.com/nick123/archive/2009/05/07/1452129.html)、[参考文3](https://www.cnblogs.com/yuphone/archive/2010/07/23/1783623.html)

### 按键扫描 驱动控制方法

**原理**：4x4的矩阵键盘，主要由16个按键组成，电路图如图所示。组成4x4的阵列，由4+4 8个输出，确定按键的键值，主要方法如下，依次拉低TIER0-3，检测ROW0-3的点电平，当检测结果为低时，表示为由TIRE和ROW对应的键被按下，由此的得到键值的获取方法；

**方案**：

实现方法很简单，依次扫描TIRE（列），检测ROW的键值，即可实现键值的检测。

<img src="/images/post_images/verilog_demo_05_keyscan/keyscan.png">

### 按键扫描代码

```verilog
module key_scale(			//（当key_flag为高电平时,表示有键值输出）
    input				clk,  	//50MHZ
    input				rst_n,
    input		[	3:0]	row,   	//行
    output	reg	[	3:0]	col,   	//列
    output	reg	[	3:0]	key_value,  //键值
    output	reg 			key_flag   	//按键标志位
);
    localparam	[	19:0]	COUNTER_MAX = 20'd500000;
    //localparam	[	19:0]	COUNTER_MAX = 20'd5;
    reg [19:0] count;	//delay_20ms
    reg [1:0] 	state;  //状态标志
    reg 	clk_50Hz;  	//500KHZ时钟信号

    always @(posedge clk or negedge rst_n)begin
        if(!rst_n) begin 
            clk_50Hz 	<= 1'b0; 
            count 		<= 20'd0; 
        end
        else begin
            if(count == COUNTER_MAX - 1'd1) begin 
                clk_50Hz <= ~clk_50Hz;
                count    <= 20'd0;
            end
            else 
                count   <= count + 1'b1;
        end
    end
    always@(posedge clk_50Hz or negedge rst_n)begin
        if(!rst_n)
            state <= 2'd0;
        else begin
            if(state == 2'b11)
                state	<= 2'b00;
            else
                state 	<= state + 1'b1;
        end
    end
    always@(posedge	clk_50Hz or negedge rst_n)begin
        if(!rst_n)
            col    <= 4'b1111;
        else begin
            case(state)
                2'b00	: col	<= 4'b0111;
                2'b01	: col	<= 4'b1011;
                2'b10	: col	<= 4'b1101;
                2'b11	: col	<= 4'b1110;
            endcase
        end
    end
    always@(posedge clk_50Hz or negedge rst_n)begin
        if(!rst_n)begin
            key_flag	<= 1'b0;
            key_value	<= 4'b0000;
        end
        else begin
            case({col,row})
                8'b0111_0111	:begin	key_flag <= 1'b1;key_value <= 4'd0;  end
                8'b0111_1011	:begin	key_flag <= 1'b1;key_value <= 4'd4;  end
                8'b0111_1101	:begin	key_flag <= 1'b1;key_value <= 4'd8;  end
                8'b0111_1110	:begin	key_flag <= 1'b1;key_value <= 4'd12; end			
                8'b1011_0111	:begin	key_flag <= 1'b1;key_value <= 4'd1;  end
                8'b1011_1011	:begin	key_flag <= 1'b1;key_value <= 4'd5;  end
                8'b1011_1101	:begin	key_flag <= 1'b1;key_value <= 4'd9;  end
                8'b1011_1110	:begin	key_flag <= 1'b1;key_value <= 4'd13; end			
                8'b1101_0111	:begin	key_flag <= 1'b1;key_value <= 4'd2;  end
                8'b1101_1011	:begin	key_flag <= 1'b1;key_value <= 4'd6;  end
                8'b1101_1101	:begin	key_flag <= 1'b1;key_value <= 4'd10; end
                8'b1101_1110	:begin	key_flag <= 1'b1;key_value <= 4'd14; end			
                8'b1110_0111	:begin	key_flag <= 1'b1;key_value <= 4'd3;  end
                8'b1110_1011	:begin	key_flag <= 1'b1;key_value <= 4'd7;  end
                8'b1110_1101	:begin	key_flag <= 1'b1;key_value <= 4'd11; end
                8'b1110_1110	:begin	key_flag <= 1'b1;key_value <= 4'd15; end
                default		:begin	key_flag <= 1'b0;end
            endcase
        end
    end
endmodule
```



[  完  ]





