# docker

### 1.常用指令

- ##### 查看全部容器

```
docker ps -a
```

------

- ##### 查看实时容器运行状态

```
docker stats 
```

------

- ##### 进入容器

```
docker exec [OPTIONS] CONTAINER（id,name） COMMAND [ARG...]
```

OPTIONS说明：

- **-d :**分离模式: 在后台运行
- **-i :**即使没有附加也保持STDIN 打开
- **-t :**分配一个伪终端

##### 如：docker exec -it nginx /bin/sh（进入nginx容器，并打开sh命令行）

------

- ##### 查看容器运行日志

```
docker logs --tail 50 --follow --timestamps [container]
```

------

- ##### 启动容器

```
Usage: docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]

-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-d: 后台运行容器，并返回容器ID；
-i: 以交互模式运行容器，通常与 -t 同时使用；
-P: 随机端口映射，容器内部端口随机映射到主机的高端口
-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
--name="nginx-lb": 为容器指定一个名称；
--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
-h "mars": 指定容器的hostname；
-e username="ritchie": 设置环境变量；
--env-file=[]: 从指定文件读入环境变量；
--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
-m :设置容器使用内存最大值；
--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
--link=[]: 添加链接到另一个容器；
--expose=[]: 开放一个端口或一组端口；
--volume , -v: 绑定一个卷
```

### 2.gitlab-runner in Docker（docker-in-docker）

- ##### start gitlab-runner

```
docker run -d --network=host --restart always \
-v ~/gitlab-runner:/etc/gitlab-runner \
-v /var/run/docker.sock:/var/run/docker.sock \
gitlab/gitlab-runner 
```

命令说明

- 映射宿主机的套接字

  -v /var/run/docker.sock:/var/run/docker.sock

- 映射配置文件路径

  -v ~/gitlab-runner:/etc/gitlab-runner

------

- ##### register runner

```
gitlab-runner register \
 --non-interactive \
 --executor "docker" \
 --docker-image alpine:latest \
 --docker-privileged \
 --url "https://os1.com/" \
 --registration-token "y_6sthX4mvh9MdyJEFpq" \
 --description "os2" \
 --tag-list "devolop-tag" \
 --run-untagged="true" \
 --locked="false" \
 --tls-ca-file=/etc/gitlab-runner/ca.crt
```

参数说明

- 使用自签证书

  --tls-ca-file=/etc/gitlab-runner/ca.crt

### 3. docker的持久化

```
Docker的数据持久化主要有两种方式：

- bind mount

- volume

Docker的数据持久化即使数据不随着container的结束而结束，数据存在于host机器上——要么存在于host的某个指定目录中（使用bind mount），要么使用docker自己管理的volume（/var/lib/docker/volumes下）。
```

- #### bind mount

bind mount自docker早期便开始为人们使用了，用于将host机器的目录mount到container中。但是bind mount在不同的宿主机系统时不可移植的，比如Windows和Linux的目录结构是不一样的，bind mount所指向的host目录也不能一样。这也是为什么bind mount不能出现在Dockerfile中的原因，因为这样Dockerfile就不可移植了。

将host机器上当前目录下的host-data目录mount到container中的/container-data目录：

```bash
docker run -it -v $(pwd)/host-dava:/container-data alpine sh
```

有几点需要注意：

- host机器的目录路径必须为全路径(准确的说需要以`/`或`~/`开始的路径)，不然docker会将其当做volume
- 如果host机器上的目录不存在，docker会自动创建该目录
- 如果container中的目录不存在，docker会自动创建该目录
- 如果container中的目录已经有内容，那么docker会使用host上的目录将其覆盖掉

- ### volume

volume也是绕过container的文件系统，直接将数据写到host机器上，只是volume是被docker管理的，docker下所有的volume都在host机器上的指定目录下/var/lib/docker/volumes。

- 查看所有volume

```bash
docker volume ls
```





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

