---
title: Verilog编辑仿真环境
author: yasheng
img: /medias/featureimages/fpga.jpg
toc: true
mathjax: false
summary: 搭建轻量级verilog编辑仿真环境
categories: ☸ FPGA
tags:
  - iverilog
  - sublime
  - gtkwave
abbrlink: 3995157926
date: 2020-03-22 09:32:09
password:
---



## Verilog 编辑环境搭建

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

1. 安装汉化插件，`ctrl+shift+p` 弹出搜索框输入 Install Package，选择命令，等一会重新弹出一个框，输入 ChineseLocalizations，然后点击安装就行，然后在菜单栏的 `help` 里最后一行就有language选项，选简体中文就可以了；
2. 安装 verilog 插件，与和安装汉化一样的流程，最后输入verilog，选择第一个安装即可，然后在菜单栏`视图`-`语法`，里面就有verilog选项
3. 安装 verilog 便捷插件，和上面一样的操作，最后输入verilog automatic，安装即可
4. 安装 verilog 模板插件，[下载插件](https://www.lanzous.com/iai53zg)，解压至下图中的文件夹内，Sublime 会自动识别并添加复制的插件。绿色框里面存放了verilog快捷的片段代码，可以自行修改添加。（下载的插件中已经包含常用的代码段）



<img src="/images/post_images/fpga_iverilog/sublime_plugin.jpg">

打开绿色框中的文件夹，选择一个文件用 sublime 打开。黄色框就是代码片段，红色框是快捷命令，白色框是生成代码片段后鼠标的位置。

<img src="/images/post_images/fpga_iverilog/verilog_snipaste.png">

 ${x} 这个是鼠标位置，里面的数字是操作顺序，${1}是生成当前片段后鼠标自动跳到这个${1}位子，${1}位子编写完后可以按 tab 键跳到${2}位置继续编写，以此类推。

## Verilog仿真环境搭建

​	Icarus Verilog是一个轻量、免费、开源的Verilog编译器，基于C++实现，开发者是 Stephen Williams ，遵循 [GNU GPL license](http://www.gnu.org/licenses/gpl.html) 许可证，安装文件中已经包含 GTKWave支持Verilog/VHDL文件的编译和仿真，命令行操作方式，类似gcc编译器，通过testbench文件可以生成对应的仿真波形数据文件，通过自带的GTKWave可以查看仿真波形图，支持将Verilog转换为VHDL文件。

### 安装 iverilog  

可以从[iverilog官网](http://iverilog.icarus.com/)下载，安装 iverilog，默认会把GTKWave一起安装，用于查看生成的波形图。

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

[  完  ]

