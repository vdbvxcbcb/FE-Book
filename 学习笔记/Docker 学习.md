# Docker

Docker 官方文档: https://docs.docker.com/

Docker Hub: https://hub.docker.com

## 概念

镜像（Image）：

镜像是一个文件，用来创建容器的。如果你有装过 Windows 操作系统，那么 Docker 镜像特别像“Win7纯净版.rar”文件

Docker 的镜像是用于生成容器的模板,

镜像与容器的关系，就是面向对象编程中类与对象的关系，我们定好每一个类，然后使用类创建对象，

对应到 Docker 的使用上，则是构建好每一个镜像，然后使用镜像创建我们需要的容器。

image 的获取

- 自己制作
- 别处拷贝
- 从 registry 拉取（比如docker hub）

容器（Container）：

容器特别像一个一个操作系统，可以在容器中装 Nodejs，可以执行`npm install`，可以做一切你当前操作系统能做的事情

通过镜像创建，用于独立运行一个或一组应用

容器与容器，容器和主机都是互相隔离的

宿主机（Host） ：

在 Docker 中，我们通常称你当前使用的真实操作系统为“宿主机”(Host)

Dockerfile ：

镜像通过一个 Dockerfile 打包来的，它非常像我们前端的`package.json`文件

```
Dockerfile: 类似于“package.json”
 |
 V
Image: 类似于“Win7纯净版.rar”
 |
 V
Container: 一个完整操作系统
```

仓库（registry）：

仓库就像是 github 那样的，我们可以制作镜像然后 push 提交到云端的仓库，也可以从仓库 pull 下载镜像

对比传统虚拟机总结

| 特性       | 容器               | 虚拟机      |
| ---------- | ------------------ | ----------- |
| 启动       | 秒级               | 分钟级      |
| 硬盘使用   | 一般为 `MB`        | 一般为 `GB` |
| 性能       | 接近原生           | 弱于        |
| 系统支持量 | 单机支持上千个容器 | 一般几十个  |

Docker 架构

docker client: 即 docker 命令行工具

docker host: 宿主机，docker daemon 的运行环境服务器

docker daemon: docker 的守护进程，docker client 通过命令行与 docker daemon 交互

image: 镜像，可以理解为一个容器的模板，通过一个镜像可以创建多个容器

container: 最小型的一个操作系统环境，可以对各种服务以及应用容器化，是镜像的运行实例

registry: 镜像仓库，存储大量镜像，可以从镜像仓库拉取和推送镜像

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681550058126-bd3dbcdb-a285-4dd6-aa77-f6108a95c595.png" alt="img" style="zoom:50%;" />

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681645311747-769ae0b4-1d02-4a90-bc5a-eed7ef9fe152.png" alt="img" style="zoom: 33%;" />



![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555696674-550ce4fc-36a4-4a4e-a6ba-3f797a4df155.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681645311208-e92601a1-7eab-4743-91d4-a69411414fde.png)

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555696630-de8de040-4a8c-40ef-b96e-8d9231842452.png" alt="img"  />

## docker 命令

```sh
docker version  
docker info    # docker 系统详细信息，比如镜像与容器的数量
docker --help
```

### 镜像

#### docker images 列出本地主机上的镜像

各个选项说明:

- REPOSITORY：表示镜像的仓库源
- TAG：镜像的标签, 同一仓库源可以有多个 TAG，代表这个仓库源的不同个版本
- IMAGE ID：镜像ID
- CREATED：镜像创建时间
- SIZE：镜像大小

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555959681-ea53935e-71a6-49dd-aa3f-d0a7105d172e.png" alt="img" style="zoom:80%;" />

#### docker search 搜索镜像

各个选项说明:

- NAME: 镜像仓库源的名称
- DESCRIPTION: 镜像的描述
- OFFICIAL: 是否 docker 官方发布
- STARS: 类似 Github 里面的 star，表示点赞、喜欢的意思。
- AUTOMATED: 自动构建

<img src="https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555959747-b247a928-6e68-47bf-8dc7-4947f983d9c0.png" alt="img" style="zoom: 80%;" />

#### docker pull 拉取镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555696860-8d5c6668-7132-437d-92ef-fd6283f562b8.png)

#### docker rmi 删除镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555696673-c17d5896-58ed-408f-af34-89d648176c0f.png)

#### docker 更新镜像

制作自己要的基础镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681562474578-8193b7e0-b915-494d-80b0-bbcd3b1846fd.png)

我们先跑一个ubuntu容器实例，同时在里面进行`apt-get update`更新

```bash
[root@pdai ~]# docker run -it ubuntu:latest
root@0a1556ca3c27:/# apt-get update
Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
Get:2 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
Get:3 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:4 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:5 http://security.ubuntu.com/ubuntu bionic-security/main amd64 Packages [817 kB]
Get:6 http://archive.ubuntu.com/ubuntu bionic/main amd64 Packages [1344 kB]
Get:7 http://archive.ubuntu.com/ubuntu bionic/multiverse amd64 Packages [186 kB]
Get:8 http://archive.ubuntu.com/ubuntu bionic/restricted amd64 Packages [13.5 kB]
Get:9 http://archive.ubuntu.com/ubuntu bionic/universe amd64 Packages [11.3 MB]
Get:10 http://security.ubuntu.com/ubuntu bionic-security/restricted amd64 Packages [27.5 kB]
Get:11 http://security.ubuntu.com/ubuntu bionic-security/multiverse amd64 Packages [7064 B]
Get:12 http://security.ubuntu.com/ubuntu bionic-security/universe amd64 Packages [818 kB]
Get:13 http://archive.ubuntu.com/ubuntu bionic-updates/multiverse amd64 Packages [11.1 kB]
Get:14 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 Packages [1345 kB]
Get:15 http://archive.ubuntu.com/ubuntu bionic-updates/restricted amd64 Packages [41.2 kB]
Get:16 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 Packages [1104 kB]
Get:17 http://archive.ubuntu.com/ubuntu bionic-backports/universe amd64 Packages [4252 B]
Get:18 http://archive.ubuntu.com/ubuntu bionic-backports/main amd64 Packages [2496 B]
Fetched 17.6 MB in 10s (1759 kB/s)
Reading package lists... Done
root@0a1556ca3c27:/# exit
exit
```

此时 ID 为 0a1556ca3c27 的容器，是按我们的需求更改的容器。我们可以通过命令 docker commit 来提交容器副本：

```bash
[root@pdai ~]# docker commit -m="update test" -a="pdai" 0a1556ca3c27  pdai/ubuntu:v1.0.1
sha256:b51e9f5f98cdb5b95c34181148ed7029f0150618aa8cd0e916b10b6aea1bd4c9
```

可以看到已经生成成功, 且镜像的大小不一样了：

```bash
[root@pdai ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
pdai/ubuntu         v1.0.1              b51e9f5f98cd        5 seconds ago       92.1MB
mysql               latest              791b6e40940c        2 weeks ago         465MB
ubuntu              latest              ccc6e87d482b        4 weeks ago         64.2MB
```



### 容器

#### docker run 通过镜像启动容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555697661-ec3ae3a2-c4d9-40fc-b81f-11be940777ca.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555697670-89e876fa-8aa4-4848-91c9-9c174f0421a1.png)

#### docker ps 查看容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681555697806-12f6a9bc-c1eb-4bd5-9339-f0c257fb92fd.png)

#### 退出容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681556532521-173c5f51-c38b-4f39-ab59-752031c1fb7a.png)

#### docker rm 删除容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681556532561-7fd60bd6-8e88-4312-8af6-09f6a1c788b6.png)

#### 启动和停止容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681556532569-f377471e-7c4b-4c70-9701-95c9b2c819cb.png)

#### 后台启动容器

-d 容器处于后台运行的状态，不会对当前终端产生任何输出

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681558958491-be01b157-129c-4edd-8087-51a9d6cfe471.png)

#### 查看容器中进程信息

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681558958497-6c63dc68-d58c-4bde-b34a-3a78cfbd41d6.png)

#### 查看日志

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681558958541-894e941e-d063-4131-bc73-57cff06cd600.png)

```bash
例：实时查看docker容器名为user-uat的最后10行日志
docker logs -f -t --tail 10 user-uat

例：查看指定时间后的日志，只显示最后100行：
docker logs -f -t --since="2018-02-08" --tail=100 user-uat

例：查看最近30分钟的日志:
docker logs --since 30m user-uat

例：查看某时间之后的日志：
docker logs -t --since="2018-02-08T13:23:37" user-uat

例：查看某时间段日志：
docker logs -t --since="2018-02-08T13:23:37" --until "2018-02-09T12:23:37" user-uat

例：将错误日志写入文件：
docker logs -f -t --since="2018-02-18" user-uat | grep error >> logs_error.txt
```

#### 进入当前运行的容器

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681558958662-e94f847f-ea05-413f-8d88-fd98aa785b9f.png)

#### 容器内拷贝文件到主机

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681558958667-8133a0f5-eacb-4187-bc63-7a1d98edab4e.png)

## Docker 安装 nginx

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681562150175-6400ec3d-c22f-4da8-851f-92a58041905f.png)![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681562150112-02e16bf1-3d3a-4559-8816-1f40b7218d38.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681562170024-9b1441a0-ec2d-4b04-a31e-02d4722e5a55.png)

## 联合文件系统

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617753408-fc904806-3537-4f05-aa1e-eeec34b197ab.png)


![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617753674-c3a2f29f-0f83-4e2d-bd39-922e1540aaf0.png)

## 分层理解

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617753657-849b35be-d3e3-4030-96ad-2e7c9f3b9afe.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617753412-3554f171-3445-42b8-8836-4d6388d0e379.png)


![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617753366-5690669a-167c-4a41-8bf2-ea2f742f058f.png)


![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617754643-cbe34bf0-2419-4dd7-98d8-b179b953f9ab.png)


![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681617755549-00f47146-bb68-4bbf-819d-cabf1a825086.png)

## 容器数据卷

数据卷相当于 **给容器挂载一个虚拟的磁盘**，该虚拟机磁盘是 **可以在宿主机中访问的**，即便容器挂掉了，数据还存在

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681618668483-59e31881-00d9-42e5-b68b-657abbca3ec6.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681618669126-e3404591-dd34-4857-a554-cfb0ca99a91c.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681618669025-ba6e6ada-8ee0-4483-a4df-f142b1aba0a9.png)

### MySQL 数据同步

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621917683-c66d672f-3df6-4952-a51d-fc21d6f4a495.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621917595-0eb70426-cc1c-4e9f-9b23-f7774f041118.png)

### 具名挂载与匿名挂载

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621917689-f0bdbd3b-c0b6-42d2-bbce-b24eece1b4ac.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621917726-53d25ac1-f395-44dd-8b69-014e675b8b21.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621917683-c5f90005-4926-40a2-b7fe-a9a14a23c9a2.png)

### 数据卷与 Dockerfile

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621919199-b0151318-67da-47e5-b93f-647cabf05c07.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621919396-6ae2eecc-b3c5-4b1c-aa4d-f39ca4455e01.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621919054-56b9fd5e-cad2-49ec-90df-8a5066d04f3f.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681621919296-2319f33e-6bbd-4367-aa1f-d35acd0d1897.png)

### 数据卷容器之间数据同步

--volumes-from 相当于继承

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632104036-18d4f5d6-711f-4234-bbf9-7744e8ef0555.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632104277-48cc66bc-0abe-4933-b397-dfc271947d11.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632104377-a8921996-f4b5-4ef1-9d36-5c4d916ea102.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632104055-107b0a4e-e4aa-42f8-abad-6f09c6781858.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632105019-2a81c47e-c85d-4a1a-ba29-a9f084db915e.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632104035-2350faf3-3395-49c3-aa67-252eb4221b7a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681632234840-8b8c58ae-6fba-46e0-bafe-26dd4e80f29c.png)

## DockerFile

### 概念

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681633996754-24b47864-5b9a-4ec5-949c-76c48c4816d8.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681633996882-06245d99-7127-4b17-b724-b401e10e4bd2.png)

### DockerFile 命令

一组命令，可以在 DockerFile 写多组：

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681633996833-d6145e50-a39f-492d-ac6c-a8cf1cba8c89.png)

Dockerfile 由一行行命令语句组成，并且支持以 # 开头的注释行。

使用 # 来注释

FROM 告诉 Docker 使用哪个镜像作为基础

MAINTAINER 镜像作者信息

LABEL 用来给镜像以键值对的形式添加一些元数据（metadata）

RUN 在镜像中执行命令，由于 `ufs` 的文件系统，它会在当前镜像的顶层新增一层。

格式为：

1. RUN
2. RUN ["executable", "param1", "param2"]

```dockerfile
RUN yum -y install httpd
```

前者将在 shell 终端中运行命令，即 /bin/sh -c ；

后者则使用 exec 执行。指定使用其它终端可以通过第二种方式实 现，例如 RUN ["/bin/bash", "-c", "echo Build_Dockerfile"] 

COPY 从 Docker 宿主机复制文件至创建的新镜像文件

ADD 把宿主机的文件或目录加入到镜像的文件系统中。类似于 COPY 指令，ADD 支持 tar 文件和 URL 路径

WORKDIR 用于为 Dockerfile 中所有的 RUN、CMD、ENTRYPOINT、COPY、ADD 等指定设定当前工作目录

VOLUME 数据卷，用于在 image 中创建挂载点目录，以挂载 Docker host 上的卷或者其他容器上的卷

EXPOSE 为容器打开指定的监听端口以实现与外部通信

ENV 用于为镜像定义所需的环境变量，可以被 Dockerfile 文件中其他命令调用(ENV、ADD、COPY、RUN、CMD)

ARG 定义一个变量，构建镜像时，可以使用`--build-arg <varname>=<value>`将该变量传递给构建器，

`ARG` 所设置的构建环境的环境变量，在将来容器运行时是不会存在这些环境变量的，

灵活的使用 `ARG` 指令，能够在不修改 Dockerfile 的情况下，构建出不同的镜像。

CMD 启动容器指定默认要运行的程序或命令，默认"/bin/sh -c"运行

ENTRYPOINT 类型 CMD 指令的功能，用于为容器指定默认运行程序或命令

USER 指定当前用户

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681645311513-10f98f74-72e0-4d84-b8cf-f2576a8a3888.png)

### Dockerfile 构建自己的 centos

docker build 生成镜像。

-f  指定要使用的 Dockerfile 文件路径

 -t 给镜像命名，添加 tag

. 是 Dockerfile 所在的路径（当前目录），也可以替换为一个具体的 Dockerfile 的路径，表示基于哪个目录的 Dockerfile 来构建镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681636680519-9e910f72-0128-4f25-a43c-a2cc38a1c64e.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681636833379-f0c034da-691b-4b6a-8839-39df57f8374a.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681636680656-352f2e0b-0c77-4c48-8ab9-ebf5e3d6dd74.png)

### Dockerfile 制作  tomcat  镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643539548-2fd62e18-4c82-4aa2-97ab-e4846761898f.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643539412-8b986cce-0485-4208-96af-171b47fc560b.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643539482-dd0c52c5-302f-4f26-9d96-67ca6f6164c3.png)

## 发布镜像

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643061720-3c770726-b610-4816-8fc8-e28ffddd8b05.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643061829-2152372f-e298-4b17-ab07-dd865c02def4.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643048338-4397adfd-76a2-4460-a059-9b53e5fc0d46.png)

## 流程小结

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643539455-2755280b-d362-4a7f-84d8-787530545137.png)

![img](https://cdn.nlark.com/yuque/0/2023/png/1614731/1681643539484-671aae25-557f-4c90-b621-8f1775d4eb77.png)

## docker compose

docker compose 使用 YAML 文件来定义多服务的应用。

YAML 是 JSON 的一个子集，因此也可以使用JSON。

docker compose 默认使用文件名 docker-compose.yml。当然，也可以使用 -f 参数指定具体文件。

使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。

最后，执行 docker-compose up 命令来启动并运行整个应用程序。

### yml 文件

版本号：进入 dockerhub ，https://docs.docker.com/compose/compose-file/compose-file-v3/

```yml
version: "3.8" # version 是必须指定的，而且总是位于文件的第一行。它定义了 Compose 文件格式(主要是 API)的版本。

services: # 容器
  servicename: # 服务名字，这个名字也是内部 bridge 网络可以使用的 DNS name
    image: # 镜像的名字
    command: # 可选，如果设置，则会覆盖默认镜像里的 CMD命令，表示设置容器启动后默认执行的命令
    environment: # 可选，相当于 docker run里的 --env，环境变量配置
    volumes: # 可选，相当于docker run里的 -v
    networks: # 可选，相当于 docker run里的 --network
    ports: # 可选，相当于 docker run里的 -p
    build: # 构建镜像的路径，利用它自动构建镜像，然后使用这个镜像启动服务容器。
      context    # 指定 dockerfile 的路径 或者 git存储库的url 
      dockerfile # 指定 dockerfile 文件
      args       # 添加构建的参数
      cache_from # 缓存解析的镜像列表
      abels      # 添加元数据，我们也可以使用数组或者是字典
      network    # 定义网络
      shm_size   # 设置构建镜像的分区大小 /dev/shm，例如 shm_size: '2gb'
      target     # 指定构建的作用，例如 prod，test，de
    depends_on: # 前置服务，决定服务启动的先后顺序，帮助 compose 理解容器之间的关系
      - servicename2
      # servicename2 将会在 servicename 之前被启动
    restart: always # 重启 docker 时，自动启动相关容器。
  servicename2:

volumes: # 可选，相当于 docker volume create

networks: # 可选，相当于 docker network create
```

### 相关命令

```docker
 # 1. 基于docker-compose.yml启动管理的容器
docker-compose up -d
# 2. 关闭并删除容器
docker-compose down
# 3. 开启|关闭|重启已经存在的由docker-compose维护的容器
docker-compose start|stop|restart
# 4. 查看由docker-compose管理的容器
docker-compose ps
# 5. 查看日志
docker-compose logs -f
# 8. 构建或者重新构建服务
docker-compose build
```













