# Docker 安装及基本命令
```
# 安装依赖
yum update
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2

#国内安装需要添加国内源,服务器在国外不用

yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

# 更新 yum 软件源缓存，并安装 docker(有些系统需要docker-ce,或者docker-io）
yum makecache fast
# 安装 Docker
yum -y install docker
# 启动 Docker
systemctl start docker
# 设置 Docker开机自启动
systemctl enable docker
## 检查docker是否安装成功
docker run hello-world
查看docker版本信息
docker -v
#-----------------------------其它 Docker常用命令-----------------------------
#镜像查看
docker images
#镜像删除，***为镜像id前3位或者4位
docker image rm ***
#清空容器（除了正在运行的）
docker system prune
#删除正在运行容器,****为容器名
docker kill ****
# 查看 Docker 状态，报错时候使用
systemctl status docker
# 停止 Docker
systemctl stop docker
# 重启 Docker 
systemctl restart docker 
# 查看正在运行的容器
docker ps 
# 查看所有容器，包括停止的容器
docker ps -a
# 查看容器占用资源情况
docker stats
# 查看容器的日志（可在这查看报错信息）
docker logs 容器名或ID
# 启动已被停止的容器
docker start 容器名或ID
# 停止运行中的容器
docker stop 容器名或ID
# 重启容器
docker restart 容器名或ID
```
# 安装 docker-compose

官方是通过docker-compose管理seafile、seafile-elasticsearch、seafile-memcached和seafile-mysql四个容器。

    Docker-Compose 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。

通过 pip 安装 docker-compose ，也可参考 CentOS 7.x 安装 Docker-Compose 30。
```
# 添加企业版附加包
yum -y install epel-release
# 安装pip
yum -y install python-pip
# 更新pip
pip install --upgrade pip
# 安装docker-compose
pip install docker-compose

# 查看docker compose的版本
docker-compose version
```
下载并修改 docker-compose.yml

    引用 Seafile 官方文档：

    下载 docker-compose.yml 100 示例文件到您的服务器上，然后根据您的实际环境修改该文件。尤其是以下几项配置：

        MySQL root 用户的密码 (MYSQL_ROOT_PASSWORD and DB_ROOT_PASSWD)
        持久化存储 MySQL 数据的 volumes 目录 (volumes)
        持久化存储 Seafile 数据的 volumes 目录 (volumes)
        持久化存储 Elasticsearch 索引数据的 volumes 目录 (volumes)

下载docker-compose.yml到本地，然后修改完成后上传到服务器/opt目录。

需要修改的配置有：

    设置 mysql 的 root 密码
    Seafile 中需要填写第一步设置的 mysql 密码
    设置 Seafile 管理员账号邮箱
    设置 Seafile 管理员账号密码
    开启 https 443 端口
    设置 https 域名
``
version: '2.0'
services:
  db:
    image: mariadb:10.1
    container_name: seafile-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=mysql-root-password  # 1. 设置MySQL的root密码
      - MYSQL_LOG_CONSOLE=true
    volumes:
      - /opt/seafile-mysql/db:/var/lib/mysql  # 指定MySQL数据持久存储的路径，默认即可不用修改。
    networks:
      - seafile-net

  memcached:
    image: memcached:1.5.6
    container_name: seafile-memcached
    entrypoint: memcached -m 256
    networks:
      - seafile-net
  ```
  
  
