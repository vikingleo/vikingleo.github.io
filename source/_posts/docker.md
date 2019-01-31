---
title: docker 命令记录
tags:
  - docker
originContent: ''
categories:
  - 容器
toc: true
date: 2019-01-29 16:44:38
---

# 进入容器bash
```
docker exec -it [container_name] /bin/bash
```

# 查询IP
```
docker inspect [container_name] | grep "IPAddress"
```
# 容器热重载
```
docker exec -i [container_name] /usr/sbin/nginx -s reload
```