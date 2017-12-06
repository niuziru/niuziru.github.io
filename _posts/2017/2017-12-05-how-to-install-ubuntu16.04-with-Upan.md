---
layout: post
title: 如何使用u盘安装ubuntu16.04
categories:
- Technology
tags:
- 系统
---


# 使用upan系统制作工具
* 不要使用ultraISO工具
* 使用 [easyBCD](http://neosmart.net/EasyBCD/) 或者 [Universal-USB-Installer](https://www.pendrivelinux.com/universal-usb-installer-easy-as-1-2-3/) 工具

# 系统安装完毕后

## 更新源

### 如何更改源

可以在软件更新中选择源,
使用如下命令更改(修改前先备份):

    sudo vim /etc/apt/source.list

### 阿里云

```sh
deb http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse
```

### 东北大学

```sh
deb-src http://mirror.neu.edu.cn/ubuntu/ xenial main restricted #Added by software-properties
deb http://mirror.neu.edu.cn/ubuntu/ xenial main restricted
deb-src http://mirror.neu.edu.cn/ubuntu/ xenial restricted multiverse universe #Added by software-properties
deb http://mirror.neu.edu.cn/ubuntu/ xenial-updates main restricted
deb-src http://mirror.neu.edu.cn/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties
deb http://mirror.neu.edu.cn/ubuntu/ xenial universe
deb http://mirror.neu.edu.cn/ubuntu/ xenial-updates universe
deb http://mirror.neu.edu.cn/ubuntu/ xenial multiverse
deb http://mirror.neu.edu.cn/ubuntu/ xenial-updates multiverse
deb http://mirror.neu.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirror.neu.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties
deb http://archive.canonical.com/ubuntu xenial partner
deb-src http://archive.canonical.com/ubuntu xenial partner
deb http://mirror.neu.edu.cn/ubuntu/ xenial-security main restricted
deb-src http://mirror.neu.edu.cn/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties
deb http://mirror.neu.edu.cn/ubuntu/ xenial-security universe
deb http://mirror.neu.edu.cn/ubuntu/ xenial-security multiverse
```

### 清华大学

```sh
# deb cdrom:[Ubuntu 16.04 LTS _Xenial Xerus_ - Release amd64 (20160420.1)]/ xenial main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security multiverse
```

## 安装gofont字体

从github下载gofont, `git clone https://github.com/golang/image.git`, 拷贝`font/gofont/ttfs/`下面的字体到系统字体中：

```sh
sudo mkdir /usr/share/fonts/gofonts
sudo cp font/gofont/ttfs /usr/share/fonts/gofonts
cd /usr/share/fonts/gofonts
sudo chmod 744 *
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -f -v
```

接着注销ubuntu，再次登录就可以使用该go字体了。

## 其他操作
* [Ubuntu 16.04 LTS安装好需要设置的15件事](http://www.linuxidc.com/Linux/2016-04/130519.htm)
* [Ubuntu 安装terminator 后更改默认终端设置](http://blog.csdn.net/qq_20336817/article/details/46122849)

```sh
$ sudo apt-get install terminator
$ sudo apt-get install dconf-tools

若想换回terminal
    exec  gnome-terminal
    exec-arg -x
否则：
    exec  x-terminal-emulator
    exec-arg -e
```

* [Ubuntu 16.04安装sogou 拼音输入法](http://blog.csdn.net/ljheee/article/details/52966456)
*