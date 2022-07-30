到目前为止，网上介绍Nextcloud下载插件的文章全都是以OcDownloder为对象的，但NextCloud更新到了目前版本已经不再支持安装Oc插件了。
而一个新的插件横空出世，那就是NCDownloader，这款插件简单到直接在NextCloud里面点击一次安装并开启按钮就可以实现离线下载。
 # 安装NCDownloader插件
```
打开NextCloud,
点击头像下面的应用
找到NCDownloader
点击安装并启用
```
 ![](/images/944.png)
 
搞定
 # 开始下载
 ```
 点击上方新出现的NCDownloader图标
 点击左上方 Start Aria2
 在上方输入下载地址
 点击Download
 ```
  ![](/images/939.png)
  
 下载成功
 
 # 精益求精
 还有一个小问题，需要安装ffmpeg，否则下载U2相关视频的时候，画面和声音是分开的
  ![](/images/941.png)
  
  ```
  yum install epel-release
  rpm -v --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
  rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
  yum install ffmpeg ffmpeg-devel
  ffmpeg -version
  ```
  
