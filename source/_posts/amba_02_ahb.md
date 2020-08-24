---
title: AMBA AHB总线介绍
author: yasheng
img: /medias/featureimages/amba.jpg
toc: true
mathjax: true
summary: 本文整理AMBA AHB总线知识
categories: FPGA-详解
tags:
  - AHB
  - AMBA
abbrlink: 181549677
date: 2020-08-24 23:27:09
password:
layout:
---


## AHB 总线协议介绍

---

 注 : 本文内容收集至网络，[参考文-1](https://blog.csdn.net/ivy_reny/article/details/78144785)、[参考文-2](https://www.cnblogs.com/uiojhi/p/9366884.html)、[参考文-3](https://blog.csdn.net/weixin_30335575/article/details/97205742)

**AHB**总线强大之处在于它可以将微控制器（CPU）、高带宽的片上RAM、高带宽的外部存储器接口、DMA总线控制器，以及各种AHB接口的控制器等连接起来构成一个独立的完整SOC系统，还可以通过AHB-APB桥来连接APB总线系统。

<img src="/images/post_images/amba_02_ahb/amba_ahb_01.png">

### 1、AHB总线基本特性

- Burst传输
- Split事务处理
- 单周期master移交
- 单一时钟沿操作
- 无三态
- 更宽的数据总线配置（64/128）
- 流水线操作
- 可支持多个总线主设备（最多16个）

### 2、AHB总线构成

AHB总线主要由四个部分组成

（1）AHB主设备Master：发起一次读/写操作，某一时刻只允许一个主设备使用总线

（2）AHB从设备Slave：响应一次读/写操作，通过地址映射来选择使用哪一个从设备

（3）AHB仲裁器Arbiter：允许某一个主设备控制总线

（4）AHB译码器Decoder：通过地址译码决定选择哪一个从设备

总线可以分成三组信号  写数据总线（HWDATA）、读数据总线（HRDATA）、地址控制总线（HADDR）

<img src="/images/post_images/amba_02_ahb/amba_ahb_02.png">

### 3、总线组成接口概述

| **Name**     | **Source**       | **To**                     | **Description**                                            |
| ------------ | ---------------- | -------------------------- | ---------------------------------------------------------- |
| HCLK         | clock source     | 各module                   | 总线时钟，上升沿采样                                       |
| HRESETn      | reset controller | 各module                   | 总线复位，低电平有效                                       |
| HADDR[31:0]  | Master           | decodermux to slavearbiter | 32位系统地址总线                                           |
| HTRANS[1:0]  | Master           | mux to slave               | 当前传输类型NONSEQ, SEQ, IDLE, BUSY                        |
| HWRITE       | Master           | mux to slave               | 1为写，0为读                                               |
| HSIZE[2:0]   | Master           | mux to slave               | 每一个transfer传输的数据大小，以字节为单位，最高支持1024位 |
| HBURST[2:0]  | Master           | mux to slave               | burst类型，支持4、8、16 burst，incrementing/wrapping       |
| HPROT[3:0]   | Master           | mux to slave               | 保护控制信号，需要slave带保护功能，一般不用                |
| HWDATA[31:0] | Master           | mux to slave               | 写数据总线，Master到Slave                                  |
| HRDATA[31:0] | Slave            | mux to master              | 读数据总线，Slave到Master                                  |
| HREADY       | Slave            | mux to masterarbiter       | 高：Slave指出传输结束低：Slave需延长传输周期               |
| HRESP[1:0]   | Slave            | mux to masterarbiter       | Slave发给Master的总线传输状态OKAY, ERROR, RETRY, SPLIT     |
| HSELx        | Decoder          | slave                      | slave选择信号                                              |

#### 3.1、AHB从接口模块

<img src="/images/post_images/amba_02_ahb/amba_ahb_03.png">

#### 3.2、AHB主接口模块

| **Name** | **Source** | **To**       | **Description**                            |
| -------- | ---------- | ------------ | ------------------------------------------ |
| HGRANTx  | Arbiter    | 各AHB Master | 仲裁器锁定信号                             |
| HBUSREQx | AHB master | Arbiter      | 总线请求信号                               |
| HLOCKx   | AHB master | Arbiter      | 向仲裁器请求锁定总线，在传输期间不丢失总线 |

<img src="/images/post_images/amba_02_ahb/amba_ahb_04.png">

#### 3.3、AHB 仲裁器模块

用来仲裁来自master的信号，决定那个主设备使用总线

| Name          | Source  | To                   | Description                                                  |
| ------------- | ------- | -------------------- | ------------------------------------------------------------ |
| HBUSREQx      | Master  | arbiter              | master给仲裁器的请求获得总线使用权的请求信号，最多支持16个master |
| HLOCKx        | Master  | arbiter              | 如果一个master希望自己在传输期间不希望丢掉总线，则需要向仲裁器发送这个锁定信号 |
| HGRANTx       | arbiter | master               | 授权信号，当前bus master x的优先级最高。当HREADY和HGRANTx同时为高时，master获取系统总线的权利 |
| HMASTER [3:0] | arbiter | 具有split功能的slave | 仲裁器为每一个master分配的ID，指出哪个主设备正在进行传输，提供进行split的信息，用于地址控制多路选择哪个主设备接入总线。 |
| HMASTLOCK     | arbiter | 具有split功能的slave | 表示当前的master正在执行Locked操作。这个信号和HMASTER有这相同的时序 |
| HSPLITx[15:0] | slave   | arbiter              | 从设备用这个信号告诉仲裁器哪个主设备运行重新尝试一次split传输，每一位对应一个主设备 |

<img src="/images/post_images/amba_02_ahb/amba_ahb_05.png">

#### 3.4、AHB 译码器模块

用来将地址信号，翻译成每个从模块的片选信号，每个从模块会被分配一定的地址空间，通过访问地址决定访问的从设备

<img src="/images/post_images/amba_02_ahb/amba_ahb_06.png">

### 4、总线操作过程

有需要占用总线的主设备向仲裁器发出请求，仲裁器授权给指定的总设备，任一时间周期只有一个主设备可以使用总线，对其指定的从设备进行读写操作。

获得授权的总线开始AHB传输，首先发出地址和控制信号，提供地址信息（**HADDR**）、传输方向、带宽（**HSIZE**）和burst类型信号（**HBURST**）。

总线统一规划从设备的地址（**HSELx**），译码器根据地址和控制信号确定某个从设备与主设备进行通信。数据传输通过**数据总线完成，不出现三态总线**，AHB将读写总线分为读总线和写总线。每次传输包括一个地址和控制周期，一个或多个数据周期。地址和控制周期不能扩展，从设备必须在一个周期内采样地址信号，数据周期可以通过**HREADY**信号扩展。HREADY为低时给传输加入一个等待状态以使从设备获得额外的时间提供或采样数据，从设备通过响应信号HRESP反映传输状态，AHB-APB桥连接APB总线系统时，**PSLVERR**被映射到**HRESP** = ERROR(**PSLVERR -> HRESP[0])。**

#### 4.1 相关传输 控制信号

涉及信号传输类型HTRANS[1:0]、数据尺寸HSIZE[2:0]、Burst传输类型

| **HTRANS[1:0]** | **传输类型** | **Description**                                              |
| --------------- | ------------ | ------------------------------------------------------------ |
| 00              | IDLE         | 主设备占用总线，但没进行传输两次burst传输中间主设备可发IDLE此时就算slave被使能，也不会从总线上获取任何的数据信号。如果此时salve被选中，那么每一个IDLE周期slave都要通过HRESP[1:0]返回一个OKAY响应 |
| 01              | BUSY         | 主设备占用总线，但是在burst传输过程中还没有准备好进行下一次传输一次burst传输中间主设备可发BUSY这时slave不会从总线上收取数据而是等待，并且通过HRESP[1:0]返回一个OKAY响应。需要注意的是，这个transfer需要给出下一拍的地址和控制信号，尽管slave不会去采样。 |
| 10              | NONSEQ       | 表明一次单个数据的传输或者一次burst传输的第一个数据地址和控制信号与上一次传输无关 |
| 11              | SEQ          | burst传输接下来的数据地址和上一次传输的地址是相关的，这时总线上的控制信号应当与之前的保持一致，地址视情况递增或者回环。 |

| **HSIZE[2:0]** | **Size**  | **Description** |
| -------------- | --------- | --------------- |
| 000            | 8 bits    | Byte            |
| 001            | 16 bits   | Halfword        |
| 010            | 32 bits   | Word            |
| 011            | 64 bits   | -               |
| 100            | 128 bits  | 4-word line     |
| 101            | 256 bits  | 8-word line     |
| 110            | 512 bits  | -               |
| 111            | 1024 bits | -               |

| **HBURST[2:0]Burst传输类型** | **类型** | **Description**                                              |
| ---------------------------- | -------- | ------------------------------------------------------------ |
| 000                          | SINGLE   | Single transfer**单个传输数据传输**                          |
| 001                          | INCR     | Incrementing burst of unspecified length **任意长度的数据传输** |
| 010                          | WRAP4    | 4-beat wrapping burst     **打包4拍传输，四个地址是一个回环范围** |
| 011                          | INCR4    | 4-beat increment burst    **4拍传输，地址是增加的**          |
| 100                          | WRAP8    | 8-beat wrapping burst     **打包8拍传输，八个地址是一个回环范围** |
| 101                          | INCR8    | 8-beat increment burst    **8拍传输，地址是增加的**          |
| 110                          | WRAP16   | 16-beat wrapping burst    **打包16拍传输，十六个地址是一个回环范围** |
| 111                          | INCR16   | 16-beat increment burst    **16拍传输，地址是增加的**        |

#### 4.2 没有等待状态的单次传输

<img src="/images/post_images/amba_02_ahb/amba_ahb_07.png">

第一个周期的上升沿，master驱动地址和控制信号；

第二个周期的上升沿，slave采样地址和控制信号，并将HREADY拉高；

如果是写操作，master会在第二个周期的上升沿传输要写入的数据；

如果是读操作，slave会在HREADY信号拉高后将读取的数据写入总线；

第三个周期的上升沿，

如果是写操作，master获取HREADY高信号，表明slave已成功接收数据，操作成功；

如果是读操作，master获取HREADY高信号，表明此时的读数据有效并且接收下来，操作成功。

需要注意，HREADY信号在数据有效期间必须为高，并且延续到第三个周期的上升沿之后，确保master的正确采样。

#### 4.3 有等待时间的单次传输

<img src="/images/post_images/amba_02_ahb/amba_ahb_08.png">

slave可以及时处理master请求，但也可能存在slave太慢不能立即处理的情况。

这时需要让master稍微等一等，需要slave插入一些等待的状态。

如图所示，HREADY信号在第二和第三周期拉低，意在告诉master，slave不能立即处理，需要master等待2个周期。在这里需要注意2点：

​    如果是写操作，master需要在等待期间保持写数据不变，直到本次传输完成；

​    如果是读操作，slave不需要一开始就给出数据，仅当HREADY拉高后才给出有效数据。

#### 4.4 多个单次传输的流水线操作（pipeline）

<img src="/images/post_images/amba_02_ahb/amba_ahb_09.png">

扩展数据周期的一个负效应是必需延长相应的下一笔传输的地址周期。A和C为零等待传输，B加入了一个等待周期，因此相应的C地址周期要进行扩展。

​    第一个周期，master发起一个操作A，并驱动地址和控制信号；

​    第二个周期，slave收到了来自总线的请求，将HREADY信号拉高；

​    第二个周期上升沿后，master发现有操作B需要执行，并且检查到上一周期的HREADY为高，则发起第二个操作B；

​    第三个周期，master获取HREADY信号为高，表示操作A已经完成；

​    第三个周期上升沿后，master发现有操作C需要执行，并且检查到上一周期的HREADY为高，则发起第三个操作C；

​    第三个周期上升沿后，slave由于繁忙插入了一个等待状态，将HREADY拉低；

​    第四个周期，master获取HREADY信号为低，知道slave希望等待，于是master保持和上一拍一样的信号；

​    第四个周期，slave处理完了事务，将HREADY信号拉高，表示可以继续处理；

​    第五个周期，master获取HREADY信号为高，知道slave已经可以处理B操作；

​    第五个周期上升沿后，B操作完成；

​    第六个周期上升沿后，C操作完成。

​    需要注意几点：

​    HREADY在一定程度上表示了slave的pipeline能力，在AHB中是2个pipe，也就是总线上最多存在2个未处理完的transfer。只有当总线上未完成的transfer少于2个时，master才能发起操作。

#### 4.5 打包4拍传输

<img src="/images/post_images/amba_02_ahb/amba_ahb_10.png">

HTRANS用来表示单次传输类型，SEQ的地址和上一次传输的地址是相关的

HADDR是一个4拍回环地址，每个地址之间相差4个字节，HSIZE表示单次传输数据大小为“Word”为4个字节，HBURST为WRAP4。

#### 4.6 无定长数据增量地址传输

<img src="/images/post_images/amba_02_ahb/amba_ahb_11.png">

T1，master传入地址和控制信号，因为是新的burst开始，transfer的类型是NONSEQ；

T2，由于master不能在第二个周期里处理第二拍，所以master使用BUSY transfer来为自己延长一个周期的时间。注意，虽然是延长了一个周期，但是master需要给出第二个transfer的地址和控制信号；

T3，slave采集到了master发来的BUSY，知道master需要等待一拍，所以slave会忽略这个BUSY transfer；

T4，master发起了第二个transfer，因为是同一个burst的第二个transfer，所以transfer的类型是SEQ；

T5，slave将HREADY信号拉低，告诉master需要等待一个周期

 T8时刻完成最后一个transfer。

需要注意的 虽然slave会忽略掉BUSY transfer，但是master也需要给出下一拍的地址和控制信号。

#### 4.7 增量4拍burst

<img src="/images/post_images/amba_02_ahb/amba_ahb_12.png">

地址并不是回环地址，HBURST为INCR4。



​                

​                   

[  完  ]