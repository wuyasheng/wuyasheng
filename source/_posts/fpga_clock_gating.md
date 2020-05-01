---
title: 门控时钟总结
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: false
summary: 本文整理门控时钟知识
categories: ☸ FPGA
tags:
  - FPGA
  - 门控时钟
abbrlink: 606381165
date: 2020-04-28 16:35:09
password:
---

## 门控时钟总结

---

注 : 本文内容来整理摘抄总结至[参考文1](https://blog.csdn.net/icxiaoge/article/details/80792819)、[参考文2](https://blog.csdn.net/bleauchat/article/details/96502907)

### 概念解释

**Clockgating：**在数据无效时,将寄存器时钟关闭的技术，能够有效降低功耗， 是低功耗设计的重要方法之一。门控时钟其实就是一个逻辑模块，在寄存器的输入数据无效时，将寄存器的输入时钟置为0，而此时寄存器值保持不变，此时没有时钟翻转，避免了动态功耗。

时钟使能电路是同步设计的重要基本电路，在很多设计中，虽然内部不同模块的处理速度不同，但是由于这些时钟是同源的，可以将它们转化为单一的时钟电路处理。在FPGA的设计中，分频时钟和源时钟的skew不容易控制，难以保证分频时钟和源时钟同相。故此推荐采用使用时钟使能的方法，通过使用时钟使能可以避免时钟“满天飞”的情况，进而避免了不必要的亚稳态发生，在降低设计复杂度的同时也提高了设计的可靠性。

### **如何生存门控时钟**

在IC设计中，编写RTL代码的时候，只要采用合理规范的编码风格，即可以通过EDA工具自行产生门控时钟模块。

<img src="/images/post_images/fpga_clock_gating/clock_gating_01.png">

<img src="/images/post_images/fpga_clock_gating/clock_gating_02.png">

推荐的风格代码在else分支忽略，此时意味着在data_vld无效时data_out保持不变，此时状态与clock保持为0一致，因此具备生成门控时钟的条件，而不推荐风格代码，在data_vld无效时data_out为0，不符合门控时钟的状态，因此无法生成门控时钟。

上图两个电路图均为推荐风格代码的映射电路图，右侧为采用门控时钟设计的电路图。采用推荐风格代码的代码即可以通过EDA工具自行插入门控时钟。

由于门控时钟逻辑具有一定的开销，因此数据宽度过小不适合做clockgating。一般情况下，数据宽度大于8比特时建议采用门控时钟。

实际芯片使用的门控时钟模块是后端厂家专门设计的模块单元，而不仅仅是图上的简单设计。

\---------------------

版权声明：本文为CSDN博主「IC小鸽」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/icxiaoge/article/details/80792819

---------

### 门控时钟比较

**结构1**

<img src="/images/post_images/fpga_clock_gating/clock_gating_03.png">

这种系统时钟门控的机制算然简单，但是容易使门控后的时钟不完整，甚至产生毛刺

**结构2**

<img src="/images/post_images/fpga_clock_gating/clock_gating_04.png">

这种门控方法避免了门控时钟的不完整性，也可以避免避免毛刺的产生，但门控后的时钟可能会产生亚稳态

**结构3**

<img src="/images/post_images/fpga_clock_gating/clock_gating_05.png">

这种结构解决了结构-2的亚稳态问题

**结构4**

<img src="/images/post_images/fpga_clock_gating/clock_gating_06.png">

这种带测试模式的结构可以在测试时让时钟一直开着

**结构5**

<img src="/images/post_images/fpga_clock_gating/clock_gating_07.png">

在ASIC进行后端测试的时候，有时候可能会将不同时钟域的逻辑和寄存器连起来进行扫描链插入，此时可能某个模块的时钟来源可能不是通过它原来的时钟路径，而是整个芯片统一的测试时钟，就此时需要对时钟进行选择

-------------------

注：结构3的门控时钟结构比较好，<font color=red>但是得到的时钟是不建议直接给后续电路使用的，容易产生毛刺，最好在分频时钟加一级寄存器，打一拍，或者这个分频信号做为后续电路的时钟信号！</font>



​                  

[  完  ]