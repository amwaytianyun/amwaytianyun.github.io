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
