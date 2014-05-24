--- 
published: true
title: zabbix之监控nginx
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
- nginx
- shell
---

##zabbix` 监控`nginx` ##

1.解读`nginx`的`status`包含字段意义

> Active connections：活动连接数

> server accepts handled requests下的三个数：

>> accepts：已接受过的连接数

>> handled：已处理过的连接数

>> requests：处理的请求数

>>（连接和请求的区别：当开启keepalive之后一个连接可发起多个请求）

> Reading：正在接进的请求数

> Writing:正在处理或已经返回给用户信息

> Waiting：活动连接数（Writing+Reading）

_____________________________________________________________

2.配置`zabbix_agentd.conf`文件

取消下面两项的注释

    
    {% highlight cl %}
    // 取消改行的注释
    Include=/usr/local/etc/zabbix_agentd.conf.d/
    
	// 取消改行注释，并把0更改为1
    UnsafeUserParameters=1
    {% endhighlight %}
    
3.添加自定义的key文件

在`/usr/local/etc/zabbix_agentd.conf.d/`目录下创建一个`nginx_status.conf`文件，并添加如下内容
    
    {% highlight cl %}
    UserParameter=nginx.accepts,/usr/local/sbin/nginx_status.sh accepts
    UserParameter=nginx.handled,/usr/local/sbin/nginx_status.sh handled
    UserParameter=nginx.requests,/usr/local/sbin/nginx_status.sh requests
    UserParameter=nginx.connections.active,/usr/local/sbin/nginx_status.sh active
    UserParameter=nginx.connections.reading,/usr/local/sbin/nginx_status.sh reading
    UserParameter=nginx.connections.writing,/usr/local/sbin/nginx_status.sh writing
    UserParameter=nginx.connections.waiting,/usr/local/sbin/nginx_status.sh waiting
    {% endhighlight %}
    
_______________________________________________________________________

4.添加nginx_status.sh脚步到与上面所写内容对应的目录中去

脚步内容如下：
    
    {% highlight cl %}
    [root@nginx zabbix-2.2.0]# cat /usr/local/sbin/nginx_status.sh 
    #!/bin/bash
    #script to fetch nginx statuses for tribily monitoring systems
    
    # Set Variables
    
    ipaddr=`/sbin/ifconfig em1 | sed -n '/inet /{s/.*addr://;s/ .*//;p}'`
    PORT="80"
    
    # Functions to return nginx stats
    
    function active {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| grep 'Active' | awk '{print $NF}' 
    
            } 
    
    function reading {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| grep 'Reading' | awk '{print $2}' 
    
            } 
    
    function writing {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| grep 'Writing' | awk '{print $4}' 
    
            } 
    
    function waiting {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| grep 'Waiting' | awk '{print $6}' 
    
            } 
    
    function accepts {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| awk NR==3 | awk '{print $1}'
    
            } 
    
    function handled {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| awk NR==3 | awk '{print $2}'
    
            } 
    
    function requests {
    
            /usr/bin/curl "http://$ipaddr:$PORT/status" 2>/dev/null| awk NR==3 | awk '{print $3}'
    
            }
    
    # Run the requested function
    $1 
    {% endhighlight %}
    
________________________________________________________________________________

5.设置nginx.conf文件

配置`/status`权限相关
    	
		{% highlight cl %}
		location /status {
			stub_status on;
			allow 127.0.0.1;
			allow 192.168.146.130;
			deny all;
			access_log   off;
		{% endhighlight %}

6.客户端重启`zabbix_agentd`和`nginx`服务

________________________________________________________________________________

7.`zabbix`服务器段设置监控项，当然你也可以去下载模板这里我使用模板来做的

**废话不多说，上图来看**

<img src="/images/monitor_nginx.png" alt="Sanjose" class="img-center" />


