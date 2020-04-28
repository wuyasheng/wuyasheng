---
title: Verilog Demo_13 RAM
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理 RAM 的相关知识
categories: ☸ FPGA
tags:
  - Verilog
  - ram
abbrlink: 3752417558
date: 2020-03-29 15:40:09
password:
---

## RAM 设计原理及方法

---

注 : 本文整理摘抄 RAM 相关内容， [参考文1](https://blog.csdn.net/bleauchat/article/details/85557869)、[参考文2](https://blog.csdn.net/Stynis/article/details/80555825)、[参考文3](https://zhuanlan.zhihu.com/p/93231539)、[参考文4](https://wenku.baidu.com/view/2b928f8e700abb68a882fb06.html)

### RAM 分类

- 单口与双口

  单口与双口的区别在于，单口只有一组数据线与地址线，因此读写不能同时进行；而双口有两组数据线与地址线，读写可同时进行；FIFO读写可同时进行，可以看作是双口；

- 简单双口RAM与真双口RAM

  双口RAM分为**伪双口RAM**（Xilinx称为Simple two-dual RAM）与**双口RAM**（Xilinx称为true two-dual RAM），伪双口RAM，一个端口只读，另一个端口只写，且写入和读取的时钟可以不同，位宽比可以不是1:1；而双口RAM两个端口都分别带有读写端口，可以在没有干扰的情况下进行读写，彼此互不干扰；

<img src="/images/post_images/verilog_demo_13_ram/ram_1.png">

<img src="/images/post_images/verilog_demo_13_ram/ram_2.png">

###  单口 RAM

单口RAM只有一组数据线与地址线，读写不能同时进行；

```verilog
module ram #(
    parameter     ADDR_WIDTH   = 8	,	//定义地址线位宽
    parameter     DATA_WIDTH   = 16		//定义数据线位宽
    )
    (
    input                   clk,
    input                   rst_n,
    input                   wr_en,
    input                   rd_en,
    input [ADDR_WIDTH - 1:0]  addr,
    inout [DATA_WIDTH - 1:0]  data_io
    );
    reg [DATA_WIDTH - 1:0] bram[2 ** ADDR_WIDTH - 1:0];           
    reg [DATA_WIDTH - 1:0] data;
    integer          i;

    //add implementation code here 
    always @(posedge clk or negedge rst_n)begin
        if (rst_n == 1'b0)begin
            for(i = 0;i < 2 ** ADDR_WIDTH;i = i+1) //reset, 按字操作
                bram[i] <= 32'b0;
        end
        else if (wr_en)
            bram[addr] <= data_io;
        else if (rd_en) 
            data <= bram[addr];
        else
            data <= 32'bz;//读写均无效时，为高阻态。若不加此句，时序会出现问题
    end

    assign data_io = rd_en? data : 32'bz; //三态门实现
endmodule
```

### 简单双口RAM

简单双口 ram 是单一时钟，支持一个读地址和一个写地址。本设计是同步读数ram，异步读数ram去掉时钟即可。

```verilog
module ram_simple_double_port #(
    parameter     ADDR_WIDTH   = 8   ,  
    parameter     DATA_WIDTH   = 16
    )
    (
    input		clk,
    input		rst_n,
    input		cs_en,
    input		wr_en,
    input		rd_en,
    input [ADDR_WIDTH - 1:0]  addr_a,//写地址
    input [ADDR_WIDTH - 1:0]  addr_b,//读地址
    input 		[DATA_WIDTH - 1:0]  data_a,//写数据
    output reg 	[DATA_WIDTH - 1:0]  data_b//写数据
    );

    reg [DATA_WIDTH - 1:0] bram[2 ** ADDR_WIDTH - 1:0];           
    //reg [DATA_WIDTH - 1:0] data;
    integer          i;

    //add implementation code here 
    always @(posedge clk or negedge rst_n)begin
        if (rst_n == 1'b0)begin
            for(i = 0;i < 2 ** ADDR_WIDTH;i = i+1) //reset, 按字操作
                bram[i] <= 32'b0;
        end
        else if (wr_en & cs_en)
            bram[addr_a] <= data_a;
        else if (rd_en & cs_en) 
            data_b <= bram[addr_b];
        else
            data_b <= 32'bz;      //读写均无效时，为高阻态。
    end

endmodule
```



### 真双口RAM

真双口ram是两个时钟，支持两套独立完整的读写。

```verilog
module ram_true_double_port #(
	parameter     ADDR_WIDTH   = 8   ,  
    parameter     DATA_WIDTH   = 16
    )(
    //端口1
	input	wr_clk_a,
	input	wr_en_a,
	input	[ADDR_WIDTH - 1:0] wr_addr_a,
	input	[DATA_WIDTH - 1:0] wr_data_a,

	input	rd_clk_a,
	input	rd_en_a,
	input		[ADDR_WIDTH - 1:0] rd_addr_a,
	output	reg [DATA_WIDTH - 1:0] rd_data_a,
	//端口2
	input	wr_clk_b,
	input	wr_en_b,
	input	[ADDR_WIDTH - 1:0] wr_addr_b,
	input	[DATA_WIDTH - 1:0] wr_data_b,

	input	rd_clk_b,
	input	rd_en_b,
	input		[ADDR_WIDTH - 1:0] rd_addr_b,
	output	reg [DATA_WIDTH - 1:0] rd_data_b
	);

	reg [DATA_WIDTH - 1:0] mem[2 ** ADDR_WIDTH - 1:0];   
	wire wr_a;
	wire wr_b;
	assign wr_a = wr_en_a & wr_clk_a;
	assign wr_b = wr_en_b & wr_clk_b;
	//写数据，避免冲突
	always @(*)begin
		if(wr_a)
			mem[wr_addr_a] = wr_data_a;
		else if(wr_b)
			mem[wr_addr_b] = wr_data_b;
	end
	//读数据
	always@(posedge rd_clk_a)begin
		if(rd_en_a)
			rd_data_a <= mem[rd_addr_a];
	end
	always@(posedge rd_clk_b)begin
		if(rd_en_b)
			rd_data_b <= mem[rd_addr_b];
	end
endmodule
```

注：声明wr_a和wr_b两个变量，好处在于，当a,b两侧的时钟为同一个时钟时，只要错开半个周期，就不存在冲突的情况。在 `写进程里`，当a和b的地址不一样时，不会冲突；当地址一样时，b的值为最后的新值。

​    

[   完  ]



