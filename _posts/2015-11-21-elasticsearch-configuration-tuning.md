---
layout: post
title: Elasticsearch Configuration and Performance Tuning
description: elasticsearch, installation, configuration, tuning  
headline:
category: development
tags: [elasticsearch, tuning, ubuntu, full-text, elasticsearch-tuning, elasticsearch configuration, elasticsearch installation]
comments: true
mathjax:
---

# Elasticsearch Configuration
_**WARNING: This configuration is not production-ready yet!**_

In this post, we will be talking about how to make Elasticsearch more stable and performant.

> Elasticsearch is a distributed RESTful search engine built for the cloud. Fore more information please follow [this link](https://www.elastic.co/products/elasticsearch).


*We can start with tuning OS level settings which mentioned in ES documentations;*

# Configuring OS

### Brief
First things first, let's get OS ready. 

> Virtual memory is typically consumed by processes, file system caches, and the kernel. Virtual memory utilization depends on a number of factors, which can be affected by the following parameters.

`vm.swappiness`

ES recommends to set this value `1`, also according to Red Hat, a low `swappiness` value is recommended for database workloads.  As an example, for Oracle databases, Red Hat recommended  `swappiness` value  is  `10`. For further reading [Tuning Virtual Memory](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Performance_Tuning_Guide/s-memory-tunables.html).
> Why do we set this value to 1 instead of 0?
>  Setting swappiness to 0 more aggressively avoids swapping out, which increases the risk of OOM killing under strong memory and I/O pressure.

`net.core.somaxconn`

Maximum number of connection an application can request.

`vm.max_map_count`

This property allows for the restriction of the number of VMAs (Virtual Memory Areas) that a particular process can own. When it reaches the limit, out of memory error will be thrown.

`fs.file-max`

Sets the maximum number of file-handles that the Linux kernel will allocate.

`elasticsearch    soft     nofile             65535`
`elasticsearch    hard     nofile             65535`

Sets the limits of file descriptors for specific user.

`elasticsearch    soft     memlock          unlimited`
`elasticsearch    hard     memlock          unlimited`

I had ``Unable to lock JVM memory (ENOMEM). This can result in part of the JVM being swapped out. Increase RLIMIT_MEMLOCK (limit).`` error before making this change. But thanks to [mrzard](http://mrzard.github.io/blog/2015/03/25/elasticsearch-enable-mlockall-in-centos-7/) I got rid of this problem by setting this unlimited. 

> Just in case, soft limit can be temporarily exceeded by the user,  but the system will not allow a user to exceed hard limit. We just go strict with this so we set both the same value.

`session required pam_limits.so`

The pam_limits PAM module sets limits on the system resources that can be obtained in a user-session.

`bootstrap.mlockall: true`

Tries to lock the process address space into RAM, preventing any Elasticsearch memory from being swapped out. This attribute provides JVM to lock its memory block and protects it from OS to swap this memory block. This is kind of performance optimization.

`indices.fielddata.cache.size: 40%`

Field data cache is unbounded. This, of course, could make your JVM heap explode.To avoid nasty surprises we limit this with 40%.(affects search performance)

`indices.cache.filter.size: 30%`

Even though filters are relatively small, they can take up large portions of the JVM heap if you have a lot of data and numerous different filters. So we limit this with 30%.

`indices.cache.filter.terms.size: 1024mb`

The size of the lookup cache. Default value is 10mb.

` threadpool.search.type: cached`

Thread pool is an unbounded thread pool that will spawn a thread if there are pending requests.

` threadpool.search.size: 100`

This parameter controls the number of threads and default value is the number of cores times 5.

` threadpool.search.queue_size: 2000`

Allows to control the size of the queue of pending requests that have no threads to execute them. By default, it is set to -1 which means its unbounded. When a request comes in and the queue is full, it will abort the request. Increasing this too much, causes performance problem.

`transport.tcp.compress`

Set to true to enable compression (LZF) between all nodes. Defaults to false.

`ES_HEAP_SIZE`

The default installation of Elasticsearch is configured with a 1 GB heap. According to our long researches this value should be the half size of total RAM. Should not cross 30.5 GB!

## Implementation

Open the _sysctl.conf_;
```bash
nano /etc/sysctl.conf
```
Add these properties
```bash
vm.swappiness=1                          # turn off swapping
net.core.somaxconn=65535                 # up the number of connections per port
vm.max_map_count=262144                  #(default) http://www.redhat.com/magazine/001nov04/features/vm
fs.file-max=518144                       # http://www.tldp.org/LDP/solrhe/Securing-Optimizing-Linux-RH-Edition-v1.3/chap6sec72.html
```

After that, go to the _limits.conf_;
```bash
nano /etc/security/limits.conf
```
The important thing is, which user is defined below. Our ES user should access these informations. It is recommended that using specific user for such big applications.(We did it in Redis too.) This user name is default when you installed the ES.
```bash
elasticsearch    soft    nofile          65535
elasticsearch    hard    nofile          65535
elasticsearch    soft    memlock         unlimited
elasticsearch    hard    memlock         unlimited

```
and to make these properties persistent you have to modify the 
```bash
nano /etc/pam.d/common-session-noninteractive
nano /etc/pam.d/common-session 
```
Add this property
```bash
session required pam_limits.so
```

_You may need to reboot the machine to those changes to be applied._  



## Configuring Elasticsearch

Now everyting is ready for the Elasticsearch to be installed. You can use this bash script.[Here you can get the gist](https://gist.githubusercontent.com/ziyasal/67b2c68930a168735052/raw/64ff4d6510f91c70416df1ff238f62cda558f6c7/es.sh).

```wget "https://gist.githubusercontent.com/ziyasal/67b2c68930a168735052/raw/64ff4d6510f91c70416df1ff238f62cda558f6c7/es.sh" ```

After that execute;

```sh es.sh```

```bash
#!/bin/bash

ELASTICSEARCH_VERSION=1.7

### Download and install the Public Signing Key
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

### Setup Repository
echo "deb http://packages.elastic.co/elasticsearch/${ELASTICSEARCH_VERSION}/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elk.list

### Install Elasticsearch
sudo apt-get update && sudo apt-get install elasticsearch -y

### Start ElasticSearch
sudo service elasticsearch start

### Lets wait a little while ElasticSearch starts
sleep 10

### Make sure service is running
curl http://localhost:9200 
```
 
Elasticsearch has newer versions but I go with 1.7. It is up to you. You can choose whichever you want. 
I strongly recommend you to install Elasticsearch this way. If you download the tar.gz and go with that way, you have to create your init scripts and also you have to create Elasticsearch user which is very important to make configuration easier.
Anyway, I assume you installed it with the script. Now you have elasticsearch.yml and logging.yml files under
```bash
cd /etc/elasticsearch
```
In this part, let's open the elasticsearch.yml. I only show you the places that need to be shown. All other settings are default. If you don't believe me [here](https://gist.github.com/ozzimpact/49d750b6cd73eac9acf7) you can check whole file.
```bash
nano /etc/elasticsearch/elasticsearch.yml
```
```bash
bootstrap.mlockall: true

transport.tcp.compress: true

indices.fielddata.cache.size: 40%
indices.cache.filter.size: 30%
indices.cache.filter.terms.size: 1024mb

threadpool:
    search:
        type: cached
        size: 100
        queue_size: 2000
```

After that, let's go to _elasticsearch_ start script.
```bash
nano /etc/init.d/elasticsearch
```
One of the most important thing in ES, heap size. As much as I searched, mostly heap size should be half of total ram size and also [should not be more than 30.5GB](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html).

```bash
# Heap size defaults to 256m min, 1g max
# Set ES_HEAP_SIZE to 50% of available RAM, but no more than 31g
ES_HEAP_SIZE=4g
```
Finally, you can check the properties for our ES user
```bash
su elasticsearch --shell /bin/bash --command "cat /proc/sys/vm/swappiness "
su elasticsearch --shell /bin/bash --command "cat /proc/sys/net/core/somaxconn"
su elasticsearch --shell /bin/bash --command "cat /proc/sys/vm/max_map_count "
su elasticsearch --shell /bin/bash --command "cat /proc/sys/fs/file-max "

su elasticsearch --shell /bin/bash --command "ulimit -n"
su elasticsearch --shell /bin/bash --command "ulimit -Sn"
su elasticsearch --shell /bin/bash --command "ulimit -Hn"
```

You can reboot the machines and check your cluster status from sense
```bash
GET /_nodes/process?pretty
```
or check every node from console
```bash
curl 'http://localhost:9200/?pretty'
```


If your nodes don't start on startup, probably your init scripts did not installed properly. Use this command and reboot. 
```bash
sudo update-rc.d elasticsearch defaults 95 10
```

If you get this exception ``org.elasticsearch.transport.RemoteTransportException`` check your nodes to know which version of java is installed.


----------


----------


