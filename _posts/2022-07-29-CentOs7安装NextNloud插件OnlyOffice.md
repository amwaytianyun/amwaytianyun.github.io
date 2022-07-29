书接上回  [CentOs7安装NextNloud](https://amwaytianyun.github.io/2022/07/29/CentOs7%E5%AE%89%E8%A3%85nextcloud.html)
,想要实现在线查看和编辑office文档不是容易的事情，电脑上需要安装软件，网页上就需要相应的服务器，在这里我们用docker实现。
# 安装依赖
```
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2
```
国内安装需要添加国内源,服务器在国外不用
```
yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```
更新 yum 软件源缓存，并安装 docker-ce
```
yum makecache fast
yum install docker-io
```
