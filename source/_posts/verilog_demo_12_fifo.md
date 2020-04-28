---
title: Verilog Demo_12 FIFO
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理fifo的相关知识
categories: ☸ FPGA
tags:
  - Verilog
  - fifo
abbrlink: 967455898
date: 2020-03-29 15:27:09
password:
---

## Verilog  同步/异步FIFO设计

---

注 : 本文整理摘抄 同步 / 异步FIFO 的相关内容 [参考文1](https://www.cnblogs.com/yuphone/archive/2010/12/22/1913500.html)、[参考文2](https://www.cnblogs.com/ylsm-kb/p/9068449.html)（异步fifo）、[参考文3](https://www.cnblogs.com/ylsm-kb/p/9055510.html)（同步fifo）、[参考文4](https://www.cnblogs.com/jason-tian-lei/p/4302849.html)、[参考文5](https://www.cnblogs.com/kingstacker/p/7666358.html)、[参考文6](https://blog.csdn.net/limanjihe/article/details/49910557)、[参考文7](https://www.cnblogs.com/dxs959229640/p/8144656.html)（深度计算）、[参考文8](https://www.cnblogs.com/chip/p/5080341.html)（深度计算）

### FIFO 原理

​		FIFO缓存是介于两个子系统之间的弹性存储器，其概念图如图所示。它有两个控制信号，wr和rd，用于读操作和写操作。当wr被插入时，输入的数据被写入缓存，此时读操作被忽视。FIFO缓存的head一般情况下总是有效的，因此可在任意时间被读取。rd信号实际上就像“remove”信号；当其被插入的时候，FIFO缓存的第一个项（即head）被移除，下一个项变为可用项。

FIFO是一种先进先出的数据缓存器，他与普通存储器相比：

- 优点：没有外部读写地址线，这样使用起来非常简单；

- 缺点：只能顺序写入数据，顺序的读出数据， 其数据地址由内部读写指针自动加1完成，不能像普通存储器那样可以由地址线决定读取或写入某个指定的地址。

<img src="/images/post_images/verilog_demo_12_fifo/fifo.png">

​		FIFO设计方法，实现FIFO缓存的方法是给寄存器文件添加一个控制电路。寄存器文件通过两个指针像循环队列一样来排列寄存器。写指针（write poniter）指向队列的头（head）；读指针（read poniter）指向队列的尾（tail）。每次读操作或写操作，指针都会前进一个位置。8-字循环队列的操作如下图所示。

<img src="/images/post_images/verilog_demo_12_fifo/fifo_2.png">

根据FIFO工作的时钟域，可以将FIFO分为同步FIFO和异步FIFO。同步FIFO是指读时钟和写时钟为同一个时钟，在时钟沿来临时同时发生读写操作。异步FIFO是指读写时钟不一致，读写时钟是互相独立的。

### 同步 FIFO 原理

​		同步FIFO是指读时钟和写时钟为同一个时钟，在时钟沿来临时同时发生读写操作。FIFO设计的难点在于怎样判断FIFO的空/满状态。为了保证数据正确的写入或读出，而不发生溢出或读空的状态出现，必须保证FIFO在满的情况下不能进行写操作，在空的状态下不能进行读操作。因此，怎样判断FIFO的满/空就成了FIFO设计的核心问题。

### 同步 FIFO 代码

```verilog
module fifo_syn #(
	parameter	WIDTH	= 8	,
	parameter	DEPTH	= 256	//深度
	)(
	//system signals
	input		clk ,
	input		rst_n ,
	input		fifo_wr , 
	input		fifo_rd	,
	input		[WIDTH - 1:0]	data_in		,
	output	reg [WIDTH - 1:0]	data_out	,
	output		full		,
	output		empty		
);

reg [7 : 0] ram [DEPTH - 1'b1 : 0];	//开辟存储区
reg [DEPTH-1 : 0] count;	//计数
reg [DEPTH-1 : 0] p_wr;		//写指针
reg [DEPTH-1 : 0] p_rd;		//读指针
//简单状态机
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)begin
		count 	<= 0;
		p_wr	<= 0;
		p_rd	<= 0;
		data_out<= 8'd0;
	end
	else begin
		case ({fifo_wr,fifo_rd}) 
		    2'b10	:begin
		    	if(~full)begin
		    		ram[p_wr] <= data_in;	
		    		count	<= count + 1'b1;
		    		p_wr	<= p_wr + 1'b1;
		    	end
		    end
		    2'b01	:begin
		    	if(~empty)begin
		    		data_out <= ram[p_rd];	
		    		count	<= count - 1'b1;
		    		p_rd	<= p_rd + 1'b1;
		    	end
		    end
		    2'b11	:begin
		    	if(empty)begin
		    		ram[p_wr] <= data_in;	
		    		count	<= count + 1'b1;
		    		p_wr	<= p_wr + 1'b1;
		    	end
		    	else begin
		    		ram[p_wr] <= data_in;
		    		p_wr	<= p_wr + 1'b1;

		    		data_out	<= ram[p_rd];
		    		p_rd	<= p_rd + 1'b1;
		    		count	<= count;
		    	end   		
		    end		
		endcase
	end	   
end
//判断空满状态
assign full = (count == 8'hff) ? 1 : 0;
assign empty = (count == 8'd0) ? 1 : 0;

endmodule
```



### 异步 FIFO 原理

由于是异步FIFO的设计，读写时钟不一样，在产生读空信号和写满信号时，会涉及到跨时钟域的问题，如何解决？

　　**跨时钟域的问题：**由于读指针是属于读时钟域的，写指针是属于写时钟域的，而异步FIFO的读写时钟域不同，是异步的，要是将读时钟域的读指针与写时钟域的写指针不做任何处理直接比较肯定是错误的，因此我们需要进行同步处理以后仔进行比较

　　**解决方法**：**加两级寄存器同步 + 格雷码（目的都是消除亚稳态）**

<img src="/images/post_images/verilog_demo_12_fifo/fifo_3.png">

​	1.使用异步信号进行使用的时候，好的设计都会对异步信号进行同步处理，同步一般采用多级D触发器级联处理，如下图。这种模型大部分资料都说的是第一级寄存器产生亚稳态后，第二级寄存器稳定输出概率为90%，第三极寄存器稳定输出的概率为99%，如果亚稳态跟随电路一直传递下去，那就会另自我修护能力较弱的系统直接崩溃。

​	2.将一个二进制的计数值从一个时钟域同步到另一个时钟域的时候很容易出现问题，因为采用二进制计数器时所有位都可能同时变化，在同一个时钟沿同步多个信号的变化会产生亚稳态问题。而使用格雷码只有一位变化，因此在两个时钟域间同步多个位不会产生问题。所以需要一个二进制到gray码的转换电路，将地址值转换为相应的gray码，然后将该gray码同步到另一个时钟域进行对比，作为空满状态的检测。

<img src="/images/post_images/verilog_demo_12_fifo/fifo_4.png">

换一种描述方法：

<img src="/images/post_images/verilog_demo_12_fifo/fifo_5.png">

verilog代码实现就一句：assign  gray_code = (bin_code>>1)  ^  bin_code;

 **使用gray码解决了一个问题，但同时也带来另一个问题，即在格雷码域如何判断空与满。** 

这里直接给出结论：

　　**判断读空时**：需要读时钟域的格雷码rgray_next和被同步到读时钟域的写指针rd2_wp每一位完全相同;

　　**判断写满时**：需要写时钟域的格雷码wgray_next和被同步到写时钟域的读指针wr2_rp高两位不相同，其余各位完全相同；

### 异步FIFO代码

```verilog
module  fifo
#(
　　parameter WSIZE = 8,
　　parameter DSIZE = 32
)
(
　　input wr_clk,
　　input rst,
　　input wr_en,
　　input [WSIZE-1 : 0]din,
　　input rd_clk,
　　input rd_en,
　　output [WSIZE-1 : 0]dout,
　　output reg rempty,
　　output reg wfull
);
//定义变量
reg [WSIZE-1 :0] mem [DSIZE-1 : 0];
reg [WSIZE-1 : 0] waddr,raddr;
reg [WSIZE : 0] wbin,rbin,wbin_next,rbin_next;
reg [WSIZE : 0] wgray_next,rgray_next;
reg [WSIZE : 0] wp,rp;
reg [WSIZE : 0] wr1_rp,wr2_rp,rd1_wp,rd2_wp;
wire rempty_val,wfull_val;

//输出数据
assign dout = mem[raddr];
//输入数据
always@(posedge wr_clk)
　　if(wr_en && !wfull)
　　　　mem[waddr] <= din;

//1.产生存储实体的读地址raddr; 2.将普通二进制转化为格雷码，并赋给读指针rp
always@(posedge rd_clk or negedge rst_n)
　　if(!rst_n)
　　　　{rbin,rp} <= 0;
　　else
　　　　{rbin,rp} <= {rbin_next,rgray_next};

assign raddr = rbin[WSIZE-1 : 0];
assign rbin_next = rbin + (rd_en & ~rempty);
assign rgray_next = rbin_next ^ (rbin_next >> 1);

//1.产生存储实体的写地址waddr; 2.将普通二进制转化为格雷码，并赋给写指针wp
always@(posedge wr_clk or negedge rst_n)
　　if(!rst_n)
　　　　{wbin,wp} <= 0;
　　else
　　　　{wbin,wp} <= {wbin_next,wgray_next};

assign waddr = wbin[WSIZE-1 : 0];
assign wbin_next = wbin + (wr_en & ~wfull);
assign wgray_next = wbin_next ^ (wbin_next >> 1);

//将读指针rp同步到写时钟域
always@(posedge wr_clk or negedge rst_n)
　　if(!rst_n)
　　　　{wr2_rp,wr1_rp} <= 0;
　　else
　　　　{wr2_rp,wr1_rp} <= {wr1_rp,rp};

//将写指针wp同步到读时钟域
always@(posedge rd_clk or negedge rst_n)
　　if(!rst_n)
　　　　{rd2_wp,rd1_wp} <= 0;
　　else
　　　　{rd2_wp,rd1_wp} <= {rd1_wp,wp};

//产生读空信号rempty
assign rempty_val = (rd2_wp == rgray_next);
always@(posedge rd_clk or negedge rst_n)
　　if(rst_n)
　　　　rempty <= 1'b1;
　　else
　　　　rempty <= rempty_val;

//产生写满信号wfull
assign wfull_val = ((~(wr2_rp[WSIZE : WSIZE-1]),wr2_rp[WSIZE-2 : 0]) == wgray_next);
always@(posedge wr_clk or negedge rst_n)
　　if(!rst_n)
　　　　wfull <= 1'b0;
　　else
　　　　wfull <= wfull_val;

endmodule
```



### 异步FIFO 最小深度计算

​		数字IC设计中我们经常会遇到这种场景，工作在不同时钟域的两个模块，它们之间需要进行数据传递，为了避免数据丢失，我们会使用到FIFO。当读数据的速率小于写数据的速率时，我们就不得不将那些还没有被读走的数据缓存下来，那么我们需要开多大的空间去缓存这些数据呢？缓存开大了会浪费资源，开小了会丢失数据，如何去计算最小FIFO深度是我们讨论的重点。

**数据突发长度(burst length)**

​	在讲解如何去计算FIFO深度之前，我们来理解一个术语burst length。要理解数据的突发长度，首先我们来考虑一种场景，假如模块A不间断的往FIFO中写数据，模块B同样不间断的从FIFO中读数据，不同的是模块A写数据的时钟频率要大于模块B读数据的时钟频率，那么在一段时间内总是有一些数据没来得及被读走，如果系统一直在工作，那么那些没有被读走的数据会越累积越多，那么FIFO的深度需要是无穷大的，因此只有在突发数据传输过程中讨论FIFO深度才是有意义的。也就是说我们一次传递一包数据完成后再去传递下一包数据，我们把一段时间内传递的数据个数称为**burst length**。

​	我们知道burst length过后，通过上述讨论我们大概就知道FIFO的最小深度与burst rate, burst size, read and write frequency等因素有关。要确定FIFO的深度，关键在于计算出在突发读写这段时间内有多少个数据没有被读走。也就是说FIFO的最小深度就等于没有被读走的数据个数。

**场景1：fa>fb with no idle cycles in both write and read**

假设：

- 写数据时钟频率fa=80MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120
- 在突发传输过程中，数据都是连续读写的

那么：

- 写一个数据所需要的时间 = 1/80MHz = 12.5ns
- 突发传输中，写完所有数据所需要的时间 = 120*12.5ns = 1500ns
- 读一个数据所需要的时间 = 1/50MHz = 20ns
- 所以写完所有的突发传输数据需要花费1500ns
- 在1500ns内能够读走的数据个数 = 1500ns/20ns = 75
- 所以在1500ns内还没有被读走的数据个数 = 120-75 = 45
- 因此FIFO的最小深度为45

**场景2：fa > fb with idle cycles in both write and read**

假设：

- 写数据时钟频率fa=80MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120
- 每隔1个cycle写一次
- 每隔3个cycle读一次

那么：

- 每隔1个cycle写一次，意味着2个cycle才写一个数据
- 每隔3个cycle读一次，意味着4个cycle才读一个数据
- 写一个数据所需要的时间 = 2*1/80MHz = 25ns
- 突发传输中，写完所有数据所需要的时间 = 120*25ns = 3000ns
- 读一个数据所需要的时间 = 4*1/50MHz = 80ns
- 所以写完所有的突发传输数据需要花费3000ns
- 在3000ns内能够读走的数据个数 = 3000ns/80ns = 37.5
- 所以在3000ns内还没有被读走的数据个数 = 120-37.5 = 82.5
- 因此FIFO的最小深度为83

**场景3：fa < fb with no idle cycles in both write and read**

假设：

- 写数据时钟频率fa=40MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120
- 在突发传输过程中，数据都是连续读写的

那么：

- 由于读数据比写数据要快，因此FIFO只起到过时钟域的作用，FIFO的最小深度为1即可

**场景4：fa < fb with idle cycles in both write and read**

假设：

- 写数据时钟频率fa=40MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120
- 每隔1个cycle写一次
- 每隔3个cycle读一次

那么：

- 每隔1个cycle写一次，意味着2个cycle才写一个数据
- 每隔3个cycle读一次，意味着4个cycle才读一个数据
- 写一个数据所需要的时间 = 2*1/40MHz = 50ns
- 突发传输中，写完所有数据所需要的时间 = 120*50ns = 6000ns
- 读一个数据所需要的时间 = 4*1/50MHz = 80ns
- 所以写完所有的突发传输数据需要花费6000ns
- 在6000ns内能够读走的数据个数 = 6000ns/80ns = 75
- 所以在6000ns内还没有被读走的数据个数 = 120-75 = 45
- 因此FIFO的最小深度为45

**场景5：fa = fb with no idle cycles in both write and read**

假设：

- 写数据时钟频率fa=50MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120

那么：

- 如果读写时钟同源并且无相位差，那么可以不需要FIFO；否则FIFO的最小深度为1

**场景6：fa = fb with idle cycles in both write and read**

假设：

- 写数据时钟频率fa=50MHz
- 读数据时钟频率fb=50MHz
- 突发长度= number of data to be transferred = 120
- 每隔1个cycle写一次
- 每隔3个cycle读一次

那么：

- 每隔1个cycle写一次，意味着2个cycle才写一个数据
- 每隔3个cycle读一次，意味着4个cycle才读一个数据
- 写一个数据所需要的时间 = 2*1/50MHz = 40ns
- 突发传输中，写完所有数据所需要的时间 = 120*40ns = 4800ns
- 读一个数据所需要的时间 = 4*1/50MHz = 80ns
- 所以写完所有的突发传输数据需要花费4800ns
- 在4800ns内能够读走的数据个数 = 4800ns/80ns = 60
- 所以在4800ns内还没有被读走的数据个数 = 120-60 = 60
- 因此FIFO的最小深度为60

**场景7：Data rates are given，read and write random(important!!!)**

​	在前面几种场景中，我们给的条件都是每隔几个时钟读写一次，这种周期性读写在实际中很常见。但是在工程设计中还存在这样一种情形，只给出数据在一段时间内的读写速率，怎么读写完全随机，这种情况我们需要考虑最坏的一种情况避免数据丢失。在最坏的情形中，读写的速率应该相差最大，也就是说需要找出最大的写速率和最小的读速率。

假设：

- 写数据时钟频率fa=80MHz
- 读数据时钟频率fb=50MHz
- 在写时钟周期内，每100个周期就有40个数据写入FIFO
- 在读时钟周期内，每10个周期可以有8个数据读出FIFO

那么：

- 首先这里没有给出数据的突发长度，从假设中可以得出每100个周期就有40个数据写入FIFO，这里可能就有人会说突发长度就是40个数据，其实不是这样的，因为数据是随机写入FIFO的，我们需要考虑做坏的情形，即写速率最大的情形，只有如下图背靠背的情形才是写速率最高的情形，burst length为80

<img src="/images/post_images/verilog_demo_12_fifo/fifo_6.png">

- **注意：这里需要验证一下是否有解**，即写入burst数据时间必须大于等于读出burst数据时间，不然数据就会越累积越多，使得FIFO的深度必须为无穷大。首先写入80个数据需要的时间 = 1/80MHz*(80*100/40)=2500ns，读出80个数据需要的时间 = 1/50MHz*(80*10/8)=2000ns，由于写入burst数据时间大于对出burst数据时间，因此有解。
- 下面来计算FIFO最小深度，连续写入80个数据最快所需要时间 = 1/80MHz * 80 = 1000ns
- 从FIFO中读出一个数据至少所需时间 = (1/50MHz) * (10/8) = 25ns
- 那么在1000ns内能够读出的数据 = 1000ns/25ns = 40
- 在1000ns内没有读出的数据 = 80 - 40 = 40
- 因此FIFO的最小深度为40

### 异步FIFO最小深度计算总结

从上面分析来看，求FIFO的最小深度主要有以下**要点**：

- 在求解之前需要验证一下在允许的最大时间长度内写入的数据量是否等于读出的数据量，保证有解；
- 求FIFO深度需要考虑最坏的情形，读写的速率应该相差最大，也就是说需要找出最大的写速率和最小的读速率；
- 不管什么场景，要确定FIFO的深度，关键在于计算出在突发读写这段时间内有多少个数据没有被读走；
- 由于FIFO空满标志位的判断延迟，在实际应用中需要预留一些余量。

下面我们来推导一下FIFO深度的求解公式，假设：

- 写时钟频率为fwr
- 读时钟频率为frd
- 在写时钟周期内，每m个周期内就有n个数据写入FIFO
- 在读时钟周期内，每x个周期内可以有y个数据读出FIFO

那么：

- 首先必须满足(1/fwr)*(m/n) ≥ (1/frd)*(x/y) 
- **”背靠背“的情形下是FIFO读写的最坏情形，burst长度 B = 2*n**
- 写完burst长度数据最快所需时间 T = (1/fwr) * B
- 从FIFO中读出一个数据至少需要时间 t= (1/frd) * (x/y)
- 在T时间内能够从都走的数据个数 = T/t = B * (frd/fwr) * (y/x)
- 在T时间内还没有读走的数据个数 = B - B * (frd/fwr) * (y/x)
- **因此FIFO的最小深度为 B - B * (frd/fwr) * (y/x)**
- 注意保留一些余量

​    

[  完  ]



