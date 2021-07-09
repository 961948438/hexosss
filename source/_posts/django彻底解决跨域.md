---
title: django彻底解决跨域
date: 2020-06-25 00:24:48
tags:
  - python_django
categories:
  - python
---
#### 详情查看如下：
https://blog.csdn.net/zhu6201976/article/details/84677213

#### 补充在跨域请求下可以访问cookie但是无法设置cookies
  参考链接：https://blog.csdn.net/kevinfan2011/article/details/90111854
  浏览器接受到设置cookies的响应之后无法做出处理，这时候需要前后端都做出处理：
  同时在此访问同网站时无法携带cookie的问题
  1.前端配置axios.defaults.withCredentials=true
  2.后端配置响应的cookies支持：
  response['Access-Control-Allow-Origin'] = 'http://localhost:3000'
  response['Access-Control-Allow-Credentials'] = True

#### 熟悉django中session（session对象.set_expiry(过期time)）和cookies对象下的属性和方法

#### 利用oauth完成我们的认证信息

#### 注意有可能我们的django框架拿不到contenttype="application/json"的请求数据
  + 此时我们需要利用序列化工具json.dumps(request.body)方法；
  + 当然了如果内容类型为urlencode就可以直接获取到

####  组件的更新机制当前组件发生变化只会影响当前组件和子组件极其后代组件的更新调用render函数

####  使用纯组件

####  纯组件内部对比使用的是浅层对比，非浅层可能检测不到

  + 如果又非浅层数据修改的需求就需要使用assign或者使用扩展运算符；引用类型应该创建一个新数据；


####  js也是一样，变量存储的数据在内存中的地址，没有小数据池，

####  render方法返回的产物是虚拟dom，是为了新旧虚拟dom进行diff算法比较；

#### 虚拟dom存在可以使得我们的程序脱离浏览器，只要能够运行js的环境我们程序就能运行；

  + 为跨平台提供了条件；react为面向虚拟dom开发；

#### Router路由分发器是一个容器组件，通常要它来包裹整个应用；

  + 路由器有两种,一种是hashrouter，和browserrouter(使用h5里面的history Api来实现的)

  + 路由的执行过程：
    
    - 点击link路由入口组件，修改了浏览器地址栏中的url
    - react路由监听到地址栏中的url的变化。
    - react路由内部遍历所有的route组件，使用路由规则和路径名进行匹配；
    - 当路由规则path能够匹配到地址栏中的pathname就展route组件的内容；

  + 

#### 默认路由：默认路由的的路径就是/（模糊匹配的存在可能导致多个组件被渲染）

  + 比如 /first 时，默认路由和/first对应组件都会被匹配成功：
  + 模糊匹配规则只要pathname以path开头就会匹配成功，不一定要完全一样，
    - 比如/a/b的pathname（url中的或者link的to属性的值）和/a的path(route中的path属性的值)
    就能匹配成功
  + 精确匹配就是pathname和path要一摸一样就是精确匹配
    给我们的路由添加exact属性，切记不要随意添加exact属性，可能会影响子路由；
  + 通常情况下给我们的默认路由添加精确匹配属性是非常推荐的，其他情况下不建议添加我们的
    exact属性

#### 路由的匹配模式，模糊匹配（默认匹配模式）和精确匹配；

#### django框架如何根据已有的数据库表生成模型类

#### react的重定向组件redirect的用法：

  + <Route exact path='/' render={()=><Redirect to='/home'></Redirect>}></Route>

#### antd组件中的留白组件WingBlank的取舍：

#### 如何手动分发一个原生事件：window.dispatchEvent(new Event('resize'));

#### 在脚手架工具中使用sass工具、安装yarn add node-sass即可

#### 经常遇到的额文字和图标基线不一致的问题：可以通过flex布局解决

  + display: flex;align-items: center;

#### h5中2的地理位置api，通过
  + navigator.gelocation.getcurrentpostion((position)=>console.log(position))
  + position表示当前的位置信息
  + 他会返回一个地理包含地理坐标和时间错的对象，我们从坐标中获取到我们的信息；
  + latitude和longitude分别表示经纬度
  + 定位的方式有wifi、gps、mac等，具体使用什么定位视设备而定
  + 实际开发过程中使用的是百度地图api、高德地图api

#### 具体使用方法在百度api中查看

  + 注意：在脚手架中相关的方法window.BMapGL.Map/Point是挂载在window上的，我们必须通过
  window来获取

#### 闪烁问题