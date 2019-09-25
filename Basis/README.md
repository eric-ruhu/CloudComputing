# 云计算开发

## 预备工作

* 购买[腾讯云学生计划](https://cloud.tencent.com/act/campus)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/1.png?raw=true)

1. 使用webshell登录已购买的云服务器

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/2.png?raw=true)

* 下载[Xshell6](http://www.xshellcn.com/xiazai.html)和[Xmananger6](http://www.xshellcn.com/xiazai.html)

  > Xmanager是可以浏览远端X窗口系统的工具，Xshell插件是一款功能强大的终端模拟器。

1. 新建会话

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/3.png?raw=true)

2. 成功连接

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/4.png?raw=true)

* 注册GitHub账号并登录

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/5.png?raw=true)

## SSH Key

* 安装[Git](https://git-scm.com/downloads)

  > 说明：Git Bash (基于MINGW) 是Windows下进行Git操作的shell。

* 打开Git Bash，创建密钥

* 验证是否有ssh key ，在Git Bash上输入指令

  > ls -al ~/.ssh

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/6.png?raw=true)

***注：如果出现id_rsa和id_rsa.pub文件，则ssh key存在，以下步骤可以跳过。

* 如果不存在ssh key，则新建ssh key

  > ssh-keygen -t rsa -b 4096 -C "1795025175@qq.com"

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/7.png?raw=true)

***注：” ”内替换为本人注册GitHub所使用的邮箱

* 复制密钥的内容到GitHub的SSH keys中

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/8.png?raw=true)

* 测试ssh key是否配置成功

  > ssh -T git@github.com

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/9.png?raw=true)

## 创建GitHub项目并在本地同步

* 新建代码仓库

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/10.png?raw=true)

之前已创建过同名代码仓库但是没有截图，换个名字是相同道理。

***注：此处先不初始化README，后续再对此操作

* 新建本地代码仓库

右键单击Git Bash

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/11.png?raw=true)

复制先前创建的代码仓库的url

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/12.png?raw=true)

> git remote add origin https://github.com/eric-ruhu/CloudComputing.git

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/13.png?raw=true)

由于之前我已经新建过代码仓库所以显示已存在，再新建指令也是相同。

>git pull origin master
>
>git touch README.md  //由于做本实验的时候已经有这个文件了，在此就用一个test的文本文件替代
>
>git status
>
>git add .  //添加所有modified文件
>
>git status

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/14.png?raw=true)

> git commit -m "test!!!"
>
> git push -u origin master

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/15.png?raw=true)

刷新网页可看到上传成功！！！

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/16.png?raw=true)

## 安装VMware Workstation和CentOS系统

* 下载[VMware](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)
* 下载[CentOS](https://www.centos.org/)
* 创建新的虚拟机

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/17.png?raw=true)

语言设置为英语，安装桌面GUI，并设置开启网络，其他选项自行选择。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/18.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/19.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/20.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/21.png?raw=true)

***注：由于写报告之前已安装所以有些配置没有截图，这里有几张图参考

此处要注意，网络开启有一个选项最好要打开，不然之后可能会遇到网络打不开需要修改脚本的问题。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/22.png?raw=true)

设置root用户，设置密码，设置一些选项。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/23.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/24.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/25.png?raw=true)

登录成功！！！over！！！

## 后记

安装虚拟机的时候受到舍友很多帮助要不然不知道配到啥时候去哦。。太难了。。

上传GitHub文件途中遇到本地库和远程库冲突的情况（可能是因为我之前把同名文档删除）

解决错误的方法：(https://blog.csdn.net/i_tenkai/article/details/95483289)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/26.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Basis/image/27.png?raw=true)

