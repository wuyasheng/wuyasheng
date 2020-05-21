---
title: 同步时序逻辑设计
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
layout: 
summary: 本文整理同步时序逻辑设计方法
categories: ☸ FPGA
tags:
  - FPGA
  - 同步时序逻辑
abbrlink: 3420608021
date: 2020-04-28 16:27:09
password:
---

## 同步时序逻辑设计

---


注 : 本文内容来自个人学习整理

### 时序电路

**同步时序电路**：只有一个时钟源，也就是说这个电路中的每一个触发器都是zd同时被触发
**异步时序电路**：有多个时钟源，也就是说，每个触发器不是内同时被触发的，有时间先后。

**时序电路结构**

<img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_01.png">

### 同步时序电路分析

1. 首先根据电路图列出，电路的驱动方程、特征方程、输出方程

<img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_02.png">

2. 根据方程可以写出状态表和状态图
3. 根据状态表和状态图，分析电路的实际功能

### 触发器类型

#### D触发器

$$
Q_{n+1}=D
$$

#### JK触发器

$$
Q_{n+1}=J\overline{\text Q_n}+\overline{\text K}Q_n
$$

#### T触发器

$$
Q_{n+1}=T㊉Q_n
$$



### 同步时序电路设计

**设计步骤：**

1. 确定状态和状态图
2. 状态化简
3. 状态分配(编码）
4. 选择触发器类型
5. 状态方程Qn+1及控制输入-J,K,D,T
6. 画出电路
7. 自启动

**实例：**设计同步5进制加法计数器

1. 确定状态及状态图：M5计数器，5个状态:S0,S1,S2,S3,S4在计数脉冲CLK作用下，5个状态周期性变换，在S4状态下进位输出Y

2. 状态化简：5个状态不需要化简

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_03.png">

3. 状态分配（编码）

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_04.png">

4. 选择触发器类型，确定状态方程Q n+1 及输入

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_05.png">

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_06.png">

   根据方程选择合适的触发器类型，分别使用，D触发器、T触发器、JK触发器，确定输入

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_07.png">

5. 电路结构

   <img src="/images/post_images/fpga_syn_sequ_logic/syn_sequ_08.png">

6. 确定所有的状态都能够返回到正常的逻辑当中，避免无法启动的情况发生

​             

​            

[  完  ]