---
title: Verilog Demo_10 SCCB
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 SCCB驱动控制方法
categories: FPGA-例程
tags:
  - Verilog
  - SCCB
abbrlink: 502546194
date: 2020-03-28 14:27:09
password:
---

## Verilog  SCCB驱动

---

注 : 本文内容来自整理摘抄至原文 [参考文1](https://www.cnblogs.com/xianyufpga/p/11338204.html)

### SCCB 驱动原理

SCCB（Serial Camera Control Bus，串行摄像头控制总线）是由OV（OmniVision的简称）公司定义和发展的三线式串行总线，该总线控制着摄像头大部分的功能，包括图像数据格式、分辨率以及图像处理参数等。结构框图如下所示：

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_1.png">

OV公司为了减少传感器引脚的封装，现在SCCB总线大多采用两线式接口总线。OV7725使用的是两线式接口总线，该接口总线包括SIO_C串行时钟输入线和SIO_D串行双向数据线，分别相当于IIC协议的SCL信号线和SDA信号线。可以为两线和三线，两线为（只控制一个从设备），三线（能够控制多个设备）

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_2.png">

SCCB就是改版的IIC

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_3.png">

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_4.png">

　“X”的意思是“don't care”，该位是由从机发出应答信号来响应主机表示当前ID Address、Sub-address和Write Data是否传输完成，但是从机有可能不发出应答信号，因此主机（此处指FPGA）可不用判断此处是否有应答，直接默认当前传输完成即可。“X”即IIC中的ACK应答位。

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_5.png">

数据手册中的SCCB读只写了上图的Phase3和Phase4，实际上它是和Phase1和Phase2联系在一起的。SCCB不支持连续读，Phase4的主机应答位必须为NA(no ack)，所以SCCB读其实就专指单次读，和IIC单次读几乎一样。

　　区别就一点：在IIC读传输协议中，写完寄存器地址后会有restart即重复开始的操作；而SCCB读传输协议中没有重复开始的概念，在写完寄存器地址后，需发起总线停止信号。

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_6.png">

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_7.png">

<img src="/images/post_images/verilog_demo_10_sccb/SCCB_8.png">

除去上面三点，SCCB和IIC再无区别，因此如果只需要配置寄存器（只用到写），可以直接拿IIC的时序来当做SCCB用，如果需要读，读操作中间必须有一个stop。

### 代码（sccb）

```verilog
//**************************************************************************
// *** 名称 : sccb.v
// *** 作者 : xianyu_FPGA
// *** 博客 : https://www.cnblogs.com/xianyufpga/
// *** 日期 : 2019-08-10
// *** 描述 : SCCB控制器，只支持写
//**************************************************************************
module sccb
    //========================< 参数 >==========================================
    #(
        parameter DEVICE_ID         = 8'b01010000           , //器件ID
        parameter CLK               = 26'd50_000_000        , //本模块的时钟频率
        parameter SCL               = 18'd250_000             //输出的SCL时钟频率
    )
    //========================< 端口 >==========================================
    (
        input                       clk                     , //时钟
        input                       rst_n                   , //复位，低电平有效
        //SCCB control -------------------------------------- 
        input                       sccb_en                 , //SCCB触发信号
        input                       addr16_en               , //16位地址使能
        input                       addr8_en                , //8位地址使能
        //SCCB input ---------------------------------------- 
        input        [15:0]         sccb_addr               , //SCCB器件内地址
        input        [ 7:0]         sccb_data               , //SCCB要写的数据
        //SCCB output --------------------------------------- 
        output  reg                 sccb_done               , //SCCB一次操作完成
        output  reg                 sccb_scl                , //SCCB的SCL时钟信号
        inout                       sccb_sda                , //SCCB的SDA数据信号
        //dri_clk ------------------------------------------- 
        output  reg                 sccb_dri_clk              //驱动SCCB操作的驱动时钟，1Mhz
    );
    //========================< 状态机参数 >====================================
    localparam  IDLE            = 6'b00_0001            ; //空闲状态
    localparam  DEVICE          = 6'b00_0010            ; //写器件地址
    localparam  ADDR_16         = 6'b00_0100            ; //写字地址高8位
    localparam  ADDR_8          = 6'b00_1000            ; //写字地址低8位
    localparam  DATA            = 6'b01_0000            ; //写数据
    localparam  STOP            = 6'b10_0000            ; //结束
    //========================< 信号 >==========================================
    reg                         sda_dir                 ; //SCCB数据(SDA)方向控制
    reg                         sda_out                 ; //SDA输出信号
    reg                         state_done              ; //状态结束
    reg    [ 6:0]               cnt                     ; //计数
    reg    [ 7:0]               state_c                 ; //状态机当前状态
    reg    [ 7:0]               state_n                 ; //状态机下一状态
    reg    [15:0]               sccb_addr_t             ; //地址寄存
    reg    [ 7:0]               sccb_data_t             ; //数据寄存
    reg    [ 9:0]               clk_cnt                 ; //分频时钟计数
    wire   [ 8:0]               clk_divide              ; //模块驱动时钟的分频系数

    //==========================================================================
    //==    sda控制
    //==========================================================================
    assign  sccb_sda = sda_dir ?  sda_out : 1'bz;         //SDA数据输出或高阻

    //==========================================================================
    //==     生成SCL的4倍时钟来驱动后面SCCB的操作，生成1Mhz的sccb_dri_clk
    //==========================================================================
    assign  clk_divide = (CLK/SCL) >> 3;                  // >>3即除以8

    always @(posedge clk or negedge rst_n) begin
        if(!rst_n) begin
            sccb_dri_clk <=  1'b1;
            clk_cnt <= 10'd0;
        end
        else if(clk_cnt == clk_divide - 1'd1) begin
            clk_cnt <= 10'd0;
            sccb_dri_clk <= ~sccb_dri_clk;
        end
        else
            clk_cnt <= clk_cnt + 1'b1;
    end

    //==========================================================================
    //==    状态机
    //==========================================================================
    always @(posedge sccb_dri_clk or negedge rst_n) begin
        if(!rst_n)
            state_c <= IDLE;
        else
            state_c <= state_n;
    end

    always @(*) begin
        case(state_c)
            IDLE: begin                             //空闲状态
                if(sccb_en)
                    state_n = DEVICE;
                else
                    state_n = IDLE;
            end
            DEVICE: begin                           //写器件ID
                if(state_done) begin
                    if(addr16_en)
                        state_n = ADDR_16;
                    else if(addr8_en)
                        state_n = ADDR_8 ;
                end
                else
                    state_n = DEVICE;
            end
            ADDR_16: begin                          //写地址高8位
                if(state_done)
                    state_n = ADDR_8;
                else
                    state_n = ADDR_16;
            end
            ADDR_8: begin                           //写地址低8位
                if(state_done)
                    state_n = DATA;
                else
                    state_n = ADDR_8;
            end
            DATA: begin                             //写数据
                if(state_done)
                    state_n = STOP;
                else
                    state_n = DATA;
            end
            STOP: begin                             //结束
                if(state_done)
                    state_n = IDLE;
                else
                    state_n = STOP ;
            end
            default:state_n= IDLE;
        endcase
    end

    //==========================================================================
    //==    设计各路信号
    //==========================================================================
    always @(posedge sccb_dri_clk or negedge rst_n) begin
        if(!rst_n) begin
            sccb_scl    <= 1'b1;
            sda_out     <= 1'b1;
            sda_dir     <= 1'b1;
            sccb_done   <= 1'b0;
            cnt         <= 1'b0;
            state_done  <= 1'b0;
            sccb_addr_t <= 1'b0;
            sccb_data_t <= 1'b0;
        end
        else begin
            state_done  <= 1'b0 ;
            cnt         <= cnt + 1'b1 ;
            case(state_c)
                //--------------------------------------------------- 空闲状态
                IDLE: begin
                    sccb_scl  <= 1'b1;
                    sda_out   <= 1'b1;
                    sda_dir   <= 1'b1;
                    sccb_done <= 1'b0;
                    cnt       <= 7'b0;
                    if(sccb_en) begin
                        sccb_addr_t <= sccb_addr;
                        sccb_data_t <= sccb_data;
                    end
                end
                //--------------------------------------------------- 写器件ID
                DEVICE: begin
                    case(cnt)
                        7'd1 : sda_out  <= 1'b0;
                        7'd3 : sccb_scl <= 1'b0;
                        7'd4 : sda_out  <= DEVICE_ID[7];
                        7'd5 : sccb_scl <= 1'b1;
                        7'd7 : sccb_scl <= 1'b0;
                        7'd8 : sda_out  <= DEVICE_ID[6];
                        7'd9 : sccb_scl <= 1'b1;
                        7'd11: sccb_scl <= 1'b0;
                        7'd12: sda_out  <= DEVICE_ID[5];
                        7'd13: sccb_scl <= 1'b1;
                        7'd15: sccb_scl <= 1'b0;
                        7'd16: sda_out  <= DEVICE_ID[4];
                        7'd17: sccb_scl <= 1'b1;
                        7'd19: sccb_scl <= 1'b0;
                        7'd20: sda_out  <= DEVICE_ID[3];
                        7'd21: sccb_scl <= 1'b1;
                        7'd23: sccb_scl <= 1'b0;
                        7'd24: sda_out  <= DEVICE_ID[2];
                        7'd25: sccb_scl <= 1'b1;
                        7'd27: sccb_scl <= 1'b0;
                        7'd28: sda_out  <= DEVICE_ID[1];
                        7'd29: sccb_scl <= 1'b1;
                        7'd31: sccb_scl <= 1'b0;
                        7'd32: sda_out  <= DEVICE_ID[0];
                        7'd33: sccb_scl <= 1'b1;
                        7'd35: sccb_scl <= 1'b0;
                        7'd36: begin
                            sda_dir <= 1'b0;    //从机应答
                            sda_out <= 1'b1;
                        end
                        7'd37: sccb_scl <= 1'b1;
                        7'd38: state_done <= 1'b1;  //状态结束
                        7'd39: begin
                            sccb_scl <= 1'b0;
                            cnt <= 1'b0;
                        end
                        default :  ;
                    endcase
                end
                //--------------------------------------------------- 写字地址高8位
                ADDR_16: begin
                    case(cnt)
                        7'd0 : begin
                            sda_dir <= 1'b1 ;
                            sda_out <= sccb_addr_t[15];
                        end
                        7'd1 : sccb_scl <= 1'b1;
                        7'd3 : sccb_scl <= 1'b0;
                        7'd4 : sda_out  <= sccb_addr_t[14];
                        7'd5 : sccb_scl <= 1'b1;
                        7'd7 : sccb_scl <= 1'b0;
                        7'd8 : sda_out  <= sccb_addr_t[13];
                        7'd9 : sccb_scl <= 1'b1;
                        7'd11: sccb_scl <= 1'b0;
                        7'd12: sda_out  <= sccb_addr_t[12];
                        7'd13: sccb_scl <= 1'b1;
                        7'd15: sccb_scl <= 1'b0;
                        7'd16: sda_out  <= sccb_addr_t[11];
                        7'd17: sccb_scl <= 1'b1;
                        7'd19: sccb_scl <= 1'b0;
                        7'd20: sda_out  <= sccb_addr_t[10];
                        7'd21: sccb_scl <= 1'b1;
                        7'd23: sccb_scl <= 1'b0;
                        7'd24: sda_out  <= sccb_addr_t[9];
                        7'd25: sccb_scl <= 1'b1;
                        7'd27: sccb_scl <= 1'b0;
                        7'd28: sda_out  <= sccb_addr_t[8];
                        7'd29: sccb_scl <= 1'b1;
                        7'd31: sccb_scl <= 1'b0;
                        7'd32: begin
                            sda_dir  <= 1'b0;    //从机应答
                            sda_out  <= 1'b1;
                        end
                        7'd33: sccb_scl <= 1'b1;
                        7'd34: state_done <= 1'b1;  //状态结束
                        7'd35: begin
                            sccb_scl <= 1'b0;
                            cnt <= 1'b0;
                        end
                        default :  ;
                    endcase
                end
                //--------------------------------------------------- 写字地址低8位
                ADDR_8: begin
                    case(cnt)
                        7'd0: begin
                            sda_dir <= 1'b1 ;
                            sda_out <= sccb_addr_t[7];
                        end
                        7'd1 : sccb_scl <= 1'b1;
                        7'd3 : sccb_scl <= 1'b0;
                        7'd4 : sda_out  <= sccb_addr_t[6];
                        7'd5 : sccb_scl <= 1'b1;
                        7'd7 : sccb_scl <= 1'b0;
                        7'd8 : sda_out  <= sccb_addr_t[5];
                        7'd9 : sccb_scl <= 1'b1;
                        7'd11: sccb_scl <= 1'b0;
                        7'd12: sda_out  <= sccb_addr_t[4];
                        7'd13: sccb_scl <= 1'b1;
                        7'd15: sccb_scl <= 1'b0;
                        7'd16: sda_out  <= sccb_addr_t[3];
                        7'd17: sccb_scl <= 1'b1;
                        7'd19: sccb_scl <= 1'b0;
                        7'd20: sda_out  <= sccb_addr_t[2];
                        7'd21: sccb_scl <= 1'b1;
                        7'd23: sccb_scl <= 1'b0;
                        7'd24: sda_out  <= sccb_addr_t[1];
                        7'd25: sccb_scl <= 1'b1;
                        7'd27: sccb_scl <= 1'b0;
                        7'd28: sda_out  <= sccb_addr_t[0];
                        7'd29: sccb_scl <= 1'b1;
                        7'd31: sccb_scl <= 1'b0;
                        7'd32: begin
                            sda_dir  <= 1'b0;    //从机应答
                            sda_out  <= 1'b1;
                        end
                        7'd33: sccb_scl <= 1'b1;
                        7'd34: state_done <= 1'b1;  //状态结束
                        7'd35: begin
                            sccb_scl <= 1'b0;
                            cnt <= 1'b0;
                        end
                        default :  ;
                    endcase
                end
                //--------------------------------------------------- 写数据
                DATA: begin
                    case(cnt)
                        7'd0: begin
                            sda_out <= sccb_data_t[7];
                            sda_dir <= 1'b1;
                        end
                        7'd1 : sccb_scl <= 1'b1;
                        7'd3 : sccb_scl <= 1'b0;
                        7'd4 : sda_out  <= sccb_data_t[6];
                        7'd5 : sccb_scl <= 1'b1;
                        7'd7 : sccb_scl <= 1'b0;
                        7'd8 : sda_out  <= sccb_data_t[5];
                        7'd9 : sccb_scl <= 1'b1;
                        7'd11: sccb_scl <= 1'b0;
                        7'd12: sda_out  <= sccb_data_t[4];
                        7'd13: sccb_scl <= 1'b1;
                        7'd15: sccb_scl <= 1'b0;
                        7'd16: sda_out  <= sccb_data_t[3];
                        7'd17: sccb_scl <= 1'b1;
                        7'd19: sccb_scl <= 1'b0;
                        7'd20: sda_out  <= sccb_data_t[2];
                        7'd21: sccb_scl <= 1'b1;
                        7'd23: sccb_scl <= 1'b0;
                        7'd24: sda_out  <= sccb_data_t[1];
                        7'd25: sccb_scl <= 1'b1;
                        7'd27: sccb_scl <= 1'b0;
                        7'd28: sda_out  <= sccb_data_t[0];
                        7'd29: sccb_scl <= 1'b1;
                        7'd31: sccb_scl <= 1'b0;
                        7'd32: begin
                            sda_dir  <= 1'b0;    //从机应答
                            sda_out  <= 1'b1;
                        end
                        7'd33: sccb_scl <= 1'b1;
                        7'd34: state_done <= 1'b1;  //状态结束
                        7'd35: begin
                            sccb_scl  <= 1'b0;
                            cnt  <= 1'b0;
                        end
                        default  :  ;
                    endcase
                end
                //--------------------------------------------------- 结束
                STOP: begin
                    case(cnt)
                        7'd0: begin
                            sda_dir  <= 1'b1;
                            sda_out  <= 1'b0;
                        end
                        7'd1 : sccb_scl <= 1'b1;
                        7'd3 : sda_out  <= 1'b1;
                        7'd15: state_done <= 1'b1;  //状态结束
                        7'd16: begin
                            cnt <= 1'b0;
                            sccb_done <= 1'b1;   //sccb配置完成
                        end
                        default  : ;
                    endcase
                end
            endcase
        end
    end

endmodule
```

[  完  ]

