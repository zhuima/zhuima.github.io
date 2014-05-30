--- 
published: true
title: tomcat一机多实例
meta: 
  views: "2014"
  _wp_old_slug: 
  _edit_last: "1"
type: post
status: publish
layout: post
tags: 
- tomcat
- instance
- jdk
---


## `tomcat`一机多实例的实现

### 1. `jdk`的安装配置

> - 安装`jdk`
``` cpp
tar xf $DIR/jdk-7u9-linux-x64.tar.gz -C /usr/java/ &>/dev/null
```

> - 配置jdk环境变量
``` cpp
cat >> /etc/profile.d/java.sh << EOF
export JAVA_HOME=/usr/java/jdk1.7.0_09
export PATH=$PATH:$JAVA_HOME/bin
export JRE_HOME=/usr/java/jre
export CLASSPATH=.:PATH:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
EOF
```


> -  测试效果
``` cpp
ot@tomcat1 usr]# java -version
java version "1.7.0_09"
Java(TM) SE Runtime Environment (build 1.7.0_09-b05)
Java HotSpot(TM) 64-Bit Server VM (build 23.5-b02, mixed mode)
[root@tomcat1 usr]# 
```

### 2. `tomcat`的安装配置

**略过**

------------------------------------

### 1. 单实例多站点的配置

**单实例的优缺点**
> - 一个主机修改了相关配置，所有主机都要重启，严重影响线上业务

> - 系统关联性问题过大，容易造成一荣俱荣，一损俱损的情况

**配置** `server.xml`文件配置中关于主机的定义

``` cpp
 <Connector port="80" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

<Host name="www.zhuima.xxx" appBase="/tpmcat/myapp"
	unpackWARs="true" autoDeploy="true">
<Context path="/" docBase="/tpmcat/myapp" />
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
        prefix="zhuima_access_log." suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>

<Host name="local.zhuima.xxx"  appBase="webapps"
	unpackWARs="true" autoDeploy="true">
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
        prefix="zhuima_access_log." suffix=".txt"
        pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>

```

**访问测试**

``` cpp
[root@tomcat1 conf]# for x in www local;do curl -I http://$x.zhuima.xxx;done

HTTP/1.1 200 OK
Server: nginx/0.8.53
Date: Mon, 26 May 2014 06:59:59 GMT
Content-Type: text/html
Content-Length: 166
Last-Modified: Thu, 26 Dec 2013 13:50:37 GMT
Connection: close
Accept-Ranges: bytes

HTTP/1.1 200 OK
Server: nginx/0.8.53
Date: Mon, 26 May 2014 07:00:02 GMT
Content-Type: text/html
Content-Length: 166
Last-Modified: Thu, 26 Dec 2013 13:50:37 GMT
Connection: close
Accept-Ranges: bytes
```



------------------------------------------------

### 2. 多实例多站点的配置


**多实例的优缺点**

> - 各实例间分开部署，相互之间无影响
> - 一机器多实例的部署，对系统内容要求比较高

**配置**安装`apache-tomcat`的时候，多复制几份

### 1. 配置每个实例的`tomcat`文件

> `tomcat1`的环境变量配置

``` cpp
[root@tomcat1 conf]# cat /etc/profile.d/tomcat1.sh 
#################################################
# File Name: /etc/profile.d/tomcat1.sh
# Author: zhuima
# mail: 993182876@qq.com
# Created Time: Thu 24 Apr 2014 04:54:46 AM CST
#################################################
#!/bin/bash
export CATALINA_HOME=/usr/local/tomcat1
export PATH=$PATH:$CATALINA_HOME/bin
```
> `tomcat2`的环境变量配置

``` cpp
[root@tomcat1 conf]# cat /etc/profile.d/tomcat2.sh 
#################################################
# File Name: /etc/profile.d/tomcat2.sh
# Author: zhuima
# mail: 993182876@qq.com
# Created Time: Thu 24 Apr 2014 04:54:46 AM CST
#################################################
#!/bin/bash
export CATALINA_HOME=/usr/local/tomcat2
export PATH=$PATH:$CATALINA_HOME/bin
```
-------------------------------------------------

### 2. 配置每个实例的`init`文件

> - `tomcat1`的init文件

``` cpp
################################################
# File Name: /etc/rc.d/init.d/inittomcat.sh
# Author: zhuima
# mail: 993182876@qq.com
# Created Time: Thu 24 Apr 2014 04:55:22 AM CST
################################################
#!/bin/bash
# chkconfig: - 96 14
# description: Tht Apache Tomcat Servlet/JSP container.

    export JAVA_HOME=/usr/local/jdk
    export CATALINA_HOME=/usr/local/tomcat1
    exec   $CATALINA_HOME/bin/catalina.sh  $*
```

> - `tomcat2`的init文件

``` cpp
################################################
# File Name: /etc/rc.d/init.d/inittomcat.sh
# Author: zhuima
# mail: 993182876@qq.com
# Created Time: Thu 24 Apr 2014 04:55:22 AM CST
################################################
#!/bin/bash
# chkconfig: - 96 14
# description: Tht Apache Tomcat Servlet/JSP container.

    export JAVA_HOME=/usr/local/jdk
    export CATALINA_HOME=/usr/local/tomcat2
    exec   $CATALINA_HOME/bin/catalina.sh  $*
```

------------------------------------------------

### 3. 配置每个实例的`server.xml`文件


> `tomcat1`的`server.xml`配置

``` cpp
<Server port="8005" shutdown="SHUTDOWN">
<Service name="Catalina">
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="192.168.146.132">
<Connector port="8081" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Host name="192.168.146.132"  appBase="webapps"
		  unpackWARs="true" autoDeploy="true">
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

> `tomcat2`的`server.xml`配置

``` cpp
<Server port="8006" shutdown="SHUTDOWN">
<Service name="Catalina">
<Connector port="8010" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="192.168.146.132">
<Connector port="8081" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Host name="192.168.146.132"  appBase="webapps"
		  unpackWARs="true" autoDeploy="true">
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log." suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

**测试效果**

``` cpp
[root@tomcat1 conf]# ss -tunlp | grep java
tcp    LISTEN     0      100                   :::8080                 :::*      users:(("java",1697,40))
tcp    LISTEN     0      100                   :::8081                 :::*      users:(("java",1727,40))
tcp    LISTEN     0      1       ::ffff:127.0.0.1:8005                 :::*      users:(("java",1697,51))
tcp    LISTEN     0      1       ::ffff:127.0.0.1:8006                 :::*      users:(("java",1727,51))
tcp    LISTEN     0      100                   :::8009                 :::*      users:(("java",1697,41))
tcp    LISTEN     0      100                   :::8010                 :::*      users:(("java",1727,41))
[root@tomcat1 conf]# 
```

**访问效果**

``` cpp
[root@tomcat1 conf]# for x in `seq 8080 8081`;do curl -I http://192.168.146.132:$x;done
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: text/html;charset=ISO-8859-1
Transfer-Encoding: chunked
Date: Sat, 17 May 2014 01:32:18 GMT

HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Content-Type: text/html;charset=ISO-8859-1
Transfer-Encoding: chunked
Date: Sat, 17 May 2014 01:32:18 GMT

[root@tomcat1 conf]# 
```


**参考站点**
[tomcat配置文件参数，一机多实例管理配置.详解](http://blog.coocla.org/224.html)
[孤城的博客](http://freeloda.blog.51cto.com/2033581/1299644)
