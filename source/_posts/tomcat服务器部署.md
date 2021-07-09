---
title: tomcat服务器部署
date: 2021-05-08 18:32:31
tags:
  - Yapi接口管理工具的使用
categories:
  - 后端杂项
---

## 1. 环境
  + java版本：1.8.0_141
  + 系统：Windows 10
  + tomcat版本: 7.0.79（亲测本地1.6java环境和此版本tomcat冲突）

## 2. 下载TomCat并启动
  1. 去官网http://tomcat.apache.org/下载Tomcat压缩包。
  2. 解压到任意路径后，在bin目录下找到startup.bat，双击后即可验证是否能顺利运行。
  ![](https://img-blog.csdnimg.cn/20200510154046517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)
  3. 在浏览器输入127.0.0.1:8080，如果正确显示页面就大功告成了！
  ![](https://img-blog.csdnimg.cn/2020051015421964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)
  4. 使用80默认端口如果每次都要写8080的话会比较烦，可以通过修改server.xml的内容解决该问题。路径为conf/server.xml。找到8080后，改成80，就可以每次不用输入啦。
  ![](https://img-blog.csdnimg.cn/20200510224328821.png)

## 3. 创建本地项目
### 3.1 创建本地工程
  1. 首先在本地新建一个文件夹，并在其中放入一个HTML页面。HTML代码如下。
  ```
  <html>

  <body>

  <h1>This is heading 1</h1>
  <h2>This is heading 2</h2>
  <h3>This is heading 3</h3>
  <h4>This is heading 4</h4>
  <h5>This is heading 5</h5>
  <h6>This is heading 6</h6>

  <p>请仅仅把标题标签用于标题文本。不要仅仅为了产生粗体文本而使用它们。请使用其它标签或 CSS 代替。</p>

  </body>
  </html>
  ```
  2. 我创建的文件夹名为“LocalServer”（最好是英文，中文没敢尝试），目录如下
  ![](https://img-blog.csdnimg.cn/20200510155335233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)
  3. 该文件夹位于桌面上。
  ![](https://img-blog.csdnimg.cn/2020051015535541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)

### 3.2环境配置
  1. 复制WEB-INF文件:打开Tomcat安装目录，复制\webapps\ROOT的WEB-INF这个文件夹到自己的本地项目中。
  2. 配置server.xml: 打开Tomcat安装目录中的conf文件夹，找到server.xml，在如下位置添加对应内容
  ![](https://img-blog.csdnimg.cn/20200510160631820.png)
  > <Context path = "xxx" docBase="xxx"/>
  其中，docBase就是放本地测试网页的目录的路径，如：C:\Users\UncleDong\Desktop\LocalServer。path表示的就是以后在浏览器中输入的路径地址，是自己电脑的ip+path。如http://127.0.0.1:8080/test/index.html。
  在Tomcat目录下找见conf目录下的web.xml文件，并用编辑器打开，并作如下修改，主要是将false改成true即可。
  ![](https://img-blog.csdnimg.cn/20200510160818675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)
  3. 最后重启Tomcat
  ![](https://img-blog.csdnimg.cn/20200510161103140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mjc2MzY5Ng==,size_16,color_FFFFFF,t_70)