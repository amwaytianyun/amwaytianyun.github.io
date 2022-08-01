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

 安装 docker-compose ，也可参考 CentOS 7.x 安装 Docker-Compose 30。
```
# 添加企业版附加包
yum -y install epel-release
wget https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose


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
        
        mkdir /home
        cd /home
        vi docker-compose.yml

需要修改的配置有：

    设置 mysql 的 root 密码
    Seafile 中需要填写第一步设置的 mysql 密码
    设置 Seafile 管理员账号邮箱
    设置 Seafile 管理员账号密码
    开启 https 443 端口
    设置 https 域名
    
```
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
  
     docker-compose up -d #漫长的等待
  # 配置 Seafile https ssl 证书

 服务初始化完成后，需要上传自己的 ssl 证书。

 创建 ssl 目录，然后将证书上传到该目录
 
     mkdir /opt/seafile-data/ssl

   修改 Nginx 的配置文件，配置ssl。
     
     vi /opt/seafile-data/nginx/conf/seafile.nginx.conf
           server {
	listen 443;
           server_name example.seafile.com; # 你的域名
    
           ssl on;
           ssl_certificate /shared/ssl/你的证书.pem;        #pem 文件路径
    ssl_certificate_key /shared/ssl/你的证书.key;    #pem 文件路径
    ssl_session_timeout 5m;
    ssl_session_cache shared:SSL:5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
	ssl_ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!MD5:!DSS;
    #ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
    ssl_prefer_server_ciphers on;

    proxy_set_header X-Forwarded-For $remote_addr;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
    server_tokens off;
    # 注释掉80端口自动转发到443端口，添加一句'error_page 497 301 https://$http_host$request_uri;'，这样就可以实现http自动跳转到https了。
    error_page 497 301 =307 https://v.itmm.wang:6443$request_uri;
    
    ... 后面的内容保持不变
    location / {        
       ... 
    }
    ...
           }
6. 防火墙放行端口
```
yum install iptables-services
 yum install firewalld
 systemctl start firewalld
 systemctl enable firewalld
# 开放端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT  
iptables -A INPUT -p tcp --dport 443 -j ACCEPT  
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
yum install git
git clone https://github.com/lyy289065406/seafile-docker /usr/local/seafile-docker
cd /usr/local/seafile-docker
./run.sh -u admin -p admin123

# 顺便把 OnlyOffice 用到的两个端口也开放，后面就不用再开了
firewall-cmd --zone=public --add-port=8088/tcp --permanent
firewall-cmd --zone=public --add-port=8443/tcp --permanent
# 重载防火墙
firewall-cmd --reload
```



           
  
