
## Single-Node Redis Cluster (6 Nodes): 

Below is a simple, working way to run a 6-node Redis Cluster using Docker (3 masters + 3 replicas). This is the most common setup.


_This setup is perfect for:_
- Learning
- Testing
- Development
- Demo
- CI/CD validation




### Architecture:
- Node: `192.168.10.193` 
- 6 Redis nodes (All Redis nodes run on **one server**)
- Ports: 7001–7006
- Cluster bus ports: 17001–17006
- Persistent data volumes
- Cluster mode enabled
- `cluster-announce-ip` MUST be host IP
- Auto-configured using `redis-cli --cluster create`



### Create Directory:

```
mkdir -p /opt/redis-cluster
cd /opt/redis-cluster

mkdir -p 7001 7002 7003 7004 7005 7006
```



### Redis Configuration:

_Create file on `/opt/redis-cluster/redis.conf`:_
```bash
## redis.conf

bind 0.0.0.0
protected-mode no

cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000

appendonly yes

#dir /data

cluster-announce-ip 192.168.10.193

```



### Docker Compose: 

_Create docker compose file on `/opt/redis-cluster/docker-compose.yml`:_
```yaml
## docker-compose.yml

services:
  redis-1:
    image: redis:7
    container_name: redis-1
    command: redis-server /etc/redis.conf --port 7001 --cluster-announce-port 7001 --cluster-announce-bus-port 17001
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7001:/data
    ports:
      - "7001:7001"
      - "17001:17001"

  redis-2:
    image: redis:7
    container_name: redis-2
    command: redis-server /etc/redis.conf --port 7002 --cluster-announce-port 7002 --cluster-announce-bus-port 17002
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7002:/data
    ports:
      - "7002:7002"
      - "17002:17002"

  redis-3:
    image: redis:7
    container_name: redis-3
    command: redis-server /etc/redis.conf --port 7003 --cluster-announce-port 7003 --cluster-announce-bus-port 17003
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7003:/data
    ports:
      - "7003:7003"
      - "17003:17003"

  redis-4:
    image: redis:7
    container_name: redis-4
    command: redis-server /etc/redis.conf --port 7004 --cluster-announce-port 7004 --cluster-announce-bus-port 17004
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7004:/data
    ports:
      - "7004:7004"
      - "17004:17004"

  redis-5:
    image: redis:7
    container_name: redis-5
    command: redis-server /etc/redis.conf --port 7005 --cluster-announce-port 7005 --cluster-announce-bus-port 17005
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7005:/data
    ports:
      - "7005:7005"
      - "17005:17005"

  redis-6:
    image: redis:7
    container_name: redis-6
    command: redis-server /etc/redis.conf --port 7006 --cluster-announce-port 7006 --cluster-announce-bus-port 17006
    volumes:
      - ./redis.conf:/etc/redis.conf
      - ./7006:/data
    ports:
      - "7006:7006"
      - "17006:17006"

```



```
docker compose up -d
```


### Create the Redis Cluster:

_Run ONCE:_
```
docker exec -it redis-1 redis-cli --cluster create \
192.168.10.193:7001 \
192.168.10.193:7002 \
192.168.10.193:7003 \
192.168.10.193:7004 \
192.168.10.193:7005 \
192.168.10.193:7006 \
--cluster-replicas 1
```




### Verify cluster: 

```
docker exec -it redis-1 bash 

redis-cli -p 7001 cluster info      # check if the status is OK
redis-cli -p 7001 cluster nodes     # check the cluster node
redis-cli -p 7001 -c                # enter the cluster mode
```



_To install `redis-cli` on **Ubuntu** and **CentOS**:_
```
apt install redis-tools -y
yum install redis -y
```


```
redis-cli -h 192.168.10.193 -p 7001 cluster nodes  
redis-cli -h 192.168.10.193 -p 7001 -c 
```


### Test persistence:


```
redis-cli -c -h 192.168.10.193 -p 7001 set user:1 "kingkong"

docker restart redis-1

redis-cli -c -h 192.168.10.193 -p 7001 get user:1
```



