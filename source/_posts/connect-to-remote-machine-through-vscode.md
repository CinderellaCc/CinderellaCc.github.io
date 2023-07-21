---
title: 如何使用vscode调试远程代码（通过跳板机登录、终端使用）
tags: vscode
abbrlink: 20818
date: 2023-07-05 17:17:40
---

本教程将教会你使用vscode连接远程服务器（包括需要跳板机连接的远程服务器）进行代码调试、终端使用等。

这个方法已经被安利给组内的小伙伴了，他们都赞不绝口（不夸张），像是打开了新世界的大门！！！开发效率蹭蹭蹭！



# 在本地安装VSCode软件
    略
# 在VSCode中连接远程服务器
1. 在VSCode中安装SSH插件。在插件安装处搜索“SSH”，选择下载量最高的SSH插件安装即可 
2. 点击vscode左边栏“远程资源管理器”，配置本地~/.ssh/config文件  
![config文件截图](/images/connect-to-remote-machine-through-vscode/snapshot1.png)
在config文件中输入以下内容（修改机器ip、账户名称、本地私钥位置）
```shell
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config

# 跳板机115
Host go115 # 跳板机名称
    HostName 10.18.75.115
    Port 22
    User root
    IdentityFile C:\Users\chengchen219429\.ssh\id_rsa # 本地私钥位置

# 目的机247
Host go247 # 目的机名称
    HostName 10.23.162.14
    User root
    ProxyCommand C:\Windows\System32\OpenSSH\ssh.exe -W %h:%p go115 # 这里注明跳板机名称
```

3. 将本地ssh公钥上传至跳板机和目的机，方法如下    
找到本地.ssh文件夹（默认在用户目录下，如C:\Users\XXX\.ssh），打开公钥id_rsa.pub，复制。注：如果本地没有公私钥文件，可以手动生成，在cmd中输入`ssh-keygen -t rsa -C "your email address"`

4. 登录跳板机和目的机，将复制的公钥拷贝至~/.ssh/authorized_keys文件最后一行，保存

5. 登录远程服务器~
![config文件截图](/images/connect-to-remote-machine-through-vscode/snapshot2.png)

# 使用远程服务器进行调试
1. 调试配置：点击vscode左侧“运行和调试”按钮，编写launch.json文件，在“python”字段输入python解释器路径，其他保持不变，保存文件
```
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: 当前文件",
            "type": "python",
            "python": "/data/chencheng/anaconda3/envs/comment_env/bin/python3.8",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": true
        }
    ]
}
```
2. 开始调试：编写一个调试测试py文件，打上断点，即可开始愉快的debug啦

# 其他
## 文件传输功能
使用vscode连接服务器后可以直接上传下载文件，非常方便
## 设置vscode终端每次打开都以最大化显示
点击“文件”-“首选项”，搜索“Panel: Opens Maximized”，设置为“always”
## 设置vscode不显示隐藏文件（命名以'.'开头的文件或文件夹）
按下快捷键：ctrl+shift+P，在输入框中输入settings回车，进入User Settings页面搜索files.exclude，没有的话则手动添加。false为显示，true为隐藏。
```
"files.exclude": {
        // "**/.git": false,
        // "**/.svn": false,
        // "**/.hg": false,
        // "**/CVS": false,
        // "**/.DS_Store": false,
        // "**/Thumbs.db": false,
        // "**/.local":false,
        "**/.*": true
    },
```
## 无法连接远程服务器的原因
以下可能的解决方法  
用root登录远程主机，修改vi /etc/ssh/sshd_config文件的字段，改为
```
PasswordAuthentication yes
ChallengeResponseAuthentication no
```
重启服务
```
systemctl restart sshd.service
systemctl status sshd.service #查看ssh服务的状态
```

## vscode设置自动保存（很重要）
快捷键`ctrl+，`打开设置，搜索`auto save`，按下图设置
![config文件截图](/images/connect-to-remote-machine-through-vscode/snapshot3.png)




参考资料：
- [vscode使用跳板机（密钥）进入内网并连接内网中其它机器（密码）](https://blog.csdn.net/baidu_41553551/article/details/128505159)

## 如何解决VSCode无法连接到Python内核问题
参考：https://www.python100.com/html/ZQQX88O0863H.html