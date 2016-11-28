---
title: ubuntu14.04配置android开发环境 
date: 2016-11-28 19:37:43
header-img: /img/post-default.jpg
tags:
    - Android
    - Linux
---
## 如何在linux下搭建android开发环境？
## 一、JDK配置
### 1、软件下载
在[Oracle官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载对应系统版本的jdk, 我的下载后的软件为`jdk-8u111-linux-x64.tar.gz`，然后用以下命令解压：
```
tar -xvf ~/Downloads/jdk-8u111-linux-x64.tar.gz　#解压jdk，解压后在相同目录里有一个文件夹
sudo mkdir -p /usr/local/jvm      #新建一个文件夹
sudo mv jdk1.8.0  /usr/local/jvm/jdk1.8.0_111 #把解压的 JDK 文件内容都移动到创建的目录中
```

### 2、添加jdk到系统环境变量
``` bash
cp /etc/profile /etc/profile.bak #备份
sudo vim /etc/profile #编辑,在最后添加下面的内容
```
```
export JAVA_HOME=/usr/local/jvm/jdk1.8.0_111
export JRE_HOME=${JAVA_HOME}/jre 
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib 
export PATH=${JAVA_HOME}/bin:$PATH
```
```
source /etc/profile 　　　#使配置文件立即生效
```
由于系统中可能会有默认的其他版本JDK，所以，为了将我们安装的JDK设置为默认JDK版本，还要进行如下工作。
```
sudo update-alternatives --install /usr/bin/java java /usr/local/jvm/jdk1.8.0_111/bin/java 1  
sudo update-alternatives --install /usr/bin/javac  javac /usr/local/jvm/jdk1.8.0_111/bin/javac 1
```
执行下面命令，设置默认版本，此命令执行后，系统会列出当前存在的各种JDK版本，会提示你选择,然后根据提升选择编号。
```
sudo update-alternatives --config java
```
### 3、测试是否OK
```
java -version # 测试是否成功 
```
如果ok,会出现以下信息：
```
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)
```
## 二、Android Studio下载和配置
### 1、软件下载
android studio的安装可以按照官方网站[推荐操作](https://developer.android.com/studio/install.html)进行。我下载的版本为`android-studio-ide-145.3360264-linux.zip`，然后按操作执行：
```
unzip android-studio-ide-145.3360264-linux.zip
sudo android-studio /usr/local
cd /usr/local/android-studio/bin
./studio.sh #选择是否想要导入之前的 Android Studio 设置，然后点击 OK
```
注意, 在/etc/profile里添加以下内容即可通过终端输入`studio.sh`打开
```
export ANDROID_STUDIO=/usr/local/android-studio
export PATH=${ANDROID_STUDIO}/bin:$PATH
```
之后 Android Studio 设置向导将指导您完成余下的设置，包括下载开发所需的 Android SDK 组件。

>* 注：如果您运行的是 64 位版本 Ubuntu，则您需要使用以下命令安装一些 32 位库：
```
sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6
```
>* 如果您运行的是 64 位版本的 Fedora，则所用命令为：
```
sudo yum install zlib.i686 ncurses-libs.i686 bzip2-libs.i686
```

### 2、配置android sdk
按照默认操作会将android sdk　默认下载到~/Android文件夹下面,在 `file->Other Settings->Default Project Structure`下可设置jdk路径，以及更新ndk.

over, enjoy it~
