
## Redis on CentOS-7:

```
yum install epel-release

yum install redis -y
```



```
systemctl start redis
systemctl status redis
systemctl enable redis
```


_By Default version: v3 :_
```
redis-server --version

Redis server v=3.2.12 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 build=7897e7d0e13773f
```


```
redis-cli ping

PONG
```


```
redis-cli

127.0.0.1:6379> ping
PONG
```



```
redis-cli

127.0.0.1:6379> auth redis
(error) ERR Client sent AUTH, but no password is set
```


```
redis-cli

CONFIG SET requirepass "redis"
OK

auth redis
OK
```


```
redis-cli

127.0.0.1:6379> AUTH redis
OK

127.0.0.1:6379> ping
PONG
```



### Redis Configure:

To modify Redis configurations, edit the config file located at `/etc/redis.conf`. By default, this line will be commented out (with a # at the beginning). It will look like this `# requirepass foobared`:


```
vim /etc/redis.conf

#### NETWORK ####
bind 0.0.0.0

### SECURITY ####
requirepass redis
```


```
systemctl restart redis
```


```
redis-cli

127.0.0.1:6379> ping
(error) NOAUTH Authentication required.

127.0.0.1:6379> AUTH redis
OK

127.0.0.1:6379> ping
PONG
```



```
redis-cli

127.0.0.1:6379> INFO

127.0.0.1:6379> INFO Server

127.0.0.1:6379> INFO clients

127.0.0.1:6379> INFO replication
```


```
127.0.0.1:6379> client list
```


---
---



## To install Redis 5.x on CentOS 7:

To install Redis version 5 on CentOS 7, you'll need to add a repository that contains Redis 5, as the default CentOS 7 repositories may contain an older version. Here's how you can install Redis 5 on CentOS 7:



### Pre-requisite:
```
yum install yum-utils
```


```
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```


_Enable the Remi repository for Redis-5:_
```
yum-config-manager --enable remi
yum-config-manager --disable remi

yum-config-manager --enable remi-redis5

yum-config-manager --enable remi-redis7
```


### Install Redis-5:
```
yum --enablerepo=remi install redis
```


```
redis-server --version

Redis server v=7.2.5 sha=00000000:0 malloc=jemalloc-5.3.0 bits=64 build=2dd7a20ee3c6d015
```


```
systemctl start redis
systemctl status redis
systemctl enable redis
```



---
---




## Redis on Rocky Linux 8:


```
yum module list redis

Last metadata expiration check: 1:27:52 ago on Mon 09 Sep 2024 10:34:08 AM +06.
Rocky Linux 8 - AppStream
Name                         Stream                          Profiles                          Summary
redis                        5 [d][e]                        common [d]                        Redis persistent key-value database
redis                        6                               common [d]                        Redis persistent key-value database

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
```


```
yum install redis -y
```


or,

```
yum -y module reset redis
```

```
yum install @redis:6
```


```
systemctl start redis
systemctl status redis
systemctl enable redis
```


_By Default version: v5 :_
```
redis-server --version

Redis server v=5.0.3 sha=00000000:0 malloc=jemalloc-5.1.0 bits=64 build=7fa21edfc0646001
```


_Redis version: v6 :_
```
redis-server --version

Redis server v=6.2.7 sha=00000000:0 malloc=jemalloc-5.1.0 bits=64 build=56aeeede275be948
```


### Installing redis 7.x from remi-modular repo:

```
yum install epel-release
yum install yum-utils
```

```
yum install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
```


```
yum module list redis

Remi's Modular repository for Enterprise Linux 8 - x86_64                                                                329 kB/s | 928 kB     00:02
Safe Remi's RPM repository for Enterprise Linux 8 - x86_64                                                               548 kB/s | 2.0 MB     00:03
Last metadata expiration check: 0:00:01 ago on Mon 09 Sep 2024 12:15:15 PM +06.
Rocky Linux 8 - AppStream
Name                        Stream                         Profiles                             Summary
redis                       5 [d]                          common [d]                           Redis persistent key-value database
redis                       6 [e]                          common [d] [i]                       Redis persistent key-value database

Remi's Modular repository for Enterprise Linux 8 - x86_64
Name                        Stream                         Profiles                             Summary
redis                       remi-5.0                       common [d]                           Redis persistent key-value database
redis                       remi-6.0                       common [d]                           Redis persistent key-value database
redis                       remi-6.2                       common [d]                           Redis persistent key-value database
redis                       remi-7.0                       common [d]                           Redis persistent key-value database
redis                       remi-7.2                       common [d]                           Redis persistent key-value database

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
```




```
yum module reset redis -y 
```


```
yum module install redis:remi-7.0
```



```
redis-server --version

Redis server v=7.0.15 sha=00000000:0 malloc=jemalloc-5.2.1 bits=64 build=2539154d11bf479d
```


```
yum remove redis -y 
```



### Redis Configure:
```

#### NETWORK ####
bind 0.0.0.0

#### SECURITY ####
requirepass redis
```


```
systemctl restart redis
```


```
redis-cli ping
(error) NOAUTH Authentication required.
```

```
redis-cli
127.0.0.1:6379> AUTH redis
OK

127.0.0.1:6379> ping
PONG

127.0.0.1:6379> ping "Hello Redis"
"Hello Redis"
```


```
redis-cli -h 192.168.10.190 -p 6379

192.168.10.190:6379> ping
(error) NOAUTH Authentication required.

192.168.10.190:6379> AUTH redis
OK
```


### Links:
- [redis.io](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-on-linux/)
- [Install Redis on CentOS 7](https://www.howtoforge.com/how-to-install-and-secure-redis-on-centos-7/)




