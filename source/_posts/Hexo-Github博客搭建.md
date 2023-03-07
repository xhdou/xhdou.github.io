---
title: Hexo+Github博客搭建
date: 2023-03-03 09:25:00
author: 小派派
img: https://raw.githubusercontent.com/xhdou/image/main/PicGoCover.png
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/xhdou/image/main/PicGoCover.png
toc: true
mathjax: false
categories: 计算机
tags:
  - Hexo
  - 博客
---
## 一、准备工作
1. `GitHub`账号  
需要有一个`GitHub`账号，没有的话到官网申请一个。  
注册很简单，网上教程很多，可以参考[GitHub申请账号](https://blog.csdn.net/yaorongke/article/details/119086305)。

2. 安装`Git`  
在自己电脑上安装好`Git`，`hexo`部署到`GitHub`时要用。  
网上找篇教程或者参考[Git安装(Windows)](https://blog.csdn.net/yaorongke/article/details/119085413)。

3. 安装`NodeJS`   
在自己电脑上安装好`NodeJS`，`Hexo`是基于`NodeJS`编写的，所以需要安装`NodeJS`和`npm`工具。
网上找篇教程或者参考[NodeJS安装及配置(Windows)](https://blog.csdn.net/yaorongke/article/details/119084295)。

## 二、创建仓库
在`GitHub`上创建一个新的代码仓库用于保存我们的网页。

点击`Your repositories`，进入仓库页面。

点击```New```按钮，进入仓库创建页面。

填写仓库名，格式必须为`<用户名>.github.io`，然后点击`Create repository`。


 
## 三、安装Hexo
采用`Hexo`来创建我们的博客网站，`Hexo`是一个基于NodeJS的静态博客网站生成器，使用`Hexo`不需开发，只要进行一些必要的配置即可生成一个个性化的博客网站，非常方便。点击进入[官网](https://hexo.io/zh-cn/)。

```base
npm install hexo-cli -g    # 安装 Hexo 
hexo init blog             # 创建一个项目 blog 并初始化
cd blog                    # 到 blog 文件夹下
npm install                # 安装 hexo 包
hexo server                # 本地启动
``` 
<code>hexo</code>常用命令：
```base
hexo clean     # 清除缓存文件 (db.json) 和已生成的静态文件 (public)
hexo g         # 生成静态文件
hexo d         # 部署之前预先生成静态文件
```
更多命令请参考[官方文档](https://hexo.io/zh-cn/docs/commands.html)。


## 四、更换主题

默认的主题不太好看，不过官方提供了数百种主题供用户选择，可以根据个人喜好更换，官网主题点[这里](https://hexo.io/themes/)查看。个人用的[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)，就以此为例。

安装主题
```base
cd blog                                                       # 到博客根目录下
git clone https://github.com/blinkfox/hexo-theme-matery.git   # 拉取主题仓库源码
```
将```blog```文件下的```_confige.yml```中的主题改成```next```
```yml  
theme: next
```
主题内的其他的内容设置请看[作者文档](http://blinkfox.com/2018/09/28/qian-duan/hexo-bo-ke-zhu-ti-zhi-hexo-theme-matery-de-jie-shao/)。

## 五、创建文章
你可以执行下列命令来创建一篇新文章或者新的页面。
```base
$ hexo new [layout] <title>
```
您可以在命令中指定文章的布局```（layout）```，默认为```post```，可以通过修改 ```_config.yml``` 中的 ```default_layout``` 参数来指定默认布局。详情请看[官方文档](https://hexo.io/zh-cn/docs/writing)。  

如下修改 ```Hexo``` 博客目录中的 ```_config.yml```，打开这个配置是为了在生成文章的时候生成一个同名的资源目录用于存放图片文件（当然一般不用这种方式，都用图床）。
```yml
post_asset_folder: true
```


例如执行如下命令创建一篇新文章，名为《测试文章》
```base
hexo new post 测试文章
```
执行完成后在```source\_posts```目录下生成了一个```md```文件和一个同名的资源目录(用于存放图片)。


## 六、不同电脑同步
参考：
1. [Hexo在多台电脑上提交和更新](https://blog.csdn.net/K1052176873/article/details/122879462)。  
2. [多台电脑同步更新Hexo博客](https://blog.csdn.net/qq_30105599/article/details/118302086)
### （一）hexo文件结构
老电脑上的目录结构
![](https://raw.githubusercontent.com/xhdou/image/main/PicGo4ce330d23cd43a89aa8d8865fad67017.png)
| 文件夹啊          | 说明 | 是否需要上传GitHub | 
|    :-:  | :-:    | :-:|
|   node_modules   |hexo需要的模块，就是一些基础的npm安装模块，比如一些美化插件，在执行npm install的时候会重新生成                                                                           |  不需要 | 
|   themes         |  主题文件                                                         | 需要  |
|   public         |   hexo g命令执行后生成的静态页面文件                                | 不需要 |
|   packages.json  | 记录了hexo需要的包的信息，之后换电脑了npm根据这个信息来安装hexo环境   |  需要  |
|   _config.yml    | 全局配置文件                                                      |  需要  |
|  .gitignore      |  hexo生成的默认的.gitignore模块                                   |   需要  |
|  scaffolds       |  文章的模板                                                       | 需要   |
|  deploy_git      |hexo g自动生成的                                                   | 不需要 |
### （二）同步原理

主要思路是利用```git```分支来实现```hexo```的同步。

生成的静态页面文件默认放在```main```分支上，这是由```_config.yml```配置文件所决定的

你可以在全局配置文件```_config.yml```中找到这么一段
```yml
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: main
  ```
 
因此每当我们执行```hexo d```的时候，```hexo```都会帮我们把生成好的静态页面文件推到```main```分支上。

在我们第一次部署好博客的时候，```github```给我们创建的唯一一个分支就是```main```分支，同时也是默认分支。默认分支就意味着每次我们执行```git clone``` 仓库地址或者```git pull```仓库地址拉取的是默认分支的代码。

但是执行```hexo d``` 对应的分支和默认分支是没有关系的，因为这是由配置文件决定的，配置文件写的哪个分支就是哪个分支。

因此，```hexo```生成的静态博客文件默认放在```main```分支上。```hexo```的源文件（部署环境文件）可以都放在```hexo```分支上（可以新创建一个```hexo```分支）。然后把```hexo```分支设置成默认分支。

### （三）老电脑上具体操作
先创建一个分支```hexo```，将其设置为默认分支

先```clone```该```hexo```仓库到本地（clone的是hexo默认分支）   
```base
git clone https://github.com/xhdou/xhdou.github.io.git
```

下载的文件夹里仅留下```.git```，```.gitignore```，其他的文件都删除

在克隆的仓库下分别执行以下命令更新删除操作到远程
```base
git add -A             # A指all，将工作区被修改、被删除、新增的文件都提交到暂存区
git commit -m "--"     # 将暂存区所有文件添加到本地仓库
git push origin hexo   # 推送当前本地分支到指定远程分支(hexo)
```

- 注意：
    - 现在```clone```下来的文件夹内应该有个```.gitignore```文件，用来忽略一些不需要的文件，表示这些类型文件不需要git。如果没有，右键新建，并移动至博客文件夹内，内容如下：
        ```
        .DS_Store
        Thumbs.db
        db.json
        *.log
        node_modules/
        public/
        .deploy*/
        ```

    - 如果已经```clone```过主题文件，那么需要把```theme```主题文件夹里的 ```.git``` 也删除。因为```git```不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了了。

将下载的文件夹里的```.git```，```.gitignore```移动至博客文件夹内，并在博客目录下执行以下命令：  
```base
git add -A                   # A指all，将工作区被修改、被删除、新增的文件都提交到暂存区
git commit -m "Hexo配置"     # 将暂存区所有文件添加到本地仓库，并备注为“Hexo配置”
git push origin hexo         # 推送当前本地分支到指定远程分支(hexo)
```

此时已经成功将整个网站环境配置文件推送到了远程库的默认分支```hexo``` 
![](https://raw.githubusercontent.com/xhdou/image/main/PicGo20230306132325.png) 

至此，网站部署至```main```分支，整个网站备份至```hexo```分支。当网站的配置或文章修改后都要将远程仓库更新。首先，依次执行：  
```base
git add -A               # A指all，将工作区被修改、被删除、新增的文件都提交到暂存区
git commit -m [massage]  # 将暂存区所有文件添加到本地仓库, [massage]是备注信息        
git push origin hexo     # 推送当前本地分支到指定远程分支(hexo)，保证hexo分支版本最新。
```
然后执行  
```base
hexo g
hexo d
```
（在此之前，有时可能需要执行```hexo clean```），完成后就会发现，最新改动已经更新到```main```分支了，两个分支互不干扰！