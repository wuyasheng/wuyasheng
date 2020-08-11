---
title: Nodemcu 8266使用方法
author: yasheng
img: /medias/featureimages/nodemcu.jpg
toc: true
mathjax: false
summary: 本文介绍Nodemcu 8266内容
categories: 硬件
tags:
  - nodemcu
  - 物联网
abbrlink: 454003237
date: 2020-08-11 20:40:09
layout:
password:
---

## Nodemcu 8266使用方法

---

 注 : 本文内容收集至网络，[参考文-1](https://blog.csdn.net/weixin_42573320/article/details/86648994)、[参考文-2](https://www.cnblogs.com/yafengabc/p/8681380.html)、[参考文-3](https://blog.csdn.net/huangshangcheng/article/details/80796296)、[参考文-4](https://blog.csdn.net/FourLeafCloverLLLS/article/details/83047369)、

### 1、NodeMCU 开发环境搭建

- 固件
- 烧录软件
- 烧录固件
- 编程工具

文件打包蓝奏云下载链接：https://wwa.lanzous.com/iN8obfi6unc

#### 1.1 固件下载

可以下载官方的固件库进行开发，[固件下载网址](https://nodemcu-build.com/index.php) ，填入自己的邮箱

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_01.png">

下拉页面，选择固件包含的模块

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_02.png">

选择完成后，下拉至最下方，点击开始构建

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_03.png">

跳转到如下页面，表示构建开始，等待几分钟，就会收到构建完成的邮件，及固件下载地址

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_04.png">

提供的固件包含一个 float 和一个 integer，一般 integer就行了

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_05.png">

#### 1.2 烧录软件下载

可以使用官网提供的烧写软件进行烧写，[烧写软件链接](https://github.com/marcelstoer/nodemcu-pyflasher)

选择合适的版本进行下载，绿色软件，无需安装

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_06.png">

#### 1.3 固件烧写

链接nodemcu 至电脑，可能需要安装对应的串口驱动，自行百度

打开固件烧写软件 NodeMCU-PyFlasher-4.0-x64.exe

依次进行，选择串口（设备管理器可查看）、选择固件文件、开始烧写，等待完成即可

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_07.png">

#### 1.4 编程软件使用

编程软件可直接 [下载](https://esp8266.ru/esplorer/)，下载完成后是一个压缩包，解压即可，使用之前，需要进行jdk的安装及配置（jdk版本大于1.8，安装配置自行百度，高于1.8可能会闪退，退回1.8即可）双击该bat文件即可打开编程软件

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_08.png">

编程软件如下图所示

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_09.png">

使用方法为，将nodemcu连接电脑，选择串口，设置波特率（一般为115200），点击open 进行连接

连接完成后，会打印 nodemcu 信息（若没有打印结果，可以按一下nodemcu的重启键即可）

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_10.png">

### 2、NodeMCU 驱动 iic oled驱动

本次使用 iic 0.92oled显示屏，为了驱动该显示屏模块，需要固件支持 iic 和 u8g2 模块

#### 2.1 驱动步骤

在构建固件时，进行勾选 iic 及 u8g2，并且根据 oled 的驱动芯片型号，选择对应的 u8g2 库（本次使用的是SSD1315芯片驱动，兼容SSD1306）

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_11.png">

固件正常构建，进行烧写即可

对应模块的使用方法可以点击模块右侧的小图标，打开模块使用介绍

<img src="/images/post_images/aliyun_02_nodemcu/aliyun_02_nodemcu_12.png">

烧写完成后，进行程序的编写，程序如下

该教程使用：https://blog.csdn.net/weixin_42573320/article/details/86648994

#### 2.2 代码

```lua
-- oled init code 
function init_OLED(sda,scl)     
    sla = 0x3c     
    i2c.setup(0, sda, scl, i2c.SLOW)     
    disp=u8g2.ssd1306_i2c_128x64_noname(0,sla)     
    disp:setFont(u8g2.font_unifont_t_symbols)     
    disp:setFontPosTop() 
end 
-- oled display code 
function print_OLED()   
    disp:drawFrame(0, 0,128,16)   
    disp:setFont(u8g2.font_6x10_tf)   
    disp:drawStr(30, 5, "Hello Mcu!!")   
    disp:drawStr(30, 20,"nihao")   
    disp:drawStr(80, 40, str1)   
    disp:drawFrame(0, 16,128,45)   
    disp:sendBuffer()  
end 
-- main code 
sda = 1    -- 定义端口 
scl = 2    -- 定义端口 
str1 = "hello" 
print('code bengin to run')  
init_OLED(sda,scl)   
-- oled init 
print_OLED()         -- oled disp  
print("code run end....") 
```

还有各种各样的功能，可以进行额外的学习

### 3、系列教程

简书，大佬各模块使用： https://www.jianshu.com/c/822b99541ef5

CSDN，大佬各模块使用：https://blog.csdn.net/weixin_42573320/category_8603700.html

​                    

​                        

[  完  ]

