# docker

### 1.常用指令

```
docker ps -a
```

查看全部容器

```
docker stats 
```

查看实时容器运行状态

```
docker exec [OPTIONS] CONTAINER（id,name） COMMAND [ARG...]
```

OPTIONS说明：

- **-d :**分离模式: 在后台运行
- **-i :**即使没有附加也保持STDIN 打开
- **-t :**分配一个伪终端

##### 如：docker exec -it nginx /bin/sh（进入nginx容器，并打开sh命令行）

### # 坑

##### 1. 启动runner（https）

docker内部签署CA

ADD your_ca_root.crt /usr/local/share/ca-certificates/foo.crt
RUN update-ca-certificates

```
docker run -d --network=host --restart always \

-v ~:/etc/gitlab-runner \(映射crt)

-v /var/run/docker.sock:/var/run/docker.sock \（映射守护进程）

gitlab/gitlab-runner 
```

