# Docker

[toc]



## 安装docker

官网帮助文档：

##### 1、卸载旧的版本

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

##### 2、使用yum安装

```shell
yum install yum-utils
```

##### 3、设置镜像仓库

```shell
yum-config-manager \
	 --add-repo \
	 http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    ##修改仓库地址为国内镜像源
```

##### 4、安装最新版

ce 为社区版

```shell
yum install docker-ce docker-ce-cli containerd.io
```

##### 5、启动docker

```shell
systemctl start docker
#查看docker信息
[root@localhost ~]# docker version
Client: Docker Engine - Community
 Version:           20.10.1
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        831ebea
 Built:             Tue Dec 15 04:37:17 2020
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.1
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       f001486
  Built:            Tue Dec 15 04:35:42 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc92
  GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

##### 6、测试docker

> 启动docker后，使用hello-world测试，会自动下载镜像与运行

```shell
docker run hello-world
```

![image-20201226202016606](C:\Users\lx\Desktop\Docker.assets\image-20201226202016606.png)

##### 7、验证镜像

> docker 默认资源路劲 `/var/lib/docker`

```shell
docker images
```

![image-20201226202854179](C:\Users\lx\Desktop\Docker.assets\image-20201226202854179.png)

##### 8、卸载docker

```shell
yum remove docker-ce docker-ce-cli containerd.io
rm -rf /var/lib/docker
```



##### 9、国内仓库镜像加速

```shell
mkdir -p /etc/docker/
tee daemon.json <<-'EOF'
{
"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
EOF
systemctl daemon-reload
systemctl restart docker
```



#### docker运行流程

![image-20201226202016606](C:\Users\lx\Desktop\Docker.assets\image-20201226202016606.png)

![image-20201226204503402](C:\Users\lx\Desktop\Docker.assets\image-20201226204503402.png)

#### docker基本原理

------

docker是C-S架构，守护进程运行在主机上，通过Socket从客户端访问

DockerServer接收到Docker-Client的指令去执行响应的命令

![image-20201226205247592](C:\Users\lx\Desktop\Docker.assets\image-20201226205247592.png)

与VM的区别

> 容器的抽象层比VM少
>
> 容器利用宿主机的内核，VM需要完整Guest OS

![img](https://uploadfiles.nowcoder.com/files/20200820/192185794_1597925441812_u32288082223079667590fm26gp0.jpg)

#### Docker常用命令

##### 帮助命令

> 官方文档https://docs.docker.com/reference/

```shell
docker version	#版本信息
docker info 	#完整信息
docker 命令 --help
```

#### 镜像命令

docker images 查看所有本地的镜像

```shell
[root@localhost docker]# docker images 
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    bf756fb1ae65   11 months ago   13.3kB

# REPOSITORY	镜像的仓库源
# TAG			镜像的标签
# IMAGE ID 		镜像的ID
# CREATE		镜像的创建时间
# SIZE			镜像的大小

# 可选项
Options:
  -a, --all             #列出所有镜像
      --digests         #Show digests
  -f, --filter filter   #Filter output based on conditions provided
      --format string   #Pretty-print images using a Go template
      --no-trunc        #Don't truncate output
  -q, --quiet           #只显示镜像的id
```

##### docker search 镜像搜索

```shell
[root@localhost docker]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10308     [OK]       
mariadb                           MariaDB is a community-developed fork of MyS…   3819      [OK]       
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   754                  [OK]

#可选项

Options:
  -f, --filter filter   #条件筛选，如 --filter=STARS=3000
      --format string   Pretty-print search using a Go template
      --limit int       Max number of search results (default 25)
      --no-trunc        Don't truncate output
```

docker pull 镜像下载

```shell
# 下载镜像 docker pull 镜像名称[:tag] 版本
[root@localhost docker]# docker pull mysql
Using default tag: latest # 如果写版本号，默认下载最新版
latest: Pulling from library/mysql
6ec7b7d162b2: Pull complete 		#分层下载，docker images 核心，联合文件系统
fedd960d3481: Pull complete 
7ab947313861: Pull complete 
64f92f19e638: Pull complete 
3e80b17bff96: Pull complete 
014e976799f9: Pull complete 
59ae84fee1b3: Pull complete 
ffe10de703ea: Pull complete 
657af6d90c83: Pull complete 
98bfb480322c: Pull complete 
6aa3859c4789: Pull complete 
1ed875d851ef: Pull complete 
Digest: sha256:78800e6d3f1b230e35275145e657b82c3fb02a27b2d8e76aac2f5e90c1c30873  #签名
Status: Downloaded newer image for mysql:latest		#下载结果通知
docker.io/library/mysql:latest		#真实地址
##
## 真实地址 ：docker pull mysql 等价于 docker pull docker.io/library/mysql:latest
##
[root@localhost docker]# docker pull mysql:5.7 # 指定镜像版本下载
5.7: Pulling from library/mysql
6ec7b7d162b2: Already exists 
fedd960d3481: Already exists 
7ab947313861: Already exists 
64f92f19e638: Already exists 
3e80b17bff96: Already exists 
014e976799f9: Already exists 
59ae84fee1b3: Already exists 		#已经存在不需要下载
7d1da2a18e2e: Pull complete 		#不存在增量下载
301a28b700b9: Pull complete 
529dc8dbeaf3: Pull complete 
bc9d021dc13f: Pull complete 
Digest: sha256:c3a567d3e3ad8b05dfce401ed08f0f6bf3f3b64cc17694979d5f2e5d78e10173
Status: Downloaded newer image for mysql:5.7 	
docker.io/library/mysql:5.7	
```

![image-20201226213656882](C:\Users\lx\Desktop\Docker.assets\image-20201226213656882.png)

##### docker rmi  删除镜像

```shell
docker rmi -f 镜像id1 镜像id2   #强制删除镜像
docker rmi $(docker images -qa)	#删除所有镜像
```

![image-20201226214051944](C:\Users\lx\Desktop\Docker.assets\image-20201226214051944.png)



#### 容器命令

-----

> 有了镜像才能创建容器

```shell
docker pull centos
```

##### 新建容器并启动

```shell
docker run [可选参数] image

#可选参数
--name=“Name”	容器名称，用来区别容器
-d				后台方式运行
-it				使用交互式方式运行，进入容器查看内容
-p				指定容器端口 -p 8080:8010
		-p 	ip:主机端口：容器端口
		-p  主机端口：容器端口 （常用）
		-p	容器端口
-P（大写）		  随机指定端口

#测试，启动并进入容器
[root@localhost docker]# docker run -it centos /bin/bash	#以交互方式启动镜像
[root@a13f47a3bc37 /]# 
[root@a13f47a3bc37 /] exit			#退出容易

```

##### 列出所有的运行的容器

```shell
#docker ps 
		#列出当前正在运行的容器
	-a 	#列出当前正在运行的容器+历史运行过的容器
	-n=? #列出最近创建的容器
	-q	#只显示容器的id
	
[root@localhost docker]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost docker]# 
[root@localhost docker]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                     PORTS     NAMES
a13f47a3bc37   centos         "/bin/bash"   8 minutes ago   Exited (0) 3 minutes ago             trusting_kalam
c8a86b612a4b   bf756fb1ae65   "/hello"      2 hours ago     Exited (0) 2 hours ago               crazy_driscoll
3a38678a679b   bf756fb1ae65   "/hello"      2 hours ago     Exited (0) 2 hours ago               goofy_wu
[root@localhost docker]# 
[root@localhost docker]# docker ps -a -n=1
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
a13f47a3bc37   centos    "/bin/bash"   9 minutes ago   Exited (0) 4 minutes ago             trusting_kalam
[root@localhost docker]# 
[root@localhost docker]# docker ps -q
[root@localhost docker]# docker ps -a -q
a13f47a3bc37
c8a86b612a4b
3a38678a679b
```

##### 退出容器

```shell
exit #直接退出并停止容器
ctrl +p +q #不停止容器退出
```

##### 删除容器

```shell
docker rm 容器id 	#删除指定容器,不能删除正在运行的容器，-f 参数强制删除
docker rm -f $(docker ps -aq) 	#删除所有容器

```

##### 启动和停止容器

```shell
docker start 容器id
docker restart 	容器id
docker stop	容器id
docker kill 容器id
```



#### 常用其他命令

------

##### 后台启动

```shell
#命令 docker run -d 镜像名
docker run -d centos
##常见疑问，dockers容器使用后台运行，默认要有一个前台进程，如果docker没有发现应用就会自动停止
```

##### 日志查看命令

```shell
docker logs			容器id  #查看日志
docker logs --tf 容器id	  #实时显示全部日志
docker logs --tail number 容器id #显示日志条目
```

##### 查看容器中进程

```shell
# 命令 docker top 容器id
[root@localhost docker]# docker top 77c3f5983aaf
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                14113               14088               0                   11:08               ?                   00:00:02            /bin/bash -c while true ;do echo 2020-12-27 11:08:08;sleep 2;done
root                27470               14113               0                   13:46               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 2
```

##### 查看镜像的元数据

```shell
# 命令	docker inspect 容器id
[root@localhost docker]# docker inspect 77c3f5983aaf
[
    {
        "Id": "77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42",
        "Created": "2020-12-27T03:08:08.823192627Z",
        "Path": "/bin/bash",
        "Args": [
            "-c",
            "while true ;do echo 2020-12-27 11:08:08;sleep 2;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 14113,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-12-27T03:08:09.24942467Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42/hostname",
        "HostsPath": "/var/lib/docker/containers/77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42/hosts",
        "LogPath": "/var/lib/docker/containers/77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42/77c3f5983aaf6dfff5b4991649b932d0a8291409674f015bad3fdee7b048ad42-json.log",
        "Name": "/fervent_leavitt",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/f1887b9e39bd6c4501257baf5b61ec10acf7221aab7b9e21990584cf0d7629de-init/diff:/var/lib/docker/overlay2/d6f3ca61e8a8899a6f8b5ca93e3adac802cdfdc09e3784cd37abbf105dd05064/diff",
                "MergedDir": "/var/lib/docker/overlay2/f1887b9e39bd6c4501257baf5b61ec10acf7221aab7b9e21990584cf0d7629de/merged",
                "UpperDir": "/var/lib/docker/overlay2/f1887b9e39bd6c4501257baf5b61ec10acf7221aab7b9e21990584cf0d7629de/diff",
                "WorkDir": "/var/lib/docker/overlay2/f1887b9e39bd6c4501257baf5b61ec10acf7221aab7b9e21990584cf0d7629de/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "77c3f5983aaf",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash",
                "-c",
                "while true ;do echo 2020-12-27 11:08:08;sleep 2;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "b7f9f8a6ee11fa8da954be143341c2a59c8bb4c8ff31ba99253d90f5060814b1",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/b7f9f8a6ee11",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "5c49c8f4839c486cd2fc617912fd1fd2763cc9b129720b80ab31e901740391fd",
            "Gateway": "172.17.42.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "635738bc10de12f34b4297e5448dc09a99cb18a8cbe53ba754c888e941bddcdc",
                    "EndpointID": "5c49c8f4839c486cd2fc617912fd1fd2763cc9b129720b80ab31e901740391fd",
                    "Gateway": "172.17.42.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

##### 进入当前正在运行的容器

> 容器通常以后台方式运行，需要进入容器，修改一些配置

```shell
#命令
#方式一
docker exec -it 容器id /bin/bash	#以bash命令行进入容器，新开一个终端
#方式二
docker attach 容器id				#进入当前正在执行的终端
```

##### 从容器中拷贝文件到主机上

> 拷贝是一个手动过程

```shell
docker cp 容器id：容器内路径	目的的主机路径
```



#### 测试

安装nginx

```shell
# 1、查找安装nginx镜像
# 2、启动nginx容器
docker pull nginx	#拉取nginx镜像

docker run -d --name nginx01 -p 3340:80 nginx	#以后台方式启动nginx，并将容器内3340端口映射到宿主机的80端口

curl 127.0.0.1：80	#宿主机中curl测试

docker exec -it nginx01 /bin/bash	#进入容器

```

安装tomcat

安装es+ kibana

```shell
#下载es7.6.2镜像并启动
docker run elasticsearch  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
#使用docker stats 查看doeker 状态
#测试es状态	curl localhost：9200

#修改启动配置，对程序进行内存限制
docker run elasticsearch  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
```

安装kibana



#### 可视化

> ​	docker 图形化界面管理工具

- portainer、

  ```shell
  docker run -d -p 80088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

  

- rancher

#### docker 镜像原理

> UnionFS (联合文件系统)

联合文件系统

>  镜像加载原理

#### 分层原理

所有镜像都有一个基础层

> 特点

Docker 镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。

这一层就是我们通常所说的的容器层，容器之下的都叫镜像层。



#### 提交一个自己的镜像

------

commit镜像

```shell
docker commit 提交容器成为一个新的副本

docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```





## Docker-compose

docker-compose是docker官方的开源项目，需要安装

定义、运行多个容器、YAML file 配置文件

步骤：

1. 需要Dockerfile
2. 需要docker-compose.yml配置文件
3. 使用docker-compose up 启动项目



Docker-compose.yml

```yaml
version: "3.7"

services:
  app:
    image: node:12-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:5.7
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment: 
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

概念：

- 服务 **services** ：容器、应用（web、redis、mysql...）
- 项目**project**：一组关联的容器（商场、app）



安装：

- python 包管理器pip安装

  ```shell
  pip install -U docker-compose
  ```

- 二进制安装(不建议，慢)

  ```shell
  sudo curl -L "https://github.com/docker/compose/releases/download/1.29.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  
  sudo chmod +x /usr/local/bin/docker-compose
  ```

  

#### 体验

https://docs.docker.com/compose/gettingstarted/



Compose流程

1. 创建创建网络

   ![image-20210411100358187](/Users/lion/Library/Application Support/typora-user-images/image-20210411100358187.png)

2. 执行docker-compose.yml

3. 启动服务

   ![image-20210411100600564](/Users/lion/Library/Application Support/typora-user-images/image-20210411100600564.png)

docker ps 

![image-20210411101311565](/Users/lion/Library/Application Support/typora-user-images/image-20210411101311565.png)

compose 创建容器，默认的容器名规则：文件名_服务名__num

多个服务器集群，_num 副本数量

集群状态，服务都不是单一实例的



#### 小结

1. docker镜像 	run —>容器
2. dockerfile 构建镜像（服务打包）
3. docker-compose启动项目（编排、多个微服务、环境）
4. docker网络



#### Docker-compose.yml 编写规则

---

三层结构

```yaml
version: ''	#
service:		#
	服务1: web
```







## Docker-Swarm

- 创建swarm集群

  ```shell
  #在某一台机器上初始化swarm集群，ip为管理节点的物理网卡ip
  docker swarm init --advertise-addr 182，168.1.34
  ```

  ![image-20210411145735212](/Users/lion/Library/Application Support/typora-user-images/image-20210411145735212.png)

  - **docker info** 可以查看swarm状态
  - **docker node ls** 可以查看节点相关信息

  ![image-20210411150412575](/Users/lion/Library/Application Support/typora-user-images/image-20210411150412575.png)

- 获取令牌

  ```shell
  #在集群管理节点运行
  docker swarm join-token manager		#获取管理节点token
  docker swarm join-token worker		#获取worker节点token
  ```

  

- 将节点作为worker添加到swarm集群中

  ```shell
  docker swarm join --token SWMTKN-1-5ibh6k3hxxo4bd82vq7y13jwnj9qxuxuzvi5udtjk5v0azhl4k-bcglobemaubsykin8zk8cfwmn 10.1.1.60:2377
  ```

- 将节点作为manager添加到swarm集群中

  ```shell
  ##生成manager节点token
  [manjaro my_wordpress] docker swarm join-token manager
  To add a manager to this swarm, run the following command:
  
      docker swarm join --token SWMTKN-1-5ibh6k3hxxo4bd82vq7y13jwnj9qxuxuzvi5udtjk5v0azhl4k-89m6llcyelyxh8pgm3xaiz47s 10.1.1.60:2377
      
   ##节点机   上运行
   [manjaro my_wordpress] docker swarm join --token SWMTKN-1-5ibh6k3hxxo4bd82vq7y13jwnj9qxuxuzvi5udtjk5v0azhl4k-89m6llcyelyxh8pgm3xaiz47s 10.1.1.60:2377
  ```

- 使用 **docker node ls**查看集群节点

  ![image-20210411151606661](/Users/lion/Library/Application Support/typora-user-images/image-20210411151606661.png)



#### Raft协议

---

Raft一致性协议：保证大多数节点存货才可以用。

数量大于1，集群至少大于三台





#### 集群部署服务

---

弹性、扩缩容、集群

`docker run`	单体容器

`docker-compose up` 	启动一个项目，单机

集群swarm：  `docker service`

集群概念下，启动的容器作为服务，每个服务可以有多个副本（多个节点）



#### docker service

```shell
#创建服务
docker service create -p 8888:80 --name my-nginx nginx
```

![image-20210411155252478](/Users/lion/Library/Application Support/typora-user-images/image-20210411155252478.png)

- 查看服务

  ```shell
  docker service ps my-nginx
  docker service ls
  ```

  ![image-20210411155401117](/Users/lion/Library/Application Support/typora-user-images/image-20210411155401117.png)

- **docker service inspect**   查看服务源信息

- 动态扩缩容 docker service update --relipcas 3 my-nginx

  > 服务，急群中的任意节点都可以访问，服务可以有多个副本动态扩容实现高可用

  ```shell
  #将服务my-nginx扩展为3个
  docker service update --relipcas 3 my-nginx
  
  #将服务my-nginx缩减为1个
  docker service update --relipcas 1 my-nginx
  ```

  ![image-20210411161700307](/Users/lion/Library/Application Support/typora-user-images/image-20210411161700307.png)

- 动态扩缩容，**docker service scale**  效果与上类似

  ```shell
  #将my-nginx服务扩展为5个
  docker service scale my-nginx=5
  
  #将my-nginx服务缩小为2个
  docker service scale my-nginx=2
  ```

  ![image-20210411162552446](/Users/lion/Library/Application Support/typora-user-images/image-20210411162552446.png)



调整service以什么方式运行

```shell
#默认方式，服务只在工作节点运行
docker service crtate --mode replicated --name mytom tomcat:7 

#服务在全局节点运行，所有节点都可运行
docker service create --mode golobal --name litom alpine ping baidu.com
```



#### swarm总结

---

swarm

集群的管理和编号，docker可以初始化一个swarm集群，其他节点可以介入（管理、工作者）

node

就是一个docker 节点，多个节点组成一个网络集群（管理、工作者）

service

任务、服务，可以在管理节点或者工作节点来运行。核心

task

容器内的命令，细节任务



##### swarm 网络

> docker service inspect my-nginx 查看服务元信息 
>
> 网络模式："PublishMode":"ingress"

swarm网络模式：

1. Overlay
2. ingress   特殊的overlay网络，有负载均衡的功能

![image-20210411165039826](/Users/lion/Library/Application Support/typora-user-images/image-20210411165039826.png)





### docker stack

**docker-compost up -d**  单机

**docker-stack up -d xxx.yml** 集群



### Docker secret

安全、配置密码，证书



### docker config

配置