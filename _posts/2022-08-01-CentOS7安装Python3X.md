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

     mv python python.bak #备份python文件
2.安装新版本的Python

下载Python新版本https://www.python.org/downloads/source/

可以看到截止到目前，最新版本为3.10.5版本是2022.6.6发布的。
