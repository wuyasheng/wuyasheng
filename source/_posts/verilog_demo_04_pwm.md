---
title: Verilog Demo_4 PWM
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理 PWM 驱动控制
categories: FPGA-例程
tags:
  - Verilog
  - PWM
abbrlink: 1822425848
date: 2020-03-27 22:27:09
password:
---

## Verilog  PWM驱动

---

注 : 本文内容来自整理摘抄至原文[参考文1](https://blog.csdn.net/whik1194/article/details/89371599)

### PWM 驱动控制方法

**原理：**脉冲宽度调制(PWM)，是英文“Pulse Width Modulation”的缩写，简称脉宽调制，是利用微处理器的数字输出来对模拟电路进行控制的一种非常有效的技术。简单一点，就是对脉冲宽度的控制。

PWM的频率：指每秒钟信号从高电平到低电平再回到高电平的次数，

占空比：是高电平持续时间和低电平持续时间之间的比例。

pwm的频率越高，其对输出的响应就会越快，频率越低输出响应越慢。

**方案：**

​		实现方法很简单，使用一个计数器一直计数，然后和两个值进行比较，一个值是高电平时间h_time，一个值是周期period，在小于h_time期间，输出高电平；大于h_time期间，输出低电平，到达周期period时，计数器清零。

<img src="/images/post_images/verilog_demo_04_pwm/pwm.png">

### 代码（PWM周期，占空比可调）

```verilog
module pwm#(
    parameter CLK_FREQ = 50000000
)(
    input 		clk	,//时钟输入，可在外部设置不同时钟
    input 		rst_n,//低电平复位
    input 	[15	:0]	f,//频率控制，最大100KHz
    input 	[7	:0] d,//占空比控制字，上限100,  d%
    output	 	pwm_out	//PWM输出
);
    //假设时钟频率为50MHz，设最大周期时间为1s钟
    reg [25	:0]	count ;	       //计数
    reg [15	:0]	fre;
    reg [7	:0]	duty;
    //修改PWM的周期及占空比参数
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            fre		<= 16'd1;
            duty	<= 8'd1;
        end
        else begin             //此处可以考加上判断计数器回0以后再进行赋值，使得波形更加完整
            fre		<= f;
            duty	<= d;
        end
    end
    //计数器计数
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)	begin
            count	<= 26'd0	;
        end
        else if(count >= CLK_FREQ - 1'b1)		//计数到最大周期清零
            count	<= 26'd0	;
        else
            count   <= count + fre;			//每次累加频率值
    end
    //PWM输出
    assign	pwm_out = (count < CLK_FREQ / 100 *duty)? 1:0;

endmodule
```



[  完  ]

