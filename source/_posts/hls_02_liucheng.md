---
title: HLS高级综合开发流程
author: yasheng
img: /medias/featureimages/hls.jpg
toc: true
mathjax: true
summary: 本文整理 HLS 开发流程
categories: FPGA-总结
tags:
  - HLS
abbrlink: 1615252239
date: 2020-08-27 22:08:12
password:
layout:
---


## HLS 开发流程

---

注：本文粘贴至正点原子ZYNQ_HLS开发指南

### 1、HLS综合流程

Vivado High Level Synthesis（即 HLS，高层次综合）工具使用 C、C++或 System C 语言在更抽象的算法层次描述设计，并将 C 代码综合成 RTL 级的 HDL 描述。 使用 Vivado HLS 进行高层次综合的过程如下图所示：

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_01.png">

从图中可以看到，设计的输入是 C 代码 ，Vivado HLS 工具将其综合成使用 VHDL 或者 Verilog语言描述的 RTL 级实现。HLS 综合得到的结果将以 IP 集成的方式添加到 Xilinx 设计工具（如 Vivado）中，并最终综合成网表形式的电路。

### 2、HLS设计流程

#### 2.1 新建工程

首先打开 Vivado HLS 工具，找到 Vivado HLS 2018.3程序，双击打开

点击图示圆圈，打开创建工程向导

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_02.png">

工程文件夹需要自己新建，填入工程名称，点击下一步

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_03.png">

填入顶层函数名称，下一步

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_04.png">

添加/创建文件，可以跳过，直接下一步

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_05.png">

选择自己的器件型号，结束

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_06.png">

打开界面如图所示

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_07.png">

#### 2.2 创建/编写源文件

右键source ，创建新的源文件

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_08.png">

创建src文件夹，编辑文件名，保存到src文件夹即可

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_09.png">

编辑源文件

其中 uint2 表示两位无符号整型数据

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_10.png">

#### 2.3 代码综合

代码编写完成后，保存一下，就可以进行C代码综合

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_11.png">

综合完成后，自动打开综合结果报告

图中接口信号的红框部分，为模块级协议，用于握手，但本实现功能较为简单，不需要，可以使用更简单的协议->ap_ctrl_none,也就是不需要额外的接口；

同样的输出接口控制，led_ap_valid 也不太需要，可以使用ap_none，进行替换 ap_vld;

下面将通过插入指令（DIrective）指导综合过程

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_12.png">

#### 2.4 综合约束

如图所示打开配置

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_13.png">

如图进行源文件的端口设置

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_14.png">

配置完成后，源文件中 额外添加

\#pragma HLS INTERFACE ap_ctrl_none port=return

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_15.png">

然后对输出端口进行设置

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_16.png">

配置如下

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_17.png">

设置完成后如下，再次综合

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_18.png">

本次综合的端口信息如下

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_19.png">

#### 2.5 导出设计

在完成设计后，可以将本次设计导出RTL，如图

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_20.png">

配置导出格式

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_21.png">

导出完成后，以文件夹的形式打包完成

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_22.png">

导出的包可以解压以后，由Vivado调用，使用

### 附录

这是因为 HLS 规定了协议、端口类型和方向之间的相关性，在 Vivado HLS 开发过程中，考虑 C/C++函数参数的类型是很重要的。Vivado HLS 规定可以传入/传出 C/C++函数的值有四种不同的数据类型，分别是：变量、指针、数组和引用。

这也就是说，一种特定的参数类型只对应于有限的几种协议。比如，传入一个数组作为形参输入，能使用的协议就只有：ap_hs、ap_memory、bram、 ap_fifo、ap_bus、axis 和 m_axi，其中 ap_memory 是默认的。参数类型对应支持的接口协议如下图所示：

<img src="/images/post_images/hls_02_liucheng/hls_02_liucheng_23.png">

图中的“D”表示“default”,表示 Vivado HLS 工具默认综合出来的接口。“S”表示“support”，表示 Vivado HLS 工具支持综合出来的接口。

从图中我们可以看出，使用 ap_none 时，当变量作为形参时，接口只能被综合成输入而不能被综合成输出，当指针变量作为形参时，接口可以被综合成输入、输出和双向端口。

本节实验中，我们定义按键 key 的类型为变量，led 的类型指针，从而实现了将按键 key 作为输入接口，led 作为输出接口

​                    

​                         

[  完  ]