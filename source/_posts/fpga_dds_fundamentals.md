---
title: DDS基本原理
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理DDS基本原理
categories: ☸ FPGA
tags:
  - FPGA
  - DDS
  - 信号发生器
abbrlink: 3176115177
date: 2020-04-26 18:27:09
password:
---

## 锁相环 基本原理

---


注 : 本文内容来整理总结至[参考文1](https://www.cnblogs.com/zhouzheng/p/5793073.html)、[参考文2](https://www.cnblogs.com/christsong/p/5536995.html)

### DDS介绍

DDS是直接数字式频率合成器（Direct Digital Synthesizer）的英文缩写，与传统的频率合成器相比，DDS具有低成本，高分辨率，低功耗，高分辨率，和快速转换时间等优点，广泛应用在电信与电子仪器领域，是实现设备全数字化的一个关键技术。

DDS建立在采样定理基础上，首先对需要产生的波形进行采样，将采样值数字化后存入存储器作为查找表，然后通过查表读取数据，再经D／A转换器转换为模拟量，将保存的波形重新合成出来。DDS基本原理框图如图所示。

<img src="/images/post_images/fpga_dds_fundamentals/dds_01.png">

除了滤波器(LPF)之外，DDS系统都是通过数字集成电路实现的，易于集成和小型化。系统的参考时钟源通常是一个具有高稳定性的晶体振荡器，为各组成部分提供同步时钟。频率控制字(FSW)实际上是相位增量值(二进制编码)作为相位累加器的累加值。相位累加器在每一个参考时钟脉冲输入时，累加一次频率字，其输出相应增加一个步长的相位增量。由于相位累加器的输出连接在波形存储器(ROM)的地址线上，因此其输出的改变就相当于查表。这样就可以通过查表把存储在波形存储器内的波形抽样值(二进制编码)查找出来。ROM的输出送到D／A转换器，经D／A转换器转换成模拟量输出。

### DDS调频原理

​		如何得到任意频率的波形发生器呢？  因为事先的波形数据是存在ROM里面的，每给rom一个地址，就可以读出ROM里的相应的数据，rom的深度是事先定好的256，地址范围是0-255。所以DDS就是循环读取ROM里的数据，系统时钟是50MHZ，若每个时钟地址加1，则有输出的信号为50M/256=195.3K。此时的频率就是这么大，但是我们不想要这个频率怎么办？

观察上述描述，我们看到最后的输出时钟是由采样时钟和地址深度决定的，所以可以通过对这两个地方着手更改，首先先看下采样时钟，原来是每个时钟的作用下，采集数据加1，现在可以通过对时钟计数，然后让地址在两个时钟作用下采集1，这样就可以实现分频的目的。

说完采样时钟，那么看下地址深度的问题，因为地址深度是由定制ROM时，就已经确认下来的，所以不能直接更改地址深度，但是可通过更改读取ROM的地址，使其只读其中的一半等，也可以达到效果。  有奈奎斯特采样原理我们知道，在做ADDA转换的过程当中，采样频率要大于信号最高频率的两倍，才能保证信号不失真，这个方法说白了其实就是改变采样的点数，然后达到倍频的目的，不过因为这样做，点数采样的比较少，会导致波形失真。

前面两种方法，也只是实现了整数倍频，和分数分频的目的，并不能实现我们所说的任意分频，那么怎么实现任意分频呢，这里就要介绍一种相对简单并且实用的方法。首先需要明确的是输出信号的频率计算公式：
$$
F_{out}=B*\frac{F_{dk}}{2^N}
$$
​		前面介绍的两种方法中，一个是让时钟计数，时钟记多次地址加一下，另一种是地址每次加，但是不是加1 而是2,3,4这样的加，也可以实现分频。不过这两种方法都有各自的坏处，不能实现任意分频，现在我们介绍一种比较简单的调频原理，前面那个公式说了输出信号 = 50M/256。这里的256 = 2^8。所以我们的地址之前定义[7:0]就够了，前面两种都是通过计数的方式实现的调频，现在我们改变下思路，可以通过改变地址位宽的方法，然后读取的时候取数据地址的高八位，是否就可以实现功能。假设我们拓展为32位，最终输出的地址 取高8位。那么输出信号的最低频率是不是 = 50M/2^32 = 0.01164HZ。如果我们想要其他的频率，再在这个基础上乘就行了。怎么乘前面是不是已经讲过了，如果我要得到1K的，1K/0.01164 = 85911，我们让地址计数器每个时钟来了加85911。这样就可以实现比较方便的调频原理。

### DDS调相原理

调相相对来说比较简单，无外乎就是改变初始相位就可以了，实现方法就是定义一个调相控制字，当复位赋初值的时候将想要的初始相位赋给地址初值就可以了，实现起来相对简单。调相计算公式：

 假设是256个点则有:		

```
地址初值 = 256*（初始相位/360）
```

### DDS代码

```verilog
module DDS(
        input        wire    sclk,
        input        wire    rst_n,
        input        wire     [7:0]    odata_1,//sine_wave
        input        wire     [7:0]    odata_2,//square_wave
        input        wire    [1:0]    flag,
        output    wire    [7:0]    addr_num,
        output    reg    [7:0]    o_wave
        );
        
parameter    pword    =0;    
 
reg    [31:0]    cnt;
reg    [1:0]        flag_S;
reg    [1:0]        flag_F;
reg    [31:0]    fword;

    always @(posedge sclk or negedge rst_n)begin
        if(!rst_n)
                flag_S    <=0;
        else    if(flag_S==1 && flag[0])
                flag_S    <=0;
        else    if(flag[0])
            begin
                flag_S<=flag_S+1;
            end 
    end

    always@(posedge sclk or negedge rst_n)
        case(flag_S)
            0:    o_wave    <=    odata_1;
            1:    o_wave    <=    odata_2;
            default:    o_wave    <= o_wave;
        endcase
    
    
    always @(posedge sclk or negedge rst_n)begin
        if(!rst_n)
              flag_F    <=0;
        else if(flag_F==1 && flag[1])
            flag_F    <=0;
        else    if(flag[1])
            begin
                flag_F<=flag_F+1;
        	end 
    end

    always@(posedge sclk or negedge rst_n)
        case(flag_F)
            0:    fword    <= 4295533;
            1:  fword <= 8591065;
            2:  fword <= 42955326;
            3:  fword <= 85910653;
        default: fword <= fword;
     endcase    
 
    always@(posedge sclk or negedge rst_n)begin                                       
        if(~rst_n)                                
            cnt <= {pword,23'b0};                 
        else if(|flag)                        
            cnt <= {pword,23'b0};                 
        else                                      
            cnt <= cnt + fword;                      
    end                                         
                                           
assign addr_num = cnt[31:24];          

endmodule
```

上文DDS代码，输出可以接ROM的地址线，将ROM数据输出至D/A模块，再通过低通滤波器即可得到对应的模拟信号波形       



[  完  ]



