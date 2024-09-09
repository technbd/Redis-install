## Install Redis from source: 

To install Redis 5 from the source on CentOS 7, follow these steps. Installing from source ensures you get the exact version and full control over the installation.



```
yum install gcc make tcl -y
```



### Download Redis 5 Source: 


```
mkdir -p /redis/5.0.5
```



```
cd /redis/5.0.5

wget https://download.redis.io/releases/redis-5.0.5.tar.gz

tar -xvf redis-5.0.5.tar.gz
```


```
ll redis-5.0.5

-rw-rw-r--  1 root root 105K May 15  2019 00-RELEASENOTES
-rw-rw-r--  1 root root   53 May 15  2019 BUGS
-rw-rw-r--  1 root root 2.4K May 15  2019 CONTRIBUTING
-rw-rw-r--  1 root root 1.5K May 15  2019 COPYING
drwxrwxr-x  6 root root  124 May 15  2019 deps
-rw-rw-r--  1 root root  376 May 15  2019 .gitignore
-rw-rw-r--  1 root root   11 May 15  2019 INSTALL
-rw-rw-r--  1 root root  151 May 15  2019 Makefile
-rw-rw-r--  1 root root 6.8K May 15  2019 MANIFESTO
-rw-rw-r--  1 root root  21K May 15  2019 README.md
-rw-rw-r--  1 root root  61K May 15  2019 redis.conf
-rwxrwxr-x  1 root root  275 May 15  2019 runtest
-rwxrwxr-x  1 root root  280 May 15  2019 runtest-cluster
-rwxrwxr-x  1 root root  341 May 15  2019 runtest-moduleapi
-rwxrwxr-x  1 root root  281 May 15  2019 runtest-sentinel
-rw-rw-r--  1 root root 9.5K May 15  2019 sentinel.conf
drwxrwxr-x  3 root root 4.0K May 15  2019 src
drwxrwxr-x 11 root root  182 May 15  2019 tests
drwxrwxr-x  8 root root 4.0K May 15  2019 utils
```


### Install Redis:

```
cd redis-5.0.5
```


```
make


cd src && make all
make[1]: Entering directory `/redis/5.0.5/redis-5.0.5/src'
    CC Makefile.dep
make[1]: Leaving directory `/redis/5.0.5/redis-5.0.5/src'
make[1]: Entering directory `/redis/5.0.5/redis-5.0.5/src'
rm -rf redis-server redis-sentinel redis-cli redis-benchmark redis-check-rdb redis-check-aof *.o *.gcda *.gcno *.gcov redis.info lcov-html Makefile.dep dict-benchmark
(cd ../deps && make distclean)
make[2]: Entering directory `/redis/5.0.5/redis-5.0.5/deps'

...
...

===============================================================================
jemalloc version   : 5.1.0-0-g0
library revision   : 2

CONFIG             : --with-version=5.1.0-0-g0 --with-lg-quantum=3 --with-jemalloc-prefix=je_ --enable-cc-silence 'CFLAGS=-std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops ' LDFLAGS=
CC                 : gcc
CONFIGURE_CFLAGS   : -std=gnu11 -Wall -Wsign-compare -Wundef -Wno-format-zero-length -pipe -g3 -fvisibility=hidden -O3 -funroll-loops
SPECIFIED_CFLAGS   : -std=gnu99 -Wall -pipe -g3 -O3 -funroll-loops
EXTRA_CFLAGS       :
CPPFLAGS           : -D_GNU_SOURCE -D_REENTRANT
CXX                : g++
CONFIGURE_CXXFLAGS : -fvisibility=hidden -O3
SPECIFIED_CXXFLAGS :
EXTRA_CXXFLAGS     :
LDFLAGS            :
EXTRA_LDFLAGS      :
DSO_LDFLAGS        : -shared -Wl,-soname,$(@F)
LIBS               : -lm  -lpthread -ldl
RPATH_EXTRA        :

XSLTPROC           : false
XSLROOT            :

PREFIX             : /usr/local
BINDIR             : /usr/local/bin
DATADIR            : /usr/local/share
INCLUDEDIR         : /usr/local/include
LIBDIR             : /usr/local/lib
MANDIR             : /usr/local/share/man

srcroot            :
abs_srcroot        : /redis/5.0.5/redis-5.0.5/deps/jemalloc/
objroot            :
abs_objroot        : /redis/5.0.5/redis-5.0.5/deps/jemalloc/

JEMALLOC_PREFIX    : je_
JEMALLOC_PRIVATE_NAMESPACE
                   : je_
install_suffix     :
malloc_conf        :
autogen            : 0
debug              : 0
stats              : 1
prof               : 0
prof-libunwind     : 0
prof-libgcc        : 0
prof-gcc           : 0
fill               : 1
utrace             : 0
xmalloc            : 0
log                : 0
lazy_lock          : 0
cache-oblivious    : 1
cxx                : 0
===============================================================================

...
...


LINK redis-server
    INSTALL redis-sentinel
    CC redis-cli.o
    LINK redis-cli
    CC redis-benchmark.o
    LINK redis-benchmark
    INSTALL redis-check-rdb
    INSTALL redis-check-aof

Hint: It's a good idea to run 'make test' ;)

make[1]: Leaving directory `/redis/5.0.5/redis-5.0.5/src'
```


_Now run the `make test` command to make sure the build is correct._
```
make test

cd src && make test
make[1]: Entering directory `/redis/5.0.5/redis-5.0.5/src'
    CC Makefile.dep
make[1]: Leaving directory `/redis/5.0.5/redis-5.0.5/src'
make[1]: Entering directory `/redis/5.0.5/redis-5.0.5/src'
Cleanup: may take some time... OK


...
...

!!! WARNING The following tests failed:

*** [err]: Connect a replica to the master instance (scripts replication) in tests/unit/scripting.tcl
Can't turn the instance into a replica
*** [err]: Active defrag big keys in tests/unit/memefficiency.tcl
Expected condition '$max_latency <= 120' to be true (124 <= 120)
Cleanup: may take some time... OK
make[1]: *** [test] Error 1
make[1]: Leaving directory `/redis/5.0.5/redis-5.0.5/src'
make: *** [test] Error 2
```


```
make install

cd src && make install
make[1]: Entering directory `/redis/5.0.5/redis-5.0.5/src'

Hint: It's a good idea to run 'make test' ;)

    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
    INSTALL install
make[1]: Leaving directory `/redis/5.0.5/redis-5.0.5/src'
```


```
ll src/redis*

-rw-rw-r-- 1 root root 2.4K May 15  2019 src/redisassert.h
-rwxr-xr-x 1 root root 4.2M Sep  9 21:38 src/redis-benchmark
-rw-rw-r-- 1 root root  29K May 15  2019 src/redis-benchmark.c
-rw-r--r-- 1 root root 107K Sep  9 21:38 src/redis-benchmark.o
-rwxr-xr-x 1 root root 7.8M Sep  9 21:38 src/redis-check-aof
-rw-rw-r-- 1 root root 7.1K May 15  2019 src/redis-check-aof.c
-rw-r--r-- 1 root root  29K Sep  9 21:37 src/redis-check-aof.o
-rwxr-xr-x 1 root root 7.8M Sep  9 21:38 src/redis-check-rdb
-rw-rw-r-- 1 root root  14K May 15  2019 src/redis-check-rdb.c
-rw-r--r-- 1 root root  65K Sep  9 21:37 src/redis-check-rdb.o
-rwxr-xr-x 1 root root 4.6M Sep  9 21:38 src/redis-cli
-rw-rw-r-- 1 root root 261K May 15  2019 src/redis-cli.c
-rw-r--r-- 1 root root 907K Sep  9 21:38 src/redis-cli.o
-rw-rw-r-- 1 root root  31K May 15  2019 src/redismodule.h
-rwxr-xr-x 1 root root 7.8M Sep  9 21:38 src/redis-sentinel
-rwxr-xr-x 1 root root 7.8M Sep  9 21:38 src/redis-server
-rwxrwxr-x 1 root root 3.6K May 15  2019 src/redis-trib.rb
```


```
ll /usr/local/bin/redis*

-rwxr-xr-x 1 root root 4.2M Sep  9 21:43 /usr/local/bin/redis-benchmark
-rwxr-xr-x 1 root root 7.8M Sep  9 21:43 /usr/local/bin/redis-check-aof
-rwxr-xr-x 1 root root 7.8M Sep  9 21:43 /usr/local/bin/redis-check-rdb
-rwxr-xr-x 1 root root 4.6M Sep  9 21:43 /usr/local/bin/redis-cli
lrwxrwxrwx 1 root root   12 Sep  9 21:43 /usr/local/bin/redis-sentinel -> redis-server
-rwxr-xr-x 1 root root 7.8M Sep  9 21:43 /usr/local/bin/redis-server
```



```
cat utils/redis_init_script
```



### Create Redis User:
- `--system` : This option creates a system account.
- `-g redis` : This option specifies the group for the user. In this case, the user will belong to the redis group.
- `--no-create-home` : his option ensures that no home directory is created for the user.
- `-M`, `--no-create-home` : do not create the user's home directory
- `redis` : This is the name of the user being created.


```
groupadd redis
adduser --system -g redis --no-create-home redis

adduser --system -M redis
```


```
chown -R redis:redis /redis
```



### Configure Redis:

```
mkdir -p /redis/5.0.5/data
mkdir -p /redis/5.0.5/log
```


```
ll /redis/5.0.5/redis-5.0.5/redis.conf

-rw-rw-r-- 1 root root 61K May 15  2019 /redis/5.0.5/redis-5.0.5/redis.conf
```


```
vim /redis/5.0.5/redis-5.0.5/redis.conf


### NETWORK ###
bind 0.0.0.0
port 6379


### GENERAL ###
daemonize yes
supervised systemd

pidfile /var/run/redis_6379.pid
loglevel notice

logfile /redis/5.0.5/log/redis.log


### SNAPSHOTTING  ###
dir /redis/5.0.5/data/


### SECURITY ###
requirepass redis


### APPEND ONLY MODE ###
appendonly yes
appendfilename "appendonly.aof"

appendfsync everysec
```


```
vim redis-505.sh

export REDIS_HOME=/redis/5.0.5/redis-5.0.5
export PATH=$REDIS_HOME/src:$PATH
```


```
cp redis-505.sh /etc/profile.d/
source /etc/profile.d/redis-505.sh
```


```
echo $REDIS_HOME
```


```
redis-server --version

Redis server v=5.0.5 sha=00000000:0 malloc=jemalloc-5.1.0 bits=64 build=4cbe76d6d538bfad
```


### Start Redis:

```
/redis/5.0.5/redis-5.0.5/src/redis-server /redis/5.0.5/redis-5.0.5/redis.conf

or,

redis-server /redis/5.0.5/redis-5.0.5/redis.conf
```


or,



_Shell Start:_
```
vim redis-start.sh 

/redis/5.0.5/redis-5.0.5/src/redis-server /redis/5.0.5/redis-5.0.5/redis.conf
```


```
chmod +x redis-start.sh 

./redis-start.sh 
```



```
pgrep redis

ps aux | grep redis
```



```
tail -f /redis/5.0.5/log/redis.log
```



### Stop Redis: 

```
kill -9 $(pgrep redis-server)
```

or,


_Shell Stop:_
```
vim redis-stop.sh 

kill -9 $(pgrep redis-server)
```


```
chmod +x redis-stop.sh 

./redis-stop.sh 
```


### Verify:

```
netstat -tlpn | grep 6379
```


```
redis-cli ping

PONG
```




### Links:

- [Redis releases list](https://download.redis.io/releases/)
- [Install Redis from Source](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-from-source/)
- [Install Redis on CentOS 7](https://azdigi.com/blog/en/linux-server-en/how-to-install-redis-6-on-centos-7/)
- [Setup Redis](https://www.linuxcloudvps.com/blog/how-to-set-up-redis-on-centos-7/)


