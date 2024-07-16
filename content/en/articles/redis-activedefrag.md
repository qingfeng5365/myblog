+++
title = 'Redis Enables Activedefrag'
date = 2024-07-14T04:49:57-04:00
description = ""
tags = ["redis","memo"]
draft = false
+++

Redis is a widely used in-memory database, renowned for its speed and efficiency. However, prolonged usage can lead to memory fragmentation issues, impacting performance and efficiency. In this blog post, we'll explore Redis memory fragmentation using a vivid analogy and discuss how Redis' active defragmentation feature can mitigate these issues.
## The Courier Box Anology
Imagine Redis' memory space as a courier box used for storing items. Initially, the box is neatly organized, efficiently storing items of various sizes. However, with frequent insertion and removal of items (data operations), the contents inside the box can become disorganized. Over time, small graps and irregular spaces (fragments) start forming within the box. These fragments, though individually usable, create inefficiencies in utilizing the box's total capacity effectively.
## Consequences
1. **Space Inefficiency**: Just like the courier box with scattered fragments, Redis may have fragmented memory spaces that cannot be efficiently utilized for storing new data. This leads to wasted memory space within Redis.
2. **Performence Degradation**: As fragmentation increases, Redis may struggle to find contiguous memory blocks for storing larger data items efficiently. This can result in slower data access times and reduced overall performance of Redis.
3. **Maintenance Overhead**: To optimize memory utilization, periodic defragmentation becomes necessary. This process is akin to organizing and rearranging items inside the courier box to minimize wasted space and ensure efficient use of available memory.
## Enable Activedefrag
1. **Install Redis From Source**: 
```
wget https://download.redis.io/redis-stable.tar.gz
tar -xzvf redis-stable.tar.gz
cd redis-stable
sudo make MALLOC=jemalloc
sudo make install
```
2. **Edit Configuration File**: 
```
sudo vim redis.conf
daemonize yes
activedefrag yes
dir /var/redis/6379
port 6379
pidfile /var/run/redis_6379.pid
loglevel /var/log/redis_6379.log
```
3. **Start Redis Service**:
```
sudo mkdir /etc/redis
sudo mkdir /var/redis
sudo mkdir /var/redis/6379
sudo cp utils/redis_init_script /etc/init.d/redis_6379
sudo cp redis.conf /etc/redis/6379.conf
sudo update-rc.d redis_6379 defaults
sudo /etc/init.d/redis_6379 start
```
{{< giscus >}}
