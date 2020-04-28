---
title: 跨时钟域处理
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: false
summary: 本文整理跨时钟域相关问题
categories: ☸ FPGA
tags:
  - FPGA
  - 时钟域
abbrlink: 2284481526
date: 2020-04-28 16:10:09
password:
---

## 跨时钟域处理

---


注 : 本文内容来整理摘抄总结至[参考文1](https://www.cnblogs.com/rouwawa/p/7501319.html)、[参考文2](http://dengkanwen.com/238.html)、[参考文3](https://blog.csdn.net/yc16032399/article/details/100113564)、[参考文4](https://www.jianshu.com/p/e9d0d38649fa)

### **时钟域**

假如设计中所有的触发器都使用一个全局网络，比如FPGA的主时钟输入，那么我们说这个设计只有一个时钟域。假如设计有两个输入时钟，如图1所示，一个时钟给接口1使用，另一给接口2使用，那么我们说这个设计中有两个时钟域。

<img src="/images/post_images/fpga_time_field/time_field_01.png">

### **亚稳态**

触发器的建立时间和保持时间在时钟上升沿左右定义了一个时间窗口，如果触发器的数据输入端口上数据在这个时间窗口内发生变化（或者数据更新），那么就会产生时序违规。存在这个时序违规是因为建立时间要求和保持时间要求被违反了，此时触发器内部的一个节点（或者要输出到外部的节点）可能会在一个电压范围内浮动，无法稳定在逻辑0或者逻辑1状态。换句话说，如果数据在上述窗口中被采集，触发器中的晶体管不能可靠地设置为逻辑0或者逻辑1对应的电平上。所以此时的晶体管并未处于饱和区对应的高或者低电平，而是在稳定到一个确定电平之前，徘徊在一个中间电平状态（这个中间电平或许是一个正确值，也许不是）。如图所示，这就是所谓的亚稳态。

<img src="/images/post_images/fpga_time_field/time_field_02.png">

### 信号亚稳态解决方法

#### 1.  多级寄存器

一般针对单bit控制信号跨越两个异步时钟域传输，可以采用多级寄存器，俗称多打拍。同步电路中的第一拍后也许会产生亚稳态，但是信号有机会在其被第二级寄存以及被其它逻辑看到之前稳定下来。常用的就是对单bit信号打两拍，这也是最简单、最常见的处理方式。

打两拍的方式，其实说白了，就是**定义两级寄存器，对输入的数据进行延拍。**如下图所示。

<img src="/images/post_images/fpga_time_field/time_field_03.png">

应该很多人都会问，**为什么是打两拍呢，打一拍、打三拍行不行呢？**

先简单说下两级寄存器的原理：两级寄存是一级寄存的平方，两级并不能完全消除亚稳态危害，但是提高了可靠性减少其发生概率。总的来讲，就是**一级概率很大，三级改善不大**。这样说可能还是有很多人不够完全理解，那么请看下面的时序示意图：

<img src="/images/post_images/fpga_time_field/time_field_04.png">

data是时钟域1的数据，需要传到时钟域2（clk）进行处理，寄存器1和寄存器2使用的时钟都为clk。假设在clk的上升沿正好采到data的跳变沿（从0变1的上升沿，实际上的数据跳变不可能是瞬时的，所以有短暂的跳变时间），那这时作为寄存器1的输入到底应该是0还是1呢？这是一个不确定的问题。所以Q1的值也不能确定，但至少可以保证，在clk的下一个上升沿，Q1基本可以满足第二级寄存器的保持时间和建立时间要求,出现亚稳态的概率得到了很大的改善。

如果再加上第三级寄存器，由于第二级寄存器对于亚稳态的处理已经起到了很大的改善作用，第三级寄存器在很大程度上可以说只是对于第二级寄存器的延拍，所以意义是不大的。

##### 信号从B到A（慢到快）

<img src="/images/post_images/fpga_time_field/time_field_05.png">

在时钟域B下的脉冲信号pulse_b在时钟域A看来，是一个很宽的“电平”信号会，保持多个clk_a的时钟周期，所以一定能被clk_a采到。经验设计采集过程必须寄存两拍。第一拍将输入信号同步化，同步化后的输出可能带来建立/保持时间的冲突，产生亚稳态。需要再寄存一拍，减少亚稳态带来的影响。一般来说两级是最基本要求，如果是高频率设计，则需要增加寄存级数来大幅降低系统的不稳定性。也就是说采用多级触发器来采样来自异步时钟域的信号，级数越多，同步过来的信号越稳定。

特别需要强调的是，此时pulse_b必须是clk_b下的寄存器信号，如果pulse_b是clk_b下的组合逻辑信号，一定要先在clk_b先用D触发器（DFF）抓一拍，再使用两级DFF向clk_a传递。这是因为clk_b下的组合逻辑信号会有毛刺，在clk_b下使用时会由setup/hold时间保证毛刺不会被clk_b采到，但由于异步相位不确定，组合逻辑的毛刺却极有可能被clk_a采到。一般代码设计如下：

```verilog
always @ (posedge clk_a or negedge rst_n)
	begin
		if (rst_n == 1'b0) 
			begin
			   pules_a_r1 <= 1'b0;
			   pules_a_r2 <= 1'b0;
			   pules_a_r3 <= 1'b0;
			end
		else 
			begin									//打3拍
			   pules_a_r1 <= pulse_b;
			   pules_a_r2 <= pules_a_r1;
			   pules_a_r3 <= pules_a_r2;
			end
	end

assign pulse_a_pos	= pules_a_r2 & (~pules_a_r3);	//上升沿检测
assign pulse_a_neg	= pules_a_r3 & (~pules_a_r2);	//下降沿检测
assign pulse_a		= pules_a_r2;
```

实际上，具体打几拍背后是有时序收敛的理论作支撑的，对于一般的设计而言，打两三拍就已经足够了。

##### 信号从A到B（快到慢）

<img src="/images/post_images/fpga_time_field/time_field_06.png">

如果单bit信号从时钟域A到时钟域B，那么存在两种不同的情况，传输脉冲信号pulse_a或传输电平信号level_a。实际上，在一般情况下只有电平信号level_a的宽度能被clk_b采集到才可以保证系统正常工作。那么对于脉冲信号pulse_a采取怎样的处理方法呢？可以用一个展宽信号来替代pulse_a实现垮时钟域的握手。

主要原理就是先把脉冲信号在clk_a下展宽，变成电平信号signal_a，再向clk_b传递，当确认clk_b已经“看见”信号同步过去之后，再清掉signal_a。代码通用框架如下：

```verilog
module Sync_Pulse (
	input				clk_a,
	input				clk_b,
	input				rst_n,
	input				pulse_a,
	output				pulse_b_out,
	output				b_out		
	 );
/****************************************************/	
	reg					signal_a;
	reg					signal_b;
	reg					signal_b_r1;
	reg					signal_b_r2;
	reg					signal_b_a1;
	reg					signal_b_a2;
/****************************************************/
	//在时钟域clk_a下，生成展宽信号signal_a
    always @ (posedge clk_a or negedge rst_n) begin
        if (rst_n == 1'b0)
            signal_a <= 1'b0;
        else if (pulse_a_in)			//检测到到输入信号pulse_a_in被拉高，则拉高signal_a
            signal_a <= 1'b1;
        else if (signal_b_a2)			//检测到signal_b1_a2被拉高，则拉低signal_a
            signal_a <= 1'b0;
        else;
	end
	//在时钟域clk_b下，采集signal_a，生成signal_b
	always @ (posedge clk_b or negedge rst_n)begin
        if (rst_n == 1'b0)
            signal_b <= 1'b0;
        else
            signal_b <= signal_a;
	end
	//多级触发器处理
	always @ (posedge clk_b or negedge rst_n)begin
        if (rst_n == 1'b0) begin
            signal_b_r1 <= 1'b0;
            signal_b_r2 <= 1'b0;
        end
        else begin
            signal_b_r1 <= signal_b;		//对signal_b打两拍
            signal_b_r2 <= signal_b_r1;
        end
	end
	//在时钟域clk_a下，采集signal_b_r1，用于反馈来拉低展宽信号signal_a
    always @ (posedge clk_a or negedge rst_n) begin
        if (rst_n == 1'b0) begin
            signal_b_a1 <= 1'b0;
            signal_b_a2 <= 1'b0;
        end
        else begin
            signal_b_a1 <= signal_b_r1;		//对signal_b_r1打两拍，因为同样涉及到跨时钟域	
            signal_b_a2 <= signal_b_a1;
        end
    end
	assign	pulse_b_out =	signal_b_r1 & (~signal_b_r2);
	assign	b_out		=	signal_b_r1;
endmodule
```

这样一来，实际上clk_a下的脉冲信号“作用”到了clk_b时钟域下，它对于clk_a与clk_b的时钟频率关系没有任何限制，快到慢，慢到快就都没问题了。

总而言之，在设计中可以简单的牢记以下五条原则：

1. 在全局时钟的跳变沿最可靠。
2. 来自异步时钟域的输入需要寄存一次以同步化，再寄存一次以减少亚稳态带来的影响。
3. 不需要用到跳变沿的来自同一时钟域的输入，没有必要对信号进行寄存。
4. 需要用到跳变沿的来自同一时钟域的输入，寄存一次即可。
5. 需要用到跳变沿的来自不同时钟域的输入，需要用到3个触发器，前两个用以同步，第3个触发器的输出和第2个的输出经过逻辑门来判断跳变沿。

#### 2. 异步FIFO缓存

一般用于跨时钟域传输数据，写端和读端分别对应两个时钟域，由空/满信号控制着读写过程，实现数据的跨域传输。

处理多bit数据的跨时钟域，一般采用异步双口RAM。假设我们现在有一个信号采集平台，ADC芯片提供源同步时钟60MHz，ADC芯片输出的数据在60MHz的时钟上升沿变化，而FPGA内部需要使用100MHz的时钟来处理ADC采集到的数据（多bit）。

在这种类似的场景中，我们便可以使用异步双口RAM来做跨时钟域处理。先利用ADC芯片提供的60MHz时钟将ADC输出的数据写入异步双口RAM，然后使用100MHz的时钟从RAM中读出。

对于使用异步双口RAM来处理多bit数据的跨时钟域，相信大家还是可以理解的。**当然，在能使用异步双口RAM来处理跨时钟域的场景中，也可以使用异步FIFO来达到同样的目的。**

#### 3.  格雷码转换

我们依然继续使用介绍第二种方法中用到的ADC例子，将ADC采样的数据写入RAM时，需要产生RAM的写地址，但我们读出RAM中的数据时，肯定不是一上电就直接读取，而是要等RAM中有ADC的数据之后才去读RAM。这就需要100MHz的时钟对RAM的写地址进行判断，当写地址大于某个值之后再去读取RAM。

在这个场景中，其实很多人都是使用直接用100MHz的时钟于RAM的写地址进行打两拍的方式，但RAM的写地址属于多bit，如果单纯只是打两拍，那不一定能确保写地址数据的每一个bit在100MHz的时钟域变化都是同步的，肯定有一个先后顺序。如果**在低速的环境中不一定会出错，在高速的环境下就不一定能保证了。所以更为妥当的一种处理方法就是使用格雷码转换。**

对于格雷码，相邻的两个数间只有一个bit是不一样的（格雷码，在本文中不作详细介绍），如果先将RAM的写地址转为格雷码，然后再将写地址的格雷码进行打两拍，之后再在RAM的读时钟域将格雷码恢复成10进制。这种处理就相当于对单bit数据的跨时钟域处理了。

十进制与格雷码转换

```verilog
function [WADDRWIDTH:0] GRAY_W(input op,input [WADDRWIDTH:0] addr_in);
	integer i;
	begin
		if(op==ENCODE)
			GRAY_W = (addr_in >> 1) ^ addr_in;
		else if(op==DNCODE) begin
			GRAY_W[WADDRWIDTH] = addr_in[WADDRWIDTH];
			for(i=WADDRWIDTH-1;i>=0;i=i-1)
				GRAY_W[i] = GRAY_W[i+1] ^ addr_in[i];
		end
	end
endfunction
```



#### 4.  握手协议

<img src="/images/post_images/fpga_time_field/time_field_07.png">

使用握手信号“xreq”和“yack”，“系统x”将数据发送给“系统y”。下面是使用握手信号传输数据的例子，如图所示

1. 发送器“系统x”将数据放在数据总线上并发出“xreq”（请求）信号，表示有效数据已经发到接收器“系统y”的数据总线上。

2. 采用两级D触发器缓存，把“xreq”信号同步到接收器的时钟域“yclk”上，得到“yreq2”信号，当采样到yreq2有效后，将数据锁存到系统y的总线上，同时接收器发出“yack”信号（相应信号）。

3. 发送器接收到接收器发送来的“yack”信号，然后将“yack”信号同步到“xclk”时钟域上，同步的原理同上，采用两级的D触发器采样，采样时钟为“xclk”，得到同步后的“xack2”信号，xclk时钟采样到“xack2”有效后，发出下一个数据，同时让“xreq”有效一个时钟（xclk）。

<img src="/images/post_images/fpga_time_field/time_field_08.png">

​      

​       

[  完  ]