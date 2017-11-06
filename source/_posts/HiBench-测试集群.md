---
title: HiBench 测试集群
date: 2017-09-25 12:03:31
tags:
 - test
 - hadoop
 - spark
---

[HiBench](https://github.com/intel-hadoop/HiBench) is a big data benchmark suite that helps evaluate different big data frameworks in terms of speed, throughput and system resource utilizations. It contains a set of Hadoop, Spark and streaming workloads, including Sort, WordCount, TeraSort, Sleep, SQL, PageRank, Nutch indexing, Bayes, Kmeans, NWeight and enhanced DFSIO, etc. It also contains several streaming workloads for Spark Streaming, Flink, Storm and Gearpump.

<!--more-->

`report/hibench.report`文件

``` bash
Type         Date       Time     Input_data_size      Duration(s)          Throughput(bytes/s)  Throughput/node
HadoopSleep  2017-09-22 15:31:36 0                    35.444               0                    0
ScalaSparkSleep 2017-09-22 15:32:24 0                    47.662               0                    0
HadoopSort   2017-09-22 15:33:33 3289154              36.619               89820                89820
ScalaSparkSort 2017-09-22 15:33:57 3289154              20.123               163452               163452
HadoopTerasort 2017-09-22 15:35:17 320000000            44.907               7125837              7125837
ScalaSparkTerasort 2017-09-22 15:35:50 320000000            28.086               11393576             11393576
HadoopWordcount 2017-09-22 15:37:26 328489350            57.490               5713851              5713851
ScalaSparkWordcount 2017-09-22 15:37:52 328489350            21.967               14953764             14953764
HadoopDfsioe-read 2017-09-22 15:41:17 337405021            108.139              3120104              3120104
HadoopDfsioe-write 2017-09-22 15:43:13 339398079            112.121              3027069              3027069
HadoopAggregation 2017-09-22 15:55:06 3729493              51.905               71852                71852
ScalaSparkAggregation 2017-09-22 15:55:49 3729453              35.889               103916               103916
HadoopJoin   2017-09-22 16:08:18 19201694             86.413               222208               222208
ScalaSparkJoin 2017-09-22 16:09:04 19201694             45.728               419911               419911
HadoopScan   2017-09-22 16:16:33 20104750             51.996               386659               386659
ScalaSparkScan 2017-09-22 16:17:14 20105279             34.558               581783               581783
HadoopPagerank 2017-09-22 16:32:24 1811191              241.664              7494                 7494
ScalaSparkPagerank 2017-09-22 16:32:51 1811191              23.345               77583                77583
HadoopBayes  2017-09-22 16:43:44 111385907            608.958              182912               182912
ScalaSparkBayes 2017-09-22 16:44:18 111385907            29.423               3785674              3785674
HadoopKmeans 2017-09-22 16:50:16 602462585            310.383              1941029              1941029
ScalaSparkKmeans 2017-09-22 16:51:01 602462585            40.560               14853614             14853614
LogisticRegression 2017-09-22 16:52:09 80062008             42.601               1879345              1879345
ALS          2017-09-22 16:53:18 8034800              44.931               178825               178825
GradientBoostingTree 2017-09-22 16:55:48 408432               124.001              3293                 3293
RandomForest 2017-09-22 16:56:50 408432               37.436               10910                10910
SVD          2017-09-22 16:58:15 16034800             58.932               272089               272089
ScalaSparkNWeight 2017-09-22 16:59:36 39301550             37.593               1045448              1045448
```

