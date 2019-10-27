# 腾讯云CentOS 7上搭建WordPress

## 部署环境：LAMP

* 云操作系统：CentOS 7.6 64位；
* HTTP服务器：Apache Web 服务器；
* 数据库：MySQL；
* 建站工具：WordPress（基于PHP）。

## 步骤

### 1.安装Apache Web服务器

> sudo yum install httpd

***注：这里及下文提到的sudo命令是获得root用户的执行权限，我则直接用root用户安装。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/1.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/2.png?raw=true)

安装完成后，启动Apache Web服务器：

> sudo systemctl start httpd.service

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/3.png?raw=true)

测试Apache服务器是否成功运行，找到腾讯云实例的公有IP地址(your_cvm_ip)，在你本地主机的浏览器上输入：

> http://106.54.62.70/

若运行正常，将出现如下界面：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/4.png?raw=true)

### 2.安装MySQL

CentOS 7.2的yum源中并末包含MySQL，需要其他方式手动安装。因此，我们采用MySQL数据库的开源分支MariaDB作为替代。
安装MariaDB：

> sudo yum install mariadb-server mariadb

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/5.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/6.png?raw=true)

安装好之后，启动mariadb：

> sudo systemctl start mariadb

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/7.png?raw=true)

运行简单的安全脚本以移除潜在的安全风险，启动交互脚本：

> sudo mysql_secure_installation

***注：这里设置了密码，还有一系列选项，我这里全选择了Y。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/8.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/9.png?raw=true)

设置开机启动MariaDB：

> sudo systemctl enable mariadb.service

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/10.png?raw=true)

### 3.安装PHP

PHP是一种网页开发语言，能够运行脚本，连接MySQL数据库，并显示动态网页内容。这里手动安装PHP较新的版本(PHP 7.2)。PHP 7.x包在许多仓库中都包含，这里我们使用Remi仓库，而Remi仓库依赖于EPEL仓库。
启用这两个仓库：

> sudo yum install epel-release yum-utils

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/11.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/12.png?raw=true)

> sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/13.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/14.png?raw=true)

启用PHP 7.2 Remi仓库：

> sudo yum-config-manager --enable remi-php72

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/15.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/16.png?raw=true)

安装PHP以及php-mysql

> sudo yum install php php-mysql

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/17.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/18.png?raw=true)

查看安装的php版本：

> php -v

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/19.png?raw=true)

安装之后，重启Apache服务器以支持PHP：

> sudo systemctl restart httpd.service

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/20.png?raw=true)

### 4.安装PHP模块

启动PHP附加模块，使用如下命令可以查看可用模块：

> yum search php-

部分结果如图所示：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/21.png?raw=true)

这里先行安装php-fpm和php-gd，WordPress使用php-gd进行图片的缩放。

> sudo yum install php-fpm php-gd

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/22.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/23.png?raw=true)

重启Apache服务：

> sudo service httpd restart

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/24.png?raw=true)

至此，LAMP环境已经安装成功，接下来测试PHP。

### 5.测试PHP

利用一个简单的信息显示页面（info.php）测试PHP。

创建info.php并将其置于Web服务的根目录（/var/www/html/）：

> sudo vim /var/www/html/info.php

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/25.png?raw=true)

该命令使用vim在/var/www/html/处创建一个空白文件info.php，添加如下内容：

> <?php phpinfo(); ?>

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/26.png?raw=true)

完成之后，使用刚才获取的cvm的IP地址（腾讯云实例公有地址），在本地主机的浏览器中输入:

> http://106.54.62.70/info.php

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/27.png?raw=true)

### 6.安装Wordpress以及完成相关配置

##### （1）为WordPress创建一个MySQL数据库

首先以root用户登录MySQL数据库：

> mysql -u root -p

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/28.png?raw=true)

为WordPress创建数据库wordpress：

> CREATE DATABASE wordpress;

***注：MySQL语句都是以分号结尾。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/29.png?raw=true)

为WordPress创建一个独立的MySQL用户：

> CREATE USER ruhu@localhost IDENTIFIED BY '123456';
>
> GRANT ALL PRIVILEGES ON wordpress.* TO ruhu@localhost IDENTIFIED BY '123456';
>
> FLUSH PRIVILEGES;
>
> exit

* 创建用户ruhu和设置密码
* 授予给ruhu用户访问数据库的权限
* 刷新MySQL的权限
* 退出

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/30.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/31.png?raw=true)

##### （2）安装WordPress

下载WordPress至当前用户的主目录：

> cd ~
> wget http://wordpress.org/latest.tar.gz

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/32.png?raw=true)

解压文件

> tar xzvf latest.tar.gz

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/33.png?raw=true)

解压之后在主目录下产生一个wordpress文件夹。我们将该文件夹下的内容同步到Apache服务器的根目录下，使得wordpress的内容能够被访问。这里使用rsync命令：

> sudo rsync -avP ~/wordpress/ /var/www/html/

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/34%20.png?raw=true)

在Apache服务器目录下为wordpress创建一个文件夹来保存上传的文件：

> mkdir /var/www/html/wp-content/uploads

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/35.png?raw=true)

对Apache服务器的目录以及wordpress相关文件夹设置访问权限：

> sudo chown -R apache:apache /var/www/html/*

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/36.png?raw=true)

这样Apache Web服务器能够创建、更改WordPress相关文件，同时我们也能够上传文件。

##### （3）配置WordPress

连接WordPress和MySQL

定位到wordpress所在文件夹：

> cd /var/www/html

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/37.png?raw=true)

WordPress的配置依赖于wp-config.php文件，当前该文件夹下并没有该文件，我们通过拷贝wp-config-sample.php文件来生成：

> cp wp-config-sample.php wp-config.php

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/38.png?raw=true)

在wordpress配置文件中修改数据库信息：

> nano wp-config.php

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/39.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/40.png?raw=true)

***注：目前只修改DB_NAME，DB_USER和DB_PASSWORD。

##### （4）通过Web界面进一步配置WordPress

在网页输入IP地址或者域名：

> http://eric-nam.cn or 106.54.62.70

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/41.png?raw=true)

登录：

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/42.png?raw=true)

(慢慢完善ing！！！！已经可以自定义头像了kkkkk)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Website/website_images/43.png?raw=true)

[IP查看网站链接！！！](http://106.54.62.70/)

[域名查看网站链接！！！](http://www.eric-nam.cn/)

