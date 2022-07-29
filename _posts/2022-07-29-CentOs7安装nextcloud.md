Nextcloud通过插件内置了文件下载，分享，Office文档、PDF文档查看以及在线编辑，还有日历、RSS等功能，已经脱胎于网盘向着协作式办公平台发展，下面我来演示一下CentOs7安装nextcloud过程。
# 安装Snap
安装前修改好hostname。在终端窗口中输入命令：hostname或uname –n，均可以查看到当前主机的主机名：
```
vi /etc/hosts
151.254.64.178 pan.ddaniba.tokyo pan  

vi /etc/hostname
pan.ddaniba.tokyo
hostname -F /etc/hostname #重启
hostname #再次查看
```
## 开始安装
```
yum update
yum install epel-release
yum install snapd
sudo snap install nextcloud
```
过程中都是按`y`回车。
## 开启snap服务  
```
systemctl restart snapd.service
```
