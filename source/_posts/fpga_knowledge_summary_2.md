---
title: FPGA知识总结_02
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理FPGA知识总结02
categories: ☸ FPGA
tags:
  - FPGA
  - 总结
abbrlink: 89524871
date: 2020-05-10 18:27:09
layout:
password: 1df1854015e31ca286d015345eaff29a6c6073f70984a3a746823d4cac16b075
---

## FPGA知识总结--笔试

---

注 : 本文内容来自网络收集总结

https://blog.csdn.net/bleauchat/article/details/98314001

https://blog.csdn.net/bleauchat/article/details/96422839

### 1、题目1

flip-flop 和 logic-gate 设计一个 1 位加法器，输入 carryin 和 current-stage ，输出 carryout 和 next-stage ？
carryout=carryin*current-stage ；与门
next- stage=carryin ’*curren -stage+carryin*current- stage ’;与门，非门，或门（或者异或门）

```verilog
module(
    input clk, 
    input current-stage,
    input carryin,
    output next-stage,
    output carryout
);
    always@(posedge clk)begin
        carryout <= carryin & next-stage;
        nextstage <= carryin*curren -stage+carryin*current- stage
    end
endmodule
```

### 2、题目2

设计一个自动饮料售卖机，饮料 10 分钱，硬币有 5 分和 10 分两种，并考虑找零，

1. 画出 fsm（有限状态机）
2. 用 verilog 编程，语法要符合 FPGA设计的要求
3. 设计工程中可使用的工具及设计大致过程？
   设计过程：
   1、首先确定输入输出， A=1 表示投入 10 分，B=1 表示投入 5 分，Y=1 表示弹出饮料， Z=1
   表示找零。
   2、确定电路的状态， S0 表示没有进行投币， S1 表示已经有 5 分硬币。
   3、画出状态转移图。

```verilog
module sell(clk,rst,a,b,y,z);
    input clk,rst,a,b;
    output y,z;
    parameter s0=0,s1=1;
    reg state,next_state;
    always@(posedge clk)
        begin
            if(!rst)
                state<=s0;
            else
                state<=next_state;
        end
    always@(a or b or cstate)
        begin
            y=0;z=0;
            case(state)
                s0: if(a==1&&b==0) next_state=s1;
                else if(a==0&&b==1)
                    begin
                        next_state=s0; y=1;
                    end
                else
                    next_state=s0;
                s1: if(a==1&&b==0)
                    begin
                        next_state=s0;y=1;
                    end
                else if(a==0&&b==1)
                    begin
                        next_state=s0; y=1;z=1;
                    end
                else
                    next_state=s0;
                default: next_state=s0;
            endcase
        end
endmodule
```

扩展：设计一个自动售饮料机的逻辑电路。它的投币口每次只能投入一枚五角或一元的硬币。投入一元五角硬币后给出饮料；投入两元硬币时给出饮料并找回五角。

1. 确定输入输出，投入一元硬币 A=1 ，投入五角硬币 B=1，给出饮料 Y=1，找回五角Z=1；

2. 确定电路的状态数，投币前初始状态为 S0，投入五角硬币为 S1，投入一元硬币为S2。画出转该转移图，根据状态转移图可以写成 Verilog 代码。

![image-20200510181312785](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181312785.png)



### 3、题目3

用 mos管搭出一个二输入与非门？

与非门：上并下串 或非门：上串下并

![image-20200510181344776](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181344776.png)

### 4、题目4

画出 NOT、NAND、NOR 的符号 , 真值表 , 还有 transistor level （晶体管级）的电路？

<数字电子技术基础（第五版） > 117 页— 134 页



### 5、题目5

画出 CMOS的图 , 画出 tow-to-one  mux gate.( 二选一选择器 ) 
Y=SA+S’B 利用与非门和反相器，进行变换后 Y=((SA) ’*(S ’A)’) ’，三个与非门，一个反相器。也可以用传输门来实现数据选择器或者是异或门。

![image-20200510181401905](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181401905.png)

### 6、题目6

用一个二选一 mux和一个 inv 实现异或？

其中 :B 连接的是地址输入端， A 和 A 非连接的是数据选择端 ,F 对应的的是输出端 ,使能端固
定接地置零 (没有画出来 ).
Y=BA’+B’A
利用 4 选 1 实现 

F(x,y,z)=xz+yz'
F(x,y,z)=x yz+xy’z+xyz' +x’yz’=x’y’0+x’yz’+xy’z+xy1
Y=A’B’D0+A’BD1+AB’D2+ABD3
所以 D0=0 ，D1=z ’，D2=z ，D3=1

### 7、题目7

画出 CMOS电路的晶体管级电路图 , 实现 Y=A*B+C(D+E).( 仕兰微电子 ) ？

画出 Y=A*B+C 的 CMOS 电路图，画出 Y=A*B+C*D 的 CMOS 电路图。
利用与非门和或非门实现
Y=A*B+C(D+E) =((AB ’)(CD) ’(CE)’) ’ 三个两输入与非门，一个三输入与非门
Y=A*B+C=((AB)’C’)  一个反相器，两个两输入与非门
Y=A*B+C*D=((AB)’(CD)’) ’ 三个两输入与非门

### 8、题目8

用与非门等设计全加法器？（华为）

![image-20200510181424265](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181424265.png)

### 9、题目9

A,B,C,D,E 进行投票 , 多数服从少数 , 输出是 F( 也就是如果 A,B,C,D,E 中 1 的个数比0  多, 那么 F 输出为 1, 否则 F 为 0), 用与非门实现 , 输入数目没有限制？ （与非 - 与非形式）先画出卡诺图来化简，化成与或形式，再两次取反便可。

### 10、题目10

画出一种 CMOS的 D锁存器的电路图和版图？

![image-20200510181441347](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181441347.png)

### 11、题目11

用 D触发器做个二分频的电路？画出逻辑电路？

```verilog
module div2 (clk,rst,clk_out);
    input clk,rst;
    output  reg  clk_out;
    always@(posedge clk)
        begin
            if(!rst)
                clk_out <=0;
            else
                clk_out <=~ clk_out;
        end
endmodule
```

现实 工程设计中一般不采用这样的方式来设计，二分频一般通过 DCM 来实现。通过 DCM得到的分频信号没有相位差。或者是从 Q 端引出加一个反相器，**相当于过一拍取反一次。如果是四分频，就用两个触发器，过两拍取反一次，以此类推**

![image-20200510181501611](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181501611.png)

### 12、题目12

用你熟悉的设计方式设计一个可预置初值的 7 进制循环计数器 ,15 进制的呢？

```verilog
module counter7(clk,rst,load,data,cout);
    input clk,rst,load;
    input [2:0] data;
    output reg [2:0] cout;
    always@(posedge clk)
        begin
            if(!rst)
                cout<=3 ’d0;
            else if(load)
                cout<=data;
            else if(cout>=3 ’d6)
                cout<=3 ’d0;
            else
                cout<=cout+3 ’d1;
        end
endmodule
```

### 13、题目13

用 Verilog 或 VHDL写一段代码 , 实现消除一个 glitch （毛刺）？
将传输过来的信号经过两级触发器就可以消除毛刺。 （这是我自己采用的方式： 这种方式消除毛刺是需要满足一定条件的，并不能保证一定可以消除）

```verilog
module(clk,data,q_out)
    input clk,data;
    output reg q_out;
    reg q1;
    always@(posedge clk)
        begin
            q1<=data;
            q_out<=q1;
        end
endmodule
```

### 14、题目14

给出一个组合逻辑电路，要求分析逻辑功能。所谓组合逻辑电路的分析， 就是找出给定逻辑电路输出和输入之间的关系， 并指出电路的逻辑功能。
分析过程一般按下列步骤进行：
1：根据给定的逻辑电路，从输入端开始，逐级推导出输出端的逻辑函数表达式。
2：根据输出函数表达式列出真值表；
3：用文字概括处电路的逻辑功能；

### 15、题目15

实现三分频电路， 3/2 分频电路等（偶数倍分频 奇数倍分频）
图 2 是 3 分频电路，用 JK-FF 实现 3 分频很方便，不需要附加任何逻辑电路就能实现同步计数分频。 但用 D-FF 实现 3 分频时， 必须附加译码反馈电路， 如图 2 所示的译码复位电路，强制计数状态返回到初始全零状态，就是用 NOR 门电路把 Q2，Q1=―11B‖的状态译码产生―H‖ 电平复位脉冲，强迫 FF1 和 FF2 同时瞬间（在下一时钟输入 Fi 的脉冲到来之前）复零，于是 Q2， Q1=―11B‖状态仅瞬间作为 ―毛刺 ‖存在而不影响分频的周期，这种 ―毛刺 ‖仅在 Q1中存在， 实用中可能会造成错误， 应当附加时钟同步电路或阻容低通滤波电路来滤除， 或者仅使用 Q2 作为输出。 D-FF 的 3 分频，还可以用 AND 门对 Q2，Q1 译码来实现返回复零。

![image-20200510181534452](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181534452.png)

### 16、题目6

三极管特性曲线

![image-20200510181553855](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181553855.png)

### 17、题目17

 Please show the CMOS inverter schematic,  layout and its cross section  with P-well process.  Plot its transfer  curve (Vout-Vin)  and also explain the operation region of PMOS and NMOS for each segment of the transfer  curve?  （威盛笔试题 circuit design-beijing-03.11.09 ）

### 18、题目18

 To design a CMOS inverter  with  balance rise and fall  time,  please define the ration of channel width of PMOS and NMOS and explain?  P管要比 N管宽

### 19、题目19

Please draw the transistor level schematic of a  CMOS 2 input AND gate and explain which input has faster response for output rising edge.(less delay time) 。（威盛笔试题 circuit design-beijing-03.11.09 ）

### 20、题目20

为了实现逻辑 Y=A’B+AB’+CD，请选用以下逻辑中的一种，并说明为什么？
1）INV  2 ）AND  3 ）OR  4 ）NAND  5 ）NOR  6 ）XOR
答案： NAND （未知）

### 21、题目21

用波形表示 D触发器的功能。（扬智电子笔试）

![image-20200510181617428](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181617428.png)

### 22、题目22

用传输门和倒向器搭一个边沿触发器（ DFF）。（扬智电子笔试）--通过级联两个 D 锁存器组成

![image-20200510181632763](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181632763.png)

### 23、题目23

用逻辑门画出 D触发器。（威盛 VIA 2003.11.06  上海笔试试题）

![image-20200510181648804](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181648804.png)

![image-20200430100007729](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181703618.png)

### 24、题目24

画出 DFF的结构图 , 用 verilog 实现之。（威盛）

```verilog
module dff(clk,d,qout
           input 	clk,
           input	d,
           output reg qout
          );
    always@(posedge clk)begin
        if(!reset)
            qout<=0;
        else
            qout<=d;
    end
endmodule
```

### 25、题目25

画出一种 CMOS的 D锁存器的电路图和版图。（未知）或者是利用前面与非门搭的 D 锁存器实现

![image-20200510181721890](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181721890.png)

### 26、题目26

用 D触发器做个 4 进制的计数。（华为）
按照时序逻辑电路的设计步骤来：
写出状态转换表
寄存器的个数确定
状态编码
卡诺图化简
状态方程，驱动方程等
阎石数字电路 P314

### 27、题目27

实现 N位 Johnson Counter,  N=5。（南山之桥）

### 28、题目28

数字电路设计当然必问 Verilog/VHDL ，如设计计数器。（未知）

### 29、题目29

请用 HDL描述四位的全加法器、 5 分频电路。（仕兰微电子）

```verilog
module adder4(a,b,ci,s,co);
    input ci;
    input [3:0] a,b;
    output co;
    output [3:0] s;
    assign {co,s}=a+b+ci;
endmodule
module div5(clk,rst,clk_out);
    input clk,rst;
    output clk_out;
    reg [3:0] count;
    always@(posedge clk)
        begin
            if(!rst)
                begin
                    count<=0;
                    clk_out=0;
                end
            else if(count==3 ’d5)
                begin
                    count<=0;
                    clk_out=~clk_out;
                end
            else
                count<=count+1;
        end
endmodule
实现奇数倍分频且占空比为 50% 的情况：
module div 7 ( clk, reset_n, clkout );
    input clk, reset_n;
    output clkout;
    reg [ 3:0] count;
    reg div1;
    reg div2;
    always @( posedge clk )
        begin
            if ( ! reset_n )
                count <= 3'b000;
            else
                case ( count )
                    3'b000 : count <= 3'b001;
                    3'b001 : count <= 3'b010;
                    3'b010 : count <= 3'b011;
                    3'b011 : count <= 3'b100;
                    3'b100 : count <= 3'b101;
                    3'b101 : count <= 3'b110;
                    3'b110 : count <= 3'b000;
                    default :
                        count <= 3'b000;
                endcase
        end
    always @( posedge clk )
        begin
            if ( ! reset_n )
                div1 <= 1'b0;
            else if ( count == 3'b000 )
                div1 <= ~ div1;
        end
    always @( negedge clk )
        begin
            if ( ! reset_n )
                div2 <= 1'b0;
            else if ( count == 3'b100 )
                div2 <= ~ div2;
        end
    assign clkout = div1 ^ div2;
endmodule
```

### 30、题目30

用 VERILOG或 VHDL写一段代码，实现 10 进制计数器。（未知）

```verilog
module counter10 (clk,rst, count);
    input clk,rst;
    output [3:0] coun t;
    reg [3:0] count;
    always@(posedge clk)
        begin
            if(!rst)
                count<=0;
            else if(count >=4’d 9)
                count<=0;
            else
                count<=count+1;
        end
endmodule
```

### 31、题目31

画状态机，接受 1，2，5 分钱的卖报机，每份报纸 5 分钱。（扬智电子笔试）
1、确定输入输出，投 1 分钱 A=1 ，投 2 分钱 B=1 ，投 5 分钱 C=1 ，给出报纸 Y=1
2、确定状态数画出状态转移图，没有投币之前的初始状态 S0，投入了 1 分硬币 S1，投入
了 2 分硬币 S2，投入了 3 分硬币 S3 ，投入了 4 分硬币 S4。
3、画卡诺图或者是利用 verilog 编码

### 32、题目32

设计一个自动售货机系统，卖 soda 水的，只能投进三种硬币，要正确的找回钱
数。 （1）画出 fsm（有限状态机）；（ 2）用 verilog 编程，语法要符合
fpga 设计的要求。（未知）

### 33、题目33

设计一个自动饮料售卖机，饮料 10 分钱，硬币有 5 分和 10 分两种，并考虑找零： （ 1）
画出 fsm（有限状态机）；（ 2）用 verilog 编程，语法要符合 fpga 设计的要求；（ 3）设
计工程中可使用的工具及设计大致过程。（未知）
1、输入 A=1 表示投 5 分钱， B=1 表示投 10 分钱，输出 Y=1 表示给饮料， Z=1 表示找零
2、确定状态数，没投币之前 S0，投入了 5 分 S1

### 34、题目34

画出可以检测 10010 串的状态图 , 并 verilog 实现之。（威盛）
1、输入 data，1 和 0 两种情况，输出 Y=1 表示连续输入了 10010
2、确定状态数没输入之前 S0，输入一个 0 到了 S1,10 为 S2,010 为 S3,0010 为 S4

### 35、题目35

用 FSM实现 101101 的序列检测模块。（南山之桥）
a 为输入端， b 为输出端，如果 a 连续输入为 101101 则 b 输出为 1，否则为 0。
例如 a： 0001100110110110100110
b： 0000000000100100000000
请画出 state machine ；请用 RTL描述其 state machine 。（未知）
确定状态数，没有输入或输入 0 为 S0，1 为 S1，01 为 S2,101 为 S3,1101 为 S4，01101
为 S5。知道了输入输出和状态转移的关系很容易写出状态机的 verilog 代码，一般采用两段
式状态机

### 36、题目36

给出单管 DRAM的原理图

![image-20200510181748128](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200510181748128.png)

### 37、题目37

用 verilog/vhdl 写一个 fifo 控制器包括空，满，半满信号。

### 38、题目38

用 verilog/vddl 检测 stream 中的特定字符串分状态用状态机写。

### 39、题目39

CMOS结构

![image-20200430112437794](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200430112437794.png)CMOS

### 40、题目40

典型的输入设备与微机接口逻辑示意图（数据接口、控制接口、所存器 /缓冲器）。

![image-20200430111322080](C:\Users\yasheng\AppData\Roaming\Typora\typora-user-images\image-20200430111322080.png)



### 41、题目41

x^4+a*x^3+x^2+c*x+d  最少需要做几次乘法？ (Dephi)







第一题：用 mos 管搭出一个二输入与非门。
第二题：集成电路前段设计流程，写出相关的工具。

第四题： unix 命令 cp -r, rm,uname
第五题：用波形表示 D 触发器的功能
第六题：写异步 D 触发器的 verilog module
第七题： What is PC Chipset?
第八题：用传输门和倒向器搭一个边沿触发器
第九题：画状态机，接受 1，2， 5 分钱的卖报机，每份报纸 5 分钱。

1.用与非门等设计全加法器
2.给出两个门电路让你分析异同
3.名词 :sram,ssram,sdram
DRAM ，动态随机存取存储器，需要不断的刷新，才能保存数据。
SRAM 是 Static Random Access Memory 的缩写，中文含义为静态随机访问存储器
SDRAM 代表的是同步 DRAM （Synchronous DRAM ），这与 SRAM 是完全不同的。
4.信号与系统 :在时域与频域关系
5.信号与系统 :和 4 题差不多
6.晶体振荡器 ,好像是给出振荡频率让你求周期 (应该是单片机的 ,12 分之一周期 ..
..)
7.串行通信与同步通信异同 ,特点 ,比较
8.RS232c 高电平脉冲对应的 TTL 逻辑是 ?(负逻辑 ?)
9.延时问题 ,判错
10.史密斯特电路 ,求回差电压
11.VCO 是什么 ,什么参数 (压控振荡器 ?)

12. 用 D 触发器做个二分颦的电路 .又问什么是状态图
13. 什么耐奎斯特定律 ,怎么由模拟信号转为数字信号
14. 用 D 触发器做个 4 进制的计数
    15.那种排序方法最快 ?

d 触发器和 d 锁存器的区别
（2）有源滤波器和无源滤波器的原理及区别
（3）sram，falsh memory，及 dram 的区别？
（4）iir ，fir 滤波器的异同
（5）冒泡排序的原理
（6）操作系统的功能
（7）学过的计算机语言及开发的系统
（8）拉氏变换和傅立叶变换的表达式及联系。





基尔霍夫定理的内容是什么？（仕兰微电子）
2、平板电容公式 (C=ε S/4πkd)。（未知）
3、最基本的如三极管曲线特性。 （未知）
4、描述反馈电路的概念，列举他们的应用。 （仕兰微电子）
5、负反馈种类（电压并联反馈，电流串联反馈，电压串联反馈和电流并联反馈） ；负反 馈
的优点（降低放大器的增益灵敏度，改变输入电阻和输出电阻，改善放大器的线性和非 线
性失真，有效地扩展放大器的通频带，自动调节作用） （未知）
6、放大电路的频率补偿的目的是什么，有哪些方法？（仕兰微电子）
7、频率响应，如：怎么才算是稳定的，如何改变频响曲线的几个方法。 （未知）
8、给出一个查分运放，如何相位补偿，并画补偿后的波特图。 （凹凸）
9、基本放大电路种类（电压放大器，电流放大器，互导放大器和互阻放大器） ，优缺 点，
特别是广泛采用差分结构的原因。 （未知）
10、给出一差分电路，告诉其输出电压 Y+和 Y-,求共模分量和差模分量。 （未知）
11、画差放的两个输入管。 （凹凸）
12、画出由运放构成加法、 减法、微分、积分运算的电路原理图。 并画出一个晶体管级的 运
放电路。（仕兰微电子）
13、用运算放大器组成一个 10 倍的放大器。（未知）
14、给出一个简单电路， 让你分析输出电压的特性 （就是个积分电路） ，并求输出端某点 的
rise/fall 时间。 (Infineon 笔试试题 )
15、电阻 R 和电容 C 串联，输入电压为 R 和 C 之间的电压，输出电压分别为 C 上电压和 R
上电 压，要求制这两种电路输入电压的频谱，判断这两种电路何为高通滤波器，何为低通
滤 波器。当 RC<< period - setup – hold
16、时钟周期为 T,触发器 D1 的建立时间最大为 T1max，最小为 T1min。组合逻辑电路最大
延 迟为 T2max,最小为 T2min。问，触发器 D2 的建立时间 T3 和保持时间应满足什么条件。
（华 为）
17、给出某个一般时序电路的图，有 Tsetup,Tdelay,Tck->q, 还有 clock 的 delay,写出决 定最大
时钟的因素，同时给出表达式。 （威盛 VIA 2003.11.06 上海笔试试题）
18、说说静态、动态时序模拟的优缺点。 （威盛 VIA 2003.11.06 上海笔试试题）
19、一个四级的 Mux,其中第二级信号为关键信号 如何改善 timing 。（威盛 VIA 2003.11.06 上
海笔试试题）
20、给出一个门级的图， 又给了各个门的传输延时， 问关键路径是什么， 还问给出输入， 使
得输出依赖于关键路径。 （未知）
21、逻辑方面数字电路的卡诺图化简， 时序（同步异步差异） ，触发器有几种 （区别， 优 点），
全加器等等。 （未知）
22、卡诺图写出逻辑表达使。 （威盛 VIA 2003.11.06 上海笔试试题）
23、化简 F(A,B,C,D)= m(1,3,4,5,10,11,12,13,14,15)的和。（威盛）
24、please show the CMOS inverter schmatic,layout and its cross sectionwith P- well process.Plot
its transfer curve (Vout-Vin) And also explain the operation region of PMOS and NMOS for each
segment of the transfer curve? （威 盛笔试题 circuit design-beijing-03.11.09 ）
25、To design a CMOS invertor with balance rise and fall time,please define the ration of channel
width of PMOS and NMOS and explain?
26、为什么一个标准的倒相器中 P管的宽长比要比 N 管的宽长比大？（仕兰微电子）
27、用 mos 管搭出一个二输入与非门。 （扬智电子笔试）
28、please draw the transistor level schematic of a cmos 2 input AND gate and explain which
input has faster response for output rising edge.(less delay time) 。（威盛笔试题 circuit
design-beijing-03.11.09 ）
29、画出 NOT,NAND,NOR的符号，真值表，还有 transistor level 的电路。（Infineon 笔 试）
30、画出 CMOS的图，画出 tow-to-one mux gate 。（威盛 VIA 2003.11.06 上海笔试试题）
31、用一个二选一 mux 和一个 inv 实现异或。（飞利浦－大唐笔试）
32、画出 Y=A*B+C的 cmos 电路图。（科广试题）
33、用逻辑们和 cmos 电路实现 ab+cd。（飞利浦－大唐笔试）
34、画出 CMOS电路的晶体管级电路图，实现 Y=A*B+C(D+E) 。（仕兰微电子）
35、利用 4 选 1 实现 F(x,y,z)=xz+yz’。（未知）
36、给一个表达式 f=xxxx+xxxx+xxxxx+xxxx用最少数量的与非门实现（实际上就是化 简）。
37、给出一个简单的由多个 NOT,NAND,NOR组成的原理图，根据输入波形画出各点波形。
（Infineon 笔试）
38、为了实现逻辑 （A XOR B）OR （C AND D），请选用以下逻辑中的一种， 并说明为什 么？
1）INV 2）AND 3）OR 4）NAND 5）NOR 6）XOR 答案： NAND（未知）
39、用与非门等设计全加法器。 （华为）
40、给出两个门电路让你分析异同。 （华为）
41、用简单电路实现，当 A 为输入时，输出 B 波形为⋯（仕兰微电子）
42、A,B,C,D,E进行投票， 多数服从少数， 输出是 F（也就是如果 A,B,C,D,E中 1 的个数比 0 多，
那么 F输出为 1，否则 F为 0），用与非门实现，输入数目没有限制。 （未知）
43、用波形表示 D 触发器的功能。 （扬智电子笔试）
44、用传输门和倒向器搭一个边沿触发器。 （扬智电子笔试）
45、用逻辑们画出 D 触发器。（威盛 VIA 2003.11.06 上海笔试试题）
46、画出 DFF的结构图 ,用 verilog 实现之。（威盛）
47、画出一种 CMOS的 D 锁存器的电路图和版图。 （未知）
48、D 触发器和 D 锁存器的区别。 （新太硬件面试）
49、简述 latch 和 filp-flop 的异同。（未知）
50、LATCH和 DFF的概念和区别。 （未知）
51、latch 与 register 的区别 ,为什么现在多用 register.行为级描述中 latch 如何产生的。 （南
山之桥）
52、用 D 触发器做个二分颦的电路 .又问什么是状态图。 （华为）
53、请画出用 D 触发器实现 2倍分频的逻辑电路？（汉王笔试）
54、怎样用 D 触发器、与或非门组成二分频电路？（东信笔试）
55、How many flip-flop circuits are needed to divide by 164 (Intel) 16 分频？
56、用 filp-flop 和 logic-gate 设计一个 1 位加法器，输入 carryin 和 current-stage，输出 carryout
和 next-stage. （未知）
57、用 D 触发器做个 4 进制的计数。 （华为）
58、实现 N 位 Johnson Counter,N=5。（南山之桥）
59、用你熟悉的设计方式设计一个可预置初值的 7 进制循环计数器， 15 进制的呢？ （仕兰 微
电子）
60、数字电路设计当然必问 Verilog/VHDL，如设计计数器。 （未知）
61、BLOCKING NONBLOCKING 赋值的区别。 （南山之桥）
62、写异步 D 触发器的 verilog module 。 （扬智电子笔试） module dff8(clk , reset, d, q); input clk;
input reset; input [7:0] d; output [7:0] q; reg [7:0] q; always @ (posedge clk or posedge reset)
if(reset) q <= 0; else q <= d; endmodule
63、用 D 触发器实现 2 倍分频的 Verilog 描述？ （汉王笔试） module divide2( clk , clk_o, reset);
input clk , reset; output clk_o; wire in; reg out ; always @ ( posedge clk or posedge reset) if ( reset)
out <= 0; else out <= in; assign in = ~out; assign clk_o = out; endmodule
6?、可编程逻辑器件在现代电子设计中越来越重要，请问： a) 你所知道的可编程逻辑器 件
有哪些？ b) 试用 VHDL或 VERILOG、ABLE描述 8 位 D 触发器逻辑。（汉王笔试） PAL，PLD，
CPLD，FPGA。 module dff8(clk , reset, d, q); input clk; input reset; input d; output q; reg q; always
@ (posedge clk or posedge reset) if(reset) q <= 0; else q <= d; endmodule
65、请用 HDL描述四位的全加法器、 5 分频电路。（仕兰微电子） 66、用 VERILOG或 VHDL
写一段代码，实现 10 进制计数器。 （未知） 67、用 VERILOG或 VHDL写一段代码，实现消
除一个 glitch。（未知）
68、一个状态机的题目用 verilog 实现（不过这个状态机画的实在比较差，很容易误解 的）。
（威盛 VIA 2003.11.06 上海笔试试题）
69、描述一个交通信号灯的设计。 （仕兰微电子）
70、画状态机，接受 1， 2，5 分钱的卖报机，每份报纸 5 分钱。（扬智电子笔试）
71、设计一个自动售货机系统， 卖 soda 水的，只能投进三种硬币， 要正确的找回钱 数。（ 1）
画出 fsm（有限状态机） ；（2）用 verilog 编程，语法要符合 fpga 设计 的要求。（未知）
72、设计一个自动饮料售卖机，饮料 10 分钱，硬币有 5 分和 10 分两种，并考虑找零： （ 1）
画出 fsm（有限状态机） ；（2）用 verilog 编程，语法要符合 fpga 设计的要求； （3）设计 工
程中可使用的工具及设计大致过程。 （未知）
73、画出可以检测 10010 串的状态图 ,并 verilog 实现之。（威盛）
74、用 FSM 实现 101101 的序列检测模块。 （南山之桥） a 为输入端， b 为输出端，如果 a
连续输入为 1101 则 b 输出为 1，否则为 0。 例如 a： 0001100110110100100110 b：
0000000000100100000000 请画出 state machine ；请用 RTL描述其 state machine 。（未知）
75、用 verilog/vddl 检测 stream 中的特定字符串（分状态用状态机写） 。（飞利浦－大唐 笔
试）
76、用 verilog/vhdl 写一个 fifo 控制器 (包括空，满，半满信号 )。（飞利浦－大唐笔试）
77、现有一用户需要一种集成电路产品，要求该产品能够实现如下功能： y=lnx，其中， x 为
4 位二进制整数输入信号。 y 为二进制小数输出， 要求保留两位小数。 电源电压为 3~5v 假 设
公司接到该项目后， 交由你来负责该产品的设计， 试讨论该产品的设计全程。 （仕兰微 电子）
78、 sram，falsh memory，及 dram 的区别？（新太硬件面试）
79、给出单管 DRAM 的原理图 (西电版《数字电子技术基础》作者杨颂华、冯毛官 205 页图
9 －14b)，问你有什么办法提高 refresh time ，总共有 5 个问题，记不起来了。 （降低温 度，
增大电容存储容量） （Infineon 笔试）
80、Please draw schematic of a common SRAM cell with 6 transistors,point out which nodes can
store data and which node is word line control?  （威盛笔试题 circuit design-beijing-03.11.09 ）
81、名词 :sram,ssram,sdram 名词 IRQ,BIOS,USB,VHDL,SDR IRQ: Interrupt ReQuest BIOS: Basic
Input Output System USB: Universal Serial Bus VHDL: VHIC Hardware Description Language SDR:
Single Data Rate  压控振荡器的英文缩写 (VCO)。 动态随机存储器的英文缩写
(DRAM)。 名词解释，无聊的外文缩写罢了，比如 PCI、 ECC、 DDR、interrupt 、pipeline 、
IRQ,BIOS,USB,VHDL,VLSI VCO( 压控振荡器 ) RAM (动态随机存储器 )，FIR IIR DFT( 离散 傅立叶变
换)或者是中文的，比如： a.量化误差 b.直方图 c.白平衡



## 面试经验

这段时间去面试了几家公司， 发现比较大的公司相对于重视基础问题。 这里边又有几个问题
特别的突出。他们是：同步时钟设计、亚稳态、异步 FIFO。可以说，这些个问题要是弄清
楚了，就至少满足了技术方面 1/3 的要求，另外的 2/3 是什么，我就说不清楚了。又有人发
了竞争冒险毛刺的问题， 不过， 对于采用同步设计方法的系统，这些问题一般不会遇到。下
面就谈谈我对这些问题的看法， 要是你觉得看这些东西觉得类似一堆狗屎， 那么恭喜你， 你
面试成功的机会增加了 1/3 ；要是你你觉得阿， 什么样的牛人拉了一堆牛屎， 那么不好意思，
还是再去补补课把。这里推荐一本《数字设计——原理和实践》 (John F.Wakerly)的书，仔细
看一遍吧。
同步时钟设计简单说就是一个系统中 （或系统中的一部分） 都采用同一个时钟触发。 系统中
的(D)触发器全部都连接到一个时钟，而且只控制触发器的同步端（输入，同步置位，同步
复位）。这样的系统是相对于异步系统而言的，异步系统并不是不同的触发器时钟端连接到
不同的时钟信号的系统 （一般的这样叫做跨时钟系统， 是相对几个较小的同步系统的组合） ，
而是更本没有了时钟的概念， 依靠和触发器构造一样的反馈电路组成。 相对于异步系统， 同
步系统更好设计（异步设计则象一个魔术 , 类似于汇编和高级语言的关系） ，更容易进行时
序分析 (为什么要用 D 触发器而不用 D 锁存器 )——在这里组合逻辑的竞争冒险毛刺问题都不
存在了。应该说，同步系统最大的问题在于时钟的偏斜 (skew)。同步时钟系统也存在一些涉
及的技巧， 这些技巧一般围绕着降低关键路径的延时和时间和空间的平衡。 这些都是平衡的
艺术（了解了基本的部件之后，剩下的工作就是一个字 "平衡 "),这里边的方法就太具体，而
且本人也知道得不多，不敢乱说了。 不过，只要你用过一种方法， 就可以体会到其中的精神
了。
亚稳态这是跨时钟设计中最基础的一个问题 (宏观的问题是 FIFO)，按照我的观察，上论坛问
问题多的一般不明白这个， 请一定要注意了。 什么是亚稳态？数字电路中的简单双稳态电路
就是两个反相器首尾相连组成（加一些控制逻辑变成了锁存器，触发器） ，然而并不像名字
显示的，这种电路其实还有第三种半稳定态——就是当两个反相器都处于中间值得情况——
这称之为亚稳态。 我们知道反相器在非逻辑值范围的反馈系数是相当大的， 一旦因为干扰或
者噪音离开了这个中心点，就会很快地进入逻辑值范围（稳态） 。数学分析，从亚稳态进入
稳态，正如放射元素的衰变，是一个指数的规律（为什么是指数的规律？你要是想不明白，
说明你还没有搞明白亚稳态） 。那么，亚稳态的危害到底是什么呢？消耗功率； ），其实不是
（虽然亚稳态消耗很大的功率） ，亚稳态的问题在于其电平并不处于有效逻辑电平范围内，
而且在变化。这就导致与其相连其他数字部件将其作出不同的判断（注意，不同） ，有的作
为'1',有的作为 '0'，有的也进入了亚稳态，数字部件就会逻辑混乱。那么究竟如何避免（或
者减小 )亚稳态的危险呢？注意到亚稳态的触发器继续停留在亚稳态的几率按照指数减少，
那么办法就是等——等足够长的时间， 直到这个几率变得小的实际上不会发生。 到底需要有
多长呢？有的厂商有一个数据， 有的没有， 按照普通的做法， 至少等一个时钟周期——这也
就是所谓的异步数据要用两个触发器打一下。 这一段有点糊涂， 不容易说明白， 你看了要是
觉得云里雾里， 不知所云，那们你只有找一本书学习了； 要是觉得作者表达不清， 那么恭喜
你，面试通过了的几率增加了。 关于这个问题有很多糊涂的认识， 要是你的主考官和你争论，
你就顺着他的意思，毕竟没有人想找一个管教不了的手下。
异步 FIFO 异步 FIFO是跨时钟域设计方法的集中体现， 体现了很多的方法。 不过，其中最重
要的有两点， 一个就是亚稳态， 一个就是和亚稳态类似但不相同的——多个控制 /状态信号
的跨时钟传递。 具体地说， 就是当你把一组信号传递到另外一个时钟域的话， 这一组信号可
能因为延迟不同， 这样到达新时钟域之后， 得到的数据相差一个老时钟域的时钟周期。 兴好，
对于 FIFO，需要传递的是一个计数器，这个计数器可以编码成格雷码（ gray code），这样的
编码每次只变化一个位，正好解决了上面的问题 (要是没有画过图，最好画一个图看一下） 。
真不清楚这是怎么发明的！ 注意，这里其实还对格雷码的相对延迟和相关的时钟周期有一个
要求。这就是异步 FIFO中最关键的一点， 至于指针如何控制， 稍微考虑一下都很容易清楚。
需要注意的事，这些东西不是用嘴能说清楚的，最好画一个示意图，不要因为没有说清楚，
让主考官觉得你没有清楚。
当然， 除此之外还有很多很多的东西。比如组合逻辑的设计技巧， 俺就没有研究。 比如高速
布线信号完整性问题，俺也不敢多说。至于整个系统的设计思想， 更不敢妄语。不过如果只
要你有一个问题了解到了相当的深度，相信你很容易搞清楚其他问题。



**注：所有内容均来源于网络，如本文的内容对您的权利造成了影响，请<a href="mailto:1058349718@qq.com">邮件联系</a>，我们会在第一时间将涉及版权的内容进行删除。**

[  完  ]

