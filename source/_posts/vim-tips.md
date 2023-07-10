---
title: vim使用教程，提高你的工作效率
tags:
  - vim
  - Linux
categories: 笔记
abbrlink: 24236
date: 2023-07-10 15:38:12
---

我在入职第一年内，写代码没有使用其他IDE，所有编程工作都是使用vim完成。我将结合一年多的vim使用经验，总结一套最实用的vim使用技巧。

# 下载最佳搭档 [Termius](https://www.termius.com/)
Termius是ssh客户端，免费好用，界面美观
> 使用技巧：窗口切换快捷键：Alt + 左右键（掌握这一个就行）

# 环境配置 .vimrc
创建自己的个人Linux账户后，在用户目录（~）下创建.vimrc文件，写入以下内容
```shell
syntax enable
set number
set tabstop=4
set expandtab
set paste
```
> .vimrc文件主要用于配置vim，例如语法高亮、显示行号、自动缩进等；功能很多，但我觉得以上简单配置就够用了

# 熟记vim快捷键
掌握vim快捷键能够极大的提高工作效率，非常重要

vim命令模式分为三类：编辑模式、命令模式、底线命令模式
```
编辑模式：

- 命令模式下按 i 进入编辑模式，开始编辑文本

命令模式：

- [ + ]：跳到文件顶部
- ] + [：跳到文件底部
- Ctrl + f ：向下翻页
- Ctrl + b：向上翻页
- 小写o：在下方插入一行
- 大写O：在上方插入一行
- g + f：跳转到路径所指向的文件（在log中查bug好用）
- Shift + #：向上查找；此时按 n，则为向下查找
- Ctrl + o：返回到跳转前的位置（可以一直往前跳哦，还可以在不同文件之间跳哦）
- Ctrl + i ： 与Ctrl + o 作用相反
- Ctrl + v：可视块模式，搭配Shift + i 可以实现代码块的左右移动，注释等功能
- u：撤销操作（一不小心输了奇奇怪怪的字符或删除了代码的时候可用）
- x：删除当前字符
- 大写 I：行首插入（不包括空格）
- 大写A：行尾插入

底线命令模式：

- 命令模式下按 ：进入底线命令模式
- e /home/beauty.txt：跳转到/home/beauty.txt文件中
- /beautiful：查找beautiful
- /beautiful\|handsome：同时查找beautiful和handsome
- set nu：设置行号，set nu!：取消行号
- set list：查看文本格式
- set paste：复制大段代码不错位
```

# vim跨文件复制粘贴
**step 1**: vim打开A文件  
**step 2**: 命令模式，输入：":sp"或者":vsp"切分出另一个窗口  
**step 3**: 命令模式，输入：":e B"，在一个窗口中打开B文件  
**step 4**: 按ctrl+w，按w，切换到A文件窗口命令模式，光标移到需复制起始行，输入复制的行数量，输入yy，进行复制  
**step 5**: 按ctrl+w，按w，切换到B文件窗口；命令模式，光标移到需粘贴行，输入p，完成粘贴

# 字符串全局替换
```
:%s/a/b/g # 用b替换a
```

# 搜索同时包含两个字符串的所有“行”
```
:set magic
/foo.*bar # 搜索含有foo和bar所在的行
```

