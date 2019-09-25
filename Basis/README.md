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

