---
title: Docker
date: 2022-03-18 23:16:59
categories:
- tools
tags:
---

本文主要记录 Docker 常用命令。

<!-- more -->

## images

### pull

``` shell
docker pull ubuntu:latest
```

### remove

一定要删除正在运行的容器

``` shell
docker rmi plctlab
```

### build images by Dockerfile

``` shell
sudo docker build -t plctlab .
```

``` Dockerfile
FROM ubuntu:latest

# 根据宿主机的 ip 来决定
ENV PROXY_IP=192.168.5.38
ENV PROXY_PORT=7890
ENV http_proxy=http://$PROXY_IP:$PROXY_PORT
ENV https_proxy=http://$PROXY_IP:$PROXY_PORT
ENV all_proxy=socks5://$PROXY_IP:$PROXY_PORT

RUN apt update && apt install -y build-essential gcc make perl dkms git gcc-riscv64-unknown-elf gdb-multiarch qemu-system-misc

WORKDIR /root

RUN git clone https://github.com/plctlab/riscv-operating-system-mooc.git

# 在容器启动时执行的命令
CMD ["bash"]
```

## container

### 新建

``` shell
docker run -it --name my-ubuntu ubuntu:latest /bin/bash
docker run -it --name my-ubuntu ubuntu:20.04 /bin/bash
```

exit 后容器会退出运行

- -d 后台运行
- --rm 退出后自动删除容器

### 删除

``` shell
docker rm my-ubuntu
```

### 查看

``` shell
# 查看运行的容器
docker ps
# 查看所有包括退出的容器
docker ps -a
```

### 继续运行

``` shell
docker start my-ubuntu
docker exec -it my-ubuntu /bin/bash
```

### attach 容器

``` shell
sudo docker start my-ubuntu
sudo docker attach my-ubuntu
```

equals

``` shell
sudo docker start -i my-ubuntu
```

---

formal

1. C-S 架构，C 端为 Docker 或者 Docker Compose，S 端为 Docker daemon

2. To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

3. When running a container, it uses an isolated filesystem. This custom filesystem is provided by a container image. Since the image contains the container’s filesystem, it must contain everything needed to run an application - all dependencies, configuration, scripts, binaries, etc. The image also contains other configuration for the container, such as environment variables, a default command to run, and other metadata.

4. containers: 可以定义network, storage

## 创建 image

sudo docker build -t                        // tag 取 image 的名字
                  .                         // 选择 Dockerfile 目录

## 运行 container

sudo docker run -i                          // interactively
                -t                          // attach to your terminal
                ubuntu                      // ubuntu 是 image 名字
                /bin/bash                   // command

sudo docker run  -d                         // detached 模式分离
                 -p 3000:3000               // Without the port mapping, we wouldn’t be able to access the application. port mapping 左边是本机的 3000 端口映射到容器的 3000 端口上

## 修改容器

sudo docker ps -a

sudo docker stop container-id

sudo dokcer rm container-id

sudo docker rm -f container-id              // by force

## push

docker login -u USER-NAME
docker tag local-image:tagname              // 为本地当前已有的 image 起别名, 也可以使用 repo 的名字, tagname 不指定即是 latest 的 tag，本身使用的是相同的 image id，但是是不同的 REPOSITORY
           new-repo:tagname
docker push new-repo:tagname

### example

远程库名字为 love1angel/getting-started
docker tag hello love1angel/getting-started:1.1
docker push love1angel/getting-started:1.1

下面命令如果本地没有 latest 标签，就会报错
docker push love1angel/getting-started

# filesystem

When a container runs, it uses the various layers from an image for its filesystem. Each container also gets its own “scratch space 暂存空间” to create/update/remove files. Any changes won’t be seen in another container, even if they are using the same image.

docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
docker exec container-id cat /data.txt
docker run -it ubuntu ls /


Volumes provide the ability to connect specific filesystem paths of the container back to the host machine. If a directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that same directory across container restarts, we’d see the same files.

1. named volumes

$ docker volume create todo-db

在/etc/todos/todo.db底下，mount it to，-v flag to specify a volume mount.

$ docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started

While named volumes and bind mounts (which we’ll talk about in a minute) are the two main types of volumes supported by a default Docker engine installation, there are many volume driver plugins available to support NFS, SFTP, NetApp, and more! This will be especially important once you start running containers on multiple hosts in a clustered environment with Swarm, Kubernetes, etc.

docker volume inspect todo-db

## bind-mount

When working on an application, we can use a bind mount to mount our source code into the container to let it see code changes, respond, and let us see the changes right away.

Mount our source code into the container
Install all dependencies, including the “dev” dependencies
Start nodemon to watch for filesystem changes

docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"

-dp 3000:3000 - same as before. Run in detached (background) mode and create a port mapping
-w /app - sets the “working directory” or the current directory that the command will run from
-v "$(pwd):/app" - bind mount the current directory from the host in the container into the /app directory
node:12-alpine - the image to use. Note that this is the base image for our app from the Dockerfile
sh -c "yarn install && yarn run dev" - the command. We’re starting a shell using sh (alpine doesn’t have bash) and running yarn install to install all dependencies and then running yarn run dev. If we look in the package.json, we’ll see that the dev script is starting nodemon.

docker logs -f <container-id>

docker build -t getting-started .

去运行容器，此时已变成更改后的

Using bind mounts is very common for local development setups. The advantage is that the dev machine doesn’t need to have all of the build tools and environments installed. With a single docker run command, the dev environment is pulled and ready to go.

# 7

the container only starts one process

If two containers are on the same network, they can talk to each other. If they aren’t, they can’t.

There are two ways to put a container on a network: 1) Assign it at start or 2) connect an existing container.

docker network create todo-app

docker run -d \
     --network todo-app --network-alias mysql \
     -v todo-mysql-data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=secret \
     -e MYSQL_DATABASE=todos \
     mysql:5.7

https://hub.docker.com/_/mysql/

# Dockerfile

1. CMD ["node", "src/index.js"] The CMD directive specifies the default command to run when starting a container from this image.
