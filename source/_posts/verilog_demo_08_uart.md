---
title: Verilog Demo_8 UART
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 UART驱动控制方法
categories: FPGA-例程
tags:
  - Verilog
  - UART
abbrlink: 2565030233
date: 2020-03-28 11:27:09
password:
---

## Verilog  UART驱动

---

注 : 本文内容来自整理摘抄至原文 [参考文1](https://blog.csdn.net/u012135070/article/details/79610218)、以及明德扬的大道至简书，计数器法部分章节

### UART驱动原理

**原理：**UART使用的是 **异步**，**串行通信**。用于实现设备之间低速数据通信的标准协议。因发送时不需同时发送时钟，故此协议为异步。UART波特率典型为38400，9600，115200等 。

异步通信以一个字符为传输单位，通信中两个字符间的时间间隔多少是不固定的，然而在同一个字符中的两个相邻位间的时间间隔是固定的。 

数据传送速率用**波特率**来表示，即每秒钟传送的二进制位数。例如数据传送速率为120字符/秒，而每一个字符为10位（1个起始位，7个数据位，1个校验位，1个结束位），则其传送的波特率为10×120＝1200字符/秒＝1200波特。

UART字符格式为1个起始位，5~8个数据位，1个地址位或奇偶位(可选)，1个停止位。

先发低位再发高位，由低到高依次发送

<img src="/images/post_images/verilog_demo_08_uart/uart.png">

### 代码1（串口发送）

```verilog
module uart_tx(
    input       clk     ,
    input       rst_n   ,
    input       tx_en   ,
    input [7:0] data_in ,
    output reg  tx_done ,
    output reg  tx
);
    //设置波特率----100M的时钟下
    localparam  BAND_COUNTER = 10417;   //波特率9600
    //localparam  BAND_COUNTER = 5208;  //波特率19200
    //localparam  BAND_COUNTER = 2604;  //波特率38400
    //localparam  BAND_COUNTER = 1736;  //波特率57600
    //localparam  BAND_COUNTER = 868;   //波特率115200

    wire                tx_start;    
    wire                tx_end;
    //计数器参数定义
    reg     [   15: 0]  cnt0;      
    reg     [    3: 0]  cnt1;
    wire                add_cnt0;
    wire                add_cnt1;
    wire                end_cnt0;
    wire                end_cnt1;
    reg                 flag;
    reg     [    9: 0]  data_in_reg;
    //定义传输开始及结束标志位
    assign  tx_start    = tx_en && !flag;
    assign  tx_end      = add_cnt1 && cnt1 == 4'd9;
    //标志位的写法
    always@(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            flag    <= 1'b0;
        end
        else if(tx_start)begin
            flag    <= 1'b1;
        end
        else if(tx_end)begin
            flag    <= 1'b0;
        end
    end
    //输入需要发送的值
    always  @(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            data_in_reg     <= 10'd1;
        end
        else if(tx_start)begin
            data_in_reg     <= {1'b1,data_in,1'b0};
        end
        else
            data_in_reg     <= data_in_reg;
    end
    //计数发送
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
    assign add_cnt0 = flag ==1'b1;
    assign end_cnt0 = add_cnt0 && cnt0== BAND_COUNTER - 1;
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
    assign end_cnt1 = add_cnt1 && cnt1== 9;
    //按时序进行输出
    always  @(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            tx  <= 1'b1;
        end
        else if(cnt0 == 15'd0 && add_cnt0)begin
            tx  <= data_in_reg[cnt1];
        end
        else
            tx  <= tx;
    end
    //发送结束标志输出
    always  @(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            tx_done <= 1'b0;
        end
        else if(end_cnt1)begin
            tx_done <= 1'b1;
        end
        else
            tx_done <= 1'b0;
    end
endmodule
```

### 代码2（串口接收）

```verilog
module uart_rx(
    input               clk     ,
    input               rst_n   ,
    input               rx      ,
    output  reg [7:0]   data_out,
    output  reg         rx_done 
);
    //设置波特率----100M的时钟下;
    localparam  BAND_COUNTER = 10417;//波特率9600
    //localparam  BAND_COUNTER = 5208;//波特率19200
    //localparam  BAND_COUNTER = 2604;//波特率38400
    //localparam  BAND_COUNTER = 1736;//波特率57600
    //localparam  BAND_COUNTER = 868;//波特率115200
    //参数定义
    reg    flag;
    reg     [15:0]  cnt0;
    reg     [3:0]   cnt1;
    wire    add_cnt0;
    wire    end_cnt0;
    wire    add_cnt1;
    wire    end_cnt1;
    wire    rx_start;
    wire    rx_end;
    assign  rx_start    = !rx && !flag;
    assign  rx_end      = end_cnt1;
    //接收开始标志位产生
    always@(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            flag    <= 1'b0;
        end
        else if(rx_start)begin
            flag    <= 1'b1;
        end
        else if(rx_end)begin
            flag    <= 1'b0;
        end
        else
            flag    <= flag;
    end
    //计数其部分
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
    assign add_cnt0 = flag == 1'b1;
    assign end_cnt0 = add_cnt0 && cnt0== BAND_COUNTER - 1;
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
    assign end_cnt1 = add_cnt1 && cnt1== 4'd9;
    //记录对应位的值
    always  @(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            data_out    <= 8'd0;
        end
        else if(cnt1 >= 4'd1 && cnt1 < 4'd9 && add_cnt0 && cnt0 == BAND_COUNTER/2 - 1)begin
            data_out[cnt1 - 1]    <= rx;
        end
        else
            data_out[cnt1 - 1]    <= data_out[cnt1 - 1];
    end
    //输出的标志位
    always  @(posedge clk or negedge rst_n)begin
        if(rst_n==1'b0)begin
            rx_done <= 1'b0;
        end
        else if(end_cnt1)begin
            rx_done <= 1'b1;
        end
        else
            rx_done <= 1'b0;
    end
endmodule
```



[  完  ]



