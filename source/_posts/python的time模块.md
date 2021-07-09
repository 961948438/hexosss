---
title: python的time模块
date: 2020-02-10 16:46:55
tags:
  - python_time模块
categories:
  - python
---

### Python  time模块的时间转换：


#### 1.time()函数
time( )函数用于返回当前时间的时间戳(从1970年1月1日00时00分00秒到现在的浮点秒数)
time()函数的语法如下： time.time()
1、此语法中第一个 time 表示 time 模块，该函数不需要传递参数
2、返回值：返回当前时间的时间戳
 {% codeblock %}
  import time
  print("当前时间的时间的时间戳：%f" % time.time())
  当前时间的时间的时间戳：1536375255.196752
 {% endcodeblock %}

#### 2.localtime()函数
time.localtime( )函数的作用是格式化时间戳为本地时间(struct_time类型）。
如果secs参数未传入，就以当前时间为转换标准
localtime() 方法的语法：time.localtime([ secs ])
1、参数secs -- 指转换为  time.struct_time 类型的对象的秒数
2、返回值：该函数无任何返回值
{% codeblock %}
  import time
  print(time.localtime())
  time.struct_time(tm_year=2018, tm_mon=9, tm_mday=8, tm_hour=10, 
  tm_min=59, tm_sec=39, tm_wday=5, tm_yday=251, tm_isdst=0)
{% endcodeblock %}
#### 3.gmtime()函数
gmtime( )  函数用于将一个时间戳转换为UTC时区(0时区)的 struct_time。可选参数
secs 表示从1970-1-1 到现在的秒数，无参数时默认为本地时间
函数返回 time.struct_time 类型的对象 (struct_time 是在 time 模块中定义的表示时间的对象)
gmtime([secs]) 的语法如下：time.gmtime([secs])
1、参数secs -- 指转换为 time.struct_time 类型的对象的秒数
2、返回值：该函数无任何返回值
{% codeblock %}
  import time
  time.gmtime()
time.struct_time(tm_year=2018, tm_mon=9, tm_mday=8, tm_hour=8, 
tm_min=22, tm_sec=14, tm_wday=5, tm_yday=251, tm_isdst=0)
{% endcodeblock %}
#### 4.mktime()函数
mktime( )函数用于执行与 gmtime()、localtime() 相反的操作，接收 struct_time 对
象作为参数，返回用秒数表示时间的浮点数。如果输入的值不是合法时间，
就会触发OverflowError或ValueError
以下是 mktime()方法的语法：  time.mktime(t)
1、参数t -- 这是 struct_time  (结构化时间)或全满 9 个元素的元组。
2、返回值：该方法返回一个浮点数，为了兼容time()函数。
{% codeblock %}
import time
t = ( 2018,9,8,16,34,30,5,251,0)
time.mktime(t)  1536395670.0  
time.mktime(time.localtime())
1536395774.0
{% endcodeblock %}
#### 5.asctime([t])函数
接收一个时间元组并返回一个可读的形式为"Tue Dec 11 18:07:14 2008"
（2008年12月11日 周二18时07分14秒）的24个字符的字符串
asctime() 方法的语法：time.asctime([t]))
1、参数  t -- 完整的9位元组元素或 struct_time 表示，由 gmtime() 和 localtime() 函数返回的时间的元组。
2、返回值：此方法返回以下形式的24个字符的字符串： 'Tue Feb 17 23:21:05 2009'.
{% codeblock %}
import time
t = ( 2021,3,10,16,34,30,5,251,0)
time.asctime(t)
'Wed Mar 10 17:03:25 2021'
{% endcodeblock %}
#### 6.ctime([ secs ]))函数
时间戳转换为time.asctime()的形式。
语法如下：time.ctime([ sec ])
1、参数sec -- 这是将被转换成字符串表示的秒数。
2、返回值：此方法不返回任何值。
{% codeblock %}
import time
time.ctime()
'Wed Mar 10 17:05:44 2021'
{% endcodeblock %}
 #### 7.sleep(secs)函数
sleep()函数用于推迟调用线程的运行，可通过参数secs指定进程挂起的时间
sleep()方法的语法：time.sleep(t)
1、参数t -- 这是要暂停执行的秒数。
2、返回值：此方法不返回任何值。
{% codeblock %}
import time 
def sleep_time(times):
    print(time.ctime())
    time.sleep(times)
    print(time.ctime())
sleep_time(100)
{% endcodeblock %}

#### 8.clock函数
 不建议使用，已经废弃
#### 9.strftime(format[, t])函数
strftime()  方法用于接收时间元组，并返回以可读字符串表示的当地时间。格式由format参数决定。
strftime()只能接受struct_time类型的参数
strftime()方法的语法：time.strftime(format  [,t]  )
{% codeblock %}
import time
t = (2022, 9, 25, 17, 50, 38, 6, 48, 0)
t = time.mktime(t)
print(time.strftime('%b %d %Y %H:%M:%S', time.gmtime(t)))
print("\n\n",time.strftime('%b %d %Y %H:%M:%S'))
print("\n\n",time.strftime('%H:%M:%S'))
{% endcodeblock %}
#### 10.strPtime(string [, format])函数
strptime( )函数用于根据format的格式把一个时间字符串解析为时间元组。
语法如下：strptime (string [,format] )
{% codeblock %}
import time
struct_time = time.strptime("8 Sep 18", "%d %b %y")
print('returned tuple: ', struct_time)
{% endcodeblock %}







