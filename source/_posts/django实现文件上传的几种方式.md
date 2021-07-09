---
title: django实现文件上传的几种方式
date: 2019-11-18 23:37:31
tags:
  - python_hashlib模块
categories:
  - python
---

### django实现文件上传的几种方式

#### 方式一、通过Django的Form方式上传文件
  1. 前台html创建Form，根据格式要求完善form内标签属性；提交后台后，后台获取前台传来的数据进行处理：文件路径保存至数据库，文件内容保存至本地文件夹中；后台处理完成后刷新前台页面。
  2. 静态html页面设置：写一个form表单，三个关键标签：text、file、submit，分别存新的文件名、文件内容、提交按钮，由于是文件上传因此form中enctype需要设置成multipart/form-data。

  ```
  <form action="/upload.html" method="post" enctype="multipart/form-data">
    <input type="text" name="fileName">
    <input type="file" name="fileContent">
    <input type="submit" value="提交">
  </form>
  ```
  3、views后台设置：

  ```
  from django.shortcuts import render,redirect
import os
 
def Upload(request):
   if request.method=="GET":
      
        return render(request,"upload.html")
    elif request.method=="POST":
        # 获取普通input标签值，即文件名
        filname=request.POST.get('fileName')
        # 获取file类型的input标签值，即文件内容
        file=request.FILES.get('fileContent')
 
        # 获取文件后缀名
        postfix=file.name.split('.')[1]
        # 设置本地文件路径
        file_path=os.path.join('static',filname+'.'+postfix)
 
 
        # 将上传的文件写入本地目录
        f=open(file_path,"wb")
        for chunk in file.chunks():
            f.write(chunk)
        f.close()
 
 
        return redirect("upload.html")
  ```

  4. 配置文件

  ```
  from Upload import views
 
  urlpatterns = [
    
      path('upload.html',views.Upload),
  ]
  ```
  5. 原理：
    + 当Get请求时，即浏览器打开该网页时，显示上传页面；
    + 当POST请求时，即点击“提交”按钮时：
      - 获取界面传过来的新的文件名及文件内容。
      - 分块读取文件内容，并写入到本地目录。
  
  6. 页面上动态显示刚刚上传的图片，需做如下改造：

  ```
  新建数据库表，用来存储图片路径
  class image(models.Model):
    # 路径
    file_Path=models.CharField(max_length=32)

  在上传成功时将文件路径保存至数据库，即在views的视图方法的POST中的保存文件后面添加代码：

   models.image.objects.create(file_Path=file_path)

  页面刷新时展现图片，即在视图方法的GET中读取数据库中添加的图片路径，并将其返给html页面：
   if request.method=="GET":
        # 获取所有图片
        imgs=models.image.objects.all()
        return render(request,"upload.html",{"imgs":imgs})
    html页面中增加:
    <div class="imgs">
      { % for obj in imgs % }
          <img src="{{ obj.file_Path }}">
      { % endfor % }
    </div>
    由于Django对静态文件浏览的限制，需要在配置中添加：
    STATIC_URL = '/static/'
 
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR,"static"),
    ]

  ```


#### 方式二、通过ajax

  1. 前端处理：
  ```
  <input type="file" name="img" id="img1">
  <p><input type="button" value="上传图片" id="upload1"></p>
  ```
  2. 利用formdata表单数据对象添加数据：
  ```
  document.querySelector('#upload1').addEventListener('click',function(e){
        e.preventDefault()
        imgele =  document.querySelector('#img1')
        img = imgele.files[0]
        // console.log(img.name)
        var formdata = new FormData();
        formdata.append('myfiles',img)
        console.log(formdata)
        axios.post('http://localhost:8000/app/img/upload',formdata).then(function(result){
          console.log(result)
        }).catch(function(error){
          console.log(error)
        })
      })
  ```

  3. FormData对象的作用(formdata本质就是一个js对象)
  a、模拟HTML表单，相当于将HTML表单映射为表单对象，自动将表单对象中的数据拼接成请求参数的格式
  b、异步上传二进制文件（比如图片、视频和音频）
  FormData对象与ajax有关
  FormData对象不能用于get请求
  formData.get(key)
  formData.set(key,value)
  formData.delete(key)
  formData.append(key,value)
  var formData=new FormData()
  formData.append(key,value)
  4. django后端处理：

  ```
  def imgss(request):
  print('*' * 50)
  # print(request.POST.get('myfiles'))
  img = request.FILES['myfiles']
  print('*' * 50)
  with open("upload/imgs/"+img.name,'ab') as fp:
    for chunk in img.chunks():
            fp.write(chunk)
  return HttpResponse("is ok")
  ```

