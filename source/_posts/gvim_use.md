---
title: gvim配置及使用
tags:
  - gvim
categories: gvim
description: 本文记录gvim的配置及使用方法
top_img: /src/img/featureimages/top.jpg
cover: /src/img/featureimages/gvim_new.jpg
toc: true
toc_number: true
copyright: false
mathjax: false
katex: false
aplayer: false
highlight_shrink: false
aside: true
abbrlink: 30059
date: 2021-08-19 12:37:09
updated: 2021-08-19 13:25:05
keywords:
---

##  gvim配置及使用方法

---

### 简介

Vim 是 Linux 上的著名的文本编辑器，它是早年的 Vi 编辑器的加强版。这个 GVim 是 Windows 版的，并且有了标准的windows风格的图形界面，所以叫g(graphical)Vim。这是一个国际版本，会根据安装的平台自动选择相应语言包，支持中文及其各种编码，连界面也是中文的，请放心使用。

### Windows下安装及配置

#### gvim安装

1. 首先下载安装包，选择合适的版本即可，[下载链接](https://github.com/vim/vim-win32-installer/releases)
2. 双击安装包，选择语言（中文简体）-> 下一步 -> 接收许可 -> 下一步 -> 下一步 ->默认 -> 安装 -> 等待安装成功
3. 默认的安装路径：C:\Program Files\Vim
4. 打开新安装的gvim，界面如下图所示

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/gvim_use/gvim_use_01.png)

#### gvim配置

为了能够让gvim的界面更好看以及使用更加的便捷，需要对gvim进行配置，配置的方法则是通过配置文件实现的。在gvim的安装路径下：C:\Program Files\Vim，有一个`_vimrc`文件，该文件是gvim的配置文件。但是这个文件不允许修改，需要以命令行的方式启动记事本，在记事本中打开gvim安装路径（显示所有文件），打开`vim_rc`文件，如图所示，并在图示位置添加自定义配置代码

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/gvim_use/gvim_use_02.png)

常用的配置如下（复制到配置文件即可，可根据需要进行调整）

```
colo evening				"配置主题颜色为深灰色
set guifont=Courier_New:h12	 "设置字体及大小
set	number					"显示行号
set lines=24				"设置窗口大小
set columns=72
set guioptions-=T 			"去除图形菜单栏

set t_Co=256        		"开启256色支持
set showcmd     			"select模式下显示选中的行数
set ruler       			"总是显示光标的位置
set cursorline      		"高亮显示当前行
set cursorcolumn 			"高亮显示当前列

set autoindent      		"设置自动缩进
set expandtab       		"将制表符扩展为空格
set tabstop=4       		"设置编辑时制表符所占的空格数
set shiftwidth=4    		"设置格式化时制表符占用的空格数
set softtabstop=4			"设置4个空格为制表符
set nowrap     				"禁止折行
set encoding=utf8			"设置编码格式

filetype on     			"设置开启文件类型侦测
syntax enable       		"开启语法高亮功能
syntax on       			"自动语法高亮
filetype plugin on			"为特定的文件类型允许插件文件的载入"


"分割出来的窗口位于当前窗口下边/右边
set splitbelow
set splitright


```

gvim配置完成的效果如图所示

![](https://cdn.jsdelivr.net/gh/wuyasheng/pic_bed/images_blog/gvim_use/gvim_use_03.png)



### linux下安装及配置

#### gvim安装

linux下默认安装的有vi或者vim，可直接使用。本人使用的linux自带gvim，可直接使用

#### gvim配置

Linux下的配置文件在个人目录下，可以通过命令行`cd ~`来打开个人命令，在个人目录下应当有一个`.vimrc`的文件，该文件为gvim的配置文件，具体配置参数可以参考windows下的配置。其中字体的大小可以采用如下配置

```
set guifont = Courier\ 14
```



### 常用快捷键

暂时省略，后续补充



### 插件安装及使用

#### systemverilog的高亮显示

1. linux在home文件夹下创建目录
```
mkdir -p ~/.vim/ftdetect 
mkdir -p ~/.vim/syntax
```
2. 打开语法检测等功能
```
//在配置文件中添加，如下配置
syntax   on           "确定vim打开语法高亮 
filetype on           "打开文件类型检测 
filetype plugin on    "为特定的文件类型允许插件文件的载入 
filetype indent on    "为特定的文件类型载入缩进文件
```
3. 添加语法高亮文件
verilog语法检测文件[下载](https://www.vim.org/scripts/script.php?script_id=1573)，下载的文件为`systemverilog.vim`，然后将下载的文件添加到上文新建的`syntax`文件夹中，在上文新建的`ftdetect `文件夹中新建`sv.vim`文件，并添加如下内容（两种方式都可），`filetype`的名字就是上文中`syntax`文件夹下的语法文件的名称
```
"含义是指在打开或者新建 .sv或者.v文件时进行高亮显示
au BufRead,BufNewFile *.sv,*.v set filetype=systemverilog
au BufRead,BufNewFile *.sv,*.v setf systemverilog
```







