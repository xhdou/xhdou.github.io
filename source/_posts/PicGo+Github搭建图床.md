---
title: PicGo+Github搭建图床
date: 2023-03-06 18:25:00
author: 小派派
img: https://raw.githubusercontent.com/xhdou/image/main/PicGo20230306181459.png
top: false
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/xhdou/image/main/PicGo20230306181459.png
toc: true
mathjax: false
categories: 计算机
tags:
  - PicGo
  - 图床
  - 工具
---
##  一、注册一个Github账号
实在是太基础了，自己随便上网找一个[教程](https://blog.csdn.net/weixin_51674304/article/details/121525251)。
##  二、配置Github
1. 创建一个新仓库，用于存放图片。
![](https://raw.githubusercontent.com/xhdou/image/main/20230307102314.png)
1. 生成一个`token`，用于`picGo`访问`github`。选择左侧`Personal access tokens`，再点击`Generate new token`
![](https://raw.githubusercontent.com/xhdou/image/main/20230307101249.png)
1. 填写`Note`，勾选`repo`（我全选了）。
![](https://raw.githubusercontent.com/xhdou/image/main/20230307101637.png)
1. 注意，生成的`token`只会在这里显示一次，所以记得单独保存。
![](https://raw.githubusercontent.com/xhdou/image/main/20230307101807.png)

## 三、PicGo下载及配置

[PicGo](https://github.com/Molunerfinn/PicGo)是`Github`上的一个开源项目，支持多个图床平台。下载及安装看作者文档即可。

本人用的`vscode`写`Markdown`，因此用的`vscode`中的`PicGo`插件，当然也可以下载客户端使用，以下为两种方式的教程。

### （一）vscode中PicGo配置
首先需要下载安装`PicGo`插件，设置如下：
![](https://raw.githubusercontent.com/xhdou/image/main/1678156617346.jpg)

### （二）客户端PicGo配置
客户端配置操作其实差不多，可以参考[PicGo + GitHub 搭建个人图床工具](https://blog.csdn.net/yefcion/article/details/88412025)。

