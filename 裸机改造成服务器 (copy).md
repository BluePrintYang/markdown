## 改造服务器

### 在电脑装Linux系统(以CentOS为例)

#### 首先制作启动盘

准备工具

- CentOS镜像    可在[南邮镜像源](https://mirrors.njupt.edu.cn/)下载
- 容量8G及以上的U盘



使用老毛桃工具制作启动盘

参考此篇[博客](https://blog.csdn.net/gavinbj/article/details/82843989)

打开老毛桃工具后，选择ISO模式，然后在画面右侧选择已经准备好的CentOS7安装用的ISO文件，然后直接执行一键制作。

![image-20191129191335756](裸机改造成服务器 (copy).assets/image-20191129191335756.png)

 

制作过程中会弹出如下提示信息，我们直接选择“否”，使用现有的ISO文件直接制作。

![image-20191129191402854](裸机改造成服务器 (copy).assets/image-20191129191402854.png)

按照默认的参数，直接选择写入即可。写入过程需要等待几分钟。

![image-20191129191431593](裸机改造成服务器 (copy).assets/image-20191129191431593.png)

【注意】启动制作完成后，需要我们更改一下U盘的卷标，因为在进行CentOS系统安装的时候需要使用，我们修改一个简单易识别的名字：CentOS7。



#### 安装CentOS

将机器设置为U盘启动

重启电脑(按F12进入BIOS界面)

选择UEFI:Teclast(没有这个选项的话选择带usb的选项)

参考此[博客](https://blog.csdn.net/bajiudongfeng/article/details/47732377)

 开始安装，在进入安装界面，显示 Install CentOS 7 的这个界面的时候，选择第一个选项，也就是Install CentOS 7，按e编辑启动项， 将vmlinuz initrd=initrd.imginst.stage2===hd:LABEL=CentOS\x207\x20x86_64 rd.live.check== quiet 改为：vmlinuz initrd=initrd.img inst.stage2===hd:/dev/sda1== quite 

按CTRL+X