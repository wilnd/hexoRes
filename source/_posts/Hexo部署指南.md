---
title: Hexo部署指南
date: 2017-08-18 02:08:41
tags: 博客搭建
categories: 博客搭建
---
## Hexo 对于我的意义
- 我对自己的评价是善于总结分析，不善于记忆，更别提表达，很多事情心里大概清楚要怎么做，但是在做的时候实际可能卡在一个需要记忆的地方然后就动弹不得了，所以我需要一款能够将我做过的事记忆起来的工具，帮我保存我的理解，以便随时翻看  
- 我是一个急性子的人，对于一件事情，希望是越快完成越好，越是重要的事情，越着急，心里一急，对于资料的阅读情不自禁的采用扫读的模式。这样的行为会让效率下贱，增加挫败感，进而产生畏惧的心理。对于一个我不是很了解的东西，需要慢慢沉淀，记录是一种十分有效的方式。
- 任何困难的事情，都会因量变积累产生质变，最终困难的事情将会被解决。其中有两个关键词：量变，积累。量变是时间的堆砌，积累是对事情的反思总结化作经验的累计。两者缺一不可。如果仅有时间的堆积，精力的支出，而经验积累不到位，或者总是总结，却不花时间去积累实践，也是不会有效果的。Hexo，便是我的积累。
- 我个人感觉我的基因还是挺好的，各方面都有兴趣，各方面天赋也还好，但是属于广而杂的类型，我需要将我各方面的优势拧成一根绳子，增加个人的生活品质，或者说是活得更快乐一点，方不辜负我老爸老妈给我留下的这么优秀的基因，而且还要更好的遗传下去。

## 我的hexo运行环境
- Git
    - git version 2.14.1.windows.1
- Node-js
    - node  version 6.11.2
    - npm   version 3.10.10
-  hexo
    - hexo: 3.3.8
    - hexo-cli: 1.0.3

## 环境的准备
- [nodejs下载页面](https://nodejs.org/en/)
- [git 下载页面](https://git-scm.com/)
- [hexo文档](https://hexo.io/zh-cn/docs/)  
###### 下载这些东西可能会比较慢，如果感觉下载的比较慢有两种处理方式。找国内镜像，翻墙。后面我会做一个翻墙的教程出来，傻瓜式教学，包学会。其实也很简单的，主要是路子要对...

## 项目搭建  

- GitHub pagge的配置。
> 用Github配置有一个好处是，我们无需购买服务器了，gitub为我么提供了（大企业范~）。当然这个是有限制的。如果要享受GitHub提供的免费服务器，需要将项目完全公开，所以任何人都能对我现在部署的项目进行操作。如果希望项目私有化，那就得给钱给他。所以很多人会买个域名，因为域名可以隐藏项目信息，让人不那么容易找到放在GitHub上的项目

> 首先需要在GitHub上注册一个账号。仅仅需要用到邮箱（QQ邮箱也行），密码（大小写都要有）注册还是很方便的。 注册，邮箱激活，登录后到如下界面，点击如图的新建按钮

![](http://ww1.sinaimg.cn/large/005Y4715gy1fin7xe1iwoj30va0e4mye.jpg)
> 在框框里面输入项目名 格式：GitHub用户名+github.io

![](http://ww1.sinaimg.cn/large/005Y4715gy1fin7z7wodoj30u80jn3zw.jpg)
> 点击Create responsitory 完成GitHub page的创建

- ssh的配置
> ssh是用来做免密登录的，整个配置过程就是做四件事情：设置Git的默认邮箱，设置Git的默认用户名（我设置的用户名跟我的GitHub登录的用户名一致，是否可以不一致有时间再验证），生成秘钥对，将公钥放到GitHub里面去，验证。
> - 设置Git的默认用户名及邮箱

```
git config --global user.name "youname"
git config --global user.email "youeamil@email.com"
```

> - 生成密钥的过程 
>1. 查看密钥是否存在
    
```
cd ~/.ssh
```
>2. 如果有密钥了删除没有则生成

```
 ssh-keygen -t rsa -C “287329409@qq.com”
```
> 一直按回车，四下

> - 将公钥丢到GitHub上去

> 1. 点击setting
![](http://ww1.sinaimg.cn/large/005Y4715gy1fin8m2wewqj30vx0jkabo.jpg)

> 2. 点击SSH
![](http://ww1.sinaimg.cn/large/005Y4715gy1fin8pbsy1mj30yx0mn76f.jpg)

> 3. New SSH key
![](http://ww1.sinaimg.cn/large/005Y4715gy1fin8qt6qlhj30wr0gcdhq.jpg)

> 4. title随便填，key填写公钥（id_rsa.pub）里面的全部内容
![](http://ww1.sinaimg.cn/large/005Y4715gy1fin8s0lvvej318g0o4427.jpg)

> - 验证

```
 ssh -T git@github.com
```
> ![](http://ww1.sinaimg.cn/large/005Y4715gy1fin8w17pe9j30fq03nglm.jpg)

> 如果不是这样的，ssh重新来过

## Hexo项目的生成
> - 安装Hexo  [Hexo参数](https://hexo.io/zh-cn/docs/configuration.html)

```
npm install -g hexo-cli
```
> Hexo目录结构
![](http://ww1.sinaimg.cn/large/005Y4715gy1fin94lrgrwj30ik07igm3.jpg)

> 编译

```
hexo g
```
> 本地启动,启动后可以在浏览器输入localhost:4000访问

```
hexo s
```
> 部署到gitub page 部署成功后可以访问 例如我的,https://wilnd.github.io

```
hexo d
```

## hexo写作
> - 新建文章 [hexo写作](https://hexo.io/zh-cn/docs/writing.html)

```
hexo new post/page/draft <title>
```
>  - - post 这个操作会在source/_post目录下生成一个名为titel 的MarkDowm文件，是用来写文章的
>  - - page 这个操作会在source目录下生成一个MarkDowm文件，是用来生成大目录的，例如 我的界面的标签功能和目录功能都是通过这个方式来生成的
