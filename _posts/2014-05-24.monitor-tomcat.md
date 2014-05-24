--- 
published: true
title: zabbix之通过jmx监控tomcat
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
- tomcat
- jmx
---

##`zabbix`监控`tomcat` ##

1.编译zabbix的时候需要添加参数`--enable-java`

**关于如何安装`zabbix`,可以参考上一篇文章<http://blog.unix178.com/>**

备份`zabbix_server.conf`和`zabbix_agentd.conf`文件，重新编译安装`zabbix`

    [root@nginx zabbix-2.2.0]# ./configure --enable-server --enable-agent     --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-ssh2 --enable-java
    
2.zabbix_server端安装jdk

安装`jdk`

    [root@nginx tmp]# tar xf jdk-7u9-linux-x64.tar.gz -C /usr/local/
    [root@nginx tmp]# ls /usr/local/jdk1.7.0_09/
    bin        jre      README.html                         THIRDPARTYLICENSEREADME.txt
    COPYRIGHT  lib      release
    db         LICENSE  src.zip
    include    man      THIRDPARTYLICENSEREADME-JAVAFX.txt

3.修改zabbix_java相关选项

然后进入到下面的目录下面进行`seeting.sh`脚本编辑

    [root@nginx zabbix-2.2.0]# find / -name zabbix_java
    /usr/local/sbin/zabbix_java
    /tmp/zabbix-2.2.0/src/zabbix_java
    [root@nginx zabbix-2.2.0]# 
    [root@nginx zabbix-2.2.0]# ls
    bin  lib  settings.sh  shutdown.sh  startup.sh


该文件默认全部注释的，启用下面几项即可

    [root@nginx zabbix_java]# vim settings.sh 
    [root@nginx zabbix_java]# sed -e '/^#/d;/^$/d' settings.sh
     LISTEN_IP="0.0.0.0"
    LISTEN_PORT=10052
    PID_FILE="/tmp/zabbix_java.pid"
    START_POLLERS=5
    [root@nginx zabbix_java]# 
    
4.修改zabbix_server文件

启用其中的几项

    [root@nginx zabbix_java]# sed -e '/^#/d;/^$/d' /usr/local/etc/zabbix_server.conf
    LogFile=/tmp/zabbix_server.log
    DBName=zabbix
    DBUser=zabbix
    DBPassword=zabbix
    [root@nginx zabbix_java]# vim /usr/local/etc/zabbix_server.conf
    [root@nginx zabbix_java]# sed -e '/^#/d;/^$/d' /usr/local/etc/zabbix_server.conf
    LogFile=/tmp/zabbix_server.log
    DBName=zabbix
    DBUser=zabbix
    DBPassword=zabbix
    JavaGateway=127.0.0.1
    JavaGatewayPort=10052
    StartJavaPollers=5
    
5.启动zabbix_java

找到`zabbix_java`目录路径，然后执行命令`./startup.sh`

    [root@nginx zabbix-2.2.0]# find / -name zabbix_java
    /usr/local/sbin/zabbix_java
    /tmp/zabbix-2.2.0/src/zabbix_java
    [root@nginx zabbix-2.2.0]# 
    [root@nginx zabbix-2.2.0]# ls
    bin  lib  settings.sh  shutdown.sh  startup.sh
    [root@nginx zabbix-2.2.0]# ss -tunlp | grep 10052
    tcp    LISTEN     0      50                    :::10052                    :::*      users:(("java",16405,12))

    
6.调整`tomcat`端,安装`catalina-jmx-remote.jar`

下载来安装：

    root@tomcat5 tmp]# wget  http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.8/bin/extras/catalina-jmx-remote.jar

将下载好的文件存放到`tomcat`子目录目录`lib`录下

    [root@tomcat5 tmp]# mv catalina-jmx-remote.jar /usr/local/tomcat/lib/
    [root@tomcat5 tmp]# 
    
7.修改`catalina.sh`文件

修改catalina.sh文件，添加

    CATALINA_OPTS="-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=**ip**"

8.重启启动`tomcat`服务和`zabbix_agentd`服务


9.服务器端测试是否能正常获取信息

    [root@nginx zabbix-2.2.0]# java -jar /root/cmdline-jmxclient-0.10.3.jar  - 192.168.146.130:9999 java.lang:type=Memory NonHeapMemoryUsage
    05/24/2014 15:42:02 +0800 org.archive.jmx.Client NonHeapMemoryUsage: 
    committed: 47316992
    init: 24313856
    max: 136314880
    used: 47012784
    
10.服务器端自定义监控项

> 添加jmx监控端口

> 自定义监控项

> 查看绘图结果

**直接上图，自己看吧**

**添加jmx监控端口**

<img src="/images/jmx_port.png" alt="Sanjose" class="img-center" />
	
**自定义监控项**

<img src="/images/items.png" alt="Sanjose" class="img-center" />
	
**查看绘图结果**

<img src="/images/graphs.png" alt="Sanjose" class="img-center" />



11.参考网站

[tomcat官网jmx监控介绍](http://tomcat.apache.org/tomcat-6.0-doc/monitoring.html)

[CU上一哥们的博客](http://blog.chinaunix.net/uid-29179844-id-4093754.html)


