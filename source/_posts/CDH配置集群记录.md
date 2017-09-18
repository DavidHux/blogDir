---
title: CDH配置集群记录
date: 2017-09-05 18:02:03
tags: InstallInstruction
---
CDH is Cloudera’s 100% open source platform distribution, including Apache Hadoop and built specifically to meet enterprise demands. CDH delivers everything you need for enterprise use right out of the box. By integrating Hadoop with more than a dozen other critical open source projects, Cloudera has created a functionally advanced system that helps you perform end-to-end Big Data workflows.

这篇博客用来记录使用CDH安装工具部署集群的实验。

## 环境基础

### 硬件

实验集群包括四个节点，每个节点的物理内存16g，硬盘容量40g.

### 软件

集群四个节点使用的操作系统为Ubuntu 16.04(Xenial).  
CDH安装工具版本 CM 5.12.0  

安装后的各个服务版本号：

hadoop: 2.6.0
spark: 1.6.0 using scala version 2.10.5
HBase: 1.2.0
Hive: 1.1.0

## 安装前准备工作

官网 [安装指导网站](https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cm_ig_install_spark.html)

### 安装前需要先做的工作：

1. 修改每个节点的hosts文件

``` bash
$ vim /etc/hosts
```

添加每个节点的ip信息，注释掉127.0.1.1的地址

``` bash
114.212.82.50   u50
114.212.83.36   u36
114.212.81.68   u68
114.212.82.62   u62
```

2. 保证每个节点可以以root用户身份登陆

设置root用户密码

``` bash
$ sudo -s
$ passwd
```

输入密码。

允许以root用户身份ssh登陆

``` bash
$vim /etc/ssh/sshd_config
```

注释掉

``` bash
PermitRootLogin without-password
```

添加

``` bash
PermitRootLogin yes
```

保存退出

``` bash
sudo service ssh restart
```

## 开始安装

下载 [cloudera-manager-installer.bin](https://www.cloudera.com/documentation/enterprise/5-4-x/topics/cm_ig_install_path_a.html#cmig_topic_6_5_1)

修改权限

``` bash
$ chmod u+x cloudera-manager-installer.bin
```

在主节点运行该程序安装Cloudera Manager Server：

``` bash
$ sudo ./cloudera-manager-installer.bin
```

安装完成后，该服务运行在7180端口了，打开web页面http://hostip:7180，可以根据提示提加新的集群。

安装过程根据提示即可进行。

## 额外的

由于及其联接外网需要连接bras，大部分情况下集群里的机器是没有连接外网的，cloudra manager service 会提示集群中主机存在时间同步的问题，没有连接到远程服务器，可以修改ntp服务的配置文件，添加校内server。

``` bash
$ vim /etc/ntp.conf
```

添加一行，重启

``` bash
server ntp.nju.edu.cn
```
