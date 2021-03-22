---
title: 有符号数与无符号数运算
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理数值运算内容
categories: FPGA-详解
tags:
  - FPGA
  - 有符号数
abbrlink: 2839697031
date: 2020-08-08 21:10:09
layout:
password:
---

## 有符号数与无符号数运算

---

注 : 本文内容摘抄引用至[原文章](https://blog.csdn.net/u014485485/article/details/79962316)，侵删

### 1、无符号数

#### 1.1 高位溢出

```verilog
wire [3:0] a = 4'b1111;//15 
wire [3:0] b = 4'b0010;//2 
wire [3:0] c; 
assign c = a + b;//17=10001
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_01.png">

```verilog
wire [3:0] a = 4'b1111; 
wire [3:0] b = 4'b0010; 
wire [2:0] c; 
assign c = a + b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_02.png">

**注：无符号数，结果溢出，高位截断，保留低位**

#### 1.2 高位溢出赋给位宽足够的数

```verilog
wire [3:0] a = 4'b1111; 
wire [3:0] b = 4'b0010; 
wire [4:0] c; 
assign c = a + b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_03.png">

```verilog
wire [3:0] a = 4'b1111; 
wire [3:0] b = 4'b0010; 
wire [5:0] c; 
assign c = a + b; 
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_04.png">

结果正确。

#### 1.3 对中间结果移位

```verilog
wire [3:0] a = 4'b1111; 
wire [3:0] b = 4'b0010; 
wire [3:0] c; 
assign c = (( a + b) >> 1); //17=10001
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_05.png">

**注：可以看出是先赋值再移位的。**

```verilog
wire [3:0] a = 4'b1111; 
wire [3:0] b = 4'b0010; 
wire [4:0] c; 
assign c = (( a + b) >> 1); //17=10001
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_06.png">

结果正确，左边补0。

```verilog
wire  [3:0] a = 4'b1111; 
wire  [3:0] b = 4'b0010; 
wire  [4:0] c; 
assign c = ((a + b)>>>1) ; 
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_07.png">

**注：>>> 算数右移，无符号数还是补0，有符号数补高位**

### 2、有符号数

#### 2.1 正常运算

```verilog
wire signed [3:0] a = 4'b1111;//-1 
wire signed [3:0] b = 4'b0010;//2 
wire signed [3:0] c; 
assign c = a + b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_08.png">

```verilog
wire signed [3:0] a = 4'b1110;//-2 
wire signed [3:0] b = 4'b0001;//1 
wire signed [3:0] c; 
assign c = a + b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_09.png">

#### 2.2 赋给位宽不够的数

```verilog
wire signed [3:0] a = 4'b0111;//7 
wire signed [3:0] b = 4'b0010;//2 
wire signed [3:0] c; 
assign c = a + b; //9=1001
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_10.png">

```verilog
wire signed [3:0] a = 4'b1001;//-7 
wire signed [3:0] b = 4'b1110;//-2 
wire signed [3:0] c; 
assign c = a + b;//-9=10111
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_11.png">

采用补码进行计算、直接舍弃高位。

#### 2.3 赋给位宽足够的数

```verilog
wire signed [3:0] a = 4'b0111; 
wire signed [3:0] b = 4'b0010; 
wire signed [4:0] c; 
assign c = a + b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_12.png">

```verilog
wire signed [3:0] a = 4'b1001;//-7 
wire signed [3:0] b = 4'b1110;//-2 
wire signed [4:0] c; 
assign c = a + b;//-9=10111
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_13.png">

结果正确。

#### 2.4 给中间结果移位

```verilog
wire signed [3:0] a = 4'b1001;//-7 
wire signed [3:0] b = 4'b1110;//-2 
wire signed [3:0] c; 
assign c = (( a + b ) >> 1); //-9=10111
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_14.png">

```verilog
wire signed [3:0] a = 4'b1001;//-7 
wire signed [3:0] b = 4'b1110;//-2 
wire signed [5:0] c; 
assign c = ((a + b)>>1) ;//逻辑右移
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_15.png">

```verilog
wire signed [3:0] a = 4'b0111;//7 
wire signed [3:0] b = 4'b0010;//2 
wire signed [3:0] c; 
assign c = (( a + b ) >> 1);//9=01001
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_16.png">

**注：>>逻辑右移，高位都是都是补0**

```verilog
wire signed [3:0] a = 4'b1001;//-7 
wire signed [3:0] b = 4'b1110;//-2 
wire signed [3:0] c; 
assign c = ((a + b)>>>1) ; //-9=10111
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_17.png">

**注：>>> 算数右移。截断赋值然后把最高位当符号位，补最高位。**

```verilog
wire signed [3:0] a = 4'b1001; 
wire signed [3:0] b = 4'b1110; 
wire signed [4:0] c; 
assign c = ((a + b)>>>1) ; 
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_18.png">

```verilog
wire signed [3:0] a = 4'b1001; 
wire signed [3:0] b = 4'b1110; 
wire  [3:0] c,d; 
assign c = a >> 1; 
assign d = a >>> 1; 
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_19.png">

\>>逻辑右移，不管有符号还是无符号，都是补0。

\>>>算术右移，补最高位。

### 3、有符号数与无符号数运算

#### 3.1 计算规则

只有两个操作数都是有符号数，才会把两个操作数都看作有符号数计算，否则无论是有符号数还是无符号数都会按照无符号数计算。

```verilog
wire  [3:0] a = 4'b1001; 
wire signed [3:0] b = 4'b1110; 
wire signed [4:0] c; 
assign c = ((a + b) >>> 1);  //-9=10111
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_20.png">

还是把c当做无符号数移位。

#### 3.2 位宽扩展

把a赋给b，若a是有符号数，则b高位用a的最高位填充，若a是无符号数，b的高位用0填充 。

```verilog
wire  [3:0] a = 4'b1001; 
wire signed [3:0] b = 4'b1001; 
wire signed [4:0] c,d; 
assign c = a; 
assign d = b;
```

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_21.png">

### 4、有符号数乘法

例如有符号数[3:0]a * [3:0]b. 其中a=-5，b=7。a用补码表示为1011，b用补码表示是0111,对于这个例子，乘法过程如下：

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_22.png">

其中，b的符号位跟a相乘的时候需要注意，如果b的符号位是1，则与a相乘的时候实际表示的是-1\*a,所以需要将a的结果按位取反加一。上面的例子b的符号位是0，所以结果是101_1101,补码是1100011 = -35。如果遇到符号位是1的情况，比如a=-5,b=-3：

<img src="/images/post_images/fpga_15_signed_unsigned_number/fpga_15_signed_unsigned_number_23.png">

可以看到上面最后一行的结果需要对a进行取反加一才正确,并且此时取反加一也包括a的符号位。

另外，还需要注意的是所有部分积都要补符号位补到乘法输出值的位数。

​                  

​                  

[  完  ]     