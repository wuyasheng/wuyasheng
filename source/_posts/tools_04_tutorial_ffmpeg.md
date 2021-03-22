---
title: FFmpeg 使用方法
author: yasheng
img: /medias/featureimages/ffmpeg.jpg
toc: true
mathjax: false
summary: 本文介绍FFmpeg使用方法
categories: Tools
tags:
  - FFmpeg
abbrlink: 380386778
date: 2021-02-08 15:07:09
layout:
password:
---

## FFmpeg 使用方法

---

注：本文内容复制整理至网络

### 1 FFmpeg 安装

首先访问FFmpeg 官网，[下载页面链接](http://www.ffmpeg.org/download.html) ，根据提示进行下载

根据自己电脑系统，进行选择下载，这里选择windows，点击田字图案，下拉页面，如图所示

<img src="/images/post_images/tools_04_tutorial_ffmpeg/ffmpeg_01.png">

跳转新页面后，下拉至如图所示，下载如图版本即可，下载完成后是一个压缩包

<img src="/images/post_images/tools_04_tutorial_ffmpeg/ffmpeg_02.png">

下载完成后，将压缩包解压到自己想要放置的地方，即可；最后将该文件夹的bin目录添加到环境变量当中去。添加环境变量方法自行百度。

添加完成后，在命令提示行中输入

```
ffmpeg  -version
```

出现版本号，表示安装成功

<img src="/images/post_images/tools_04_tutorial_ffmpeg/ffmpeg_03.png">

### 2 FFmpeg 使用

FFmpeg 的命令行参数非常多，可以分成五个部分。

```
ffmpeg {1} {2} -i {3} {4} {5}
```

上面命令中，五个部分的参数依次如下。

1. 全局参数
2. 输入文件参数
3. 输入文件
4. 输出文件参数
5. 输出文件

#### 2.1 格式转换

```
ffmpeg -i input.mp4 -c copy output.mkv
```

批量转换(将ts文件批量转mp4)

```
for %%a in ("*.ts") do ffmpeg -i "%%a" -f mp4 -codec copy "%%~na.mp4
pause
```

#### 2.2 调整分辨率

```
## 转成480p
ffmpeg \
-i input.mp4 \
-vf scale=480:-1 \
output.mp4
```

#### 2.3 提取音频

```
$ ffmpeg \
-i input.mp4 \
-vn -c:a copy \
output.aac
## 上面例子中，-vn表示去掉视频，-c:a copy表示不改变音频编码，直接拷贝。
```

#### 2.4 截图

下面的例子是从指定时间开始，连续对1秒钟的视频进行截图。

```bash
ffmpeg \
-y \
-i input.mp4 \
-ss 00:01:24 -t 00:00:01 \
output_%3d.jpg
```

如果只需要截一张图，可以指定只截取一帧。

```bash
 $ ffmpeg \
 -ss 01:23:45 \
 -i input \
 -vframes 1 -q:v 2 \
 output.jpg
```

上面例子中，`-vframes 1`指定只截取一帧，`-q:v 2`表示输出的图片质量，一般是1到5之间（1 为质量最高）。



#### 2.5 裁剪

裁剪（cutting）指的是，截取原始视频里面的一个片段，输出为一个新视频。可以指定开始时间（start）和持续时间（duration），也可以指定结束时间（end）。

```bash
ffmpeg -ss [start] -i [input] -t [duration] -c copy [output]
ffmpeg -ss [start] -i [input] -to [end] -c copy [output]
```

下面是实际的例子。

```bash
$ ffmpeg -ss 00:01:50 -i [input] -t 10.5 -c copy [output]
$ ffmpeg -ss 2.5 -i [input] -to 10 -c copy [output]
```

上面例子中，`-c copy`表示不改变音频和视频的编码格式，直接拷贝，这样会快很多。

​                

 待补充....

​                

​                    

[  完  ]