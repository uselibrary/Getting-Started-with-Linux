 # mjj版的linux入门教程



本文的首要目的是给予Linux初学者一个简单、易学的教程，以便在看完本文后对Linux系统有一个基础的认识（而非系统级的深入），可以对常见的软件和功能进行配置，甚至可以自己写一写一键脚本。

本教程写于2021年下半年，采用的系统为Debian GNU/Linux 11 (bullseye)。



## 0 前言吐槽CentOS

**解释使用Debian而不是CentOS的原因**

国内首批接触Linux系统的人主要集中在科研院校，大多数是延续了Unix-like的背景，在千禧年前后才有了真正意义上的Linux使用者：纯Linux平台开发、运行服务和应用，他们或直接或间接地推广了Linux系统。红帽（Red Hat, Inc.）在1994年就开始发布了同名的操作系统：Red Hat Linux（后改组为Red Hat Enterprise Linux，缩写为RHEL）。得益于红帽优秀的团队和商业支持，RHEL这一发行版迅速占领了国内市场。彼时的国内计算机市场远不如今日繁荣，在口口相传和红帽的推广中，RHEL成为了Linux入门的主流选项，即使后来号称用户用好的Ubuntu出现了，绝大多数尝鲜的人依然能看到众多网站里面只提供RHEL版本的教程。

CentOS是根据RHEL的源码重新编译的，等于换商标版本的RHEL，软件层面上，两者无本质区别。但CentOS是反人类的，至少是反入门用户的。使用RHEL的基本为商业用户，可以付费获得红帽的技术支持，或者干脆有一个自己的维护团队；而CentOS作为一个社区自发形成的操作系统，拥有落后的软件源/包，繁琐的配置，和对个人用户而言根本没有必要的SElinux等。举个例子，很多入门者修改SSH端口的时候，发现所有的操作都没有问题，但是死活无法生效，最终发现是没有在SElinux里面放行。如果你想安装个软件，你就得考虑是从落后主流版本好几代的软件源/包里面安装，还是自己下载源码进行编译以获取主流的使用体验。对于入门者而言，CentOS的安全性和稳定性是个虚假的概念，毕竟让一个刚接触Linux的人去自己编译源码安装，无异于让小学生上战场，输了就说是小学生战斗力太弱。

所以本文以Debian GNU/Linux（后续简称为Debian）来演示，也有着推广Debian的意思在里面，毕竟相比于Ubuntu往系统里面塞包括snap在内的一系列私货而言，Debain始终遵循着一个纯净的Linux的要求。而其他一些发行版，要么是专用性太强（如SUSE），要么是入门者不友好（如 Arch Linux），权衡之后，选择了写本文时，最新的Debian系统，即Debian GNU/Linux 11 (bullseye)。



## 1 环境搭建

### 1.1 系统选择与安装

Debian的安装包有一系列的前缀或者后缀，例如在默认的下载地址`https://www.debian.org/download`中的是`debian-11.0.0-amd64-netinst.iso`。其中，
- 11代表大版本是11，代号是bullseye，各版本代号都来源于电影《玩具总动员》中的角色名称；
- amd64是指系统为64位的，i386或者x86是32位的，amd64或者x86-64是64位的，32位系统已经被逐步弃用，目前仅在特定行业中使用；
- netinst是网络安装版本，只是个安装器，安装过程需要联网，而DVD后缀的是完整版（如：debian-11.0.0-amd64-DVD-1.iso），如果系统太大，会在DVD后面加数字，默认DVD-1是完整版本，其后数字的是软件源/包；
- 带firmware前缀的是包含第三方非开源驱动的（如：firmware-11.1.0-amd64-DVD-1.iso），其中就包含intel和Realtek等公司的闭源网卡驱动。


VPS全称为virtual private server（虚拟专用服务器），如果需要安装纯净版的Debian 11系统，推荐使用vicer的Linux一键重装脚本（如下）：

```shell
bash <(wget --no-check-certificate -qO- 'https://raw.githubusercontent.com/MoeClub/Note/master/InstallNET.sh') -d 11 -v 64 -p "自定义root密码" -port "自定义ssh端口"
```



###  1.2 常用的命令

`cat` 用于查看文本文件的内容，如`cat /etc/os-release` 将显示系统信息，如下：

```
PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"
NAME="Debian GNU/Linux"
VERSION_ID="11"
VERSION="11 (bullseye)"
VERSION_CODENAME=bullseye
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

`touch` 新建文本文件，如`touch /home/hello.py` 将在`home` 文件夹下新建一个Python文件。

`cd` 进入指定文件夹，如`cd /home` 将进入`home`目录。

`mkdir` 新建文件夹，如`mkdir /home/Python` 将在`home` 文件夹下新建一个`Python` 文件夹。

`mv` 移动文件和文件夹，也可以用来修改名称，如`mv /home/hello.py /home/helloworld.py` 将上文的`hello.py`重命名为`helloworld.py`，`mv /home/helloworld.py /home/Python/helloworld.py` 将`helloworld.py` 由`home`文件夹移动到了次级的`Python`文件夹。

`cp` 复制文件，`cp /home/Python/hellowrold.py /home/Python/HelloWorld.py` 将`helloworld.py`复制为`HelloWolrd.py`。注意：Linux系统严格区分大小写，`helloworld.py`和`HelloWolrd.py`是两个文件。如果想复制整个文件夹，则需要带`r`，即`cp -r`，但此命令无法复制隐藏文件夹，需要使用`cp -r pathA/. pathB` 注意这个点`.`是灵魂。

`rm` 删除，即江湖传说中`rm -rf` ，`r`为递归，可以删除文件夹中的文件，`f`为强制删除。`rm /home/Python/helloworld.py` 可以删除刚才的`helloworld.py` 文件，而想删除包括`Python` 在内的所有文件，则是`rm -rf /home/Python` 。

`du -lh` 查看当前文件夹下，各文件、文件夹的大大小，`l`是硬链接（软连接类似于快捷方式），`h`是让文件自动使用K/M/G显示而不是只有K。



### 1.3 基础文本编辑器nano

Linux系统的一大优势（也是劣势）是默认不需要GUI，因此节省了大量的性能开支，无GUI版本的Debian 11可以在512M甚至更小内存的VPS上正常启动和运行。但缺少GUI加大了入门者修改文件的难度，所幸Debian 11自带了简便易用的nano文本编辑器。

```shell
nano /home/helloworld.py
```





## 2 SSH连接和基础配置

一般VPS供应商都会提供SSH的链接方式，包括用户名，密码和端口号，一些注重安全性的会修改端口号甚至只有采用密钥才能登陆VPS。这里使用纯净版的系统和默认配置进行演示。

### 2.1 连接SSH的软件和相关操作



### 2.2 ssh配置文件介绍和修改

ssh的配置文件在`/etc/ssh/sshd_config`中，是一个纯文本文件，可以使用`nano`或者`vim`打开和编辑，`vim`功能强大但是对入门者不友好，`nano`功能简单但是便于操作。



### 2.3 使用密钥登陆SSH



## 3 Linux文件系统



## 4 Shell入门



## 5 Crontab定时任务



## 6 系统权限

### 6.1 root和user

### 6.2 用户组



## 7 Systemd入门和配置

### 7.1 进程守护

### 7.2 Timer代替Crontab



## 8 网站环境搭建



### 8.1 宝塔解人忧



### 8.2 手动搭建

#### 8.2.1 Apache和Nginx

#### 8.2.2 PHP

#### 8.2.3 MySQL和MariaDB



## 9 手动配置系统：以syncthing为例

