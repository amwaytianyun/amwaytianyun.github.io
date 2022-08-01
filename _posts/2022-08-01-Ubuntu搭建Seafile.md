这里我们使用apt包管理器进行安装，安装之前，我们首先需要使用sudo apt-get update更新apt包管理器列表。然后在安装python及其 Seafile 所需要的环境。最后使用pip工具安装 Seafile 所需要的视频渲染动画插件moviepy具体代码如下

    apt update
    apt install python python2.7 libpython2.7 python-setuptools python-imaging python-ldap python-urllib3 ffmpeg python-pip python-mysqldb python-memcache -y
    apt-get install mysql-server
    pip install pillow moviepy
 [这里获得下载地址](https://www.seafile.com/download/)
    
    wget https://seafile-downloads.oss-cn-shanghai.aliyuncs.com/seafile-server_9.0.6_x86-64.tar.gz
部署Seafile

接下来，我们需要解压这个文件，为了方便后续升级，建议按照下面的步骤去设置目录结构。我们新建一个名为Seafile的目录，当然，这个名字是自定义的，我这里只是用Seafile演示，请执行下面的命令。
```
mkdir Seafile
mv seafile-server_* Seafile
cd Seafile
tar -zxvf seafile-server_*
mkdir installed
mv seafile-server_* installed

#这时，你的文件目录类似如下

ubuntu@VM-0-7-ubuntu:~$ tree Seafile -L 2
Seafile
├── installed
│   └── seafile-server_6.3.3_x86-64.tar.gz
└── seafile-server-6.3.3
    ├── check_init_admin.py
    ├── reset-admin.sh
    ├── runtime
    ├── seaf-fsck.sh
    ├── seaf-fuse.sh
    ├── seaf-gc.sh
    ├── seafile
    ├── seafile.sh
    ├── seahub
    ├── seahub.sh
    ├── setup-seafile-mysql.py
    ├── setup-seafile-mysql.sh
    ├── setup-seafile.sh
    └── upgrade
```
# 安装Seafile

接下来就可以进行安装了，我们找到上一步新建的目录，然后开始安装。

    cd seafile-server-*
    ./setup-seafile-mysql.sh

输出如下：

ubuntu@VM-0-7-ubuntu:~/Seafile/seafile-server-6.3.3$ sudo ./setup-seafile-mysql.sh
Checking python on this machine ...
  Checking python module: setuptools ... Done.
  Checking python module: python-imaging ... Done.
  Checking python module: python-mysqldb ... Done.
​
-----------------------------------------------------------------
This script will guide you to setup your seafile server using MySQL.
Make sure you have read seafile server manual at
​
        https://github.com/haiwen/seafile/wiki
​
Press ENTER to continue
----------------------------------------------------------------

系统示意我们按ENTER键继续，我们按下键盘上的回车键，第一个弹出的是server name这里我们输入自己想定义的服务器名称，我这里填写为qcloud-Seafile，接下来要求填写你的服务器的IP或者指向的域名，我这里填写自己CVM的IP123.456.789.123。接下来系统要求填写你存放文件数据的目录，因为我们要放在CFS上，则填写CFS映射的文件夹\home\ubuntu\qcloud-cfs。最后填写你服务器文件同步端口，该端口用于文件同步，请使用默认的 8082，不能更改。然后，服务器将要求你选择创建数据库的方式。

-------------------------------------------------------
Please choose a way to initialize seafile databases:
-------------------------------------------------------
​
[1] Create new ccnet/seafile/seahub databases
[2] Use existing ccnet/seafile/seahub databases

    如果选择1, 你需要提供根密码. 脚本程序会创建数据库和用户。
    如果选择2, ccnet/seafile/seahub 数据库应该已经被你（或者其他人）提前创建。

这里我们是新建，则选择1，选择完成后，系统要求你输入数据库地址，我们这里输入准备数据库步骤中创建数据库的内网地址。我这里填172.27.16.14，接下来会询问你使用那个账户运行MySQL，默认是root，我们直接点回车，接下来会询问你的数据库的端口，我们依然保持默认3306端口，接下来，输入你的数据库的密码。默认密码是不显示的。输入完成后，系统会为你创建一个名为seafile的用户，我们保持默认即可，之后输入你为seafile用户创建的密码。创建完成后系统要求你创建ccnet-db数据库，我们保持默认的ccnet-db就行。接下来要求创建seafile-server的数据库，我们依然保持默认。接下来还要创建seahub数据库，仍保持默认就OK。一切完成后，系统将输出下面的内容。

--------------------------------
This is your configuration
---------------------------------
​
    server name:            qcloud-Seafile
    server ip/domain:       123.456.789.123
​
    seafile data dir:       \home\ubuntu\qcloud-cfs
    fileserver port:        8082
​
    database:               create new
    ccnet database:         ccnet-db
    seafile database:       seafile-db
    seahub database:        seahub-db
    database user:          seafile
​
​
---------------------------------
Press ENTER to continue, or Ctrl-C to abort
---------------------------------

我们按下键盘上的Enter键继续。不久，你会发现服务器创建完成。

# 启动 Seafile 服务器

现在，我们打开seafile-server-6.3.3目录，使用下面的命令启动启动 Seafile 服务器和 Seahub 网站。

    启动 Seafile 服务:

sudo ./seafile.sh start

    启动 Seahub 网站 （默认运行在8000端口上）

sudo ./seahub.sh start

你第一次启动 seahub 时，seahub.sh 脚本会提示你创建一个 seafile 管理员帐号。会要求你输入电子邮箱，管理密码及重新输入密码。请如实输入就行。输入完成后，你就通过访问http://你服务器的地址:8000来打开Seafile了。

 

输入你刚创建的用户名及密码，就可以访问 seafile 管理页面了。

关闭/重启 Seafile 和 Seahub

关闭

./seahub.sh stop # 停止 Seahub
./seafile.sh stop # 停止 Seafile 进程

重启

./seafile.sh restart # 停止当前的 Seafile 进程，然后重启 Seafile
./seahub.sh restart  # 停止当前的 Seahub 进程，并在 8000 端口重新启动 Seahub

如果停止/重启的脚本运行失败

大多数情况下 seafile.sh seahub.sh 脚本可以正常工作。如果遇到问题：

    使用pgrep命令检查 seafile/seahub 进程是否还在运行中

pgrep -f seafile-controller # 查看 Seafile 进程
pgrep -f "seahub" # 查看 Seahub 进程

    使用pkill命令杀掉相关进程

pkill -f seafile-controller # 结束 Seafile 进程
pkill -f "seahub" # 结束 Seafile 进程
————————————————

 
