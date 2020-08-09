---
title: 有限状态机总结
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理有限状态机的比较
categories: FPGA-总结
tags:
  - Verilog
  - 状态机
abbrlink: 3916580815
date: 2020-05-16 16:27:09
layout:
password:
---

## 状态机总结与感悟

---

注 : 本文内容来自整理摘抄至[参考文章1](https://www.cnblogs.com/zzjjhh250/archive/2010/12/22/1914129.html)、[参考文章2](https://www.cnblogs.com/ninghechuan/p/7898297.html)、[参考文章3](https://www.cnblogs.com/zxl2431/archive/2010/10/07/1845154.html)

### 状态机介绍

有限状态机主要分为Mealy和moore型状态机

**Moore型状态机**：下一状态只由当前状态决定，即次态=f(现状，输入)，输出=f（现状）；
**Mealy型状态机**：下一状态不但与当前状态有关，还与当前输入值有关，即次态=f(现状，输入)，输出=f（现状，输入）；

#### moore状态机状态图画法

<img src="/images/post_images/verilog_fsm_moore_mealy/fsm_01.png">

#### mealy状态机状态图画法

<img src="/images/post_images/verilog_fsm_moore_mealy/fsm_02.png">

### moore状态机

Moore型状态机输出仅仅与现态有关，而Mealy型状态机不仅与现态有关，也与输入有关，所以会受到输入的干扰，可能会产生毛刺（Glith）的现象，所以我们通常使用的是Moore型状态机。

状态机的编码，**二进制编码（Binary）**，**格雷码编码（Gray-code）**，**独热码（One-hot）**。不同的编码方式是防止在状态转移中发生突变，使得状态转移更为稳定，系统更加可靠，但是通常情况下我们直接采用的是二进制进行编码，除非系统对稳定性和状态编码有特殊要求。

#### moore状态机选择

<img src="/images/post_images/verilog_fsm_moore_mealy/fsm_03.png">

#### 一段式状态机

将组合逻辑和时序逻辑混合在一起，这样的写法对于逻辑简单的状态机来说还是可以使用的，但是对于复杂的逻辑就不推荐了，如果状态复杂也会容易出错，而且一个always块中信号太多也不利于维护和修改。

```verilog
//状态参数声明
parameter     S0    =    4'b0000,
            S1    =    4'b0001,
            s2    =    4'b0010;
//FSM one segment
reg     [3:0]    state;
always @(posedge clk or negedge rst_n)begin
    if(!rst_n)
        state <= S0;
    else begin
        case(state)
        S0:
        S1:
        S2:
        .
        .
        .
        default:
        endcase 
    end
end
```

#### 二段式状态机

两段式状态机也是一种常用的写法，它把组合逻辑和时序逻辑区分出来，第一段负责状态的转移，第二段是组合逻辑赋值，但是这种写法的缺点是，组合逻辑较容易产生毛刺等常见问题，关于组合逻辑较容易产生毛刺原因，下文会提到。

```verilog
//状态参数声明
parameter     S0    =    4'b0000,
            S1    =    4'b0001,
            s2    =    4'b0010;
//FSM two segment
reg     [3:0]    pre_state;
reg     [3:0]    next_state;
//--------------------------------------
//FSM one
always @(posedge clk or negedge rst_n)begin
    if(!rst_n)
        pre_state <= S0;
    else 
        pre_state <= next_state;
end
//FSM two
always    @(*)begin
    case(pre_state)
    S0:
    S1:
    S2:
    .
    default:;
    endcase

end
```

#### 三段式状态机

三段式状态机就可以较好的解决一段二段的不足，我也是比较推荐的写法，第一段采用时序逻辑负责状态转移，第二段组合逻辑负责数据赋值，第三段时序逻辑负责输出，代码层次清晰，容易维护，时序逻辑的输出解决了两段式写法中组合逻辑的毛刺问题。但是资源消耗会多一些，此外，三段式从输入到输出会比一段式和二段式延迟一个时钟周期。在书写状态机的时候，一定要事先设计好状态转移图，将所有的状态都考虑到，避免状态进入死循环，或者跳到偏离态。

```verilog
//状态参数声明
parameter     S0    =    4'b0000,
            S1    =    4'b0001,
            s2    =    4'b0010;
//FSM three segment
//--------------------------------------
//FSM one
always @(posedge clk or negedge rst_n)begin
    if(!rst_n)
        pre_state <= S0;
    else 
        pre_state <= next_state;
end
//FSM two
always    @(*)begin
    case(pre_state)
    S0:
    S1:
    S2:
    .
    default:;
    endcase
end
//FSM three
always    @(posedge clk or negedge rst_n)begin
    if(!rst_n)
        dout <= 'b0;
    else begin
        case(pre_state)
        S0:    
        S1:
        S2:
        .
        default:;
        endcase
    end
end
```

#### moore状态机注意

一般来说，如果是模块的输出，我会用寄存器打一拍，保证模块所有输出都由寄存器驱动（因为无法预测负载要经过多少级LUT才能到达下一级寄存器）。

这样一来有个坏处：某个状态的输出信号要到下一个时钟才有效，而不是和state一起变化。在某些设计中这个限制会导致一些问题。因此如果是本模块自己使用的信号，我就不用寄存器打一拍了，只要布局布线后能满足Period约束就可以。 

**注意**

<font color = "red">状态机一般会分为3段，最后一段用时序逻辑做输出，但是都是 NEXT_STATE 作为敏感信号，这样可以解决慢一拍的问题。</font>

### mealy状态机

mealy状态机与moore状态机的区别在于，输出是否除了与本状态有关，还与当前的输入有关。在3段式状态机中，mealy状态机的前两级应该与moore状态机的前两级相同，区别在于最后一级。在mealy的最后一集，敏感信号一定需要是当前状态，再通过输入值，决定输出，信号不会有延迟。

而使用moore状态机的话，最后一级若使用CURRENT_STATE作为敏感信号，则输出会慢一拍，可以通过将敏感信号修改为NEXT_STATE，解决信号慢一拍的问题。

​             

​                

[  完  ]