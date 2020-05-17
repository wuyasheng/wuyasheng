---
title: 数字电路系统总结
author: yasheng
img: /medias/featureimages/digital_circuits.jpg
toc: true
mathjax: true
summary: 本文整理数字电路与系统知识
categories: 🍎 数字电路
tags:
  - 数字电路
  - 总结
abbrlink: 2205922468
date: 2020-05-13 14:27:09
layout:
password: 1df1854015e31ca286d015345eaff29a6c6073f70984a3a746823d4cac16b075
---

## 数字电路与系统知识总结

---

注 : 本文内容来自课件整理

## 第一章：数字逻辑基础

### 1.1数字电路

自然界的物理量，按其变化规律可分为两类：

**模拟量Analog** ：数值和时间都可以连续取值
**数字量 Digital**  ：时间上离散，值域内只能取某些特定值

### 1.2数制

**基数** ：一个数字系统中 数的个数 称为基数。 （radix or base） 

数制系统
十进制 		decimal (r =10)
二进制 		binary (r =2)
八进制 		octal (r =8)
十六进制 	hexadecimal (r =16)

### 1.3 数制间转换

1）γ  进制转换成十进制:
将 γ  进制的数按权展开，就实现了γ  进制转换成十进制。
$$
(111001.01)_2 = (1*2^5+1*2^4+1*2^3+1*2^0+1*2^{-2})_{10}=(57.25)_{10}
$$
2）十进制转换成 γ  进制:

1. 整数部分，除以 γ  取余，直到商为0 为止，逆序；
2. 小数部分，乘以 γ  取整，顺序。

十进制转成二进制---->举例： 将 (39.2) 10  转换成二进制数

![image-20200513151637365](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_01.png)

![image-20200513151800861](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_02.png)

### 1.4代码

**8421 BCD 码**：用4 位二进制数表示1 位十进制数。 **有权码**
BCD  码还包括 2421BCD, 4221BCD, 5421BCD 等，这些BCD 码也都是有权码。

**格雷码**：任意两个相邻码之间只有一位不同，格雷码是一种无权码。

### 1.5带符号的二进制码

#### 1 原码、反码、补码

**原码**：二进制码

**反码**：二进制码全部取反，0变1,1变0

**补码**：反码加1

**由原码直接求补码**：从右侧数第一个1 不动，向左依次求反。
原码 1101   -->   补码 0011
反码求反为原码
补码求补为原码

#### 2 正负数表示

最左侧一位为符号位：0表示正数、1表示负数

**正数**的表示法不变，**负数**的表示法，在原码，反码，补码首位加1
$$
-13 = -（1101）_2
$$
**原码表示：**1,1101

**反码表示：**1,0010

**补码表示：**1,0011

建立原码、补码等负数的不同表示方法,是为了计算机运算方便,快速。用补码作减法,可以把减法变加法。这样计算机中只有二进制加法器和求补电路来进行加法和减法运算

![image-20200513153124988](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_03.png)

#### 3 偏移码

偏移码的构成：补码的符号位取反



## 第二章：逻辑门电路

### 2.1 概述

用以实现逻辑运算单元电路称为逻辑门电路。

二进制系统中，高电平为1，低电平为0

### 2.2 逻辑门电路

#### 1 与门（AND）

![image-20200513153458415](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_04.png)
$$
逻辑表达式：F=A*B
$$

#### 2 或门（OR）

![image-20200513153559887](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_05.png)
$$
逻辑表达式：F=A+B
$$

#### 3 非门（NOT）

![image-20200513153657536](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_06.png)

#### 4 与非门（NAND）

![image-20200513153941632](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_07.png)

#### 5 或非门（NOR）

![image-20200513153959395](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_08.png)

#### 6 异或门（XOR）

![image-20200513154025535](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_09.png)

#### 7 同或门（XNOR）

![image-20200513154227490](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_10.png)

#### 8 三态门（TSL）

![image-20200513154302228](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_11.png)

#### 9 传输门（TG）

![image-20200513154453735](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_12.png)



### 2.3 TTL 集成门

TTL集成门存在门槛电压，并不能达到VCC、GND

**输入端悬空表示高电平，输入端接电阻大于1000欧也视为高电平**

**CMOS门电路时输入端特别注意不能悬空**

**灌电流**：输出为0时，后端负载倒灌的电流；TTL  不能带过多负载门。驱动门的最大容流 许灌电流 16 mA.

**扇出系数**：输出为1时，对后端负载输出的电流；驱动门最大允许拉电流400μA

**OC门需要外接上拉电阻**

### 2.4  MOS逻辑门电路

MOS  逻辑电路的基本单元为MOSFET

包括，N-MOS电路，CMOS电路



## 第三章：逻辑代数基础

### 3.1 逻辑代数运算法则

![image-20200513160824189](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_13.png)

#### 1 基本定律

![image-20200513161019120](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_14.png)

#### 2 基本规则

**带入规则**：等式两侧某一变量都用一个逻辑函数代入，等式仍成立。

**反演规则**：将一个函数表达式 F  中所有的“与”(·)换成“或”(+)，“或”(+)换成“与”(·)；“0” 换成“1”， “1” 换成“0” ；原**变量换成反变量，反变量换成原变量**，则所得到的逻辑函数即F 的反函数，表达式为F-。

**对偶规则**：将一个函数表达式 F  中所有“与”(·)换成“或”(+)，“或”(+)换成“与”(·)；“0” 换成“1”， “1” 换成“0” ；则所得到的逻辑函数即F 的对偶式，表达式为 F’ 

**多变量异或**，变量为 1  的个数为奇数，异或结为 果为 1 ；1  的个数为偶数，结果为 0  ；与变量为 0的个数无关。



### 3.2 逻辑函数的标准形式 

#### 1 最小项及标准与或式

![image-20200513161708971](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_15.png)

#### 2 最大项及标准或与式

![image-20200513161756463](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_16.png)

#### 3 两种标准式间的关系

1）最小项和最大项互为反函数

![image-20200513161846872](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_17.png)

2）不在最小项中出现的编号, 一定出现在最大项的编号中

![image-20200513161917577](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_18.png)

### 3.3 逻辑函数的公式化简

公式法化简

运用逻辑代数运算法则进行运算化简

### 3.4 卡诺图化简逻辑函数

使用画卡诺图的方法进行化简

#### 1 卡诺图

卡诺图 (K-map) 与真值表相似，可以给出输入所有可能组合所对应的输出值  。与真值表不同的是卡诺图是由小格构成。每个小格代表一个二进制输入的组合。

n  个变量的 卡诺图中有2 n 个 小格,  每个小格表示一个最小项。

**几何相邻**:位置相邻
**逻辑相邻**:只有一个变量变化

#### 2 求最简与或式

方法 : 圈相邻格中的1, 合并最小项

![image-20200513162532627](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_19.png)

#### 3 求最简或与式

尽可能多的把相邻矩形中 2 n 个 个0  圈在一起, 消 去变化了的n  个变量,  留下不变的变量, ( 是0 写 原变量, 是 是 1  写反变量) 组成 或项;  每个圈中至少有一个别的圈没圈过的0,  所有0  都要圈, 0  可重复圈,  圈之间为 与

![image-20200513162900309](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_20.png)

#### 4 具有随意项化简

实际逻辑电路中,  有些变量( 输入) 组合不会出现或不允许出现,  如 BCD  码中 1010~1111; 这些组合对输出不产生任何影响( 是 1 是 0  不影响输出), 这种组合称  “随意项” （ （Don’t care）

**化简时,  根据化简需要, φ 可作1  或作 作0;但不能既当1  同时又当0**

#### 5 引入变量卡诺图

一般，变量超过5 个时，采用引入变量卡诺图方法化简逻辑函数。将n  变量函数中一个变量作为引入变量，填入(n-1)  变量卡诺图中。

![image-20200513163454627](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_21.png)



## 第四章：组合逻辑电路

**组合逻辑特点**：任何时刻输出仅取决于当时的输入、由门电路构成、无反馈线( no memory)

### 4.1  组合电路分析

分析:已知电路,求输出(F),分析电路功能，步骤
1. 从输入端到输出端,逐级写出各逻辑门的输出:
2. 化简逻辑函数
3. 列出真值表
4. 分析电路功能

### 4.2 组合逻辑电路设计

组合逻辑设计步骤：
1. 确定输入、输出及它们的关系
2. 列出真值表
4. 得出函数的最简形式（通过卡诺图或其他方法）
5. 画出电路图

### 4.3 译码器

#### 1  74138 3线-8线译码器

![image-20200513164300226](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_22.png)

#### 2 使用译码器实现组合逻辑电路

1）高电平译码器+或门

![image-20200513164612275](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_23.png)

2）低电平译码器+与非门

![image-20200513164736234](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_24.png)

3）低电平译码器+与门

![image-20200513164840057](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_25.png)



#### 3 BCD转十进制译码器

略

#### 4 显示译码器

略

### 4.4 多路选择器

MUX 功能: 在多路输入数据中选择一路进行输出

使用多路选择器实现组合逻辑，最小项

### 4.5  比较器

基本功能: 比较两个二进制数的大小

### 4.6 加法器

#### 1）半加器

S = A ⊕ B
C = AB

#### 2）全加器

S = A ⊕ B ⊕ C
C_i+1 = AB + AC_i + BC_i

#### 3）并行加法器

1. **串行进位**

![image-20200513165612599](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_26.png)

2. **超前进位**

![image-20200513165724002](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_27.png)

![image-20200513165806702](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_28.png)



### 4.7 组合逻辑电路的竞争冒险

**竞争**： 从输入到输出的途径不同，延时时间不同，到达输出端的时间不同，这种现象为竞争。
**冒险**： 竞争结果导致逻辑电路产生错误输出，称为冒险或险象。

#### 1 竞争冒险的分类与判别

**代数法**：逻辑函数表达式中，若某个变量同时以原变量和反变量两种形式出现，就具备了竞争条件。去掉其它变量，留下有竞争能力的变量，如果表达式为：F=A+A’，就会产生“0”冒险；F=A*A‘，就会产生“1”冒险。

**卡诺图法**: 只要在卡诺图中存在两个相切但不相交的圈（静态-1型冒险是1构成的圈，静态-0型冒险是0构成的圈），就会产生冒险。

#### 2 竞争冒险消除方法

1）输出端接入滤波电容；

2）增加冗余项，修改逻辑设计；

3）引入取样脉冲

4）利用可靠性编码；



## 第五章：触发器

**触发器**定义:能储存一位二进制信息的基本单元。记忆元件，它可以存储一位二进制信息,也称为锁存器( Latch)

### 5.1 基本 RS- 触发器（锁存器）

#### 1 与非门构成的基本RS-FF

![image-20200513170728812](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_29.png)

![image-20200513170917723](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_30.png)

#### 2 或非门构成的基本RS-FF

![image-20200513171041905](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_31.png)

### 5.2 时钟 FF ( 同步 FF)（锁存器）

在数字系统中，为协调各部分动作，需要某些FF在同一时刻动作。引入一同步信号，使这些 FF 只有在同步信号到达时才按输入信号改变状态。同步信号被称时钟脉冲

#### 1 时钟RS-FF

![image-20200513171233544](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_32.png)

#### 2 同步D-FF

![image-20200513171447879](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_33.png)

![image-20200513190538367](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_34.png)

#### 3 同步JK-FF

![image-20200513175022550](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_35.png)

![image-20200513175110210](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_36.png)

#### 4 同步T-FF

![image-20200513175219399](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_37.png)



#### 5 同步触发器的缺点

在 CLK=1 期间, FF 处于触发状态  ，Q n+1  随着输入信号 R, S, D, J, K, T  的变化而变化，出现 空翻 现象。

一个 CLK  周期内，Q  端只能变化一次，变化一次以上称为触发器的空翻。



### 5.3 主从-FF （触发器）

#### 1 主从RS触发器

![image-20200513175444443](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_38.png)



#### 2 主从 JK-FF

![image-20200513175547670](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_39.png)



#### 3 触发器的直接输入

同步输入: CLK, J, K, D, T, R, S
直接置位输入：set 0、set 1

#### 4 主从 D-FF

![image-20200513175821652](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_40.png)

#### 5 主从 T-FF

![image-20200513175851188](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_41.png)

### 5.4 正边沿触发器

略

### 5.5  触发器之间的转换

JK - FF  -->   D - FF

JK - FF   -->  T - FF

T - FF    -->   D - FF

T - FF  转成 JK - FF

D - FF  转成 JK - FF

D - FF  转成 T - FF

### 5.6 触发器应用

1 分频电路

2 RS触发器可以防跳变噪声，用于按键消抖



## 第六章：时序逻辑电路

分为同步时序逻辑和异步时序逻辑

![image-20200513180634580](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_42.png)

### 6.1 同步时序电路分析

1）确定输入输出
2）输出方程、驱动方程、特征方程
3）状态表和状态图
4）电路功能

### 6.2 同步时序电路设计

1) 确定状态和状态图
2) 状态化简
3) 状态分配(编码)
4) 选择触发器类型
5) 状态方程Q及控制输入-J,K,D,T
6) 画出电路
7) 自启动

### 6.3 计数器

#### 1 集成计数器 74161

二进制同步模16 加法计数器，同步置数、异步清0 功能.

#### 2 集成计数器 74163

二进制同步模16 加法计数器，同步清0 ，其它与 74161 相同

#### 3 集成计数器 74160

二进制模10,  其它与74161 相同. 异步清0

#### 4 集成计数器 74290

模 2-5-10  异步计数器

### 6.4 寄存器

寄存器用于寄存一组二进制代码。主要由触发器构成。储 一个触发器能够存储 1  位二进制代码，所以用 n  个触发器组成的寄存器可以存储一组 n  位二进制信息。寄存器广泛地应用于各类数字系统和数字计算机中。移位型寄存器在数字通信中的应用极其广泛。如在计算机串行数据通信中，需要发送的信息总是先放在发送端的移位寄存器中，然后由移位寄存器将其逐位移出。接收端的寄存器逐位从线路上接收信息，收完一个完整的数据后才从移位寄存器中取走数据。

#### 寄存器分类

1）并入并出

2）左/右移串入/串出

3）左/右串入/并出

4）左/右移环型寄存器

5）左/右移扭环寄存器

### 6.5  集成寄存器

### 6.6 序列信号发生器

Counter-type 计数型
Shift-type 移位型

计数器 + 对路选择器



## 第七章：脉冲电路

作用在电路中短暂的电压或电流信号叫做脉冲信号。  （既非直流又非正弦交流的电压或电流）

**脉冲参数：**

**脉宽(Tw)** :半高宽(脉冲最大值一半时的宽度
**幅度(V)**   :电压变化最大幅度
**周期(T)**   :两相邻脉冲间间隔
**频率(f)**    :1/T
**占空比**(q):脉冲中高电平/脉冲中有效宽度

### 7.1 555定时器

![image-20200513182725262](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_43.png)

![image-20200513182743989](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_44.png)

### 7.2 施密特触发器

连接555定时器的两输入端即可

### 7.3 单稳态触发器

借助电容的储能原理

![image-20200513182926193](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_45.png)

**暂稳态持续时间** 是电容C 充电到 2/3 Vcc所用时间    **1.1RC**

**单稳态触发器的恢复时间**(recovery time):T R = (3~5)RonC = 4RonC

### 7.4 多谐振荡器

产生矩形波的自激振荡器

两个不稳定状态，无触发信号，周期性从一个状态转移至另一个状态

![image-20200513183349610](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_46.png)

![image-20200513183410147](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_47.png)



## 第八章：数字系统设计-略

算法状态机 —ASM 

## 第九章：AD/DA转换

### 9.1 数模转换DAC电路

#### 1 权电阻网络DAC

![image-20200513183859270](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_48.png)

![image-20200513184006316](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_49.png)

#### 2 R-2R  梯形DAC 

![image-20200513184053266](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_50.png)

1) 整个网络只有2种电阻。网络由相同的电路环节组成,每节有2个电阻,一个开关,每节对应二进制一位数
2) 每个节点(cBA)对地等效电阻都是R

![image-20200513184136415](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_51.png)

**优点**: 与权电阻DAC比,电阻种类少,易集成开关工作条件相同
**缺点**: 工作速度慢(开关接1、0换向时,开关分布，电容充放电,有动态尖峰电流,影响工作速度

#### 3 R-2R  倒梯形DAC

![image-20200513184306013](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_52.png)

![image-20200513184334803](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_53.png)

**优点:** : 开关位置改换时电压变化很小，各支路电流不改变，初态尖峰电流小，

### 9.2 模数转换 ADC 电路

**采样   保持   量化   编码**

量化：四色五人，直舍不入

#### 1 并行比较 ADC

![image-20200513184640559](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_54.png)

 **优点**: 速度快( 并行)
 **缺点**: 硬件庞大

#### 2 并/ 串型ADC

![image-20200513184728316](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_56.png)

#### 3 逐次逼近型ADC 

![image-20200513184803029](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_57.png)

#### 4 双积分ADC 

![image-20200513184836131](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_58.png)

## 第十章：存储器及可编程逻辑器件

### 10.1 存储器概述

1 半导体存储器分类

SAM （Sequential Access Memory ，顺序存储器）
RAM  （Random Access Memory ，随机存储器）
ROM（ （ Read Only Memory ，只读存储器）

### 10.2  随机存储器RAM

优点: 所有 RAM  具有读、写功能。
缺点: RAM 属于挥发性元件, 断电将丢失全部数据

RAM主要包括：存储矩阵、地址译码器、输入输出控制

#### 1 存储矩阵

SRAM 的存储单元由触发器构成。
DRAM 的存储单元由MOS

#### 2 地址译码器

单译码 ：直接翻译的结果

双译码 ：将地址所对应的二进制数译成：行选信号和列选信号，从而选中该存储单元。

#### 3 RAM扩展

位扩展：将RAM并联

字扩展；将片选信号加入地址信号，多片可以加译码器

### 10.3  只读存储器ROM

ROM是半导体存储器的一类。数据永久或半永久地存储在其中。可以从ROM中“读出”数据,但没有“写”功能ROM一般由专用装置写入数据,数据一旦写入便不能随意改写,断电后,数据也不会丢失。

#### 1 ROM分类

![image-20200513185707735](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_59.png)

#### 2 ROM结构

主要包括：地址译码器、存储矩阵、输出缓冲器

![image-20200513185818031](C:\Ownfolder\Git_home\blog\themes\hexo-theme-matery\source\images\post_images\digital_circuit_summary\digital_circuit_summary_60.png)

#### 3 ROM应用

设计组合逻辑

用于函数运算表电路

### 10.4 可编程逻辑器件

基本结构

PLD：与阵列可编程、或阵列可编程、与/或阵列都可以编程

通用阵列逻辑电路 GAL

​             

[  完  ]