yum update
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2
         yum makecache fast
# 安装 Docker
yum -y install docker
# 启动 Docker
systemctl start docker
# 设置 Docker开机自启动
systemctl enable docker

yum -y install epel-release
wget https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 创建目录
cd /home
mkdir -vp cloudreve/{uploads,avatar} \
&& touch cloudreve/conf.ini \
&& touch cloudreve/cloudreve.db

docker run -d \
-p 5212:5212 \
--mount type=bind,source=/home/cloudreve/conf.ini,target=/cloudreve/conf.ini \
--mount type=bind,source=/home/cloudreve/cloudreve.db,target=/cloudreve/cloudreve.db \
-v /home/cloudreve/uploads:/cloudreve/uploads \
-v /home/cloudreve/avatar :/cloudreve/avatar \
cloudreve/cloudreve:latest

# docker compose 构建
mkdir -vp /cloudreve/{uploads,avatar} \
&& touch /cloudreve/conf.ini \
&& touch /cloudreve/cloudreve.db \
&& mkdir -p /aria2/config \
&& mkdir -p /data/aria2 \
&& chmod -R 777 /data/aria2

vi vi docker-compose.yml

version: "2.5"
services:
  cloudreve:
    container_name: cloudreve
    image: cloudreve/cloudreve:latest
    restart: unless-stopped
    ports:
      - "5212:5212"
    volumes:
      - temp_data:/data
      - /cloudreve/uploads:/cloudreve/uploads
      - /cloudreve/conf.ini:/cloudreve/conf.ini
      - /cloudreve/cloudreve.db:/cloudreve/cloudreve.db
      - /cloudreve/avatar:/cloudreve/avatar
    depends_on:
      - aria2
  aria2:
    container_name: aria2
    image: p3terx/aria2-pro
    restart: unless-stopped
    environment:
      - RPC_SECRET=
      - RPC_PORT=6800
    volumes:
      - /aria2/config:/config
      - temp_data:/data
volumes:
  temp_data:
    driver: local
    driver_opts:
      type: none
      device: /data
      o: bind
      
  docker-compose up -d
