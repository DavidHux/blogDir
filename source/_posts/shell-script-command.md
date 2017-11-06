---
title: shell script command
date: 2017-09-18 19:35:15
tags: shell
categories: programming
---

shell commands records

<!--more-->

## 一 `` && $()

用于执行命令

``` bash
MY_VAR=$(command)
# or you can do
MY_VAR=`command`
```

prefer $(), 后者易于嵌套

``` bash
$(echo foo$(echo bar))
```

output
```
foobar
```

``` bash
$ git clone $(cat gitt)
```
