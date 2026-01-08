## Three-Physical-Node DC/DR Redis Cluster:

Below is a clean, production-grade DC/DR Redis Cluster design using 3 physical nodes and 6 Redis nodes (3 masters + 3 replicas), exactly with your IPs.



### Physical Hosts:
Each physical node runs 2 Redis containers.

| Host   | IP                 |
| ------ | ------------------ |
| Node-A | **192.168.10.191** |
| Node-B | **192.168.10.192** |
| Node-C | **192.168.10.193** |


### High-Availability Goal:

- 3 masters on different hosts
- Each master’s replica on a different host
- Cluster survives **1 full host failure**
- DC / DR safe for single-node failure
- Persistent data



### Golden Rule (Remember This):

- Redis Cluster needs an ODD number of masters on DIFFERENT physical hosts

| Physical Nodes | Safe? |
| -------------- | ----- |
| 1              | ❌     |
| 2              | ❌     |
| **3**          | ✅     |
| 5+             | ✅     |



### Create Directory: (ALL NODES)

```
mkdir -p /opt/redis-cluster

cd /opt/redis-cluster

mkdir -p data
```


### Redis Configuration: (ALL NODES)

```bash 
## redis.conf

bind 0.0.0.0
protected-mode no

cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 10000

dir /data

appendonly yes

```


### Docker Compose:

#### Node-A (192.168.10.191):

_Run containers: redis-1 (master) + redis-6 (replica):_

```yaml
## docker-compose.yml

services:
  redis-1:
    image: redis:7
    container_name: redis-1
    command: redis-server /etc/redis.conf --port 7001  --cluster-announce-ip 192.168.10.191 --cluster-announce-port 7001  --cluster-announce-bus-port 17001
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7001:/data
    ports:
      - "7001:7001"
      - "17001:17001"

  redis-6:
    image: redis:7
    container_name: redis-6
    command: redis-server /etc/redis.conf --port 7006  --cluster-announce-ip 192.168.10.191 --cluster-announce-port 7006  --cluster-announce-bus-port 17006
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7006:/data
    ports:
      - "7006:7006"
      - "17006:17006"

```


#### Node-B (192.168.10.192):

_Run containers: redis-2 (master) + redis-4 (replica):_

```yaml
## docker-compose.yml

services:
  redis-2:
    image: redis:7
    container_name: redis-2
    command: redis-server /etc/redis.conf --port 7002 --cluster-announce-ip 192.168.10.192 --cluster-announce-port 7002 --cluster-announce-bus-port 17002
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7002:/data
    ports:
      - "7002:7002"
      - "17002:17002"

  redis-4:
    image: redis:7
    container_name: redis-4
    command: redis-server /etc/redis.conf --port 7004 --cluster-announce-ip 192.168.10.192 --cluster-announce-port 7004 --cluster-announce-bus-port 17004
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7004:/data
    ports:
      - "7004:7004"
      - "17004:17004"

```



#### Node-C (192.168.10.193):

_Run containers: redis-3 (master) + redis-5 (replica):_

```yaml
## docker-compose.yml

services:
  redis-3:
    image: redis:7
    container_name: redis-3
    command: redis-server /etc/redis.conf --port 7003 --cluster-announce-ip 192.168.10.193 --cluster-announce-port 7003 --cluster-announce-bus-port 17003
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7003:/data
    ports:
      - "7003:7003"
      - "17003:17003"

  redis-5:
    image: redis:7
    container_name: redis-5
    command: redis-server /etc/redis.conf --port 7005 --cluster-announce-ip 192.168.10.193 --cluster-announce-port 7005 --cluster-announce-bus-port 17005
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./data/7005:/data
    ports:
      - "7005:7005"
      - "17005:17005"

```



### Start Redis on ALL Nodes:

```
docker compose up -d
```


### Create the Cluster (RUN ONCE):

```
docker exec -it redis-1 bash

redis-cli --cluster create \
192.168.10.191:7001 \
192.168.10.192:7002 \
192.168.10.193:7003 \
192.168.10.192:7004 \
192.168.10.193:7005 \
192.168.10.191:7006 \
--cluster-replicas 1
```





### Verify Cluster:

```
redis-cli -c -p 7001 cluster info
redis-cli -c -p 7001 cluster nodes
```


```
redis-cli -c -h 192.168.10.191 -p 7001 set user:1 "kingkong"

redis-cli -c -h 192.168.10.192 -p 7002 get user:1
redis-cli -c -h 192.168.10.193 -p 7003 get user:1
```



### Failure Test:

#### Shutdown physical node:
- Replica promoted to master
- Cluster stays ONLINE
- No data loss

```
shutdown -h now   # on 192.168.10.192
```


### Final Truth Table:

| Failure           | Result      |
| ----------------- | ----------- |
| 1 Redis container | ✅ OK        |
| 1 physical host   | ✅ OK        |
| 2 physical hosts  | ❌ DOWN      |
| Network partition | ❌ Protected |



### This Architecture Gives You:

- True Redis Cluster HA
- DC / DR readiness
- Automatic failover
- Persistent data
- Production-safe design




### Why 2 Physical Nodes is a Problem (Redis Cluster):

- Redis Cluster requires majority of masters.
    - Minimum safe cluster: 3 masters
- With 2 physical nodes:
    - If 1 node goes down → 50% loss
    - Redis cannot determine who is authoritative
    - Split-brain risk
    - Cluster becomes read-only or FAILS

#### What happens in reality:

_Scenario:_
| Node A        | Node B | Result                   |
| ------------- | ------ | ------------------------ |
| UP            | UP     | ✅ OK                     |
| DOWN          | UP     | ❌ Cluster FAILS          |
| Network split | UP     | ❌ Split-brain protection |



