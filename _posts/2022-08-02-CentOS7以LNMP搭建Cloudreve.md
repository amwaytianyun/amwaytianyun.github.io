CentOS7以LNMP搭建Cloudreve.md



安装LNMP环境,相关文章很多，我也写过，请参考


Nginx版本：Nginx 1.12.2
MySQL版本：MySQL 5.7.25
PHP版本：PHP 7.0.33

# 安装
[下载最新版](https://github.com/cloudreve/Cloudreve/releases)

cd /usr/share/nginx/html/
wget https://github.com/cloudreve/Cloudreve/releases/download/3.5.3/cloudreve_3.5.3_linux_amd64.tar.gz
tar -zxvf cloudreve_3.5.3_linux_amd64.tar.gz
chmod +x ./cloudreve
./cloudreve
admin@cloudreve.org
[Info]    2022-08-01 23:18:11 初始管理员密码：6xwFjVsk
配置

    vi /etc/nginx/nginx.conf

    # 开启默认index.php  添加伪静态。
        location / {
          root   html;
          index  index.html index.htm index.php;
                if (!-e $request_filename) {
                rewrite  ^(.*)$  /index.php?s=/$1  last;
                break;
                }
        }
        
    #开启nginx 支持PHP
        location ~ \.php$ {
            root           /usr/share/nginx/html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
            include        fastcgi_params;
            }

 
导入MySQL
    
    mysql -u root -p
    CREATE DATABASE Cloudreve;

    CREATE USER cloudreve@127.0.0.1 IDENTIFIED BY 'examplepassword';

    GRANT ALL ON Cloudreve.* TO cloudreve@127.0.0.1;

    exit;
    # mysql -uroot -p*** mysql < /usr/share/nginx/html/mysql.sql

    chmod 777 cloudreve

    chown root:root cloudreve

    ./cloudreve

打开浏览配置

http://IP/CloudreveInstaller
根据提示 把目录权限分配完成。(最后的二步验证可不操作)

信息填写
站点URL
http://IP/
MySQL服务器
127.0.0.1
MySQL端口
3306
MySQL用户名
root
MySQL密码
-******
数据库名
mysql
这样主环境就搭建完成
/usr/share/nginx/html/public

这个目录就是上传保存文件的目录。 可以单独挂一个大点的磁盘到此目录。
添加定时任务

crontab -e
5 * * * * curl http://IP/Cron

    1
    2

更改最大上传权限
管理面板->上传策略->编辑->单文件最大大小->填写自己设定。

vi /etc/nginx/nginx.conf
http { } 里面添加

    client_max_body_size 4000m;

    1
    2
    3
    4

vi /etc/php.ini

file_uploads = On ;打开文件上传选项 
upload_max_filesize = 4000M ;上传文件上限 
post_max_size = 20M ;post上限 

;还需要根据实际情况调整下面的选项
max_execution_time = 0 ;脚本最大执行时间（秒） ，过短会导致文件还没上传完脚本就停止了。
max_input_time = 0 ;请求的最大传输时间，过短会导致文件还没上传完传输就停止了。
memory_limit = 2048M ;内存上限 

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10

开启离线下载功能
安装aria2

yum -y install aria2

    1

创建aria2配置目录

mkdir /usr/share/nginx/html/public/aria2
cd /usr/share/nginx/html/public/aria2
touch aria2.session
touch aria2.conf
mkdir tmp
chmod 777 tmp

    1
    2
    3
    4
    5
    6

配置
vi aria2.conf

# 文件的save路径(可使用绝对路径或对立路径), 原始: 当前启动位置
#dir=/home/download
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 原始:16M
#disk-cache=32M
# 文件预分派方法, 能有效低落磁盘碎片, 原始:prealfubitech
# 预分派所需时间: none < falfubitech ? trunc < prealfubitech
# falfubitech和trunc则需要文件系统和内核支持
# NTFS提议使用falfubitech, EXT3/4提议trunc, MAC下需要正文此项
#file-alfubitechation=trunc
# 断点续传
continue=true


## 下载连接相关 ##


# 最大同时下载任务数, 执行时可修正, 原始:5
max-concurrent-downloads=5
# 同一服务器连接数, 增加时可指定, 原始:1
max-connection-per-server=5
# 最小文件分片巨细, 增加时可指定, 取值范畴1M -1024M, 原始:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 增加时可指定, 原始:5
split=32
# 团体下载速率限定, 执行时可修正, 原始:0
#max-overall-download-limit=0
# 单个任务下载速率限定, 原始:0
#max-download-limit=0
# 团体上传速率限定, 执行时可修正, 原始:0
#max-overall-upload-limit=0
# 单个任务上传速率限定, 原始:0
#max-upload-limit=0
# 禁用IPv6, 原始:false
disable-ipv6=true


## 进度save相关 ##


# 从会话文件中读取下载任务
input-file=/usr/share/nginx/html/public/aria2/aria2.session
#在Aria2退出时save`毛病/未完成`的下载任务到会话文件
save-session=/usr/share/nginx/html/public/aria2/aria2.session
# 按时save会话, 0为退出时才save, 需1.16.1以上版本, 原始:0
#save-session-interval=60


## RPC相关设置 ##


# 启用RPC, 原始:false
enable-rpc=true
# 允许一切来源, 原始:false
rpc-allow-origin-all=true
# 允许非表面访问, 原始:false
rpc-listen-all=true
# 事变轮询方法, 取值:[epoll, kqueue, port, poll, select], 不同系统原始值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修正, 原始:6800
#rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 代替 --rpc-user 和 --rpc-passwd 选项
rpc-secret=lswzw
# 设置的RPC访问用户名, 此选项新版已烧毁, 提议改用 --rpc-secret 选项
#rpc-user=
# 设置的RPC访问**, 此选项新版已烧毁, 提议改用 --rpc-secret 选项
#rpc-passwd=


## BT/PT下载相关 ##


# 当下载的是一个种子(以.torrent煞尾)时, 主动开始BT任务, 原始:true
#follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 原始:6881-6999
listen-port=51413
# 单个种子最大连接数, 原始:55
#bt-max-peers=55
# openDHT功能, PT需要禁用, 原始:true
enable-dht=false
# openIPv6 DHT功能, PT需要禁用
#enable-dht6=false
# DHT网络监听端口, 原始:6881-6999
#dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 原始:false
#bt-enable-lpd=false
# 种子对换, PT需要禁用, 原始:true
enable-peer-exchange=false
# 每个种子限速, 对少种的PT很有用, 原始:50K
#bt-request-peer-speed-limit=50K
# 客户端假装, PT需要
peer-id-prefix=-TR2770-
user-agent=Transmission/2.77
# 当种子的分享率到达这个数时, 主动终止做种, 0为一向做种, 原始:1.0
seed-ratio=0
# 胁制save会话, 就算任务曾经完成, 原始:false
# 较新的版本开启后会在任务完成后仍旧保存.aria2文件
#force-save=false
# BT校验相关, 原始:true
#bt-hash-check-seed=true
# 持续之前的BT任务时, 无需再次校验, 原始:false
bt-seed-unverified=true
# save磁力链接元数据为种子文件(.torrent文件), 原始:false
bt-save-metadata=true

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    32
    33
    34
    35
    36
    37
    38
    39
    40
    41
    42
    43
    44
    45
    46
    47
    48
    49
    50
    51
    52
    53
    54
    55
    56
    57
    58
    59
    60
    61
    62
    63
    64
    65
    66
    67
    68
    69
    70
    71
    72
    73
    74
    75
    76
    77
    78
    79
    80
    81
    82
    83
    84
    85
    86
    87
    88
    89
    90
    91
    92
    93
    94
    95
    96
    97
    98
    99
    100
    101
    102
    103
    104

须要修改地方

rpc-secret= Token
Token 获取方法。
管理面板->其他->任务队列->随机生成Token
启动aria2

#用php用户启动。这样权限才能保存。
sudo -u apache aria2c --conf-path=./aria2.conf -D

    1
    2

配置启用离线下载
管理面板->离线下载

Token 就是上面获取的Token
下载临时目录 /usr/share/nginx/html/public/aria2/tmp
管理面板->用户组->管理员->编辑->离线下载->允许。
如果让用户也可以离线下载 就改用户组这里就行了。
这样在新建项目里就有离线下载了。
