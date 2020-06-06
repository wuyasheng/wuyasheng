---
title: Verilog 设计方法-计数器
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理计数器架构设计方法
categories: ☸ FPGA
tags:
  - Verilog
  - 计数器
abbrlink: 2990932631
date: 2020-03-27 09:27:09
password:
---

## Verilog 设计方法-计数器

---

注 : 本文内容来自整理摘抄至《手把手教你学FPGA设计-基于大道至简的至简设计法》

### **计数器思想设计方法**

计数器规则

1. 计数器逐一考虑三要素 -- 初值、加一条件、结束值
2. 计数器初值必须为 0 
3. 使用某一计数值时必须满足加一条件
4. 结束条件必须同时满足加一条件
5. 当取某一值时，assign 形式必须为：（加1条件）&&（cnt == x - 1）
6. 结束后计数值必须回到 0 
7. 若需要限定范围，推荐使用  “ >= ” 和 “ < ” 两种符号 
8. 设计步骤是，先写计数器的always段，条件用名字代替，然后用 assign 写出加一条件，最后用assign 写出结束条件。
9. 加一条件必须与计数器严格对齐，其他信号一律向计数器对齐
10. 命名必须符合规范，比如 add_cnt 表示加一条件, end_cnt 表示结束条件
11. 减一计数器一般不使用

第一段代码

```verilog
reg [20:0] cnt; 
wire add_cnt,end_cnt; 
always@(posedge clk or negedge rst_n)begin    
    if(!rst_n)        
        cnt <= 0;    
    else if(add_cnt)begin        
        if(end_cnt)            
            cnt <= 0;        
        else           
        	cnt <= cnt + 1'b1;    
    end    
end
```

第二段代码

```verilog
assign add_cnt = 1'b1;		//加一条件
```

第三段代码

```verilog
assign end_cnt = add_cnt && cnt = 10 - 1;	//结束条件
```

设计步骤（八步）

1. 明确功能
2. 确定功能波形
3. 确定计数器的结构
4. 确定加一条件和结束条件
5. 定义设计中会使用到的特殊点
6. 检查信号的完整性
7. 实现计数器代码（三段）
8. 实现其他的功能代码



**注意：使用计数器可以实现线性序列机，用以产生特定的不规则时序，比状态机更加的灵活**



### 一级计数器模板

```verilog
reg [20:0] cnt0; 
wire add_cnt0,end_cnt0; 

always @(posedge clk or negedge rst_n)begin
    if(!rst_n)begin
        cnt0 <= 0;
    end
    else if(add_cnt0)begin
        if(end_cnt0)
            cnt0 <= 0;
        else
            cnt0 <= cnt0 + 1;
    end
end
assign add_cnt0 = ;
assign end_cnt0 = add_cnt0 && cnt0== ;
```

### 二级计数器模板

```verilog
reg [20:0] cnt0,cnt1; 
wire add_cnt0,end_cnt0; 
wire add_cnt1,end_cnt1; 
always @(posedge clk or negedge rst_n)begin
    if(!rst_n)begin
        cnt0 <= 0;
    end
    else if(add_cnt0)begin
        if(end_cnt0)
            cnt0 <= 0;
        else
            cnt0 <= cnt0 + 1;
    end
end
assign add_cnt0 = ;
assign end_cnt0 = add_cnt0 && cnt0== ;

always @(posedge clk or negedge rst_n)begin 
    if(!rst_n)begin
        cnt1 <= 0;
    end
    else if(add_cnt1)begin
        if(end_cnt1)
            cnt1 <= 0;
        else
            cnt1 <= cnt1 + 1;
    end
end
assign add_cnt1 = end_cnt0;
assign end_cnt1 = add_cnt1 && cnt1== ;
```

### 三级计数器模板

```verilog
reg [20:0] cnt0,cnt1,cnt2; 
wire add_cnt0,end_cnt0; 
wire add_cnt1,end_cnt1; 
wire add_cnt2,end_cnt2; 
always @(posedge clk or negedge rst_n)begin
    if(!rst_n)begin
        cnt0 <= 0;
    end
    else if(add_cnt0)begin
        if(end_cnt0)
            cnt0 <= 0;
        else
            cnt0 <= cnt0 + 1;
    end
end
assign add_cnt0 = ;
assign end_cnt0 = add_cnt0 && cnt0== ;

always @(posedge clk or negedge rst_n)begin 
    if(!rst_n)begin
        cnt1 <= 0;
    end
    else if(add_cnt1)begin
        if(end_cnt1)
            cnt1 <= 0;
        else
            cnt1 <= cnt1 + 1;
    end
end
assign add_cnt1 = end_cnt0;
assign end_cnt1 = add_cnt1 && cnt1== ;

always @(posedge clk or negedge rst_n)begin
    if(!rst_n)begin
        cnt2 <= 0;
    end
    else if(add_cnt2)begin
        if(end_cnt2)
            cnt2 <= 0;
        else
            cnt2 <= cnt2 + 1;
    end
end
assign add_cnt2 = end_cnt1;
assign end_cnt2 = add_cnt2 && cnt2== ;
```



[  完  ]



