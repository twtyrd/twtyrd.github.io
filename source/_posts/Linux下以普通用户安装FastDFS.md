---
title: Linux下以普通用户安装FastDFS.md
tags: 
- FastDFS
categories:
- FastDFS
---

## 获取安装包 

FastDFS：[https://github.com/happyfish100/fastdfs/releases](https://github.com/happyfish100/fastdfs/releases)

libfastcommon：[https://github.com/happyfish100/libfastcommon/releases](https://github.com/happyfish100/libfastcommon/releases)

本文将以`fastdfs-5.12.tar.gz`和`libfastcommon-1.0.40.tar.gz`进行安装实践

<!-- more -->

## 安装libfastcommon

### 创建安装目录
`mkdir -pv $HOME/fastdfs`
### 解压安装包
`tar -zxvf libfastcommon-1.0.40.tar.gz`
### 设置环境变量，用于指定安装目录
`export DESTDIR=$HOME/fastdfs`
### 依次执行以下命令，完成安装
`cd libfastcommon-1.0.40`

`./make.sh`

`./make.sh install`
### 在`~/.profile`文件中添加以下环境变量
`export LD_LIBRARY_PATH=$HOME/fastdfs/usr/lib64:$LD_LIBRARY_PATH`

`source .profile`

## 安装FastDFS

### 解压安装包`tar -zxvf fastdfs-5.12.tar.gz`
### 进入解压后的目录`cd fastdfs-5.12`
### 修改`make.sh`文件，指定配置文件存放目录
修改以下内容：
```bash
      if [ ! -d /etc/fdfs ]; then
        mkdir -p /etc/fdfs
```
为：
```bash
if [ ! -d $TARGET_CONF_PATH ]; then
        mkdir -p $TARGET_CONF_PATH
```
### 修改`tracker`目录下的`Makefile.in`文件，指定库路径
修改以下内容：
```bash
INC_PATH = -I../common -I/usr/local/include
LIB_PATH = $(LIBS) -lfastcommon
```
为：
```bash
INC_PATH = -I../common -I${DESTDIR}/usr/include
LIB_PATH = $(LIBS) -L${DESTDIR}/usr/lib64 -lfastcommon
```
### 同`tracker`，完成`storage``和``client`目录下的`Makefile.in`文件修改
修改`storage/Makefile.in`以下内容：
```bash
INC_PATH = -I. -Itrunk_mgr -I../common -I../tracker -I../client -Ifdht_client -I/usr/include/fastcommon
LIB_PATH = $(LIBS)  -lfastcommon
```
为：
```bash
INC_PATH = -I. -Itrunk_mgr -I../common -I../tracker -I../client -Ifdht_client -I${DESTDIR}/usr/include
LIB_PATH = $(LIBS)  -L${DESTDIR}/usr/lib64 -lfastcommon
```
修改`client/Makefile.in`以下内容：
```bash
INC_PATH = -I../common -I../tracker -I/usr/include/fastcommon
LIB_PATH = $(LIBS) -lfastcommon
```
为：
```bash
INC_PATH = -I../common -I../tracker -I${DESTDIR}/usr/include
LIB_PATH = $(LIBS) -L${DESTDIR}/usr/lib64 -lfastcommon
```
### 编译安装
`./make.sh`

`./make.sh install`