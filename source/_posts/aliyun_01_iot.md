---
title: aliyun物联网云平台使用
author: yasheng
img: /medias/featureimages/aliyun.jpg
toc: true
mathjax: false
summary: 本文介绍阿里云物联网使用方法
categories: 硬件
tags:
  - aliyun
  - 物联网
abbrlink: 2464849977
date: 2020-07-02 16:07:09
layout:
password:
---

## 阿里云物联网平台测试

---

### 1 功能介绍

- 使用硬件设备接入阿里云物联网平台
- 实现硬件设备和阿里云物联网平台双向通信
- 阿里云 iot-studio 介绍

### 2 准备工作

- 硬件设备（庆科EMW3060）
- 连云固件下载（http://developer.mxchip.com/downloads/22）
- 硬件配置软件下载（https://wwa.lanzous.com/ijjI4e83juj）
- 注册登录阿里云物联网平台（支付宝）

### 3 云端配置

阿里云物联网平台架构

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_00.png">

打开阿里云生活物联网平台（https://help.aliyun.com/product/30520.html）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_01.png">

登录阿里云物联网平台（https://iot.console.aliyun.com/lk/summary）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_02.png">

创建产品（相当于一个模板，其下的设备都会集成产品的所有功能属性）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_03.png">

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_05.png">



进行产品功能定义，可以自定义添加

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_06.png">

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_07.png">



创建设备

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_08.png">

创建完成后即可查看设备（此时得到**设备的三元素**，用来绑定硬件设备）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_09.png">



### 4 硬件设备配置

本次使用 庆科 EMW3060 P 无线模块，进行测试

- 下载 EMW3060 连接阿里云平台固件包（http://developer.mxchip.com/downloads/22）
- 下载固件下载工具 SecureCRT（https://wwa.lanzous.com/ijjI4e83juj）
- 下载模块配置工具 格西烽火（https://wwa.lanzous.com/ijjI4e83juj）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_10.png">

**硬件配置**，连接EMW 3060 的debug串口，打开SecureCRT，选择对应的串口，设置波特率为 921600

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_11.png">

连接成功后，将模块的boot引脚拉低，按下reset按键（重启设备），即可进入boot模式，进入以下界面

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_12.png">

固件下载：

- 输入命令,擦除旧固件

  ```
  erase 0x13200 0x100000
  ```

- 等待$ 出现表示擦除完成

- 输入命令,写入固件

  ```
  write 0x13200
  ```

- 出现字符Waiting for the file to be sent … (press ‘a’ to abort) 若没出现该字符,请重新输入write 0x13200

- 烧录固件（如下图）

- 选择固件,我的路径是 D:\AliOS-Things\out\helloworld@mk3060\binary\helloworld@mk3060.ota.bin

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_13.png">

烧录速度有点慢.大概要30秒,完成后出现 Programming Completed Successfully!

将boot引脚拉高，按一下重启按键,运行固件

固件烧写完成



### 5 模块配置

- 将模块的通信串口连接，（非debug串口）
- 打开格西烽火软件，连接串口（波特率设置为115200）
- 软件打开之后，打开直接激励文件（.bsp文件）（https://wwa.lanzous.com/ijjI4e83juj）
- 打开之后如下图所示，按照步骤进行配置
- 依次进行配置即可，下方的代码为信息发送格式，根据需求进行修改；模块配置完成后，向云端发送一次数据，云端即可收到消息，设备即可激活

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_14.png">



### 6 云端 / 客户端开发

云端开发：可以使用官方提供的SDK（不会整）

客户端开发：可以直接使用官方提供的 IOT Studio进行开发，以可视化的工具进行编辑，可以直接发布（访问需要绑定域名）

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_15.png">

<img src="/images/post_images/aliyun_01_iot/aliyun_01_iot_16.png">



### 7 小结

阿里云物联网平台提供了一个托管硬件设备的平台，用户不需要接管硬件的连接，只需要通过阿里云物联网平台进行信息交互，简化了物联网的通信方式；以便用户更加简便的开发实现物联网平台。

​               

​                        

[  完  ]





