---
title: Nodejs+Express简易服务器
author: yasheng
img: /medias/featureimages/nodejs.jpg
toc: true
mathjax: false
layout: 
summary: 利用Nodejs搭建建议服务器
categories: Nodejs
tags:
  - Nodejs
  - 服务器
  - Express
abbrlink: 1127848599
date: 2020-04-26 09:32:09
password:
---



## Nodejs+Express搭建服务器

------

注：本文整理来自[参考文1](https://www.cnblogs.com/wgl0126/p/9290157.html)，[参考文2](https://www.cnblogs.com/rope/p/10726134.html)文章整理总结

### Nodejs安装

1. 首先下载Nodejs安装包，[Nodejs下载](http://nodejs.cn/download/)，安装Nodejs
2. 安装完成后，`win+r`，输入`cmd`回车，打开命令行，输入`node -v`，查看Nodejs版本号
3. 在系统环境变量中添加 Nodejs 路径，（安装时默认添加）

### Nodejs新建项目

1. 创建服务器文件夹
2. 在命令行中打开新建的文件夹
3. 初始化项目，输入`npm init`
4. 安装express，输入`npm install -g express-generator`
5. 使用express创建服务器文件夹，输入`express -e express_name`
6. 打开新建的服务器文件夹
7. 下载安装相关的依赖，输入`npm install`

下图是创建好的服务器文件夹目录

<img src="/images/post_images/nodejs_building/nodejs_01.png">

- **node_modules文件夹**

 这文件夹就是在创建完项目后，cd到项目目录执行npm install后生成的文件夹，下载了项目需要的依赖项

- **package.json文件**

此文件是项目的配置文件（可定义应用程序名，版本，依赖项等等）

node_modules文件夹下的依赖项是从哪里知道的呢？原因就是项目根目录下的这个package.json文件，执行npm install时会去找此文件中的dependencies，并安装指定的依赖项

- **public文件夹(包含images、javascripts、stylesheets)**

这个文件夹做过Web开发的应该一看就知道，为了存放图片、脚本、样式等文件的

- **routes文件夹**

用于存放路由文件，

- **views文件夹**

用于存放模版文件

### nodejs服务器配置

下载相关依赖(下载各种需要的包)

```
npm install mysql --save
```

启动nodejs服务器

```
npm start
```

浏览器网址输入，打开服务器首页

```
localhost:3000
```

<img src="/images/post_images/nodejs_building/nodejs_03.png">

app.js是整个工程真正的入口文件。在其内部，加载主要的依赖包，配置中间件，加载路由等等。最后在www文件中启动服务。

<img src="/images/post_images/nodejs_building/nodejs_02.png">

在服务器中添加页面主要有三个步骤

1. 编写路由文件 在 routes 路径之下

2. 编写路由文件返回的界面内容

3. 在app.js当中，添加路由文件路径以及注册路由

使用以上内容，即可搭建简易Nodejs服务器     

​      

[  完  ]







