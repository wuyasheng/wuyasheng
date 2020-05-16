---
title: 数字集成电路设计总结
author: yasheng
img: /medias/featureimages/digital_ic.jpg
toc: true
mathjax: true
summary: 本文整理数字集成电路知识
categories: "\U0001F34E 数字集成电路"
tags:
  - 数字集成电路
  - 总结
password: 1df1854015e31ca286d015345eaff29a6c6073f70984a3a746823d4cac16b075
abbrlink: 2266976213
date: 2020-05-14 09:27:09
layout:
---

## 数字集成电路设计知识总结

---

注 : 本文内容来自课件整理

## 第一章：数字集成电路简介

### 简单的数字集成电路

#### 1 什 么是 **CMOS** （ Complementary MOS ）

**构成**：上拉网络(PMOS)+下拉网络(NMOS) 
**关系**： 相互排斥

#### 2 为什么上拉网络是 PMOS 下拉是 NMOS

由于MOS管阈值电压的存在，如果用NMOS做上拉网络，输出达不到`VCC`的电压，只能达到`Vcc-Vth`，如果用PMOS做下拉网络，输出达不到`GND`的电压，只能达到`Vth`，使得电压不完整。

#### 3 CMOS电路设计

1）首选设计N网络
	--  化成“非”逻辑
	--  与 --> 串联 ， 或--> 并联
2）然后设计P 网络 （ 对偶原理 ）
	N“串” -->P“并”； N“并” --> P“串”
3）尺寸确定
	-- 串联翻倍 ， 串几个 ， 翻几倍
	-- 并联不变

#### 4 CMOS反相器版图侧面图

![image-20200514095200915](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514095200915.png)



## 第二章：MOS device & CMOS Inverter

设计抽象级别：系统级，行为级，门级，晶体管级

### 1 MOS Device

#### 1 阈值电压

![image-20200514095554585](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514095554585.png)

![image-20200514095616232](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514095616232.png)



#### 2 线性区

![image-20200514095806698](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514095806698.png)

#### 3 饱和区

![image-20200514095846386](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514095846386.png)

#### 4 沟道长度调制

实际上，由于VDS 的作用，有效沟道长度发生变化（被调制， 夹断点向源端移动），所以：

![image-20200514100144564](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514100144564.png)

#### 5 短沟道效应

当电场强度小： 速率随电场强度成线性关系
当电场强度大：速率饱和（载流子碰撞—散射效应）

![image-20200514100404749](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514100404749.png)

沟道效应与电流Id的关系

**长沟道效应：平方关系**

**短沟道效应：线性关系**

![image-20200514101054724](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514101054724.png)



#### 6 DIBL对亚阈值的影响

DIBL：漏端感应势垒降低

#### 7 闩锁效应

![image-20200514101355807](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514101355807.png)

![image-20200514101514291](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514101514291.png)

**抗闩锁的方法：**

1）减小Rs和 和Rw  ：均匀且充分设计阱和衬底的电源和地的欧姆接触，并用金属线连接，必要时采用环结构。

2）减小β npn 和β pnp ：加大 加大MOS管源漏区距阱边界的距离，必要时采用伪收集极结构。

**内部电路抗闩锁**

内部一般电路工作电压低，工作电流小，一般采用的方法是：充分且均匀地布置内部一般电路工作电压低，工作电流小，
一般采用的方法是：充分且均匀地布置P型衬底电源的欧姆接触孔和N型衬底地的欧姆接触孔，用金属线直接连接到电源或地。

工作电流较大的器件（单元）或状态同步转换集中的模块，一般采用保护环（N+环或P+ 环）的结构。

**外部电路抗闩锁**

外围电路主要是指输入/输出单元电路，一方面易受高压影响，另一方面工作电流很大。因此，极易发生闩锁效应，通常都
采用双环保护结构，而且保护环上要充分开孔，用金属线直接连到电源或地上。

### 2 CMOS反相器 -- 静态

![image-20200514102043222](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514102043222.png)

#### 1 CMOS反相器特点

1）全电压摆幅（输出逻辑电平与器件尺寸无关 ⇒晶体管可以最小尺寸 ⇒ 无比电路）

2）稳态下总是有到 V dd 或 GND的通道 ⇒ 低输出电阻 (kΩ 量级) ⇒ 大扇出(fan-out) (扇出增大，性能降低)

3）无穷大输入电阻 ⇒ 几乎为0的出入电流

4）稳态下电源到地间无通道 ⇒ 无静态电流

5）传输延迟与负载电容及自身的电阻有关

#### 2 INV传输特性

![image-20200514102800683](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514102800683.png)



**设计CMOS静态电路时**，一般要：增大噪声容限，曲线要对称

![image-20200514103451982](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514103451982.png)

**VDD降低，过渡区变窄**

VDD降低限制：
1，延迟增大
2，VDD与阈值电压差不多时，dc特性受影响大
3，串扰噪声小了，但抗外部噪声干扰能力降低

### 3 CMOS反相器 -- 动态

#### 1 CMOS反相器电容环境

![image-20200514103700176](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514103700176.png)

#### 2 米勒效应

密勒效应（Miller effect）是在电子学中，反相放大电路中，输入与输出之间的分布电容或寄生电容由于放大器的放大作用，其等效到输入端的电容值会扩大1+K倍，其中K是该级放大电路电压放大倍数。

![image-20200514103757755](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514103757755.png)



#### 3 单级INV 驱动CL 速度

1）减小 C L
2）增加 V DD   -->  V DD 增加到一定值后回报很小
3）增加宽长比 W/L
	-- P:N = 2:1最快
​	-- 同时增大P，N管，开始收益大，后期收益小

#### 4 多级INV 驱动CL速度

**等比例放大，inv 链最快**

**f = 4 ，4级inv速度最快**



#### 5 CMOS反相器功耗

**动态功耗**：开关功耗、短路功耗
**静态功耗**：漏电功耗、直流功耗

**开关功耗**
$$
P_{switch} = αC{V_{DD}}^2f_{sw}
$$
α：活动因子，电路导通的概率

**短路功耗**

P管，N管同时导通的瞬间功耗

**漏电功耗**

1）增加 V t ： 多 V t仅在关键电路中使用低 V t
2）增加 V s ：堆栈效果，中的输入矢量控制
3）减少 V b：体偏压

降低漏电功耗

![image-20200514113136236](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514113136236.png)

第二条，添加休眠控制，叠加stack



## 第三章：静态CMOS电路设计

### 1 标准CMOS逻辑

导通能力等效关系

并联：电导相加；串联：电阻相加

**CMOS构成：由关系互斥的 PMOS上拉网络和NMOS下拉网络组成的电路**

![image-20200514113803152](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514113803152.png)

#### CMOS 确定管子的尺寸

串联管子宽度变成2倍，并联不变，使得P ：N = 2 : 1

#### CMOS版图实例

![image-20200514114047411](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514114047411.png)



**注意：尽可能多的有源区接地**

### 2 CMOS传输特性

#### 设计要求

1）技术方法1-- 晶体管的大小

![image-20200514115147928](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514115147928.png)

2）技术方法2 -- 晶体管的顺序

**晚到的信号靠近输出端**

3）技术方法3 -- 等价结构的选择

### 3 逻辑努力

#### 1 单级逻辑门延迟

d  = p + f = p + gh

**逻辑努力  g** -->   原先有逻辑输入/等效反相器输入

![image-20200514120248027](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514120248027.png)

**电力努力  h**  -->  后一级的输入/前一级的输入（负载）

Cout / Cin

**寄生延迟  p**  --> 本及电路的输出端电容/输入端电容

![image-20200514120413217](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514120413217.png)

#### 2 多级逻辑门延迟

**Path Effort:**    F = GH

**Path Logical Effort:**  G = g1g2g3g4...gn

**Path Electrical Effort:**   H = Cout / Cin

f = F^(1/n)是最优的

当存在支路的时候

**F = GBH**

f = F^(1/n)是最优的

b = （Con_Path + Coff_path）/ Con_path

B = b1b2b3b4...bn

**延迟计算**

![image-20200514122202969](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122202969.png)

![image-20200514122308880](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122308880.png)

#### 3 其他情况

1）复合门

![image-20200514122454865](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122454865.png)

2）倾斜门

![image-20200514122525167](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122525167.png)

## 第四章：标准CMOS改进

CMOS的缺点：

1）管子数量多,对提高集成度不利。
2）Pch网络冗余大，电路和版图设计，比较复杂。
3）有n、p个网络
4）输入电容大。

### 1 Pseudo-NMOS logic

（伪NMOS逻辑）因为CMOS 的P 型电路只负责上拉，对负载提供充电电流。而且n 、p电路间有对应的拓扑关系，所以可以只注重n网络的设计和分析方法，而将整个网络的设计和分析方法，而将整个p 型电路简化为一个p

**为保证足够的驱动电流，要求pseudo －NMOS 的p管宽长比大。并要求合适的偏置使其始终处于导通状态。**

![image-20200514122759483](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122759483.png)

![image-20200514122920220](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514122920220.png)



###  2 Transmission Gate

输入端同时驱动源和漏

传输管逻辑为非再生信号，无放大功能 - 信号越来越弱 (用INV再生)

![image-20200514123045848](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514123045848.png)

Differential PT Logic (CPL)

![image-20200514123259552](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514123259552.png)

![image-20200514123242998](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514123242998.png)



PT管解决方法

1）Level Restorer  -- > 反馈补偿电路

2）Multiple V T Transistors -- >多阈值传输管

3）传输门 （TG），互补PMOS和NMOS --> 全电压摆幅

传输管可以搭建多选一电路



TG门改进 --> 将P网络和N网络分开编组

继续改进 --> 差分传输管逻辑(DPTL) 将PMOS再次映射回NMOS

### 3 Differential Casecode Voltage Switch Logic

差分级联电压开关逻辑

将P网络映射回N网络，形成差分逻辑

![image-20200514124059982](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124059982.png)

举例

![image-20200514124247005](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124247005.png)

方法小结

![image-20200514124330801](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124330801.png)

有交叉链的DCVSL

![image-20200514124401571](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124401571.png)

![image-20200514124617282](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124617282.png)



## 第五章：动态CMOS设计

动态CMOS引入，预充电放电模型

### 1 引入NMOS网络开关门

在预充电时，关闭N网络

![image-20200514124813008](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514124813008.png)

问题

1）功耗问题（电流泄露，可以使用补充电流）

2）信号完整性问题（电荷共享）使用时钟驱动晶体管预充电内部节点（以增加面积和功率为代价）

3）回栅问题

4）时钟馈通

5）级联问题（多米诺逻辑）

### 2 多米诺逻辑

![image-20200514125418899](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514125418899.png)

**多米诺逻辑改进**

只有第一级存在footer，以后都没有

差分( 双轨) Domino，提供差分输出

**np-CMOS (Zipper)**

### 3 有比逻辑

略



## 第六章：静态时序电路

什么是无比电路、有比电路？
什么是静态逻辑、动态逻辑？
什么是D触发器？锁存器？

### 1 锁存器和触发器

两种时序逻辑状态机

![image-20200514130002616](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514130002616.png)

锁存器和触发器

![image-20200514130102898](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514130102898.png)



**两级锁存器构成触发器**

**建立时间**Tsu：时钟沿到来之前数据需要保持的时间

**保持时间**Thold：时钟沿到来之后数据需要保持的时间

**传输延迟**Tc-q：触发器从输入到输出的传输延迟

系统时钟周期 T > Tc-q + Tdelay + Tsu

### 2 存储单元

1）静态存储（双稳态电路，两个反向器）

2）动态存储（电容存储，利用电容存储，需要刷新）

**6管SRAM**

![image-20200514130835173](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514130835173.png)

有比电路--高触发强度改写

![image-20200514130906214](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514130906214.png)

无比电路--切断反馈回路型改写

![image-20200514131117322](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514131117322.png)

### 3 触发器

![image-20200514131334309](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514131334309.png)

![image-20200514131243965](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514131243965.png)

### 4 建立时间/保持时间

![image-20200514131510618](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514131510618.png)

**保持时间与时钟信号线有关系**



### 5 时钟问题

多路开关型时钟负载高(4TG)-时钟网络问题严重，可以采用有比电路降低时钟负载-稳定性下降

![image-20200514132038412](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514132038412.png)

时钟负载降低了，但稳定性下降了（因为有比电路！）

**两相不重叠时钟**

![image-20200514132201943](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514132201943.png)

**Power PC Flipflop**

![image-20200514132427265](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514132427265.png)

分析，建立时间，保持时间，传输延迟

![image-20200514132702831](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514132702831.png)



## 第七章：动态时序电路-略

略，未整理

## 第八章：时序问题-简略

时序分类

![image-20200514140216234](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514140216234.png)

### 时钟抖动（jitter）/时钟歪斜（skew）

![image-20200514140352719](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514140352719.png)



### 关键路径

最长路径



## 第九章：加法器-简略

![image-20200513165724002](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200513165724002.png)



### 多位加法器

1）进位链

![image-20200514141423492](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514141423492.png)

进位链优化

曼彻斯特进位链

2）超前进位

![image-20200513165806702](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200513165806702.png)

## 第十章：乘法器-略



## 第十一章：移位寄存器, 译码器, 多路选选择器-略

## 第十二章：存储器-简略

SRAM读写

![image-20200514142254091](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514142254091.png)

![image-20200514142309720](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200514142309720.png)

DRAM

ROM

​            

[  完  ]