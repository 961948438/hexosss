---
title: Yapi接口管理工具
date: 2021-04-09 15:10:23
tags:
  - Yapi接口管理工具的使用
categories:
  - 后端杂项
---

## Yapi接口管理工具的使用
说明：文章引用稍加个人改动

### 搭建环境
随着前端技术的浪潮层起不断，前后端分离项目越来越多，接口管理以及规范的接口文档可以使得前后端调试流程更加省时省力。
重要的是：后端接口项目一般缺少优雅的接口管理工具，一旦项目完成年代久远或者是后续修改接口，文档丢失、接口更新不及时将成为一大难题。
省时省力的反之就是费时费力。。。。。。成为老大难项目

页面预览：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi1mYzUxYjdhOGNiOTA2YjU4ZmU0YTk0MGIzYjEyNWFmZl9oZC5qcGc?x-oss-process=image/format,png)

inux和window的安装环境基本上没有什么区别。
安装有两种方式，这里面我都会提到。因为考虑到大家如果要是在公司测试环境搭建这个，似乎基本上都是属于没有网络的情况，因此这里面我着重介绍一下离线安装：

mongodb（window or linux）---根据自己的使用环境有关
node 环境（window or linux） ---注意版本不能太高否则会报错，建议12版本node
YApi（离线安装需要准备的）

mongodb下载地址（网络导致的下载问题后面我会给分享链接）：
安装的时候不建议勾选安装compass，否则由于网络问题导致的安装失败将会浪费很多时间。
https://www.mongodb.com/download-center/community
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi05N2YzZjEyOGI1YmQ2MWI2NjdmMjc4YWFiZTUwZWE1YV9oZC5qcGc?x-oss-process=image/format,png)

Yapi在线安装方式：
node环境安装完成之后，执行命令 npm intall yapi-cli -g
安装完成之后启动Yapi服务： yapi server（在我们指定的接口项目里，没有就创建一个）
注意：在线安装的时候Yapi命令会有对应的配置页面，如果这里采用的是离线安装需要下载对应的安装文件，启动server/app.js即可：
在线安装启动yapi server之后可以看到提示：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi05NTg2MmQzOTEyYmNlNDQyOTM4NDA1ZjMyZGU3Y2RhYV9oZC5qcGc?x-oss-process=image/format,png)

提示的意思是服务启动就绪了，我们可以在浏览器中访问主机所在的ip加上执行的端口浏览配置页面：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi05NTg2MmQzOTEyYmNlNDQyOTM4NDA1ZjMyZGU3Y2RhYV9oZC5qcGc?x-oss-process=image/format,png)
点击部署
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi1iZmJmZWNlNjk4NzVhYzkwMmJhOWM4NDg2NWMxYzFhNl9oZC5qcGc?x-oss-process=image/format,png)
记住初始化密码:
账号：admin@admin.com
密码：ymfe.org"

启动之后，登陆用户进得去基本上就是没什么大问题了：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi03N2IzMzc2OWU2MDhmOTA4OTMxYWI3MGJhYjNkMTNkM19oZC5qcGc?x-oss-process=image/format,png)

### Yapi的使用
由于我这里面演示使用的是管理员账号，普通用户登录不可见操作可忽略！！！！
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMxLnpoaW1nLmNvbS84MC92Mi1kODA0NDhlZmU5MDczMTk2MWRjZjRlMTE2ZjY0Yjk4MF9oZC5qcGc?x-oss-process=image/format,png)
创建一个项目
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi1mNzc4MDQ2NjFlMDdhMmFjYjlhMmIwYjM0OGU4N2VjNl9oZC5qcGc?x-oss-process=image/format,png)
添加一个接口
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMyLnpoaW1nLmNvbS84MC92Mi02MTVlOGNiNTg4NjdlNGJjMmE2NzNmMjA4OGE0YTE4NV9oZC5qcGc?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi1kY2VmM2Y0OTI3NjJmNzFjYTA5MTliOTM3NWJjZjczYl9oZC5wbmc?x-oss-process=image/format,png)

可以看到接口有对应的状态，如果前后端都在这个平台可以实时看到接口的开发进度。
接口编辑设置：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMyLnpoaW1nLmNvbS84MC92Mi1hY2I0ZmI4YmIxZDZjNGQ5OTQ3MDA0NDBjOTk0MjdhNV9oZC5qcGc?x-oss-process=image/format,png)
至于预览编辑运行等，操作几遍就知道大致流程，高级Mock是需要时间去学习的
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi1jMzMyMTg5NGI1YThiZDZiMzQyN2U2NzBlOGQ0ODQ4M19oZC5qcGc?x-oss-process=image/format,png)
接口文档定义结束之后就可以使用mock地址进行请求访问，但是编写mock期望又是很浪费时间的一个操作，所以推荐文档写的详细点，如果对于前后端分离有严格流程或者前端比较擅长mock，主导权可以交给他们来。

### 数据管理

数据管理在这里面是没得说的，我这里面列举一下支持的导出种类：
导出：
  + html
  + markdowm
  + swaggerApi.json
导入：
  + postMan
  + swagger
  + har
  + json
导入导出不仅可以消除跨平台导入的问题，有利于文档分享，还可以定时做备份和恢复

### 环境配置
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi00MWEwZmE5NDZjYmM0Y2E2M2JhZmY3M2IwNTc0MTJhMl9oZC5qcGc?x-oss-process=image/format,png)
环境列表：
一般都是定义为：
开发环境 测试环境 本地环境等
在接口调用的时候可以选择
global介绍:在global中可以定义全局的参数，举例说明。
使用测试环境中，每次都有一个token需要在请求的时候发送进去，但是每次拷贝到请求中显得十分麻烦，可以在此处定义参数的数值，在参数改变或者需要更新的时候只需要修改全局参数中的这个参数就可以使用，在接口参数中使用需要使用{{global.name}}。截图详细说明：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMxLnpoaW1nLmNvbS84MC92Mi1lOWVkZWI3NTRlZmUwYzU0MWViMDgyM2FiZDMxMmY1MF9oZC5qcGc?x-oss-process=image/format,png)
为了实现这个请求，还可以使用其他的方式实现，这个还是需要看一下官方的文档才能写的出来的，有点小难度：


### 重点token

问题：
请求的时候每次都需要在header中传入一个token数据，但是token不需要每次都用，只需要在失效的时候刷新就行了，如果设置成每次都更改global中的参数，还是需要隔一段时间之后就进行更新操作。
解决：
token失效的时候每次都会返回403，因此这里判断返回状态如果是403的时候重新请求登录操作获取token的数据，然后每次请求的时候都塞到header中既可以解决。
一些请求参数和返回参数都能在context中找到，代码脚本如下（代码隶属个人风格，仅供参考）
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMxLnpoaW1nLmNvbS92Mi1kNGU5MjJjM2JlZjVhY2Y5YjMyNjU5YWI5MjQxNzQ4Y19yLmpwZw?x-oss-process=image/format,png)

```
/*请求执行之前使用脚本控制*/
var token = storage.getItem("T-Authorization");
context.requestHeader['T-Authorization'] = token;
/*请求结束之后使用脚本控制*/
var requestPath = context.pathname
var statusCode = context.responseStatus
var token = storage.getItem("T-Authorization");
 
if (403 == statusCode) {
    
    const api = context.utils.axios.post('http://127.0.0.1:10001/bjDemographics/auth/getToken?account=admin&password=21232f297a57a5a743894a0e4a801fc3'
        );
    api.then((resp) => {
        var codeResult = resp.status
        if (codeResult == 200) {
            var token = resp.data.obj.token
            storage.setItem("T-Authorization", token)
            console.log(token)
        } else {
            console.error("请检查鉴权接口是否正常");
        }
    });
```