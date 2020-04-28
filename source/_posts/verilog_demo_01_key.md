---
title: Verilog Demo_1 按键消抖
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理按键消抖方法
categories: ☸ FPGA
tags:
  - Verilog
  - 按键消抖
abbrlink: 3675679110
date: 2020-03-27 17:27:09
password:
---

## Verilog  按键消抖

---

注 : 本文内容来自整理摘抄至原文[自由的青](https://www.cnblogs.com/qingkai/p/7596126.html)、[触发器消抖](https://www.cnblogs.com/nucfrank/archive/2011/09/29/2196032.html)

### 按键消抖方法概述

1. 在按键电平稳定的情况下，当第一次检测到键位电平变化，开始20ms计时，计时时间到后将按键电平更新为当前电平。
2. 在20ms计时的过程中，有任何的电平变化都立即复位计时
3. 在有电平变化时立即改变按键输出电平，并开始20ms计时，忽略这其中抖动
4. 触发器消抖

### 按键消抖方法（1）

```verilog
module key1(
    input           clk, 
    input           rst_n,
    input           key_in,
    output   reg    key_out
);
    // 当clk为50MHz时的计数时间
    localparam [19:0] TIME_20MS = 1_000_000;
    //变量定义
    reg [19:0] cnt;
    reg key_cnt;
    // 当消抖时间到20ms时，按键状态发生变化
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            key_out <= 1'b0;
        else if(cnt == TIME_20MS - 1'b1)
            key_out <= key_in;
    end
    //确定状态发生变化，提供20ms的标志位key_cnt；
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            key_cnt <= 1'b0;
        else if(key_cnt == 1'b0 && key_in != key_out)
            key_cnt <= 1'b1;
        else if(cnt == TIME_20MS - 1'b1)
            key_cnt <= 1'b0;
    end
    //当按键状态发生变化时开始计数
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            cnt <= 20'd0;
        else if(key_cnt)
            cnt <= cnt + 1'b1;
        else
            cnt <= 20'd0; 
    end  
endmodule
```

### 按键消抖方法（2）

```verilog
module key2(
    input           clk, 
    input           rst_n,
    input           key_in,
    output   reg    key_out
);
    // 当clk为50MHz时的计数时间
    localparam TIME_20MS = 1_000_000;
    //变量定义
    reg key_cnt;
    reg [19:0] cnt;
    // 当消抖时间到20ms时，按键状态发生变化
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            key_out <= 1'b1;
        else if(cnt == TIME_20MS - 1'b1)
            key_out <= key_in;
    end
    //确定状态发生变化，提供20ms的标志位key_cnt；
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            key_cnt <= 1'b0;
        else if(cnt == TIME_20MS - 1'b1)
            key_cnt <= 1'b0;
        else if(key_cnt == 1'b0 && key_out != key_in)
            key_cnt <= 1'b1;
    end
    //判断按键的状态发生变化时开始计数，状态变化时，计数器不断清零
    always @(posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            cnt <= 20'd0;
        else if(key_cnt) begin
            if(key_out == key_in)
                cnt <= 20'd0;
            else
                cnt <= cnt + 1'b1;
        end
        else
            cnt <= 20'd0;
    end
endmodule
```

### 按键消抖方法（3）

```verilog
module key3(
    input           clk, 
    input           rst_n,
    input           key_in,
    output   reg    key_out
    );
    // 当clk为50MHz时的计数时间
    localparam TIME_20MS = 1_000_000;
    //变量定义
    reg key_cnt;
    reg [19:0] cnt;
    // 按键状态发生变化，输出立即改变
	always @(posedge clk or negedge rst_n) begin
		if(rst_n == 1'b0)
            key_out <= 1'b1;
        else if(key_cnt == 1'b0 && key_out != key_in)
            key_out <= key_in;
    end
	//确定状态发生变化，提供20ms的标志位key_cnt；
    always @(posedge clk or negedge rst_n) begin
		if(rst_n == 1'b0)
            key_cnt <= 1'b0;
        else if(key_cnt == 1'b0 && key_out != key_in)
            key_cnt <= 1'b1;
        else if(cnt == TIME_20MS - 1'b1)
            key_cnt <= 1'b0;
    end
    //当按键状态发生变化时开始计数
    always @(posedge clk or negedge rst_n) begin
		if(rst_n == 1'b0)
            cnt <= 20'd0;
        else if(key_cnt)
            cnt <= cnt + 1'b1;
        else
            cnt <= 20'd0;
    end
endmodule
```

### 按键消抖方法（4）

```verilog
module key4(
    input           clk, 
    input           rst_n,
    input           key_in,
    output          key_out
	);
    reg    key_dly1　　;
    reg    key_dly2　　;
    reg    key_dly3　　;
    always @(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            key_dly1 <= 1'b0;
            key_dly2 <= 1'b0;
            key_dly3 <= 1'b0;
        end
        else begin
            key_dly1 <= key_in  ;
            key_dly2 <= key_dly1;
            key_dly3 <= key_dly2;            
        end        
    end
    assign    key_out = key_dly1 | key_dly2 | key_dly3;
    
endmodule
```



[  完  ]