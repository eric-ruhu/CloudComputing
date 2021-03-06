# Ceph的安装和实践

### 先决条件

创建一个主机名为ceph-admin的虚拟机以备后续操作并克隆。

* 更改主机名

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/1.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/2.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/3.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/4.png?raw=true)

* 重启

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/5.png?raw=true)

* 修改完成

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/6.png?raw=true)

* 查看ip

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/7.png?raw=true)

### 步骤1-配置所有节点

***注:这里的操作都先在主机名为ceph-admin的虚拟机上做，之后再进行克隆

#### 创建一个Ceph用户

* 在所有节点上创建一个名为“ **cephuser** ” 的新用户

> useradd -d /home/cephuser -m cephuser
>
> passwd cephuser

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/8.png?raw=true)

* 为“ cephuser”配置sudo，运行以下命令为用户创建一个sudoers文件，并使用sed编辑/ etc / sudoers文件

> echo "cephuser ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/cephuser
>
> chmod 0440 /etc/sudoers.d/cephuser
>
> sed -i s'/Defaults requiretty/#Defaults requiretty'/g /etc/sudoers

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/9.png?raw=true)

#### 安装和配置NTP

> yum install -y ntp ntpdate ntp-doc
>
> ntpdate 0.us.pool.ntp.org
>
> hwclock --systohc
>
> systemctl enable ntpd.service
>
> systemctl start ntpd.service

***注:安装NTP以同步所有节点上的日期和时间。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/10.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/11.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/12.png?raw=true)

#### 安装Open-vm-tools

* 在VMware内部运行所有节点，则需要安装此虚拟化实用程序。

> yum install -y open-vm-tools

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/13.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/14.png?raw=true)

#### 禁用SELinux

> sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/15.png?raw=true)

### ~~***(做到这步复制虚拟机！！！！！)***~~

**复制完之后每一虚拟机都重新更改主机名（跟改ceph-admin主机名步骤一样）并查看各个ip**

* mon1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/16.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/17.png?raw=true)

* osd1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/18.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/19.png?raw=true)

* osd2

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/20.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/21.png?raw=true)

#### 配置主机文件（所有节点）

使用vim编辑器在所有节点上编辑/ etc / hosts文件，并添加带有所有集群节点的IP地址和主机名的行。

* ceph-admin

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/22.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/23.png?raw=true)

* mon1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/24.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/25.png?raw=true)

* osd1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/26.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/27.png?raw=true)

* osd2

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/28.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/29.png?raw=true)

**尝试使用主机名在服务器之间ping通，以测试网络连接。**

> 例: ping -c 5 mon1

~~**注：这里要将所有的虚拟机全部打开要不然ping不通**~~

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/30.png?raw=true)

### 第2步-配置SSH服务器

在此步骤中，将配置**ceph-admin节点**。admin节点用于安装和配置所有群集节点，因此ceph-admin节点上的用户必须具有无需密码即可连接到所有节点的特权。

* 为“ **cephuser** ” 生成ssh密钥，划线位置全部直接回车将密码短语留空/空白。

> ssh-keygen

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/31.png?raw=true)

* 为ssh配置创建配置文件

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/32.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/33.png?raw=true)

* 更改配置文件的权限

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/34.png?raw=true)

* 使用ssh-copy-id命令将SSH密钥添加到所有节点

> ssh-keyscan osd1 osd2  mon1 >> ~/.ssh/known_hosts
>
> ssh-copy-id osd1
>
> ssh-copy-id osd2
>
> ssh-copy-id mon1

**问题：**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/35.png?raw=true)

~~**注：这里要将所有的虚拟机全部打开要不然输入命令会提示以上错误表示连接不到**~~

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/36.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/37.png?raw=true)

（箭头处输入密码）

* 完成后，尝试从ceph-admin节点访问osd1服务器

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/38.png?raw=true)

成功！！！

### **步骤3-配置防火墙**

我们将使用防火墙保护系统。在此步骤中，我们将在所有节点上启用firewalld，然后打开ceph-admon，ceph-mon和ceph-osd所需的端口。

* 登录到ceph-admin节点并启动firewalld，打开端口80、2003和4505-4506，然后重新加载防火墙

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/39.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/40.png?raw=true)

* 从ceph-admin节点登录到监视节点“ mon1”，然后启动firewalld,在Ceph监视节点上打开新端口，然后重新加载防火墙。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/41.png?raw=true)

* 打开每个osd节点上的端口6800-7300-osd1，osd2，打开端口并重新加载防火墙

* osd1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/42.png?raw=true)

* osd2

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/43.png?raw=true)

防火墙配置完成。

### 第4步-构建Ceph集群

#### 在ceph-admin节点上安装ceph-deploy

添加Ceph存储库，并使用yum命令安装Ceph部署工具' **ceph-deploy** '。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/44.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/45.png?raw=true)

**问题：**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/46.png?raw=true)

（**解决：**可能是依赖包没下载完全，在第二条命令后面加上 --skip-broken 后安装一次，再按原命令安装一次就可以了）

#### **创建新的集群配置**

* 创建新的群集目录

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/47.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/48.png?raw=true)

* 使用“ **ceph** **-deploy** ”命令创建一个新的集群配置，将监视节点定义为“ **mon1** ”，该命令将在集群目录中生成Ceph集群配置文件'ceph.conf'。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/49.png?raw=true)

* 编辑ceph.conf文件，在[global]块下，添加配置

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/50.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/51.png?raw=true)

#### **在所有节点上安装Ceph**

从ceph-admin节点在所有其他节点上安装Ceph这可以通过单个命令完成。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/52.png?raw=true)

#### ~~**在安装上卡住了，在所有节点上用以下方法**~~

>yum -y install epel-release
>
>yum -y install yum-plugin-priorities
>
>sudo yum install -y https://download.ceph.com/rpm-jewel/el7/noarch/ceph-release-1-0.el7.noarch.rpm
>
>
>
>添加ceph仓库：
>
>sudo vi /etc/yum.repos.d/ceph.repo
>
>#添加如下配置
>
>[ceph]
>
>name=Ceph packages for $basearch
>
>baseurl=http://mirrors.163.com/ceph/rpm-jewel/el7/$basearch
>
>enabled=1
>
>gpgcheck=0
>
>priority=1
>
>type=rpm-md
>
>gpgkey=http://mirrors.163.com/ceph/keys/release.asc
>
>[ceph-noarch]
>
>name=Ceph noarch packages
>
>baseurl=http://mirrors.163.com/ceph/rpm-jewel/el7/noarch
>
>enabled=1
>
>gpgcheck=0
>
>priority=1
>
>type=rpm-md
>
>gpgkey=http://mirrors.163.com/ceph/keys/release.asc
>
>[ceph-source]
>
>name=Ceph source packages
>
>baseurl=http://mirrors.163.com/ceph/rpm-jewel/el7/SRPMS
>
>enabled=0
>
>gpgcheck=0
>
>type=rpm-md
>
>gpgkey=http://mirrors.163.com/ceph/keys/release.asc
>
>priority=1
>
>#####################################（配置结束分界线）
>
>yum -y install ceph ceph-radosgw

* ceph-admin

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/53.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/54.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/55.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/56.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/57.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/58.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/59.png?raw=true)

**（mon1，osd1，osd2用同上步骤安装ceph，以下再给出mon1的详细步骤，osd1，osd2同上）**

* mon1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/60.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/61.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/62.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/63.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/64.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/65.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/66.png?raw=true)

* osd1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/67.png?raw=true)

* osd2

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/68.png?raw=true)

下面这一句需要mon1节点执行，修改主机名为mon1

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/69.png?raw=true)

* 将ceph-mon部署在mon1节点上。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/70.png?raw=true)

* 该命令将创建监视键，并使用“ ceph”命令检查并获取键。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/71.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/72.png?raw=true)

#### 添加OSD节点

* 添加两个OSD进程

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/73.png?raw=true)

* 从管理节点执行ceph-deploy来准备OSD

**这里要到cluster目录下执行命令要不然会提示找不到ceph.conf文件**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/74.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/75.png?raw=true)

* 激活OSD

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/76.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/77.png?raw=true)

* 用ceph-deploy把配置文件和admin密钥复制到管理节点和Ceph节点

**这样每次执行Ceph命令时就不用指定monitor地址和ceph.client.admin.keyring**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/78.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/79.png?raw=true)

* 确保对ceph.client.admin.keyring有正确的操作权限

**（在所有节点上执行该命令）**

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/80.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/81.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/82.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/83.png?raw=true)

* 查看集群健康运行状况和集群状态。

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/84.png?raw=true)

![](https://github.com/eric-ruhu/CloudComputing/blob/master/Ceph/ceph_images/85.png?raw=true)

**可以看到成功构建了一个新的Ceph集群！！！！！**

参考：

书＋[教程](https://www.howtoforge.com/tutorial/how-to-build-a-ceph-cluster-on-centos-7/#install-cephdeploy-on-the-cephadmin-node)

### **感谢朱朱老师和CTO的鼎力相助！！！**

