---
title: 从零开始搭建你的个人博客（Github+hexo+butterfly）
categories: 
    - 教程
tags:
    - 个人网站搭建
abbrlink: 52106
date: 2023-06-28 21:09:35
---

激动之情无以言表，我居然自己成功搭建了属于自己的博客；从零开始，一点点查资料，耗时3天左右，整个过程跌跌绊绊，最终达到了预想的结果，实在太有成就感了！

事情是这样的，我在与一个校友接触的过程中，通过他分享的经验帖进到了他的[博客网站](https://youngforest.github.io/2016/08/25/my-britain-trip/)，里面有好多干货，既包含技术层面，也包含了一些生活记录；那个校友用博客来记录年终总结和制订年度计划，太赞了！没想到用博客来做记录居然是一件这么有意思的事！那我也要搭建自己的博客网页！

不废话了，这篇博客主要记录从零开始搭建个人博客网站的**核心步骤**。正文开始~~~

**技术关键词**
- Github
- Hexo
- butterfly

# 本地环境配置
关键词： Git、Node.js、npm、Hexo  
## 安装Git
去[Git官网](https://git-scm.com/download/)下载所需版本，一直点下一步安装。
## 安装node.js
去[node.js官网](https://nodejs.org/en/download)下载符合自己系统的版本，傻瓜式安装，一直点下一步即可。node.js自带npm。命令行中输入`node -v`验证是否安装成功。

## 安装Hexo
命令行中输入`npm install -g hexo-cli`。输入`hexo -v`验证是否安装成功

# 创建本地开发目录
创建一个博客项目文件夹，在该路径下用`Git bash`打开；输入`npm i`安装依赖；输入`hexo init blog-demo(项目名)`生成我们的Hexo项目，生成后会出现如下文件：  

>【node_modules】：依赖包  
>【scaffolds】：生成文章的一些模板  
>【source】：用来存放你的文章  
>【themes】：主题  
>【.npmignore】：发布时忽略的文件（可忽略）  
>【_config.landscape.yml】：主题的配置文件  
>【_config.yml】：博客的配置文件  
>【package.json】：项目名称、描述、版本、运行和开发等信息

至此，我们的博客已经搭好啦！

什么！！你在跟我开玩笑吗？？

没错，我们的博客雏形到这里已经完成了，我们可以运行本地博客项目查看我们的初版博客了；此后就是在此基础上**美化博客网站**、**写博客**、**部署到互联网上**。

在Git bash中继续依次输入`hexo clean` & `hexo g` & `hexo s`运行服务，在浏览器打开即可~

# 美化网站
初始的博客实在太丑，Hexo提供了大量的模板用来美化我们的网页，可以去hexo官网找一个喜欢的风格模板。本博客网站采用了butterfly模板，参考[教程](https://fe32.top/articles/hexo1600/)，讲的非常的详细！  
此处不做赘述，主要是刚开始写博客，肯定没人家解释的清楚~

# 写博客
这里写博客用的是Markdown语法。在Git bash中输入`hexo new 我的博客名称`新建博客，新建的博客可以在`./source/_posts/`目录下看到，用IDE打开编辑保存即可。

重新输入`hexo clean` & `hexo g` & `hexo s`运行服务，可以看到博客已经出现在网页上了。

# 部署到Github上
参考[教程](https://fe32.top/articles/hexo1600/)

# 其他
## 图片放置问题
参考[资料](https://blog.csdn.net/ayuayue/article/details/109198493) 配置   
注意，博客的卡片图（cover）放置的位置和博文图片放置的位置不一样