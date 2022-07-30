Nextcloud通过插件内置了文件下载，分享，Office文档、PDF文档查看以及在线编辑，还有日历、RSS等功能，已经脱胎于网盘向着协作式办公平台发展，下面我来演示一下CentOs7安装nextcloud过程。
# 安装Snap
安装前修改好hostname。这一步可以不做，喜欢用域名的可以做，但可能与某些插件冲突，不建议做。直接跳转到开始安装即可。

 <details><summary>就要用域名</summary>

<p>

 就要用域名的操作如下，注意替换自己域名和IP。在终端窗口中输入命令：hostname或uname –n，均可以查看到当前主机的主机名：
 
```
vi /etc/hosts
151.254.64.178 pan.ddaniba.tokyo pan  

vi /etc/hostname
pan.ddaniba.tokyo
hostname -F /etc/hostname #重启
hostname #再次查看
```
 
 </p>
  </details>
  
## 开始安装
如果不用域名，那从这里开始输入
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

服务默认是80端口，可以通过命令修改 （一般没必要改）

snap set nextcloud ports.http=8000

卸载命令为（安装时不要输入）

 snap remove nextcloud 

Nextcloud可以实现文件上传，下载，分享，照片的在线幻灯播放，视频在线播放
![](/images/931.png "Nextcloud界面")
# Nextcloud安装SSL证书
强烈不建议安装，会和某些插件冲突

<details>
 <p>
如果以后想安装在线编辑office插件，建议先不要装SSL.
安装前一定先确保你的域名已经成功解析到你的VPS主机上，然后执行命令：
  
```
nextcloud.enable-https lets-encrypt #安装Let's Encrypt SSL
#如果你想使用自己的证书，请执行：
nextcloud.enable-https self-signed
#如果你是使用自已的证书，请在执行命令后找到SSL证书的路径，将自己的证书上传替换生成的自签名证书文件即可。

####如果提示 command not found，请加上路径
/snap/bin/nextcloud.enable-https lets-encrypt
/snap/bin/nextcloud.enable-https self-signed

```
有时候，比如安装onlyoffice的时候不能加密，需要卸载证书，请执行

nextcloud.disable-https lets-encrypt

 </p>

</details>

NextCloud 到此安装完毕
下一篇介绍NextCloud在线编辑office和离线下载视频，
