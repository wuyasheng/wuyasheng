---
title: python实现图片转txt
author: yasheng
img: /medias/featureimages/python.jpg
toc: true
mathjax: false
summary: 本文使用python实现图片转txt
categories: python
tags:
  - python
  - img
abbrlink: 1299007310
date: 2020-07-01 21:07:09
layout:
password:
---

## python实现图片转txt

---

### 1 功能描述

- 使用 python 调用openCV
- 能够将一幅像素点图，转换成16进制数值，存储在txt文档中

### 2 准备工作

- 安装 python（https://www.python.org/downloads/）
- 安装pip（在安装python可以勾选安装pip）
- pip 安装 openCV（命令行输入 pip install opencv-python ）

### 3 实现过程

#### 1 调用openCV读取图片

```python
# 引用openCV包
import cv2
# 使用openCV读取图片，并获取各个颜色通道的值
img = cv2.imread('16.png')
b_img = img[:,:, 0] # B通道
g_img = img[:,:, 1] # G通道
r_img = img[:,:, 2] # R通道
# 获取图片的形状，大小
a,b,c = img.shape
print(a,b)  


```

#### 2 遍历图片像素点并存储

```python
# 打开文件，
# w 模式：打开不存在的文件会,会新建一个文件；
#        打开存在的文件会先清空后覆盖原有文件；不能读

f = open("test.txt","w")
# 遍历图片，从上到下从左到右，（从左到右，从上到下，可自行修改）
for i in range(b):
    for j in range(a): 
        # 将颜色值转换成16进制
        r_hex   = hex(r_img[j][i])
        g_hex   = hex(g_img[j][i])
        b_hex   = hex(b_img[j][i])
        # 判断颜色的值是否大于 16，避免缺位的现象
        # 5  ->  5 应该是 5  ->  05
        if(r_img[j][i] < 16):
            r_hex_new = '0'+ r_hex[2:]
        else:
            r_hex_new = r_hex[2:]
            
        if(g_img[j][i] < 16):
            g_hex_new = '0'+ g_hex[2:]
        else:
            g_hex_new = g_hex[2:]    
            
        if(b_img[j][i] < 16):
            b_hex_new = '0'+ b_hex[2:]
        else:
            b_hex_new = b_hex[2:] 
        # 拼接颜色    
        rgb_hex = g_hex_new + r_hex_new + b_hex_new
        # 将背景反色，白色变成黑色
        if(rgb_hex == 'ffffff'):
            rgb_hex = '000000'
        # 每个像素点的颜色占据一行，添加换行
        rgb_hex = rgb_hex + '\n'
        f.write(rgb_hex)
# 关闭文件
f.close()
```



### 4 完整代码

```python
import cv2

f = open("test.txt","w")

img = cv2.imread('16.png')
b_img = img[:,:, 0] # B通道
g_img = img[:,:, 1] # G通道
r_img = img[:,:, 2] # R通道

a,b,c = img.shape
print(a,b)    
    
    
for i in range(b):
    for j in range(a): 
        r_hex   = hex(r_img[j][i])
        g_hex   = hex(g_img[j][i])
        b_hex   = hex(b_img[j][i])
        if(r_img[j][i] < 16):
            r_hex_new = '0'+ r_hex[2:]
        else:
            r_hex_new = r_hex[2:]
            
        if(g_img[j][i] < 16):
            g_hex_new = '0'+ g_hex[2:]
        else:
            g_hex_new = g_hex[2:]    
            
        if(b_img[j][i] < 16):
            b_hex_new = '0'+ b_hex[2:]
        else:
            b_hex_new = b_hex[2:] 
            
        rgb_hex = g_hex_new + r_hex_new + b_hex_new
        if(rgb_hex == 'ffffff'):
            rgb_hex = '000000'
        rgb_hex = rgb_hex + '\n'
        f.write(rgb_hex)


f.close()
```



### 5 运行演示

<img src="/images/post_images/python_02_img_to_num/python_img_to_num.gif">

​                   

​                     

[  完  ]