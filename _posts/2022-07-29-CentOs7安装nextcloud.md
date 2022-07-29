Nextcloud通过插件内置了文件下载，分享，Office文档、PDF文档查看以及在线编辑，还有日历、RSS等功能，已经脱胎于网盘向着协作式办公平台发展，下面我来演示一下CentOs7安装nextcloud过程。
# 安装Snap
安装前修改好hostname。注意替换自己域名和IP。在终端窗口中输入命令：hostname或uname –n，均可以查看到当前主机的主机名：
```
vi /etc/hosts
151.254.64.178 pan.ddaniba.tokyo pan  

vi /etc/hostname
pan.ddaniba.tokyo
hostname -F /etc/hostname #重启
hostname #再次查看
```
## 开始安装
如果没有自己域名，那从这里开始输入
```
yum update
yum install epel-release
yum install snapd
```
过程中如果有提问，都是按`y`回车。
## 开启snap服务，重启
```
systemctl restart snapd.service
systemctl enable --now snapd.socket
ln -s /var/lib/snapd/snap /snap
reboot
```
# 安装Nextcloud
```
snap install nextcloud
```
浏览器输入域名或IP地址，开始第一次访问 , 输入管理员的帐号和密码。确定后，安装。

服务默认是80端口，可以通过命令修改 
```
snap set nextcloud ports.http=8000
```
卸载命令
```
 snap remove nextcloud 
```
