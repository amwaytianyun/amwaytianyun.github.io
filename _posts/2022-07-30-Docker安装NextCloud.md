# 安装Docker
## 安装依赖
```
yum update
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
## 更新 yum 软件源缓存，并安装 docker-io(有些系统需要docker-ce,或者docker-io）
```
yum makecache fast
yum install docker
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

## 安装docker-compose（可选安装）
```
wget https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 验证docker-compose命令
docker-compose -v
```
# 配置mysql
```
docker pull mysql:5.7
docker run -d --name mysql \
    -v d_mysql:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=nextcloud \
    -e MYSQL_USER=nextcloud \
    -e MYSQL_PASSWORD=password \
    -p 3306:3306 \
    mysql:5.7
  
docker ps
```
执行完毕看到屏幕最后一项应该显示的是mysql，接着执行如下命令
```
docker exec -it mysql bash
mysql -unextcloud -p
Enter password: 
mysql> show databases;
```
看到如下结果就是成功了
```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| nextcloud          |
+--------------------+
2 rows in set (0.00 sec)
```
# 安装NextCloud
```
docker pull nextcloud
docker run -d --name nextcloud \
    -v nextcloud:/var/www/html \
    --link mysql:mysql \
    -p 8080:80 \
    nextcloud
 ```
 打开浏览器，输入ip:8080即可安装，经过测试Docker安装后的nextcloud性能下降很多，这里只是给出方法进行测试，不建议这样安装。
 
# docker清空
如果安装有问题，可以随时清空docker，重新来过，很方便测试。
```
docker system prune
```

