--- 
published: true
title: zabbix之监控DiskIO
meta: 
  views: "2014"
  _wp_old_slug: 
  _edit_last: "1"
type: post
status: publish
layout: post
tags: 
- zhuima
- zabbix
- diskio
---

bbix` 监控磁盘`IO` ##

1.在客户端上添加监控项目

** 有多种方式可以实现,`cat /proc/diskstats`和`iostat`都可以实现

** 使用`iostat`的时候，要检测系统上是否安装了`sysstat`

    {% highlight cl %}
    [root@nginx zabbix-2.2.0]# rpm -qa | grep sysstat
    sysstat-9.0.4-22.el6.x86_64
    [root@nginx zabbix-2.2.0]# 

    {% endhighlight %}
    
**添加如下所示的自定义项**

    {% highlight cl %}
    UserParameter=io_sda_tps,iostat -d -k /dev/sda | sed -n '4p' | awk '{print $2}'
    UserParameter=io_sda_rsec,iostat -x  /dev/sda | sed -n '7p' | awk '{print $6}'
    UserParameter=io_sda_wsec,iostat -x  /dev/sda | sed -n '7p' | awk '{print $7}'
    UserParameter=io_sda_await,iostat -x  /dev/sda | sed -n '7p' | awk '{print $10}'
    {% endhighlight %}
     
**每个公司的环境需求不一样，当然你可以根据自己的需求来自定义**

——————————————————————————————————————————————————————————————————————

2.zabbix服务器上添加自定义的`items`

**直接上图吧，不说了**

**自定义监控项**

<img src="/images/disk_io.png" alt="Sanjose" class="img-center" />

**`graphs样式`**

<img src="/images/disk_graphs.png" alt="Sanjose" class="img-center" />




