---
title: Hexo 搭建博客指南
author: yasheng
toc: true
mathjax: false
layout: 
img: /medias/featureimages/hexo.jpg
summary: 本文总结Hexo搭建博客的基本操作
categories: ☁ Hexo
tags:
  - Hexo
abbrlink: 1170186835
date: 2020-03-20 16:25:05
password:
---


## Hexo博客搭建指南

---

注 : 本文内容来自整理ouuan和code004的博客，地址[ouuan原文](https://ouuan.github.io/post/hexo%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8C%97/)、[code004原文](https://theme-materialized.github.io/how-to-build-a-hexo-blog/)、[大佬1博客](https://yafine-blog.cn/posts/8c84.html)、[大佬2博客](https://sunhwee.com/posts/6e8839eb.html)


### 安装软件
需要安装的软件有：   
- [git](https://git-scm.com/downloads)  
- [Node.js](http://nodejs.cn/)。   


直接下载安装即可，安装路径可以根据喜好更改，最好按默认。建议在node.js安装的时候选择左边的绿按钮，稳定版本更好，开发版本可能不稳定

请在cmd中使用 

```
node -v             //查看nodejs版本
npm -v              //查看npm版本
git --version       //查看git版本
```
检查软件是否安装成功。如果成功，应返回一行含有版本号的字符串，而不是“xxx不是内部或外部命令“等。如果node或者npm安装不成功，请重新安装nodejs；如果git不成功，请重新安装git。  


### 本地设置

软件安装成功后后，请从开始菜单打开Git Bash或者在任意位置右键选择Git Bash Here,

<img src="/images/post_images/hexo_blog_building/git.jpg">

在命令窗口中输入

```
npm install -g hexo
```
安装hexo

如果地址被墙，请自行科学上网或者输入

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
切换到淘宝镜像后输入

```
cnpm install -g hexo
```
等待片刻后会有一些提示和警告，可以忽略。完成后请使用hexo -v来检查是否安装成功


&#160; &#160; &#160; &#160;接下来，在任意位置创建一个文件夹，名字任意，作为博客的根目录，(最好不要有一些奇怪的字符)。这个文件夹将用于存储你的blog的所有信息，包括设置/插件/页面的markdown文件/文章的markdown文件，随着文章/页面与插件的数量增加而增加，可以占到50M~1G，请谨慎选好位置，由于文件很多，事后移动耗时很长   

再接下来，在你刚刚创建的文件夹里（后文称为站点目录）右键单击，选择Git Bash Here，输入命令

```
hexo init
```
等待一会，您可以看到您选择的主文件夹里多了这些文件/文件夹：  

- node_modules
- scaffolds
- source
- themes
- _config.yml
- package.json

其中  
node_modules：是用来存放您使用npm下载的插件的     
scaffolds：是文章模板，您稍后创建的文章会按照模板创建，可以对模板进行修改   
source：用来存放您所有文章/页面的markdown代码或者HTML代码   
themes：用来存放您稍后下载的主题文件（默认主题landscape）   
_config.yml：则是用来进行站点配置的，在后文中会被称为站点配置文件    
package.json：用来存放您下载插件的目录，平常不要修改为好    

请接着输入

```
npm install
```
来下载一些需要的包，等待下载完毕后，请打开站点配置文件 _config.yml ，进行基本配置：

```
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/
# Site
title: Wonderful        //站名
subtitle: ''            //副标题
description: ''         //描述
keywords:               //关键字
author: yasheng         //作者
#language: en
language: zh-CN         //语言
timezone: ''
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://wuyasheng.github.io/   //自己的网址github配置的个人域名
root: /
permalink: :year/:month/:day/:title/
```
如上进行部分设置后，请在站点目录中右键Git Bash Here，输入   

```
hexo s
```
打开浏览器，地址栏输入 localhost:4000 即可查看(这里的hexo s 是hexo server的缩写)  
如果4000端口冲突，请使用cmd进行     

```
taskkill /f /im 4000    //或者使用
hexo s -p 4001
```
修改到4001端口  
如果您看到以下界面，说明成功：

<img src="/images/post_images/hexo_blog_building/hexo_demo.jpg">

### 配置 GitHub 仓库

1. 首先你需要有一个 [GitHub](https://github.com/) 账号,在该网站上进行注册登录;     

2. 然后点击右上角你的头像，打开"Your repositories"，点击绿色的按钮"New"。
在"Repository name"一栏填入 yourname.github.io （“yourname”指你的 GitHub ID，比如我就填 yasheng.github.io），“Description"可以随便填也可以不填，然后点绿色的按钮“Create repository”。

3. 配置本地git（在git bash中设置）

  ```
  # 配置用户名
  git config --global user.name "username"    //（ "username"是自己的账户名，）
  # 配置邮箱
  git config --global user.email "username@email.com"     //("username@email.com"注册账号时用的邮箱)
  
  ```

4. 输入命令 ssh-keygen 来生成 SSH，让你输入东西你就空着，按回车（应该要按三次回车）。

5. 然后用任意的文本编辑器打开 C:\Users\电脑用户名\.ssh\id_rsa.pub,复制里面的内容。
6. 打开 GitHub，点击右上角的头像，打开"Settings"，选择左边的"SSH and GPG keys"，点绿色的按钮"New SSH key"，Title 随便填，下面的 Key 把刚才复制的东西粘贴进去，然后点绿色的按钮"Add SSH key"(过程中可能需要输入密码)

7. 输入命令 ssh -T git@github.com ，若出现 "Hi yourname! You've successfully authenticated, but GitHub does not provide shell access"，表示 SSH 配置成功。


### 将博客上传至 GitHub
1. 命令行或者gitBash输入命令 

```
npm install hexo-deployer-git --save 
```

安装 deployer (博客发布插件)

2. 打开根目录下的 _config.yml ，将最后几行改为：

```
deploy:
  type: git
  repository: https://github.com/yourname/yourname.github.io.git
  branch: master
```
当然"yourname"要改成你的 GitHub ID 。
3. 依次输入命令：

```
hexo cl     //删除之前生成的静态文件
hexo g      //重新生成静态文件
hexo d      //将静态文件发布至自己的github上，并启动访问
```

4. 等几分钟，再用浏览器打开 yourname.github.io ,应该就可以看到你的博客了。  

### Git发布问题
在使用hexo d 发布博客时会出现警告：

```
warning: LF will be replaced by CRLF in 文件 ....
```
的问题，原因是存在符号转义问题。windows中的换行符为 CRLF， 而在linux下的换行符为LF，所以在执行命令时会出现出现提示，解决办法是在GitBash输入命令：

```
git config --global core.autocrlf false
```
再重新提交发布   



## 博客美化
---
### 下载主题
&#160; &#160; &#160; &#160;[hexo主题页面](https://hexo.io/themes/)中有许多主题，可以慢慢挑选，当然也可以利用搜索功能。对每个主题，点击预览图片会进入预览网站，点击蓝色的主题名称会进入相应的Github repo。若主题README中无特殊说明，请使用下面命令,可以将主题复制到当前文件夹下；

```
git clone https://github.com/xxx/xxx themes/xxx
```
(xxx请替换为主题名称)

本文使用[闪烁之狐](https://blinkfox.github.io/)主题:[Repositories
](https://github.com/blinkfox/hexo-theme-matery)

### 配置主题
1. 首先将主题复制到根目录下的theme文件夹下  

2. 打开站点配置文件，将themes: landscape改为themes: xxx（xxx为主题在themes下的目录名字，就是代码里的主题名称）

3. 通多阅读主题中的readme.md文件,可以快速了解主题的各种配置，根据自己的需要，进行修改主题配置文件  

比如，我把next主题通过git clone到了themes目录下，就把站点配置文件中的themes选项改为next即可  
在每个主题下都有一个_config.yml文件，它被称为主题配置文件，用来对主题进行自定义设置（注意，主题的语言设置在站点配置文件里）
### 添加live2d卡通人物


&#160; &#160; &#160; &#160;实现的效果是，博客页面的左、右下角的会出现一个live2d卡通人物，而且她还会眨眼睛，头会随着鼠标的移动而转动。  

1. 安装hexo-helper-live2d

```
npm install --save hexo-helper-live2d
```
2. 安装live2d

```
npm install live2d-widget-model
```
其中 live2d-widget-model 替换成想要的卡通模型，比如我安装的的是live2d-widget-model-shizuku  
当然，还有很多的model可供选择，参考[live2d-widget-models](https://github.com/xiazeyu/live2d-widget-models)

3. 根据[hexo-helper-live2d](https://github.com/EYHN/hexo-helper-live2d)中的描述，在Hexo站点配置文件或者主题配置文件_config.yml中添加如下配置

```
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  tagMode: false
  log: false
  model:
    use: live2d-widget-model-wanko      //修改为想要添加的模型名称
  display:
    position: right
    width: 150
    height: 300
  mobile:
    show: true
  react:
    opacity: 0.7
```

重新hexo d -g即可看到效果

## 博客的写作
---
### Markdown编辑器
&#160; &#160; &#160; &#160;博客的写作主要是使用Markdown文件，网上有很多 Markdown 的学习资源，ouuan大佬通过洛谷剪贴板学会的。一般推荐 Typora 这款软件编写md文件，比较方便。

### 撰写前的准备
&#160; &#160; &#160; &#160;打开根目录下的 _config.yml ，将 post_asset_folder 设为 true。
这样就可以把图片放到博客里而不用其它图床了。

### 博文的撰写
新建一篇博客：

```
hexo new "博文标题"         //或者
hexo n "博文标题"
```
&#160; &#160; &#160; &#160;然后等几秒钟，在 \source\_posts 文件夹下，就会生成 博客名 这个文件夹（如果你把 post_asset_folder 设为 true 了）以及 博客名.md。  

&#160; &#160; &#160; &#160;撰写博客就是编辑 博客名.md。这个文件的开头是博客的一些设置，可以在 \scaffolds\posts.md 中修改默认设置(博文模板)，我的默认设置是：

```
title: {{ title }}
date: {{ date }}
tags: 
top: 
---
```

### 博文模板
&#160; &#160; &#160; &#160;更多博文模板在 \scaffolds 文件夹中可以增加，比如ouuan就搞了一个 \scaffolds\tutorial.md，这样的话，新建一篇题解的时候输入命令

```
hexo new tutorial "博文标题" 
```
就可以使用模板了。

### 图片引用
#### 引用本地图片
1. 把主页配置文件_config.yml 里的post_asset_folder:这个选项设置为true
2. 安装插件此版本可以正确显示图片，可能会有一些问题

``` nodejs
npm install https://github.com/CodeFalling/hexo-asset-image --save
```
3. 等待一小段时间后，再运行hexo n "xxxx"来生成md博文时，/source/_posts文件夹内除了xxxx.md文件还有一个同名的文件夹
4. 最后在xxxx.md中想引入图片时，先把图片复制到xxxx这个文件夹中，然后只需要在xxxx.md中按照markdown的格式引入图片：

```
![你想输入的替代文字](xxxx/图片名.jpg)
```
或者使用绝对路径(无需插件，图片放置在主题文件下的source中)(最好的方法)

```
<img src="/images/material-3.png">
<img src="/images/material-4.png">
<img src="/images/material-5.png">
```

#### 引用网络链接与图片
与普通 Markdown 相同

```
[链接名称](链接地址)        //链接引用
![图片描述](图片地址)       //图片引用
```

#### 引用Gif动图和视频

在文章中插入Gif动图和视频

```
<iframe height=100 width=100 src="视频地址">      //插入视频
<iframe height=100 width=100 src="gif 图片地址">  //插入Gif动图
```

---
博客的发布
其实前文提到过，依次输入以下三条命令即可：

```
hexo cl     //清理
hexo g      //生成
hexo d      //发布
```
hexo cl 是可选的。加上不会有坏处,而且有时候必须加上。  
发布之前还可以执行 hexo s 并在本地使用浏览器打开 localhost:4000 进行预览。

<!--more-->

## Hexo 问题及优化

---

### Hexo  添加 404 自定义页面

原来的`Matery`主题没有`404`页面，我们加一个。首先在`/source/`目录下新建一个`404.md`，内容如下：

```markdown
---
title: 404
date: 2019-08-5 16:41:10
type: "404"
layout: "404"
description: "Oops～，我崩溃了！找不到你想要的页面 :("
---
```

然后在`/themes/matery/layout/`目录下新建一个`404.ejs`文件，内容如下：

```
<style type="text/css">
    /* don't remove. */
    .about-cover {
        height: 75vh;
    }
</style>
<div class="bg-cover pd-header about-cover">
    <div class="container">
        <div class="row">
            <div class="col s10 offset-s1 m8 offset-m2 l8 offset-l2">
                <div class="brand">
                    <div class="title center-align">
                        404
                    </div>
                    <div class="description center-align">
                        <%= page.description %>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
<script>
    $('.bg-cover').css('background-image', 'url(/medias/banner/' + new Date().getDay() + '.jpg)');
</script>
// 每天切换 banner 图.  Switch banner image every day.
```



### Hexo  标签名大小写问题

在 tags 标签出现大小写的时候会出现，标签点击出现404的情况，解决方法[参考](https://blog.csdn.net/weixin_43219615/article/details/102536642)

将博客根目录下的.deploy_git.git\config文件改一下（.git是隐藏文件夹，记得调一下显示隐藏文件夹 ），默认的忽略大小写 ignorecase 改成 false。

### Hexo  实现懒加载

​		图片懒加载是提升网站性能和用户体验的一个非常很好方式，并且几乎所有的大型网站都使用到了，比如微博，仅把用户可见的部分显示图片，其余的都暂时不加载，做法就是：让所有图片元素src指向一个小的站位图片比如loading，并新增一个属性(如data-original)存放真实图片地址。每当页面加载（或者滚动条滚动），使用JS脚本将可视区域内的图片src替换回真实地址，并做请求重新加载。

懒加载的实现主要借助于 Hexo-lazy-image 的使用，安装步骤：

```undefined
npm install hexo-lazyload-image --save	//在bolg目录 Git Bash下安装
```

然后修改 blog 根目录的`_config.yml` 文件，添加如下代码

```bash
lazyload:
  enable: true 
  onlypost: false
  loadingImg: # eg. ./images/loading.png 
```

### 懒加载-优化

**问题1**：查看大图，发现全部为loading加载图，原因是因为懒加载插件与**lightgallery插件**冲突，解决办法如下：

修改主题文件下的**matery.js**，在108行左右添加以下代码：

```
$(document).find('img[data-original]').each(function(){
    $(this).parent().attr("href", $(this).attr("data-original"));
});
```

**问题2**：其实第一次加载后本地都是有缓存的，如果每次都把loading显示出来就不那么好看

打开 `Hexo根目录`>`node_modules` > `hexo-lazyload-image` > `lib` > `simple-lazyload.js` 文件

第9行修改为：

```
&& rect.top <= (window.innerHeight +240 || document.documentElement.clientHeight +240)
```

作用：提前240个像素加载图片；当然这个值也可以根据自己情况修改

### 添加加载动画

添加网页加载动画，首先在站点根目录下新建一个文件夹，名为**scripts**，紧接着在新建的文件夹下新建一个名为**loading-pages.js******文件，然后再这个**js**文件填入下面的代码：

```
/* global hexo */
"use strict";
hexo.extend.filter.register('after_render:html', function (htmlContent) {
     const injectHead =
 `<style type="text/css" lang="css">
    #loading-container{
        position: fixed;
        top: 0;
        left: 0;
        min-height: 100vh;
        width: 100vw;
        z-index: 9999;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background: #FFF;
        text-align: center;
        /* loader页面消失采用渐隐的方式*/
        -webkit-transition: opacity 1s ease;
        -moz-transition: opacity 1s ease;
        -o-transition: opacity 1s ease;
        transition: opacity 1s ease;
    }
    .loading-image{
        width: 120px;
        height: 50px;
        transform: translate(-50%);
    }

    .loading-image div:nth-child(2) {
        -webkit-animation: pacman-balls 1s linear 0s infinite;
        animation: pacman-balls 1s linear 0s infinite
    }

    .loading-image div:nth-child(3) {
        -webkit-animation: pacman-balls 1s linear .33s infinite;
        animation: pacman-balls 1s linear .33s infinite
    }

    .loading-image div:nth-child(4) {
        -webkit-animation: pacman-balls 1s linear .66s infinite;
        animation: pacman-balls 1s linear .66s infinite
    }

    .loading-image div:nth-child(5) {
        -webkit-animation: pacman-balls 1s linear .99s infinite;
        animation: pacman-balls 1s linear .99s infinite
    }

   .loading-image div:first-of-type {
        width: 0;
        height: 0;
        border: 25px solid #49b1f5;
        border-right-color: transparent;
        border-radius: 25px;
        -webkit-animation: rotate_pacman_half_up .5s 0s infinite;
        animation: rotate_pacman_half_up .5s 0s infinite;
    }
    .loading-image div:nth-child(2) {
        width: 0;
        height: 0;
        border: 25px solid #49b1f5;
        border-right-color: transparent;
        border-radius: 25px;
        -webkit-animation: rotate_pacman_half_down .5s 0s infinite;
        animation: rotate_pacman_half_down .5s 0s infinite;
        margin-top: -50px;
    }
    @-webkit-keyframes rotate_pacman_half_up {0% {transform: rotate(270deg)}50% {transform: rotate(1turn)}to {transform: rotate(270deg)}}

    @keyframes rotate_pacman_half_up {0% {transform: rotate(270deg)}50% {transform: rotate(1turn)}to {transform: rotate(270deg)}}

    @-webkit-keyframes rotate_pacman_half_down {0% {transform: rotate(90deg)}50% {transform: rotate(0deg)}to {transform: rotate(90deg)}}

    @keyframes rotate_pacman_half_down {0% {transform: rotate(90deg)}50% {transform: rotate(0deg)}to {transform: rotate(90deg)}}

    @-webkit-keyframes pacman-balls {75% {opacity: .7}to {transform: translate(-100px, -6.25px)}}

    @keyframes pacman-balls {75% {opacity: .7}to {transform: translate(-100px, -6.25px)}}


    .loading-image div:nth-child(3),
    .loading-image div:nth-child(4),
    .loading-image div:nth-child(5),
    .loading-image div:nth-child(6){
        background-color: #49b1f5;
        width: 15px;
        height: 15px;
        border-radius: 100%;
        margin: 2px;
        width: 10px;
        height: 10px;
        position: absolute;
        transform: translateY(-6.25px);
        top: 25px;
        left: 100px;
    }
    .loading-text{
        margin-bottom: 20vh;
        text-align: center;
        color: #2c3e50;
        font-size: 2rem;
        box-sizing: border-box;
        padding: 0 10px;
        text-shadow: 0 2px 10px rgba(0,0,0,0.2);
    }
    @media only screen and (max-width: 500px) {
         .loading-text{
            font-size: 1.5rem;
         }
    }
    .fadeout {
        opacity: 0;
        filter: alpha(opacity=0);
    }
    /* logo出现动画 */
    @-webkit-keyframes fadeInDown{0%{opacity:0;-webkit-transform:translate3d(0,-100%,0);transform:translate3d(0,-100%,0)}100%{opacity:1;-webkit-transform:none;transform:none}}
    @keyframes fadeInDown{0%{opacity:0;-webkit-transform:translate3d(0,-100%,0);}}
 </style>
 <script>
(function () {
    const loaded = function(){
       setTimeout(function(){
            const loader = document.getElementById("loading-container");
            loader.className="fadeout" ;//使用渐隐的方法淡出loading page
            // document.getElementById("body-wrap").style.display="flex";
            setTimeout(function(){
                loader.style.display="none";
            },1000); 
        },1000);//强制显示loading page 1s  
    };
    loaded();
})()
 </script>`;
     const injectBody = `
 <div id="loading-container">
     <p class="loading-text">玩命加载中 . . . </p> 
     <div class="loading-image">
         <div></div>
         <div></div>
         <div></div>
         <div></div> 
         <div></div>
     </div>
 </div>`;
     if (/<\/head>/gi.test(htmlContent)) {
         let lastIndex = htmlContent.lastIndexOf('</head>');
         htmlContent = htmlContent.substring(0, lastIndex) + injectHead + htmlContent.substring(lastIndex, htmlContent.length);
     }
     if (/<body>/gi.test(htmlContent)) {
         let index = htmlContent.indexOf('<body>');
         htmlContent = htmlContent.substring(0, index) + injectBody + htmlContent.substring(index, htmlContent.length);
     }
     return htmlContent;
 }, 1);
```



### 添加搞笑动态标题

直接在`themes/matery/layout/layout.ejs`文件中添加如下代码：

```
<script type="text/javascript">
    var OriginTitile=document.title,st;
    document.addEventListener("visibilitychange",function(){
        document.hidden?(document.title="ヽ(●-`Д´-)ノ你要玩捉迷藏嘛",clearTimeout(st)):(document.title="(Ő∀Ő3)ノ好哦！",st=setTimeout(function(){document.title=OriginTitile},3e3))
    })
</script>
```



### 添加鼠标点击烟花爆炸效果

在 /themes/matery/source/js 新建文件 fireworks.js，并添加如下代码

```javascript
"use strict";
function updateCoords(e){
	pointerX=(e.clientX||e.touches[0].clientX)
	-canvasEl.getBoundingClientRect().left,
	pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top
	}function setParticuleDirection(e){
		var t=anime.random(0,360)*Math.PI/180,
		a=anime.random(50,180),
		n=[-1,1][anime.random(0,1)]*a;
		return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}
		}function createParticule(e,t){
			var a={};return a.x=e,
			a.y=t,
			a.color=colors[anime.random(0,colors.length-1)],
			a.radius=anime.random(16,32),
			a.endPos=setParticuleDirection(a),
			a.draw=function(){
				ctx.beginPath(),
				ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),
				ctx.fillStyle=a.color,ctx.fill()},a
		}function createCircle(e,t){
			var a={};
			return a.x=e,
			a.y=t,
			a.color="#F00",a.radius=0.1,
			a.alpha=0.5,
			a.lineWidth=6,
			a.draw=function(){
				ctx.globalAlpha=a.alpha,
				ctx.beginPath(),
				ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),
				ctx.lineWidth=a.lineWidth,
				ctx.strokeStyle=a.color,ctx.stroke(),
				ctx.globalAlpha=1},a
				}function renderParticule(e){
					for(var t=0;t<e.animatables.length;t++){
						e.animatables[t].target.draw()}
				}function animateParticules(e,t){
					for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){
						n.push(createParticule(e,t))
					}anime.timeline().add({
						targets:n,
						x:function(e){
							return e.endPos.x
						},y:function(e){
							return e.endPos.y
						},radius:0.1,
						duration:anime.random(1200,1800),
						easing:"easeOutExpo",
						update:renderParticule}).add({targets:a,
						radius:anime.random(80,160),
						lineWidth:0,
						alpha:{value:0,easing:"linear",duration:anime.random(600,800)},
						duration:anime.random(1200,1800),
						easing:"easeOutExpo",
						update:renderParticule,offset:0})
				}function debounce(e,t){
					var a;
					return function(){
						var n=this,
						i=arguments;
						clearTimeout(a),
						a=setTimeout(function(){e.apply(n,i)},t)}
				}var canvasEl=document.querySelector(".fireworks");
				if(canvasEl){var ctx=canvasEl.getContext("2d"),
				numberOfParticules=30,
				pointerX=0,pointerY=0,
				tap="mousedown",
				colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],
				setCanvasSize=debounce(function(){
					canvasEl.width=2*window.innerWidth,
					canvasEl.height=2*window.innerHeight,
					canvasEl.style.width=window.innerWidth+"px",
					canvasEl.style.height=window.innerHeight+"px",
					canvasEl.getContext("2d").scale(2,2)},500),
					render=anime({duration:1/0,update:function(){
						ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});
					document.addEventListener(tap,function(e){
						"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),
						updateCoords(e),animateParticules(pointerX,pointerY))},!1),
						setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)}"use strict";
					function updateCoords(e){
						pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,
						pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){
						var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),
						n=[-1,1][anime.random(0,1)]*a;
						return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};
						return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],
						a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),
						a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),
						ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){
							var a={};return a.x=e,a.y=t,a.color="#F00",
							a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){
								ctx.globalAlpha=a.alpha,ctx.beginPath(),
								ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),
								ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,
								ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){
									for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}
								}function animateParticules(e,t){
									for(var a=createCircle(e,t),n=[],i=0;
									i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},
									y:function(e){return e.endPos.y},
									radius:0.1,
									duration:anime.random(1200,1800),
									easing:"easeOutExpo",
									update:renderParticule}).add({targets:a,radius:anime.random(80,160),
									lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},
									duration:anime.random(1200,1800),
									easing:"easeOutExpo",
									update:renderParticule,
									offset:0})}function debounce(e,t){
										var a;
										return function(){
											var n=this,
											i=arguments;
											clearTimeout(a),
											a=setTimeout(function(){
												e.apply(n,i)},t)}
											}var canvasEl=document.querySelector(".fireworks");
											if(canvasEl){var ctx=canvasEl.getContext("2d"),
											numberOfParticules=30,
											pointerX=0,
											pointerY=0,
											tap="mousedown",
											colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],
											setCanvasSize=debounce(function(){
												canvasEl.width=2*window.innerWidth,
												canvasEl.height=2*window.innerHeight,
												canvasEl.style.width=window.innerWidth+"px",
												canvasEl.style.height=window.innerHeight+"px",
												canvasEl.getContext("2d").scale(2,2)},500),
												render=anime({duration:1/0,
												update:function(){
													ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});
													document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),
													updateCoords(e),
													animateParticules(pointerX,pointerY))},!1),
													setCanvasSize(),
													window.addEventListener("resize",setCanvasSize,!1)};
```

然后在 /themes/matery/layout/_partial/footer.ejs 中添加如下代码：

```js
<% if (theme.fireworks.enable) { %>
<canvas class="fireworks" style="position: fixed; left: 0; top: 0; z-index: 1; pointer-events: none;" ></canvas>
<script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script>
<script type="text/javascript" src="/js/fireworks.js"></script>
<% } %>

```

在主题配置文件 .yml中配置:

```yml
# 鼠标点击烟花爆炸动效
fireworks:
  enable: true
```



### Hexo url优化

​		网站的最佳结构是**用户从首页点击三次就可以到达任何一个页面**，但是我们使用`hexo`编译的站点打开文章的`url`是：`sitename/year/mounth/day/title`四层的结构，这样的`url`结构很不利于`seo`，爬虫就会经常爬不到我们的文章，于是，我们需要优化一下网站文章`url`，通过修改根目录配置文件

```yaml
#permalink: :year/:month/:day/:title/
#将上文的形式修改成下面的形式，文章的名称最好是英文的，
#网址就会变成`sitename/posts/title`
permalink: posts/:title/
```

注：也可以使用`hexo-abbrlink`，将路径变成数字，但是会与`hexo-asset-image`冲突，出现图片加载不出来的问题，查阅好多无法解决，所以放弃了

### Hexo 源文件备份

原文1链接：https://blog.csdn.net/qq_36759224/article/details/101702153

原文2链接：https://blog.csdn.net/qq_41793001/article/details/103151182

由于 Hexo 博客是静态托管的，所有的原始数据都保存在本地，如果哪一天电脑坏了，或者是误删了本地数据，没有办法恢复，此时定时备份就显得比较重要了，常见的备份方法有：打包数据保存到U盘、云盘或者其他地方，但是早就有大神开发了备份插件：hexo-git-backup ，只需要一个命令就可以将所有数据包括主题文件备份到 github 了

**安装备份插件**

首先进入你博客目录，输入命令 hexo version 查看 Hexo 版本

如果你的 Hexo 版本是 2.x.x，则使用以下命令安装：

```
npm install hexo-git-backup@0.0.91 --save
```


如果你的 Hexo 版本是 3.x.x以上，则使用以下命令安装：

```
npm install hexo-git-backup --save
```

**建立分支**

来到你的github仓库目录，选择新建分支，我创建的分支名为hexo

<img src="/images/post_images/hexo_blog_building/hexo_backup.jpg">

**配置备份设置**

到 Hexo 博客根目录的 _config.yml 配置文件里添加以下配置：

```
backup:
  type: git
  repository:
    github: git@github.com:XXX/XXX.github.io.git,branchName
    #coding: git@git.dev.tencent.com:XXX/XXX.git,branchName
```


其中 repository：仓库名，注意仓库地址后面要添加一个分支名，branchName为分支名

如果你只是想备份主题目录，只需要添加theme属性

```
backup:
  type: git
  theme: coney,landscape,xxx
  repository:
    github: git@github.com:XXX/XXX.github.io.git,branchName
    #coding: git@git.dev.tencent.com:XXX/XXX.git,branchName
```

**备份源文件**

这里就差不多配置完成了，使用以下任一命令，即可备份所有文件（不包括public文件夹）


```
hexo backup 
hexo b
```

​         

### Hexo 添加Aplayer播放器

#### 创建歌单页面

由于我想在单独的页面加入歌单，所以额外创了个页面，也可以直接在文章中插入，原理都是一样的。

1. 新建页面，命名为playlist：

   

   ```cpp
   hexo new page playlist
   ```

2. 这时候在 /Hexo/source 文件夹下会生成一个playlist文件夹，打开里面的index.md，修改如下：

   

   ```bash
   title: 歌单
   date: 2019-02-21 16:14:00
   type: "playlist"
   ```

3. 打开主题的 _config.yml文件，在menu下新建一个名为playlist的类（注意这里使用的图标是图标库中的图标，网址为 [http://www.fontawesome.com.cn/faicons/](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.fontawesome.com.cn%2Ffaicons%2F) 。可以选择自己喜欢的图标，我这里选择的是music）。完成后如下所示：

   

   ```ruby
   menu:
     home: / || home
     categories: /categories/ || th
     tags: /tags/ || tags
     archives: /archives/ || archive
     playlist: /playlist/ || music
     about: /about/ || user
   ```

4. 打开/Hexo/themes/hexo-theme-next/languages/zh-Hans.yml，添加对应的中文翻译：

   

   ```undefined
   menu:
     playlist: 歌单
   ```

这样歌单就创建完成啦~

#### 使用 hexo-tag-aplayer 插件

1. hexo-tag-aplayer 是Aplayer在hexo上的插件，这里的配置参考的是[官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FMoePlayer%2Fhexo-tag-aplayer%2Fblob%2Fmaster%2Fdocs%2FREADME-zh_cn.md) ，第一步安装 hexo-tag-aplayer：

   

   ```undefined
   npm install --save hexo-tag-aplayer
   ```

2. 最新版的 hexo-tag-aplayer 已经支持了MetingJS的使用，可以直接解析网络平台的歌曲（简直是神器），首先要在站点配置文件中开启meting模式，添加以下代码在配置文件的最后：

   

   ```bash
   aplayer:
     meting: true
   ```

3. 复制歌单的链接，然后复制歌单的id，例如 [https://music.163.com/playlist?id=523845661&userid=46562117](https://links.jianshu.com/go?to=https%3A%2F%2Fmusic.163.com%2Fplaylist%3Fid%3D523845661%26userid%3D46562117) ，这个歌单的id就是523845661，公司名可以是tencent、netease或是其他公司，下面给出一个例子，打开 /Hexo/source/playlist/[index.md](https://links.jianshu.com/go?to=http%3A%2F%2Findex.md)文件，输入：

```bash
{% meting "523845661" "netease" "playlist" "theme:#FF4081" "mode:circulation" "mutex:true" "listmaxheight:340px" "preload:auto" %}
```

效果很不错



作者：shenghaishxt
链接：https://www.jianshu.com/p/f1005ae09e5a
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





​      

[完]




