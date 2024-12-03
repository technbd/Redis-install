

## Install Redis v3.2.12:

Redis 3.2.12 is an older version, and installing it might require specific steps since many modern package managers focus on newer releases. Here’s how to install Redis 3.2.12 on Rocky Linux 8:




```
dnf groupinstall "Development Tools" -y

dnf install gcc make
```


### Download Redis Binary: 


```
mkdir /redis
cd /redis
```


```
wget http://download.redis.io/releases/redis-3.2.12.tar.gz
```



### Extract and Build: 

```
tar -xvzf redis-3.2.12.tar.gz
```



```
cd redis-3.2.12
```


```
make
make test 
make install 
```


```
ll src/redis*
```


```
//cp src/redis-server /usr/local/bin/
//cp src/redis-cli /usr/local/bin/
```



```
ll /usr/local/bin/redis*
```



### Configure Redis:


```
useradd --system -M redis
chown -R redis:redis /redis
```


```
mkdir -p /redis/redis-3.2.12/data
mkdir -p /redis/redis-3.2.12/log
```


_Edit the configuration file `redis.conf` as needed:_

```
vim /redis/redis-3.2.12/redis.conf


bind 0.0.0.0

#logfile ""
logfile "/redis/redis-3.2.12/log/redis.log"

#dir ./
dir /redis/redis-3.2.12/data

appendonly yes
```



### Create ENV File: 


```
vim redis-3.sh

export REDIS_HOME=/redis/redis-3.2.12
export PATH=$REDIS_HOME/src:$PATH
```


```
cp redis-3.sh /etc/profile.d
source /etc/profile.d/redis-3.sh
```


```
echo $REDIS_HOME
```


```
redis-server --version

Redis server v=3.2.12 sha=00000000:0 malloc=jemalloc-4.0.3 bits=64 build=c7e33a5097f78d2b
```



### Start MongoDB:

```
redis-server /redis/redis-3.2.12/redis.conf
```




```
redis-cli
```




```
netstat -tlpn
```



### Start MongoDB using shell:


```
vim redis-start.sh

redis-server /redis/redis-3.2.12/redis.conf
```


```
chmod +x redis-start.sh

./redis-start.sh
```


### Stop MongoDB using shell:


```
ps aux | grep redis-server
```


```
vim redis-stop.sh

kill -9 $(pgrep redis-server)
```



```
chmod +x redis-stop.sh

./redis-stop.sh
```






### Redis as a system service: [Worked]

Create a systemd service file `/etc/systemd/system/redis.service`:


```
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
#ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
#ExecStop=/usr/local/bin/redis-cli shutdown

ExecStart=/redis/redis-3.2.12/src/redis-server /redis/redis-3.2.12/redis.conf
ExecStop=/redis/redis-3.2.12/src/redis-cli shutdown

Restart=always
User=redis
Group=redis

[Install]
WantedBy=multi-user.target
```


```
cp redis.service /etc/systemd/system
```



```
systemctl daemon-reload
```


```
systemctl start redis
systemctl status redis
```



_Ensure the Redis directory is owned by the `redis` user:_

```
chown -R redis:redis /redis
```



