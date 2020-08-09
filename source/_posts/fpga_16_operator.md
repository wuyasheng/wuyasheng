---
title: Verilog 运算符优先级
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: true
summary: 本文整理运算符优先级
categories: FPGA-详解
tags:
  - Verilog
  - 运算符
abbrlink: 1205431079
date: 2020-08-09 20:40:09
layout:
password:
---

## Verilog 运算符优先级

---

### 1、图表描述

<img src="/images/post_images/fpga_16_operator/fpga_16_operator_01.png">

<img src="/images/post_images/fpga_16_operator/fpga_16_operator_02.png">

### 2、优先级详解

| 运算符                               | 优先级           |
| ------------------------------------ | ---------------- |
| 单目运算符：!、~、^、~^、\|、&       | 0（优先级最高）  |
| 幂次：**                             | 1                |
| 乘积、除、取余：*、/、%              | 2                |
| 二进制加、减：+、-                   | 3                |
| 逻辑移位、算术移位：>>、<<、>>>、<<< | 4                |
| 比较运算符：>、>=、<、<=             | 5                |
| 比较-等于-不得于：==、!=、===、!==   | 6                |
| 按位与：&                            | 7                |
| 按位异或、同或：^、~^、^~            | 8                |
| 按位或：\|                           | 9                |
| 逻辑与：&&                           | 10               |
| 逻辑或：\|\|                         | 11               |
| 3目运算符：?  :                      | 12               |
| 拼接运算符：{ , }                    | 13（优先级最低） |

​                          

​                     

[  完  ]