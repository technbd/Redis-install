
## Redis 03-nodes cluster:

A Redis Cluster requires a minimum of three master nodes for proper operation. However, to ensure high availability, a minimum of six nodes (three masters + three replicas) is recommended.



#### Redis Clustering Components: 
A Redis cluster consists of the following components:

- Nodes: These are individual Redis instances that store your data and participate in the cluster.

- Slots: Redis Cluster divides the key space into 16,384 slots (0-16,383). Each key is assigned to a slot based on its hash, and each slot is assigned to a specific node.

- Master nodes: Master nodes store the data and are responsible for serving read and write requests for their assigned slots.

- Replica nodes: Replica nodes store copies of the data from their associated master node and can serve read requests, providing fault tolerance and load balancing.



#### Minimum Node Requirement for a Redis Cluster:
- 03 Nodes (Master Only) → No high availability, but cluster mode works.
- 06 Nodes (03 Masters + 03 Replicas) → High availability with failover support.


> [!NOTE]
> If you try to create a cluster with less than 3 nodes, Redis will not function properly because it requires a quorum of masters to elect a leader during failures.


#### Environment:
- Host1 : 192.168.10.190
- Host2 : 192.168.10.191
- Host3 : 192.168.10.192



## Configure Redis Cluster Mode:

Edit the Redis configuration file `redis.conf` on each node:

```
bind 0.0.0.0
protected-mode no
port 6379

appendonly yes

cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
```


```
mkdir -p /redis/5.0.5/redis-5.0.5/master1/data
```


```
cd /redis/5.0.5/redis-5.0.5
```


```
mv redis.conf master1-6379.conf
```



### Host-1: (192.168.10.190):

#### Configuration file for master1:

```
vim master1-6379.conf


bind 192.168.10.190

protected-mode no
port 6379

### GENERAL ###
daemonize no
supervised no

pidfile /var/run/redis_6379.pid

logfile ""
#logfile /redis/5.0.5/log/redis.log


### SNAPSHOTTING  ###
dbfilename dump.rdb
dir /redis/5.0.5/redis-5.0.5/master1/data


### MEMORY MANAGEMENT ###
maxmemory 1gb
maxmemory-policy volatile-lru


### APPEND ONLY MODE ###
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec


### REDIS CLUSTER  ###
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000

```


#### Start redis Master1:

```
screen -S redis-master1

/redis/5.0.5/redis-5.0.5/src/redis-server /redis/5.0.5/redis-5.0.5/master1-6379.conf
```


```
./src/redis-cli -h 192.168.10.190 -p 6379
```


```
192.168.10.190:6379> CLUSTER nodes

91ec05ed65ea06abfc7fc57fe070fd930fdce24c :6379@16379 myself,master - 0 0 0 connected
```


```
192.168.10.190:6379> client list
```



```
ll /redis/5.0.5/redis-5.0.5/master1/data/

-rw-r--r-- 1 root root  69 Mar  4 11:20 appendonly.aof
-rw-r--r-- 1 root root 122 Mar  4 11:20 dump.rdb
-rw-r--r-- 1 root root 385 Mar  4 11:28 nodes-6379.conf
```



### Host-2: (192.168.10.191):

#### Configuration file for master2:

```
vim master1-6379.conf


bind 192.168.10.191

protected-mode no
port 6379

### GENERAL ###
daemonize no
supervised no

pidfile /var/run/redis_6379.pid

logfile ""
#logfile /redis/5.0.5/log/redis.log


### SNAPSHOTTING  ###
dbfilename dump.rdb
dir /redis/5.0.5/redis-5.0.5/master1/data


### MEMORY MANAGEMENT ###
maxmemory 1gb
maxmemory-policy volatile-lru


### APPEND ONLY MODE ###
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec


### REDIS CLUSTER  ###
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000

```




#### Start redis Master2:

```
screen -S redis-master2

/redis/5.0.5/redis-5.0.5/src/redis-server /redis/5.0.5/redis-5.0.5/master1-6379.conf
```


```
./src/redis-cli -h 192.168.10.191 -p 6379
```


```
192.168.10.191:6379> CLUSTER nodes

600dbcf46ab5b415a67d823f0bda3246cd281f98 :6379@16379 myself,master - 0 0 0 connected
```


```
192.168.10.191:6379> client list
```





### Host-3: (192.168.10.192):

#### Configuration file for master3:

```
vim master1-6379.conf


bind 192.168.10.192

protected-mode no
port 6379

### GENERAL ###
daemonize no
supervised no

pidfile /var/run/redis_6379.pid

logfile ""
#logfile /redis/5.0.5/log/redis.log


### SNAPSHOTTING  ###
dbfilename dump.rdb
dir /redis/5.0.5/redis-5.0.5/master1/data


### MEMORY MANAGEMENT ###
maxmemory 1gb
maxmemory-policy volatile-lru


### APPEND ONLY MODE ###
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec


### REDIS CLUSTER  ###
cluster-enabled yes
cluster-config-file nodes-6379.conf
cluster-node-timeout 15000

```



#### Start redis Master3:

```
screen -S redis-master3

/redis/5.0.5/redis-5.0.5/src/redis-server /redis/5.0.5/redis-5.0.5/master1-6379.conf
```


```
./src/redis-cli -h 192.168.10.192 -p 6379
```


```
192.168.10.192:6379> CLUSTER nodes

64835832a9d2390dfdbefe86b39d26115dc50dd7 :6379@16379 myself,master - 0 0 0 connected
```


```
192.168.10.192:6379> client list
```


## Create the Redis Cluster:

Run the following command on any one node:

- Replace `<Node1-IP>`, `<Node2-IP>`, and `<Node3-IP>` with your actual node IPs.
- If you want replication, use `--cluster-replicas 1`.


_Syntax:_

```
redis-cli --cluster create <Node1_IP>:6379 <Node2_IP>:6379 <Node3_IP>:6379

or,

redis-cli --cluster create <Node1_IP>:6379 <Node2_IP>:6379 <Node3_IP>:6379 --cluster-replicas 0
```


```
redis-cli --cluster create 192.168.10.190:6379 192.168.10.191:6379 192.168.10.192:6379
```


```
### output:

>>> Performing hash slots allocation on 3 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
M: 91ec05ed65ea06abfc7fc57fe070fd930fdce24c 192.168.10.190:6379
   slots:[0-5460] (5461 slots) master
M: 600dbcf46ab5b415a67d823f0bda3246cd281f98 192.168.10.191:6379
   slots:[5461-10922] (5462 slots) master
M: 64835832a9d2390dfdbefe86b39d26115dc50dd7 192.168.10.192:6379
   slots:[10923-16383] (5461 slots) master

Can I set the above configuration? (type 'yes' to accept): yes

>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node 192.168.10.190:6379)
M: 91ec05ed65ea06abfc7fc57fe070fd930fdce24c 192.168.10.190:6379
   slots:[0-5460] (5461 slots) master
M: 64835832a9d2390dfdbefe86b39d26115dc50dd7 192.168.10.192:6379
   slots:[10923-16383] (5461 slots) master
M: 600dbcf46ab5b415a67d823f0bda3246cd281f98 192.168.10.191:6379
   slots:[5461-10922] (5462 slots) master
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```


### Verify:

```
./src/redis-cli -c -h 192.168.10.190 -p 6379 cluster nodes

64835832a9d2390dfdbefe86b39d26115dc50dd7 192.168.10.192:6379@16379 master - 0 1741064867708 3 connected 10923-16383
600dbcf46ab5b415a67d823f0bda3246cd281f98 192.168.10.191:6379@16379 master - 0 1741064865684 2 connected 5461-10922
91ec05ed65ea06abfc7fc57fe070fd930fdce24c 192.168.10.190:6379@16379 myself,master - 0 1741064864000 1 connected 0-5460
```





```
./src/redis-cli -c -h 192.168.10.190 -p 6379 cluster info


cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:3
cluster_size:3
cluster_current_epoch:3
cluster_my_epoch:1
cluster_stats_messages_ping_sent:320
cluster_stats_messages_pong_sent:356
cluster_stats_messages_sent:676
cluster_stats_messages_ping_received:354
cluster_stats_messages_pong_received:320
cluster_stats_messages_meet_received:2
cluster_stats_messages_received:676
```


```
./src/redis-cli -c -h 192.168.10.190 -p 6379 INFO replication


# Replication
role:master
connected_slaves:0
master_replid:c2cdf9c98a348d8becf81e34fdd30055a362aa04
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```



```
./src/redis-cli -c -h 192.168.10.190 -p 6379 cluster slots

```


### Testing the Cluster: 

- To enable cluster mode `-c` when using `redis-cli` and Redis will **automatically route** the command to the correct node.

```
./src/redis-cli -c -h 192.168.10.190 -p 6379


set foo bar
get foo

del foo
```


```
./src/redis-cli -c -h 192.168.10.191 -p 6379

get foo
```


```
./src/redis-cli -c -h 192.168.10.192 -p 6379

get foo
```



If you need automatic failover, consider setting up Redis Sentinel.