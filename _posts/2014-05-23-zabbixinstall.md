--- 
published: true
title: 分布式安装LNMP + zabbix
meta: 
  views: "1948"
  _wp_old_slug: 
  _edit_last: "1"
type: post
status: publish
layout: post
tags: 
- zhuima
- zabbix
- lnmp
---

##### 安装`lnmp`简介 #####

1. ### 手动编译安装分布式`lnmp` ###
			
- 这里使用别人的一键安装包，仅供参考(许久没有更新，不建议使用)
			
			wget -c http://soft.vpser.net/lnmp/lnmp1.0-full.tar.gz && tar zxvf lnmp1.0-full.tar.gz && cd lnmp1.0-full && screen -S lnmp  && ./centos.sh

2. ### 新建zabbix用户和组 ###
			groupadd zabbix
			useradd -g zabbix -s /sbin/nologin -m zabbix 

3. ### 安装zabbix的一些依赖包 ###
			yum -y install mysql-devel libcurl-devel net-snmp-devel php-pecl-ssh2.x86_64 libssh2-devel.x86_64 

4. ### 创建数据库，并授权账号 ###
			mysql> create database zabbix character set utf8;
			Query OK, 1 row affected (0.03 sec)

			mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
			Query OK, 0 rows affected (0.00 sec)''


####  正式安装`zabbix` ####

5. ### 编译安装zabbix ###
			[root@nginx tmp]# wget http://jaist.dl.sourceforge.net/project/zabbix/ZABBIX%20Latest%20Stable/2.2.0/zabbix-2.2.0.tar.gz

			[root@nginx zabbix-2.2.0]# ./configure --enable-server --enable-agent --with-mysql --enable-ipv6 --with-net-snmp --with-libcurl --with-ssh2
			[root@nginx zabbix-2.2.0]# make 
			[root@nginx zabbix-2.2.0]# make install

6. ### 导入数据库 ###
			[root@nginx zabbix-2.2.0]# mysql -uzabbix -pzabbix -hlocalhost zabbix < database/mysql/schema.sql 
			[root@nginx zabbix-2.2.0]# mysql -uzabbix -pzabbix -hlocalhost zabbix < database/mysql/images.sql 
			[root@nginx zabbix-2.2.0]# mysql -uzabbix -pzabbix -hlocalhost zabbix < database/mysql/data.sql 

7. ### 修改配置文件 ###
- 复制`zabbix_server` init文件
			[root@nginx zabbix-2.2.0]# cp misc/init.d/fedora/core/zabbix_server /etc/init.d
			[root@nginx zabbix-2.2.0]# chmod +x /etc/init.d/zabbix_server
			[root@nginx zabbix-2.2.0]# chkconfig add zabbix_server
			[root@nginx zabbix-2.2.0]# chkconfig zabbix_server on

- 复制`zabbix_agentd` init文件
			[root@nginx zabbix-2.2.0]# cp misc/init.d/fedora/core/zabbix_agentd /etc/init.d
			[root@nginx zabbix-2.2.0]# chmod +x /etc/init.d/zabbix_agentd
			[root@nginx zabbix-2.2.0]# chkconfig add zabbix_agentd
			[root@nginx zabbix-2.2.0]# chkconfig zabbix_server on

- 复制`zabbix` 网页文件到nginx目录下
			[root@nginx zabbix-2.2.0]# cp -R frontends/php /home/wwwroot/default/zabbix

- 修改`zabbix_server.conf` 中的数据库链接相关信息
			[root@nginx zabbix-2.2.0]# sed -i 's/^DBUser=.*$/DBUser=zabbix/g' /usr/local/etc/zabbix_server.conf
			[root@nginx zabbix-2.2.0]# sed -i 's/^DBPassword=.*$/DBPassword=zabbix/g' /usr/local/etc/zabbix_server.conf

8. ### 添加服务端口 ###
			cat >>/etc/services <<EOF
			zabbix-agent 10050/tcp Zabbix Agent
			zabbix-agent 10050/udp Zabbix Agent
			zabbix-trapper 10051/tcp Zabbix Trapper
			zabbix-trapper 10051/udp Zabbix Trapper
			EOF

9. ### 网页安装 ###
这里不再讲解，这里说下注意事项：

- 如果`zabbix`和`mysql`不在同一台机器上面，一定要注意防火墙的开关

- 在最后会让下载一个`zabbix_conf.php`文件，下载并导入到提示目录即可

10. ### 启动服务 ###
			[root@nginx zabbix-2.2.0]# /etc/init.d/zabbix_server start
			Starting zabbix_server:                                    [  OK  ]
			[root@nginx zabbix-2.2.0]# /etc/init.d/zabbix_agentd start
			Starting zabbix_agentd:                                    [  OK  ]
			[root@nginx zabbix-2.2.0]# 

11. ### 登陆web页面 ###

- 默认账号： admin
- 默认密码： zabbix

12. ## 问题解决 ##

稍后补充吧，上图不好上~
