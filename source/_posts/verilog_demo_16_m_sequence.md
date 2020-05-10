---
title: Verilog Demo_16 M序列
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: true
layout: 
summary: 本文整理M序列发生器相关内容
categories: ☸ FPGA
tags:
  - Verilog
  - M序列
  - 伪随机序列
abbrlink: 2051595482
date: 2020-03-30 08:07:09
password:
---

## M序列发生器

---

注 : 本文整理摘抄复制 M序列发生器相关内容， [M序列参考文章](https://mp.weixin.qq.com/s?__biz=MzA4MzA2ODExOQ==&mid=2457256018&idx=3&sn=8678385878ddf422c1c03fa064393a52&chksm=887e75eabf09fcfc5a484efb0cb854c99120bfaffcb9c4a07c96fa4968b4af8a4976e41b3dd5&scene=21#wechat_redirect)、[参考文2](https://blog.csdn.net/u013056038/article/details/46941947)

### **M序列介绍**

m序列是目前广泛应用的一种伪随机序列，其在通信领域有着广泛的应用，如扩频通信，卫星通信的码分多址，数字数据中的加密、加扰、同步、误码率测量等领域。m序列在所有的伪随机序列里面的地位是最基础同样也是最重要的。它的特点就是产生方便快捷，有很强的规律特性，同时自相关性、互相关特性也很好。简单来说，n级反馈移位寄存器的状态最多由2^n 个，同时，在线性的反馈移位寄存器中，全“0”状态永不改变，所以也就是说，它的最长的周期就是 2^n - 1。

### **M序列模型**

n级线性移位寄存器结构如下

<img src="/images/post_images/verilog_demo_16_M_sequence/M_sequence_01.png">

### **M序列性质**

周期为2^n-1的m序列由以下几个特点：

1. 随机性

   在m序列的一个周期中，0和1出现概率大致相同，0码只比1码多一个，且1的个数为，0的个数为。

2. 移位可加性

   某个周期为p的m序列与其经任意延迟移位后的序列模2相加后，其结果仍是周期为p的m序列，只是原序列某次延迟移位后的序列。

3. 预先可确定性

   m序列是由移位寄存器的初始状态和反馈网络唯一确定的。

4. 游程特性

   序列中取值相同的相继元素称为一个游程。游程长度指的是游程中元素的个数。在m序列中，一共有个游程。其中长度为1的游程占总游程数的一半；长度为2的游程占总游程的1/4；长度为k的游程占总游程数的，且在长度为k的游程中，连0与连1的游程数各占一半。另外，还有一个长度为n的1游程和一个长度为(n-1)的0游程。

### **特征方程**

$$
f(x)=c_0x^n+c_1x^{n-1}+c_2x^{n-2}+c_3x^{n-3}+...+c_{n-1}x+c_n=\displaystyle \sum^{i \to n}_{i \to 0}{c_ix^{n-i}}
$$
说明：①ci的值决定了反馈线的连接状态；

②在上式和后面的公式中都将“”简写为“+”；

③式中xi本身并无实际意义，它仅指明其系数是ci的值。

④特征方程f(x)决定了一个线性反馈移存器的结构，从而决定了它产生的序列的构造和周期。

### 本原多项式

使一个线性反馈移存器产生最长周期序列的充分必要条件是其特征方程f(x)为本原多项式。

本原多项式是指满足下列条件的多项式：

①是既约的，即不能分解因子的；

② 可以整除(x^m + 1)，m = 2^n – 1；即是( x^m + 1 )的一个因子；

③ 除不尽( x^q+ 1 )，q < m。

常用本原多项式

<img src="/images/post_images/verilog_demo_16_M_sequence/M_sequence_02.png">

### 7阶M序列发生器

<img src="/images/post_images/verilog_demo_16_M_sequence/M_sequence_03.png">

注：⊕ 代表异或

说明：r0异或r4赋值给r6，其它值按位移就可以。

```verilog
module ms7_generate(
   input clk, 
   input rst_n,
   input en,
   output ms7,
   output reg [6:0] r7
);
   parameter INIT = 7'b000_0001;
   assign ms7 = r7[0];
   always @ (posedge clk or negedge rst_n)begin
       if(!rst_n)
           r7 <= INIT;
       else if(en)begin
           r7[0] <= r7[1];
           r7[1] <= r7[2];
           r7[2] <= r7[3];
           r7[3] <= r7[4];
           r7[4] <= r7[5];
           r7[5] <= r7[6];
           r7[6] <= r7[0] ^ r7[4];
       end
       else
           r7 <= r7;
   end
endmodule
```

### 8阶M序列发生器

<img src="/images/post_images/verilog_demo_16_M_sequence/M_sequence_04.png">

说明：r0异或r4异或r5异或r6赋值给r7，其它值按位移就可以。

```verilog
module ms8_generate(
   input clk, 
   input rst_n,
   input en,
   output ms8,
   output reg [7:0] r8
);
   parameter INIT = 8'b0000_0001;
   assign ms8 = r8[0];
   always @ (posedge clk or negedge rst_n)begin
       if(!rst_n)
           r8 <= INIT;
       else if(en) begin
           r8[0] <= r8[1];
           r8[1] <= r8[2];
           r8[2] <= r8[3];
           r8[3] <= r8[4];
           r8[4] <= r8[5];
           r8[5] <= r8[6];
           r8[6] <= r8[7];
           r8[7] <= r8[0] ^ r8[4] ^ r8[5] ^ r8[6];
       end
       else
           r8 <= r8;
   end
endmodule
```

​          

​          

[  完  ]