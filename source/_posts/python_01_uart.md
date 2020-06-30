---
title: python实现串口发送
author: yasheng
img: /medias/featureimages/python.jpg
toc: true
mathjax: false
summary: 本文使用python实现串口发送
categories: python
tags:
  - python
  - uart
abbrlink: 2175180854
date: 2020-06-30 21:07:09
layout:
password:
---

## python实现串口发送 txt 文件

---



### 1 功能描述

- 使用 python 实现端口自动扫描，能够**自由选取端口**；
- 实现扫描当前文件夹中的txt文件，能够**自由选择需要发送的文本文件**；
- 发送文本的过程中能够逐行发送，并**忽略行尾的换行符**；
- 以波特率 115200 进行发送（可以手动修改）； 

### 2  准备工作

- 安装python以及pip

- 安装python（https://www.python.org/downloads/）

- 安装pip（在安装python可以勾选安装pip）

- pip安装 serial 和 pyserial（pip install xxx）


### 3 实现过程

#### 1 扫描端口列表

```python
# 需要引用串口包
import serial
import serial.tools.list_ports

# 扫描串口，并返回串口列表
def scan_serial():
	port_list = list(serial.tools.list_ports.comports())
	if len(port_list) <= 0:
		print("The Serial port can't find!")
	else:
		print("\n******************************")
		print("****** 串口扫描结果如下 ******\n")
		print("序号   :  端口")
		for x in range(0,len(port_list)):
			print(x,"     : ",port_list[x])
		print("\n******************************\n")
	return port_list
```

#### 2 序号输入判断

根据输入的序号，选择端口以及文件，对输入的字符进行判断，使之符合条件

```python
# 定义序号判断函数，若不满足条件，不跳出
def num_judg(num_max):
	while True:
		a = input("\n请输入选择对象的序号:")
		if a.isdigit():
			a_int = int(a)
			if a_int >= 0 and a_int < len(num_max):
				return a_int
			else:
				print("\nerror: 输入序号超出范围！！！\n")
		else:
			print("\nerror: 输入包含非数字字符！！！\n")
```

#### 3 选择端口并打开

根据提示输入选择端口的序号，返回端口名并打开端口

```python
# 获取端口
port_choose = port_list[a_int][0]

# 打开串口,异常处理
try:
	ser = serial.Serial(port_choose,115200)
except Exception as e:
	print("\n** 选择的端口在打开时出现异常出现异常！！\n")
	print("异常信息：",e,"\n")

# 如果没有异常执行下列程序
else:
```



#### 4 设置串口波特率

同样，根据提示输入选择波特率的序号，返回波特率的值

```python
# 选择波特率
bps_list = [300,1200,2400,9600,19200,38400,115200]

print("\n******请选择波特率 bps *******\n")
print("序号   :  波特率")
for x in range(0,len(bps_list)):
	print(x,"     : ",bps_list[x])
print("\n******************************\n")

bps_int = num_judg(bps_list)
bps = bps_list[bps_int]

print("\n**波特率设置为：",bps," \n")
print("\n******************************\n")
```



#### 5 获取需要发送的txt文件

同样，根据提示输入选择文件的序号，返回文件名

```python
# 获取当前目录下的文件列表,提取后缀为 .txt 的文件名称
def file_name(file_dir):
	filelist = os.listdir(file_dir)
	files = []

	for x in range(0,len(filelist)):
		if filelist[x].split('.')[-1] =='txt' :
			files.append(filelist[x])
	return files
```



#### 6 读取文件并发送

逐行读取文件内容，忽略行尾的换行符，进行发送

```python
# 准备通过串口写数据
	if ser.isOpen():
		print("**",port_choose," 串口打开成功 \n")
		
		# 准备选择要写的文件进行发送
		print("序号   :  文件")
		for x in range(0,len(filelist)):
			print(x,"     : ",filelist[x])

		b_int = num_judg(filelist)

		file = filelist[b_int]

		# 写数据
		# 从文本中读取数据，并以十六进制的格式进行发送
		f = open(file)

		print("\n**",file," 文件打开成功 \n")

		# 遍历文本数据（每行一个8位的数据）
		cnt = 0
		for i in f.readlines():
			str_1 		= str.strip(i)
			data_hex 	= bytes.fromhex(str_1)
			result 		= ser.write(data_hex)
			time.sleep(0.001)
			cnt = cnt + 1
			print("** 数据正在发送中：",'%.4f' % (cnt/40.96),"%\r",end='') # 因为有4096行数据，可以自行修改


		print("\n\n** 数据发送成功")
		ser.close()#关闭串口
		if ser.isOpen():
			print("**",port_choose," 串口关闭失败 \n")
		else:
			print("**",port_choose," 串口关闭成功 \n")

	print("\n** 本次服务到此结束 **\n")
```



### 4 完整代码

```python
import serial
import serial.tools.list_ports
import time
import os


# 扫描串口，并返回串口列表
def scan_serial():
	port_list = list(serial.tools.list_ports.comports())
	if len(port_list) <= 0:
		print("The Serial port can't find!")
	else:
		print("\n******************************")
		print("****** 串口扫描结果如下 ******\n")
		print("序号   :  端口")
		for x in range(0,len(port_list)):
			print(x,"     : ",port_list[x])
		print("\n******************************\n")
	return port_list


# 定义序号判断函数
def num_judg(num_max):
	while True:
		a = input("\n请输入选择对象的序号:")
		if a.isdigit():
			a_int = int(a)
			if a_int >= 0 and a_int < len(num_max):
				return a_int
			else:
				print("\nerror: 输入序号超出范围！！！\n")
		else:
			print("\nerror: 输入包含非数字字符！！！\n")


# 获取当前目录下的文件列表,提取后缀为 .txt 的文件名称
def file_name(file_dir):
	filelist = os.listdir(file_dir)
	files = []

	for x in range(0,len(filelist)):
		if filelist[x].split('.')[-1] =='txt' :
			files.append(filelist[x])
	return files

# 清除屏幕
os.system('cls')

port_list = scan_serial()

#选择串口打开
a_int = num_judg(port_list)

# 获取端口
port_choose = port_list[a_int][0]


# 打开串口,异常处理
try:
	ser = serial.Serial(port_choose,115200)
except Exception as e:
	print("\n** 选择的端口在打开时出现异常出现异常！！\n")
	print("异常信息：",e,"\n")

# 如果没有异常执行下列程序
else:
	# 获取当前目录下的txt文件
	root = os.getcwd()
	filelist = file_name(root)

	# 准备通过串口写数据
	if ser.isOpen():
		print("**",port_choose," 串口打开成功 \n")
		
		# 准备选择要写的文件进行发送
		print("序号   :  文件")
		for x in range(0,len(filelist)):
			print(x,"     : ",filelist[x])

		b_int = num_judg(filelist)

		file = filelist[b_int]

		# 写数据
		# 从文本中读取数据，并以十六进制的格式进行发送
		f = open(file)

		print("\n**",file," 文件打开成功 \n")

		# 遍历文本数据（每行一个8位的数据）
		cnt = 0
		for i in f.readlines():
			str_1 		= str.strip(i)
			data_hex 	= bytes.fromhex(str_1)
			result 		= ser.write(data_hex)
			time.sleep(0.001)
			cnt = cnt + 1
			print("** 数据正在发送中：",'%.4f' % (cnt/40.96),"%\r",end='') # 因为有4096行数据，可以自行修改


		print("\n\n** 数据发送成功")
		ser.close()#关闭串口
		if ser.isOpen():
			print("**",port_choose," 串口关闭失败 \n")
		else:
			print("**",port_choose," 串口关闭成功 \n")

	print("\n** 本次服务到此结束 **\n")
	
```



### 5 运行演示

<img src="/images/post_images/python_01_uart/python_uart_01.gif">

​             

​                      

​                           

[  完  ]

