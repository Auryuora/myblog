---
title: 用hexo与netlify搭建个人博客
date: 2026-04-03 08:51:40
categories: 网安
tags: 博客
---

开始学习web方向，第一步就是学习搭建个人博客。我认为个人博客是的作用是记录你的学习过程，相当于做笔记。在写博客时，你对知识点的总结归纳能力也会大大增强，这对学习来说十分重要。

## 环境配置

### 安装nodejs
首先进入nodejs官网下载安装包，我是window系统，选择win版本的压缩包下载。下载后最好在C盘解压。
解压完后进入终端，检测是否成功。
``` bash
$ node -v
```

此时我们会发现不可执行的提示，那是因为环境没有配置好。
进入更高级设置 -> 环境变量 -> 系统环境 -> path -> 新建,将解压后nodejs的路经放入。
``` bash
#再次检测，若两个都出现版本，则安装成功
$ node -v
$ npm -v
```

npm的官网源比较慢，通过以下测试可以加快下载过程
我一开始用的是淘宝源https://registry.npm.taobao.org ，后来发现过期了，就换了下面这个
``` bash
$ npm config get registry  #查看原来的源
$ npm config set registry https://registry.npmmirror.com  #修改源
$ npm config get registry  #查看现在的源
```

## 本地部署
### 安装hexo
``` bash
$ npm install hexo-cli -g # 全局安装hexo命令行工具
```

进入用户目录,如用户名为xiaozhang
``` bash
$ cd C:\Users\xiaozhang
```

接下来初始化
``` bash
$ hexo init "你的博客目录名称" # 目录名称不含空格的时候双引号可以省略
```

进入博客目录
``` bash
$ cd "博客目录"
```

### 安装依赖
``` bash
$ npm install
```

### 安装Fluid主题
也可以去hexo官网的主题中寻找，下载安装包后
``` bash
$ npm install hexo-theme-fluid --save
```

配置主题
``` bash
$ notepad _config.yml
```
把 theme: landscape 改成 theme: fluid 保存
{% asset_img fluid.png %}

### 本地测试
``` bash
$ nhexo generate
$ hexo server
```
根据给出的网址进行访问http://localhost:4000 ，确认博客正常
{% asset_img hexos.png %}



## 推送到GitHub

### 在 GitHub 创建新仓库
访问 https://github.com ，找到仓库repository点击new，在Repository name里输入新仓库的名字，点击 Create repository

### 推送新博客
``` bash
$ cd C:\Users\chenjiayu\myblog

#初始化Git
$ git init
$ git add .
$ git commit -m "first commit"

# 添加远程仓库，仓库地址+.git
$ git remote add origin https://github.com/Auryuora/myblog.git

# 推送
$ git branch -M main  #要与本地分支一致
$ git push -u origin main --force
```

如果本地分支是master，将main改为master
查看当前分支
``` bash
$ git branch
```

## Netlify建站
Netlify是一个国外的免费的提供静态网站部署服务的平台，能够将托管GitHub上的静态网站部署上线

首先注册并登陆Netlify
新建站点
{% asset_img SJvGYlIzZVm7iP5.png %}

连接github
{% asset_img connect.png %}

选择刚刚上传的博客项目
{% asset_img choose.png %}

一切默认，除了构建命令改成我们之前设置的npm run netlify
{% asset_img change.png %}

构建完成后我们就能够看到一个URL，打开网址就是我们的个人博客了
{% asset_img open.png %}

至此，我们的个人博客搭建完成。
我首次搭建时，也经常漏掉一些细节导致搭建失败，之后又不断的修改，甚至换过方法（使用github自带的gitpage）。可能是因为在通过ai咨询改错的同时又加入了新的错误，让我一直不知道问题出在哪里。当我发现问题找不到时，我选择了放弃这个问题，于是重新建一个博客，建立新的博客目录，建立新的仓库、新的站点，一切推翻重来时，问题都迎刃而解了。就像电脑出现无法修复的bug时，重启能解决90%的问题。

## 写第一篇博客

在写博客之前，我们想要自己的页面更加个性化，便可以在_config.fluid.yml中切换背景图和标题
``` bash
navbar:
  blog_title: Auryuora          # 主标题 a

slogan:
  text: "穿越万千残酷的大海,如太阳一般灿烂的船"    #副标题、座右铭 b

index:
  banner_img: https://图片地址.jpg              #首页背景图 c

post:
  banner_img: https://图片地址.jpg              #文章页背景图 d
```
{% asset_img title.png %}
{% asset_img title2.png %}

再打开_config.yml，找到Site部分进行以下修改
``` bash
title: Auryuora-blog
subtitle: ''
description: ''
keywords:
author: Auryuora
language: zh-CN             #语言
timezone: 'Asia/Shanghai'   #时间设置
```

URL部分的网址改成个人博客的公开网址
``` bash
url: https://celadon-jelly-d6c1a9.netlify.app/
```

则呈现下面效果

