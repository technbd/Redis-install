## Redis Sentinel (2 Physical Nodes):


### Why Sentinel (not Cluster)?

- Redis Cluster requires 3 masters
- With 2 servers → quorum problem
- Sentinel solves this by:
    - 1 master
    - 1 replica
    - 3 Sentinels (quorum = 2)

- Automatic failover
- No split-brain
- Safe for DC/DR





### Physical Hosts:

| Host                                 | IP                 | Role                     |
| ------------------------------------ | ------------------ | ------------------------ |
| Node-A                               | **192.168.10.191** | Redis Master + Sentinel  |
| Node-B                               | **192.168.10.192** | Redis Replica + Sentinel |
| Node-C (tiny VM / same DC / cloud)** | **192.168.10.193** | Sentinel ONLY            |



### Create Directory: 

```
mkdir -p /opt/redis-sentinel
cd /opt/redis-sentinel
mkdir data
mkdir sentinel-data
```



### Redis Configuration:

#### Node-A - Redis Master (`redis-master.conf`)
```bash
## redis-master.conf

bind 0.0.0.0
protected-mode no
port 6379

appendonly yes
dir /data

replica-announce-ip 192.168.10.191
replica-announce-port 6379
```



#### Node-B - Redis Replica (`redis-replica.conf`)

```bash 
## redis-replica.conf

bind 0.0.0.0
protected-mode no
port 6379

replicaof 192.168.10.191 6379

appendonly yes
dir /data

replica-announce-ip 192.168.10.192
replica-announce-port 6379
```


### Sentinel Configuration: 

#### Node-A - Redis Master (`sentinel.conf`)

_Create file on `sentinel.conf`:_

```bash
## sentinel.conf

port 26379
bind 0.0.0.0
protected-mode no


sentinel announce-ip 192.168.10.191
sentinel announce-port 26379

sentinel monitor mymaster 192.168.10.191 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
sentinel parallel-syncs mymaster 1

```



#### Node-B - Redis Replica (`sentinel.conf`)

```bash
## sentinel.conf

port 26379
bind 0.0.0.0
protected-mode no


sentinel announce-ip 192.168.10.192
sentinel announce-port 26379

sentinel monitor mymaster 192.168.10.191 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
sentinel parallel-syncs mymaster 1

```



#### Node-C - Redis Sentinel Only (`sentinel.conf`)

```bash
## sentinel.conf

port 26379
bind 0.0.0.0
protected-mode no


sentinel announce-ip 192.168.10.193
sentinel announce-port 26379

sentinel monitor mymaster 192.168.10.191 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
sentinel parallel-syncs mymaster 1

```




### Docker Compose:

#### Node-A (Master):

```yaml
## docker-compose.yml

services:
  redis:             ## Redis Master
    image: redis:7
    container_name: redis-master
    command: redis-server /etc/redis.conf
    volumes:
      - ./redis-master.conf:/etc/redis.conf
      - ./data:/data
    ports:
      - "6379:6379"

  sentinel:         ## Redis Sentinel
    image: redis:7
    container_name: redis-sentinel
    command: redis-sentinel /etc/sentinel.conf
    volumes:
      - ./sentinel.conf:/etc/sentinel.conf
      - ./sentinel-data:/data
    ports:
      - "26379:26379"

```



#### Node-B (Replica):

```yaml
## docker-compose.yml

services:
  redis:               ## Redis Replica
    image: redis:7
    container_name: redis-replica
    command: redis-server /etc/redis.conf
    volumes:
      - ./redis-replica.conf:/etc/redis.conf
      - ./data:/data
    ports:
      - "6379:6379"

  sentinel:            ## Redis Sentinel
    image: redis:7
    container_name: redis-sentinel
    command: redis-sentinel /etc/sentinel.conf
    volumes:
      - ./sentinel.conf:/etc/sentinel.conf
      - ./sentinel-data:/data
    ports:
      - "26379:26379"

```



#### Node-C (Sentinel Only):

```yaml 
## docker-compose.yml

services:
  sentinel:             ## Redis Sentinel
    image: redis:7
    container_name: redis-sentinel
    command: redis-sentinel /etc/sentinel.conf
    volumes:
      - ./sentinel.conf:/etc/sentinel.conf
      - ./sentinel-data:/data
    ports:
      - "26379:26379"

```




### Start the Redis Containers: 

```
docker compose up -d
```



### Verify Sentinel Status:

```
## On Sentinel node (Node-A): 
docker exec -it redis-sentinel bash


redis-cli -p 26379 sentinel get-master-addr-by-name mymaster

redis-cli -p 26379 sentinel masters
redis-cli -p 26379 sentinel slaves mymaster
redis-cli -p 26379 sentinel sentinels mymaster
```


### Failover Test:


#### Test persistence:

```
docker exec -it redis-master bash


redis-cli -p 6379 set user:1 "kingkong"
redis-cli -p 6379 get user:1
```


#### Stop master: Node-A

```
docker stop redis-master
```


#### Check from Node-B: 

```
docker exec -it redis-replica bash

redis-cli -p 6379 get user:1


## To Write data: 
redis-cli -p 6379 set user:2 "idea"
redis-cli -p 6379 get user:2
```




### What REALLY happens during failover: 

#### Scenario: Node-A (Master) goes DOWN:
1. Sentinel on Node-B + Node-C agree (quorum = 2)
2. Redis on Node-B is promoted to MASTER
3. Sentinel updates:
    - Master IP
    - Sentinel config

4. When Node-A comes back:
    - It becomes REPLICA automatically
    - Data is resynced
5. NO split-brain
6. NO manual action required













