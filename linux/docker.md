## Docker学习
> Docker — 从入门到实践 https://yeasy.gitbook.io/docker_practice/

### introduce
Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核的 cgroup，namespace，以及 OverlayFS 类的 Union FS 等技术，对进程进行封装隔离，属于 操作系统层面的虚拟化技术。

Docker 在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得 Docker 技术比虚拟机技术更为轻便、快捷。容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

一致的运行环境: 开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。

更轻松的维护和扩展: Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。

### BASIC
#### 镜像
Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:18.04 就包含了完整的一套 Ubuntu 18.04 最小系统的 root 文件系统。Docker 镜像 是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。

镜像并非是像一个 ISO 那样的打包文件，镜像只是一个虚拟的概念，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。

可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。
 
#### 容器
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样。镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

容器进程运行于属于自己的独立的 命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。

每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 容器存储层。容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

#### 仓库
镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

### 使用镜像
从 Docker 镜像仓库获取镜像的命令是 docker pull。`docker pull ubuntu:18.04`
 
`docker run` 就是运行容器的命令

`docker run -it --rm ubuntu:18.04 bash` -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。--rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。bash：放在镜像名后的是 命令，这里我们希望有个交互式 Shell，因此用的是 bash。

#### 列出镜像
`docker image ls` 镜像 ID 则是镜像的唯一标识

docker image ls 列表中的镜像体积总和并非是所有镜像实际硬盘消耗。由于 Docker 镜像是多层存储结构，并且可以继承、复用，因此不同镜像可能会因为使用相同的基础镜像，从而拥有共同的层。

通过 docker system df 命令来便捷的查看镜像、容器、数据卷所占用的空间。

显示包括中间层镜像在内的所有镜像的话,`docker image ls -a`. 无标签的镜像很多都是中间层镜像，是其它镜像所依赖的镜像。这些无标签镜像不应该删除，否则会导致上层镜像因为依赖丢失而出错。

根据仓库名列出镜像: `docker image ls ubuntu`. docker image ls 还支持强大的过滤器参数 --filter，或者简写 -f。

#### 删除镜像
`docker image rm [选项] <镜像1> [<镜像2> ...]` <镜像> 可以是 镜像短 ID、镜像长 ID、镜像名 或者 镜像摘要

docker commit 命令除了学习之外，还有一些特殊的应用场合，比如被入侵后保存现场等。但是，不要使用 docker commit 定制镜像，定制镜像应该使用 Dockerfile 来完成。


#### 镜像构成
镜像是多层存储，每一层是在前一层的基础上进行的修改；而容器同样也是多层存储，是在以镜像为基础层，在其基础上加一层作为容器运行时的存储层。

可以使用 docker exec 命令进入容器，修改其内容。我们修改了容器的文件，也就是改动了容器的存储层。我们可以通过 docker diff 命令看到具体的改动。

我们定制好了变化，我们希望能将其保存下来形成镜像。而 Docker 提供了一个 docker commit 命令，可以将容器的存储层保存下来成为镜像。换句话说，就是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。慎用 docker commit: 除了真正想要修改的 /usr/share/nginx/html/index.html 文件外，由于命令的执行，还有很多文件被改动或添加了。这还仅仅是最简单的操作，如果是安装软件包、编译构建，那会有大量的无关内容被添加进来，将会导致镜像极为臃肿。生成的镜像也被称为 黑箱镜像，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。

`docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]`
```
$ docker commit \
    --author "Tao Wang <twang2218@gmail.com>" \
    --message "修改了默认网页" \
    webserver \
    nginx:v2
sha256:07e33465974800ce65751acc279adc6ed2dc5ed4e0838f8b86f0c87aa1795214
```

查看Docker镜像的继承关系 
`docker history --no-trunc <image-name-or-id>`

`docker image inspect <image-name-or-id>`

### 操作容器
#### 启动容器
容器是独立运行的一个或一组应用，以及它们的运行态环境。当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：
- 检查本地是否存在指定的镜像，不存在就从 registry 下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
- 从地址池配置一个 ip 地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器被终止

新建并启动: docker run 
`$ docker run ubuntu:18.04 /bin/echo 'Hello world'
Hello world # 输出一个 “Hello World”，之后终止容器。`

`$ docker run -t -i ubuntu:18.04 /bin/bash
root@af8bae53bdd3:/# # 启动一个 bash 终端，允许用户进行交互。`
其中，-t 选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， -i 则让容器的标准输入保持打开。

#### 守护态运行
让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。此时，可以通过添加 -d 参数来实现。

`docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"`
此时容器会在后台运行并不会把输出的结果 (STDOUT) 打印到宿主机上面(输出结果可以用 docker logs 查看)。

通过 `docker container ls` 命令来查看容器信息。获取容器的输出信息，可以通过 `docker container logs` 命令。or `docker ps`

#### 停止容器
使用 `docker container stop` 来终止一个运行中的容器。当 Docker 容器中指定的应用终结时，容器也自动终止。

终止状态的容器可以用 `docker container ls -a` 命令看到。 终止状态的容器，可以通过 `docker container start` 命令来重新启动。

#### 进入容器
在使用 -d 参数时，容器启动后会进入后台。使用 docker attach 命令或 docker exec 命令 进入容器进行操作。

`docker exec` 当 `-i -t` 参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。从这个 stdin 中 exit，不会导致容器的停止。

#### 导出和导入
如果要导出本地某个容器，可以使用 docker export 命令。
`docker export 7691a814370e > ubuntu.tar`

使用 docker import 从容器快照文件中再导入为镜像，也可以通过指定 URL 或者某个目录来导入，例如
`cat ubuntu.tar | docker import - test/ubuntu:v1.0`
`$ docker import http://example.com/exampleimage.tgz example/imagerepo`

注：用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以使用 docker import 来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。

使用 docker container rm 来删除一个处于终止状态的容器。

`$ docker container rm trusting_newton`

### 使用网络
#### 外部访问容器
容器中可以运行一些网络应用，要让外部也可以访问这些应用，可以通过 -P 或 -p 参数来指定端口映射。

当使用 -P 标记时，Docker 会随机映射一个端口到内部容器开放的网络端口。本地主机的 32768 被映射到了容器的 80 端口。此时访问本机的 32768 端口即可访问容器内 NGINX 默认页面。
```
docker run -d -P nginx:alpine
$ docker container ls -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
fae320d08268        nginx:alpine        "/docker-entrypoint.…"   24 seconds ago      Up 20 seconds       0.0.0.0:32768->80/tcp   bold_mcnulty
```

可以通过 docker logs 命令来查看访问记录

-p 则可以指定要映射的端口，并且，在一个指定端口上只可以绑定一个容器。支持的格式有
`ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort`

#### 容器互联
随着 Docker 网络的完善，强烈建议大家将容器加入自定义的 Docker 网络来连接多个容器，而不是使用 --link 参数。

创建一个新的 Docker 网络。-d 参数指定 Docker 网络类型，有 bridge overlay。
`$ docker network create -d bridge my-net`

运行容器并连接到新建的 my-net 网络
`$ docker run -itd --name busybox1 --network my-net busybox`
`$ docker run -itd --name busybox2 --network my-net busybox`

通过 ping 来证明 busybox1 容器和 busybox2 
`# ping busybox2`

容器建立了互联关系。用 ping 来测试连接 busybox2 容器，它会解析成 172.19.0.3。

如果你有多个容器之间需要互相连接，推荐使用 `Docker Compose`。

### Swarm mode
运行 Docker 的主机可以主动初始化一个 Swarm 集群或者加入一个已存在的 Swarm 集群，这样这个运行 Docker 的主机就成为一个 Swarm 集群的节点 (node) 。

节点分为管理 (manager) 节点和工作 (worker) 节点。工作节点是任务执行节点，管理节点将服务 (service) 下发至工作节点执行。管理节点默认也作为工作节点。你也可以通过配置让服务只运行在管理节点。

任务 （Task）是 Swarm 中的最小的调度单位，目前来说就是一个单一的容器。服务 （Services） 是指一组任务的集合，服务定义了任务的属性。服务有两种模式：1.replicated services 按照一定规则在各个工作节点上运行指定个数的任务。2.global services 每个工作节点上运行一个任务.

#### 查看集群

在管理节点使用 docker node ls 查看集群。

#### 部署服务
使用 docker service 命令来管理 Swarm 集群中的服务，该命令只能在管理节点运行。

现在我们在上一节创建的 Swarm 集群中运行一个名为 nginx 服务。
`$ docker service create --replicas 3 -p 80:80 --name nginx nginx:1.13.7-alpine`

使用 docker service ls 来查看当前 Swarm 集群运行的服务。

使用 docker service ps 来查看某个服务的详情。

使用 docker service logs 来查看某个服务的日志。

使用 docker service scale 对一个服务运行的容器数量进行伸缩。当业务处于高峰期时，我们需要扩展服务运行的容器数量`$ docker service scale nginx=5` 

使用 docker service rm 来从 Swarm 集群移除某个服务。

部署
`docker stack deploy --compose-file=docker-compose.yml nx-cluster`

#### 使用 compose 文件
使用 docker service create 一次只能部署一个服务，使用 docker-compose.yml 我们可以一次启动多个关联的服务。

在 Swarm 集群中使用 docker-compose.yml 我们用 docker stack 命令

查看服务`docker stack ls` 

移除服务，使用 docker stack down

移除数据卷请使用 docker volume rm

#### docker 用户取消sudo
创建名为docker的组，如果之前已经有该组就会报错，可以忽略这个错误：
sudo groupadd docker
1
将当前用户加入组docker：
sudo gpasswd -a ${USER} docker
1
重启docker服务(生产环境请慎用)：
sudo systemctl restart docker
1
添加访问和执行权限：
sudo chmod a+rw /var/run/docker.sock


#### docker top --：查看指定容器中所有正在运行的进程
查看所有运行容器的进程信息
for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done


### 详解Docker挂载本地目录及实现文件共享

譬如我要启动一个centos容器，宿主机的/test目录挂载到容器的/soft目录，可通过以下方式指定：
`docker run -it -v /test:/soft centos /bin/bash`

一、容器目录不可以为相对路径

二、宿主机目录如果不存在，则会自动生成

