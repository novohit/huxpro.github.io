---
layout: post
title: "阿里云搭建代理服务器"
date: 2023-11-06
author: "Novo"
header-img: "img/post-bg-2023.jpg"
tags: [网络, 代理]
---

> 本文仅做技术分享，请勿使用服务器做违法行为。

[阿里云高校计划](https://university.aliyun.com/?spm=5176.28508143.J_4VYgf18xNlTAyFFbOuOQe.50.73b2154aoIrBI2&scm=20140722.M_10076475._.V_1)，每年 300 无门槛优惠券。



## VPS 选择

非大陆机器即可，阿里云可以选则香港或者新加坡的轻量应用服务器，香港的每天 0 点限额发售，网络相对会比较稳定一点。



## 重装系统

因为阿里云自带的镜像会自带安装有阿里云盾，会监控服务器，所以...要重新安装一个纯净的镜像。

登录阿里云控制台，创建实例后之前点远程连接

![image-20231106130853031](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106130853031.png)

连接上后，使用一键重装脚本 [https://github.com/veip007/dd](https://github.com/veip007/dd)

这里贴出命令：

```
wget -N --no-check-certificate https://down.vpsaff.net/linux/dd/network-reinstall-os.sh && chmod +x network-reinstall-os.sh && ./network-reinstall-os.sh
```

![image-20231106131837773](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106131837773.png)

根据自身需求和主机配置，选择系统，这里我选择的是Debian 10系统

输入编号回车就开始下载和解压系统相关文件了，然后会断连。

大约要等10-20分钟，让主机自己进行系统安装，如果主机支持VNC，可以通过VNC，观察系统安装进度。

如果能通过 `ssh` 连接上主机了，说明系统安装成功了，下图是已经成功安装后的界面，可以看到阿里云的云监控是停机状态，后续可以自行修改 `root` 密码。

![image-20231106132235091](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106132235091.png)



## 搭建 x-ui 面板

[https://github.com/vaxilu/x-ui](https://github.com/vaxilu/x-ui)

这里贴出命令

```
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```

安装过程会让你设置初始账号和面板访问端口，端口设置一个不常见的即可，记得在阿里云控制台开放一下访问的端口。

![image-20231106133758455](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106133758455.png)

## 搭建节点

直接看视频从 5分钟开始 [https://www.youtube.com/watch?v=SpxTFes1B8U](https://www.youtube.com/watch?v=SpxTFes1B8U)

1. 节点的端口记得也要在阿里云设置开放

2. 访问面板 404 page not found 问题，是因为根目录变了

可以用 `x-ui` 命令查看根目录

![image-20231106134547695](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106134547695.png)

![image-20231106134709842](https://zwx-images-1305338888.cos.ap-guangzhou.myqcloud.com/img/2023/11/06/image-20231106134709842.png)
