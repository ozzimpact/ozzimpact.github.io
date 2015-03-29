---
layout: post
title: "Redis sentinel and monit setup!"
description: Redis sentinel and monit setup!
headline: 
category: development
tags: [redis, monit, sentinel, high-availability]
comments: true
mathjax: 
---

Redis + Sentinel + Monit setup scripts and High Availability   

> TODO: Abstract
_**(Edited Version)**_ 
# Index
* [Redis + Sentinel + Monit Setup](#redis--sentinel--monit-setup)  
    * [Redis Master/Slave](#redis-masterslave)  
    * [Redis Sentinel](#redis-sentinel)  
    * [Monit](#monit)  
    * [Apply Redis and Sentinel Configurations into Monit](#apply-redis-and-sentinel-configurations-into-monit)  
* [System Side Settings](#system-side-settings)  
* [Shortcuts](#shortcuts)  

# Redis + Sentinel + Monit Setup  
Redis + Sentinel + Monit Setup Scripts  
_**WARNING:**_ _It is not production ready configuration yet! Work in progress.._  


###Redis Master/Slave  

**To Install Master**   
Edit ```master.sh``` file to set configurations (redis version,instance name, port);  
{% highlight bash %}
# Defaults
REDIS_VER=2.8.19
UPDATE_LINUX_PACKAGES=false
REDIS_INSTANCE_NAME=redis-server
REDIS_INSTANCE_PORT=6379
{% endhighlight %}  

{% highlight bash %}
mkdir redisetup
cd redisetup
wget https://raw.githubusercontent.com/ziyasal/redisetup/master/master.sh
sudo sh master.sh #Run install script
{% endhighlight %}  

**To Install Slave**   
Edit ```member.sh``` file to set configurations (redis version,instance name, port, master ip, master port);  
{% highlight bash %}
# Defaults
REDIS_VER=2.8.19
UPDATE_LINUX_PACKAGES=false      #true|false
REDIS_INSTANCE_NAME=redis-server
REDIS_INSTANCE_PORT=6379         #Set another one if master node is on the same host
REDIS_MASTER_IP=127.0.0.1
REDIS_MASTER_PORT=6379
{% endhighlight %}  

{% highlight bash %}
mkdir redisetup
cd redisetup
wget https://raw.githubusercontent.com/ziyasal/redisetup/master/member.sh
sudo sh member.sh #Run install script
{% endhighlight %}  

_**Set somaxconn**_   
{% highlight bash %}
echo 65535 > /proc/sys/net/core/somaxconn
{% endhighlight %}  
_**redis.conf**_   [for more detail](http://redis.io/topics/config)    
{% highlight bash %}
tcp-backlog 65535
#**TODO**
#To dump the dataset every 15 minutes (900 seconds) if at least one key changed, you can say:
#save 900 1
#**TODO**
#Redis instantly writes to the log file so even if your machine crashes, it can still recover and have the latest data. #Similar to RDB, AOF log is represented as a regular file at var/lib/redis called appendonly.aof (by default).
#appendonly yes
#**TODO**
#To tell OS to really really write the data to the disk, Redis needs to call the fsync() function right after the write call, #which can be slow.
#appendfsync everysec
{% endhighlight %}  
_**/etc/init.d/redis-server**_  
{% highlight bash %}
sudo sh -c "echo never > /sys/kernel/mm/transparent_hugepage/enabled"
ulimit -n 65535
ulimit -n >> /var/log/ulimit.log #Not required!
{% endhighlight %}  

###Redis Sentinel  
_**install**_  
{% highlight bash %}
wget https://raw.githubusercontent.com/ziyasal/redisetup/master/sentinel.sh
sudo sh sentinel.sh  #Run install script
{% endhighlight %}  

###Monit  
_**install**_  
{% highlight bash %}
sudo apt-get install monit
{% endhighlight %}  
_**update monit config file**_  
{% highlight bash %}
nano /etc/monit/monitrc
{% endhighlight %}  
_Add or update httpd settings_  
{% highlight bash %}
set httpd port 8081 and
    use address localhost  # only accept connection from localhost
    allow localhost        # allow localhost to connect to the server and
    allow admin:monit      # require user "admin" with password "monit"
{% endhighlight %}  
###Apply Redis and Sentinel Configurations into Monit  
_**Create redis.conf**_  
{% highlight bash %}
nano /etc/monit/conf.d/redis.conf
{% endhighlight %}  
Add following settings for more options [monit documentation](https://mmonit.com/monit/documentation/)  
{% highlight bash %}
#Default settings
#watch by pid
check process redis-server
    with pidfile "/var/run/redis.pid"
    start program = "/etc/init.d/redis-server start"
    stop program = "/etc/init.d/redis-server stop"
    if failed host 127.0.0.1 port 6379 then restart
    if 5 restarts within 5 cycles then timeout
{% endhighlight %}  

_**Create sentinel.conf**_  
{% highlight bash %}
nano /etc/monit/conf.d/redis-sentinel.conf
{% endhighlight %}  
Add following lines  
{% highlight bash %}
#watch by process name TODO: pid file
check process redis-sentinel
    matching "redis-sentinel"
    start program = "/etc/init.d/redis-sentinel start"
    stop program = "/etc/init.d/redis-sentinel stop"
    if failed host 127.0.0.1 port 26379 then restart
    if 5 restarts within 5 cycles then timeout
{% endhighlight %}  

##System Side Settings  
_**sysctl.conf**_  
{% highlight bash %}
vm.overcommit_memory=1                # Linux kernel overcommit memory setting
vm.swappiness=0                       # turn off swapping
net.ipv4.tcp_sack=1                   # enable selective acknowledgements
net.ipv4.tcp_timestamps=1             # needed for selective acknowledgements
net.ipv4.tcp_window_scaling=1         # scale the network window
net.ipv4.tcp_congestion_control=cubic # better congestion algorythm
net.ipv4.tcp_syncookies=1             # enable syn cookied
net.ipv4.tcp_tw_recycle=1             # recycle sockets quickly
net.ipv4.tcp_max_syn_backlog=65535    # backlog setting
net.core.somaxconn=65535              # up the number of connections per port
fs.file-max=65535
{% endhighlight %}  

_**/etc/security/limits.conf**_  
{% highlight bash %}
redis soft nofile 65535
redis hard nofile 65535
{% endhighlight %} 

Add following line  
{% highlight bash %}
session required pam_limits.so
{% endhighlight %}  
to  
{% highlight bash %}
/etc/pam.d/common-session
/etc/pam.d/common-session-noninteractive
{% endhighlight %}  

###Shortcuts  

After executing the command shown below   

{% highlight bash %}
monit monitor all
{% endhighlight %}  

Now you can keep track of redis server and sentinel by monit   

{% highlight bash %}
monit status
{% endhighlight %}  

Get Master/Slave replication information  

{% highlight bash %}
redis-cli -p 6379 info replication
{% endhighlight %}  

Get Sentinel information  

{% highlight bash %}
redis-cli -p 26379 info sentinel
{% endhighlight %}  
