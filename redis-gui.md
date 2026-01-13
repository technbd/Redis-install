## Redis GUI (RedisInsight): 

This document explains how to connect **RedisInsight** to a **Redis instance** running on one server.



### Architecture Overview:

| Component    | IP              | Port   |
|--------------|-----------------|--------|
| Redis Server | 192.168.10.192  | 6379   |
| RedisInsight (GUI) |       Any | 5540   |



### Why RedisInsight?

- Official Redis GUI
- Works with standalone Redis
- Key browsing & editing
- Memory analysis
- Slow log & command insights
- Free & production-ready



### Install RedisInsight (Docker):

Run RedisInsight on any machine:

```bash
docker run --name redisinsight -d -p 5540:5540 redis/redisinsight:latest
```



### GUI Console:

_Open in browser:_
```
http://<GUI_HOST_IP>:5540
```


