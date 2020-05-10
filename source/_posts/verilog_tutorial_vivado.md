---
title: Vivado 基本使用方法
author: yasheng
img: /medias/featureimages/vivado.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理Vivado基本使用方法
categories: ☸ FPGA
tags:
  - FPGA
  - Vivado
abbrlink: 2588731471
date: 2020-04-02 13:27:09
password:
---

## Vivado 基本使用方法

---


注 : 本文内容来基于Vivado 18.3 版本，固化程序[参考文章](https://blog.csdn.net/sinat_15674025/article/details/84535754)

### 1、Vivado  安装

1. 软件下载

   Vivado可以从官网进行下载，[vivado官网下载链接](https://china.xilinx.com/support/download.html)，根据提示进行下载（可能需要注册后才能够下载，注意选择合适的操作系统）

   <img src="/images/post_images/verilog_tutorial_vivado/vivado_01.jpg">

2. 软件安装

   软件下载完成后，需要进行解压，解压后双击 setup.exe 文件，进入安装程序步骤；依次点击 Next ，agree 等，可以进行选择安装的软件版本（免费版，付费版），在部件选择部分选择需要安装的部件，依次下一步即可，最后等待安装完成；

3. 软件激活

   若需要激活全部的功能，可以使用 license 进行激活，[license链接](https://www.lanzous.com/iay3oef)；打开Vivado工具，依次 `help`->`Manage License`，添加license文件，该license适用于任意版本至2037年

   <img src="/images/post_images/verilog_tutorial_vivado/vivado_02.jpg">

### 2、新建工程

打开 Vivado 软件，`File`->`Project`->`New`，或者直接点击屏幕上的Create Project

<img src="/images/post_images/verilog_tutorial_vivado/vivado_03.jpg">

<img src="/images/post_images/verilog_tutorial_vivado/vivado_04.jpg">

选择下一步，填写工程名以及工程路径，并下一步

<img src="/images/post_images/verilog_tutorial_vivado/vivado_05.jpg">

选择`RTL project`，并勾选`Do not... `，并下一步

<img src="/images/post_images/verilog_tutorial_vivado/vivado_06.jpg">

选择自己所要用到的芯片，依次下一步

<img src="/images/post_images/verilog_tutorial_vivado/vivado_07.jpg">

工程新建完成后即打开vivado主界面

<img src="/images/post_images/verilog_tutorial_vivado/vivado_08.jpg">

### 3、添加Verilog等文件

在vivado主界面中，点击左侧菜单栏的 `Add Sources`，在弹出的界面中选择`Add or createdesign sources`，添加或创建verilog文件

<img src="/images/post_images/verilog_tutorial_vivado/vivado_09.jpg">

如图所示，添加或创建新文件

<img src="/images/post_images/verilog_tutorial_vivado/vivado_10.jpg">

如图所示添加的文件及编辑界面

<img src="/images/post_images/verilog_tutorial_vivado/vivado_11.jpg">

添加其他的文件

<img src="/images/post_images/verilog_tutorial_vivado/vivado_12.jpg">

### 4、添加ip

点击vivado左侧菜单栏的 `IP catalog`，会出现右侧的ip器件目录窗口，可以通过搜索选择需要的ip器件

<img src="/images/post_images/verilog_tutorial_vivado/vivado_13.jpg">

双击所要添加的 ip ，进入 ip 配置界面 

<img src="/images/post_images/verilog_tutorial_vivado/vivado_14.jpg">

依次点击下一步，generate等，实例化ip，配置后的ip会出现在下图中的位置

<img src="/images/post_images/verilog_tutorial_vivado/vivado_15.jpg">

可以如下图，打开ip实例化模板，方便在设计中进行配置

<img src="/images/post_images/verilog_tutorial_vivado/vivado_16.jpg">

### 5、仿真

首先创建仿真文件，如创建设计文件步骤一样，创建simulation文件，创建后的文件如图所示

<img src="/images/post_images/verilog_tutorial_vivado/vivado_17.jpg">

当有多个仿真文件时，切换仿真文件，只需在访问文件右键，选择`Set as top`即可

<img src="/images/post_images/verilog_tutorial_vivado/vivado_18.jpg">

仿真文件编写完成后需要进行，进行对工程进行编译

<img src="/images/post_images/verilog_tutorial_vivado/vivado_19.jpg">

编译完成后，没有错误后，即可开始仿真

<img src="/images/post_images/verilog_tutorial_vivado/vivado_20.jpg">

### 6、烧写程序

依次进行综合，打开综合设计，进行端口分配

<img src="/images/post_images/verilog_tutorial_vivado/vivado_21.jpg">

<img src="/images/post_images/verilog_tutorial_vivado/vivado_22.jpg">

端口分配完成后，保存为XDC文件，包括时序约束等，再进行实现`implement`，`Generate Bitstream `

<img src="/images/post_images/verilog_tutorial_vivado/vivado_23.jpg">

Bit 流生成以后需要连接板子，打开板子开关，`open target`->`auto connect`,待板子连接成功后，就可以进行`program Device`，等待烧写成功。（该烧写只一次有效，未固化到flash等期间）

<img src="/images/post_images/verilog_tutorial_vivado/vivado_24.jpg">

 

### 7、固化程序

Vivado固化程序[参考文章](https://blog.csdn.net/sinat_15674025/article/details/84535754)

配置相关设置

<img src="/images/post_images/verilog_tutorial_vivado/vivado_25.jpg">

配置完成后，需要重新编译，实现，生成bit文件，连接板子，右键板子设备，选择存储器件（每个板子的存储器型号）

<img src="/images/post_images/verilog_tutorial_vivado/vivado_26.jpg">

<img src="/images/post_images/verilog_tutorial_vivado/vivado_27.jpg">

添加完成后，Vivado会提示添加完成，是否立即配置存储器。点击OK，进入配置存储器的界面，开始将二进制bin文件烧写到外部配置flash存储器中

<img src="/images/post_images/verilog_tutorial_vivado/vivado_28.jpg">

提醒：如果配置存储器的界面突然找不到，可以右击flash存储器，点击Program Configuration Memory Device，会出现存储器的配置界面。

<img src="/images/post_images/verilog_tutorial_vivado/vivado_29.jpg">

选择生成的bin文件，点击ok进行烧写即可 

注：二进制文件路径为：project_name\project_name.runs\impl_1\xxx.bin。或project_name \project_name.runs\impl_2\xxx.bin。（project_name根据用户工程进行修改）。



[  完  ]