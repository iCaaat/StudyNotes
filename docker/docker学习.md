# 概念

## 1.容器(Container)

用基于linux的容器技术（windows主要是运行linux子系统来兼容docker）将 **应用程序及其依赖（库、配置文件、运行环境）** 打包在一起，保证在任何环境下都能一致运行。

容器和 **虚拟机（VM）** 相比更轻量，因为它们 **共享宿主机的操作系统内核**，而不是像虚拟机一样需要完整的操作系统。

## 2.镜像(Image)

一个只读的模板，包含运行应用所需的代码和依赖，比如 `ubuntu:20.04`、`mysql:8.0`。

## 3.镜像仓库（Docker Registry）

存放、分享镜像的地方。

官方仓库：[Docker Hub](hub.docker.com)

---

# 安装与配置

## 1.安装

略

## 2.配置

### 2.1 镜像站配置

编辑配置文件

```shell
vi /etc/docker/daemon.json
```

添加镜像配置，如：

```json
{
    “registry-mirrors": [
    	"https://docker.m.daocloud.io",
    	"https://docker.1panel.live",
    	"https://hub.rat.dev"
    ]
}
```

保存退出，之后重启docker

```shell
service docker restart
```

之后运行`docker info`应该可以看到：
```shell
 Registry Mirrors:
  https://docker.m.daocloud.io/
  https://docker.1panel.live/
  https://hub.rat.dev/
```

---

# Docker最重要的命令

## 1.docker pull

用于下载镜像

```shell
docker pull [--platform=xxxx] docker.io/library/nginx:latest
```

* `docker.io`：registry-仓库地址，docker.io为官方仓库，可省略
* `library`：namespace-命名空间（作者名），library为官方命名空间，可省略
* `nginx:latest`：tag-标签（版本号），latest表示最新版，可省略
* `docker.io/library/nginx:latest`：一整条为一个镜像库（repository）
* `--platform=xxxx`：表示拉取特定CPU架构的镜像

## 2.docker images

展示下载了的镜像

```shell
docker images
```

## 3.docker rmi

删除已下载的镜像

```shell
docker rmi (image_name/image_id)
```

## 4.docker run

**运行容器**

```shell
docker run [options] (image_name/image_id)
```

* [options]：选项
  * -d：分离模式（后台运行）
  * -p：端口映射

若Docker发现本地不存在指定的镜像，则会自动拉取

## 5.docker ps

查看正在运行的容器的状况

```shell
docker ps
```

