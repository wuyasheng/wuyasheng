---
title: Verilog Demo_17 CRC校验
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
summary: 本文整理CRC冗余校验相关内容
categories: ☸ FPGA
tags:
  - Verilog
  - CRC
  - 冗余校验
abbrlink: 497666070
date: 2020-03-30 09:07:09
password:
---

## CRC冗余校验

---

注 : 本文整理摘抄CRC冗余校验相关内容， [参考文1](https://blog.csdn.net/qq_40532956/article/details/80113408)、 [参考文2](https://blog.csdn.net/li200503028/article/details/26591243?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2)、 [参考文3](https://blog.csdn.net/u011388550/article/details/45242725)、 [参考文4](https://www.cnblogs.com/kingstacker/p/9848191.html)、 [参考文5](https://www.cnblogs.com/pengwangguoyh/articles/4466269.html)

### CRC冗余校验

CRC即循环冗余校验码：是数据通信领域中最常用的一种查错校验码，其特征是信息字段和校验字段的长度可以任意选定。循环冗余检查（CRC）是一种数据传输检错功能，对数据进行多项式计算，并将得到的结果附在帧的后面，接收设备也执行类似的算法，以保证数据传输的正确性和完整性。

LFSR计算CRC，可以用多项式G（x）表示，G(x) = X16+X12+X5+1模型可如下图所示。

<img src="/images/post_images/verilog_demo_17_crc_check/crc_01.png">

### CRC校验基本原理

​    将被处理的报文比特序列当做一个二进制多项式A(x)的系数，（任意一个由二进制位串组成的代码都可以和一个系数仅为‘0’和‘1’取值的多项式一一对应。例如：代码1010111对应的多项式为x6+x4+x2+x+1，而多项式为x5+x3+x2+x+1对应的代码101111），该系数乘以2^n（n为生成多项式g(x)中x的最高次幂）以后再除以发送方和接收方事先约定好的生成多项式g(x)后，求得的余数P(x)就是CRC校验码，把它副到原始的报文A(x)后面形成新的报文即为A(x)*x^n+P(x)，并且发送到接收端，接收端从整个报文中提取出报文B(x)（即为发送端的A(x)，此时不能保证发送正确所以用B(x)表示），然后用与接收端同样的做法将B(x)对应的二进制序列乘以2^n（左移n位）后，除以事先约定好的g(x)得到一个余数p(x)，此时如果接收报文中的CRC校验码与计算得到的校验码相同，即P(x)=p(x)，则传输正确，否则传输有误，重新传输。

要校验的数据加上此数据计算出来的crc组成新的数据帧，如下图所示

<img src="/images/post_images/verilog_demo_17_crc_check/crc_02.png">

上述工作过程中有几点需要注意：

1. 在进行CRC计算时，采用二进制(模2)运算法，即加法不进位，减法不借位，其本质就是两个操作数进行逻辑异或运算；
2. 在进行CRC计算前先将发送报文所表示的多项式A(x)乘以x“，其中n为生成多项式p(x)的最高幂值。对二进制乘法来讲，A(x)·x“就是将A(x)左移n       位，用来存放余数p(x)，所以实际发送的报文就变为A(x)·x^n+p(x)：
3. 生成多项式g(x)的首位和最后一位的系数必须为1，且生成多项式根据不同国家的标准有不同的形式。

### CRC校验举例

下面举例说明CRC校验码的求法：（此例子摘自百度百科：CRC校验码）

信息字段代码为: 1011001；对应m(x)=x6+x4+x3+1

假设生成多项式为：g(x)=x4+x3+1；则对应g(x)的代码为: 11001

x4m(x)=x10+x8+x7+x4 对应的代码记为：10110010000；

采用多项式除法: 得余数为: 1010 (即CRC校验字段为：1010）

发送方：发出的传输字段为: 1 0 1 1 0 0 1 1010

给出余数（1010）的计算步骤：

除法没有数学上的含义，而是采用计算机的模二除法，即，除数和被除数做异或运算。进行异或运算时除数和被除数最高位对齐，按位异或。

```
 10110010000
^11001
--------------------------
 01111010000
```

```
 1111010000
^11001
-------------------------
 0011110000
```

```
 11110000
^11001
--------------------------
 00111000
```

```
111000
^11001
-------------------
001010
```

则四位CRC校验码就为：1010。

利用CRC进行检错的过程可简单描述为：在发送端根据要传送的k位二进制码序列，以一定的规则产生一个校验用的r位监督码(CRC码)，附在原始信息后边，构成一个新的二进制码序列数共k+r位，然后发送出去。在接收端，根据信息码和CRC码之间所遵循的规则进行检验，以确定传送中是否出错。这个规则，在差错控制理论中称为“生成多项式”。

### CRC举例代码

```verilog
//-----------------------------------------------------------------------------
// CRC module for data[31:0] ,   crc[7:0]=1+x^4+x^6+x^8;
//-----------------------------------------------------------------------------
module crc(
  input [31:0] data_in,
  input crc_en,
  output [7:0] crc_out,
  input rst,
  input clk);

  reg [7:0] lfsr_q,lfsr_c;

  assign crc_out = lfsr_q;

  always @(*) begin
    lfsr_c[0] = lfsr_q[4] ^ lfsr_q[6] ^ data_in[0] ^ data_in[2] ^ data_in[6] ^ data_in[14] ^ data_in[16] ^ data_in[20] ^ data_in[28] ^ data_in[30];
    lfsr_c[1] = lfsr_q[5] ^ lfsr_q[7] ^ data_in[1] ^ data_in[3] ^ data_in[7] ^ data_in[15] ^ data_in[17] ^ data_in[21] ^ data_in[29] ^ data_in[31];
    lfsr_c[2] = lfsr_q[6] ^ data_in[2] ^ data_in[4] ^ data_in[8] ^ data_in[16] ^ data_in[18] ^ data_in[22] ^ data_in[30];
    lfsr_c[3] = lfsr_q[7] ^ data_in[3] ^ data_in[5] ^ data_in[9] ^ data_in[17] ^ data_in[19] ^ data_in[23] ^ data_in[31];
    lfsr_c[4] = lfsr_q[0] ^ lfsr_q[4] ^ lfsr_q[6] ^ data_in[0] ^ data_in[2] ^ data_in[4] ^ data_in[10] ^ data_in[14] ^ data_in[16] ^ data_in[18] ^ data_in[24] ^ data_in[28] ^ data_in[30];
    lfsr_c[5] = lfsr_q[1] ^ lfsr_q[5] ^ lfsr_q[7] ^ data_in[1] ^ data_in[3] ^ data_in[5] ^ data_in[11] ^ data_in[15] ^ data_in[17] ^ data_in[19] ^ data_in[25] ^ data_in[29] ^ data_in[31];
    lfsr_c[6] = lfsr_q[2] ^ lfsr_q[4] ^ data_in[0] ^ data_in[4] ^ data_in[12] ^ data_in[14] ^ data_in[18] ^ data_in[26] ^ data_in[28];
    lfsr_c[7] = lfsr_q[3] ^ lfsr_q[5] ^ data_in[1] ^ data_in[5] ^ data_in[13] ^ data_in[15] ^ data_in[19] ^ data_in[27] ^ data_in[29];

  end // always

  always @(posedge clk, posedge rst) begin
    if(rst) begin
      lfsr_q <= {8{1'b1}};
    end
    else begin
      lfsr_q <= crc_en ? lfsr_c : lfsr_q;
    end
  end // always
endmodule // crc
```



crc循环冗余硬件代码模板网站

https://www.easics.com/webtools/crctool

http://outputlogic.com/?page_id=321

​       

​       

[  完  ]