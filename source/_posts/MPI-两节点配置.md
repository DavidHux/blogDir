---
title: MPI-两节点配置
date: 2017-09-20 19:10:29
tags: 
 - configure
 - shell
categories: installation
---

这篇BLOG用来记录配置两台主机并行跑MPI程序的实验。

<!--more-->

## 实验基础

- 两台主机
  主机环境：Ubuntu 14.04
- Mpi
  本次实验所安装的mpi版本为[openmpi](https://www.open-mpi.org/).

## 实验步骤

### HOSTS文件

修改每个节点的`/etc/hosts`文件，添加两个节点的IP映射，查看IP使用`ifconfig`命令，如果两个节点的主机名分别为host1和host2，IP分别为`192.168.1.1`和`192.168.1.2`，在该文件中添加;

``` bash
192.168.1.1 host1
192.168.1.2 host2
```

### SSH服务

初始机器可能没有安装ssh server服务，需要手动添加：

``` bash
$ sudo apt-get install openssh-server
```

可以在两台机器上配置相互之间的免密码SSH登陆，也可以跳过，之后在运行mpi程序的时候需要手动输入密码。相关操作可以查看网上资料，或[这篇文章](https://my.oschina.net/aiguozhe/blog/33994).

### OpenMpi安装

使用下面命令安装OpenMPI软件包：

``` bash
$ sudo apt-get install libopenmpi-dev
```

之后如果不能使用`mpirun`命令，则还需要安装：

``` bash
$ sudo apt-get install openmpi-bin
```

## 实验测试

使用简单的helloMpi程序，代码如下：

``` c
#include "mpi.h"
#include <stdio.h>
#include <stdlib.h>
#define  MASTER     0

int main (int argc, char *argv[])
{
	int   numtasks, taskid, len;
    char hostname[MPI_MAX_PROCESSOR_NAME];

    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &numtasks);
    MPI_Comm_rank(MPI_COMM_WORLD,&taskid);
    MPI_Get_processor_name(hostname, &len);
    printf ("Hello from task %d on %s!\n", taskid, hostname);
    if (taskid == MASTER)
       printf("MASTER: Number of MPI tasks is: %d\n",numtasks);
    MPI_Finalize();
    return 0 ;
}
```

使用`mpicc`命令编译：

``` bash
$ mpicc hellompi.c -o hellompi
```

运行：

``` bash
$ mpirun -np 10 -host host1,host2 hellompi
```

运行之前需要保证每台机器上都有`hellompi`，一般拷贝到HOME目录下。
