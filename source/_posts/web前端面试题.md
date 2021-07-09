---
title: web前端面试题
date: 2021-03-14 21:01:12
tags:
  - vue 前端面试题
categories:
  - 前端杂项
---


### 一、原生js和Jquery

#### 1.面向对象和面向过程的区别

  1. js是面向过程还是面向过程？一直以来，顶级大师各有各的说法，这里不敢妄加评论。
  面向过程就是函数式编程，按照传统流程编写一个又一个的函数来解决需求的方式。
  面向过程适合一个人的项目，如果是团队合作，别人想修改你的代码就不利于维护了。所以下面着重聊聊面向对象。
  2. 面向对象就是讲你的需求抽象成一个对象，然后针对这个对象分析其属性和方法。
  面向对象的主要特点就是封装，继承，多态。

#### 2. this指向

  指向this所在的函数或者方法的调用者/拥有者;

#### 3. eval函数的用法

  将eval中的字符串作为表达式去使用；使用场景多在模块化规范中比如老版本的requirejs
  require将加载到的内容eval(content)去执行，
  计时器也支持eval，他会将其内容作为表达式去使用；
  window.setTimeout("alert(0)",5000)  计时器也支持eval，他会将其内容作为表达式去使用；
  在严格模式("use strict")下：eval("var a=1;alert(a+=111)")   eval有自己的作用域即当前字符串内

#### 4. 算法：函数防抖
  函数的防抖就是固定的时间内反复触发事件只执行下一个（即删除前一个事件），
  函数的防抖就是限制函数执行的速度/次数，比如滚动滚动条事件触发的次数非常多，但是我们并不想频繁执行
  事件处理函数，处理方式通常由：
  + 设置时间间隔，延迟指定时间执行，指定时间内执行我们return取消掉；
  + 每一次时间触发的时候我们把上一次事件取消掉；
  + 利用延迟器

#### 5. 算法：函数的节流
  函数的节流就是限制函数执行的频率，即单位时间内执行的频率固定（即不执行下一个事件）；
  处理逻辑就是让短时间内只执行指定次数的函数；

#### 6. 函数的柯里化 

  函数的柯里化：函数的多参变成单参 比如 

  ```
  function f(a) {
  return  function(b){
    return function(c){
      a+b+c
    }
  }
  }
  ```

  把f(1,2,3)  = 6 变成 f(1)(2)(3)

#### 7. 作用域链

  1. js中万物接对象，是对象就有一定的属性和方法；
  2. 如下中的func方法是全局变量是保存在window下的，那局部变量num和ff是保存在哪里呢
    就是保存在哪个作用域对象下的，实际上是作用域对象.ff()调用了我们的ff方法
  3. 所谓产生一个作用域就是创建了一个作用域对象，将来作用对象里的变量将保存在其作用域对象下：
  4. 每一个产生作用域就会创建一个作用域对象；
  5. 当我们需要访问我们变量的时候，就会在我们当前作用域对象下去找我们的变量，如果没有
    就会找上一级的作用域对象上去找我们的变量，直到找到顶层作用域对象window上，这个变量的查找
    链就是我们的作用域链；

  ```
  function func(){
    var mum = 1
    function ff(){
      console.log(mum)
    }
    ff()
  }
  func()
  ```


#### 8. 原型链的继承机制
  1. 默认的继承机制是prototype
  2. JavaScript 中没有类的概念的，主要通过原型链来实现继承。通常情况下，继承意味着复制操作，然而 JavaScript默认并不会复制对象的属性，相反，JavaScript只是在两个对象之间创建一个关联（原型对象指针），这样，一个对象就可以通过委托访问另一个对象的属性和函数，所以与其叫继承，委托的说法反而更准确些
  3. 当我们 new 了一个新的对象实例，明明什么都没有做，就直接可以访问 toString 、valueOf 等原生方法。那么这些方法是从哪里来的呢？答案就是原型
  ![1](https://img-blog.csdnimg.cn/20201029013612152.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzAyMTk4Mg==,size_16,color_FFFFFF,t_70#pic_center)
  4. 在控制台打印一个空对象时，我们可以看到，有很多方法，已经“初始化”挂载在内置的 proto 对象上了。这个内置的 proto 是一个指向原型对象的指针，它会在创建一个新的引用类型对象时（显示或者隐式）自动创建，并挂载到新实例上。当我们尝试访问实例对象上的某一属性 / 方法时，如果实例对象上有该属性 / 方法时，就返回实例属性 / 方法，如果没有，就去 proto 指向的原型对象上查找对应的属性 / 方法。这就是为什么我们尝试访问空对象的 toString 和 valueOf 等方法依旧能访问到的原因，JavaScript 正式以这种方式为基础来实现继承的。
  5. 构造函数: 如果说实例的 proto 只是一个指向原型对象的指针，那就说明在此之前原型对象就已经创建了，那么原型对象是什么时候被创建的呢？这就要引入构造函数的概念。其实构造函数也就只是一个普通的函数而已，如果这个函数可以使用 new 关键字来创建它的实例对象，那么我们就把这种函数称为 构造函数。
  6. 原型对象正是在构造函数被声明时一同创建的。构造函数被申明时，原型对象也一同完成创建，然后挂载到构造函数的 prototype 属性上：原型对象被创建时，会自动生成一个 constructor 属性，指向创建它的构造函数。这样它俩的关系就被紧密地关联起来了。
  细心的话，你可能会发现，原型对象也有自己的 proto ，这也不奇怪，毕竟万物皆对象嘛。原型对象的 proto 指向的是 Object.prototype。那么 Object.prototype.proto 存不存在呢？其实是不存在的，打印的话会发现是 null 。这也证明了 Object 是 JavaScript 中数据类型的起源
  7. 原型及构造函数的大概关系了，我们可以用一张图来表示这个关系
  ![2](https://img-blog.csdnimg.cn/20201029014306353.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NzAyMTk4Mg==,size_16,color_FFFFFF,t_70#pic_center)
  8. 原型链 说完了原型，就可以来说说原型链了，如果理解了原型机制，原型链就很好解释了。其实上面一张图上，那条被 proto 链接起来的链式关系，就称为原型链。原型链的作用：原型链如此的重要的原因就在于它决定了 JavaScript 中继承的实现方式。当我们访问一个属性时，查找机制如下：
    + 访问对象实例属性，有则返回，没有就通过 proto 去它的原型对象查找。
    + 原型对象找到即返回，找不到，继续通过原型对象的 proto 查找。
    + 一层一层一直找到 Object.prototype ，如果找到目标属性即返回，找不到就返回 undefined，不会再往下找，因为在往下找 proto 就是 null 了。
  9. 通过上面的解释，对于构造函数生成的实例，我们应该能了解它的原型对象了。JavaScript 中万物皆对象，那么构造函数肯定也是个对象，是对象就有 proto ，那么构造函数的 proto 是什么？ object
  ![3](https://img-blog.csdnimg.cn/20201029014520887.jpg#pic_center)

#### 9. js的垃圾回收机制
  0. js的垃圾回收器是定期扫描的；
  1. 即变量的回收
  2. 算法1就是引用计数法，给每个变量上做上标记，有人要用就标记加1，不停的加一
    当要用的人不用的时候标记就减一，js会不断扫描我们的内存检查我们的变量，缺点就是产生了很多
    碎片
  3. 算法2.就是复制整理法
  4. 

#### 10. 引起内存泄漏的常见方法

  1. 闭包，
  2. 计时器
  3. dom操作比如 let btn = $("#btn") ; btn.remove();
    此时dom元素还是存在内存中的； 

#### 11. 展开运算符(...)

#### 12. promise对象

  1. 普通用法：来实现异步操作：

  ```
  console.log(eval("var a=10000;a"))
    let pro = new Promise(function(res,rej){
      res(1)
    })
    pro.then(function(result){
      console.log(result)
    }).catch(function(res){
  ```
  2. 链式加载
  3. all方法获取到所有的返回值,且方法里的三个异步组是并列开跑的，all方法是所有的跑完了才执行then
    而Promise.race(promise对象1,promise对象2，promise对象3).then(function(r){alert(r)})
    race()方法有个特点，只要有一个promise对象完成，就会调用我们的then，也就是说，then只会拿到第一个执行
    完成的结果；

  ```
    function apromise(a=1){
      return new Promise(function(res,rej){
        res(a+1)
      })
    }
    console.log(eval("var a=1000110;a"))
    Promise.all([apromise(1),apromise(2),apromise(3)])
    .then(function(list){
      console.log(list)
    })
  ```


#### 13. 数组的方法：map，flatmap，reduce方法

  1. map用于处理我们数组返回一个新数组,返回的数组长度和原长度一样，
  2. 但是flatmap方法会将返回的对象比如元组扁平化，原返回[[1,2,3],[1,2,3],3]会将扁平化
  为[1,2,3,1,2,3,3]

  ```
  let arr = [1,2,3]
    new_arr =  arr.map(function(r,b,c){
      console.log(r,b,c)
      return [r,r**r,r**r*r]
  })
  ```

  3.reduce函数常用于累积计算
  注意它会遍历数组length(all) -1次，因为第一次会拿到两个数组中的值，
  后面遍历将会只拿一个参数，其回调函数的第一个参数是上一次返回的值
  ```
  let arr = [1,2,3,4,5,6,7]
    nall =  arr.reduce(function(a,b,c){
      console.log(a,b,c)
      return a+b
    })
    console.log(nall)
  ```


#### 14. 浅拷贝和深拷贝

  1. 浅拷贝，只拷贝对象的第一层，深层次的对象的属性和方法拷贝其引用
  2. 方法： 遍历、

  ```
  var obj = {a:1,b:2,c:{d:10,e:30}}

    var obj1 = {}

    var obj2 = {}

    for (var item in obj){
      obj1[item] = obj[item]
    }
    Object.assign(obj2,obj)
    obj.a = 2
    obj.c.d=11111
    console.log(obj)
    console.log(obj1)
    console.log(obj2)

  ```

  2. 浅拷贝、深拷贝的方法  使用json序列化对象处理我们的对象获取一个新完全的对象(深拷贝)

    1. 但是这样存在一些问题，比如一些set,get等方法，或者后面动态添加的方法丢失，也不是完全的拷贝

      ```
      var obj = {a:1,b:2,c:{d:10,e:30}}

      var obj1 = {}

      var obj2 = {}
      var obj3 = JSON.parse(JSON.stringify(obj))

      ```


    2. 深拷贝的一些方法

    ```
    function clone(target,source){
      // 获取对象的所有的属性，包括不可遍历的属性
      var names = Object.getOwnPropertyNames(source);
      for (var i=0;i<names.length;i++ ){
        var desc = Object.getOwnPropertyDescriptor(source,names[i])
        if(typeof(desc.value) == "object" & desc.value != null){
          var obj;
          if (Array.isArray(desc.value)) {
            obj = []
          } else {
            obj = {}
          }
          Object.defineProperty(target.names[i],{
            configurable:desc.configurable,
            enumerable:desc.enumerable,
            value:obj.value,
            writable:desc.writable
          });
          clone(obj,desc.value)
        } else {
          Object.defineProperty(target.names[i],{
            // configurable 属性表示不可删除、
            configurable:desc.configurable,
            // enumerable 表示是否可以遍历
            enumerable:desc.enumerable,
            // 值
            value:desc.value,
            // 是否可写
            writable:desc.writable
          });
        }
      }
    }
    ```


#### 15. 了解一下es6的代理Proxy

  1. 代理使得一个对象拥有了相关的属性和方法：

  2. 用法：var p = new Proxy(obj,handler)使得我们的obj对象拥有了handler的一些属性和方法；

  3. 简单来说 代理就是给我们的被代理对象添加一些属性和方法，但是是作用在代理实例化对象上，而不是原被代理对象上


  ```
    let obj = {a:1,b:2}

    let handler = {
      set:function(target,key,value){
        target[key] = value
      },
      get:function(target,key){
        return target[key]
      },
      has:function(target,key){
        return key in target;
      }
    }

    var p = new Proxy(obj,handler)
    p.a = 20;
    console.log(obj.p)

  ```



####  16. 两个加载完成事件的区别

  1. $(window).load()和$(window).ready()两个加载完成事件的区别

  2. document文档加载的顺序：
    + html结构加载
    + 加载外部样式表和执行文件
    + 加载并解析执行脚本代码
    + 构造dom模型（样式应用）此时ready事件触发
    + 加载图片文件等等资源
    + 再页面加载完成 此时load事件触发

  3. ready不必等图片媒体进来之前就可以运行代码了，否则load需要等到全部完成后执行；

#### 17. 事件循环Eventloop

  1. js任务分为同步任务和异步任务，异步任务主要有宏任务，微任务
  2. js


###  二、nodejs和项目工程化

#### 1. 使用nodejs编写代码实现遍历文件夹及所有文件名

#### 2. node如何做版本的升级？为什么要做nvm

#### 3. 模块化差异，AMD,CMD,Commonjs,Esmodule,

#### 4. 图片上传到服务器的过程(filereader，readAsDataURL)

#### 5.  token如何存在localstorage里面，过期怎么处理

#### 6.  node框架中的mvc

#### 7. mongodb和mysql的优势

#### 8. less(js) ,sass(ruby),stylus,css,命名空间与css module

#### 9.  工程化上的按需加载

#### 10. git上的冲突怎么解决

#### 11. 设计模式

#### 12. node中的npm版本管理，package.lock和yarn.lock

#### 13. Webpack

#### 14. 后端的环境的搭建

#### 15. typescript

###  二、Vue相关

#### axios是什么，如何实现登录功能的

  1. 通过axios的请求拦截器interceptors完成request、response拦截

#### vuex统一状态管理

  

  