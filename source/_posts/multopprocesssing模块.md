---
title: python的多任务进程
date: 2020-03-23 20:30:34
tags:
  - python_multopprocesssing模块
categories:
  - python
---
### 进程、线程、协程的概念
  1. 进程的概念：正在进行的一个过程或者说一个任务，而负责执行任务的则是CPU，进程本身是
  一个抽象的概念,即进程就是一个过程、一个任务。
  CPU描述的是一个程序的执行过程.
  进程之间是如何做到并发的：CPU在各个任务之间来回的进行切换，并在切换的过程当中保存当前
  进程的执行状态（保存蛋糕的执行过程）。
  进程与程序的区别：程序相当于菜谱，而进程相当于做菜的整个过程。
  需要强调的是：同一个程序执行两次(双击),那也是两个进程，比如打开暴风影音，虽然都是同一个软件，但是一个可以播放a，一个可以播放b.
  核的概念：https://zhidao.baidu.com/question/541410131.html
  处理器，就是说有几个处理器。。。也就说一个CPU里面会有几个处理器，这样就可以同时处理几个要求了。
  2. 线程的概念：一个进程里面至少有一个控制线程，进程的概念只是一种抽象的概念，真正在CPU上面调度的是进程
  里面的线程,就好比真正在地铁这个进程里面工作的实际上是地铁里面的线程,北京地铁里面至少要有
  一个线程，线程是真正干活的，线程用的是进程里面包含的一堆资源,线程仅仅是一个调度单位，不包含资源。
  协程的概念：协程 ，又称为微线程，它是实现多任务的另一种方式，只不过是比线程更小的执行单元。因为它自带CPU的上下文，这样只要在合适的时机，我们可以把一个协程切换到另一个协程。

  3. 通俗的理解： 在一个线程中的某个函数中，我们可以在任何地方保存当前函数的一些临时变量等信息，然后切换到另外一个函数中执行，注意不是通过调用函数的方式做到的 ，并且切换的次数以及什么时候再切换到原来的函数都由开发者自己确定。
  协程与线程的差异：
  在实现多任务时, 线程切换__从系统层面__远不止保存和恢复CPU上下文这么简单。操作系统为了程序运行的高效性，每个线程都有自己缓存Cache等等数据，操作系统还会帮你做这些数据的恢复操作，所以线程的切换非常耗性能。但是__协程的切换只是单纯地操作CPU的上下文__，所以一秒钟切换个上百万次系统都抗的住。

### 在python中使用Process进程类

#### 1. 进程创建的两种方式
{% codeblock %}
<!-- 开启进程的第一种方式: -->
from multiprocessing import Process
import time

def task(name):
    print(f'{name} is running')
    time.sleep(2)
    print(f'{name} is gone')



if __name__ == '__main__':
    # 在windows环境下, 开启进程必须在 __name__ == '__main__' 下面
    p = Process(target=task,args=('常鑫',))  # 创建一个进程对象
    p.start()
    <!--   只是向操作系统发出一个开辟子进程的信号,然后就执行下一行了. -->
    <!-- 这个信号操作系统接收到之后,会从内存中开辟一个子进程空间,然后在将主进程所有数据copy加载到子进程,然后在调用cpu去执行.
    开辟子进程开销是很大的. -->
    print('==主开始')
    time.sleep(3)


    <!-- 所以永远会先执行主进程的代码. -->



<!-- 开启进程的第二种方式: -->

from multiprocessing import Process
import time

class MyProcess(Process):

    def __init__(self,name):
        super().__init__()
        self.name = name

    def run1(self):
        print(f'{self.name} is running')
        time.sleep(2)
        print(f'{self.name} is gone')


if __name__ == '__main__':
    p = MyProcess('常鑫')
    p.start()
    print('===主')


<!-- 简单应用: -->


from multiprocessing import Process
import time

def task(name):
    print(f'{name} is running')
    time.sleep(1)
    print(f'{name} is gone')

def task1(name):
    print(f'{name} is running')
    time.sleep(2)
    print(f'{name} is gone')

def task2(name):
    print(f'{name} is running')
    time.sleep(3)
    print(f'{name} is gone')


if __name__ == '__main__':
    # 在windows环境下, 开启进程必须在 __name__ == '__main__' 下面
    # p1 = Process(target=task,args=('常鑫',))  # 创建一个进程对象
    # p2 = Process(target=task,args=('李远点业',))  # 创建一个进程对象
    # 一个进程串行的执行三个任务
    start_time = time.time()
    task('常鑫')
    task1('李远点业')
    task2('海狗')
    print(f'结束时间{time.time() - start_time}')
    三个进程 并发或者并行的执行三个任务
    start_time = time.time()
    p1 = Process(target=task, args=('常鑫',))  # 创建一个进程对象
    p2 = Process(target=task1, args=('李远点业',))  # 创建一个进程对象
    p1.start()
    p2.start()
    task2('海狗')
    print(f'结束时间{time.time()-start_time}')

{% endcodeblock %}

#### 2. 获取进程pid
{% codeblock %}
import os
import time
print(f'子进程:{os.getpid()}')
print(f'主(父)进程:{os.getppid()}')
time.sleep(50)



from multiprocessing import Process
import time
import os

def task(name):
    print(f'子进程:{os.getpid()}')
    print(f'主进程:{os.getppid()}')



if __name__ == '__main__':
    p = Process(target=task,args=('常鑫',))  # 创建一个进程对象
    p.start()
    # print('==主开始')
    print(f'====主{os.getpid()}')
{% endcodeblock %}

#### 3. 验证进程之间的空间隔离
{% codeblock %}
#encoding=utf-8
from multiprocessing import Process
import os
import time
name = 'sa'

def task():
    global name
    name = 'assb'
    print(f'子进程{name}')


if __name__ == '__main__':
    p = Process(target=task)  # 创建一个进程对象
    p.start()
    # print('==主开始')
    time.sleep(3)
    print(f'主:{name}')

lst = ['丽丽',]

def task():
    lst.append('怼姐')
    print(f'子进程{lst}')

if __name__ == '__main__':
    p = Process(target=task)  # 创建一个进程对象
    p.start()
    # print('==主开始')
    time.sleep(3)
    print(f'主:{lst}')

{% endcodeblock %}

#### 4. join方法让主进程等待子进程结束之后,在执行主进程
1. join让主进程等待子进程结束之后,在执行主进程.
2. join 多个连接的join方法是2一起通知主进程的，只针对主进程,如果join下面多次join 他是不阻塞的.
{% codeblock %}

from multiprocessing import Process
import time

def task(name):
    print(f'{name} is running')
    time.sleep(2)
    print(f'{name} is gone')

if __name__ == '__main__':

    p = Process(target=task,args=('ad',))  # 创建一个进程对象
    p.start()
    p.join()
    print('==instart')


多个子进程使用join

from multiprocessing import Process
import time

def task(name,sec):
    print(f'{name}is running')
    time.sleep(sec)
    print(f'{name} is gone')


if __name__ == '__main__':
    start_time = time.time()
    p1 = Process(target=task,args=('常鑫',1))
    p2 = Process(target=task,args=('李业',2))
    p3 = Process(target=task,args=('海狗',3))
    p1.start()
    p2.start()
    p3.start()
    print(f'==主{time.time()-start_time}')  # 0.02 这只是主进程结束的时间,与其他进程毫无关系


验证1

from multiprocessing import Process
import time

def task(name,sec):
    print(f'{name}is running')
    time.sleep(sec)
    print(f'{name} is gone')


if __name__ == '__main__':
    start_time = time.time()
    p1 = Process(target=task,args=('常鑫',1))
    p2 = Process(target=task,args=('李业',2))
    p3 = Process(target=task,args=('海狗',3))

    p1.start()
    p2.start()
    p3.start()
    # join 只针对主进程,如果join下面多次join 他是不阻塞的.
    p1.join()
    p2.join()
    p3.join()

    print(f'==主{time.time()-start_time}')

验证2

from multiprocessing import Process
import time

def task(name,sec):
    print(f'{name}is running')
    time.sleep(sec)
    print(f'{name} is gone')


if __name__ == '__main__':
    start_time = time.time()
    p1 = Process(target=task,args=('常鑫',3))
    p2 = Process(target=task,args=('李业',2))
    p3 = Process(target=task,args=('海狗',1))

    p1.start()
    p2.start()
    p3.start()
    # join就是阻塞

    p1.join()  # 等2s
    print(f'==主1:{time.time()-start_time}')
    p2.join()
    print(f'===主2:{time.time()-start_time}')
    p3.join()
    print(f'==主3:{time.time()-start_time}')  #


 优化上面的代码



from multiprocessing import Process
import time

def task(sec):
    print(f'is running')
    time.sleep(sec)
    print(f' is gone')


if __name__ == '__main__':
    start_time = time.time()
    p1 = Process(target=task,args=(1,))
    p2 = Process(target=task,args=(2,))
    p3 = Process(target=task,args=(3,))
    
    p1.start()
    p2.start()
    p3.start()
    # join 只针对主进程,如果join下面多次join 他是不阻塞的.
    p1.join()
    p2.join()
    p3.join()



    错误示范:
    for i in range(1,4):
        p = Process(target=task,args=(i,))
        p.start()
        p.join()
    '''
    p1 = Process(target=task,args=(1,))
    p1.start()
    p1.join()
    p2 = Process(target=task,args=(2,))
    p2.start()
    p2.join()
    p3 = Process(target=task,args=(3,))
    p3.start()
    p3.join()
    
    '''

    正确示范:
    l1 = []
    for i in range(1, 4):
        p = Process(target=task,args=(i,))
        l1.append(p)
        p.start()
    
    for i in l1:
        i.join()
    
    print(f'==主{time.time()-start_time}')

    join就是阻塞,主进程有join,主进程下面的代码一律不执行,直到进程执行完毕之后,在执行.


{% endcodeblock %}

#### 5. 进程的其他参数
{% codeblock %}
from multiprocessing import Process
import time

def task(name):
    print(f'{name} is running')
    time.sleep(2)
    print(f'{name} is gone')



if __name__ == '__main__':
    # 在windows环境下, 开启进程必须在 __name__ == '__main__' 下面
    # p = Process(target=task,args=('常鑫',))  # 创建一个进程对象
    p = Process(target=task,args=('常鑫',),name='alex')  # 创建一个进程对象
    p.start()
    # time.sleep(1)
    # p.terminate()  # 杀死子进程  ***
    # p.join()  # ***
    # time.sleep(0.5)
    # print(p.is_alive())   # ***
    # print(p.name)
    p.name = 'sb'
    print(p.name)
    print('==主开始')
{% endcodeblock %}

#### 6. 守护进程

{% codeblock %}
    # 守护进程:
    # 古时候 太监守护这个皇帝,如果皇帝驾崩了,太监直接也就死了.
    # 子进程守护着主进程,只要主进程结束,子进程跟着就结束,


from multiprocessing import Process
import time

def task(name):
    print(f'{name} is running')
    time.sleep(2)
    print(f'{name} is gone')



if __name__ == '__main__':
    # 在windows环境下, 开启进程必须在 __name__ == '__main__' 下面
    p = Process(target=task,args=('常鑫',))  # 创建一个进程对象
    p.daemon = True  # 将p子进程设置成守护进程,只要主进程结束,守护进程马上结束.
    p.start()
    # p.daemon = True  # 一定要在子进程开启之前设置
    time.sleep(1)
    print('===主')
{% endcodeblock %}

#### 7. 僵尸进程孤儿进程
  基于unix环境(linux,macOS)

  + 主进程需要等待子进程结束之后,主进程才结束

    主进程时刻监测子进程的运行状态,当子进程结束之后,一段时间之内,将子进程进行回收.

   + 为什么主进程不在子进程结束后马上对其回收呢?

    1. 主进程与子进程是异步关系.主进程无法马上捕获子进程什么时候结束.
    2. 如果子进程结束之后马上再内存中释放资源,主进程就没有办法监测子进程的状态了.

  + unix针对于上面的问题,提供了一个机制.

    所有的子进程结束之后,立马会释放掉文件的操作链接,内存的大部分数据,但是会保留一些内容: 进程号,结束时间,运行状态,等待主进程监测,回收.

  + 僵尸进程: 所有的子进程结束之后,在被主进程回收之前,都会进入僵尸进程状态.

  + 僵尸进程有无危害???

    如果父进程不对僵尸进程进行回收(wait/waitpid),产生大量的僵尸进程,这样就会占用内存,占用进程pid号.

  + 孤儿进程: 

    父进程由于某种原因结束了,但是你的子进程还在运行中,这样你的这些子进程就成了孤儿进程.你的父进程如果结束了,你的所有的孤儿进程就会被init进程的回收,init就变成了你的父进程,对你进行回收.

  + 僵尸进程如何解决???

    父进程产生了大量子进程,但是不回收,这样就会形成大量的僵尸进程,解决方式就是直接杀死父进程,将所有的僵尸进程变成孤儿进程进程,由init进行回收.

#### 8. 僵尸进程孤儿进程

  1. lock与join的区别.

      + 共同点: 都可以把并发变成串行, 保证了顺序.

      + 不同点: join人为设定顺序,lock让其争抢顺序,保证了公平性.

  2. 互斥锁,锁:进程锁主要是用来解决进程直接抢占资源等操作，比如读取同一文件、抢占队列数据
      和抢占打印机等等类似操作

      ```
      # 三个同事 同时用一个打印机打印内容.
      # 三个进程模拟三个同事, 输出平台模拟打印机.
      
      # 版本一:
      # from multiprocessing import Process
      # import time
      # import random
      # import os
      #
      # def task1():
      #     print(f'{os.getpid()}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{os.getpid()}打印结束了')
      #
      # def task2():
      #     print(f'{os.getpid()}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{os.getpid()}打印结束了')
      #
      # def task3():
      #     print(f'{os.getpid()}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{os.getpid()}打印结束了')
      #
      # if __name__ == '__main__':
      #
      #     p1 = Process(target=task1)
      #     p2 = Process(target=task2)
      #     p3 = Process(target=task3)
      #
      #     p1.start()
      #     p2.start()
      #     p3.start()
      
      # 现在是所有的进程都并发的抢占打印机,
      # 并发是以效率优先的,但是目前我们的需求: 顺序优先.
      # 多个进程共强一个资源时, 要保证顺序优先: 串行,一个一个来.
      
      
      # 版本二:
      
      
      # from multiprocessing import Process
      # import time
      # import random
      # import os
      #
      # def task1(p):
      #     print(f'{p}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{p}打印结束了')
      #
      # def task2(p):
      #     print(f'{p}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{p}打印结束了')
      #
      # def task3(p):
      #     print(f'{p}开始打印了')
      #     time.sleep(random.randint(1,3))
      #     print(f'{p}打印结束了')
      #
      # if __name__ == '__main__':
      #
      #     p1 = Process(target=task1,args=('p1',))
      #     p2 = Process(target=task2,args=('p2',))
      #     p3 = Process(target=task3,args=('p3',))
      #
      #     p2.start()
      #     p2.join()
      #     p1.start()
      #     p1.join()
      #     p3.start()
      #     p3.join()
      
      # 我们利用join 解决串行的问题,保证了顺序优先,但是这个谁先谁后是固定的.
      # 这样不合理. 你在争抢同一个资源的时候,应该是先到先得,保证公平.
      
      
      # 版本3:
      
      from multiprocessing import Process
      from multiprocessing import Lock
      import time
      import random
      import os
      
      def task1(p,lock):
          '''
          一把锁不能连续锁两次
          lock.acquire()
          lock.acquire()
          lock.release()
          lock.release()
          '''
          lock.acquire()
          print(f'{p}开始打印了')
          time.sleep(random.randint(1,3))
          print(f'{p}打印结束了')
          lock.release()
      
      def task2(p,lock):
          lock.acquire()
          print(f'{p}开始打印了')
          time.sleep(random.randint(1,3))
          print(f'{p}打印结束了')
          lock.release()
      
      def task3(p,lock):
          lock.acquire()
          print(f'{p}开始打印了')
          time.sleep(random.randint(1,3))
          print(f'{p}打印结束了')
          lock.release()
      
      if __name__ == '__main__':
      
          mutex = Lock()
          p1 = Process(target=task1,args=('p1',mutex))
          p2 = Process(target=task2,args=('p2',mutex))
          p3 = Process(target=task3,args=('p3',mutex))
      
          p2.start()
          p1.start()
          p3.start()
      

      ```

#### 9.  进程之间通信（基于文件、队列、管道）

        进程在内存级别是隔离的,但是文件在磁盘上,

        1. 基于文件通信.

            利用抢票系统讲解.

         ```
         # 抢票系统.
         # 1. 先可以查票.查询余票数.  并发
         # 2. 进行购买,向服务端发送请求,服务端接收请求,在后端将票数-1,返回到前端. 串行.
         
         # from multiprocessing import Process
         # import json
         # import time
         # import os
         # import random
         #
         #
         # def search():
         #     time.sleep(random.randint(1,3))  # 模拟网络延迟(查询环节)
         #     with open('ticket.json',encoding='utf-8') as f1:
         #         dic = json.load(f1)
         #         print(f'{os.getpid()} 查看了票数,剩余{dic["count"]}')
         #
         #
         # def paid():
         #     with open('ticket.json', encoding='utf-8') as f1:
         #         dic = json.load(f1)
         #     if dic['count'] > 0:
         #         dic['count'] -= 1
         #         time.sleep(random.randint(1,3))  # 模拟网络延迟(购买环节)
         #         with open('ticket.json', encoding='utf-8',mode='w') as f1:
         #             json.dump(dic,f1)
         #         print(f'{os.getpid()} 购买成功')
         #
         # def task():
         #     search()
         #     paid()
         #
         #
         # if __name__ == '__main__':
         #
         #     for i in range(6):
         #         p = Process(target=task)
         #         p.start()
         
         # 当多个进程共强一个数据时,如果要保证数据的安全,必须要串行.
         # 要想让购买环节进行串行,我们必须要加锁处理.
         
         #
         # from multiprocessing import Process
         # from multiprocessing import Lock
         # import json
         # import time
         # import os
         # import random
         #
         #
         # def search():
         #     time.sleep(random.randint(1,3))  # 模拟网络延迟(查询环节)
         #     with open('ticket.json',encoding='utf-8') as f1:
         #         dic = json.load(f1)
         #         print(f'{os.getpid()} 查看了票数,剩余{dic["count"]}')
         #
         #
         # def paid():
         #     with open('ticket.json', encoding='utf-8') as f1:
         #
         #         dic = json.load(f1)
         #     if dic['count'] > 0:
         #         dic['count'] -= 1
         #         time.sleep(random.randint(1,3))  # 模拟网络延迟(购买环节)
         #         with open('ticket.json', encoding='utf-8',mode='w') as f1:
         #             json.dump(dic,f1)
         #         print(f'{os.getpid()} 购买成功')
         #
         #
         # def task(lock):
         #     search()
         #     lock.acquire()
         #     paid()
         #     lock.release()
         #
         # if __name__ == '__main__':
         #     mutex = Lock()
         #     for i in range(6):
         #         p = Process(target=task,args=(mutex,))
         #         p.start()
         
         
         # 当很多进程共强一个资源(数据)时, 你要保证顺序(数据的安全),一定要串行.
         # 互斥锁: 可以公平性的保证顺序以及数据的安全.
         
         # 基于文件的进程之间的通信:
             # 效率低.
             # 自己加锁麻烦而且很容易出现死锁.
         
         ```

        2. 基于队列通信.
        ```
        # 队列: 把队列理解成一个容器,这个容器可以承载一些数据,
        # 队列的特性: 先进先出永远保持这个数据. FIFO 羽毛球筒.
        # from multiprocessing import Queue
        # q = Queue()
        # def func():
        #     print('in func')
        # q.put(1)
        # q.put('alex')
        # q.put([1,2,3])
        # q.put(func)
        #
        #
        # print(q.get())
        # print(q.get())
        # print(q.get())
        # f = q.get()
        # f()


        # from multiprocessing import Queue
        # q = Queue(3)
        #
        # q.put(1)
        # q.put('alex')
        # q.put([1,2,3])
        # # q.put(5555)  # 当队列满了时,在进程put数据就会阻塞.
        # # q.get()
        #
        # print(q.get())
        # print(q.get())
        # print(q.get())
        # print(q.get())  # 当数据取完时,在进程get数据也会出现阻塞,直到某一个进程put数据.


        from multiprocessing import Queue
        q = Queue(3)  # maxsize

        q.put(1)
        q.put('alex')
        q.put([1,2,3])
        # q.put(5555,block=False)
        #
        print(q.get())
        print(q.get())
        print(q.get())
        print(q.get(timeout=3))  # 阻塞3秒,3秒之后还阻塞直接报错.
        # print(q.get(block=False))

        # block=False 只要遇到阻塞就会报错.


        ```


        3. 基于管道的通信.