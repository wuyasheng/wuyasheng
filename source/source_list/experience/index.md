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

### 3、github访问速度慢

GitHub 在国内访问速度慢的问题原因有很多，但最直接和最主要的原因是 GitHub 的分发加速网络的域名遭到 dns 污染。

解决方法：

1. 查询网址对应的 IP 地址，查询地址：https://www.ipaddress.com/

   点击下面 `域名`（domain）自动跳转到 `ipaddress.com` 查询结果页面：

- [github.com](https://github.com.ipaddress.com/)

- [assets-cdn.github.com](https://github.com.ipaddress.com/assets-cdn.github.com)

- [github.global.ssl.fastly.net](https://fastly.net.ipaddress.com/github.global.ssl.fastly.net)

2. 修改本地系统 hosts 文件

   hosts 文件路径：`C:\Windows\System32\drivers\etc\hosts`

   修改 `hosts` 文件，将查询到的 IP 和 域名写在 `hosts` 文件的最后即可

   ```text
   192.30.253.112    github.com
   185.199.108.153   assets-cdn.github.com
   185.199.109.153   assets-cdn.github.com
   185.199.110.153   assets-cdn.github.com
   185.199.111.153   assets-cdn.github.com
   199.232.5.194     github.global.ssl.fastly.net
   ```

3. 刷新本地系统 dns 缓存

   用 `WIN + R` 快捷键打开运行窗口，输入命令：`cmd` 并回车进入命令行窗口。

   接着输入命令：

   ```
   ipconfig /flushdns 
   ```

   回车后执行刷新本地 dns 缓存数据即可

### 4、高亮粘贴代码

解决方法：可以将代码放到 notepad++ 中，然后依次点击 `插件`->`NppExport`->`Copy RTF to clipboard`，然后将其粘贴至`word`等文件中；

### 5、modelsim窗口恢复

解决方法：在状态栏选择`layout`-->`reset`即可将窗口恢复默认

​                                                     

​                    

**注：所有内容均来源于网络及个人总结，如本文的内容对您的权利造成了影响，请<a href="mailto:1058349718@qq.com">邮件联系</a>，我们会在第一时间将涉及版权的内容进行删除。**

[  完  ]