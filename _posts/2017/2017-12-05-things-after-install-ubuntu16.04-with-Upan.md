---
layout: post
title: 使用u盘安装ubuntu16.04的那些事儿
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

## 安装开发工具

* [安装激活goland2017.3版本](2017-12-05-active-goland-2017.3.md)
* [安装LiteIDE](https://golangtc.com/download/liteide)

    tar jxvf liteidex32.1.linux64-qt4.tar.bz2
    cd liteide
    编辑liteide.desktop,根据自己的路径修改exec和icon配置
    然后拷贝该配置到$HOME/.local/share/applications下
    cd $HOME/liteide/bin/
    启动liteide
    并锁定到启动器上即可。

* [安装vscode指引](https://code.visualstudio.com/docs/setup/linux)
* [安装atom](http://www.linuxidc.com/Linux/2014-05/101501.htm)

    sudo add-apt-repository ppa:webupd8team/atom
    sudo apt-get update
    sudo apt-get install atom

## 安装有道字典

安装有道词典的[1.0.2版本](http://codown.youdao.com/cidian/linux/youdao-dict_1.0.2~ubuntu_amd64.deb)，据说1.1.0版本有依赖问题。暂时不要挑战了。

```sh
   zero@zero:/media/zero/Myself/Ubuntu1604$ sudo dpkg -i youdao-dict_1.0.2~ubuntu_amd64.deb
     正在选中未选择的软件包 youdao-dict。
     (正在读取数据库 ... 系统当前共安装有 218440 个文件和目录。)
     正准备解包 youdao-dict_1.0.2~ubuntu_amd64.deb  ...
     正在解包 youdao-dict (1.0.2~ubuntu) ...
     dpkg: 依赖关系问题使得 youdao-dict 的配置工作不能继续：
      youdao-dict 依赖于 python3-pyqt5；然而：
       未安装软件包 python3-pyqt5。
      youdao-dict 依赖于 python3-xlib；然而：
       未安装软件包 python3-xlib。
      youdao-dict 依赖于 tesseract-ocr；然而：
       未安装软件包 tesseract-ocr。
      youdao-dict 依赖于 tesseract-ocr-eng；然而：
       未安装软件包 tesseract-ocr-eng。
      youdao-dict 依赖于 tesseract-ocr-chi-sim；然而：
       未安装软件包 tesseract-ocr-chi-sim。
      youdao-dict 依赖于 tesseract-ocr-chi-tra；然而：
       未安装软件包 tesseract-ocr-chi-tra。
      youdao-dict 依赖于 ttf-wqy-microhei；然而：
       未安装软件包 ttf-wqy-microhei。
      youdao-dict 依赖于 python3-pyqt5.qtmultimedia；然而：
       未安装软件包 python3-pyqt5.qtmultimedia。
      youdao-dict 依赖于 python3-pyqt5.qtquick；然而：
       未安装软件包 python3-pyqt5.qtquick。
      youdao-dict 依赖于 python3-pyqt5.qtwebkit；然而：
       未安装软件包 python3
     dpkg: 处理软件包 youdao-dict (--install)时出错：
      依赖关系问题 - 仍未被配置
     正在处理用于 hicolor-icon-theme (0.15-0ubuntu1) 的触发器 ...
     正在处理用于 gnome-menus (3.13.3-6ubuntu3.1) 的触发器 ...
     正在处理用于 desktop-file-utils (0.22-1ubuntu5.1) 的触发器 ...
     正在处理用于 bamfdaemon (0.5.3~bzr0+16.04.20160824-0ubuntu1) 的触发器 ...
     Rebuilding /usr/share/applications/bamf-2.index...
     正在处理用于 mime-support (3.59ubuntu1) 的触发器 ...
     在处理时有错误发生：
      youdao-dict
  zero@zero:/media/zero/Myself/Ubuntu1604$ sudo apt-get install python3-pyqt5
  正在读取软件包列表... 完成
  正在分析软件包的依赖关系树
  正在读取状态信息... 完成
  您可能需要运行“apt-get -f install”来纠正下列错误：
  下列软件包有未满足的依赖关系：
   python3-pyqt5 : 依赖: libqt5designer5 (>= 5.0.2) 但是它将不会被安装
                   依赖: libqt5help5 (>= 5.3.0) 但是它将不会被安装
                   依赖: sip-py3api-11.2
   youdao-dict : 依赖: python3-xlib 但是它将不会被安装
                 依赖: tesseract-ocr 但是它将不会被安装
                 依赖: tesseract-ocr-eng 但是它将不会被安装
                 依赖: tesseract-ocr-chi-sim 但是它将不会被安装
                 依赖: tesseract-ocr-chi-tra 但是它将不会被安装
                 依赖: ttf-wqy-microhei
                 依赖: python3-pyqt5.qtmultimedia 但是它将不会被安装
                 依赖: python3-pyqt5.qtquick 但是它将不会被安装
                 依赖: python3-pyqt5.qtwebkit 但是它将不会被安装
                 依赖: qtdeclarative5-controls-plugin 但是它将不会被安装
  E: 有未能满足的依赖关系。请尝试不指明软件包的名字来运行“apt-get -f install”(也可以指定一个解决办法)。
  zero@zero:/media/zero/Myself/Ubuntu1604$ sudo apt-get -f install
  正在读取软件包列表... 完成
  正在分析软件包的依赖关系树
  正在读取状态信息... 完成
  正在修复依赖关系... 完成
  将会同时安装下列软件：
    fonts-wqy-microhei liblept5 libopenjp2-7 libqt5clucene5 libqt5designer5
    libqt5help5 libqt5multimediawidgets5 libtesseract3 python3-pyqt5
    python3-pyqt5.qtmultimedia python3-pyqt5.qtquick python3-pyqt5.qtwebkit
    python3-sip python3-xlib qml-module-qtquick-controls
    qtdeclarative5-controls-plugin tesseract-ocr tesseract-ocr-chi-sim
    tesseract-ocr-chi-tra tesseract-ocr-eng tesseract-ocr-equ tesseract-ocr-osd
    ttf-wqy-microhei
  建议安装：
    python3-pyqt5-dbg
  下列【新】软件包将被安装：
    fonts-wqy-microhei liblept5 libopenjp2-7 libqt5clucene5 libqt5designer5
    libqt5help5 libqt5multimediawidgets5 libtesseract3 python3-pyqt5
    python3-pyqt5.qtmultimedia python3-pyqt5.qtquick python3-pyqt5.qtwebkit
    python3-sip python3-xlib qml-module-qtquick-controls
    qtdeclarative5-controls-plugin tesseract-ocr tesseract-ocr-chi-sim
    tesseract-ocr-chi-tra tesseract-ocr-eng tesseract-ocr-equ tesseract-ocr-osd
    ttf-wqy-microhei
  升级了 0 个软件包，新安装了 23 个软件包，要卸载 0 个软件包，有 1 个软件包未被升级。
  有 1 个软件包没有被完全安装或卸载。
  需要下载 55.1 MB 的归档。
  解压缩后会消耗 191 MB 的额外空间。
  您希望继续执行吗？ [Y/n] y
```

## 其他操作

* [微信&QQ工具](http://blog.csdn.net/zhangrelay/article/details/52398037)
* [linux版本的wechat](http://blog.csdn.net/minione_2016/article/details/53289430?locationNum=4&fps=1)
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