---
title: AMBA总线介绍-引文
author: yasheng
img: /medias/featureimages/amba.jpg
toc: true
mathjax: true
summary: 本文整理AMBA总线知识
categories: ☸ FPGA
tags:
  - 总线协议
  - AMBA
abbrlink: 3342921539
date: 2020-05-19 15:27:09
password:
layout:
---


## AMBA总线协议介绍

---

 注 : 本文内容收集至网络，[参考文-1](https://blog.csdn.net/ivy_reny/article/details/56274412)、[参考文-2](https://blog.csdn.net/ivy_reny/article/details/78144785)、[参考文-3](https://blog.csdn.net/ivy_reny/article/details/56274238)、[参考文-4](https://blog.csdn.net/u013246792/article/details/79840729)、[参考文-5](https://www.cnblogs.com/uiojhi/archive/2018/07/31/9366884.html)、[参考文-6](https://blog.csdn.net/u011280717/article/details/81052436)、[参考文-7](http://blog.sina.com.cn/s/blog_13f7886010102x4t3.html)

## 1 AMBA 概述

**AMBA (Advanced Microcontroller Bus Architecture) 高级处理器总线架构**

**AHB** (Advanced High-performance Bus) 高级高性能总线
**ASB** (Advanced System Bus) 高级系统总线
**APB** (Advanced Peripheral Bus) 高级外围总线
**AXI** (Advanced eXtensible Interface) 高级可拓展接口

这些内容加起来就定义出一套为了高性能SoC而设计的片上通信的标准。

AHB：主要是针对高效率、高频宽及快速系统模块所设计的总线，它可以连接如微处理器、芯片上或芯片外的内存模块和DMA等高效率模块。
APB：主要用在低速且低功率的外围，可针对外围设备作功率消耗及复杂接口的最佳化。APB在AHB和低带宽的外围设备之间提供了通信的桥梁，所以APB是AHB或ASB的二级拓展总线。
AXI：高速度、高带宽，管道化互联，单向通道，只需要首地址，读写并行，支持乱序，支持非对齐操作，有效支持初始延迟较高的外设，连线非常多。

**几种AMBA总线的性能对比分析**

| 总线     | AXI                                                          | AHB                                                          | APB                                                     |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------- |
| 总线宽度 | 8, 16, 32, 64, 128, 256, 512, 1024                           | 32, 64, 128, 256                                             | 8, 16, 32                                               |
| 地址宽度 | 32                                                           | 32                                                           | 32                                                      |
| 通道特性 | 读写地址通道<br/>读写数据通道均独立                          | 读写地址通道<br/>共用读写数据通道                            | 读写地址通道<br/>共用读写数据通道<br>不支持读写并行操作 |
| 体系结构 | 多主/从设备<br/>仲裁机制                                     | 多主/从设备<br/>仲裁机制                                     | 单主设备（桥）/多从设备<br/>无仲裁                      |
| 数据协议 | 支持流水/分裂传输<br/>支持猝发传输<br/>支持乱序访问<br/>字节/半字/字<br/>大小端对齐<br/>非对齐操作 | 支持流水/分裂传输<br/>支持猝发传输<br/>支持乱序访问<br/>字节/半字/字<br/>大小端对齐<br/>不支持非对齐操作 | 一次读/写传输占两个时钟周期<br/>不支持突发传输          |
| 传输方式 | 支持读写并行操作                                             | 不支持读写并行操作                                           | 不支持读写并行操作                                      |
| 时序     | 同步                                                         | 同步                                                         | 同步                                                    |
| 互联     | 多路                                                         | 多路                                                         | 无定义                                                  |

————————————————
版权声明：本文为CSDN博主「ivy_reny」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ivy_reny/article/details/56274412

## 2 AHB总线

 AHB总线规范是AMBA总线规范的一部分，AMBA总线规范是ARM公司提出的总线规范，被大多数SoC设计采用，它规定了AHB (Advanced High-performance Bus)、ASB (Advanced System Bus)、APB (Advanced Peripheral Bus)。AHB用于高性能、高时钟频率的系统结构，典型的应用如ARM核与系统内部的高速RAM、NAND FLASH、DMA、Bridge的连接。APB用于连接外部设备，对性能要求不高，而考虑低功耗问题。ASB是AHB的一种替代方案。

**AHB-Lite Master信号**

AHB-Lite Master提供地址和控制信息来标识读写操作。图1-2显示了一个AHB-Lite Master接口。

<img src="/images/post_images/amba_bus/amba_01.png">

**AHB-Lite Slave信号**

<img src="/images/post_images/amba_bus/amba_02.png">

### 2.1 AHB 总线的架构

​        AHB总线的强大之处在于它可以将微控制器（CPU）、高带宽的片上RAM、高带宽的外部存储器接口、DMA总线master、各种拥有AHB接口的控制器等等连接起来构成一个独立的完整的SOC系统，不仅如此，还可以通过AHB-APB桥来连接APB总线系统。AHB可以成为一个完整独立的SOC芯片的骨架。
​        下图是一个典型的AHB系统总线的结构示意图

<img src="/images/post_images/amba_bus/amba_03.png">

### 2.2 AHB 基本特性

- Burst传输
- Split事务处理
- 单周期master移交
- 单一时钟沿操作
- 无三态
- 更宽的数据总线配置（64/128）
- 流水线操作
- 可支持多个总线主设备（最多16个）

### 2.3 AHB 总线组成

<img src="/images/post_images/amba_bus/amba_04.png">

AHB总线由Master、Slave和Infrastructure构成。Infrastructure由arbiter（仲裁器）、数据多路选择器、地址多路选择器、译码器构成。
**主设备Master**：发起一次读/写操作，某一时刻只允许一个主设备使用总线
**从设备Slave**：响应一次读/写操作，通过地址映射来选择使用哪一个从设备
**仲裁器arbiter**：允许某一个主设备控制总线
**译码器decoder**：通过地址译码决定选择哪一个从设备
总线可以分为三组
  写数据总线（HWDATA）
  读数据总线（HRDATA）
  地址控制总线（HADDR）

### 2.4 AHB 信号描述

<img src="/images/post_images/amba_bus/amba_05.png">

**AHB 仲裁信号**

<img src="/images/post_images/amba_bus/amba_06.png">

### 2.5 总线操作

  有需要占用总线的Master向arbiter发出请求，arbiter授权给指定的master。任一时间周期只有一个master可以接入总线，对其指定的slave进行读写操作。
  获得授权的总线开始AHB传输，首先发出地址和控制信号，提供地址信息、传输方向、带宽和burst类型。总线统一规划slave的地址，译码器根据地址和控制信号确定哪个slave与master进行数据通信。数据传输通过数据总线完成。为避免出现三态总线，AHB将读写总线分开，写数据总线用于从master到slave的数据传输，读数据总线用于从slave到master的数据传输。每笔传输包括一个地址和控制周期，一个或多个数据周期。地址和控制周期不能被扩展，因此slave必须在一个周期内采样地址信号。数据周期可以通过HREADY信号扩展，但HREADY为低时给传输加入一个等待状态以使slave获得额外的时间来提供或采样数据，另外slave通过响应信号HRESP反映传输状态。
  一般情况下master完成完整的burst传输，arbiter才会授权给其他的master接入总线，然而为避免过大的判决延迟，arbiter也可能打断burst传输。在这种情况下master必须再次接入总线以进行中断的burst剩余部分的传输。

### 2.6基本传输

一笔传输由如下两部分组成：
  地址阶段：一个周期
  数据阶段：一个或多个周期，由HBURST信号决定需要几个有效周期，可以由HREADY发出请求延长一个周期。

#### 2.6.1 没有等待状态的single transfer

<img src="/images/post_images/amba_bus/amba_07.png">

第一个周期的上升沿，master驱动地址和控制信号；
第二个周期的上升沿，slave采样地址和控制信号，并将HREADY拉高；
  如果是写操作，master会在第二个周期的上升沿传输要写入的数据；
  如果是读操作，slave会在HREADY信号拉高后将读取的数据写入总线；
第三个周期的上升沿，
  如果是写操作，master获取HREADY高信号，表明slave已成功接收数据，操作成功；
  如果是读操作，master获取HREADY高信号，表明此时的读数据有效并且接收下来，操作成功。
**需要注意**，HREADY信号在数据有效期间必须为高，并且延续到第三个周期的上升沿之后，确保master的正确采样。

#### 2.6.2 slave插入等待状态的single transfer

#### 2.6.3 多个single transfer的pipeline操作

#### 2.6.4 递增burst

#### 2.6.5 wrapping 4-beat burst

#### 2.6.6 递增4拍burst

### 2.7 控制信号

详情查看引文：

————————————————
版权声明：本文为CSDN博主「ivy_reny」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ivy_reny/article/details/78144785

## 3 APB 总线

主要应用在低带宽的外设上，如UART、 I2C，它的架构不像AHB总线是多主设备的架构，APB总线的唯一主设备是APB桥（与AXI或APB相连），因此不需要仲裁一些Request/grant信号。APB的协议也十分简单，甚至不是流水的操作，固定两个时钟周期完成一次读或写的操作。其特性包括：两个时钟周期传输，无需等待周期和回应信号，控制逻辑简单，只有四个控制信号。APB上的传输可用如图所示的状态图来说明。

1、系统初始化为IDLE状态，此时没有传输操作，也没有选中任何从模块。

2、当有传输要进行时，PSELx=1,，PENABLE=0，系统进入SETUP状态，并只会在SETUP状态停留一个周期。当PCLK的下一个上升沿到来时，系统进入ENABLE状态。

3、系统进入ENABLE状态时，维持之前在SETUP状态的PADDR、PSEL、PWRITE不变，并将PENABLE置为1。传输也只会在ENABLE状态维持一个周期，在经过SETUP与ENABLE状态之后就已完成。之后如果没有传输要进行，就进入IDLE状态等待；如果有连续的传输，则进入SETUP状态。

————————————————
版权声明：本文为CSDN博主「ivy_reny」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ivy_reny/article/details/56274412

文章也可以参考：

https://blog.csdn.net/u011280717/article/details/81052436

http://blog.sina.com.cn/s/blog_13f7886010102x4t3.html



## 4 AXI 总线

- 高性能、高带宽、低延迟的片内总线
- 地址/控制和数据相位是分离的，分离的读写数据通道。控制和数据通道分离，就可以不等需要的操作完成，就发出下一个操作，流水线操作，数据吞吐量增加达到提速的作用。
- 单向通道体系结构，使得片上信息流只是以单方向传输，减少了延时，更小的面积，更低的功耗，获得优异的性能。

**AXI的五个通道**  

- 读地址通道，包含ARVALID, ARADDR, ARREADY信号；
- 写地址通道，包含AWVALID，AWADDR, AWREADY信号；
- 读数据通道，包含RVALID, RDATA, RREADY, RRESP信号；
- 写数据通道，包含WVALID, WDATA，WSTRB, WREADY信号；
- 写应答通道，包含BVALID, BRESP, BREADY信号；
- 系统通道，包含：ACLK，ARESETN信号；

**ACLK**为axi总线时钟，**ARESETN**是axi总线复位信号，低电平有效；读写数据与读写地址类信号宽度都为32bit；**READY**与**VALID**是对应的通道握手信号；**WSTRB**信号为1的bit对应WDATA有效数据字节，WSTRB宽度是32bit/8=4bit；**BRESP**与**RRESP**分别为写回应信号，读回应信号，宽度都为2bit，‘h0代表成功，其他为错误。

**AXI 5个独立的通道，基于握手协议**

————————————————
版权声明：本文为CSDN博主「ivy_reny」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/ivy_reny/article/details/56274238
————————————————
版权声明：本文为CSDN博主「FRAWSCCC」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/u011280717/article/details/81052436



内容也可以参考：

https://blog.csdn.net/u011280717/article/details/81052436

​                

​                   

[  完  ]