# 准备阶段

1.查看是否已经安装Python

Centos7默认安装了python2.7.5 因为一些命令要用它比如yum 它使用的是python2.7.5。

使用python -V命令查看一下是否安装Python

    python -V
    which python #看可执行文件位置
      /usr/bin/python
    cd /usr/bin/ #进入所在目录
    ls -al python* #查看命令对应目录
      lrwxrwxrwx. 1 root root    7 Jul 31 21:24 python -> python2
      lrwxrwxrwx. 1 root root    9 Jul 31 21:24 python2 -> python2.7
      -rwxr-xr-x. 1 root root 7144 Jun 28 11:30 python2.7
   可以看到可执行文件python指向python2，python2又指向python2.7也就是说Python命令执行的系统预装的Python2.7。

     cp python python.bak #备份python文件
2.安装新版本的Python

下载Python新版本https://www.python.org/downloads/source/

可以看到截止到目前，最新版本为3.10.5版本是2022.6.6发布的。
 ![](/images/947.png) 
 
    cd /home/ #转到home目录
    wget https://www.python.org/ftp/python/3.10.5/Python-3.10.5.tar.xz #下载新版Python，注意修改你要下载的版本号
    tar -xvJf Python-3.10.5.tar.xz #解压缩
    cd Python-3.10.5 #进入目录，注意你自己的版本
 # 安装阶段
1.安装编译相关工具
    
    yum -y groupinstall "Development tools"
    yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
    yum install libffi-devel -y
2.编译安装

    cd /usr/bin
    mv python python.ba2
    cd /home/myhome/Python-3.10.5
    mkdir /usr/local/python3 #创建编译安装目录
    ./configure --prefix=/usr/local/python3
    make && make install #漫长的等待
 3.创建软连接

    ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3
    ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3

4.验证是否成功

    python3 -V
    pip3 -V
    
# 修正
    ln -s /usr/local/bin/python3 /usr/bin/python #系统默认版本更换为3.10
    mv /usr/bin/pip pip8
    ln -s /usr/local/bin/pip3 /usr/bin/pip #修改系统默认pip
    vi /usr/bin/yum #新版python可能影响yum,需要改回2.7
        #把文件头部的#!/usr/bin/python改成#!/usr/bin/python2.7保存退出
    #验证系统版本
    python -V
    pip -V
    
  ##安装完毕
    
