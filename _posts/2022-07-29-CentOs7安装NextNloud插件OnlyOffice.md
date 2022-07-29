书接上回  [CentOs7安装NextNloud](https://amwaytianyun.github.io/2022/07/29/CentOs7%E5%AE%89%E8%A3%85nextcloud.html)
,想要实现在线查看和编辑office文档不是容易的事情，电脑上需要安装软件，网页上就需要相应的服务器，在这里我们用docker实现。
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

# 安装OnlyOffice
## 拉取onlyoffice镜像
```
docker pull onlyoffice/documentserver
```
这个过程相当漫长，耐心等待，拉取结束后查看镜像
```
docker images
```
出现onlyoffice即为成功
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
onlyoffice/documentserver   latest              5bebc75c54be        2 weeks ago         2.57GB

## 创建并且启动onlyoffice容器
```
docker run -i -t -d -p 8090:80 --restart=always onlyoffice/documentserver
```
关键参数解读

-p 8090:80 表示端口映射，前者是宿主机端口，后者是容器内的映射端口。

--restart=always 容器自动重启

onlyoffice/documentserver 镜像名称

## 查看容器是否在运行
```
docker ps
```
以下显示即为正常运行
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                           NAMES
798add6ef38a        onlyoffice/documentserver   "/app/ds/run-docum..."   2 minutes ago       Up 2 minutes        443/tcp, 0.0.0.0:8090->80/tcp   heuristic_mcnulty

命令行验证
```
curl 127.0.0.1:8090/welcome
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Redirecting</title>
</head>
<body>
<pre>Redirecting to <a href="/welcome/">/welcome/</a></pre>
</body>
</html>

浏览器验证，浏览器输入
···
IP地址:8090
···
如下显示即安装完成
 ![](/images/932.png)
 随着版本不同画面会有不同，只要logo出现在页面就是ok
 
 # onlyoffice插件链接NextCloud
 ## 安装onlyoffice插件

打开nextcloud网页，在个人头像-应用-office&text栏，选择onlyoffice，直接安装并使用

![](/images/935.png)

## onlyoffice插件启用文档服务

在设置-onlyoffice设置中，输入上一步访问成功的网址(http://IP地址:8090)，点击save保存，有提示success成功，安装成功，接下来就可以在线预览和编辑office文档
![](/images/936.png)

