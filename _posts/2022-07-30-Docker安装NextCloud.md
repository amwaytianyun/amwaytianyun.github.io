# 安装Docker
## 安装依赖
```
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2
```
## 国内安装需要添加国内源,服务器在国外不用
```
yum-config-manager \
    --add-repo \
    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```
## 更新 yum 软件源缓存，并安装 docker-io(有些系统需要docker-ce）
```
yum makecache fast
yum install docker-io
```
## 启动docker，且设置开机自启
```
systemctl enable docker
systemctl start docker
```
## 检查docker是否安装成功
```
docker run hello-world
```
查看docker版本信息
```
docker -v
```

## 安装docker-compose
```
wget https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 验证docker-compose命令
docker-compose -v
```
# 
