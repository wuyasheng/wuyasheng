---
title: 时序约束详解(2)
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理时序约束相关内容
categories: ☸ FPGA
tags:
  - FPGA
  - 时序约束
abbrlink: 54437889
date: 2020-07-15 21:10:09
layout:
password:
---

## 时序约束详解

---

注 : 本文内容总结至[文章1](http://www.technomania.cn/tutorials/fpga/timing-analysis/)、小梅哥时序约束课程

### 时序路径

典型的时序路径有4类，如下图所示，这4类路径可分为片间路径（标记①和标记③)和片内路径（标记②和标记④）。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_01.png">

对于所有的时序路径，我们都要明确其起点和终点，这4类时序路径的起点和终点分别如下表。

| 时序路径                              | 起点      | 终点     | 应用约束         |
| :------------------------------------ | :-------- | :------- | :--------------- |
| ①输入端口到FPGA内部第一级触发器的路径 | ChipA/clk | rega/D   | set_input_delay  |
| ②FPGA内部触发器之间的路径             | rega/clk  | regb/D   | create_clock     |
| ③FPGA内部末级触发器到输出端口的路径   | regb/clk  | ChipB/D  | set_output_delay |
| ④FPGA输入端口到输出端口的路径         | 输入端口  | 输出端口 | set_max_delay    |

---

### 时序模型

典型的时序模型如下图所示，一个完整的时序路径包括源时钟路径、数据路径和目的时钟路径，也可以表示为触发器+组合逻辑+触发器的模型。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_02.png">

该时序模型的要求为(公式1)
$$
T_{clk} ≥ T_{co} + T_{logic} + T_{routing} + T_{setup} - T_{skew}  
$$
其中，Tco为发端寄存器时钟到输出时间；Tlogic为组合逻辑延迟；Trouting为两级寄存器之间的布线延迟；Tsetup为收端寄存器建立时间；Tskew为两级寄存器的时钟歪斜，其值等于时钟同边沿到达两个寄存器时钟端口的时间差；Tclk为系统所能达到的最小时钟周期。

这里我们多说一下这个Tskew，skew分为两种，positive skew和negative skew，其中positive skew见下图，这相当于增加了后一级寄存器的触发时间。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_03.png">

但对于negative skew，则相当于减少了后一级寄存器的触发时间，如下图所示。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_04.png">

发送端寄存器产生的数据，数据经过Tco、Tlogic、Trouting后到达接收端，同时还要给接收端留出Tsetup的时间。而时钟延迟了Tskew的时间，因此有： 
$$
T_{data\_path} + T_{setup} <= T_{skew} + T_{clk} 
$$
对于同步设计Tskew可忽略(认为其值为0)，因为FPGA中的时钟树会尽量保证到每个寄存器的延迟相同。

公式中提到了建立时间，那保持时间在什么地方体现呢？

保持时间比较难理解，它的意思是reg1的输出不能太快到达reg2，这是为了防止采到的新数据太快而冲掉了原来的数据。保持时间约束的是同一个时钟边沿，而不是对下一个时钟边沿的约束。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_05.png">

在时钟沿到达之后，数据要保持Thold的时间，因此，要满足：
$$
T_{data\_path} =  T_{co} + T_{logic} + T_{routing} ≥ T_{skew} + T_{hold}
$$
在公式当中：Tco跟Tsu一样，也取决于芯片工艺，因此，一旦芯片型号选定就只能通过Tlogic和Trouting来改善Tclk。其中，Tlogic和代码风格有很大关系，Trouting和布局布线的策略有很大关系。

---

### 输入延迟约束

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_06.png">



由图中的节点可知

▷T0 --> T1：Tclk2（ext）可知，由pcb布线长度可知，1ns/5555mil；

▷T1 --> T2：Tclk2（in）未知，由FPGA布线决定；

▷T0 --> T3：Tclk1（in）可知，输入设备内部走线决定；

▷T3 --> T4：Tco 可知，输入设备输出寄存器的Tco；

▷T4 --> T5：Tdata（ext）可知，由pcb布线长度可知，1ns/5555mil；

▷T5 --> T6：Tdata（in）未知，由FPGA布线决定；

▷T6：Tsu 未知，由FPGA布线决定；

---

**分析建立时间**
$$
{}data\ arrive\ time =Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(ext) + T{data}(in)\tag{3-1}
$$

$$
data\ require\ time = Latch\ edge + T_{clk2}(ext) + T_{clk2}(in) - T_{su}\tag{3-2}
$$

$$
建立时间容限：Slack(su) =  data\ require\ time - data\ arrive\ time >= 0\\\ \tag{3-3}
$$

$$
Latch\ edge + T_{clk2}(ext) + T_{clk2}(in) - T_{su} \\\ -Launch\ edge - T_{clk1}(in) - T_{co} - T_{data}(ext) - T{data}(in) >= 0\tag{3-4}
$$

通过化简得：
$$
( Latch\ edge  - Launch\ edge ) \\\ - ( T_{data}(ext) - T_{clk2}(ext) + ( T_{clk1}(in) + T_{co} ) )>= - T_{clk2}(in) + T_{su} + T_{data}(in)\tag{3-5}
$$
由上式可知：数据经过最大的外部传输延迟到达目的寄存器时依旧满足目的奇存器的建立时间
$$
input\ delay\ max :  T_{data}(ext)(max) - T_{clk2}(ext)(min) + ( T_{clk1}(in)+ T_{co} )(max)\tag{3-6}
$$

---

**分析保持时间**
$$
data\ arrive\ time =Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(ext) + T{data}(in)\tag{4-1}
$$

$$
data\ require\ time = Latch\ edge + T_{clk2}(ext) + T_{clk2}(in) + T_{hold}\tag{4-2}
$$

$$
保持时间容限：Slack(hold) = data\ arrive\ time - data\ require\ time  >= 0\tag{4-3}
$$

$$
Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(ext) + T{data}(in) -\\\ Latch\ edge - T_{clk2}(ext) - T_{clk2}(in) - T_{hold}>= 0\tag{4-4}
$$

通过化简得：
$$
( Launch\ edge - Latch\ edge ) \\\ + ( T_{data}(ext) - T_{clk2}(ext) + ( T_{clk1}(in) + T_{co} ) )>= -T_{data}(in) + T_{clk2}(in) + T_{hold}\tag{4-5}
$$
由上式可知：数据经过最小的外部传输延迟到达目的寄存器时依旧满足目的奇存器的保持时间
$$
input\ delay\ min : T_{data}(ext)(min) - T_{clk2}(ext)(max) + ( T_{clk1}(in) + T_{co})(min)\tag{4-6}
$$

**注：以上分析的是当时钟由设备发送端发出，由FPGA接收时钟和数据，当时钟脱离FPGA，或者时钟由FPGA发出，则需要修改公式，通用公式如下**

**其中 Tclk2(ext)  为，时钟到FPGA的外部走线延迟；Tclk1(ext)  为，时钟到数据发送端的外部走线延迟；Tclk1(in) + Tco 为数据发送端的内部延迟**
$$
input\ delay\ max :  T_{data}(ext)(max) - ( T_{clk2}(ext)(min) -  T_{clk1}(ext)(max) ) + ( T_{clk1}(in) + T_{co})(max)
$$

$$
input\ delay\ min :  T_{data}(ext)(min) - ( T_{clk2}(ext)(max) -  T_{clk1}(ext)(min) ) + ( T_{clk1}(in) + T_{co})(min)
$$

---

### 输出延迟约束

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_07.png">

由图中的节点可知

▷T0 --> T1：Tclk2（in）未知，由FPGA布线决定；

▷T1 --> T2：Tclk2（ext）可知，由pcb布线长度可知，1ns/5555mil；

▷T0 --> T3：Tclk1（in）未知，由FPGA布线决定；

▷T3 --> T4：Tco 未知，FPGA输出寄存器的Tco；

▷T4 --> T5：Tdata（in）未知，由FPGA布线决定；

▷T5 --> T6：Tdata（ext）可知，由pcb布线长度可知，1ns/5555mil；

▷T6：Tsu 可知，输出设备的，输入寄存器的建立时间；

---

**分析建立时间**
$$
data\ arrive\ time =Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(in) + T_{data}(ext)\tag{1-1}
$$

$$
data\ arrive\ time =Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(in) + T_{data}(ext)\tag{1-2}
$$

$$
建立时间容限：Slack(su) =  data\ require\ time - data\ arrive\ time >= 0\tag{1-3}
$$


$$
Latch\ edge + T_{clk2}(in) + T_{clk2}(ext) - T_{su} \\\ - Launch\ edge - T_{clk1}(in) - T_{co} - T_{data}(in) - T_{data}(ext) >= 0\tag{1-4}
$$
通过化简得：
$$
( Latch\ edge  - Launch\ edge ) \\\ - (T_{data}(ext) - T_{clk2}(ext) + T_{su} )  >= - T_{clk2}(in) + T_{clk1}(in) + T_{co} +  T_{data}(in)\tag{1-5}
$$
由上式可知：数据经过最大的外部传输延迟到达目的寄存器时依旧满足目的奇存器的建立时间
$$
output\ delay\ max : T_{data}(ext) - T_{clk2}(ext) + T_{su}
$$

---

**分析保持时间**
$$
data\ arrive\ time = Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(in) + T_{data}(ext)\tag{2-1}
$$

$$
data\ require\ time = Latch\ edge + T_{clk2}(in) + T_{clk2}(ext) + T_{hold}\tag{2-2}
$$

$$
保持时间容限：Slack(hold) = data\ arrive\ time - data\ require\ time >= 0\tag{2-3}
$$

$$
Launch\ edge + T_{clk1}(in) + T_{co} + T_{data}(in) + T_{data}(ext) \\\ - Latch\ edge - T_{clk2}(in) - T_{clk2}(ext) - T_{hold} >= 0\tag{2-4}
$$

通过化简得：

$$
( Launch\ edge  - Latch\ edge ) \\\ + ( T_{data}(ext)  - T_{clk2}(ext) - T_{hold} ) >= -T_{clk1}(in) - T_{co} - T_{data}(in) + T_{clk2}(in)\tag{2-5}
$$
由上式可知：数据经过最小的外部传输延迟到达目的寄存器时依旧满足目的奇存器的保持时间
$$
output\ delay\ min : T_{data}(ext)  - T_{clk2}(ext) - T_{hold}\tag{2-6}
$$
**注：以上分析的是当时钟由FPGA发出，由设备接收时钟和数据，当时钟脱离FPGA，或者时钟由设备接收端发出，则需要修改公式，通用公式如下**

**其中 Tclk2(ext)  为，时钟到设备接收端的外部走线延迟；Tclk1(ext)  为，时钟到FPGA端的外部走线延迟；**
$$
output\ delay\ max : T_{data}(ext) - （ T_{clk2}(ext) - T_{clk1}(ext) ） + T_{su}
$$

$$
output\ delay\ min : T_{data}(ext)  - （ T_{clk2}(ext) - T_{clk1}(ext) ） - T_{hold}
$$

---


### 时序约束命令

#### 1、IO约束

用于IO引脚约束，约束顶层文件定义的出入输出引脚映射到器件的实际引脚，以及引脚电平设置

```tcl
set_property -dict {PACKAGE_PIN AJ16  IOSTANDARD  LVCMOS18} [get_ports "led[0]"    ]
```

举例

```tcl
set_property PACKAGE_PIN P17 [get_ports clk100]
set_property IOSTANDARD LVCMOS33 [get_ports clk100]
```



#### 2、时钟约束

使用命令`report_clocks`可以输出已经约束的时钟列表

##### **（1）时钟周期约束**

用于创建系统等时钟，可以设置时钟的周期，占空比等（其中`get_ports`获取的是FPGA的IO端口，`get_pins`获取的是FPGA内部子模块的Pin）

```tcl
create_clock -name <name> -period <period> -waveform {<rise_time> <fall_time>} [get_ports <input_port>]
```

| 参数      | 含义                                                         |
| :-------- | :----------------------------------------------------------- |
| -name     | 时钟名称                                                     |
| -period   | 时钟周期，单位为ns                                           |
| -waveform | 波形参数，第一个参数为时钟的第一个上升沿时刻，第二个参数为时钟的第一个下降沿时刻 |
| -add      | 在同一时刻源上定义多个时钟时使用                             |

举例说明

```tcl
create_clock -name clk0 -period 10.0 -waveform {0 5} [get_ports clk0]
create_clock -name clk1 -period 8.0 -waveform {2 8} [get_ports clk1]
```

##### **（2）衍生时钟**

用于产生中间生成的时钟如PLL，分频，倍频的时钟输出

```tcl
create_generated_clock -name <generated_clock_name> \
                       -source <master_clock_source_pin_or_port> \
                       -multiply_by <mult_factor> \
                       -divide_by <div_factor> \
                       -master_clock <master_clk> \
                       <pin_or_port>
```

| 参数         | 含义               |
| :----------- | :----------------- |
| -name        | 时钟名称           |
| -source      | 产生该时钟的源时钟 |
| -multiply_by | 源时钟的多少倍频   |
| -divide_by   | 源时钟的多少分频   |

举例说明

```tcl
create_generated_clock -name clk_samp -source [get_pins clk_gen_i0/clk_core_i0/clk_tx] -divide_by 32 [get_pins clk_gen_i0/BUFHCE_clk_samp_i0/O]
create_generated_clock -name spi_clk -source [get_pins dac_spi_i0/out_ddr_flop_spi_clk_i0/ODDR_inst/C] -divide_by 1 -invert [get_ports spi_clk_pin]
create_generated_clock -name clk_tx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT1]
create_generated_clock -name clk_rx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT0]
```

注：由PLL或者MMCM生成的时钟，软件可能会自行推导出衍生始终约束，用户并不清楚自动推导出的衍生钟的名字，当设计层次改变时，衍生钟的名字也有可能改变。但由于该衍生时钟的约束并非我们自定义的，因此可能会没有关注到它名字的改变，当我们使用者这些衍生时钟进行别的约束时，就会出现错误。

解决办法是用户自己手动写出自动推导的衍生时钟的名字，也仅仅写出名字即可，其余的不写。如下所示。

```tcl
create_generated_clock -name <generated_clock_name> \
                       -source <master_clock_source_pin_or_port>
```



##### **（3）时钟组**

```tcl
set_clock_groups -asynchronous -group <clock_name_1> -group <clock_name_2>
set_clock_groups -physically_exclusive  -group <clock_name_1> -group <clock_name_2>
```

举例1（异步时钟约束）

```tcl
create_clock -period 10 -name clk1 [get_ports clk1]
create_clock -period 8 -name clk2 [get_ports clk2]
set_clock_groups -asynchronous -group clk1 -group clk2
```

举例2（不同频率下的约束情况，时钟同一时刻只有一个有效）

```
create_clock -name clk1A -period 20.0 [get_ports clk1]
create_clock -name clk1B -period 10.0 [get_ports clk1] -add
create_clock -name clk1C -period 5.0  [get_ports clk1] -add 
create_clock -name clk2 -period 10.0 [get_ports clk2]
set_clock_groups -physically_exclusive -group clk1A -group clk1B -group clk1C
set_clock_groups -asynchronous -group "clk1A clk1B clk1C" -group clk2
```

##### **（4）虚拟时钟约束**

虚拟时钟通常用于设定对输入和输出的延迟约束，这个约束其实是属于IO约束中的延迟约束。虚拟时钟和前面讲的延迟约束的使用场景不太相同。顾名思义，虚拟时钟，就是没有与之绑定的物理管脚。 虚拟时钟主要用于以下三个场景：

- 外部IO的参考时钟并不是设计中的时钟
- FPGA I/O路径参考时钟来源于内部衍生时钟，但与主时钟的频率关系并不是整数倍
- 针对I/O指定不同的jitter和latency

使用 `create_clock` 创建，但是没有`get_ports` 参数

```tcl
create_clock -name sysclk -period 10 [get_ports clkin]
create_clock -name virclk -period 6.4
set_input_delay 2 -clock sysclk [get_ports A]
set_input_delay 2 -clock virclk [get_ports B]
```

**注：虚拟时钟必须在约束I/O延迟之前被定义**

#### 3、最大最小延迟约束

顾名思义，就是设置路径的max/min delay，主要应用场景有两个：

- 输入管脚的信号经过组合逻辑后直接输出到管脚
- 异步电路之间的最大最小延迟

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_08.png">

```tcl
set_max_delay <delay> [-datapath_only] [-from <node_list>][-to <node_list>][-through <node_list>]
set_min_delay <delay> [-from <node_list>] [-to <node_list>][-through <node_list>]
```

| 参数     | 含义                                                         |
| :------- | :----------------------------------------------------------- |
| -from    | 有效的起始节点包含:时钟,input(input)端口,或时序单元(寄存器,RAM)的时钟引脚. |
| -to      | 有效的终止节点包含:时钟,output(inout)端口或时序单元的数据端口. |
| -through | 有效的节点包含:引脚,端口,线网.                               |

max/min delay的约束平时用的相对少一些，因为在跨异步时钟域时，我们往往会设置`asynchronous`或者`false_path`。对于异步时钟，我们一般都会通过设计来保证时序能够收敛，而不是通过时序约束来保证。

#### 4、输入输出延迟约束

对于输入管脚，首先判断捕获时钟是主时钟还是衍生时钟，如果是主时钟，直接用`set_input_delay`即可，如果是衍生时钟，要先创建虚拟时钟，然后再设置delay。对于输出管脚，判断有没有输出随路时钟，若有，则直接使用`set_output_delay`，若没有，则需要创建虚拟时钟。

输入输出延迟约束（需要首先确定时钟）

```tcl
create_clock -name sysclk -period 10 [get_ports clkin]
set_input_delay 2 -max -clock sysclk [get_ports Datain]
set_input_delay 1 -min -clock sysclk [get_ports Datain]
```

```tcl
# 主时钟约束
create_clock -period 25.000 -name clk2 [get_ports clk_in2]

# 衍生时钟约束
create_generated_clock -name clk_samp -source [get_pins clk_gen_i0/clk_core_i0/clk_tx] -divide_by 32 [get_pins clk_gen_i0/BUFHCE_clk_samp_i0/O]
create_generated_clock -name spi_clk -source [get_pins dac_spi_i0/out_ddr_flop_spi_clk_i0/ODDR_inst/C] -divide_by 1 -invert [get_ports spi_clk_pin]
create_generated_clock -name clk_tx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT1]
create_generated_clock -name clk_rx -source [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKIN1] [get_pins clk_gen_i0/clk_core_i0/inst/mmcm_adv_inst/CLKOUT0]

# 设置异步时钟
set_clock_groups -asynchronous -group [get_clocks clk_samp] -group [get_clocks clk2]
# 延迟约束
create_clock -period 6.000 -name virtual_clock
set_input_delay -clock [get_clocks -of_objects [get_ports clk_pin_p]] 0.000 [get_ports rxd_pin]
set_input_delay -clock [get_clocks -of_objects [get_ports clk_pin_p]] -min -0.500 [get_ports rxd_pin]

set_input_delay -clock virtual_clock -max 0.000 [get_ports lb_sel_pin]
set_input_delay -clock virtual_clock -min -0.500 [get_ports lb_sel_pin]
set_output_delay -clock virtual_clock -max 0.000 [get_ports {txd_pin {led_pins[*]}}]
set_output_delay -clock virtual_clock -min -0.500 [get_ports {txd_pin {led_pins[*]}}]

set_output_delay -clock spi_clk -max 1.000 [get_ports {spi_mosi_pin dac_cs_n_pin dac_clr_n_pin}]
set_output_delay -clock spi_clk -min -1.000 [get_ports {spi_mosi_pin dac_cs_n_pin dac_clr_n_pin}]
```

#### 5、多周期约束

一般情况下默认按照单周期关系来分析数据路径，即数据的发起沿和捕获沿是最邻近的一对时钟沿，如下图所示

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_09.png">

但在实际的工程中，经常会碰到数据被发起后，由于路径过长或者逻辑延迟过长要经过多个时钟周期才能到达捕获寄存器；又或者在数据发起的几个周期后，后续逻辑才能使用。这时如果按照单周期路径进行时序检查，就会报出时序违规。因此就需要进行多周期路径约束



```tcl
set_multicycle_path <num_cycles> [-setup|-hold] [-start|-end][-from <startpoints>] [-to <endpoints>] [-through <pins|cells|nets>]
```

| 参数                       | 含义                    |
| :------------------------- | :---------------------- |
| num_cycles [-setup -hold]  | 建立/保持时间的周期个数 |
| [-start -end]              | 参数时钟选取            |
| -from                      | 发起点                  |
| -to                        | 捕获点                  |
| -through <pins/cells/nets> | 经过点                  |

对于建立时间，num_cycles是指多周期路径所需的时钟周期个数；对于保持时间，num_cycles是指相对于默认的捕获沿，实际捕获沿应回调的周期个数。

发起沿和捕获沿可能是同一个时钟，也可能是两个时钟，参数`start`和`end`就是选择参考时钟是发送端还是接收端。

- start表示参考时钟为发送端（发端）所用时钟，对于保持时间的分析，若后面没有指定`start`或`end`，则默认为为-start；
- end表示参考时钟为捕获端（收端）所用时钟,对于建立时间的分析，若后面没有指定`start`或`end`，则默认为为-end；

上面这两句话也不难理解，因为setup-time是在下一个时钟沿进行捕获时的约束，因此默认是对接收端的约束；而hold-up-time是对同一个时钟沿的约束，目的是发送端不能太快，是对发送端的约束。

  对于单周期路径来说，setup的num_cycles为1，hold的num_cycles为0.

举例说明：

单时钟域的多周期路径常见于带有使能的电路中，我们以双时钟周期路径为例，其实现电路如下：

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_10.png">

若我们没有指定任何的约束，默认的建立/保持时间的分析就像我们上面所讲的单周期路径，如下图所示。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_11.png">

但由于我们的的数据经过了两个时钟周期才被捕获，因此建立时间的分析时需要再延迟一个周期的时间。

采用如下的时序约束：

```tcl
set_multicycle_path 2 -setup -from [get_pins data0_reg/C] -to [get_pins data1_reg/D]
```

在建立时间被修改后，保持时间也会自动调整到捕获时钟沿的前一个时钟沿，如下图所示。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_12.png">

很明显，这个保持时间检查是不对的，因为保持时间的检查针对的是同一个时钟沿，因此我们要把保持时间往回调一个周期，需要再增加一句约束：

```tcl
set_multicycle_path 1 -hold -end -from [get_pins data0_reg/C]  -to [get_pins data1_reg/D]
```

这里加上`-end`参数是因为我们要把捕获时钟沿往前移，因此针对的是接收端，但由于我们这边讲的是单时钟域，发送端和接收端的时钟是同一个，因此`-end`可以省略。这样，完整的时序约束如下：

```
set_multicycle_path 2 -setup -from [get_pins data0_reg/C] -to [get_pins data1_reg/D]
set_multicycle_path 1 -hold  -from [get_pins data0_reg/C]  -to [get_pins data1_reg/D]
```

约束完成后，建立保持时间检查如下图所示。

<img src="/images/post_images/fpga_14_time_constraint_2/fpga_14_time_constraint_2_13.png">

#### 6、伪路径约束

伪路径指的是该路径存在，但该路径的电路功能不会发生或者无须时序约束。创建伪路径可以减少工具运行优化时间,增强实现结果,避免在不需要进行时序约束的地方花较多时间而忽略了真正需要进行优化的地方。

伪路径一般用于：

- 跨时钟域
- 一上电就被写入数据的寄存器
- 异步复位或测试逻辑
- 异步双端口RAM

伪路径约束为：

```tcl
set_false_path [-setup] [-hold] [-from <node_list>] [-to <node_list>] [-through <node_list>]
```

- `-from`的节点应是有效的起始点.有效的起始点包含时钟对象,时序单元的clock引脚,或者input(or inout)原语;
- `-to`的节点应包含有效的终结点.一个有效的终结点包含时钟对象,output(or inout)原语端口,或者时序功能单元的数据输入端口;
- `-through`的节点应包括引脚,端口,或线网.当单独使用-through时,应注意所有路径中包含-through节点的路径都将被时序分析工具所忽略.

需要注意的是，`-through`是有先后顺序的，下面的两个约束是不同的约束：

```tcl
set_false_path -through cell1/pin1 -through cell2/pin2
set_false_path -through cell2/pin2 -through cell1/pin1
```

因为它们经过的先后顺序不同，伪路径的约束是单向的，并非双向的，若两个时钟域相互之间都有数据传输，则应采用如下约束：

```tcl
set_false_path -from [get_clocks clk1] -to [get_clocks clk2]
set_false_path -from [get_clocks clk2] -to [get_clocks clk1]
```

也可以直接采用如下的方式，与上述两行约束等效：

```tcl
set_clock_groups -async -group [get_clocks clk1] -to [get_clocks clk2]
```



#### 7、约束优先级

对于同样的约束，定义的越精细，优先级越高。各对象的约束优先级从高到低为：

1. ports->pins->cells
2. clocks

  路径声明的优先级从高到低为：

1. -from -through -to
2. -from -to
3. -from -through
4. -from
5. -through -to
6. -to
7. -through

***优先考虑对象，再考虑路径\***

​                         

​                       

[  完  ]



