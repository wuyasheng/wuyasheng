---
title: Verilog Demo_11 clk分频
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理clk整数分频方法
categories: ☸ FPGA
tags:
  - Verilog
  - 分频
abbrlink: 4209496447
date: 2020-03-29 14:27:09
password:
---

## Verilog  整数倍分频

---

注 : 本文整理整数分频，占空比50%，内容来自整理摘抄至原文 [参考文1](https://www.cnblogs.com/yuphone/archive/2010/02/08/1666130.html)

### 偶数倍分频（D触发器）

在现实工程设计中一般不采用这样的方式来设计，二分频一般通过 DCM 来实现。通过 DCM得到的分频信号没有相位差。或者是从 Q 端引出加一个反相器，**相当于过一拍取反一次。如果是四分频，就用两个触发器，过两拍取反一次，以此类推**

**也可以由二分频电路级联，组合成2的幂次分频，由1、2、3级触发器组成，各级输出组合即可得到计数器**

**注意，次级D触发器的时钟信号为上一级的输出**

<img src="/images/post_images/verilog_demo_11_clk_div/div_demo.png">

**该种方式会有延迟，慎用**

二分频D触发器实现代码

```verilog
module  clk_div2_dff(
    //system signals
    input					clk 			,
    input					rst_n 			,
    output		reg			clk_out			
);
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            clk_out <= 1'b0;
        else	
            clk_out <= ~clk_out;     
    end
endmodule
```

四分频D触发器实现代码

```verilog
module  clk_div4_dff(
    //system signals
    input					clk 			,
    input					rst_n 			,
    output		reg			clk_out			
);
    reg clk_r0;
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)begin
            clk_out <= 1'b0;
            clk_r0	<= 1'b0;
        end  
        else begin
            clk_r0  <= clk_out;
            clk_out <= ~clk_r0;
        end
    end
endmodule
```

### 3分频（D触发器）

用JK-FF实现3分频很方便，不需要附加任何逻辑电路就能实现同步计数分频。

但用D-FF实现3分频时，必须附加译码反馈电路，如图所示的译码复位电路，强制计数状态返回到初始全零状态，就是用NOR门电路把Q2，Q1=“11B”的状态译码产生“H”电平复位脉冲，强迫FF1和FF2同时瞬间(在下一时钟输入Fi的脉冲到来之前)复零，于是Q2，Q1=“11B”状态仅瞬间作为“毛刺”存在而不影响分频的周期，**这种“毛刺”仅在Q1中存在，实用中可能会造成错误，应当附加时钟同步电路或阻容低通滤波电路来滤除**，或者仅使用Q2作为输出。D-FF的3分频，还可以用AND门对Q2，Q1译码来实现返回复零。**该种方式为计时器计数，然后在合适的位置置0，并非50%的占空比**

<img src="/images/post_images/verilog_demo_11_clk_div/div3_dff_01.png">

<img src="/images/post_images/verilog_demo_11_clk_div/div3_dff_02.png">

## 计数分频

### 偶数倍分频

偶分频比较简单，有两种方法，假设为N分频，

1. 只需计数到N/2－1，然后时钟翻转、计数清零，如此循环就可以得到N（偶）分频。
2. 计数到N-1，当计数值小于N/2时，输出置0，反之，输出置1.

### 偶数分频代码（方法 2）

```verilog
module clk_div_even (	
	input			clk 		,
	input			rst_n 		,
	output		reg	clk_out			
);
parameter	DIV_NUM	= 16'd4;
reg 	[15:0]	cnt	;
//计数器
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		cnt	<= 16'd0;
	else if(cnt == DIV_NUM - 1'b1)
		cnt	<= 16'd0;
	else
		cnt	<= cnt + 1'b1;	 		
end
//输出
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		clk_out <= 1'b0;
	else if(cnt < (DIV_NUM >> 1))
		clk_out <= 1'b0;
	else
		clk_out <= 1'b1;	
end
endmodule
```



### 奇数倍分频

奇数倍分频，采用上升沿与下降沿检测的方法，进行分频，将分频结果相与即可

3分频时序图

<img src="/images/post_images/verilog_demo_11_clk_div/div_3.png">

5分频时序图

<img src="/images/post_images/verilog_demo_11_clk_div/div_5.png">

### 奇数分频代码

```verilog
module clk_div_odd (
	input		clk ,
	input		rst_n ,
	output		clk_out			
);
parameter	CLK_NUM	= 16'd3	;

reg 	[15:0]	cnt_pose			;//上升沿计数
reg 	[15:0]	cnt_nege			;//下降沿计数

reg 		clk_pose		;
reg 		clk_nege		;

//上升沿计数器
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		cnt_pose <= 16'd0;
	else if(cnt_pose == CLK_NUM - 1'b1)
		cnt_pose <= 16'd0;
	else
		cnt_pose <= cnt_pose + 1'b1;
end
//下降沿计数器
always @ (negedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		cnt_nege <= 16'd0;
	else if(cnt_nege == CLK_NUM - 1'b1)
		cnt_nege <= 16'd0;
	else
		cnt_nege <= cnt_nege + 1'b1;
end

always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		clk_pose <= 1'b0;
	else if(cnt_pose < CLK_NUM >> 1)//相当于减一除以2
        clk_pose <= 1'b0;
    else
    	clk_pose <= 1'b1;
end
    
always @ (negedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		clk_nege <= 1'b0;
	else if(cnt_nege < CLK_NUM >> 1)//相当于加一除以2
        clk_nege <= 1'b0;
    else
    	clk_nege <= 1'b1;
end
    
assign clk_out = clk_nege & clk_pose;
endmodule

```

## 另外的分频方法

### a. 交错法

交错法其实就是用两种整数分频交替出现使得总结果与原非整数分频等效

举个例子，要实现8.3分频，那么就可以用x次8分频和y次9分频交替来实现。满足(8x+9y)/(x+y)的一切x和y都可以。所以x=7，y=3就满足条件。即7次8分频和3次9分频交替出现。

设原时钟频率为f，即1s有f个时钟脉冲，8.3分频就是实现8.3s有f个时钟脉冲。7次8分频的结果为56s有7f个时钟脉冲，3次9分频的结果为27s有3f个时钟脉冲，合起来就是83s有10f个时钟脉冲，即8.3s有f个时钟脉冲，就实现了与8.3分频等效。

### b. 累加法

类加法就是用一个计数器来实现非整数分频

设要实现a/b分频（a>b），设一个计数器x从0开始自加，每个clk自加b. x>=a/2时cllkout = ~clkout，x>=a时clkout = ~clkout同时x <= x - a（变相的求余数）。记住一定要对x的每种状态都做上述的判断，这样输出的clkout才会是a/b分频。

举个例子，仍然是8.3分频，需要先把8.3写成166/20，不写成83/10是因为83不能被2整除。那么寄存器x的两个阈值就分别为83和166，自加步长为20，分频器就完成了。下面验证clkout的是不是8.3分频。

x的自加步长为20，那么自加166次本来的应该20*166，花费的时间是166clk. **因为每到大阈值就取一次余数**因此自加166次共计有20*166/166=20次到达过大阈值。因此这166次自加共计输出20个时钟脉冲，即166clk有20个时钟脉冲，即8.3clk有1个时钟脉冲，所以8.3s有f个时钟脉冲，8.3分频没有问题。



## 波形绘制软件

时序图使用 TimeGen 绘制，[下载地址](http://www.xfusionsoftware.com/index.html)  

 时序图绘制： [WaveDrom官方网站](https://wavedrom.com/)

[  完  ]