---
title: Yarn resource
date: 2017-11-03 19:16:44
tags: 
 - configure
 - bigdata
categories: Bigdata
---

之前配置的集群在执行任务时出现了只使用集群中几个节点的问题，应该是配置和角色分配的问题，这篇文章是一些对yarn的角色的一些理解。

<!--more-->

## NodeManager
For 原始文档，refer to [NodeManager](The NodeManager is responsible for launching and managing containers on a node. Containers execute tasks as specified by the AppMaster).

The NodeManager is responsible for launching and managing containers on a node. Containers execute tasks as specified by the AppMaster.

一般来说，如果要把一个节点作为slave node,应该在这个节点上启动nodemanager 服务。Nodemanager的作用：
#### Health Checker Service

检查该节点的物理资源的健康状况，出现错误之后会告知resource manager, resource manager负责分配container到某个节点，nodemanager则负责管理这些container.

check interval 默认2分钟。

每个节点上的NodeManger需要向resource manager进行注册。

1. NodeManagers take instructions from the ResourceManager and manage resources available on a single node.
2. ApplicationMasters are responsible for negotiating resources with the ResourceManager and for working with the NodeManagers to start the containers.

总结， NodeManager是YARN中单个节点的代理，它需要与应用程序的ApplicationMaster和集群管理者ResourceManager交互;它从ApplicationMaster上接收有关Container的命令并执行(比如启动、停止Contaner);向ResourceManager汇报各个Container运行状态和节点健康状况，并领取有关Container的命令（比如清理Container）。

之前只在三个节点上分配了node manager角色。所以只能使用三个节点进行计算。

## Resource Manager

![](https://2xbbhjxc6wk3v21p62t8n4d4-wpengine.netdna-ssl.com/wp-content/uploads/2012/08/resource_manager.png)

In YARN, the ResourceManager is primarily limited to scheduling i.e. only arbitrating available resources in the system among the competing applications and not concerning itself with per-application state management. Because of this clear separation of responsibilities coupled with the modularity described above, and with the powerful scheduler API discussed in the previous post, RM is able to address the most important design requirements – scalability, support for alternate programming paradigms.

To allow for different policy constraints, the scheduler described above in the RM is pluggable and allows for different algorithms. In a future post of this series, we will dig deeper into various features of CapacityScheduler that schedules containers based on capacity guarantees and queues.