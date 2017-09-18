---
title: Docklet experiment record
date: 2017-09-18 14:01:01
tags:
---
## 初次使用

在创建了工作空间后第一次使用要对镜像进行配置，使其能够连接外网，安装软件等。

首先要修改每个节点的dns,修改/etc/resolve.conf文件，添加一个可用的dns server：

``` bash
nameserver 114.212.11.66
```

修改镜像源，修改/etc/source.list文件

添加
``` bash
deb http://mirrors.ustc.edu.cn/ubunutu xenial main restricted universe multiverse
```

之后可以安装需要使用的软件

## 安装要使用的软件包

因为要跑[mpi](https://www.open-mpi.org)的测试程序，所以要安装mpi的相关软件包，第一次安装错误，安装了mpich的包，导致后面p测试的时候出现了一些错误，本次实验应该使用openmpi的软件包。具体的openmpi和mpich的差别可以看[这篇文章](https://www.stackoverflow.com/questions/2427399/mpich-vs-openmpi).

安装openmpi使用下面的命令：

``` bash
$ sudo apt install libopenmpi-dev //ubuntu
```
## 测试程序

测试程序有两个，一个是docklet官方文档里的mpi简单的测试程序，并行程序可以输出多个helloword,另一个是cpi的测试程序。

### helloword测试程序

代码如下:

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

保存文件为mpihello.c

使用mpicc进行编译，基本语法与gcc一致：

``` bash
$ mpicc mpihello.c -o mpihello
```

利用mpirun执行程序：

``` bash
$ mpirun mpihello -np 2 -host host-0,host-1 --allow-run-as-root ./mpihello
```

参数意义:

> -np: 指定线程数目
> -host: 指定运行改程序的z节点，一个程序运行命令只需要执行在一个节点即可。
> --allow-run-as-root: mpirun 命令默认不允许以root身份执行，docklet环境默认是在root身份下，需要添加这个参数

测试结果：

![](/images/p1.PNG)

### cpi测试程序

代码如下。

[code](https://www.usqcd.org/fnal/example/cpi.c)

执行结果：

![](/images/p2.PNG)

## hadoop测试

未完待测试

### 待实验

如果使用CDH进行Hadoop的安装，需要解决docklet虚拟机群IP与外部连接的问题，可以实现manager server web安装界面，不确定添加节点时，内部IP内否被寻找到。
