---
title: Verilog 命名规范
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理按verilog命名规范
categories: ☸ FPGA
tags:
  - Verilog
  - 命名规范
abbrlink: 478174874
date: 2020-03-27 16:27:09
layout:
password:
---

## Verilog  命名规范

---

注 : 本文内容来自整理摘抄至原文[关于Verilog代码中命名的六大黄金规则](https://blog.csdn.net/li_qcxy/article/details/8299691)

### 

**关于Verilog代码中命名的六大黄金规则**

### 1. 系统级信号的命名。

系统级信号指复位信号，置位信号，时钟信号等需要输送到各个模块的全局信号；系统信号以字符串Sys开头。

### 2.低电平有效的信号

在信号名称后一律加下划线和字母n。如：SysRst_n；FifoFull_n；

### 3.经过锁存器锁存后的信号

后加下划线和字母r，与锁存前的信号区别。如CpuRamRd信号，经锁存后应命名为CpuRamRd_r。低电平有效的信号经过锁存器锁存后，其命名应在_n后加r。

如CpuRamRd_n信号，经锁存后应命名为CpuRamRd_nr多级锁存的信号，可多加r以标明。如CpuRamRd信号，经两级触发器锁存后，应命名为CpuRamRd_rr。

### 4.模块的命名

在系统设计阶段应该为每个模块进行命名。

命名的方法是，将模块英文名称的各个单词首字母组合起来，形成3到5个字符的缩写。若模块的英文名只有一个单词，可取该单词的前3个字母。各模块的命名以3个字母为宜。例如：Arithmatic Logical Unit模块，命名为ALU。Data Memory Interface模块，命名为DMI。Decoder模块，命名为DEC。

### 5.模块之间的接口信号的命名

所有变量命名分为两个部分，第一部分表明数据方向，其中数据发出方在前，数据接收方在后，第二部分为数据名称。两部分之间用下划线隔离开。第一部分全部大写，第二部分所有具有明确意义的英文名全部拼写或缩写的第一个字母大写，其余部分小写。

举例：CPUMMU_WrReq，下划线左边是第一部分，代表数据方向是从CPU模块发向存储器管理单元模块（MMU）。下划线右边Wr为Write的缩写，Req是Request的缩写。两个缩写的第一个字母都大写，便于理解。整个变量连起来的意思就是CPU发送给MMU的写请求信号。模块上下层次间信号的命名也遵循本规定。若某个信号从一个模块传递到多个模块，其命名应视信号的主要路径而定。

### 6.模块内部信号

模块内部的信号由几个单词连接而成，缩写要求能基本表明本单词的含义；

单词除常用的缩写方法外（如：Clock-》Clk， Write-》Wr， Read-》Rd等），一律取该单词的前几个字母（ 如：Frequency-》Freq， Variable-》Var 等）；每个缩写单词的第一个字母大写；若遇两个大写字母相邻，中间添加一个下划线（如DivN_Cntr）；

举例：SdramWrEn_n；FlashAddrLatchEn.

​                      

摘自：https://blog.csdn.net/li_qcxy/article/details/8299691

​                       

[  完  ]