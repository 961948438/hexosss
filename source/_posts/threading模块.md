---
title: python的多任务线程
date: 2019-03-13 23:39:46
tags:
  - python_threading模块
categories:
  - python
---
###  1. 线程的理论知识
  1. 什么是线程一条流水线的工作流程.进程: 在内存中开启一个进程空间,然后将主进程的所有的资源数据复制一份,然后调用cpu去执行这些代码.
  之前的描述不够具体:开启一个进程:在内存中开启一个进程空间,然后将主进程的所有的资源数据复制一份,然后调用线程去执行代码进程是资源单位, 线程是执行单位.以后你描述开启一个进程:开启一个进程:进程会在内存中开辟一个进程空间,将主进程的资料数据全部复制一份,线程会执行里面的代码.

  2. 线程vs进程
    1. 开启进程的开销非常大,比开启线程的开销大很多
    2. 开启线程的速度非常快要快几十倍到上百倍
    3. 线程线程之间可以共享数据,进程与进程之间需借助队列等方法实现通信.
  3. 线程的应用
    并发: 一个cpu 看起来像是同时执行多个任务.单个进程开启三个线程.并发的执行任务.开启三个进程并发的执行任务.文本编辑器:1. 输入文字.2. 在屏幕上显示.3. 保存在磁盘中.开启多线程就非常好了:数据共享, 开销小,速度快.主线程子线程没有地位之分,但是,一个进程谁在干活?一个主线程在干活,当干完活了,你得等待其他线程干完活之后,才能结束本进程.

### 2. 开启线程的两种方式
  ```
  # 多进程:

  from threading import Thread
  # from multiprocessing import Process
  # import os
  #
  # def work():
  #     print('hello')
  #
  # if __name__ == '__main__':
  #     #在主进程下开启线程
  #     t=Process(target=work)
  #     t.start()
  #     print('主线程/主进程')




  # 多线程
  # from threading import Thread
  # import time
  #
  # def task(name):
  #     print(f'{name} is running')
  #     time.sleep(1)
  #     print(f'{name} is gone')
  #
  #
  #
  # if __name__ == '__main__':
  #
  #     t1 = Thread(target=task,args=('海狗',))
  #     t1.start()
  #     print('===主线程')  # 线程是没有主次之分的.

  ```

### 3. 线程vs进程的代码对比

  1. 开启速度对比
  2. 对比pid
  3. 同一个进程内线程共享内部数据

  ```
  # 多进程:

  from threading import Thread
  # from multiprocessing import Process
  # import os
  #
  # def work():
  #     print('hello')
  #
  # if __name__ == '__main__':
  #     #在主进程下开启线程
  #     t=Process(target=work)
  #     t.start()
  #     print('主线程/主进程')




  # 多线程
  # from threading import Thread
  # import time
  #
  # def task(name):
  #     print(f'{name} is running')
  #     time.sleep(1)
  #     print(f'{name} is gone')
  #
  #
  #
  # if __name__ == '__main__':
  #
  #     t1 = Thread(target=task,args=('海狗',))
  #     t1.start()
  #     print('===主线程')  # 线程是没有主次之分的.
  ```

  ```
  # from multiprocessing import Process
  # import time
  # import os
  # def task(name):
  #     print(f'子进程: {os.getpid()}')
  #     print(f'主进程: {os.getppid()}')
  #
  #
  # if __name__ == '__main__':
  #
  #     p1 = Process(target=task,args=('常鑫',))  # 创建一个进程对象
  #     p2 = Process(target=task,args=('常鑫',))  # 创建一个进程对象
  #     p1.start()
  #     p2.start()
  #     print(f'==主{os.getpid()}')



  # 线程:



  from threading import Thread
  import os

  def task():
      print(os.getpid())

  if __name__ == '__main__':

      t1 = Thread(target=task)
      t2 = Thread(target=task)
      t1.start()
      t2.start()
      print(f'===主线程{os.getpid()}')
  ```

  ```
  from threading import Thread
  import os

  x = 3
  def task():
      global x
      x = 100

  if __name__ == '__main__':

      t1 = Thread(target=task)
      t1.start()
      t1.join()
      print(f'===主线程{x}')

  # 同一进程内的资源数据对于这个进程的多个线程来说是共享的.
  ```


### 4. 线程的相关其他方法(了解)
  1. currentThread方法用于获取当前线程对象
  2. enumerate方法用于枚举当前进程的所有线程对象
  3. activeCount方法用法返回当前正在运行的线程数量
  4. 线程对象下的isAlive()方法用于判断线程是否还或者
  5. 线程对象下的getName()方法用于返回当前线程的名字
  6. 线程对象下的setName('子线程-1')方法或者name属性用于设置当前线程名
  7. os.getpid()方法返回进程id且一个进程的所有线程是在同一进程id下的
  
  ```

  from threading import Thread
  from threading import currentThread
  from threading import enumerate
  from threading import activeCount
  import os
  import time

  x = 3
  def task():
      # print(currentThread())
      time.sleep(1)
      print('666')
  print(123)
  if __name__ == '__main__':

      t1 = Thread(target=task,name='线程1')
      t2 = Thread(target=task,name='线程2')
      # name 设置线程名
      t1.start()
      t2.start()
      # time.sleep(2)
      # print(t1.isAlive())  # 判断线程是否活着
      # print(t1.getName())  # 获取线程名
      # t1.setName('子线程-1')
      # print(t1.name)  # 获取线程名  ***

      # threading方法
      # print(currentThread())  # 获取当前线程的对象
      # print(enumerate())  # 返回一个列表,包含所有的线程对象
      print(activeCount())  # ***
      print(f'===主线程{os.getpid()}')
  ```


### 5. 守护线程(考点)

  ```
  # join: 阻塞 告知主线程要等待我子线程执行完毕之后再执行主线程

  # from threading import Thread
  # import time
  #
  # def task(name):
  #     print(f'{name} is running')
  #     time.sleep(1)
  #     print(f'{name} is gone')
  #
  #
  #
  # if __name__ == '__main__':
  #     start_time = time.time()
  #     t1 = Thread(target=task,args=('海狗',))
  #     t2 = Thread(target=task,args=('海狗1',))
  #     t3 = Thread(target=task,args=('海狗2',))
  #
  #     t1.start()
  #     t1.join()
  #     t2.start()
  #     t2.join()
  #     t3.start()
  #     t3.join()
  #
  #     print(f'===主线程{time.time() - start_time}')  # 线程是没有主次之分的.


  # 守护线程

  # 回忆一下守护进程

  # from multiprocessing import Process
  # import time
  #
  #
  # def foo():
  #     print(123)
  #     time.sleep(1)
  #     print("end123")
  #
  #
  # def bar():
  #     print(456)
  #     time.sleep(2)
  #     print("end456")
  #
  # if __name__ == '__main__':
  #
  #     p1 = Process(target=foo,)
  #     p2 = Process(target=bar,)
  #
  #     p1.daemon = True
  #     p1.start()
  #     p2.start()
  #     print('====主')



  # 守护线程

  # from threading import Thread
  # import time
  #
  #
  # def sayhi(name):
  #     print('你滚!')
  #     time.sleep(2)
  #     print('%s say hello' %name)
  #
  # if __name__ == '__main__':
  #     t = Thread(target=sayhi,args=('egon',))
  #     # t.setDaemon(True) #必须在t.start()之前设置
  #     t.daemon = True
  #     t.start()  # 线程的开启速度要跟进程开很多
  #
  #     print('主线程')



  # from threading import Thread
  # import time
  #
  # def foo():
  #     print(123)  # 1
  #     time.sleep(1)
  #     print("end123")  # 4
  #
  # def bar():
  #     print(456)  # 2
  #     time.sleep(3)
  #     print("end456")  # 5
  #
  #
  # t1=Thread(target=foo)
  # t2=Thread(target=bar)
  #
  # t1.daemon=True
  # t1.start()
  # t2.start()
  # print("main-------")  # 3


  # 主线程什么时候结束???
  # 守护线程 等待非守护子线程以及主线程结束之后,结束.
  # from threading import Thread
  # import time
  #
  # def foo():
  #     print(123)  # 1
  #     time.sleep(3)
  #     print("end123")  # 4
  #
  # def bar():
  #     print(456)  # 2
  #     time.sleep(1)
  #     print("end456")  # 5
  #
  #
  # t1=Thread(target=foo)
  # t2=Thread(target=bar)
  #
  # t1.daemon=True
  # t1.start()
  # t2.start()
  # print("main-------")  # 3
  '''
  123
  456
  main-------
  end123
  end456


  123
  456
  main
  end456

  main
  456
  end456

  456
  123
  main
  end456


  '''


  # from threading import Thread
  # import time
  #
  # def foo():
  #     print(123)
  #     time.sleep(3)
  #     print("end123")
  #
  # def bar():
  #     print(456)
  #     time.sleep(1)
  #     print("end456")
  #
  #
  # t1=Thread(target=foo)
  # t2=Thread(target=bar)
  #
  # t1.daemon=True
  # t1.start()
  # t2.start()
  # print("main-------")
  ```

### 6. 互斥锁(考点)

  ```
  # from threading import Thread
  # import time
  # import random
  # x = 100
  #
  # def task():
  #     time.sleep(random.randint(1,2))
  #     global x
  #     temp = x
  #     time.sleep(random.randint(1, 3))
  #     temp = temp - 1
  #     x = temp
  #
  #
  # if __name__ == '__main__':
  #     l1 = []
  #     for i in range(100):
  #         t = Thread(target=task)
  #         l1.append(t)
  #         t.start()
  #
  #     for i in l1:
  #         i.join()
  #     print(f'主线程{x}')

  # 多个任务公抢一个数据,保证数据的安全的目的,要让其串行


  from threading import Thread
  from threading import Lock
  import time
  import random
  x = 100

  def task(lock):

      lock.acquire()
      # time.sleep(random.randint(1,2))
      global x
      temp = x
      time.sleep(0.01)
      temp = temp - 1
      x = temp
      lock.release()


  if __name__ == '__main__':
      mutex = Lock()
      l1 = []
      for i in range(100):
          t = Thread(target=task,args=(mutex,))
          l1.append(t)
          t.start()

      time.sleep(3)
      print(f'主线程{x}')
  ```

### 7. 死锁现象与递归锁

  1. 死锁现象产生的原因：
    1线程拿着a锁想要b锁，
    2线程拿着b锁想要a锁，
    两个线程都在等着对象解锁，导致都无法拿到想要的锁而出现死锁
    在以后的任务中锁越多，出现死锁的可能越大
  
  2. 递归锁可以解决死锁现象，其内部做了如下处理,使用同一把锁
    递归锁有一个计数的功能, 原数字为0,上一次锁,计数+1,释放一次锁,计数-1,
    只要递归锁上面的数字不为零,其他线程就不能抢锁.

  ```
  # from threading import Thread
  # from threading import Lock
  # import time
  #
  # lock_A = Lock()
  # lock_B = Lock()
  #
  #
  # class MyThread(Thread):
  #
  #     def run(self):
  #         self.f1()
  #         self.f2()
  #
  #
  #     def f1(self):
  #
  #         lock_A.acquire()
  #         print(f'{self.name}拿到了A锁')
  #
  #         lock_B.acquire()
  #         print(f'{self.name}拿到了B锁')
  #
  #         lock_B.release()
  #
  #         lock_A.release()
  #
  #     def f2(self):
  #
  #         lock_B.acquire()
  #         print(f'{self.name}拿到了B锁')
  #
  #         time.sleep(0.1)
  #         lock_A.acquire()
  #         print(f'{self.name}拿到了A锁')
  #
  #         lock_A.release()
  #
  #         lock_B.release()
  #
  #
  #
  # if __name__ == '__main__':
  #
  #     for i in range(3):
  #         t = MyThread()
  #         t.start()



  from threading import Thread
  from threading import RLock
  import time

  lock_A = lock_B = RLock()

  # 递归锁有一个计数的功能, 原数字为0,上一次锁,计数+1,释放一次锁,计数-1,
  # 只要递归锁上面的数字不为零,其他线程就不能抢锁.
  class MyThread(Thread):

      def run(self):
          self.f1()
          self.f2()


      def f1(self):

          lock_A.acquire()
          print(f'{self.name}拿到了A锁')

          lock_B.acquire()
          print(f'{self.name}拿到了B锁')

          lock_B.release()

          lock_A.release()

      def f2(self):

          lock_B.acquire()
          print(f'{self.name}拿到了B锁')

          time.sleep(0.1)
          lock_A.acquire()
          print(f'{self.name}拿到了A锁')

          lock_A.release()

          lock_B.release()



  if __name__ == '__main__':

      for i in range(3):
          t = MyThread()
          t.start()
  ```


### 8. 信号量
  1. 也是一种锁, 控制并发数量
  ```
  from threading import Thread, Semaphore, current_thread
  import time
  import random
  sem = Semaphore(5)

  def task():
      sem.acquire()

      print(f'{current_thread().name} 厕所ing')
      time.sleep(random.randint(1,3))

      sem.release()


  if __name__ == '__main__':
      for i in range(20):
          t = Thread(target=task,)
          t.start()
  ```


### 9. IO计算密集型验证
  1. 对于IO密集型: 单个进程的多线程的并发效率高.

  ```
  from threading import Thread
  from multiprocessing import Process
  import time
  import random
  # # 计算密集型: 单个进程的多线程并发 vs 多个进程的并发并行
  #
  # def task():
  #     count = 0
  #     for i in range(10000000):
  #         count += 1
  #
  #
  # if __name__ == '__main__':
  #
  #     # 多进程的并发,并行
  #     # start_time = time.time()
  #     # l1 = []
  #     # for i in range(4):
  #     #     p = Process(target=task,)
  #     #     l1.append(p)
  #     #     p.start()
  #     #
  #     # for p in l1:
  #     #     p.join()
  #     #
  #     # print(f'执行效率:{time.time()- start_time}')  # 3.1402080059051514
  #
  #     # 多线程的并发
  #     # start_time = time.time()
  #     # l1 = []
  #     # for i in range(4):
  #     #     p = Thread(target=task,)
  #     #     l1.append(p)
  #     #     p.start()
  #     #
  #     # for p in l1:
  #     #     p.join()
  #     #
  #     # print(f'执行效率:{time.time()- start_time}')  # 4.5913777351379395

  # 总结: 计算密集型: 多进程的并发并行效率高.


  # IO密集型: 单个进程的多线程并发 vs 多个进程的并发并行

  def task():
      count = 0
      time.sleep(random.randint(1,3))
      count += 1

  # if __name__ == '__main__':

  # 多进程的并发,并行
  #     start_time = time.time()
  #     l1 = []
  #     for i in range(50):
  #         p = Process(target=task,)
  #         l1.append(p)
  #         p.start()
  #
  #     for p in l1:
  #         p.join()
  #
  #     print(f'执行效率:{time.time()- start_time}')  #  8.000000000

  # 多线程的并发
  #     start_time = time.time()
  #     l1 = []
  #     for i in range(50):
  #         p = Thread(target=task,)
  #         l1.append(p)
  #         p.start()
  #
  #     for p in l1:
  #         p.join()
  #
  #     print(f'执行效率:{time.time()- start_time}')  # 3.0294392108917236

  # 对于IO密集型: 单个进程的多线程的并发效率高.
  ```


### 10. GIL全局解释器锁
  1. 前言：GIL的知识大多都是在面试的时候才会用到，但是抱着学习(或者说更好的与面试官扯皮)的心态，怎么也要了解的深入一些
  2. GIL全局解释器锁说明：GIL全局解释器锁是CPython解释器独有的锁，目的就是牺牲效率保证数据安全。
  一直有CPython的并发不太行的说法，是真的呢？还是一些大V危言耸听？吸引眼球还是确有其事？接下来就跟你详细剖析一些GIL全局解释器锁
  3. 通过命令执行python文件的流程：操作系统在内存中开辟一个进程空间，将你的Python解析器以及py文件加载进去，解释器运行py文件。我们可以把Python解释器看做一个函数，你的py代码就是一堆代码，相当于一个实参，然后将这个实参传入函数中(Python解释器)执行。此时我们所说的Python解释器就是CPython解释器。

  你的Python解释器细分为三部分:

    + 先将你的代码通过编译器编译成C的字节码
    + 然后给到虚拟机将字节码输出为机器码
    + 最后配合操作系统把你的这个机器码扔给CPU去执行
    ![python解释器流程](https://img-blog.csdnimg.cn/20200613103857562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTE1MzE4,size_16,color_FFFFFF,t_70)
    你的py文件中有一个主线程(红色箭头代表的就是主线程)，主线程配合操作系统执行了整个过程。我们知道一个进程可以开启多个线程执行任务，那就意味着：理论上来说，一个进程的多个线程可以利用多核并行(不是并发)的处理任务。三个线程给Python三个CPU去并行的执行，最大限度提高效率。
    ![python解释器流程](https://img-blog.csdnimg.cn/20200613104201378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTE1MzE4,size_16,color_FFFFFF,t_70)
    but！ 这个只是理论上来说，实际上Python的单个进程的多线程是无法应用多核的，因为写Cpython源码的大佬程序员给进入解释器的线程加了一把锁，也就是我们常说的GIL锁。
    ![python解释器流程](https://img-blog.csdnimg.cn/2020061310441266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTE1MzE4,size_16,color_FFFFFF,t_70)
  4. 为什么CPython解析器要加这把锁呢？
    + 因为在Python刚刚研发出来的时候是单核的时代，而且CPU价格非常的昂贵，Python起初作为一种脚本语言，面临的需求单核解决足以
    + 如果不加这把GIL锁，那么同一时刻进入CPython解释器线程数量不固定，我们要保证CPython解释器的数据资源安全，就需要在源码内部主动加入大量的互斥锁(Lock)来保证数据安全性，这样非常麻烦并且会降低CPython源码的开发速度
  5. 那现在为什么不将这把锁去掉呢？
  CPython解释器内部的管理以及业务逻辑全部是围绕单线程实现的，并且从⻳叔创建CPython到现在，CPython源码已经更新迭代⻢上到4版本了，源码内容体量庞⼤，如果你要去掉，这个⼯程量⽆异于重新构建python，是⽐登天还难。
    + Cpython解释器是官⽅推荐的解释器，处理速度快，功能强⼤
    + JPython就是编译成Java识别的字节码，没有GIL锁
    + Pypy属于动态编译型，规则和漏洞很多，现在还在测试阶段（未来可能会成为主流）没有GIL锁
  只有Cpython解释器有GIL锁，其他类型的解释器以及其他语⾔都没有

  由此可以看出，CPython并发不太行是确有其事的，但是也不能因此否定Python，虽然说Python的单个进程的多线程不能利用多核并行处理脚本，但是并不是说Python不能并行，是单个进程下的多线程不能并行，如果你要开启多个进程是可以利用多核的。那么继续发问了，多个进程不是开销⾮常⼤么？这不是影响性能么？其实这个只是相对的，⼗⼏个进程对于我们现在⽤的
  平常的电脑是不成问题的，何况企业级服务器呢？

  其实，即使说单个进程的多个线程不能并行。也不会影响很多的。不能并行那就并发吧，这就要针对计算密集型还是IO密集型分情况讨论了

  IO密集型：

  ![IO密集型](https://img-blog.csdnimg.cn/20200613105821472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTE1MzE4,size_16,color_FFFFFF,t_70)
  操作系统可以操控着CPU遇到IO就将CPU强⾏的切执⾏另⼀个任务，⽽这个任务遇到IO阻塞了，⻢上⼜会切换，所以IO密集型利⽤单个进程的多线程并发是最好的解决⽅式（后⾯还会有协程也⾮常好⽤）。
  ![计算密集型](https://img-blog.csdnimg.cn/20200613110000538.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5OTE1MzE4,size_16,color_FFFFFF,t_70)
  多个任务都是纯计算都没有IO阻塞，那么此时应该利⽤多进程并⾏的处理任务。
  5. 总结：
    + GIL全局解释器锁只存在Cpython解释器中，他是给进⼊解释器的线程上锁：
      - 优点：便于Cpython解释器的内部资源管理，保证了Cpython解释器的数据安全。
      - 缺点：单个进程的多线程不能利⽤多核。
      - 注意：GIL全局解释器锁并不是让Cpython不能利⽤多核，多进程是可以利⽤多核的，况且IO密集型的任务，单个进程的多线程并发处理⾜以。
    + IO密集型：单个进程的多线程并发处理
    + 计算密集型：多个进程并⾏处理
  6. GIL全局解释器锁与互斥锁的关系(补充)
  Python已经有⼀个GIL来保证同⼀时间只能有⼀个线程来执⾏了，为什么这⾥还需要Lock?

  ⾸先我们需要达成共识：锁的⽬的是为了保护共享的数据，同⼀时间只能有⼀个线程来修改共享的数据
  然后，我们可以得出结论：
  保护不同的数据就应该加不同的锁。

  最后，问题就很明朗了，GIL 与Lock是两把锁，保护的数据不⼀样，前者是解释器级别的(当然保护
  的就是解释器级别的数据，⽐如垃圾回收的数据)，后者是保护⽤户⾃⼰开发的应⽤程序的数据，很明
  显GIL不负责这件事，只能⽤户⾃定义加锁处理，即Lock。
  > 过程分析：所有线程抢的是GIL锁，或者说所有线程抢的是执行权限
  线程1抢到GIL锁，拿到执行权限，开始执行，然后加了一把Lock，还没有执行完毕，即线程1还未释放Lock，有可能线程2抢到GIL锁，开始执行，执行过程中发现Lock还没有被线程1释放，于是线程2进入阻塞，被夺走执行权限，有可能线程1拿到GIL，然后正常执行到释放Lock。。。这就导致了串行运行的效果
  　　既然是串行，那我们执行
  　　t1.start()
  　　t1.join
  　　t2.start()
  　　t2.join()
  　　这也是串行执行啊，为何还要加Lock呢，需知join是等待t1所有的代码执行完，相当于锁住了t1的所有代 码，而Lock只是锁住一部分操作共享数据的代码。！！！

  join是等待所有的代码执行完之后，而加锁只是锁上了一部分操作共享数据的代码，更加灵活，看不同的场景用来应用join还是lock

  因为Python解释器帮你自动定期进行内存回收，你可以理解为python解释器里有一个独立的线程，每过一段时间它起wake up做一次全局轮询看看哪些内存数据是可以被清空的，此时你自己的程序 里的线程和py解释器自己的线程是并发运行的，假设你的线程删除了一个变量，py解释器的垃圾回收线程在清空这个变量的过程中的clearing时刻，可能一个其它线程正好又重新给这个还没来及得清空的内存空间赋值了，结果就有可能新赋值的数据被删除了，为了解决类似的问题，python解释器简单粗暴的加了锁，即当一个线程运行时，其它人都不能动，这样就解决了上述的问题， 这可以说是Python早期版本的遗留问题。

### 11. 线程池,进程池
  1. 线程池: 一个容器,这个容器限制住你开启线程的数量,比如4个,
  第一次肯定只能并发的处理4个任务,只要有任务完成,
  线程马上就会接下一个任务.以时间换空间

  ```
  from concurrent.futures import ProcessPoolExecutor, ThreadPoolExecutor
  import os
  import time
  import random

  # print(os.cpu_count())
  def task(n):
      print(f'{os.getpid()} 接客')
      time.sleep(random.randint(1,3))


  if __name__ == '__main__':

      # 开启进程池  (并行(并行+并发))
      # p = ProcessPoolExecutor()  # 默认不写,进程池里面的进程数与cpu个数相等
      #
      # # p.submit(task,1)
      # # p.submit(task,1)
      # # p.submit(task,1)
      # # p.submit(task,1)
      # # p.submit(task,1)
      # # p.submit(task,1)
      # # p.submit(task,1)
      # for i in range(20):
      #     p.submit(task,i)
      #
      # 开启线程池  (并发)
      t = ThreadPoolExecutor()  # 默认不写, cpu个数*5 线程数
      # t = ThreadPoolExecutor(100)  # 100个线程

      for i in range(20):
          t.submit(task,i)
  ```


### 12. 阻塞,非阻塞,同步,异步

  1. 阻塞,非阻塞,同步,异步进程运行的三个状态: 运行,就绪,阻塞.
    + 执行的角度:
        - 阻塞: 程序运行时,遇到了IO,程序挂起,cpu被切走.
        - 非阻塞: 程序没有遇到IO,程序遇到IO但是我通过某种手段,让cpu强行运行我的程序.
    + 提交任务的角度:
        - 同步: 提交一个任务,自任务开始运行直到此任务结束(可能有IO),返回一个返回值之后,我在提交下一个任务.
        - 异步: 一次提交多个任务,然后我就直接执行下一行代码.
    + 返回结果如何回收?给三个老师发布任务:
        - 同步: 先告知第一个老师完成写书的任务,我从原地等待,等他两天之后完成了,告诉完事了,我在发布下一个任务......
        - 异步:直接将三个任务告知三个老师,我就忙我的我,直到三个老师完成之后,告知我.

  2. 同步调用,异步调用
  ```
  # 1. 异步调用
  # 异步调用返回值如何接收? 未解决.
    from concurrent.futures import ProcessPoolExecutor,ThreadPoolExecutor
    import time
    import random
    import os

    def task(i):
        print(f'{os.getpid()}开始任务')
        time.sleep(random.randint(1,3))
        print(f'{os.getpid()}任务结束')
        return i
    if __name__ == '__main__':

        # 异步调用
        pool = ProcessPoolExecutor()
        for i in range(10):
            pool.submit(task,i)

        pool.shutdown(wait=True)
        # shutdown: 让我的主进程等待进程池中所有的子进程都结束任务之后,在执行. 有点类似与join.
        # shutdown: 在上一个进程池没有完成所有的任务之前,不允许添加新的任务.
        # 一个任务是通过一个函数实现的,任务完成了他的返回值就是函数的返回值.
        print('===主')


    # 2. 同步调用
    from concurrent.futures import ProcessPoolExecutor,ThreadPoolExecutor
    import time
    import random
    import os

    def task(i):
        print(f'{os.getpid()}开始任务')
        time.sleep(random.randint(1,3))
        print(f'{os.getpid()}任务结束')
        return i
    if __name__ == '__main__':

        # 同步调用
        pool = ProcessPoolExecutor()
        for i in range(10):
            obj = pool.submit(task,i)
            # obj是一个动态对象,返回的当前的对象的状态,有可能运行中,可能(就绪阻塞),还可能是结束了.
            # obj.result() 必须等到这个任务完成后,返回了结果之后,在执行下一个任务.
            print(f'任务结果:{obj.result()}')

        pool.shutdown(wait=True)
        # shutdown: 让我的主进程等待进程池中所有的子进程都结束任务之后,在执行. 有点类似与join.
        # shutdown: 在上一个进程池没有完成所有的任务之前,不允许添加新的任务.
        # 一个任务是通过一个函数实现的,任务完成了他的返回值就是函数的返回值.
        print('===主')


    # 3 异步如何取结果???
    # 方式一: 异步调用,统一回收结果.

    # from concurrent.futures import ProcessPoolExecutor,ThreadPoolExecutor
    # import time
    # import random
    # import os
    #
    # def task(i):
    #     print(f'{os.getpid()}开始任务')
    #     time.sleep(random.randint(1,3))
    #     print(f'{os.getpid()}任务结束')
    #     return i
    #
    # if __name__ == '__main__':
    #
    #     # 异步调用
    #     pool = ProcessPoolExecutor()
    #     l1 = []
    #     for i in range(10):
    #         obj = pool.submit(task,i)
    #         l1.append(obj)
    #
    #     pool.shutdown(wait=True)
    #     print(l1)
    #     for i in l1:
    #         print(i.result())
    #     print('===主')
    # 统一回收结果: 我不能马上收到任何一个已经完成的任务的返回值,我只能等到所有的任务全部结束统一回收.

    # 第二种方式:
  from concurrent.futures import ProcessPoolExecutor,ThreadPoolExecutor
  import time
  import random
  import os

  def task(i):
      print(f'{os.getpid()}开始任务')
      time.sleep(random.randint(1,3))
      print(f'{os.getpid()}任务结束')
      return i

  if __name__ == '__main__':

      # 异步调用
      pool = ProcessPoolExecutor()
      l1 = []
      for i in range(10):
          obj = pool.submit(task,i)
          l1.append(obj)

      pool.shutdown(wait=True)
      print(l1)
      for i in l1:
          print(i.result())
      print('===主')

    # 打印一下结果就行了
    
  ```
  3. 异步调用+回调函数
  ```
    # 浏览器工作原理, 向服务端发送一个请求,服务端验证你的请求,如果正确,给你的浏览器返回一个文件,
  # 浏览器接收到文件,将文件里面的代码渲染成你看到的漂亮美丽的模样.

  # 什么叫爬虫?
  # 1. 利用代码模拟一个浏览器,进行浏览器的工作流程得到一堆源代码.
  # 2. 对源代码进行数据清洗得到我想要数据.

  # import requests
  # ret = requests.get('http://www.baidu.com')
  # if ret.status_code == 200:
  #     print(ret.text)

  """
  # 版本一:
  # from concurrent.futures import ProcessPoolExecutor,ThreadPoolExecutor
  # import time
  # import random
  # import os
  # import requests
  #
  #
  # def task(url):
  #     '''模拟的就是爬取多个源代码 一定有IO操作'''
  #     ret = requests.get(url)
  #     if ret.status_code == 200:
  #         return ret.text
  #
  # def parse(content):
  #     '''模拟对数据进行分析 一般没有IO'''
  #     return len(content)
  #
  #
  # if __name__ == '__main__':
  #     '''串行 耗费时间长,不可取
  #     ret = task('http://www.baidu.com')
  #     print(parse(ret))
  #
  #     ret = task('http://www.JD.com')
  #     print(parse(ret))
  #
  #     ret = task('http://www.taobao.com')
  #     print(parse(ret))
  #
  #     ret = task('https://www.cnblogs.com/jin-xin/articles/7459977.html')
  #     print(parse(ret))
  #
  #     '''
  #     # 开启线程池,并发并行的执行
  #     url_list = [
  #         'http://www.baidu.com',
  #         'http://www.JD.com',
  #         'http://www.JD.com',
  #         'http://www.JD.com',
  #         'http://www.taobao.com',
  #         'https://www.cnblogs.com/jin-xin/articles/7459977.html',
  #         'https://www.luffycity.com/',
  #         'https://www.cnblogs.com/jin-xin/articles/9811379.html',
  #         'https://www.cnblogs.com/jin-xin/articles/11245654.html',
  #         'https://www.sina.com.cn/',
  #     ]
  #     pool = ThreadPoolExecutor(4)
  #     obj_list = []
  #     for url in url_list:
  #         obj = pool.submit(task,url)
  #         obj_list.append(obj)
  #
  #     pool.shutdown(wait=True)
  #     for res in obj_list:
  #         print(parse(res.result()))
  # # '''
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  # parse(res.result())
  '''
  # print('===主')
  '''
  # 版本一:
  #     1. 异步发出10个任务,并发的执行,但是统一的接收所有的任务的返回值.(效率低,不能实时的获取结果)
  #     2. 分析结果流程是串行,影响效率.
  #          for res in obj_list:
  #             print(parse(res.result()))
  '''

  # 版本二: 针对版本一的缺点2,改进,让串行编程并发或者并行.
  # from concurrent.futures import ProcessPoolExecutor, ThreadPoolExecutor
  # import time
  # import random
  # import os
  # import requests
  #
  #
  # def task(url):
  #     '''模拟的就是爬取多个源代码 一定有IO操作'''
  #     ret = requests.get(url)
  #     if ret.status_code == 200:
  #         return parse(ret.text)
  #
  #
  # def parse(content):
  #     '''模拟对数据进行分析 一般没有IO'''
  #     return len(content)
  #
  #
  # if __name__ == '__main__':
  #
  #     # 开启线程池,并发并行的执行
  #     url_list = [
  #         'http://www.baidu.com',
  #         'http://www.JD.com',
  #         'http://www.JD.com',
  #         'http://www.JD.com',
  #         'http://www.taobao.com',
  #         'https://www.cnblogs.com/jin-xin/articles/7459977.html',
  #         'https://www.luffycity.com/',
  #         'https://www.cnblogs.com/jin-xin/articles/9811379.html',
  #         'https://www.cnblogs.com/jin-xin/articles/11245654.html',
  #         'https://www.sina.com.cn/',
  #
  #     ]
  #     pool = ThreadPoolExecutor(4)
  #     obj_list = []
  #     for url in url_list:
  #         obj = pool.submit(task, url)
  #         obj_list.append(obj)
  #     '''
  #     # 1 在开一个线程进程池,并发并行的处理. 再开一个线程进程池,开销大.
  #     # 2 将原来的任务扩大,
  #     版本一:
  #         线程池设置4个线程, 异步发起10个任务,每个任务是通过网页获取源码, 并发执行,
  #         最后统一用列表回收10个任务, 串行着分析源码.
  #     版本二:
  #         线程池设置4个线程, 异步发起10个任务,每个任务是通过网页获取源码+数据分析, 并发执行,
  #         最后将所有的结果展示出来.
  #         耦合性增强了.
  #         并发执行任务,此任务最好是IO阻塞,才能发挥最大的效果
  #     '''
  #     pool.shutdown(wait=True)
  #     for res in obj_list:  # [obj1, obj2,obj3....obj10]
  #         print(res.result())
  #
  """
  # 版本三:
  # 基于 异步调用回收所有任务的结果我要做到实时回收结果,
  # 并发执行任务每个任务只是处理IO阻塞的,不能增加新得功能.
  # 异步调用 + 回调函数
  from concurrent.futures import ProcessPoolExecutor, ThreadPoolExecutor
  import time
  import random
  import os
  import requests


  def task(url):
      '''模拟的就是爬取多个源代码 一定有IO操作'''
      ret = requests.get(url)
      if ret.status_code == 200:
          return ret.text


  def parse(obj):
      '''模拟对数据进行分析 一般没有IO'''
      print(len(obj.result()))


  if __name__ == '__main__':

      # 开启线程池,并发并行的执行
      url_list = [
          'http://www.baidu.com',
          'http://www.JD.com',
          'http://www.JD.com',
          'http://www.JD.com',
          'http://www.taobao.com',
          'https://www.cnblogs.com/jin-xin/articles/7459977.html',
          'https://www.luffycity.com/',
          'https://www.cnblogs.com/jin-xin/articles/9811379.html',
          'https://www.cnblogs.com/jin-xin/articles/11245654.html',
          'https://www.sina.com.cn/',

      ]
      pool = ThreadPoolExecutor(4)

      for url in url_list:
          obj = pool.submit(task, url)
          obj.add_done_callback(parse)

      '''
      线程池设置4个线程, 异步发起10个任务,每个任务是通过网页获取源码, 并发执行,
      当一个任务完成之后,将parse这个分析代码的任务交由剩余的空闲的线程去执行,你这个线程继续去处理其他任务.
      如果进程池+回调: 回调函数由主进程去执行.
      如果线程池+回调: 回到函数由空闲的线程去执行.
      '''

  # 异步 回调是一回事儿?
  # 异步站在发布任务的角度,
  # 站在接收结果的角度: 回调函数 按顺序接收每个任务的结果,进行下一步处理.

  # 异步 + 回调:
  # 异步处理的IO类型.
  # 回调处理非IO

  ```


### 13. 线程队列queue模块
  1. 线程queue多线程抢占资源: 只能让其串行.
    1. 互斥锁.
    2. 队列.

    
  ```
    import queue
  # 第一种 先进先出
  # q = queue.Queue(3)
  # q.put(1)
  # q.put(2)
  # q.put(3)
  # # q.put(4)
  # print(q.get())
  # print(q.get())
  # print(q.get())
  # # print(q.get(block=False))
  # q.get(timeout=2)  # 阻塞2s 还没有值直接报错

  # 第二种 后进先出 LiFo 堆栈
  # q = queue.LifoQueue(4)
  # q.put(1)
  # q.put(2)
  # q.put('alex')
  # q.put('太白')
  # print(q.get())
  # print(q.get())
  # print(q.get())
  # print(q.get())

  # 第三种优先级队列
  q = queue.PriorityQueue(4)
  q.put((5, '元宝'))
  q.put((-2,'狗狗'))
  q.put((0, '李业'))
  # q.put((0, '刚哥'))
  print(q.get())
  print(q.get())
  print(q.get())

  ```


### 线程event事件
  1. 开启两个线程,一个线程运行到中间的某个阶段,触发另个线程执行.两个线程增加了耦合性.
  2. 线程事件的实例对象本质就是一个布尔值的标志位;
    + event = Event()  创建一个线程事件对象
    + event.is_set()   获取线程事件对象的标志位的布尔值，默认是false
    + event.set()  设置线程事件对象的标志位的值为true
    + event.wait()  只阻塞n秒,n秒之后如果还没有进行set 直接进行下一步操作


  ```
    # 版本一:
  # 如果程序中的其他线程需要通过判断某个线程的状态来确定自己下一步的操作
  # from threading import Thread
  # from threading import current_thread
  # import time
  #
  # flag = False
  # def check():
  #     print(f'{current_thread().name} 监测服务器是否开启...')
  #     time.sleep(3)
  #     global flag
  #     flag = True
  #     print('服务器已经开启...')
  #
  # def connect():
  #     while 1:
  #         print(f'{current_thread().name} 等待连接...')
  #         time.sleep(0.5)
  #         if flag:
  #             print(f'{current_thread().name} 连接成功...')
  #             break
  #
  # t1 = Thread(target=check,)
  # t2 = Thread(target=connect,)
  # t1.start()
  # t2.start()

  # 版本二: 事件event

  # from threading import Thread
  # from threading import current_thread
  # from threading import Event
  # import time
  #
  # event = Event()
  # def check():
  #     print(f'{current_thread().name} 监测服务器是否开启...')
  #     time.sleep(3)
  #     print(event.is_set())
  #     event.set()
  #     print(event.is_set())
  #     print('服务器已经开启...')
  #
  # def connect():
  #
  #     print(f'{current_thread().name} 等待连接...')
  #     # event.wait()  # 阻塞 直到 event.set() 方法之后
  #     event.wait(1)  # 只阻塞1秒,1秒之后如果还没有进行set 直接进行下一步操作.
  #     print(f'{current_thread().name} 连接成功...')
  #
  # t1 = Thread(target=check,)
  # t2 = Thread(target=connect,)
  # t1.start()
  # t2.start()

  # 一个线程监测服务器是否开始,
  # 另个一线程判断如果开始了,则显示连接成功,此线程只尝试连接3次,1s 一次,如果超过3次,还没有连接成功,则显示连接失败.


  # from threading import Thread
  # from threading import current_thread
  # from threading import Event
  # import time
  #
  # event = Event()
  # def check():
  #     print(f'{current_thread().name} 监测服务器是否开启...')
  #     time.sleep(4)
  #     event.set()
  #     print('服务器已经开启...')
  #
  # def connect():
  #     count = 1
  #     while not event.is_set():
  #         if count == 4:
  #             print('连接次数过多,已断开')
  #             break
  #         event.wait(1)
  #         print(f'{current_thread().name} 尝试连接{count}次')
  #         count += 1
  #     else:
  #         print(f'{current_thread().name} 连接成功...')
  #
  # t1 = Thread(target=check,)
  # t2 = Thread(target=connect,)
  # t1.start()
  # t2.start()



  ```
