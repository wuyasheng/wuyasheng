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

### 网文参考链接 -- 未总结-待学习

[FPGA校招笔试题分析](https://blog.csdn.net/bleauchat/article/details/98314001)

[数字IC面试题](https://blog.csdn.net/bleauchat/article/details/96422839)

[计数器-分频电路-串并转换电路](https://wenku.baidu.com/view/64e1c0d6195f312b3169a5f8.html)

[数字IC设计笔试问题系列](https://blog.csdn.net/bleauchat/article/details/90351877?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.nonecase)

[verilog面试宝典-同步与异步，FIFO](https://www.jianshu.com/p/80f09a832b86)

[FPGA&数字前端笔面试题](https://www.cnblogs.com/lionsde/p/10597036.html)

[明德扬-FPGA面试题](http://www.mdy-edu.com/bishiti/2019/1210/465.html )

[大疆2019校招FPGA笔试总结](https://www.cnblogs.com/lionsde/p/9564589.html)

[电子工程师面试题整理](https://blog.csdn.net/chris7878/article/details/4993211)

仿真时序问题推荐参考《通向FPGA之路---七天玩转Altera之时序篇》pdf资料



### 组合逻辑

#### 1、题目

给出一个组合逻辑电路，要求分析逻辑功能。所谓组合逻辑电路的分析， 就是找出给定逻辑电路输出和输入之间的关系， 并指出电路的逻辑功能。
分析过程一般按下列步骤进行：
1：根据给定的逻辑电路，从输入端开始，逐级推导出输出端的逻辑函数表达式。
2：根据输出函数表达式列出真值表；
3：用文字概括处电路的逻辑功能；

#### 2、题目

flip-flop 和 logic-gate 设计一个 1 位加法器，输入 carryin 和 current-stage ，输出 carryout 和 next-stage ？
carryout = carryin*current-stage ；与门
next_stage= curren_stage+carryin ;与门，非门，或门（或者异或门）

```verilog
module(
    input clk, 
    input current_stage,
    input carryin,
    output next_stage,
    output carryout
);
    always@(posedge clk)begin
        carryout <= carryin & curren_stage;
        next_stage <= curren_stage + carryin;
    end
endmodule
```

#### 3、题目

画出 NOT、NAND、NOR 的符号 , 真值表 , 还有 transistor level （晶体管级）的电路？

<数字电子技术基础（第五版） > 117 页— 134 页

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_01.png">

#### 4、题目

画出 CMOS的图 , 画出 tow-to-one  mux gate.( 二选一选择器 ) 

**可以用传输管和反相器搭建**

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_02.png">

Y=SA+S’B 利用与非门和反相器，进行变换后 Y=((SA) ’*(S ’A)’) ’，三个与非门，一个反相器。



#### 5、题目

用一个二选一 mux和一个 inv 实现异或？

其中 :B 连接的是地址输入端， A 和 A 非连接的是数据选择端 ,F 对应的的是输出端 ,使能端固
定接地置零 (没有画出来 ).
Y=BA’+B’A

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_03.png">

利用 4 选 1 实现 

F(x,y,z)=xz+yz'
F(x,y,z)=x yz+xy’z+xyz' +x’yz’=x’y’0+x’yz’+xy’z+xy1
Y=A’B’D0+A’BD1+AB’D2+ABD3
所以 D0=0 ，D1=z ’，D2=z ，D3=1

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_04.png">

#### 6、题目

AB+C(D+E) =((AB ’)(CD) ’(CE)’) ’ 三个两输入与非门，一个三输入与非门
Y=A*B+C=((AB)’C’)  一个反相器，两个两输入与非门
Y=A*B+C*D=((AB)’(CD)’) ’ 三个两输入与非门

#### 7、题目

用与非门等设计全加法器？（华为）

考察与非门做异或门

A⊕B = AB' + A'B = ((A'B + AA')' (A'B + BB')' )' = ((A(B' + A'))'  (B(B' + A'))' )'  

= ((A(BA)')'  (B(AB)')' )'       4个与非门即可实现

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_05.png">

一位全加器的表达式如下：
$$
C_i = A_iB_i+C_{i-1}(A_i+B_i)
$$

$$
S_i=A_i⊕B_i⊕C_{i-1}
$$

第二个表达式也可用一个异或门来代替或门对其中两个输入信号进行求和：因为修改成异或后，花间结果仍是上文第二个表达式
$$
C_i = A_iB_i+C_{i-1}(A_i⊕B_i)
$$
即可使用与非门搭建异或门，需要9个与非门搭建

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_06.png">

#### 8、题目

A,B,C,D,E 进行投票 , 多数服从少数 , 输出是 F( 也就是如果 A,B,C,D,E 中 1 的个数比0  多, 那么 F 输出为 1, 否则 F 为 0), 用与非门实现 , 输入数目没有限制？ （与非 - 与非形式）先画出卡诺图来化简，化成与或形式，再两次取反便可。

Y = ABC + ABD + ABE +  ACD + ACE + ADE + BCD + BCE + BDE + CDE

使用最小项将多数大于少数的表达式或起来

#### 9、题目

为了实现逻辑 Y=A’B+AB’+CD，请选用以下逻辑中的一种，并说明为什么？
1）INV  2 ）AND  3 ）OR  4 ）NAND  5 ）NOR  6 ）XOR
答案： NAND （未知）

电路性能稳定、抗干扰能力比较强、产品类型比较多。

可能是因为CMOS逻辑都是非结构，与结构相对于或结构速度快，**因为与门电容串联小，充放电速度快**

#### 10、题目

x^4+a*x^3+x^2+c*x+d  最少需要做几次乘法？ (Dephi)

((((x+a)*x)+1)*x+c)*x+d
最少需要三次乘法即可,这是一种有效的加速表达式求解的方法.

#### 11、题目

如果只使用2选1mux完成异或逻辑，至少需要几个mux？

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_07.png">



#### 12、题目

用常用的逻辑门搭一个 3 位宽的计数器；

使用3个D触发器即可

#### 13、题目

化简 F(A,B,C,D)= m(1,3,4,5,10,11,12,13,14,15)的和。（威盛）



### 时序逻辑

#### 1、题目

画出一种 CMOS的 D锁存器的电路图和版图？

D锁存器（可以用与非门搭建，或者用与门和或非门搭建）

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_08.png">

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_09.png">

#### 2、题目

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

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_10.png">

#### 3、题目

用你熟悉的设计方式设计一个可预置初值的 7 进制循环计数器 ,15 进制的呢？

```verilog
module counter7(clk,rst,load,data,cout);//同步复位，同步置数
    input clk,rst,load;
    input [2:0] data;
    output reg [2:0] cout;
    always@(posedge clk) begin
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

```verilog
module counter7(clk,rst,load,data,cout);//异步复位，同步置数
    input clk,rst,load;
    input [2:0] data;
    output reg [2:0] cout;
    always@(posedge clk or negedge rst)begin
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

#### 4、题目

用 Verilog 或 VHDL写一段代码 , 实现消除一个 glitch （毛刺）？
将传输过来的信号经过两级触发器就可以消除毛刺。 （这种方式消除毛刺是需要满足一定条件的，并不能保证一定可以消除，毛刺宽度小于一个时钟周期）

```verilog
//消除信号中的一个毛刺
module demo(
	input clk,
    input data_in,
    input reg data_out
);
	reg data_r;
    always@(posedge clk)begin
        data_r <= data_in;
        data_out <= data_r;
    end
endmodule
```

#### 5、题目

实现三分频电路， 3/2 分频电路等（偶数倍分频 奇数倍分频）
图 2 是 3 分频电路，用 JK-FF 实现 3 分频很方便，不需要附加任何逻辑电路就能实现同步计数分频。 但用 D-FF 实现 3 分频时， 必须附加译码反馈电路， 如图 2 所示的译码复位电路，强制计数状态返回到初始全零状态，就是用 NOR 门电路把 Q2，Q1=―11B‖的状态译码产生―H‖ 电平复位脉冲，强迫 FF1 和 FF2 同时瞬间（在下一时钟输入 Fi 的脉冲到来之前）复零，于是 Q2， Q1=―11B‖状态仅瞬间作为 ―毛刺 ‖存在而不影响分频的周期，这种 ―毛刺 ‖仅在 Q1中存在， 实用中可能会造成错误， 应当附加时钟同步电路或阻容低通滤波电路来滤除， 或者仅使用 Q2 作为输出。 D-FF 的 3 分频，还可以用 AND 门对 Q2，Q1 译码来实现返回复零。

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_11.png">

#### 6、题目

用波形表示 D触发器的功能。（扬智电子笔试）

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_12.png">

#### 7、题目

用传输门和倒向器搭一个边沿触发器（ DFF）。（扬智电子笔试）--通过级联两个 D 锁存器组成

**时钟加反相器buff，增强时钟驱动能力**

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_13.png">

#### 8、题目

用逻辑门画出 D触发器。（威盛 VIA 2003.11.06  上海笔试试题）

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_14.png">

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_15.png">

#### 9、题目

画出 DFF的结构图 , 用 verilog 实现之。（威盛）

```verilog
//同步复位
module dff(clk,d,qout
           input 	clk,
           input	d,
           output reg qout
          );
    always@(posedge clk)begin
        if(!reset)
            qout < =0;
        else
            qout < =d;
    end
endmodule
```

#### 10、题目

画出一种 CMOS的 D锁存器的电路图和版图。（未知）或者是利用前面与非门搭的 D 锁存器实现

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_16.png">

#### 11、题目

用 D触发器做个 4 进制的计数。（华为）

使用两个D触发器，每个触发器的Q’与D相连接，第一个D触发器的Q'接第二个触发器的CLK ，两个D触发器的Q'即为计数器的值

也可以按照时序逻辑电路的设计步骤来：
写出状态转换表
寄存器的个数确定
状态编码
卡诺图化简
状态方程，驱动方程等



#### 12、题目

实现 N位 Johnson Counter,  N=5。（南山之桥）

约翰逊(Johnson)计数器又称扭环计数器,是一种用n位触发器来表示2n个状态的计数器。它与环形计数器不同,后者用n位触发器仅可表示n个状态。2~n进制计数器(n为触发器的个数)有2~n个状态。若以四位二进制计数器为例,它可表示16个状态。但由于[8421码](https://www.baidu.com/s?wd=8421码&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)每组代码之间可能有二位度或二位以上的二进制代码发生改变,这在计数器中特别回是异步计数器中就有可能产生错误的译码信号,从而造成永久性的错误。而约翰逊计数答器的状态表中,相邻两组代码只可能有一位二进制代码不同,故在计数过程中不会产生错误的译码信号。鉴于上述优点,约翰逊计数器在同步计数器中应用比较广泛。

扭环计数器有2N个状态，其中有无效状态，

```verilog
module johnson#(
	parameter N = 4
)(
    input clk,
    input rst_n,
    output reg [N-1:0] q
);
    always@(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)
            q	<= {N{1'b0}};
        else
            q	<= {~q[0],q[N-1,1]}
    end
endmodule
```

#### 13、题目

请用 HDL描述四位的全加法器、 5 分频电路。（仕兰微电子）

```verilog
//四位全加器
module adder4(a,b,ci,s,co);
    input ci;
    input [3:0] a,b;
    output co;
    output [3:0] s;
    assign {co,s}=a+b+ci;
endmodule
```

5分频电路

```verilog
//占空比不是50%
module div_5(clk,rst_n,clk_out);
    input clk,rst_n;
    output reg clk_out;
    reg [3:0] count;
    always@(posedge clk or negedge rst_n) begin
        if(!rst_n)begin
            count <= 0;
            clk_out <= 0;
        end
        else if(count == 3'd4) begin
            count <= 0;
            clk_out <= 1'b1;
        end
        else begin
            count<=count+1;
            clk_out <= 1'b0;
        end
    end
endmodule

```

实现7倍分频且占空比为 50% 的情况：

```verilog
module  div_7_55(
	//system signals
	input			clk ,
	input			rst_n ,
	output			clk_out	
);
reg		clk_pose;
reg		clk_nege;
reg 	[3:0]	count_pose	;
reg 	[3:0]	count_nege	;
//上升沿计数
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		count_pose	<= 4'd0;
	else if(count_pose == 4'd6)
		count_pose	<= 4'd0;
	else
		count_pose	<= count_pose + 1'b1;	
end
//下降沿计数
always @ (negedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		count_nege	<= 4'd0;
	else if(count_nege == 4'd6)
		count_nege	<= 4'd0;
	else
		count_nege	<= count_nege + 1'b1;
end
//上升沿分频
always @ (posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		clk_pose	<= 1'b0;
	else if(count_pose < 4'd3)	
        clk_pose	<= 1'b0;
    else
    	clk_pose	<= 1'b1;
end
//下降沿分频
always @ (negedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)
		clk_nege	<= 1'b0;
	else if(count_nege < 4'd3)
		clk_nege	<= 1'b0;
	else
		clk_nege	<= 1'b1;			     
end
//上升沿分频与下降沿分频相与，得50%占空比
assign clk_out = clk_nege & clk_pose;
    
endmodule
```



#### 14、题目

用 VERILOG或 VHDL写一段代码，实现 10 进制计数器。（未知）

```verilog
module counter10 (
    input clk,
    input rst,
    output reg [3:0] count
);
    always@(posedge clk or negedge rst)begin
        if(!rst)
            count <= 4'd0;
        else if(count >= 4'd9)
            count <= 4'd0;
        else
            count <= count + 1'b1;
    end
endmodule
```



#### 15、题目

3.设计一个计算连续 Leading Zeros 个数的电路。输入 8-bit，输出 4-bit。
00001000 0100
00100010 0010
10001000 0000
可以 parameterize 你的设计吗？其 hardware 是什么样子的？

https://blog.csdn.net/qq_36248682/article/details/105444271

```verilog
//不清楚while能否北综合，可以修改成为for循环，综合成组合逻辑
module  leading_zeros#(
		parameter M = 8,
		parameter N = 4
	)(
	input			[M - 1:0]	data_in	,
	output		reg	[N - 1:0]	data_out
	
);
	reg [N - 1:0] i;
	always @ (*) begin
		i = 0;
		while(data_in[M-1-i] == 1'b0 && i < M)begin
			i = i + 1;
		end
	    data_out = i;    
	end
endmodule
```

vivado 2018.3

7级MUX级联

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_17.png">



#### 16、题目

设计地址生成器。要求依次输出以下序列：
0,8,2,10,4,12,6,14,1,9,3,11,5,13,7,15,
16,24,18,26,.................................,31,
32,40,34,42,.................................,47,
48,56,50,58,.................................,63,
64,72,66,76,.................................,79

```verilog
//将数据每16个数分为一组，再将一族中相同规律的数字总结起来
module  addr_counter(
    //system signals
    input					clk 			,
    input					rst_n 			,
    output	reg		[7:0]	counter
);
    reg [3:0]	count_0;
    reg [7:0]	count_1;
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)begin
            count_0 <= 4'd0;
            count_1 <= 8'd0;
        end		
        else if(count_1 < 8'd80)begin
            count_0 <= count_0 + 1'b1;
            count_1 <= count_1 + 1'b1;
        end	      
    end
    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)
            counter <= 8'd0;
        else begin
            case (count_0) 
                0,2,4,6,9,11,13,15: counter <= count_1;
                1,3,5,7: counter <= count_1 + 8'd7;
                8,10,12,14: counter <= count_1 - 8'd7;
                default: counter <= 8'd0;
            endcase
        end	     
    end

endmodule

```



17、题目

使用verilog写一段代码，实现消除一个毛刺

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_18.png">

```verilog
//使用两级D触发器实现
//消除大于一个周期，小于两个周期的毛刺，需要3级D触发器实现
module digital_filter_(clk_in,rst,host_rst,host_rst_filter);
    input  clk_in;
    input  rst;
    input  host_rst;
    output host_rst_filter;
    reg host_rst_d1;
    reg host_rst_d2;

    always@(posedge clk_in or negedge rst) begin
        if(~rst) begin
            host_rst_d1 <= 1'b1;
            host_rst_d2 <= 1'b1;
        end
        else begin
            host_rst_d1 <= host_rst;
            host_rst_d2 <= host_rst_d1;
        end
    end

    assign host_rst_filter = host_rst_d1 | host_rst_d2;
endmodule
```

https://blog.csdn.net/weixin_43343190/article/details/82961371

#### 17、题目

用Verilog实现串并转换

- lsb优先
- msb优先

```verilog
module Deserialize(
    input           clk,
    input           rst_n,
    input           data_i,
    output   reg [7:0] data_o
);
	//先补充高位
    /*always @(posedge clk or negedge rst_n)begin 
        if(rst_n == 1'b0)
            data_o <= 8'b0; 
        else 
            data_o <= {data_o[6:0], data_i}; 
    end*/
	//先补充低位
    always @(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)
            data_o <= 8'b0;
        else
            data_o <= {data_i,data_o[7:1]}; 
    end

endmodule
```

**并行转串行数据输出**：采用计数方法，将并行的数据的总数先表示出来，然后发送一位数据减一，后面的接收的这样表示: data_out <= data[cnt];//cnt表示计数器



#### 18、题目

用Verilog实现一个异步双端口ram，深度16，位宽8bit。A口读出，B口写入。支持片选，读写请求，要求代码可综合。

RAM读写冲突解决方法

https://blog.csdn.net/weixin_45985643/article/details/104652439

```verilog
module Dual_Port_Sram
    #(
        parameter           ADDR_WIDTH  =   4,
        parameter           DATA_WIDTH  =   8,
        parameter           DATA_DEPTH  =   1 << ADDR_WIDTH
    )
    (
        input               clka,
        input               clkb,
        input               rst_n,
        input               csen_n,
        //Port A Signal     
        input       [ADDR_WIDTH-1:0]   addra,
        output   reg   [DATA_WIDTH-1:0]   data_a,
        input               rdena_n,
        //Port B Signal     
        input       [ADDR_WIDTH-1:0]   addrb,
        input               wrenb_n,
        input       [DATA_WIDTH-1:0]    data_b 
    );

    integer     i;
    reg     [DATA_WIDTH-1:0]   register[DATA_DEPTH-1:0];

    always @(posedge clkb or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            for(i = 0; i < DATA_DEPTH; i = i + 1)
                register[i] <= 'b0000_1111;
        end
        else if(wrenb_n == 1'b0 && csen_n == 1'b0)
            register[addrb] <= data_b;  
    end

    always @(posedge clka or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            data_a <= 0;
        end
        else if(rdena_n == 1'b0 && csen_n == 1'b0 && wrenb_n == 1'b0 && addra == addrb)
            data_a <= data_b;
        else if(rdena_n == 1'b0 && csen_n == 1'b0 && wrenb_n == 1'b1)
            data_a <= register[addra];
        else 
            data_a <= data_a;
    end

endmodule 
```

#### 19、题目

题目：用Verilog实现glitch free时钟切换电路。输入sel，clka，clkb，sel为1输出clka，sel为0输出clkb。

https://blog.csdn.net/bleauchat/article/details/96180815

https://www.cnblogs.com/lyc-seu/p/12627297.html   3种方法

```verilog
//将选择信号时间错开，避免毛刺的发生
module clk_sel(
    input sel,
    input clka,
    input clkb,
    output clk_out
);
    reg ra,rb;
    always@(negedge clka)begin
        if(sel == 1'b1)
            ra <= 1'b1;
        else
            ra <= 1'b0;
    end
    always@(negedge clkb)begin
        if(sel == 1'b0)
            rb <= 1'b1;
        else
            rb <= 1'b0;
    end
    assign clk_out = (ra & clka) | (rb & clkb);

endmodule
```



#### 20、题目

用Verilog实现异步复位同步释放电路。

```verilog
module Sys_Rst(
    input       clk,
    input       rst_n,
    output      sys_rst
);

    reg     rst_r0;
    reg     rst_r1;
    //异步复位将复位状态存储起来，再经过同步状态输出
    always @(posedge clk or negedge rst_n)begin
        if(rst)begin
            rst_r0 <= 1'b0;
            rst_r1 <= 1'b0;
        end
        else begin
            rst_r0 <= 1'b1;
            rst_r1 <= rst_r0;
        end
    end
    assign  sys_rst = rst_r1;

endmodule
```

#### 21、题目

用Verilog实现按键抖动消除电路，抖动小于15ms，输入时钟12MHz。

http://www.yasheng.fun/posts/3675679110/

```verilog
//延迟15ms时间输出，首先进行边沿检测
module debounce(
    input           clk,//12Mhz     input           rst_n,
    input           key_in,
    output          key_flag
);
    parameter   JITTER  =   180000;//12Mhz / (1/15ms) 
    reg   [1:0]     key_r;
    wire             change;
    reg   [15:0]    delay_cnt;
	//边沿检测
    always @(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            key_r <= 0;
        end
        else begin
            key_r <= {key_r[0],key_in};
        end
    end
    assign  change = (~key_r[1] & key_r[0]) | (key_r[1] & ~key_r[0]);
	//计数
    always @(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            delay_cnt <= 0;
        end
        else if(change == 1'b1)
            delay_cnt <= 0;
        else if(delay_cnt == JITTER)
            delay_cnt <= delay_cnt;
        else 
            delay_cnt <= delay_cnt + 1;
    end

    assign  key_flag = ((delay_cnt == JITTER - 1) && (key_in == 1'b1))? 1'b1: 1'b0;

endmodule
```

#### 22、题目

用Verilog实现一个同步FIFO，深度16，数据位宽8bit。

```verilog
module fifo_syn #( 
    parameter WIDTH = 8 , 
    parameter DEPTH = 256 //深度 
)
    ( //system signals 
        input clk , 
        input rst_n , 
        input fifo_wr , 
        input fifo_rd , 
        input [WIDTH - 1:0] data_in , 
        output reg [WIDTH - 1:0] data_out , 
        output full , output empty ); 
    reg [7 : 0] ram [DEPTH - 1'b1 : 0]; //开辟存储区 
    reg [DEPTH-1 : 0] count; //计数 
    reg [DEPTH-1 : 0] p_wr;  //写指针 
    reg [DEPTH-1 : 0] p_rd;  //读指针 //简单状态机 
    always @ (posedge clk or negedge rst_n) begin 
        if(rst_n == 1'b0)begin 
            count <= 0; 
            p_wr <= 0; 
            p_rd <= 0; 
            data_out<= 8'd0; 
        end 
        else begin 
            case ({fifo_wr,fifo_rd}) 
                2'b10 :begin 
                    if(~full)begin 
                        ram[p_wr] <= data_in; 
                        count <= count + 1'b1; 
                        p_wr <= p_wr + 1'b1; 
                    end 
                end 
                2'b01 :begin 
                    if(~empty)begin 
                        data_out <= ram[p_rd]; 
                        count <= count - 1'b1; 
                        p_rd <= p_rd + 1'b1; 
                    end 
                end 
                2'b11 :begin 
                    if(empty)begin 
                        ram[p_wr] <= data_in; 
                        count <= count + 1'b1; 
                        p_wr <= p_wr + 1'b1; 
                    end 
                    else begin 
                        ram[p_wr] <= data_in; 
                        p_wr <= p_wr + 1'b1; 
                        data_out <= ram[p_rd]; 
                        p_rd <= p_rd + 1'b1; 
                        count <= count; 
                    end 
                end 
            endcase 
        end 
    end 
    //判断空满状态 
    assign full = (count == 8'hff) ? 1 : 0; 
    assign empty = (count == 8'd0) ? 1 : 0; 
endmodule

```

#### 23、题目

编写Verilog代码描述跨时钟域信号传输，慢时钟域到快时钟域

```verilog
//触发器打拍实现
module clk_2clk(
    input clk,
    input rst_n,
    input signal_r,
    output signal_out
);
    reg     [1:0]   signal_r;
    always @(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)begin
            signal_r <= 2'b00;
        end
        else begin
            signal_r <= {signal_r[0], signal_in};
        end
    end
    assign  signal_out = signal_r[1];
endmodule
```

#### 24、题目

编写Verilog代码描述跨时钟域信号传输，**快时钟域到慢时钟域**

 跨时钟域处理从快时钟域到慢时钟域，如果是下面第一个图，cklb则可以采样到signal_a_in，但是如果只有单脉冲，如第二个图，则不能确保采样掉signal_a_in。这个时候用两级触发器同步是没有用的。

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_19.png">

```verilog
/Synchronous 
module Sync_Pulse(
    input           clka,
    input           clkb,
    input           rst_n,
    input           pulse_ina,
    output          pulse_outb,
    output          signal_outb
);

//------------------------------------------------------- reg             signal_a;
reg             signal_b;
reg     [1:0]   signal_b_r;
reg     [1:0]   signal_a_r;

//------------------------------------------------------- //在clka下，生成展宽信号signal_a always @(posedge clka or negedge rst_n)begin
    if(rst_n == 1'b0)begin
        signal_a <= 1'b0;
    end
    else if(pulse_ina == 1'b1)begin
        signal_a <= 1'b1;
    end
    else if(signal_a_r[1] == 1'b1)
        signal_a <= 1'b0;
    else 
       signal_a <= signal_a;
end

//------------------------------------------------------- //在clkb下同步signal_a always @(posedge clkb or negedge rst_n)begin
    if(rst_n == 1'b0)begin
        signal_b <= 1'b0;
    end
    else begin
        signal_b <= signal_a;
    end
end

//------------------------------------------------------- //在clkb下生成脉冲信号和输出信号 always @(posedge clkb or negedge rst_n)begin
    if(rst_n == 1'b0)begin
       signal_b_r <= 2'b00;
   end
   else begin
       signal_b_r <= {signal_b_r[0], signal_b};
    end
end

assign    pulse_outb = ~signal_b_r[1] & signal_b_r[0];
assign    signal_outb = signal_b_r[1];

//------------------------------------------------------- //在clka下采集signal_b[1]，生成signal_a_r[1]用于反馈拉低signal_a always @(posedge clka or negedge rst_n)begin
    if(rst_n == 1'b0)begin
       signal_a_r <= 2'b00;
    end
   else begin
       signal_a_r <= {signal_a_r[0], signal_b_r[1]};
    end

end

endmodule
```



上述代码可以实现快到慢，慢到快时钟域任意转换，pulse_outb会输出单个脉冲，signal_outb输出信号时间长度最少为clkb的四个周期，当signal_a_in的信号长度大于clkb的四个周期，signal_outb输出与signal_a_in时间长度相同。

#### 25、题目

写异步 D 触发器的 verilog module 。 （扬智电子笔试） 异步复位

```verilog
module dff8(clk , reset, d, q);
    input clk;
    input reset; 
    input [7:0] d; 
    output [7:0] q;
    reg [7:0] q; 
    always @ (posedge clk or posedge reset)
        if(reset) 
            q <= 0; 
    	else 
       	 	q <= d; 
endmodule
```

#### 26、题目

用 D 触发器实现 2 倍分频的 Verilog 描述？ （汉王笔试） 

```verilog
module divide2( clk , clk_o, reset);
    input clk , reset; 
    output clk_o; 
    wire in; 
    reg out ; 
    always @ ( posedge clk or posedge reset) 
        if ( reset)
            out <= 0; 
    	else 
            out <= ~in; 
 
    assign clk_o = out; 
endmodule
```

#### 27、题目

可编程逻辑器件在现代电子设计中越来越重要，请问：

 a) 你所知道的可编程逻辑器 件有哪些？ 

b) 试用 VHDL或 VERILOG、ABLE描述 8 位 D 触发器逻辑。（汉王笔试） 

PAL，PLD，CPLD，FPGA。

```verilog
module dff8(
    input clk,
    input rst_n, 
    input [7:0] d,
    output reg [7:0] q
);
    always@ (posedge clk or negedge rst_n)begin
        if(!rst_n) 
            q <= 0; 
        else 
            q <= d; 
    end
endmodule
```



#### 28、题目

利用SRAM实现同步FIFO-未总结

应该是采用地址的方式，将SRAM变成存储单元，每一次的数据输入，都将调用SRAM的读写

https://blog.csdn.net/LscYBZB/article/details/89017316  -- 没有总结

### 状态机

moore状态机和mealy状态机可以相互转化，moore状态多，mealy状态少

moore状态机的输出，最好使用时序逻辑，避免组合逻辑形成的毛刺

https://blog.csdn.net/Reborn_Lee/article/details/88918615







#### 1、题目

设计一个自动饮料售卖机，饮料 10 分钱，硬币有 5 分和 10 分两种，并考虑找零，

1. 画出 fsm（有限状态机）
2. 用 verilog 编程，语法要符合 FPGA设计的要求
3. 设计工程中可使用的工具及设计大致过程？
   设计过程：
   1、首先确定输入输出， A=1 表示投入 10 分，B=1 表示投入 5 分，Y=1 表示弹出饮料， Z=1
   表示找零。
   2、确定电路的状态， S0 表示没有进行投币， S1 表示已经有 5 分硬币。
   3、画出状态转移图。

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_20.png">

米利状态机，2段式

```verilog
module sell(
    input	clk,
    input	rst_n,
    input	a,
    input	b,
    output	y,
    output	z
);
    parameter	S0	= 1'b0,S1	= 1'b1;
    reg	stage_current,stage_next;

    always@(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)
            state_current <= S0;
        else
            state_current <= stage_next;
    end
    always@(*)begin
        case(stage_current)
            S0:begin
                if(a == 1'b1 && b == 1'b0) begin 
                    stage_next = S0;
                    y = 1;
                    z = 0;
                end
                else if(a == 1'b0 && b == 1'b1)begin 
                    stage_next = S1;
                    y = 0;
                    z = 0;
                end
                else begin
                    stage_next = S0;
                    y = 0;
                    z = 0;
                end
            end
            S1:begin
                if(a == 1'b1 && b == 1'b0) begin 
                    stage_next = S0;y = 1;z = 1;
                end
                else if(a == 1'b0 && b == 1'b1) begin 
                    stage_next = S0;y = 1 ;z= 0;
                end
                else begin
                    stage_next = S0;y = 0 ;z= 0;
                end
            end
            default:stage_next = S0;
        endcase
    end

endmodule
```



扩展：设计一个自动售饮料机的逻辑电路。它的投币口每次只能投入一枚五角或一元的硬币。投入一元五角硬币后给出饮料；投入两元硬币时给出饮料并找回五角。

1. 确定输入输出，投入一元硬币 A=1 ，投入五角硬币 B=1，给出饮料 Y=1，找回五角Z=1；

2. 确定电路的状态数，投币前初始状态为 S0，投入五角硬币为 S1，投入一元硬币为S2。画出转该转移图，根据状态转移图可以写成 Verilog 代码。

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_21.png">

```verilog
module drink_state (
    //system signals
    input		clk 	,
    input		rst_n 	,
    input		a 	, 
    input		b 	,					
    output 	reg y,
    output 	reg z
);
    parameter [1:0] S0 = 2'b00;
    parameter [1:0] S1 = 2'b01;
    parameter [1:0] S2 = 2'b10;

    reg [1:0] state_current;
    reg [1:0] state_next;
    always@(posedge clk or negedge rst_n)begin
        if(rst_n == 1'b0)
            state_current <= S0;
        else
            state_current <= state_next;
    end    
    always@(*)begin
        case(state_current)
            S0:	if(a == 1'b0 && b == 1'b1)
                state_next <= S1;
            else if(a == 1'b1 && b == 1'b0)
                state_next <= S2;
            else
                state_next <= S0;   
            S1:	if(a == 1'b0 && b == 1'b1)
                state_next <= S2;
            else if(a == 1'b1 && b== 1'b0)
                state_next <= S0;
            else
                state_next <= S1;

            S2:	if(a == 1'b0 && b == 1'b1)
                state_next <= S0;
            else if(a == 1'b1 && b== 1'b0)
                state_next <= S0;
            else
                state_next <= S2;
        endcase
    end

    always @ (posedge clk or negedge rst_n) begin
        if(rst_n == 1'b0)begin
            y <= 1'b0;
            z <= 1'b0;
        end	
        else if(state_current == S1)begin
            if(a == 1'b1 && b== 1'b0)begin   			
                y <= 1'b1;
                z <= 1'b0;
            end 
            else begin
                y <= 1'b0;
                z <= 1'b0;
            end   			
        end
        else if(state_current == S2)begin
            if(a == 1'b0 && b == 1'b1)begin
                y <= 1'b1;
                z <= 1'b0;
            end
            else if(a == 1'b1 && b== 1'b0)begin
                y <= 1'b1;
                z <= 1'b1;
            end
            else begin
                y <= 1'b0;
                z <= 1'b0;
            end
        end	
        else begin
            y <= 1'b0;
            z <= 1'b0;
        end            
    end 
endmodule
```



#### 2、题目

画状态机，接受 1，2，5 分钱的卖报机，每份报纸 5 分钱。（扬智电子笔试）
1、确定输入输出，投 1 分钱 A=1 ，投 2 分钱 B=1 ，投 5 分钱 C=1 ，给出报纸 Y=1
2、确定状态数画出状态转移图，没有投币之前的初始状态 S0，投入了 1 分硬币 S1，投入
了 2 分硬币 S2，投入了 3 分硬币 S3 ，投入了 4 分硬币 S4。
3、画卡诺图或者是利用 verilog 编码

链接解释没有给出，当输入4分钱后，给出5分钱的情况，不知是否需要处理。

https://blog.csdn.net/dongdongnihao_/article/details/79954335

待续



#### 3、题目

设计一个自动饮料售卖机，饮料 10 分钱，硬币有 5 分和 10 分两种，并考虑找零： （ 1）
画出 fsm（有限状态机）；（ 2）用 verilog 编程，语法要符合 fpga 设计的要求；（ 3）设
计工程中可使用的工具及设计大致过程。（未知）
1、输入 A=1 表示投 5 分钱， B=1 表示投 10 分钱，输出 Y=1 表示给饮料， Z=1 表示找零
2、确定状态数，没投币之前 S0，投入了 5 分 S1

#### 4、题目

画出可以检测 10010 串的状态图 , 并 verilog 实现之。（威盛）
1、输入 data，1 和 0 两种情况，输出 Y=1 表示连续输入了 10010
2、确定状态数没输入之前 S0，输入一个 0 到了 S1,10 为 S2,010 为 S3,0010 为 S4



#### 5、题目

用 FSM实现 101101 的序列检测模块。（南山之桥）
a 为输入端， b 为输出端，如果 a 连续输入为 101101 则 b 输出为 1，否则为 0。
例如 a： 0001100110110110100110
b： 0000000000100100000000
请画出 state machine ；请用 RTL描述其 state machine 。（未知）
确定状态数，没有输入或输入 0 为 S0，1 为 S1，01 为 S2,101 为 S3,1101 为 S4，01101
为 S5。知道了输入输出和状态转移的关系很容易写出状态机的 verilog 代码，一般采用两段
式状态机

#### 6、题目

用 verilog/vhdl 写一个 fifo 控制器包括空，满，半满信号。

#### 7、题目

用 verilog/vddl 检测 stream 中的特定字符串分状态用状态机写。但是可以有两种方法

状态机和移位寄存器

#### 8、题目

画一个十字路口红绿灯信号等； 

具体实现看链接

https://blog.csdn.net/weixin_43417303/article/details/104382822

#### 9、题目

帧头检测 连续两次收到 ‘0x23’ ，输出一个脉冲



#### 10、摩尔状态机

输出信号，采用always时序块，而且使用 `state_current `，输出延迟一个时钟周期，使用 `state_next` 解决输出晚一拍的问题

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_22.png">

```verilog
//检测序列10010
`timescale      1ns/1ps
module fsm_moore (
    //system signals
    input		clk 	,
    input		rst_n 	,
    input		data_in , 
    output	reg	data_flag

);
    //三段式状态机
    //状态存储器
    reg				[	2:0]	state_c;
    reg				[	2:0]	state_n;

    //状态定义
    localparam			IDLE	=	3'b000	;
    localparam			S1		=	3'b001	;
    localparam			S2		=	3'b010	;
    localparam			S3		=	3'b011	;
    localparam			S4		=   3'b100	;
    localparam			S5		=   3'b101	;

    //第一段：同步时序always模块，格式化描述次态寄存器迁移到现态寄存器(不需更改）
    always@(posedge clk or negedge rst_n)begin
        if(!rst_n)begin
            state_c <= IDLE;
        end
        else begin
            state_c <= state_n;
        end
    end
    //第二段：组合逻辑always模块，描述状态转移条件判断
    always@(*)begin
        case(state_c)
            IDLE:begin state_n = data_in == 1'b1 ? S1 : IDLE;end
            S1	:begin state_n = data_in == 1'b0 ? S2 : IDLE;end
            S2	:begin state_n = data_in == 1'b0 ? S3 : IDLE;end
            S3	:begin state_n = data_in == 1'b1 ? S4 : IDLE;end
            S4	:begin state_n = data_in == 1'b0 ? S5 : IDLE;end
            S5	:begin state_n = data_in == 1'b0 ? S3 : IDLE;end
            default:begin state_n = IDLE;end
        endcase
    end
    //第三段：同步时序always模块，格式化描述寄存器输出（可有多个输出）
    //可以使用state_next 解决输出晚一拍的问题
    always  @(posedge clk or negedge rst_n)begin
        if(!rst_n) 
            data_flag <= 1'b0;
        else 
            case(state_n)
                S5	: data_flag <= 1'b1;
                default:data_flag <= 1'b0;
            endcase
    end
endmodule

```

#### 11、米利状态机

输出信号，采用always时序块，而且使用 `state_current `，根据输入决定输出结果，输出没有晚一拍的问题

```verilog
//序列检测10010
`timescale      1ns/1ps
module fsm_mealy (
	//system signals
	input		clk 	,
	input		rst_n 	,
	input		data_in , 
	output	reg	data_flag
);

//三段式状态机

//状态存储器
reg				[	2:0]	state_c;
reg				[	2:0]	state_n;

//状态定义
localparam			IDLE	=	3'b000	;
localparam			S1		=	3'b001	;
localparam			S2		=	3'b010	;
localparam			S3		=	3'b011	;
localparam			S4		=   3'b100	;
localparam			S5		=   3'b101	;

//第一段：同步时序always模块，格式化描述次态寄存器迁移到现态寄存器(不需更改）
always@(posedge clk or negedge rst_n)begin
	if(!rst_n)begin
		state_c <= IDLE;
	end
	else begin
		state_c <= state_n;
	end
end
//第二段：组合逻辑always模块，描述状态转移条件判断
always@(*)begin
	case(state_c)
		IDLE:begin state_n = data_in == 1'b1 ? S1 : IDLE;end
		S1	:begin state_n = data_in == 1'b0 ? S2 : IDLE;end
		S2	:begin state_n = data_in == 1'b0 ? S3 : IDLE;end
		S3	:begin state_n = data_in == 1'b1 ? S4 : IDLE;end
		S4	:begin state_n = data_in == 1'b0 ? S5 : IDLE;end
		S5	:begin state_n = data_in == 1'b0 ? S3 : IDLE;end
		default:begin state_n = IDLE;end
	endcase
end

//第三段：同步时序always模块，格式化描述寄存器输出（可有多个输出）
//使用state_current和当前输入的信号决定当前输出，信号无延迟
always  @(posedge clk or negedge rst_n)begin
	if(!rst_n)begin     
		data_flag <= 1'b0;
	end
	else 
		case(state_c)
			S4	: data_flag <= (data_in == 1'b0) ? 1'b1 : 1'b0;
			default:data_flag <= 1'b0;
		endcase
end
endmodule
//只有第二段的状态转移，和最后一段的逻辑输出，需要根据状态图进行调整
//（该序列检测，moore和mealy的第二段状态转移是一样的）

```

### 版图

#### 1、题目

用 mos管搭出一个二输入与非门？

与非门：上并下串 或非门：上串下并

<img src="/images/post_images/fpga_knowledge_summary_2/fpga_knowledge_summary_2_23.png">

画出 CMOS电路的晶体管级电路图 , 实现 Y=A*B+C(D+E).( 仕兰微电子 ) ？

画出 Y=A*B+C 的 CMOS 电路图，画出 Y=A*B+C*D 的 CMOS 电路图。



#### 2、题目

 Please show the CMOS inverter schematic,  layout and its cross section  with P-well process.  Plot its transfer  curve (Vout-Vin)  and also explain the operation region of PMOS and NMOS for each segment of the transfer  curve?  （威盛笔试题 circuit design-beijing-03.11.09 ）

反相器版图

**版图设计用的P衬底**

p衬底上做的是nmos器件，而由于[电子迁移率](https://www.baidu.com/s?wd=电子迁移率&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)要大于空穴，因此nmos器件比pmos器件更快，所以集成电路中使用nmos器件较多，所以多用p衬底！不过现在用双井工艺的地方也挺多啊！



#### 3、题目

 To design a CMOS inverter  with  balance rise and fall  time,  please define the ration of channel width of PMOS and NMOS and explain?  P管要比 N管宽

要设计具有平衡上升和下降时间的CMOS逆变器，请定义PMOS和NMOS通道宽度的配给并解释？

宽长比2​：1，详见数字集成电路课件​

#### 4、题目

Please draw the transistor level schematic of a  CMOS 2 input AND gate and explain which input has faster response for output rising edge.(less delay time) 。（威盛笔试题 circuit design-beijing-03.11.09 ）

请绘制 CMOS 2 输入与门 的晶体管电平原理图，并解释哪个输入对输出上升沿响应更快。（延迟时间更少） |



#### 5、题目

CMOS结构

#### 6、题目

请用P-井工艺显示CMOS逆变器的切片、布局及其横截面。绘制其传输曲线（Vout-Vin），并解释传输曲线的每个段的 PMOS 和 NMOS 的操作区域？



#### 7、题目

To design a CMOS invertor with balance rise and fall time,please define the ration of channel width of PMOS and NMOS and explain?



#### 8、题目

please draw the transistor level schematic of a cmos 2 input AND gate and explain which
input has faster response for output rising edge.(less delay time) 。（威盛笔试题 circuit
design-beijing-03.11.09 ）



#### 9、题目

画出 CMOS的图，画出 tow-to-one mux gate 。



#### 10、题目

画出一种 CMOS的 D 锁存器的电路图和版图。 （未知）





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

