---
title: 时序约束详解(1)
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理建立保持时间约束知识
categories: ☸ FPGA
tags:
  - FPGA
  - 时序约束
abbrlink: 3504084194
date: 2020-07-15 16:30:09
layout:
password:
---

## 建立/保持时间约束

---

注 : 本文内容来整理摘抄总结至[参考文1](https://www.cnblogs.com/lilto/p/9581143.html)（详细）、[参考文2](https://www.cnblogs.com/ylsm-kb/p/9129699.html)、[参考文3](https://www.cnblogs.com/yiwenbo/p/11001067.html)

### 概念解释

**建立时间（setup time）**是指在触发器的时钟信号上升沿到来以前，数据稳定不变的时间，如果建立时间不够，数据将不能在这个时钟上升沿被打入触发器；

**保持时间（hold time）**是指在触发器的时钟信号上升沿到来以后，数据稳定不变的时间，如果保持时间不够，数据同样不能被打入触发器。 

**注：Tco 和 Tsetup 是由具体的器件工艺决定的，改变的只有传输延迟Tdelay**

如图所示，数据稳定传输必须满足建立和保持时间的要求，当然在一些情况下，建立时间和保持时间的值可以为零。PLD/FPGA开发软件可以自动计算两个相关输入的建立和保持时间。

<img src="/images/post_images/fpga_time_constraint/time_constraint_01.png">

  **个人理解：**

  1、建立时间（setup time）触发器在时钟沿到来之前，其数据的输入端的数据必须保持不变的时间；**建立时间决定了该触发器之间的组合逻辑的最大延迟。**

  2、保持时间（hold time）触发器在时钟沿到来之后，其数据输入端的数据必须保持不变的时间；**保持时间决定了该触发器之间的组合逻辑的最小延迟。**



### 建立时间/保持时间约束

条件：

Tclk：系统时钟周期

Tco：触发器输出的响应时间，也就是触发器的输出在clk时钟上升沿到来之后多长的时间内发生变化并且稳定，也可以理解为触发器的传输延时

Tdelay：触发器的输出经过组合逻辑所需要的时间，也就是组合逻辑延迟。

Tsetup：建立时间

Thold：保持时间

Tclk：时钟周期

Tskew：时钟线上的延时

#### 建立时间约束

建立时间容限：相当于保护时间，这里要求建立时间容限大于等于0。

<img src="/images/post_images/fpga_time_constraint/time_constraint_02.png">

由上图可知，
$$
建立时间容限=T_{clk}-T_{co}-T_{delay}-T_{setup}
$$
根据要求，建立时间容限≥0，可以得到触发器D2的建立时间
$$
T_{setup}<=T_{clk}-T_{co}-T_{delay}
$$
当存在`Tskew`时
$$
T_{setup}<=T_{clk}+T_{skew}-T_{co}-T_{delay}
$$

#### 保持时间约束

保持时间容限：保持时间容限也要求大于等于0。

<img src="/images/post_images/fpga_time_constraint/time_constraint_03.png">

由图可知
$$
保持时间容限+T_{hold}=T_{co}+T_{delay}
$$
根据要求，保持时间容限≥0，可以得到触发器D2的建立时间
$$
T_{hold}<=T_{co}+T_{delay}
$$
当存在`Tskew`时
$$
T_{hold}<=T_{co}+T_{delay}-T_{skew}
$$
**注：D2的建立时间与保持时间与D1的建立与保持时间是没有关系的，而只和D2前面的组合逻辑和D1的数据传输延时有关，这也是一个很重要的结论。说明了延时没有叠加效应。**



### 有传输延迟下的建立时间和保持时间

事实上Clock的传输也是有延时的，如图所示，两个触发器的源时钟为clka，到达D1需要t1的时间，到达D2需要t2的时间，t2−t1t2−t1其实就是我们常说的clock skew（时钟偏斜），就是同一个时钟沿达到D1和D2的时延差别，如果D1和D2离的很远，那么相应的clock skew就会更大。甚至有前有后

建立时间

<img src="/images/post_images/fpga_time_constraint/time_constraint_04.png">
$$
T_{co}+T_{delay}+ T_setup <= T_{clk}+T_{skew}
$$
保持时间

<img src="/images/post_images/fpga_time_constraint/time_constraint_05.png">
$$
T_{co} + T_{delay} - T_{skew} >= T_{hold}
$$


### 建立/保持时间总结

以下两个公式确定了D2的Tsetup和Thold：

1) D1的Tco + max数据链路延时 + D2的Tsetup < T（即Tsetup< T - Tco - Tdelaymax）

2) D1的Tco + min数据链路延时 > D2的Thold（即Thold< Tco + T2min）

###  最大系统时钟频率

根据建立时间要求，可得
$$
f_{max} = 1/(T_{co}+T_{delay}+T_{setup}-T_{skew})
$$
​         

[  完  ]





