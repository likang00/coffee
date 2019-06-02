---
title: Docker笔记2
date: 2019-02-21 12:00:00
categories: Docker
tags: ["Docker","服务器"]
---

# docker笔记2

###### 本次实现将代码在github上托管,
###### 使用git命令配合docker-compose更新线上代码

#### 先放上 Dockerfile 和 docker-compose.yml 后面会用到
###### Dockerfile
```
FROM python:latest
ADD . /zlk
WORKDIR /zlk
RUN pip install -r zlkreq.txt
CMD ["python", "myapp.py"]
```
###### docker-compose.yml
```
version: '3'
services:
  zlkweb:
    build: .
    ports:
      - "80:5001"
    volumes:
      - .:/zlk
```
###### 启动
`docker-compose up -d`
###### 重启
`docker-compose restart`

----
## Docker-Compose

###### Compose 是一个用户定义和运行多个容器的 Docker 应用程序。在 Compose 中你可以使用 YAML 文件来配置你的应用服务。然后，只需要一个简单的命令，就可以创建并启动你配置的所有服务。

###### 使用 Compose 基本会有如下三步流程：
###### 在 Dockfile 中定义你的应用环境，使其可以在任何地方复制。
###### 在 docker-compose.yml 中定义组成应用程序的服务，以便它们可以在隔离的环境中一起运行。
###### 最后，运行dcoker-compose up，Compose 将启动并运行整个应用程序。



#### 我的准备工作

0.1购买云服务器 安装SSH远程终端工具
我使用的云服务器是 腾讯云
云主机使用的系统是 CentOS
SSH使用 Xshell6

0.2.将想要部署的项目上传到github上
本次使用的项目是 图灵程序设计丛书 Flask Web 开发中的项目
项目地址:
https://github.com/miguelgrinberg/flasky

我的github项目地址:
(暂时有个人配置 后续修改后放上来)


0.3确认你已经云服务器中安装了 Docker 与 Docker Compose
上篇已经介绍过 这次略过
```
yum install -y docker # 安装docker
systemctl start docker # 启动docker
docker search python # 查找python镜像
docker pull python # 拉取镜像
docker run -it python "/bin/bash"

python -V
pip -V
pip list
pip install xxx
exit

docker commit xxxxxxxxxxxx(上面的镜像编号) python:latest # 保存自己的python镜像
```

#### 开始
1 定义应用依赖
建文件夹cd进去
git clone xxx 拉代码
git checkout xxx 切换到指定分支

2 创建 Dockerfile
在你的项目路径下创建一个 Dockerfile 文件
在工作路径下创建一个叫做 docker-compose.yml
编辑 Compose 文件添加一个绑定挂载
这个新的 volumes 键将当前路径（项目路径）与容器中的 /code 路径挂载到一起，允许你及时修改代码而不用重新构建镜像。(关键)
(需要修改文件 因为git中拉下来是我的配置)
你也可以通过 docker inspect <tag or id> 来检查镜像。

3 运行项目​
如果你希望你的应用程序在后台运行，你可以将 -d 标记传递给 docker-compose up 并使用 
docker-compose up -d

4 更新项目并重新部署
因为应用程序的代码通过 volume 挂载到容器中，你可以更改其代码并立即查看更改，而不必重新生成镜像。
只用在你的个人电脑上连接github 修改后
在云服务器项目目录下 
**git pull**
**docker-compose restart**

ok!

## 其他补充
#### docker-compose 在dockerfile更新后自动更新image
```
比如在dockerfile里需要新安装包
RUN pip install XXX
之后,希望docker-compose能更新镜像, 然后启动容器
只需要启动时使用 --build即可:

docker-compose stop
docker-compose up -d --build
```

```
更新镜像时
这样执行 docker-compose 命令
docker-compose pull && docker-compose up --force-recreate -d
试试 docker-compose.yml 添加如下的配置
services:
  web:
    deploy:
      update_config:
        order: start-first
```