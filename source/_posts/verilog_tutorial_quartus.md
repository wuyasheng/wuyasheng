---
title: Quartus 基本使用方法
author: yasheng
img: /medias/featureimages/quartus.jpg
toc: true
mathjax: false
layout: 
summary: 本文整理Quartus基本使用方法
categories: FPGA-工具
tags:
  - FPGA
  - Quartus
abbrlink: 131603873
date: 2020-04-01 13:27:09
password:
---

## Quartus 基本使用方法

---


注 : 本文内容来基于Quartus Prime Lite 18.1 版本，参考 [文章1](https://blog.csdn.net/q35104943/article/details/104988095)、[文章2](https://blog.csdn.net/ssg18829575503/article/details/79177503)

### 1、Quartus  安装

1. 软件下载

   Quartus 现在是 intel 下的一款FPGA工具，可以从 [官网下载](https://fpgasoftware.intel.com/)，选择合适的版本和操作系统，按照提示进行下载。（个人使用的话，可以选择Lite版本（免费），推荐安装经常使用的器件库，Cyclone IV、V，按个人需求）

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_01.jpg">

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_02.jpg">

2. 软件安装

   下载下来的文件包括Quartus安装包、ModelSim安装包、以及选择的器件库；双击QuartusSetup*.exe可执行文件，根据提示安装。该安装文件会自动查找同一文件夹下的软件组件和Device Package（默认选中即可），等待安装完成。

### 2、新建工程 

打开Quartus软件

<img src="/images/post_images/verilog_tutorial_quartus/quartus_03.jpg">

点击菜单栏File-->New Project Wizard...或者点击屏幕中间的New Project Wizard

<img src="/images/post_images/verilog_tutorial_quartus/quartus_04.jpg">

之后会出现如下窗口，工程路径，选择具体的文件夹（可以自己新建好，然后选择）,工程路径及工程名称填写完成后，点击下一步 Next

<img src="/images/post_images/verilog_tutorial_quartus/quartus_05.jpg">

选择空文件夹 Empty project，点击下一步，会出现Add File 界面（根据需要可以进行添加），下一步

<img src="/images/post_images/verilog_tutorial_quartus/quartus_06.jpg">

选择使用的器件型号（根据Family 和 Device筛选）

<img src="/images/post_images/verilog_tutorial_quartus/quartus_07.jpg">

工程新建完成后的界面如下

<img src="/images/post_images/verilog_tutorial_quartus/quartus_08.jpg">

### 3、添加 Verilog 等文件

点击 File --> New 或者用Ctrl+N打开新建对话框，根据需求选择要添加的类型（选择Verilog HDL），弹出编辑界面

<img src="/images/post_images/verilog_tutorial_quartus/quartus_09.jpg">

编辑界面如下

<img src="/images/post_images/verilog_tutorial_quartus/quartus_10.jpg">

右键菜单中可以选择 insert Template添加模板

<img src="/images/post_images/verilog_tutorial_quartus/quartus_11.jpg">

最后写入代码，要有一个文件作为顶层文件（文件名需要与工程名称保持一致）

### 4、添加IP

在上方 Tools 菜单栏中打开 IP Catalog，会在编辑界面的右边出现 IP 界面，选择需要的IP，随后弹出的MegaWizard中配置 IP（设置IP名称，路径，IP的参数等）

<img src="/images/post_images/verilog_tutorial_quartus/quartus_12.jpg">

<img src="/images/post_images/verilog_tutorial_quartus/quartus_13.jpg">

根据提示将IP添加至工程，在生成文件时，可以选择谁让你工程inst文件，方便引用 配置的IP，之后在Project Navigator框的IP Components页可以看到刚才生成的IP。

<img src="/images/post_images/verilog_tutorial_quartus/quartus_14.jpg">

可以打开inst文件，查看端口例化

<img src="/images/post_images/verilog_tutorial_quartus/quartus_15.jpg">

### 5、仿真

1. 仿真设置（此步骤必须先完成)

   quartus菜单栏操作：“Assignments”中打开“settings”，打开“EDA Tool Setting”中的“simulation”如下图界面。
   “Tool name”选择“Modelsim-Altera”；
   “Format for output netlist”我选择“Verilog HDL”，因为我用的是Verilog语言；
   “Time scale”仿真时间单位为1ps可以不用管；
   “Output directory”是仿真文件输出相对路径，可以不用管。然后进行下一步。
   <img src="/images/post_images/verilog_tutorial_quartus/quartus_16.jpg">

2. 创建testbench模板，先进行编译，完成后再如下图操作。Modelsim仿真时，需要编写工程的仿真测试程序，下面操作可以很直接给出仿真测试模板，然后添加相应代码即可。

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_17.jpg">

3. 查看生成的 testbench 文件路径。上一步骤操作完成后，会在刚才的那个相对路径下的仿真文件中生成testbench文件，如下图。我的路径是 C:/Ownfolder/Quartus_projects/Test/simulation/modelsim/test.vt 。然后直接在quartus软件菜单栏中操作：“file”然后“open”，根据路径找到这个文件后打开，如下图。注意所打开程序中我圈出来的地方，以后要用到这些。
   <img src="/images/post_images/verilog_tutorial_quartus/quartus_18.jpg">

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_19.jpg">

4. 添加文件。很重要的步骤。按照步骤1再次打开“settings”。点击后面圈住的“Test Benches”。

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_20.jpg">

5. 继续添加文件。经过步骤4后点击“new”，如下图。此处得回到步骤3打开的testbench模板文件。长的名字 "test_vlg_tst" 就是“Test bench name”要填的，避免手误，可以直接拷贝，划线4位置处找到 testbench 路径即可。最后点击“Add”“OK”“Apply”等，完成配置。
   <img src="/images/post_images/verilog_tutorial_quartus/quartus_21.jpg">

6. 修改 testbench 代码，生成的模板并不能够直接使用，需要进行修改(根据具体的项目修改)

7. 最后，按照如下图操作：“Tool => Run Simulation Tool => RTL Simulation”，稍等片刻modelsim便可自动启动。

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_22.jpg">

8. 仿真可能会出现一些问题，打不开modelsim，可以查看是否是路径未配置，可以在 tools -- > option --> general --> EDA tools option下进行设置，设置ModelSim-Altera的路径；

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_23.jpg">

### 6、烧写程序

1. 分配引脚，在编译综合完成之后，确定没有错误之后，对设计进行引脚的分配，Assignments --> Pin Planner，根据设计需求，完成引脚分配，最后保存

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_24.jpg">

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_25.jpg">

2. 引脚分配成功之后，对整个工程进行编译实现

   <img src="/images/post_images/verilog_tutorial_quartus/quartus_26.jpg">

3. 连接好板子的调试接口，接通电源。（没装驱动的在设备管理器里看不到连接好的JTAG，在Quartus软件中，有个driver文件夹是驱动文件夹，可以自行百度）。

   依次点击 tools -> programmer -> hardware setup 选择已连接的开发板，直接start就可以了。  

  



[   完  ]



