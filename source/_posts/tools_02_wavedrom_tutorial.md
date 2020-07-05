---
title: WaveDrom波形绘制教程
author: yasheng
img: /medias/featureimages/wavedrom.jpg
toc: true
mathjax: false
summary: 本文整理WaveDrom简易教程
categories: ♐ Tools
tags:
  - 波形绘制
  - WaveDrom
abbrlink: 1769026586
date: 2020-05-22 10:10:09
layout:
password:
---

## WaveDrom波形绘制教程

---

注 : 本文内容来摘抄整理至[WaveDrom官方网站](https://wavedrom.com/)，可以在[在线编辑网址](https://wavedrom.com/editor.html)上编辑，也可以下载客户端本地编辑

### 1 信号

元素**"信号"**是 WaveLanes 的数组。每个 WaveLane 都有两个必填字段**："名称"**和**"wave"。**

```json
{ signal: [{ name: "Alfa", wave: "01.zx=ud.23.456789" }] }
```

**"wave"**字符串中的每个字符表示单个时间段。符号 **"."** 将以前的状态再延长一段时间。下面是它的外观：

### 2 时钟

数字时钟是一种特殊的信号类型。它每时间段变化两次，具有正极性或负极性。它还可以在工作边缘有一个可选的标记。时钟的块可以与其他信号状态混合，以创建时钟门控效果。下面是代码：

```json
{ signal: [
  { name: "pclk", wave: 'p.......' },
  { name: "Pclk", wave: 'P.......' },
  { name: "nclk", wave: 'n.......' },
  { name: "Nclk", wave: 'N.......' },
  {},
  { name: 'clk0', wave: 'phnlPHNL' },
  { name: 'clk1', wave: 'xhlhLHl.' },
  { name: 'clk2', wave: 'hpHplnLn' },
  { name: 'clk3', wave: 'nhNhplPl' },
  { name: 'clk4', wave: 'xlh.L.Hx' },
]}
```

<img src="/images/post_images/wavedrom_tutorial/wavedrom_tutorial_01.png">

**注：可以通过 配置，周期和相位修改时钟的属性，详情看第6节**

### 3 波形结合

```json
{ signal: [
  { name: "clk",  wave: "P......" },
  { name: "bus",  wave: "x.==.=x", data: ["head", "body", "tail", "data"] },
  { name: "wire", wave: "0.1..0." }
]}
```

<img src="/images/post_images/wavedrom_tutorial/wavedrom_tutorial_02.png">

### 4 间隙

```json
//其中数字代表一个时钟周期的信号，可以对数字进行设置名称等，
//不同的数字表示的颜色不同
{ signal: [
  { name: "clk",         wave: "p.....|..." },
  { name: "Data",        wave: "x.345x|=.x", data: ["head", "body", "tail", "data"] },
  { name: "Request",     wave: "0.1..0|1.0" },
  {},
  { name: "Acknowledge", wave: "1.....|01." }
]}
```

<img src="/images/post_images/wavedrom_tutorial/wavedrom_tutorial_03.png">

### 5 波形分组

将波形进行分组，由括号进行表示

```json
{ signal: [
    {    name: 'clk',   wave: 'p..Pp..P'},
    ['Master',
     ['ctrl',
      {name: 'write', wave: '01.0....'},
      {name: 'read',  wave: '0...1..0'}
     ],
     {  name: 'addr',  wave: 'x3.x4..x', data: 'A1 A2'},
     {  name: 'wdata', wave: 'x3.x....', data: 'D1'   },
    ],
    {},
    ['Slave',
     ['ctrl',
      {name: 'ack',   wave: 'x01x0.1x'},
     ],
     {  name: 'rdata', wave: 'x.....4x', data: 'Q2'},
    ]
]}
```

<img src="/images/post_images/wavedrom_tutorial/wavedrom_tutorial_04.png">

### 6 周期和相位

```json
{ signal: [
  { name: "CK",   wave: "P.......",                                              period: 2  },
  { name: "CMD",  wave: "x.3x=x4x=x=x=x=x", data: "RAS NOP CAS NOP NOP NOP NOP", phase: 0.5 },
  { name: "ADDR", wave: "x.=x..=x........", data: "ROW COL",                     phase: 0.5 },
  { name: "DQS",  wave: "z.......0.1010z." },
  { name: "DQ",   wave: "z.........5555z.", data: "D0 D1 D2 D3" }
]}
```

<img src="/images/post_images/wavedrom_tutorial/wavedrom_tutorial_05.png">



### 7 配置属性

**配置：[hscale：]**属性控制关系图的水平比例。用户可以将任何整数大于 0。

```json
{ signal: [
  { name: "clk",     wave: "p...." },
  { name: "Data",    wave: "x345x",  data: ["head", "body", "tail"] },
  { name: "Request", wave: "01..0" }
  ],
  config: { hscale: 1 }
}
```

**其他略**

**注**： 查看更多内容，进入[wavedrom官方教程](https://wavedrom.com/tutorial.html)                

​                        

[  完  ]