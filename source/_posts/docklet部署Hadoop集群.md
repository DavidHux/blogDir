---
title: docklet部署Hadoop集群
date: 2017-10-13 16:18:42
tags: 
 - configure
 - shell
categories: installation
---

这篇BLOG用来记录在docklet上模拟多个节点，部署分布式Hadoop集群的过程。

<!--more-->

## 1 docklet

首先，docklet为用户分配了一定的物理资源，如图所示，当前总共可分配的资源有40个CPU核心、20000MB内存和400000MB硬盘空间，可以将这些资源根据需要自己分配到多个节点，构成一个虚拟集群。

![](/images/1013-1.png)

### 添加workspace

填写workspace name为hadoop，选择镜像baseWithJDK，这个镜像是在基础base镜像的基础上安装了java 8的环境。docklet提供保存镜像的功能，可以将一个节点的当前镜像保存下来，完全复制到另一个节点，这样可以节省安装时间。也可以使用base镜像，但需要重新配置java环境。 
advanced options设定物理资源的分配，这里的分配如图。这里只进行了一个节点的分配，后面要添加其他节点在config中的addnode.

![](/images/1013-2.png)

### go

节点分配完成后，点击workspace的start，然后点击go，转到jupyter页面，显示的是主节点`/root`目录下的文件信息。点击`New->Terminal`，在终端下进行操作。

![](/images/1013-3.png)

## 安装Hadoop

使用`mirrors.tuna.tsinghua.edu.cn`源下载hadoop的binary压缩包，使用`wget`命令：

``` bash 
# wget http://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-2.8.1/hadoop-2.8.1.tar.gz
```

这里下载的是2.8.1版本，可以根据需要把后面的版本号改成要下载的版本。

或者在[apache hadoop](http://hadoop.apache.org/releases.html#08+June%2C+2017%3A+Release+2.8.1+available)官网获取要下载的hadoop版本的链接，

完成后将下载的压缩包hadoop-2.8.1.tar.gz解压缩：

``` bash
# tar zxvf hadoop-2.8.1.tar.gz
```

得到`hadoop-2.8.1`目录（版本不同，目录不同，可以重命名为hadoop),`cd`到该目录下，结构如下：

``` bash
# cd hadoop-2.8.1
# ll
drwxrwxr-x 11  500  500  4096 Oct 11 12:18 ./ 
drwx------ 12 root root  4096 Oct 12 12:18 ../ 
drwxrwxr-x  2  500  500  4096 Jun  2 06:24 bin/
drwxrwxr-x  3  500  500  4096 Oct 11 12:13 etc/
drwxrwxr-x  2  500  500  4096 Jun  2 06:24 include/
drwxrwxr-x  3  500  500  4096 Jun  2 06:24 lib/ 
drwxrwxr-x  2  500  500  4096 Jun  2 06:24 libexec/
-rw-rw-r--  1  500  500 99253 Jun  2 06:24 LICENSE.txt        
-rw-rw-r--  1  500  500 15915 Jun  2 06:24 NOTICE.txt     
-rw-r--r--  1  500  500  1366 Jun  2 06:24 README.txt   
drwxrwxr-x  2  500  500  4096 Jun  2 06:24 sbin/      
drwxrwxr-x  4  500  500  4096 Jun  2 06:24 share/
```

### 配置集群

如果分配了三个节点，则三个节点的主机名是host-0,host-1,host-2，以此类推，可以`cat /etc/hosts`查看。 

然后需要配置`etc/hadoop`目录下的几个文件：

#### slaves

将所有节点添加进去，这样所有节点都作为datanode。也可以将host-0只作为namenode，不添加进去：

``` bash
host-0
host-1
host-2
```

#### core-site.xml

配置为

``` bash
<configuration> 
    <property>   
        <name>fs.defaultFS</name> 
        <value>hdfs://host-0:9000</value> 
    </property>             
    <property>       
        <name>hadoop.tem.dir</name>
        <value>file:/root/hadoop-2.8.1/tmp</value>
        <description>tem dir</description>   
    </property>      
</configuration> 
```

#### hdfs-site.xml

``` bash
<configuration>   
    <property>     
        <name>dfs.namenode.secondary.http-address</name>   
        <value>host-0:50090</value>   
    </property>       
    <property>       
        <name>dfs.replication</name>  
        <value>3</value>    
    </property>        
    <property>         
        <name>dfs.namenode.name.dir</name>  
        <value>file:/root/hadoop-2.8.1/tmp/dfs/name</value> 
    </property>         
    <property>           
        <name>dfs.datanode.data.dir</name>  
        <value>file:/root/hadoop-2.8.1/tmp/dfs/data</value> 
    </property>      
</configuration>
```
注意要保证datanode 的数目大于三，否则`dfs.replication`的值设为1.

####  mapred-site.xml

可能需要先重命名，默认文件名为 `mapred-site.xml.template`，然后配置修改如下：

``` bash
<configuration>  
    <property>     
        <name>mapreduce.framework.name</name>    
        <value>yarn</value>     
    </property>        
    <property>         
        <name>mapreduce.jobhistory.address</name> 
        <value>host-0:10020</value>       
    </property>                
    <property>               
        <name>mapreduce.jobhistory.webapp.address</name> 
        <value>host-0:19888</value>           
    </property>                    
</configuration>
```

#### yarn-site.xml

``` bash
<configuration>   
<!-- Site specific YARN configuration properties -->  
    <property>          
        <name>yarn.resourcemanager.hostname</name> 
        <value>host-0</value>  
    </property>           
    <property>                
        <name>yarn.nodemanager.aux-services</name> 
        <value>mapreduce_shuffle</value>   
    </property>    
</configuration>
```

### 分发

配置完成后将hadoop-2.8.1目录分发到其他节点上.

复制到其他节点：

``` bash
# scp -r /root/hadoop-2.8.1 host-1:~
# scp -r /root/hadoop-2.8.1 host-2:~
```

## 运行

在hadoop-2.8.1目录下：
先执行一次`hdfs namenode -format`,之后不需要再执行

启动：

``` bash
# ./sbin/start-dfs.sh
# ./sbin/start-yarn.sh
```

创建hdfs下root用户目录：

``` bash
# ./bin/hdfs dfs -mkdir /user/root
```

之后可以执行mapreduce任务

例如提交input数据到hdfs:

``` bash
# ./bin/hdfs dfs -mkdir input
# ./bin/hdfs dfs -put ./etc/hadoop/*.xml input
```

执行wordcount的例子：

``` bash
# ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount input output
# ./bin/hdfs dfs -cat output/*
```

## 参考资料

[Hadoop集群安装配置教程_Hadoop2.6.0_Ubuntu/CentOS](http://www.powerxing.com/install-hadoop-cluster/)