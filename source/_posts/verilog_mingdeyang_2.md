---
title: Verilog 设计方法-状态机
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理状态机架构设计方法
categories: ☸ FPGA
tags:
  - Verilog
  - 状态机
abbrlink: 3735404488
date: 2020-03-27 11:27:09
password:
---

## Verilog 设计方法-状态机

---

注 : 本文内容来自整理摘抄至《手把手教你学FPGA设计-基于大道至简的至简设计法》

### **状态机思想设计方法**

设计规则

1. 使用四段式写法（不是指四个 always代码,而是四段代码）；注意：四段式状态机并非固定不变。如果没有输出信号就只有三段代码(两个 always);如果有多个输出信号,那么就会有多个 always代码块。
2. 四段式状态机第一段写法不变
3. 第二段的状态转移条件用信号名表示
4. 用assign的形式将状态转移条件写成 xx2xx_start 的形式
5. 状态转移条件，用assign 产生变化条件时，必须加上现态
6. 状态不发生变化时，使用 state_c = state_c

第一段代码

```verilog
always@(posedge clk or negedge rst_n)begin    
    if(!rst_n)        
        state_c <= IDLE;    
    else        
        state_c <= state_n; 
end
```

第二段代码

```verilog
always@(*)begin    
    case(state_c)        
        IDLE:begin            
            if(idle2s1_start)                
                state_n = S1;            
            else                
                state_n = state_c;        
        end        
        S1:begin            
            if(s12s2_start)                
                state_n = S2;            
            else                
                state_n = state_c;        
        end        
        S2:begin            
            if(s22idle_start)                
                state_n = idle;            
            else                
                state_n = state_c;        
        end        
        default:  state_n = IDLE;     
    endcase 
end
```

第三段代码（用assign写出转移条件）

```verilog
assign = idle2s1_start = state_c == IDLE   && 条件1 ; 
assign = s12s2_start   = state_c == S1 	   && 条件2 ; 
assign = s22idle_start = state_c == S2     && 条件3 ;
```

第四段代码（设计信号输出）

```verilog
always@(posedge clk or negedge rst_n)begin    
    if(!rst_n)        
        out1 <= 1'b0;    
    else if(state_c == S1)        
        out1 <= 1'b1;    
    else        
        out1 <= 1'b0; 
end
```

设计步骤（八步）

1. 明确功能（仔细描述功能）
2. 输出分析
3. 状态合并（状态划分和状态合并）
4. 状态转移
5. 转移条件
6. 完整性检查（检查状态机的完整性及其他信号完整性）
7. 状态机代码
8. 功能代码（补充其他信号的代码）

### 四段式状态机完整代码

```verilog
//四段式状态机
//状态存储器
reg				[	:0]	state_c;
reg				[	:0]	state_c;
//状态转移变量
wire			idl2s1_start；
wire			s12s2_start
wire			s22s3_start
//状态定义
localparam			IDLE		=	1		;
localparam			S1		=	2		;
localparam			S2		=	3		;
localparam			S3		=	4		;

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
        IDLE:begin
            if(idl2s1_start)begin
                state_n = S1;
            end
            else begin
                state_n = state_c;
            end
        end
        S1:begin
            if(s12s2_start)begin
                state_n = S2;
            end
            else begin
                state_n = state_c;
            end
        end
        S2:begin
            if(s22s3_start)begin
                state_n = S3;
            end
            else begin
                state_n = state_c;
            end
        end
        default:begin
            state_n = IDLE;
        end
    endcase
end

//第三段：设计转移条件
assign idl2s1_start  = state_c ==IDLE && 	;
assign s12s2_start   = state_c ==S1   && 	;
assign s22s3_start   = state_c ==S2   && 	;

//第四段：同步时序always模块，格式化描述寄存器输出（可有多个输出）
always  @(posedge clk or negedge rst_n)begin
    if(!rst_n)begin     
        //初始化
    end
    else if(state_c==S1)begin
        //out1 <= 1'b1;
    end
    else begin
        //out1 <= 1'b0;
    end
end
```



### 状态机描述方法

状态机描述时关键是要描述清楚几个状态机的要素，即如何进行状态转移，每个状态的输出是什么，状态转移的条件等。具体描述时方法各种各样，最常见的有三种描述方式：

（1）一段式：整个状态机写到一个always模块里面，在该模块中既描述状态转移，又描述状态的输入和输出；

（2）二段式：用两个always模块来描述状态机，其中一个always模块采用同步时序描述状态转移；另一个模块采用组合逻辑判断状态转移条件，描述状态转移规律以及输出；

（3）三段式：在两个always模块描述方法基础上，使用三个always模块，一个always模块采用同步时序描述状态转移，一个always采用组合逻辑判断状态转移条件，描述状态转移规律，另一个always模块描述状态输出(可以用组合[电路](http://bbs.elecfans.com/zhuti_dianlu_1.html)输出，也可以时序电路输出)。

 

**第四段状态机可以使用时序逻辑也可以使用组合逻辑，看需求**

状态机陷阱参考

https://www.cnblogs.com/lifan3a/articles/4583577.html



[  完  ]