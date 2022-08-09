# Centos7下增加swap分区大小

如果系统的物理内存用光了，系统就会跑得很慢，但仍能运行;如果Swap空间用光了，那么系统就会发生错误。例如，Swap空间用完，则服务进程无法启动，通常会出现“application is out of memory”的错误，严重时会造成服务进程的死锁。因此Swap空间的分配是很重要的,通常Swap空间的大小应是物理内存的2-2.5倍.

此方法不限于centos，linux均适用

以下命令均需在root帐号下操作

 1.先查看一下swap的大小
  
    free -m 

2. 使用dd命令创建/home/swap这么一个分区文件。文件的大小是512000个block，一般情况下1个block为1K，所以这里空间是4G。

    
        dd if=/dev/zero of=/home/swap bs=1024 count=4096000

3.接着再把这个分区变成swap分区。

    /sbin/mkswap /home/swap

4.使用这个swap分区。使其成为有效状态。

    /sbin/swapon /home/swap

现在再用free -m命令查看一下内存和swap分区大小，就发现增加了4G的空间了。

注意当计算机重启了以后，发现swap还是原来那么大，新的swap没有自动启动，还要手动启动。那我们需要修改/etc/fstab文件，增加如下一行

　　  vi /etc/fstab
　　  末尾添加
    /home/swap swap swap defaults 0 0

总结

以上就是这篇文章的全部内容了，希望本文的内容对大家的学习或者工作具有一定的参考学习价值，如果有疑问大家可以留言交流，谢谢大家对ZaLou.Cn的支持。

调整分区大小

    /sbin/swapoff /home/swap
    dd if=/dev/zero of=/home/swap bs=1024 count=512000
    /sbin/mkswap /home/swap
    /sbin/swapon /home/swap
    
删除分区

     /sbin/swapoff /home/swap
     rm /home/swap
 　  vi /etc/fstab
    删除开机启动语句
    #/home/swap swap swap defaults 0 0
     
        
