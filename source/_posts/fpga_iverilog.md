---
title: Verilog编辑仿真环境
tags:
  - iverilog
  - sublime
  - gtkwave
categories: FPGA-工具
description: 搭建轻量级verilog编辑仿真环境
top_img: /src/img/featureimages/top.jpg
cover: /src/img/featureimages/fpga_new.jpg
toc: true
toc_number: true
copyright: false
mathjax: false
katex: false
aplayer: false
highlight_shrink: false
aside: true
abbrlink: 10093
date: 2020-03-22 09:32:09
updated: 2021-05-08 13:25:05
keywords:
---

## Sublime Verilog 编辑环境搭建

------

注：本文整理来自[LeonSUST](https://blog.csdn.net/LeonSUST/article/details/104506830?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)，[whik](https://www.cnblogs.com/whik/p/11980103.html)文章整理总结

### 安装 sublime 编辑软件

Sublime官网直接[下载安装](http://www.sublimetext.com/)（不需要破解，会出现注册弹窗，关闭即可）

### 安装 sublime 插件

#### 安装 Package Control 插件

 Package Control是自动管理安装插件，Sublime默认是没有安装该插件的，需要我们手动安装

1. 打开命令窗口*Win/Linux: ctrl+shift+p, Mac: cmd+shift+p*
2. 输入 `Install Package Control`, 按下 `enter`键
3. 等待安装完成，重启Sublime

#### 安装 Verilog 相关插件

1. 安装汉化插件，`ctrl+shift+p` 弹出搜索框输入 `Install Package`，选择命令，等一会重新弹出一个框，输入 `ChineseLocalizations`，然后点击安装就行，然后在菜单栏的 `help` 里最后一行就有language选项，选简体中文就可以了；
2. 安装 verilog 相关插件，与和安装汉化一样的流程，最后输入verilog，依次安装下图中绿色方框内的插件，然后再安装另外两个插件（与语法检查相关），先安装1所示插件，再安装2所示插件；

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/fpga_iverilog/sublime_cfg_1.png)

3. 安装 verilog 模板插件，[下载插件](https://www.lanzous.com/iai53zg)，解压文件，将名为`Sublime-Text-Snippets-for-Verilog-master`的文件夹复制到sublime的插件目录下，打开插件目录方法如下图所示，Sublime 会自动识别并添加复制的插件。该文件夹内存放着自定义的verilog代码段，可以自行修改添加。

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/fpga_iverilog/sublime_cfg_2.png)

4. 代码块文件的格式如下，其中`content`里内容为代码块内容，`tabTrigger`里是代码块的快捷输入，`description`里是具体的描述，`scope`是指对什么语言起作用；代码块中的`${x}`表示输入的第几个位置，可以通过`tap`键进行切换

```
<snippet>
    <content><![CDATA[
always@(posedge clk or negedge rst_n) begin
	if(rst_n == 1'b0)begin
		${1}
    end
	else begin	
        ${2}
    end
end
]]></content>
    <tabTrigger>as</tabTrigger>
    <description>always sequential</description> 
    <scope>source.verilog</scope>
</snippet>
```

### sublime verilog 语法检查

语法检查主要是借助于iverilog的语法检查功能，首先需要安装iverilog，安装方法如下第三节verilog仿真环境配置，接下来如图所示，打开sublimeLinter的配置文件

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/fpga_iverilog/sublime_cfg_3.png)

将如下内容添加进去，iverilog的安装路径，由实际安装为准，保存配置文件，重启sublime即可。

```
// SublimeLinter Settings - User
{
	   "paths": {
       "linux": [],
       "osx": [],
       "windows": [
           "C:\\iverilog\\bin",  // iverilog安装路径
       ],
   },

   "linters": {
       "iverilog": {
           "disable": false,
           "args": ["-i"], // add the "-i" flag
           "excludes": [],
       },
   },

}
```



## VScode verilog 编辑环境搭建

### 安装vscode及相关插件

首先安装VScode，[下载链接](https://code.visualstudio.com/)，安装安成以后安装插件，在插件搜索栏中搜索verilog并选择安装`Verilog-HDL/SystemVerilog/Bluespec SystemVerilog`，安装完成后需要进行相关配置。

1. 首先需要安装catgs，[下载链接](https://github.com/universal-ctags/ctags-win32)，下载完成后解压到对应的位置；
2. 然后将ctags.exe添加环境变量以便能够直接找到该程序，以便实现代码跳转等操作。

### 配置VScode高亮及语法检查

#### 基于vivado 语法检查

1. 首先需要安装vivado程序，并将xvlog路径设置到系统环境变量中；
2. 在VSCode的设置中，选择Lint为xvlog；
3. 重启VSCode；

#### 基于iverilog 语法检查

1. 首先安装iverilog，[下载链接](http://bleyer.org/icarus/)，安装时将iverilog与gtkwave都安装完成；
2. 然后将iverilog设置到环境变量中；
3. 在VSCode的设置中选择Lint为iverilog；
4. 重启VSCode；

注：verilog文件路径不能有中文，有中文路径不能执行语法检查操作



## iVerilog仿真环境搭建

​	Icarus Verilog是一个轻量、免费、开源的Verilog编译器，基于C++实现，开发者是 Stephen Williams ，遵循 [GNU GPL license](http://www.gnu.org/licenses/gpl.html) 许可证，安装文件中已经包含 GTKWave支持Verilog/VHDL文件的编译和仿真，命令行操作方式，类似gcc编译器，通过testbench文件可以生成对应的仿真波形数据文件，通过自带的GTKWave可以查看仿真波形图，支持将Verilog转换为VHDL文件。

### 安装 iverilog  

可以从[iverilog官网](http://iverilog.icarus.com/)下载，[具体下载地址](http://bleyer.org/icarus/)，目前推荐v10，v11查看波形有点问题，等待官方更新；接下来安装 iverilog，默认会把GTKWave一起安装，用于查看生成的波形图。

打开命令行窗口，使用

```
where iverilog
where vvp
where gtkwave
```

查看是否安装成功，若未成功，查看环境变量 Path 中有没有添加软件的路径

### iverilog 基本参数介绍

Icarus Verilog编译器主要包含3个工具：

- iverilog：用于编译verilog和vhdl文件，进行语法检查，生成可执行文件
- vvp：根据可执行文件，生成仿真波形文件
- gtkwave：用于打开仿真波形文件，图形化显示波形

下面来详细介绍几个常用参数的使用方法。

参数   -o

这是比较常用的一个参数了，和GCC中-o的使用几乎一样，用于指定生成文件的名称。如果不指定，默认生成文件名为a.out。如：

```
iverilog -o test test.v
```

参数   -y

用于指定包含文件夹，如果top.v中调用了其他的的.v模块，top.v直接编译会提示

```
led_demo_tb.v:38: error: Unknown module type: led_demo
2 error(s) during elaboration.
*** These modules were missing:
        led_demo referenced 1 times.
***
```

找不到调用的模块，那么就需要指定调用模块所在文件夹的路径，支持相对路径和绝对路径。如：

```
iverilog -y D:/test/demo led_demo_tb.v
```

如果是同一目录下：`iverilog -y ./ led_demo_tb.v`，另外，iverilog还支持Xilinx、Altera、Lattice等FPGA厂商的仿真库，需要在编译时通过-y参数指定库文件的路径，详细的使用方法可以查看官方用户指南：

https://iverilog.fandom.com/wiki/User_Guide

参数  -I

如果程序使用`include 语句包含了头文件路径，可以通过-i参数指定文件路径，使用方法和-y参数一样。如：


```
iverilog -I D:/test/demo led_demo_tb.v
```

参数  -tvhdl

iverilog 还支持把verilog文件转换为VHDL文件，如

```
iverilog -tvhdl -o out_file.vhd in_file.v
```

注意：在testbench中添加如下代码，生成vcd文件

```verilog
initial begin 
	$dumpfile( "wave.vcd");		//生成的波形文件vcd 
	$dumpvars(0, tb_test);		//测试文件名称     
	#10000   $finish;			//设置仿真截止时间，不然仿真一直进行下去，不会停止 
end
```



### iverilog 仿真

#### 编译

通过`iverilog -o wave led_demo_tb.v led_demo.v`命令，对源文件和仿真文件，进行语法规则检查和编译。如果调用了多个.v的模块，可以通过前面介绍的 -y 参数指定源文件的路径，否则编译报错。如果源文件都在同同一个目录，可以直接通过`./`绝对路径的方式来指定。

例如，led_demo_tb.v中调用了led_demo.v模块，就可以直接使用`iverilog -o wave -y ./ top.v top_tb.v`来进行编译。

如果编译成功，会在当前目录下生成名称为wave的文件。

#### 生成波形文件

使用`vvp -n wave -lxt2`命令生成vcd波形文件，运行之后，会在当前目录下生成.vcd文件。

如果没有生成，需要检查testbench文件中是否添加了如下几行：

```verilog
initial begin
		$dumpfile( "wave.vcd");	//生成的波形文件vcd
		$dumpvars(0, tb_seg);	//测试文件名称
     	$dumpon;	//启动值变存储
#100	$dumpoff; 	//关闭值变存储
    	$finish;	//设置仿真截止，不然仿真一直进行下去，不会停止
end
```

#### 打开波形文件

使用命令`gtkwave wave.vcd`，可以在图形化界面中查看仿真的波形图。

#### Verilog转换为VHDL

Icarus Verilog 支持把使用Verilog语言编写的.v文件转换为VHDL语言的.vhd文件。如把led_demo.v文件转换为VHDL文件led_demo.vhd，使用命令：

```
iverilog -tvhdl -o led_demo.vhd led_demo.v
```



常用的编译仿真命令

```
iverilog -o wave *.v     //编译代码，检查语法等错误
vvp -n wave -lxt2        //对编译后的文件进行仿真，输出仿真波形文件
gtkwave wave.vcd     //查看仿真波形
```