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



### 1.3 基础文本编辑器nano、vim

Linux系统的一大优势（同时也是劣势）是默认不需要GUI，因此节省了大量的性能开支，无GUI版本的Debian 11可以在512M甚至更小内存的VPS上正常启动和运行。但缺少GUI加大了入门者修改文件的难度，所幸Debian 11自带了简便易用的nano文本编辑器。以下以修改系统的更新源为例

```shell
nano /etc/apt/sources.list #打开sources.list文件，在Linux系统中，#是注释符，其后的内容会被忽略
```

![nano_ui](images\nano_ui.jpg)

如图所示，即为`nano`打开`sources.list`后的界面，最下面两行为提示，比如`Ctrl+E`为退出，如果文档本被改动了，则会出现下图，询问是否保存。如果没有被更改，则会直接退出。

![nano_ctrl_e](images\nano_ctrl_e.jpg)

`Y`则保存，`N`则不保存，`Ctrl+C`取消操作。此处输入`Y`，则会如下图：

![nano_yes.jpg](images\nano_yes.jpg)

此时按下`Enter`键就会保存了。



这里多提一句关于Debian 11的更新源内容，一般是以下6行。

```shell
deb http://deb.debian.org/debian bullseye main contrib non-free
deb-src http://deb.debian.org/debian bullseye main contrib non-free

deb http://deb.debian.org/debian-security/ bullseye-security main contrib non-free
deb-src http://deb.debian.org/debian-security/ bullseye-security main contrib non-free

deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free
```

deb表示为已经编译好的安装包，类似于Windows上的MSI安装包，deb-src是源文件，万一没有打包好，提供自己本地编译安装的机会。总共分三大行，第一行是系统主文件，第二行是安全性更新，第三个是一些更新补充，推荐三个都写上。在每行的末尾都有`main contrib non-free`字样，其中`main`是官方给的包/源，严格遵守相关开源协议；`contrib`是包/源本身遵守相关开源协议，但是它们的依赖则不是；`non-free`是私有的软件，比如上文提到的Realtek的WiFi驱动等。除此之外，其实还有个`Backports`作为第四大行，是将比较陈旧的软件移植过来的，很少会用到，一般默认不写上。



nano虽然好，但是功能简单，只适合一些简单的文本文件编辑功能，而发展自vi的vim则被成为编辑器之神（Emacs被称为神之编辑器，Linux之父Linus Torvalds就在用）。系统会自带vi但是不带vim，正好我们可以使用上述修改过的更新源来安装vim作为示例。

```
apt update # 更新一下源
apt install vim -y #安装vim这个软件 -y是确认安装
```

使用`vim /etc/apt/sources.list`打开更新源文件，如下图所示：

![vim_ui](images\vim_ui.jpg)

vim功能众多，使用复杂，得慢慢说。左下角是此文件的路径和名称，右下角是光标此时的行数和列数。此时是无法直接输入，比如先按下`insert`或者`i`键变成插入模式才行。此时，左下角如下图，变成了INSERT/插入模式。

![vim_s1](images\vim_s1.jpg)

然后就是该怎么写就怎么写，一些快捷键去百度谷歌必应吧，说的肯定比我详细。但是必须提到如何保存文件：`insert`模式下按`esc`键（一般是键盘最左上角，99%的人可能都不怎么用的一个键），INSERT会消失不见，如下图：

![vim_s3](images\vim_s3.jpg)

这个时候再按下`:`键，界面上也会出现一个冒号，如下图。注意，这个冒号是半角的，全角冒号是没用的。

![vim_s2](images\vim_s2.jpg)

这个时候，按下`wq`者两个键，即可保存内容。w是write/写入的意思，q是quit/退出的意思。如果你不想保存，则只输入q键即可，但是有时候因为文件已经被修改了，vim不让退出，这时候输入`q!`就可以了，冒号是强制执行的意思，执行后文件不会被修改并且会退出vim。



### 1.4 更新系统

至此，不管是使用nano还是vim都可以对更新源进行编辑了，让我们来具体了解一下如果更新系统和相关指令。

```shell
apt update
apt list --upgradable
apt upgrade -y
```

以上三行，分别是和更新源同步，显示出哪些软件可以更新，以及进行更新。

如上文中，安装了vim，若想卸载vim，则有以下两个命令，任意一个即可，但之间存在差别。

```shell
apt remove vim -y
apt purge vim -y
```

第一个会址卸载vim软件本身，配置文件仍然会本留下；第二种连带着配置文件和相关依赖一起卸载了，所以存在一定风险。除此之外，`apt autoremove`是对整个系统进行整理，将不需要的依赖卸载了，不针对于特定软件。



## 2 SSH连接和基础配置

一般VPS供应商都会提供SSH的链接方式，包括用户名，密码和端口号，一些注重安全性的会修改端口号甚至只有采用密钥才能登陆VPS。这里使用纯净版的系统和默认配置进行演示。

### 2.1 连接SSH的软件和相关操作

SSH软件有开源的和不开源的，有付费的和免费的，整理除了一个常见SSH客户端的表格，平台为Windows。macos、Linux和2021年以后的windows 10，都自带SSH功能，这里就不讨论了。目前自用xshell+mobaxterm两个。

| 名称       | 免费与否              | 备注                                                   |
| ---------- | --------------------- | ------------------------------------------------------ |
| Xshell     | 官网的家庭/教育版免费 | 功能简单，启动速度快，传输文件需要配合xftp             |
| Mobaxterm  | 个人免费              | 功能极其完整，但是有点完整过头了                       |
| finalshell | 可选免费              | 国人开发，所以很容易上手，但是开通功能需要付费         |
| putty      | 免费                  | 老牌SSH客户端，但是部分操作反人类，不建议              |
| electerm   | 免费                  | 可以通过GitHub同步保存信息，基于electron开发的而占内存 |



### 2.2 SSH配置文件介绍和修改

SSH的配置文件在`/etc/ssh/sshd_config`中，是一个纯文本文件，可以使用`nano`或者`vim`打开和编辑。



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

