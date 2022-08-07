
# 如何快速攒一个订阅转换API|简单十步搭建subweb

  原文转载自 「Sabrina的万事屋」 ( https://merlinblog.xyz/wiki/api-dajian.html ) By Sabrina


本文介绍的订阅转换API是基于subconverter和Flask框架的subweb项目。
	
	subweb项目地址：https://github.com/lzdnico/subweb/tree/admin
	subconverter项目地址：https://github.com/tindy2013/subconverter
之前使用Nginx搭建的时候，没问题，但是新版后端总是由于各种玄学因素出毛病。在咨询了多方大佬以后，找到了Caddy的解决方案，特此记录下来。
特别鸣谢：flycloud大佬
	
	Demo: https://bianyuan.xyz/
	[scode type="lblue"]由于多次被滥用和攻击，subweb作者@NicoNewBeee已停止STC Api的运行。请各位大佬们高抬贵手，放过公益项目。[/scode]

注意： 

这里以Ubantu16.04为例，其它系统所使用的安装命令请自行研究。[其实是因为我用的是阿里云轻量，配置比较菜，懒得折腾]
前期准备：

	① 一台VPS，配置不需要太好。
	② 两个域名，一个用于前端访问，一个用于后端订阅访问。（请提前做好域名解析以备用）
本文以api.bianyuan.xyz代表前端访问域名，dy.bianyuan.xyz代表后端访问域名。牵扯到域名的操作请自行将相关参数修改为自己的域名。

	[scode type="green"]建议开启CloudFlare的CDN，避免VPS的IP被橄榄。[/scode]
正式开始
	
	1.执行 apt update 命令以更新系统。
	2.安装docker

	curl -sSL https://get.docker.com/ | sh

安装完毕执行下边命令重启docker服务：

	systemctl start docker
	systemctl enable docker

3.拉取docker镜像

执行下边命令拉取最新docker镜像

	docker pull niconewbeee/subweb:basic


4.克隆源码

执行下边命令以clone最新的源码：

	yum install git -y

	git clone -b admin https://github.com/lzdnico/subweb.git


5.修改权限&客制化

执行以下命令以修改后端权限和启动脚本权限：

	chmod 777 /root/subweb/config/subconverter
	chmod 777 /root/subweb/docker.sh

客制化修改：
config和templates里的文件可以按需修改。
⭐⭐必须修改的项目：⭐⭐
	
	务必修改config/pref.ini里的api_access_token，以及managed_config_prefix。
	否则会导致外部资源重定向到127.0.0.1
	vi /root/subweb/config/pref.ini
举个栗子👇
```
[common]
api_mode=true
;api_access_token 修改成自己的
api_access_token=这里填写自己的token #可以随便起，比如Sabrina
default_url=
exclude_remarks=(到期|流量|时间|官网|产品)
clash_rule_base=simple_base.yml
surge_rule_base=surge.conf
surfboard_rule_base=surfboard.conf
mellow_rule_base=mellow.conf
quan_rule_base=quan.conf
quanx_rule_base=quanx.conf
loon_rule_base=loon.conf
proxy_ruleset=SYSTEM
proxy_subscription=NONE
append_proxy_type=false
rename_node=\(?((x|X)?(\d+)(\.?\d+)?)((\s?倍率?)|(x|X))\)?@$1x
[managed_config]
write_managed_config=true
;managed_config_prefix 修改成自己的
managed_config_prefix=https://dy.bianyuan.xyz #改为自己的订阅前端（和aff.py中的subip值保持一致）
```
6.运行docker实例

	docker run  -d --name=subweb --restart=always -v /root/subweb:/subweb -p 10086:10086 -p 10010:10010  niconewbeee/subweb:basic


7.修改配置文件

执行此命令：

	vi /root/subweb/api/aff.py

此步骤应该修改的内容包括：

	① subip 修改为 https://dy.bianyuan.xyz

	② apiip 修改为 https://api.bianyuan.xyz

	③ passwd 为了安全一定要修改

提示：单击键盘上的字母i进入编辑模式，此时左下角会显示-- INSERT --字样。
编辑完成后按下Esc键退出编辑模式，输入英文冒号和wq [即:wq] ，然后按下回车键，保存更改。
Code_hvdCRwTZoS.jpg
8.使用Caddy配置转发

首先运行安装脚本

wget -N –no-check-certificate https://raw.githubusercontent.com/iiiiiii1/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh

#备用地址

wget -N –no-check-certificate https://www.moerats.com/usr/shell/Caddy/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh

	以下为备用但不成功方案
	{
	yum -y install yum-plugin-copr

 	yum copr enable @caddy/caddy
	yum -y install caddy
 	whereis caddy
	systemctl start caddy
	systemctl enable caddy
	}
	
	这个命令已经失效：wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh

复制粘贴以下命令到SSH执行：

echo "api.bianyuan.xyz {
 gzip
 tls 123@gmail.com
 proxy / 8.8.8.8:10086 {
    websocket
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}
dy.bianyuan.xyz {
 gzip
 tls 123@gmail.com
 proxy / 8.8.8.8:10010 {
    websocket
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}" > /usr/local/caddy/Caddyfile

请注意：
将8.8.8.8替换为自己服务器的IP
将123@gmail.com替换为自己的邮箱
配置Caddy.jpg
启动脚本

ln -s /usr/local/caddy/caddy /usr/bin/caddy
ulimit -n 8192
caddy #这是前台运行
/etc/init.d/caddy start #这是后台运行

[scode type="lblue"]此配置会自动续签SSL证书，简单省心。[/scode]
不想自动申请，想用已经事先申请好的其它SSL证书？请看这里👇
请先在VPS创建一个文件夹用于证书的保存。举一个栗子：
假设证书（包含.cert和.key文件）已上传到 /root/ssl/ 这个文件夹，那么在配置Caddy时应使用以下代码（请根据实际情况进行修改）：

echo "https://api.bianyuan.xyz {
 gzip
 tls /root/ssl/api.bianyuan.xyz_chain.crt /root/ssl/api.bianyuan.xyz_key.key
 proxy / 8.8.8.8:10086 {
    websocket
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}
https://dy.bianyuan.xyz {
 gzip
 tls /root/ssl/dy.bianyuan.xyz_chain.crt /root/ssl/dy.bianyuan.xyz_key.key
 proxy / 8.8.8.8:10010 {
    websocket
    header_upstream Host {host}
    header_upstream X-Real-IP {remote}
    header_upstream X-Forwarded-For {remote}
    header_upstream X-Forwarded-Port {server_port}
    header_upstream X-Forwarded-Proto {scheme}
  }
}" > /usr/local/caddy/Caddyfile

此配置需要先手动上传证书到自己指定的文件夹，证书到期后手动替换即可。
9.重启Caddy

执行以下命令以重启Caddy转发服务

/etc/init.d/caddy restart

执行下边命令以查看日志，如果出现自己的两个域名，说明转发就成功了，否则请检查自己之前的操作。

tail -f /tmp/caddy.log

提示：按Ctrl+C可以退出log界面
检查Caddy配置.jpg
10.重启面板服务

执行下边命令重启面板，即可正常访问使用。

docker restart subweb

每次开机后手动启动

	
	ulimit -n 8192
	/etc/init.d/caddy start

	
	

其它
管理员系统

新版API支持后台管理，可以在线修改pref.ini等默认配置，支持在线重启前、后端等。
访问地址：ip:10086/admin
地址为：../api/aff.py
管理员密码: api/aff.py的passwd参数

后台管理.jpg
监控服务器运行状态

建议为服务器安装一个NodeQuery以关注VPS状态。NodeQuery可以监控服务器的整体状态，在服务器负载过高的时候会通过邮件提醒你及时查看和维护，安装简单，操作方便，适合小白用户。免费版账户最多可以为10个服务器添加监控。
chrome_rwhFpmc3xs.jpg
注册账号这一步就略过了。
登陆后点击右上角的NewServer以添加服务器。
添加新的服务器.jpg
然后进行简单的设置后点击左下角的CreateServer。
设置条件.jpg
这时会跳出安装命令，复制，粘贴到SSH执行。
复制安装命令.jpg
安装完毕后，点击网页下方的I've installed the agent, continue to server details进行下一步操作。
稍等片刻，即可查看自己服务器的运行状态。
NodeQuery缩略图.jpg
服务器状态.jpg
Caddy的其它命令

这里分享一下其它可能会用到的Caddy命令。
停止：

/etc/init.d/caddy stop

重启：

/etc/init.d/caddy restart

查看状态：

/etc/init.d/caddy status

查看Caddy启动日志：

tail -f /tmp/caddy.log

Caddy自动申请SSL证书位置：

/.caddy/acme/acme-v01.api.letsencrypt.org/sites/xxx.xxx(域名)/

Caddy配置文件位置：

/usr/local/caddy/Caddyfile

© 2022 己思
GitHub
											
