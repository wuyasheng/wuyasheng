---
title: Verilog Demo_17 CRC校验
author: yasheng
img: /medias/featureimages/verilog.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理CRC冗余校验相关内容
categories: FPGA-例程
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

注 : 本文整理摘抄CRC冗余校验相关内容， [参考文1](https://www.cnblogs.com/moluoqishi/p/7731617.html)、 [参考文2](http://www.elecfans.com/d/892471.html)、 [参考文3](https://blog.csdn.net/slimmm/article/details/88576296)、 [参考文4](https://www.cnblogs.com/BitArt/archive/2012/12/26/2833100.html)、 [参考文5](https://www.cnblogs.com/esestt/archive/2007/08/09/848856.html)

### CRC冗余校验

CRC即循环冗余校验码：是数据通信领域中最常用的一种查错校验码，其特征是信息字段和校验字段的长度可以任意选定。循环冗余检查（CRC）是一种数据传输检错功能，对数据进行多项式计算，并将得到的结果附在帧的后面，接收设备也执行类似的算法，以保证数据传输的正确性和完整性。

可以使用 LFSR 计算 CRC ，如下图所示

<img src="/images/post_images/verilog_demo_17_crc_check/crc_01.png">

<img src="/images/post_images/verilog_demo_17_crc_check/crc_02.png">



### CRC校验基本原理

​    假设数据传输过程中需要发送15位的二进制信息 g=101001110100001，这串二进制码可表示为代数多项式g(x) = x^14 + x^12 + x^9 + x^8 + x^7 + x^5 + 1，其中g中第k位的值，对应g(x)中x^k的系数。将g(x)乘以x^m，即在g后加m个0，然后除以m阶多项式h(x)，得到的(m-1)阶余项r(x)对应的二进制码r就是CRC编码。

h(x)可以自由选择或者使用国际通行标准，一般按照h(x)的阶数m，将CRC算法称为CRC-m，比如CRC-32、CRC-64等。国际通行标准可以参看http://en.wikipedia.org/wiki/Cyclic_redundancy_check

要校验的数据加上此数据计算出来的crc组成新的数据帧，如下图所示

<img src="/images/post_images/verilog_demo_17_crc_check/crc_03.png">

g(x)和h(x)的除运算，可以通过g和h做xor（异或）运算。比如将11001与10101做xor运算：

<img src="/images/post_images/verilog_demo_17_crc_check/crc_04.png">

明白了xor运算法则后，举一个例子使用CRC-8算法求101001110100001的效验码。CRC-8标准的h(x) = x^8 + x^7 + x^6 + x^4 + x^2 + 1，既h是9位的二进制串111010101。

<img src="/images/post_images/verilog_demo_17_crc_check/crc_05.png">

经过迭代运算后，最终得到的r是10001100，这就是CRC效验码。

为了优化CRC编码，通常会采取以下参数模型，用于生成CRC编码



### 常见CRC参数模型

crc参数的初值不同得到的验证编码也不同，通常有下列参数模型，包括初值、输入值翻转、输出值翻转等

<img src="/images/post_images/verilog_demo_17_crc_check/crc_06.png">



### 并行CRC编码举例

为了优化CRC编码速度，可以使用并行的方法，改进LFSR，通过依次分析每一个clk，对应编码的变化，可以生成如下并行编码结构；

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



### 并行CRC代码模板

https://www.easics.com/webtools/crctool

http://outputlogic.com/?page_id=321

​                        

​                              

​                       

[  完  ]