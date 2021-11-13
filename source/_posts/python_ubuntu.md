---
title: ubuntu python 多版本安装
tags:
  - python
  - ubuntu
categories: python
description: 本文基于ubuntu16.04安装python3
top_img: /src/img/featureimages/top.jpg
cover: /src/img/featureimages/python_new.jpg
toc: true
toc_number: true
copyright: false
mathjax: false
katex: false
aplayer: false
highlight_shrink: false
aside: true
abbrlink: 15931
date: 2021-02-08 12:37:09
updated: 2021-05-08 13:25:05
keywords:
---

##  ubuntu安装多版本python3及pip

---

注：本文整理来自[参考文1](https://blog.csdn.net/jiduochou963/article/details/88788805)，[参考文2](https://blog.csdn.net/zhengcaihua0/article/details/79681991)、[参考文3](https://blog.csdn.net/qq_29837161/article/details/83616364)、[参考文4](https://www.cnblogs.com/ExMan/p/14317884.html)文章整理总结

### 前言

Ubuntu16.04下默认系统自带python2.7和Python3.5的版本，这个版本被系统很多程序所依赖，所以不建议删除。如果想要使用其他版本，直接命令行安装，与自带版本会有点问题（pip版本指向）；

而使用其他版本的Python ，我们可以通过编译安装源码包的方式进行安装，该种方式与系统默认包之间是没有任何影响的，亲测有效。

### 准备工作

- 下载python源码包，[python3.6.5下载地址](https://www.python.org/downloads/release/python-365/)，其他版本类似

- 下载setuptools，直接编译安装python也会安装，但版本较低，可以通过官网下载源码进行安装，[下载地址](https://pypi.org/project/setuptools/#files)

- 下载pip，直接编译安装python也会安装，但版本较低，可以通过官网下载源码进行安装，[下载地址](https://pypi.org/project/pip/#files)


下载完成后会有3个压缩包，将3个包放在合适的位置，如果下载速度慢可以从此处[下载](https://wwa.lanzous.com/b0deq01yj)，提取码（cnf5）

### 安装过程

#### python安装

首先将压缩包进行解压，对Python-3.6.5.tgz 文件解压需要用到一下命令

```
tar xfz Python-3.6.5.tgz
```

解压完成后，打开该文件夹

```
cd Python-3.6.5/
```

在进行安装之前首先检查是否安装了 ssl，若没有安装，则进行安装，因为pip3需要该组件

```
sudo apt-get install openssl
sudo apt-get install libssl-dev
```

通过源码进行安装主要有3步，分别是配置、编译、安装

**配置** ：主要配置安装路径，以及配置ssl 以免无法正常使用pip3，prefix的=后为python的安装路径，可以进行修改，看个人喜好。

```
sudo ./configure --with-ssl --prefix=/opt/python3.6
```

**编译**：运行如下命令

```
sudo make
```

**安装**：运行如下命令

```
sudo make install
```

等待安装完成

安装完成之后配置安装的python的软链接，ubuntu的默认软链接为

```
/usr/bin/python  -->  python3.5
/usr/bin/python2 --> python2.7
/usr/bin/python3 --> python3.5
```

可以将python3.6.5设置为python，方法如下

```
sudo rm /usr/bin/python
sudo ln -s /opt/python3.6/bin/python3.6 /usr/bin/python
```

此时在终端输入 python，进入python3.6.5的命令行模式，ctrl + z 退出该模式

此时python安装完成，安装路径为 /opt/python3.6

​       

----

**说明**：因为需要吧 pip 和 python3.6 关联在一起，所以运行 setup.py 都是用python3.6来运行的。安装的这个pip是包含pip和pip3的，pip是用来安装第三方模块的。在安装pip之前，需要安装setuptools

#### setuptools安装

首先回到压缩包所在的目录打开终端，此时的python 指向 python3.6.5，所以可以直接通过python执行setup.py

```
sudo tar -zxvf setuptools-53.0.0.tar.gz
cd setuptools-53.0.0
sudo python setup.py build
sudo python setup.py install
```

等待完成安装即可

#### pip安装

首先回到压缩包所在的目录打开终端，此时的python 指向 python3.6.5，所以可以直接通过python执行setup.py

```
sudo tar -zxvf  pip-21.0.1.tar.gz
cd  pip-21.0.1
sudo python setup.py build
sudo python setup.py install
```

等待完成安装即可

默认的pip和pip3已经被占用，可以建立新的软链接完成pip3的使用

```
sudo ln -s /opt/python3.6/bin/pip3 /usr/bin/pip36
```

此时可以直接使用pip36，查看pip3版本，以及指向python3.6.5

```
pip36 -v
```



#### pip换源

pip默认源在国外，速度慢，所以需要更换国内的源，方法如下

```
cd ~/.pip
##  如果不存在.pip则创建目录
mkdir ~/.pip
cd ~/.pip
## 新建 pip.conf 文件
touch pip.conf
## 编辑 pip.conf
sudo gedit ~/.pip/pip.conf
```
在pip.conf中写入如下内容，即可 ，也可以换成其他源，格式相同
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/ 
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```

其他国内源

```
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
阿里云 http://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣(douban) http://pypi.douban.com/simple/
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/
```



### 附加 + apt换源

ubuntu默认源安装速度慢，可以换成国内源，软件安装及更新速度快，方法如下，参考至：[Ubuntu16.04 更换软件源](https://blog.csdn.net/apollo_miracle/article/details/109449229)

#### 国内源地址

[国内源查看](https://wiki.ubuntu.org.cn/%E6%A8%A1%E6%9D%BF:16.04source)

```
## Ubuntu 官方（歐洲，國內較慢，無同步延遲）
http://archive.ubuntu.com/ubuntu/
## Ubuntu 官方中國（目前是阿里云）
http://cn.archive.ubuntu.com/ubuntu/
## 網易（廣東廣州電信/聯通千兆雙線接入）
http://mirrors.163.com/ubuntu/
## 搜狐（山東聯通千兆接入）
http://mirrors.sohu.com/ubuntu/
## 阿里云（北京萬網/浙江杭州阿里云服務器雙線接入）
http://mirrors.aliyun.com/ubuntu/
## 腾讯
http://mirrors.cloud.tencent.com/ubuntu/
## 华为
http://mirrors.huaweicloud.com/ubuntu/
## 首都在線科技
http://mirrors.yun-idc.com/ubuntu/
## 貝特康姆（江蘇常州電信）
http://centos.bitcomm.cn/ubuntu/
## CNNIC
https://mirrors.cnnic.cn/ubuntu/
```

#### 查看ubuntu版本

Ubuntu 每个发行版本都有自己的代号，我们要通过我们电脑上 Ubuntu 的代号去找对应的源，Ctrl+Alt+T 打开终端，执行以下命令：

```
lsb_release -a
```

然后会得到我们自己的 Ubuntu 的版本信息 ，最后一栏 codename 后面的就是我们自己的 Ubuntu 的代号。比如本机安装的是 Ubuntu 16.04.7，查出来的代号就是 xenial。

#### 编辑源的文件

源的文件模板

```
deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb http://cn.archive.ubuntu.com/ubuntu/ xenial-proposed main restricted universe multiverse
# 源碼
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial-proposed main restricted universe multiverse
```

将里面的内容复制到文本编辑器，然后将网址http://cn.archive.ubuntu.com/ubuntu/替换成自己想要替换的网址，把xenial替换成自己的Ubuntu代号。

#### 阿里源示例

```
# 阿里云源
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
# 源碼
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
##測試版源
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
```

#### 备份原来的源文件

打开源文件路径：

```
cd /etc/apt/
```

备份源文件sources.list，将sources.list备份到sources.list.bak

```
sudo cp sources.list sources.list.bak
```

#### 修改源文件

执行命令打开sources.list文件：可以用vim，gedit等工具打开（推荐使用gedit）

```
sudo gedit /etc/apt/sources.list
```

然后将准备好的源复制到sources.list中去，并覆盖原来的文件内容

#### update 更新源命令

```
sudo apt-get update
```

如果运行结束没有报错，则证明已经成功更换软件源了！



## Windows python安装-补充

### 多版本安装

python windows多版本安装时，在安装过程中修改默认的安装路径，将所有版本安装到同一个文件夹下的子文件夹（非必要，但是方便进行统一的管理）

将需要安装的各个版本安装成功之后，需要配置环境变量（包括根目录和Script目录），以便能够直接通过命令行运行；将多个版本的python全部添加至系统的环境变量当中后，在修改默认的python版本时，只需要对应的python版本的环境变量移动至最前端即可，再次使用python时，就会调用该版本的python。

### windows pip 换源

1. 打开appdata文件夹，在资源管理器的地址栏输入%appdata%后回车：

   ```
   本人路径为  C:\Users\10583\AppData\Roaming
   ```

2. 新建一个pip文件夹，在pip文件夹里面新建一个配置文件pip.ini：

3. 在配置文件中输入如下内容后保存即可：

   ```
   [global]
   index-url = https://pypi.tuna.tsinghua.edu.cn/simple/ 
   [install]
   trusted-host = pypi.tuna.tsinghua.edu.cn
   ```

