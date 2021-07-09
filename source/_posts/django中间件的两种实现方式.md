---
title: django中间件的两种实现方式
date: 2019-03-19 12:53:46
tags:
  - python_django_middleware
categories:
  - python
---

### 中间件的两种实现方式

  1. 什么是中间件：
  Django中的中间件是一个轻量级、底层的插件系统，可以介入Django的请求和响应处理过程，修改Django的输入或输出。中间件的设计为开发者提供了一种无侵入式的开发方式，增加了Django框架的健壮性。
  我们可以使用中间件，在Django处理视图的不同阶段对输入或输出进行干预。

  ![middlewareas](https://img-blog.csdnimg.cn/20190413162356225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nhcmw5NTI3MQ==,size_16,color_FFFFFF,t_70)

  1.如何定义我们的中间件：

####  1. 通过闭包函数定义我们的中间件

  ```
  def simple_middleware(get_response):
      # 此处编写的代码仅在Django第一次配置和初始化的时候执行一次。
  
      def middleware(request):
          # 此处编写的代码会在每个请求处理视图前被调用。
  
          response = get_response(request)
  
          # 此处编写的代码会在每个请求处理视图之后被调用。
  
          return response
  
      return middleware
  ```

  根据代码：需要定义一个中间件的工厂函数 simple_middleware，然后返回一个可以被调用的中间件 middleware
  中间件工厂函数 simple_middleware 需要接收一个可以调用的 get_response 对象
  返回的中间件也是一个可以被调用的对象，并且像视图一样需要接收一个request对象参数，返回一个response对象。
  例如，在users应用模块中新建一个middleware.py文件

  ```
  def my_middleware(get_response):
    print('init 被调用')
    def middleware(request):
        print('before request 被调用')
        response = get_response(request)
        print('after response 被调用')
        return response
    return middleware

  ```

  定义好中间件后，需要在settings.py 文件中添加注册中间件

  ```
  MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'users.middleware.my_middleware',  # 添加中间件
  ]
  ```

  #### 1. 通过闭包函数定义我们的中间件

  ```
  lass SimpleMiddleware:
      def __init__(self, get_response):
          self.get_response = get_response
          # One-time configuration and initialization.

      def __call__(self, request):
          
          # 视图函数请求之前做一些事情

          response = self.get_response(request)

          # 视图函数响应之后做一些事情
          return response
  ```