---
title: 常用Linux命令整理
tags: Linux
categories: 笔记
abbrlink: 64322
date: 2023-07-06 14:12:37
---

## 查看某个进程是否正在运行
```shell
ps aux | grep 进程名
```

## kill进程
```shell
kill -9 进程号  # 根据进程号kill
pkill -f 进程名 # 根据进程名kill
ps -ef | grep "进程名" | grep -v grep | cut -c 9-15 | xargs kill -9 # 批量kill同名进程
```

## 查看某个进程的代码位置
```shell
pwdx 进程号
ll /proc/进程号
ps aux | grep 进程号/进程名
```

## 在指定目录下新建或删除用户
```shell
useradd -d /opt/XXX -m 用户名
passwd 用户名 # 添加密码
userdel chencheng
```

## 批量安装和导出python依赖
```shell
pip freeze > requirement.txt # 导出依赖
pip install -r requirement.txt # 安装依赖
while read requirement; do pip install $requirement -i  https://pypi.tuna.tsinghua.edu.cn/simple; done < requirements.txt # 安装依赖，跳过失败项
```

## 如何使用别人的python环境
```shell
# 方式一：export环境
export PATH=/data/chencheng/anaconda3/envs/para_env/bin:$PATH
source ~/.bashrc
# 方式二：使用别人环境下的python运行自己的代码
nohup /data/chencheng/anaconda3/envs/para_env/bin/python csp_para.py > /dev/null 2>&1 &
```

## 打包和解压缩
```shell
# tar打包 文件1和文件2 成XXX.tar.gz
tar -zcvf XXX.tar.gz 文件1 文件2
# tar解压缩
tar -zxvf XXX.tar.gz -C /some/directory

# zip压缩文件
zip -r XXX.zip /XXX
# unzip解压缩
unzip XXX.zip -d /some/directory

# 解压.gz文件
gzip -d XXX.gz
```

## 从一台机器传文件到另一台机器
```shell
scp [-P 端口号] XXX user@ip:/path/to/some/directory
```

## 复制时排除某目录
```shell
# 例如有目录/a/b/c /a/d/e，要复制到/f，排除b目录
cp -r !(b) /f
```

## 查看磁盘占用、文件占用
```shell
df -h
du -ah --max-depth=1
```