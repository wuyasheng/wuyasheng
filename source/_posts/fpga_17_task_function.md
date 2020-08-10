---
title: Verilog task 和 function
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理task和function内容
categories: FPGA-详解
tags:
  - Verilog
  - task
  - function
abbrlink: 677329774
date: 2020-08-10 20:40:09
layout:
password:
---

## function  和  task  总结

---

### 1、function 相关内容

#### **1.1 function 定义**

(1)函数通过关键词function 和 endfunction 定义

(2)不允许输出端口声明（包括输出和双向端口） ，但可以有多个输入端口

(3)[range]参数指定函数返回值的类型或位宽，是一个可选项，若没有指定，默认缺省值为宽度 1 bit的寄存器数据

(4)function_name为所定义函数的名称，对函数的调用也是通过函数名完成的，并在函数结构体内部代表一个 内部变量，函数调用的返回值就是通过函数名变量传递给调用语句。函数定义在函数内部会隐式定义一个寄存器变量，该寄存器变量和函数同名并且位宽也一致。函数通过在函数定义中对该寄存器的显式赋值来返回函数计算结果

(5)input_declaration为各个输入端口的位宽和类型进行说明，在函数定义中至少要有一个输入端口

```verilog
function [1:0] func(//返回值位宽可以缺省，缺省则默认为1        
    input A,    //端口声明不允许存在 output 和 inout        
    input B,    //可以存在多个输入        
    input C     //至少有一个输入信号 ); 
begin    
	func = A + B + C;   //函数名 func 除了用来实现函数调用，也同时隐式声明了与函数同名位宽一致的变量                    			//在函数内部，可以对该变量进行显示赋值，返回计算结果 
end                   
endfunction
```

#### **1.2 function 特点**

(1)函数定义只能在模块中完成，不能出现在过程块中

(2)函数至少要有一个输入端口；不能包含输出端口和双向端口

(3) 在函数结构中， 不能使用任何形式的时间控制语句 （#、 wait 等） ， 也不能使用 disable中止语句

(4)函数定义结构体中不能出现过程块语句（always语句）

(5)函数内部可以调用函数，但不能调用任务。

#### **1.3 function 调用**

(1)函数调用可以在过程块中完成，也可以在 assign 这样的连续赋值语句中出现。

(2)函数调用语句不能单独作为一条语句出现，只能作为赋值语句的右端操作数。

(3)如果task或者function在不同地方并发调用，则它们使用同一组变量个内存地址，存在冲突产生错误。

为避免错误，声明时在task和function后面加上automatic 关键字。如：task automatic task_id… endtask

```verilog
wire [2:0] A; 
wire [1:0] sum; 
assign sum = func(A[2],A[1],A[0]);
```

#### **1.4 function 总结**

function 的角色就相当于，定义声明一个可用来调用的组合逻辑函数

注：

（1）函数调用可以在过程块中完成，也可以在 assign 这样的连续赋值语句中出现。 

（2）函数调用语句不能单独作为一条语句出现，只能作为赋值语句的右端操作数。

​                   

### 2、function相关内容

#### **2.1 task 定义**

（1）在第一行task语句中不能列出端口名称； 

（2）任务的输入、输出端口和双向端口数量不受限制，甚至可以没有输入、输出以及 双向端口。

（3）在任务定义的描述语句中，可以使用出现不可综合操作符合语句（使用最为频繁 的就是延迟控制语句） ，但这样会造成该任务不可综合。

（4）在任务中可以调用其他的任务或函数，也可以调用自身。 

（5）在任务定义结构内不能出现initial和 always过程块。

（6）在任务定义中可以出现“disable 中止语句” ，将中断正在执行的任务，但其是不

可综合的。当任务被中断后，程序流程将返回到调用任务的地方继续向下执行。

```verilog
task ADD;   
    input A;  
    input B;  
    input CIN;  
    output[1:0] SUM;  
    reg S;  
    reg COUT;  
    begin   
        S = A^B^CIN;   
        COUT = (A&B)|(A&CIN)|(B&CIN);   
        SUM  = {COUT,S}; 
    end 
endtask
```



#### **2.2 task 调用**

任务的调用： 任务中不能出现initial 语句和always 语句语句， 

但任务调用语句可以在initial语句和 always语句中使用。 

任务调用语句中，参数列表的顺序必须与任务定义中的端口声明顺序相同。

任务调用语句是过程性语句，所以任务调用中接收返回数据的变量必须是寄存器类型。

```verilog
always@(*)begin                     //任务调用语句只能出现在过程块内
    ADD(A[0],B[0],CIN,S0);   
    ADD(A[1],B[1],S0[1],S1);   
    ADD(A[2],B[2],S1[1],S2);   
    ADD(A[3],B[3],S2[1],S3); 
end
```



### **3、小结**

function 与 task本质上都是用来生成组合逻辑，task的功能更多，没有太多的限制，在需要产生多个相同模块时，可以由他们进行生成

​                              

​                         

[  完  ]