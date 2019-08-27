# Redis

## 1. 简介
开源，支持网络，基于内存亦可持久化的日志型、key-value数据库（非关系型
数据库）

## 2. 安装
```shell
install make
install gcc
make
```
![error](https://github.com/CookedFox/pics/blob/master/db/redis/redis1.png?raw=true)
原因是jemalloc重载了Linux下的ANSI C的malloc和free函数
```
make MALLOC=libc
```