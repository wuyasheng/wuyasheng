---
title: 经验贴
author: yasheng
layout: "post_user"
img: /medias/featureimages/app.jpg
summary: 本文记录日常经验

abbrlink: 478777895
date: 2020-06-01 10:25:30
---

## 经验贴
---



### 1、蓝奏云链接打不开

解决方法：

1. 挂TZ     
2. 换DNS (联通网络可以修改DNS为 223.5.5.5，实测可用)
3. 去掉www.   
比较简单的就是直接去掉 www. 就行了。

### 2、win10更改pip源             

解决方法：

在c:\user(或者用户)\电脑的用户名\，目录下创建一个命名为“pip”的文件夹（如：C:\Users\Administrator\pip），在该文件夹下创建一个命名为“pip.ini”的文件，在该文件中写入以下内容：

```ini
[global]
index-url=https://pypi.tuna.tsinghua.edu.cn/simple 
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn
disable-pip-version-check = true  
timeout = 6000 

-----其他国内镜像-----
阿里云 http://mirrors.aliyun.com/pypi/simple/ 
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
豆瓣(douban) http://pypi.douban.com/simple/ 
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 
```

若是想在安装package的时候再设置源，可以以这种方式来进行：              

```pip
pip install package_name -i https://pypi.tuna.tsinghua.edu.cn/simple
```





​                                                

​                   

**注：所有内容均来源于网络及个人总结，如本文的内容对您的权利造成了影响，请<a href="mailto:1058349718@qq.com">邮件联系</a>，我们会在第一时间将涉及版权的内容进行删除。**

[  完  ]