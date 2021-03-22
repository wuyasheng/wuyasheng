---
title: Verilog Demo_9 IIC
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 IIC驱动控制方法
categories: FPGA-例程
tags:
  - Verilog
  - IIC
abbrlink: 3023981678
date: 2020-03-28 13:27:09
password:
---

## Verilog  IIC驱动

---

注 : 本文内容来自整理摘抄至原文 [参考文1](https://www.cnblogs.com/xiaomeige/p/6509414.html)、[参考文2](https://www.cnblogs.com/bixiaopengblog/p/7469536.html)、[参考文3](https://www.cnblogs.com/sepeng/p/3258705.html)、[参考文4](https://www.cnblogs.com/gianttank/archive/2010/11/20/1882575.html)、[参考文5](https://www.cnblogs.com/liujinggang/p/9656358.html)、[参考文6](https://www.cnblogs.com/gianttank/archive/2010/11/20/1882581.html)

### IIC 驱动原理

IIC(Inter-Integrated Circuit)总线是一种由PHILIPS公司开发的两线式串行总线，用于连接微控制器及其外围设备。I2C总线产生于在80年代，最初为音频和视频设备开发，如今主要在服务器管理中使用，其中包括单个组件状态的通信。例如管理员可对各个组件进行查询，以管理系统的配置或掌握组件的功能状态，如电源和系统风扇。可随时监控内存、硬盘、网络、系统温度等多个参数，增加了系统的安全性，方便了管理。IIC数据传输速率有标准模式（100 kbps）、快速模式（400 kbps）和高速模式（3.4 Mbps），另外一些变种实现了低速模式（10 kbps）和快速+模式（1 Mbps）。

下图是一个嵌入式系统中处理器仅通过2根线的IIC总线控制多个IIC外设的典型应用图

<img src="/images/post_images/verilog_demo_09_iic/IIC_1.png">

图中处理器是IIC主机，它仅仅通过两根信号就可以控制IO扩展器，各种不同的传感器，EEPROM，AD/DAs等设备，这也是IIC总线协议相较于其他协议最有优势的地方。

IIC总线的特点：

1、 简单性和有效性。由于接口直接在组件之上，因此I2C总线占用的空间非常小，减少了电路板的空间和芯片管脚的数量，降低了互联成本。总线的长度可高达25英尺，并且能够以10Kbps的最大传输速率支持40个组件。

2、 支持多主控(multimastering)， 其中任何能够进行发送和接收的设备都可以成为主总线。一个主控能够控制信号的传输和时钟频率。当然，在任何时间点上只能有一个主控占用IIC总线。

IIC总线协议详解：

IIC总线接口是一个标准的双向传输接口，一次数据传输需要主机和从机按照IIC协议的标准进行。I2C总线是由数据线SDA和时钟SCL构成的串行总线，可发送和接收数据，并且在硬件上都需要接一个上拉电阻到VCC。各种被控制电路均并联在这条总线上，但就像电话机一样只有拨通各自的号码才能工作，所以每个电路和模块都有唯一的地址，这样，各控制电路虽然挂在同一条总线上，却彼此独立，互不相关。

IIC主机往从机里面写入数据的步骤如下：

1、 主机发送一个起始信号和从机的设备地址给从机

2、 主机发送数据给从机

3、 主机发送一个停止信号结束发送过程

IIC主机从从机里面读出数据的步骤如下：

1、 主机发送一个起始信号和从机的设备地址给从机

2、 主机发送一个要读取的地址给从机

3、 主机从从机接收数据

4、 主机发送一个停止信号给从机结束整个接收过程

1、主机通过IIC总线往从机里面写数据

主机通过IIC总线往从机中写数据的时候，主机首先会发送一个起始信号，接着把IIC从机的7位设备地址后面添一个0(设备地址后面的0表示主机向从机写数据，1表示主机从从机中读数据)组成一个8位的数据，把这个8位的数据发给从机，发完这8位的数据以后主机马上释放SDA信号线等待从机的应答，如果从机正确收到这个数据，从机就会发送一个有效应答位0给主机告诉主机自己已经收到了数据，主机收到从机的有效应答位以后 ，接下来主机会发送想要写入的寄存器地址，寄存器发送完毕以后主机同样会释放SDA信号线等待从机的应答，从机如果正确收到了主机发过来的寄存器地址，从机会再次发送一个有效应答位给主机，主机收到从机的有效应答位0以后，接下来主机就会给从机发送想要写入从机的数据，从机正确收到这个数据以后仍然像之前两次一样会给主机发送一个有效应答位，主机收到这个有效应答位以后给从机发送一个停止信号，整个传输过程就结束了。下图是整个传输过程的示意图：

<img src="/images/post_images/verilog_demo_09_iic/IIC_2.png">

2、主机通过IIC总线从从机里面读数据

主机通过IIC总线从从机中读数据的过程与写数据的过程有相似之处，但是读数据的过程还多了一些额外的步骤。主机从从机读数据时主机首先会发送一个起始信号，接着把IIC从机的7位设备地址后面添一个0(设备地址后面的0表示主机向从机写数据，1表示主机从从机中读数据)，把这个8位的数据发给从机，发完这8位的数据以后主机马上释放SDA信号线等待从机的应答，如果从机正确收到这个数据，从机就会发送一个有效应答位0给主机告诉主机自己已经收到了数据，主机收到从机的有效应答位以后 ，接下来主机会发送想要读的寄存器地址，寄存器发送完毕以后主机同样会释放SDA信号线等待从机的应答，从机如果正确收到了主机发过来的寄存器地址，从机会再次发送一个有效应答位给主机，主机收到从机的有效应答位0以后，主机会给从机再次发送一次起始信号，接着把IIC从机的7位设备地址后面添一个1(设备地址后面的0表示主机向从机写数据，1表示主机从从机中读数据)，注意，第一次是在设备地址后面添0，这一次是在设备地址后面添1，把这个8位的数据发给从机，发完这8位的数据以后主机马上释放SDA信号线等待从机的应答，如果从机正确收到这个数据，从机就会发送一个有效应答位0给主机告诉主机自己已经收到了数据，接着从机继续占用SDA信号线给主机发送寄存器中的数据，发送完毕以后，主机再次占用SDA信号线发送一个非应答信号1给从机，主机发送一个停止信号给从机结束整个读数据的过程。下图是整个读数据过程的示意图

<img src="/images/post_images/verilog_demo_09_iic/IIC_3.png">

总线各个时候的状态，其中在SCL为高电平时，SDA不能发生改变，只能在SCL为低电平时，SDA才能发生改变。

<img src="/images/post_images/verilog_demo_09_iic/IIC_4.png">

（注意三态门的处理方法）

### 代码1

```verilog
module IIC2(	//（使用计数器的思想进行设计-注意三态门的处理方法）
    input				clk,
    input				rst_n,
    input		[7:0]	dev_addr,
    input		[7:0]	addr,
    input				wr_en,
    input		[7:0]	wr_data,
    input				rd_en,
    output	reg	[7:0]	rd_data,
    output	reg			rd_data_valid,
    output	reg			scl,
    inout				sda,
    output	reg			rdy
);
    localparam [7:0] CLK_MAX = 8'd100;

    reg [7:0]	cnt0;
    reg	[7:0]	cnt1;
    reg	[7:0]	cnt2;

    reg			flag;
    reg			wr_flag;
    reg			rd_flag;
    reg	[7:0]	dev_addr_reg;
    reg	[7:0]	addr_reg;
    reg	[7:0]	wr_data_reg;
    reg [26:0]  all_date_reg;
    reg	[7:0]	cnt_step;

    reg		sda_en;
    reg		sda_out;
    wire	sda_in;

    wire	start_flag;
    wire	wr_start_flag;
    wire	rd_start_flag;	

    wire	add_cnt0,end_cnt0;
    wire	add_cnt1,end_cnt1;
    wire	add_cnt2,end_cnt2;


    //确定开始的标志位，以及生成工作的flag信号
    assign start_flag = !flag && (wr_en | rd_en) && rdy;
    assign wr_start_flag = !flag && wr_en && rdy;
    assign rd_start_flag = !flag && rd_en && rdy;
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            flag	<= 1'b0;
        else if(start_flag)
            flag	<= 1'b1;
        else if(end_cnt2)
            flag	<= 1'b0;
        else
            flag	<= flag;
    end
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            rd_flag	<= 1'b0;
        else if(rd_start_flag)
            rd_flag	<= 1'b1;
        else if(end_cnt2)
            rd_flag	<= 1'b0;
        else
            rd_flag	<= rd_flag;
    end
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            wr_flag	<= 1'b0;
        else if(wr_start_flag)
            wr_flag	<= 1'b1;
        else if(end_cnt2)
            wr_flag	<= 1'b0;
        else
            wr_flag	<= wr_flag;
    end
    //对读写进行初始化，写操作将地址及数据暂存，读操作对地址进行暂存
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            dev_addr_reg	<= 8'd0;
            addr_reg		<= 8'd0;
            wr_data_reg		<= 8'd0;
            cnt_step		<= 8'd0;
        end
        else if(wr_start_flag)begin
            dev_addr_reg	<= dev_addr;
            addr_reg		<= addr;
            wr_data_reg		<= wr_data;
            cnt_step		<= 8'd29;
            all_date_reg	<= {dev_addr,1'b1,addr,1'b1,wr_data,1'b1};
        end
        else if(rd_start_flag)begin
            dev_addr_reg	<= dev_addr;
            addr_reg		<= addr;
            wr_data_reg		<= 8'd0;
            cnt_step		<= 8'd39;
            all_date_reg	<= {dev_addr,1'b1,addr,1'b1,1'b1,dev_addr};
        end
        else begin
            dev_addr_reg	<= dev_addr_reg;
            addr_reg		<= addr_reg;
            wr_data_reg		<= wr_data_reg;
            cnt_step		<= cnt_step;
        end
    end
    //计数，确定几个阶段
    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            cnt0 <= 8'd0;
        end
        else if(add_cnt0)begin
            if(end_cnt0)
                cnt0 <= 8'd0;
            else
                cnt0 <= cnt0 + 1'b1;
        end
    end

    assign add_cnt0 = flag == 1'b1;
    assign end_cnt0 = add_cnt0 && cnt0 == CLK_MAX - 1'b1;

    always @(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            cnt1 <= 8'd0;
        end
        else if(add_cnt1)begin
            if(end_cnt1)
                cnt1 <= 8'd0;
            else
                cnt1 <= cnt1 + 1'b1;
        end
    end

    assign add_cnt1 = end_cnt0;
    assign end_cnt1 = add_cnt1 && cnt1 == 3;

    always @(posedge clk or negedge rst_n)begin 
        if(!rst_n)begin
            cnt2 <= 8'd0;
        end
        else if(add_cnt2)begin
            if(end_cnt2)
                cnt2 <= 8'd0;
            else
                cnt2 <= cnt2 + 1'b1;
        end
    end

    assign add_cnt2 = end_cnt1;
    assign end_cnt2 = add_cnt2 && cnt2 == cnt_step - 1'b1;

    //功能模块部分.......
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            scl		<= 1'b1;
        end
        else if(flag)begin
            case(cnt2)
                8'd0:	
                    case(cnt1)
                        8'd0	:scl	<= 1'b1;
                        8'd1	:scl	<= 1'b1;
                        8'd2	:scl	<= 1'b1;
                        8'd3	:scl	<= 1'b0;
                        default	:scl	<= 1'b1;
                    endcase
                cnt_step - 1'b1:
                    case(cnt1)
                        8'd0	:scl	<= 1'b0;
                        8'd1	:scl	<= 1'b1;
                        8'd2	:scl	<= 1'b1;
                        8'd3	:scl	<= 1'b1;
                        default	:scl	<= 1'b1;
                    endcase
                default:
                    case(cnt1)
                        8'd0	:scl	<= 1'b0;
                        8'd1	:scl	<= 1'b1;
                        8'd2	:scl	<= 1'b1;
                        8'd3	:scl	<= 1'b0;
                        default	:scl	<= 1'b1;
                    endcase
            endcase
        end
        else
            scl		<= 1'b1;
    end

    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            sda_en	<= 1'b1;
            sda_out	<= 1'b1;
        end
        else if(wr_flag)begin
            case(cnt2)
                8'd0	: 
                    case(cnt1)
                        8'd0	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                        8'd1	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                        default	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                    endcase
                8'd1,8'd2,8'd3,8'd4,8'd5,8'd6,8'd7,8'd8,
                8'd10,8'd11,8'd12,8'd13,8'd14,8'd15,8'd16,8'd17,
                8'd19,8'd20,8'd21,8'd22,8'd23,8'd24,8'd25,8'd26:
                    begin
                        sda_en <= 1'b1;
                        sda_out <= all_date_reg[8'd26 - cnt2];
                    end	
                8'd9,8'd18,8'd27:
                    begin
                        sda_en <= 1'b0;
                        sda_out <= 1'b1;
                    end
                8'd28: 
                    case(cnt1)
                        8'd0	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                        8'd1	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                        default	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                    endcase
            endcase
        end
        else if(rd_flag)begin	//读操作时序
            case(cnt2)
                8'd0,8'd19: 
                    case(cnt1)
                        8'd0	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                        8'd1	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                        default	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                    endcase
                8'd1,8'd2,8'd3,8'd4,8'd5,8'd6,8'd7,8'd8,
                8'd10,8'd11,8'd12,8'd13,8'd14,8'd15,8'd16,8'd17,
                8'd20,8'd21,8'd22,8'd23,8'd24,8'd25,8'd26,8'd27:
                    begin
                        sda_en <= 1'b1;
                        sda_out <= all_date_reg[8'd27 - cnt2];
                    end	
                8'd29,8'd30,8'd31,8'd32,8'd33,8'd34,8'd35,8'd36:
                    begin
                        sda_en <= 1'b0;
                        rd_data[8'd36 - cnt2] <= sda_in;
                    end	
                8'd9,8'd18,8'd28,8'd37:
                    begin
                        sda_en <= 1'b0;
                        sda_out <= 1'b1;
                    end
                8'd38: 
                    case(cnt1)
                        8'd0	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                        8'd1	:begin sda_en <= 1'b1;sda_out <= 1'b0;end
                        default	:begin sda_en <= 1'b1;sda_out <= 1'b1;end
                    endcase
            endcase

        end 
        else begin

        end	
    end

    //读数据完成信号，可以取读到的数据
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            rd_data_valid		<= 1'b0;
        else if(rd_flag && cnt2 == cnt_step - 1'b1)
            rd_data_valid		<= 1'b1;
        else
            rd_data_valid		<= 1'b0;
    end

    //准备信号，为1时表示可以接受控制信号，即空闲
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)
            rdy		<= 1'b1;
        else
            rdy		<= ~flag;
    end

    //三态门结构
    assign	sda = sda_en ? sda_out : 1'bz;
    assign	sda_in = sda;

endmodule
```

### 代码2

```verilog
module I2C_Controller(		//本代码为IIC驱动程序，可用于SCCB写操作
        input				iCLK,		
        input				iRST_N,		
        input				I2C_CLK,	
        input				I2C_EN,		
        input		[23:0]	I2C_WDATA,	
        output				I2C_SCLK,	
        inout				I2C_SDAT,	
        input				WR,     	
        input				GO,      	
        output				ACK,      	
        output	reg			END,     	 
        output	reg	[7:0]	I2C_RDATA	
    );

    reg		I2C_BIT;
    reg 	SCLK;	
    reg	[5:0]	SD_COUNTER;
    wire 	I2C_SCLK1 = 	(GO == 1 &&
                             ((SD_COUNTER >= 5 && SD_COUNTER <=12 || SD_COUNTER == 14) ||	
                              (SD_COUNTER >= 16 && SD_COUNTER <=23 || SD_COUNTER == 25) ||
                              (SD_COUNTER >= 27 && SD_COUNTER <=34 || SD_COUNTER == 36))) ? I2C_CLK : SCLK;					
    wire 	I2C_SCLK2 = 	(GO == 1 &&
                             ((SD_COUNTER >= 5 && SD_COUNTER <=12 || SD_COUNTER == 14) ||
                              (SD_COUNTER >= 16 && SD_COUNTER <=23 || SD_COUNTER == 25) ||
                              (SD_COUNTER >= 33 && SD_COUNTER <=40 || SD_COUNTER == 42) ||
                              (SD_COUNTER >= 45 && SD_COUNTER <=52 || SD_COUNTER == 54))) ? I2C_CLK : SCLK;						
    assign	I2C_SCLK = WR ? I2C_SCLK1 : I2C_SCLK2;	
    wire	SDO1	=		((SD_COUNTER == 13 || SD_COUNTER == 14)|| 
                             (SD_COUNTER == 24 || SD_COUNTER == 25) || 
                             (SD_COUNTER == 35 || SD_COUNTER == 36)) ? 1'b0 : 1'b1;								
    wire	SDO2	=		((SD_COUNTER == 13 || SD_COUNTER == 14)|| 
                             (SD_COUNTER == 24 || SD_COUNTER == 25) || 
                             (SD_COUNTER == 41 || SD_COUNTER == 42) ||
                             (SD_COUNTER >= 44 && SD_COUNTER <= 52)) ? 1'b0 : 1'b1;		
    wire	SDO = WR ? SDO1 : SDO2;
    assign	I2C_SDAT = SDO ? I2C_BIT : 1'bz;
    reg		ACKW1, ACKW2, ACKW3;		
    reg 	ACKR1, ACKR2, ACKR3;		
    assign	ACK = WR ? (ACKW1 | ACKW2 | ACKW3) : (ACKR1 | ACKR2 | ACKR3);

    always @(posedge iCLK or negedge iRST_N) 
        begin
            if (!iRST_N) 
                SD_COUNTER <= 6'b0;
            else if(I2C_EN)
                begin
                    if (GO == 0 || END == 1) 
                        SD_COUNTER <= 6'b0;
                    else if (SD_COUNTER < 6'd63) 
                        SD_COUNTER <= SD_COUNTER + 6'd1;	
                end
            else
                SD_COUNTER <= SD_COUNTER;
        end

    always @(posedge iCLK or negedge iRST_N) 
        begin
            if(!iRST_N) 
                begin 
                    SCLK <= 1;
                    I2C_BIT <= 1; 
                    ACKW1 <= 1; ACKW2 <= 1; ACKW3 <= 1; 
                    ACKR1 <= 1; ACKR2 <= 1; ACKR3 <= 1;
                    END <= 0;
                    I2C_RDATA <= 8'h0;	
                end
            else if(I2C_EN)		
                begin
                    if(GO)
                        begin
                            if(WR)		
                                begin
                                    case(SD_COUNTER)
                                        6'd0 :	begin
                                            SCLK <= 1;
                                            I2C_BIT <= 1;
                                            ACKW1 <= 1; ACKW2 <= 1; ACKW3 <= 1;
                                            ACKR1 <= 1; ACKR2 <= 1; ACKR3 <= 1;
                                            END <= 0;
                                        end
                                        6'd1 :	begin 
                                            SCLK <= 1;
                                            I2C_BIT <= 1;
                                            ACKW1 <= 1; ACKW2 <= 1; ACKW3 <= 1; 
                                            END <= 0;
                                        end
                                        6'd2  : I2C_BIT <= 0;		
                                        6'd3  : SCLK <= 0;			
                                        6'd4  : I2C_BIT <= I2C_WDATA[23];	
                                        6'd5  : I2C_BIT <= I2C_WDATA[22];	
                                        6'd6  : I2C_BIT <= I2C_WDATA[21];	
                                        6'd7  : I2C_BIT <= I2C_WDATA[20];	
                                        6'd8  : I2C_BIT <= I2C_WDATA[19];	
                                        6'd9  : I2C_BIT <= I2C_WDATA[18];	
                                        6'd10 : I2C_BIT <= I2C_WDATA[17];	
                                        6'd11 : I2C_BIT <= I2C_WDATA[16];	
                                        6'd12 : I2C_BIT <= 0;				
                                        6'd13 : ACKW1 	<= I2C_SDAT;		
                                        6'd14 : I2C_BIT <= 0;				
                                        6'd15 : I2C_BIT <= I2C_WDATA[15];	
                                        6'd16 : I2C_BIT <= I2C_WDATA[14];	
                                        6'd17 : I2C_BIT <= I2C_WDATA[13];	
                                        6'd18 : I2C_BIT <= I2C_WDATA[12];	
                                        6'd19 : I2C_BIT <= I2C_WDATA[11];	
                                        6'd20 : I2C_BIT <= I2C_WDATA[10];	
                                        6'd21 : I2C_BIT <= I2C_WDATA[9];   
                                        6'd22 : I2C_BIT <= I2C_WDATA[8];	
                                        6'd23 : I2C_BIT <= 0;				
                                        6'd24 : ACKW2 	<= I2C_SDAT;		
                                        6'd25 : I2C_BIT <= 0;				
                                        6'd26 : I2C_BIT <= I2C_WDATA[7];	 
                                        6'd27 : I2C_BIT <= I2C_WDATA[6];	
                                        6'd28 : I2C_BIT <= I2C_WDATA[5];	
                                        6'd29 : I2C_BIT <= I2C_WDATA[4];	
                                        6'd30 : I2C_BIT <= I2C_WDATA[3];	
                                        6'd31 : I2C_BIT <= I2C_WDATA[2];	
                                        6'd32 : I2C_BIT <= I2C_WDATA[1];	
                                        6'd33 : I2C_BIT <= I2C_WDATA[0];	
                                        6'd34 : I2C_BIT <= 0;				
                                        6'd35 : ACKW3 	<= I2C_SDAT;		
                                        6'd36 : I2C_BIT <= 0;				
                                        6'd37 : begin	SCLK <= 0; I2C_BIT <= 0; end
                                        6'd38 : SCLK <= 1;	
                                        6'd39 : begin I2C_BIT <= 1; END <= 1; end 
                                        default : begin I2C_BIT <= 1; SCLK <= 1; end
                                    endcase
                                end
                            else		
                                begin
                                    case(SD_COUNTER)
                                        6'd0 :	begin
                                            SCLK <= 1;
                                            I2C_BIT <= 1;
                                            ACKW1 <= 1; ACKW2 <= 1; ACKW3 <= 1;
                                            ACKR1 <= 1; ACKR2 <= 1; ACKR3 <= 1; 
                                            END <= 0;
                                        end
                                        6'd1 :	begin 
                                            SCLK <= 1;
                                            I2C_BIT <= 1;
                                            ACKR1 <= 1; ACKR2 <= 1; ACKR3 <= 1; 
                                            END <= 0;
                                        end
                                        6'd2  : I2C_BIT <= 0;		
                                        6'd3  : SCLK <= 0;			
                                        6'd4  : I2C_BIT <= I2C_WDATA[23];	
                                        6'd5  : I2C_BIT <= I2C_WDATA[22];	
                                        6'd6  : I2C_BIT <= I2C_WDATA[21];	
                                        6'd7  : I2C_BIT <= I2C_WDATA[20];	
                                        6'd8  : I2C_BIT <= I2C_WDATA[19];
                                        6'd9  : I2C_BIT <= I2C_WDATA[18];	
                                        6'd10 : I2C_BIT <= I2C_WDATA[17];	
                                        6'd11 : I2C_BIT <= I2C_WDATA[16];	
                                        6'd12 : I2C_BIT <= 0;				
                                        6'd13 : ACKR1 	<= I2C_SDAT;		
                                        6'd14 : I2C_BIT <= 0;				
                                        6'd15 : I2C_BIT <= I2C_WDATA[15];	
                                        6'd16 : I2C_BIT <= I2C_WDATA[14];	
                                        6'd17 : I2C_BIT <= I2C_WDATA[13];	
                                        6'd18 : I2C_BIT <= I2C_WDATA[12];	
                                        6'd19 : I2C_BIT <= I2C_WDATA[11];	
                                        6'd20 : I2C_BIT <= I2C_WDATA[10];	
                                        6'd21 : I2C_BIT <= I2C_WDATA[9];    
                                        6'd22 : I2C_BIT <= I2C_WDATA[8];	
                                        6'd23 : I2C_BIT <= 0;				
                                        6'd24 : ACKR2 	<= I2C_SDAT;		
                                        6'd25 : I2C_BIT <= 0;				
                                        6'd26 : begin	SCLK <= 0; I2C_BIT <= 0; end
                                        6'd27 : SCLK <= 1;	
                                        6'd28 : begin I2C_BIT <= 1; end 
                                        6'd29 :	begin 
                                            SCLK <= 1;
                                            I2C_BIT <= 1;
                                        end
                                        6'd30 : I2C_BIT <= 0;		
                                        6'd31 : SCLK <= 0;			
                                        6'd32 : I2C_BIT <= I2C_WDATA[23];	
                                        6'd33 : I2C_BIT <= I2C_WDATA[22];	
                                        6'd34 : I2C_BIT <= I2C_WDATA[21];	
                                        6'd35 : I2C_BIT <= I2C_WDATA[20];	
                                        6'd36 : I2C_BIT <= I2C_WDATA[19];	
                                        6'd37 : I2C_BIT <= I2C_WDATA[18];	
                                        6'd38 : I2C_BIT <= I2C_WDATA[17];	
                                        6'd39 : I2C_BIT <= 1'b1;			
                                        6'd40 : I2C_BIT <= 0;				
                                        6'd41 : ACKR3 	<= I2C_SDAT;		
                                        6'd42 : I2C_BIT <= 0;				
                                        6'd43 : I2C_BIT 	<= 0;			
                                        6'd44 : I2C_BIT 	<= 0;			
                                        6'd45 : I2C_RDATA[7] <= I2C_SDAT;	
                                        6'd46 : I2C_RDATA[6] <= I2C_SDAT;	
                                        6'd47 : I2C_RDATA[5] <= I2C_SDAT;	
                                        6'd48 : I2C_RDATA[4] <= I2C_SDAT;	 
                                        6'd49 : I2C_RDATA[3] <= I2C_SDAT;	
                                        6'd50 : I2C_RDATA[2] <= I2C_SDAT;	
                                        6'd51 : I2C_RDATA[1] <= I2C_SDAT;	
                                        6'd52 : I2C_RDATA[0] <= I2C_SDAT;	 	
                                        6'd53 : I2C_BIT 	<= 1;			
                                        6'd54 : I2C_BIT 	<= 0;			
                                        6'd55 : begin	SCLK <= 0; I2C_BIT <= 0; end
                                        6'd56 : SCLK <= 1;	
                                        6'd57 : begin I2C_BIT <= 1; END <= 1; end 
                                        default : begin I2C_BIT <= 1; SCLK <= 1; end
                                    endcase
                                end
                        end
                    else
                        begin
                            SCLK <= 1;
                            I2C_BIT <= 1; 
                            ACKW1 <= 1; ACKW2 <= 1; ACKW3 <= 1; 
                            ACKR1 <= 1; ACKR2 <= 1; ACKR3 <= 1;
                            END <= 0;
                            I2C_RDATA <= I2C_RDATA;
                        end
                end
        end

endmodule
```



[  完  ]

