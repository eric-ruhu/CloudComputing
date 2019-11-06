# CentOS 7 安装 Docker

## 先决条件

* 已安装CentOS 7，并且内核版本大等于3.10，本文使用的是腾讯云的镜像。

* 非root用户已获得sudo特权。

查看操作系统内核信息：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/1.png?raw=true)

查看Linux版本：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/2.png?raw=true)

***注：如果当前用户不能使用sudo权限，登录到root用户，在终端键入：

> gpasswd -a user wheel

（user指代希望授权的用户）

## 安装Docker

更新应用程序数据库：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/3.png?raw=true)

添加Docker的官方仓库，下载最新的Docker并安装：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/4.png?raw=true)

安装完成后启动Docker守护进程，即Docker服务：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/5.png?raw=true)

验证Docker是否启动：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/6.png?raw=true)

使Docker当服务器启动时自动启动：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/7.png?raw=true)

查看Docker版本：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/8.png?raw=true)

## Docker基本操作

***注：Docker的操作需要用户获得root权限。

Docker命令的基本格式

> docker [选项] [命令] [参数]

查看docker所有的命令：

> docker

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/9.png?raw=true)

特定命令的使用帮助：

> docker COMMAND --help

例：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/10.png?raw=true)

查看当前系统docker的相关信息：

> docker info

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/11.png?raw=true)

（可见当前并未安装任何镜像（Images），运行任何容器（Containers）。）

加载centos镜像命令：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/12.png?raw=true)

## 在Docker的CentOS容器中安装WordPress

在Docker Hub上创建一个仓库：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/13.png?raw=true)

拉取centos7（拉取latest的话后续操作会出错）

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/14.png?raw=true)

查看当前系统中存在的镜像：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/15.png?raw=true)

创建并运行Docker容器（为了方便检测后续wordpress搭建是否成功，需设置端口映射（-p），将容器端口80 映射到主机端口8888，Apache和MySQL需要 systemctl 管理服务启动，需要加上参数 --privileged 来增加权，并且不能使用默认的bash，换成 init，否则会提示 Failed to get D-Bus connection: Operation not permitted ，命令如下 ）

***注：看了CTO的解决办法发现端口映射可解决和之前搭建WordPress冲突的问题。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/16.png?raw=true)

查看已启动容器：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/17.png?raw=true)

进入容器前台（容器id可以只写前几位，如 ：145）

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/18.png?raw=true)

**接下来参照实验2搭建[WordPress](https://github.com/eric-ruhu/CloudComputing/tree/master/Website)**

(**注：按照实验2的步骤一步一步做，之前的步骤就没有写下来了和试验2相同，我从安装WordPress开始写起，老师后面检验的话直接打开[106.54.62.70:8888](http://106.54.62.70:8888/wp-admin/install.php)就可以了！！因为我映射另外端口的话，之前的ip地址还在。安装WordPress按照下面的步骤，yum下载过慢，service httpd restarted有问题见最下面的解决方法！)**

##### 安装WordPress

下载WordPress：

> yum install git
>
> git clone https://gitee.com/helang_z/wordpress.git

解压：

> cd wordpress
>
> tar xzvf wordpress-5.2.4.tar.gz

移动解压的文件：

(***注：将该文件夹下的内容同步到Apache服务器的根目录下，使得wordpress的内容能够被访问。)

> rm -rf /var/www/html
>
> mv wordpress /var/www/html

接着在Apache服务器目录下为wordpress创建一个文件夹来保存上传的文件：

> mkdir /var/www/html/wp-content/uploads

对Apache服务器的目录以及wordpress相关文件夹设置访问权限：

> chown -R apache:apache /var/www/html/*

**配置完WordPress后，在网页上输入[106.54.62.70:8888](http://106.54.62.70:8888/wp-admin/install.php)出现如图所示则表示安装完成：**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/19.png?raw=true)

##### 创建新的镜像

查看本地中的容器：

> docker ps -a 

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/20.png?raw=true)

创建新的镜像：

(***注：将容器生成镜像 ，所生成的镜像名由 "Docker用户名/Docker仓库名组成" ，否则推送会报错： denied: requested access to the resource is denied )

> docker commit -a "Docker用户名" -m "提交描述" 容器id 镜像名:tag标签

例：

> docker commit -m “website” -a “ruhu” 14563598c483 ruhu/cloud_computing:wordpress

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/21.png?raw=true)

***注：这种提交类似于git协议的提交，同样这里提交的镜像只保存在本地。后续可以提交到远程镜像仓库，比如Docker Hub。
查看镜像：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/22.png?raw=true)

***注：接下来要为新建的镜像打上标签（Tag），否则后续推送镜像到Docker Hub的时候将出现“ denied: requested access to the resource is denied”的错误。关于这个错误的解答详见[stackoverflow](https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker)。
Tag命令：

> docker tag SOURCE_IMAGE[:TAG] docker-hub-username/REPOSITORY[:TAG]

例：

> docker tag 0b44c5a9f250 ruhu/cloud_computing:wordpress

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/23.png?raw=true)

查看镜像：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/24.png?raw=true)

##### 推送镜像

shell登录Docker Hub:

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/25.png?raw=true)

推送镜像：

> docker push docker-hub-username/REPOSITORY:TAG

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/26.png?raw=true)

在Docker Hub上查看新上传的镜像：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/27.png?raw=true)

### 部分问题解决方法

1、1. Yum下载过慢是因为是国外源（真的非常非常慢。。。）

https://mirrors.tuna.tsinghua.edu.cn/help/centos/

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/28.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/29.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/30.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/31.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/32.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/33.png?raw=true)

2、service命令无法用

输入如图两行命令：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/34.png?raw=true)

可以用了！！

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Docker/docker_images/35.png?raw=true)

