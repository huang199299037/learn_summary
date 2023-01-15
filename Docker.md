# Docker

## docker架构

采用CS模式，使用远程api来管理和创建docker容器，docker容器通过docker镜像创建，容器于镜像的关系类似于面向对象编程中的对象与类

### docker基本概念

- 镜像（image）
- 容器（container）
- 仓库（Repository）

### 安装docker

参考资料：https://docs.docker.com/engine/install/ubuntu/

https://yeasy.gitbook.io/docker_practice/install/ubuntu

```
sudo groupadd docker #添加docker用户组
sudo gpasswd -a $USER docker  #将登陆用户加入到docker用户组中
newgrp docker #更新用户组
docker images    #测试docker命令是否可以使用sudo正常使用
```

### docker镜像加速器

参考资料：https://cloud.tencent.com/document/product/1207/45596

## docker基础命令

参考资料：https://blog.csdn.net/leilei1366615/article/details/106267225

**启动docker**

```bash
systemctl start docker
```

**关闭docker**

```bash
systemctl stop docker
```

**重启docker**

```bash
systemctl restart docker
```

**docker设置随服务启动而自启动**

```bash
systemctl enable docker
```

**查看docker 运行状态**

如果是在运行中 输入命令后 会看到绿色的active

```bash
systemctl status docker
```

**查看docker 版本号信息**

```bash
docker version
docker info
```

**docker 帮助命令**

```bash
docker --help
```

比如拉取命令 不知道可以带哪些参数 可以这样使用

```bash
docker pull --help
```

## docker镜像

**列出镜像**

```bash
列出已经下载的镜像
docker images
docker image ls
```

**搜索镜像**

```bash
docker search 镜像名
```

**拉取镜像**

不加tag(版本号) 即拉取docker仓库中 该镜像的最新版本latest 加:tag 则是拉取指定版本

```
docker pull 镜像名 
docker pull 镜像名:tag
```

**运行镜像**

```bash
docker run 镜像名
docker run 镜像名:Tag
```

**删除镜像**

```bash
#删除一个
docker rmi -f 镜像名/镜像ID

#删除多个 其镜像ID或镜像用用空格隔开即可 
docker rmi -f 镜像名/镜像ID 镜像名/镜像ID 镜像名/镜像ID

#删除全部镜像  -a 意思为显示全部, -q 意思为只显示ID
docker rmi -f $(docker images -aq)
```

> 要删除的镜像必须确认此镜像目前没有被任何容器使用

**强制删除镜像**

```bash
docker image rm 镜像名称/镜像ID
```

**其他辅助命令**

查看本地镜像id

```bash
docker images -q
```

查看一个镜像制作历程

```bash
docker history 镜像名称
```

