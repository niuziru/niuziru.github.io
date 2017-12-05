---
layout: post
title: 配置Tomcat以指定的身份(非root)运行
categories:
- Technology
tags:
- Tomcat
---

转载: https://www.cnblogs.com/allegro/p/5005352.html

# 本文依赖的环境：

* CentOS（大部分内容适用于其他Linux发行版）

* 已安装并配置好JVM环境

* 已安装并配置好gcc、make等编译工具

# 1. 下载Tomcat安装包并解压缩

```bash
cd /opt
wget http://www.us.apache.org/dist/tomcat/tomcat-6/v6.0.44/bin/apache-tomcat-6.0.44.tar.gz
tar zxvf apache-tomcat-6.0.44.tar.gz
mv apache-tomcat-6.0.44/ tomcat6/
```

# 2. 编译安装服务守护程序

```bash
cd /opt/tomcat6/bin/
tar vzxf commons-daemon-native.tar.gz
cd /opt/tomcat6/bin/commons-daemon-1.0.15-native-src/unix/
./configure
     如果出现如下错误提示，说明未设置JAVA_HOME变量：

*** Java compilation tools ***
checking for JDK location... configure: error: Java Home not defined. Rerun with --with-java=... parameter
    可以通过命令行参数指定JDK位置

./configure --with-java=/opt/jdk
    配置成功后，开始编译。

make
    执行make后会生成一个jsvc的文件，将其复制到tomcat的bin目录

cp jsvc /opt/tomcat6/bin
```

# 3. 建立一个用来运行Tomcat的用户

```bash
useradd tomcat -M -d / -s /usr/sbin/nologin
```
# 4. 修改启动脚本文件

```bash
vi /opt/tomcat6/bin/daemon.sh
    找到如下内容

test ".$TOMCAT_USER" = . && TOMCAT_USER=tomcat
# Set JAVA_HOME to working JDK or JRE
# JAVA_HOME=/opt/jdk-1.6.0.22
    修改TOMCAT_USER=tomcat为你指定用来运行tomcat的用户名，去除JAVA_HOME前的注释（即“#”号），并设置为JDK的所在目录。

    在文件靠前位置的注释中加入下面的内容

# chkconfig: - 80 20
```

# 5. 修改相关目录和文件权限

```bash
chown -R tomcat:tomcat /opt/tomcat6
chmod a+x /opt/tomcat6/bin/daemon.sh
```

现在可以使用这个脚本来操作tomcat了：

```bash
启动:
/opt/tomcat/bin/daemon.sh  start
停止:
/opt/tomcat/bin/daemon.sh  stop
前台运行:
/opt/tomcat/bin/daemon.sh  run
查看版本:
/opt/tomcat/bin/daemon.sh  version
```

# 6. 添加tomcat为CentOS的服务

```bash
ln -s /opt/tomcat/bin/daemon.sh /etc/init.d/tomcat6
chkconfig –-add tomcat6
    随后就可以用chkconfig命令开启/关闭服务的运行状态了。
```