---
title: natapp内网穿透的使用
date: 2021-04-08 14:57:21
tags:
  - natapp内网穿透工具的使用
categories:
  - 后端杂项
---

### natapp服务器更新:

  注意：当我们开启了本地natapp服务器之后，将来我们的随机域名/指定域名将指向我们的
  的本地ip的某个端口，记得几时开natapp服务器
  本地natapp服务器的后台管理端通常在http://localhost:8000

  全面支持HTTPS协议以及本地SSL证书,支持WSS协议.同时支持HTTP/2 WEB协议,支持微信小程序本地开发.全面自动支持泛子域名与访客真实IP地址.

  进入官网下载客服端,如下:

  ![](https://img-blog.csdnimg.cn/20190731162921964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)

  解压下载的客服端,然后配置环境变量,地址为解压路径D:\Program Files\natapp,如下:

  ![](https://img-blog.csdnimg.cn/2019073116421341.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)

  下面需要创建隧道,通过authtoken参数才能登录,下面是我在官网创建的免费通道(此处需要实名认证),如下:

  ![](https://img-blog.csdnimg.cn/20190731170102886.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)

  购买免费通道后,需要authtoken参数,如下:

  ![](https://img-blog.csdnimg.cn/20190731170912652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)
  
   打开natapp目录,执行natapp -authtoken authtoken参数,如下:

  ![](https://img-blog.csdnimg.cn/20190731172229734.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)
  
  出现下图,表示成功了,外网输入http://dn69n6.natappfree.cc即可访问本地的地址(注意访问期间,下面这个窗口不能关闭)

  ![](https://img-blog.csdnimg.cn/20190731172531602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzkyMDY5,size_16,color_FFFFFF,t_70)
  ![]()