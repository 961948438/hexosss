---
title: scrapy爬虫
date: 2021-05-22 12:15:46
tags:
  - python 爬虫
categories:
  - python
---
# scrapy爬虫所用api总结
## 以完成爬虫
   scrapy crawl tianmao :爬取苏宁图书信息
   scrapy crawl tencent ：爬取腾讯所有招聘广告
   scrapy crawl suning  ：爬取苏宁所有图书详情url
   scrapy crawl insurance ：爬取中银保监会处罚信息
   scrapy crawl govementyj ：爬取中银保监会处罚信息（crawspider系列爬虫）
   scrapy crawl piaofang ： 单日网票最高的前500名排行榜
   scrapy crawl allpiaofang ：内地电影票房总排行榜
   scrapy crawl movieinfo : 爬取所有已经上映的电影
   scrapy crawl tieba : 爬取贴吧某一主题下所有信息，具体主题可指定
   scrapy crawl jd :爬虫京东指定商品的所有分类下的商品url
   scrapy crawl jdinfo :爬取上一步所有url对应具体信息
   scrapy crawl dangdang :爬取当当全部分类下图书信息

## 爬虫的工作原理
   爬虫五大核心成员：engine引擎、scheduler调度器、downloader下载器、爬虫spider、Pipeline数据管道
   1.Spiders的 yeild 将 request 发送给Engine【编写入口url（即需要请求的网站）】
   2.Engine对 request 不做任何处理,将 request 发送给Scheduler，让它排序、入队列
   3.Scheduler排好队列后，将新的 request 请求发送给Engine
   4.Engine拿到新的 request 后，发送给Downloader让它下载
   5.Downloader下载好后(即获取到response)之后，再发送回Engine
   6.Engine获取到response之后，返回给Spiders，Spiders的 parse() 方法对获取到的response进行处理，解析出 “items” 或者
   7.Spiders将解析出来的 “items” 或者 “requests” 发送给Engine
   8.Engine获取到 “items” 或者 “requests” ，将 “items” 发送给ItemPipeline；将 “requests” 发送给Scheduler
   9.ItemPipeline收到 “items” 后，对数据进行后期处理（详细分析、过滤、存储等）
   10.Scheduler收到 “requests” 后，对请求再进行排序、入队，再发送给Downloader让它下载
   11.注意yiled抛出的request请求url不要超过域名或者添加dont_filter=true不过滤操作。
   12.scrapy只能爬取静态页面，动态页面还得靠selenium

## scrapy配合selenium爬取动态网页(可见即可爬)
   1. 在spider的实例化方法init方法中创建一个浏览器对象
      加载驱动：self.chrome_driver =  'C:\\Users\\dd\Desktop\\chromedriver.exe'
      创建浏览器实例：self.browser = webdriver.Chrome(self.chrome_driver)
      设置页面加载延迟elf.browser.set_page_load_timeout(30)
   2. 重写spider的一个方法closed(self,spider)实例化方法,在该方法中执行浏览器关闭的操作
      self.browser.close()
   3. 在下载中间件的process_request方法中,通过spider参数获取spider实例化方法中创建的浏览器对象
   4. 在中间件的process_request中定制基于浏览器自动化的操作代码(获取动态加载出来的页面源码数据)
      spider.browser.get(request.url)
      spider.browser.execute_script('window.scrollTo(0, document.body.scrollHeight)')
   5. 实例化一个响应对象,且将page_source返回的页面源码封装到该对象中
   6. 返回该新的响应对象
      HtmlResponse(url=spider.browser.current_url, body=spider.browser.page_source,
      encoding="utf-8", request=request)
   7. 注意事项：所有的请求都会经过请求下载中间件，所以一定要判断指定需要selenium的爬虫才进行操作，
      请求下载中间件中返回了封装好的响应对象之后该次请求不会经过下载器了
      我们spider爬虫接受到的是包含selenium渲染好的页面的响应对象，可以进行xpath操作，理论上和原来操作一致



## scrapy_redis的作用：搭建分布式、增量式、持久式爬虫
   1.  实现request去重（scrapy框架仅自带单词启动时的请求去重）
       底层采用sha1加密我们的请求对象得到指纹存放于redis中，（使用了hashlib.sha1()）
       将来新来请求的时候同样生成指纹判断redis中是否存在该指纹
   2.  实现爬虫持久化
   3.  实现分布式爬虫
   4.  实现增量式爬虫 （爬取数据建立再之间爬虫基础上，）
   5.  pip install scrapy-redis
   6.  分布式爬虫的本质就是使用一个redis服务器存储去重后的请求对象，或者将管道数据持久化对redis中，
       多个客户端爬取一个redis服务器里面的请求对象即可完成分布式爬虫
   7.  在redis服务器中插入一个starturl： lpush redis-key对应的键 starturl
   8.  在redis服务器中插入starturl是为了开始url被多个客户端爬取

## 使用numpy读取数据
   0. np中的数据类型不是python中的整数浮点数而是一个np的对应的数据内省
      np中通常用1表示行，0表示列，
   1. from numpy import np (nps皆为np的一个数组：nps=np.array([[[2,3]]]))
   2. nps.shape()  返回数组结构
   3. nps.reshage() 重写数组结构 它接受一个元组作为参数，元组元素个数就是数组维度，
      每个元素值就是该维度元素个数，它不修改数组本身，返回新数组
   4. nps.flatten() 数组扁平化。
   5. np.array(list,dtype=bool) 接受一个元组和列表作为参数，返回一个指定元素类型数组
   6. np.arange() 返回指定开始整数到，到指定结束整数的指定步长的整数
   7. numpy的数据类型：int8,int16,int32,float16,float32,complex64,bool
      默认情况下会按照系统位数选择指定位数的数据类型；
      nps.dtype 返回数组中元素的数据类型；
   8. nps.transponse() / nps.T 将我们的数组转制，行和列互换；
   9. np.loadtxt(file,dtype=np.float,delimiter=none,skiprows=0,usecols=none,unpack=false)
      次方法用户在家数据文件到数组中，方法中的参数依次为：
      file: 文件路径
      dtype：设置加载到数组中的元素类型，
      delimiter：设置元数据加载的分隔符，
      skiprows：设置加载的跳过行数
      unpack：是否专制互换行和列
      usecols: 接受一个元组类型作为参数，指定读取的索引
   10.t[2] :取多维数组里面的索引为2的一维元素，
   11.t[[2,3,4]] :取多维数组里索引为2，3，4的一维元素
   12.t[1,2] 取多维数组里面索引为1的一维元素和索引为2的二维元素
   13.t[1:,2] 取多维数组里面索引为1以后的一维元素和索引为2的二维元素
   14.t[[2,3],[1,2]] 取多维数组里面索引为2，3的一维元素的索引为1的元素和索引为2，注意是取对应位置
      的值比如缩影为2的一维数组里面的索引为1的二维数组的值，和索引为3的一维数组中的索引为2的二维数组
   15.t[第一个元素通常是用来操作一维元素的，第二个元素通常是用来操作二维元素的]
   16.t>10:将一个多维数组中的值转换为布尔类型的，
   17.t[true]: 选中满足条件的值，
   18.t=10: 为多维数组中的元素广播赋值
   19.nps.where(条件，满足的值，不满足的值) 多维数组的三元运算符
   20.nps.clip(12,23) :裁剪出多维数组中的指定值之间的元素
   21.nps.astype(np中的数据类型) 转换我们的数据类型
   22.np.vstack(nps1,nps2)用来竖直拼接，即一维上拼接
   23.np.hstack(nps1,nps2) 用来水平拼接，即一维以下拼接，
   24.np.zers(数组结构) 创建一个全为0的数组，
   25.np.ones(数组结构) 创建一个全为1的数组
   26.np.eye(数组当个维的数量) 创建一个对角线为1的正方形数组；
   27.np生成随机数的方法；（依赖于random模块）
   28.np.random.randint(1,29,(2,3)) 生成一个维度为2,的1到29之间的随机数组；
   29.np.ramdom.seed(种子) :设置随机数种子
   30.关于赋值，a=b，ab指向同一个对象，a = b[:] a指向b中的一部分，
      a=b.copy()这样就完全a获得了一份b的拷贝
   31.nan/inf分别表示一个非数字和无穷的。
   32.np.nan == np.nan（false）  可以用来统计非0的个数，因为出0会出现nan
      nan和任意元素计算都是nan（请和的适合应该注意）；
   33.np.inf == np.inf（true）
   34.np.count_nonzero (不是0的元素)
   35.np.sum(数组，axis行0/列1) :用来统计行上的结果和列上的结果，如果不输入求整个数组的结果
   36.np下常用语数据统计函数有：（axis可以用来统计行和列，不传或者穿none统计整个数组）
      np的数组实例都可以使用该方法，然后就可以不传指定数组了
      np.sum(数组，行/列)  取和
      np.mean(数组,行/列) 取数组均值
      np.median(c,axis=1) 取行的中值
      np.max(c,axis=1) 取行的最大值
      np.ptp(c,axis=1) 取行的极差
      np.std(c,axis=1) 取行的标准差
   37.np.isnan(c,axis=1),判断数组中指定轴元素是否为nan，是就将元素赋值为true，
      不是就将元素装换为false，如果不传轴，将操作整个数组

      
## matplotlib绘图工具的简单使用：

   1. import matplotlib.pyplot as plt 导入matplotlib并起别名：
   2. from matplotlib import font_manager
      myfont =  font_manager.FontProperties(fname="C:/Windows/Fonts/方正粗黑宋简体.ttf")
      获取系统指定格式的字体，以便设置x轴和y轴字体：
   3. x = range(0,120,10)  设置x轴范围和刻度数
   4. y = [ random.randint(20,35) for i in x] 设置y轴范围和刻度数
   5. plt.figure(figsize=(15,7))设置图片大小
   6. plt.xticks(x,[ "分钟：%d" % i  for i in x],rotation=90,fontproperties=myfont)
      设置x轴刻度值，刻度标签（非轴标签），x轴旋转方向和字体
   7. plt.plot(x,y1,label="自己",color="red",linewidth=2)  以指定颜色央视绘制图形
   8. plt.savefig("./src/2.png")  绘制之后保存图片
   9. plt.show() 显示图片
   10. plt.xlabel("时间",fontproperties=myfont) 使用已创建字体绘制x轴标签
   11. plt.ylabel("温度",fontproperties=myfont) 使用已创建字体绘制y轴标签
   12. plt.title("温度变化情况表") 设置表标题
   13. plt.grid(alpha = 0.2) 添加表格背景
   14. plt.plot(x,y2,label="别人") plt.plot(x,y1,label="自己") 绘制多个路径在一个表中
      注意：设置了label通常要配合plt.legend(prop=myfont)指令以指定字体显示
   15. plt.legend(prop=myfont) ：通过prop参数指定字体并显示我们多个线条的说明
   16. plt.scatter(x,y1) 绘制散点图
   17. plt.bar(x,y)  用来绘制树状条形图
   18. plt.barh(x,y)  用来绘制横向条形图
   19. plt.hist(x,y,)  用来绘制直方图

## pandas用来处理我们的非数值型数据，也包括数值型数据（底层采用numpy）
   1.  pip install pandas /  import pandas as pa
   2.  t =  pa.Series(值列表，索引列表)  用来创建一个一维数组
       它还可以接受一个字典，字典的键值对将被拆分。
       t是一个带标签的一维数组。
   3.  t.index  返回键的可迭代对象
   4.  t.values 返回值的可迭代对象
   5.  pa.read_csv("文件路径") 方法用来读取我们的csv对象。

## scrapy创建post的两种方式
   1. scrapy.formRequest(post请求url，formdata数据，callback回调)方法发起请求
   2. scrapy.formRequest.form_response(response对象，formdata表单数据)
      这种方法会自动从response响应对象提取我们的表单和表单提交地址，如果表单有多个我们可以
      通过formid参数、xpath参数传入去定位我们的表单
      
## scrapy模拟登录以便获取登录后的信息
   scrapy实现模拟登录的两种方式：
   1.直接携带cookie，
   2.发送post请求url地址，带上信息
   由于scrapy默认是开启cookie的，再次访问同网站将携带之前网站给我们的cookie的





## scrapy shell 开启爬虫的命令行工具
   查看对应的对象下的属性和方法，如request、response

## scrapy 的常用命令
   scrapy startproject 项目名称    | 创建项目
   scrapy genspider 爬虫名  爬虫可访问域名  |  创建爬虫
   scrapy crawl 爬虫名   | 开始爬取
   scrapy genspider -t crawl 爬虫名 爬虫可访问域名 | 创建crawl类的爬虫
   
## 响应对象response下常用方法
   response.url:  当前响应对象的url地址
   response.request.url: 当前响应对象的请求url地址
   response.headers:  响应头
   response.body:  响应体 二进制的byte的类型
   response.requests.headers: 当前响应的请求头
   response.text: 二进制解码的响应体文本
   encoding：响应正文的编码
   request： 产生该响应的request对象
   meta：可以用来不同响应对象之间传递数据
   selector： 对象用于在response中提取数据
   xpath：方法、用于提取元素
   css：方法、用于提取元素
   urljoin：用于构造绝对url, 

## request对象
   rquest：构造函数参数列表---- Request(url [, callback=None, method=‘GET’, headers=None, body=None,cookies=None, meta=None, encoding=‘utf-8’, priority=0,dont_filter=False, errback=None, flags=None])
   url： 请求页面的url
   callback：页面解析函数
   method：请求方法
   headers：请求的头部字典dict类型
   body：请求的正文
   cookies：请求的cookies信息
   meta： Request的元数据字典
   encoding：参数的编码
   priority：请求的优先级设计
   dont_filter: 默认情况下，对同一个url地址多次提交请求后面的请求会被重去过滤
   errback：请求出现错误的时候存在的回调函数

## Selector 元素选择器具有哪些属性和方法
   1. 获取值相关
       get()：获取第一个值
       extract_first(): 获取第一个值
       getall(): 获取所有的值
       extract： 获取所有的值
       get("默认值")：获取值，如果none使用默认值
   2. css选择器相关： 返回值是一个选择器
       css("li::text) :获取文本
       css("img").attrib["src"]: 获取属性
       css("a::attr(href)"): 获取属性
       css("a[href*=im]::attr(href)") :过滤获取属性值
   3. xpath选择器相关 :返回值是一个选择器
       xpath("//div[@id='in']") : 获取指定id节点
       xpatn("//p/text()"): 获取文本
       xpath("..//p/text()")： 从父节点获取文本
       xpath("..//p/text()"): 从当前节点获取文本
       xpath("//a/@href"): 获取属性
       xpath("//a/@href"): 获取属性值
       xpath(r"//li[re:test(@class,"n\d$")]") : 使用正则去查找
       xpath("//li[has-class("clzl)]") 获取有指定类名的元素
       xpath("//a[contains(@href,"png")]) 包含指定属性的元素
       xpath("//a[starts-with(@href,"https")]") 以指定属性值开头的属性的元素

## spider的升级版crawlspider
   1.  从response中提取所有的满足规则的url地址
   2.  自动的构造自己requests请求，发送给引擎
   3.  crawlspider是从rules中构建的（重点在rules中）
   4.  rules是一个元组或者是列表，包含的是Rule对象
   5.  Rule表示规则，其中包含LinkExtractor,callback和follow等参数
   6.  LinkExtractor:连接提取器，可以通过正则或者是xpath来进行url地址的匹配
       比如xpath()只需要定位到我们的a标签就行了
   7.  callback :表示经过连接提取器提取出来的url地址响应的回调函数，可以没有，没有表示响应不会进行回调函数的处理
   8.  follow：连接提取器提取的url地址对应的响应是否还会继续被rules中的规则进行提取，True表示会，Flase表示不会

# setting配置文件说明

```

BOT_NAME = 'ama'

SPIDER_MODULES = ['ama.spiders']
NEWSPIDER_MODULE = 'ama.spiders'


#用户代理可用列表
USER_AGENT_LIST = [
  "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4421.5 Safari/537.36",
  "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.62",
  "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.62",
]

# 日志等级
LOG_LEVEL = "WARNING"
# 用户代理，不要使用默认scrapy代理很明显会被识别为爬虫
USER_AGENT = "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4421.5 Safari/537.36"

# 是否遵循爬虫协议
ROBOTSTXT_OBEY = False

# 允许爬虫线程最大并发数：
CONCURRENT_REQUESTS = 200

# 下载延迟，建议一秒左右
DOWNLOAD_DELAY = 3  
# The download delay setting will honor only one of:

#每个域名的最大并发数
CONCURRENT_REQUESTS_PER_DOMAIN = 100

# 每个ip的最大并发数
CONCURRENT_REQUESTS_PER_IP = 100

#  一定要启用cookie，否则可能无法和网页交互，如登录后的爬取
COOKIES_ENABLED = True

# 是否开启插件
TELNETCONSOLE_ENABLED = True

# 默认请求头信息
DEFAULT_REQUEST_HEADERS = {
  'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
  'Accept-Language': 'en',
  'USER_AGENT' : "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4421.5 Safari/537.36"
}

# 爬虫中间件
SPIDER_MIDDLEWARES = {
   'ama.middlewares.AmaSpiderMiddleware': 543,
   
   
}

# 下载中间件
DOWNLOADER_MIDDLEWARES = {
   'ama.middlewares.AmaDownloaderMiddleware': 543,
   'ama.middlewares.RandomUserAgentmiddleware': 544,
   # 'ama.middlewares.Hhandle': 545,
   'ama.middlewares.JdongMiddleware': 546,
   
}

# 扩展中间件
#EXTENSIONS = {
#    'scrapy.extensions.telnet.TelnetConsole': None,
#}

# 管道中间件
ITEM_PIPELINES = {
   'ama.pipelines.AmaPipeline': 300,
   "ama.pipelines.AmaPipeline1" : 400
}

# 自动限速
#AUTOTHROTTLE_ENABLED = True

# 初始下载延迟
#AUTOTHROTTLE_START_DELAY = 5

# 最大下载延迟
#AUTOTHROTTLE_MAX_DELAY = 60

#AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0
# Enable showing throttling stats for every response received:
#AUTOTHROTTLE_DEBUG = False

# 是否启用http缓存
#HTTPCACHE_ENABLED = True
#HTTPCACHE_EXPIRATION_SECS = 0
#HTTPCACHE_DIR = 'httpcache'
#HTTPCACHE_IGNORE_HTTP_CODES = []
#HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage'

```

# 数据管道说明

```
#####
#####  
#####  数据管道：用来进行数据的存储入库
#####  数据会经过多个管道，且有先后之分，管道定义后记得在配置文件里面注册
#####  processs_item中的参数spider就是我们对应爬虫实例对象
#####  已注册管道：AmaPipeline、AmaPipeline1。中间管道记得返回数据
#####  一个管道类通常具有三个实例方法：epen_spider 、process_item、close_spider
#####  open_spider:在开启爬虫的时候会调用，通常用于开启上下文，如创建数据库链接、打开文件
#####  close_spider:在爬虫结束的时候会调用，通常用于关闭上下文，如关闭数据库链接等
#####  process_item：每一次爬虫返回数据的时候执行的
#####
#####

import json
from itemadapter import ItemAdapter
import logging
from ama.config import  ISDOWNLOAD_TENCENT
from ama.config import  DATACONNECT
from pymysql import *
import matplotlib.pyplot as plt
import random
import time
logger = logging.getLogger(__name__)

class AmaPipeline:
    def open_spider(self, spider):
      # print("*" * 20  + "爬虫开始" + "*" * 20)
      pass
    def close_spider(self, spider):
      # print("*" * 20  + "爬虫结束" + "*" * 20)
      # x = range(0,120)
      # y = [ random.randint(20,35) for i in range(120)]
      # plt.plot(x,y)
      # plt.show()
      # time.sleep(10)
      # plt.close()
      pass
    def process_item(self, item, spider):
        
        return item

class AmaPipeline1:
    def process_item(self, item, spider):
        if (spider.name == 'dangdang'):
          self.handlejd(item)
        if (spider.name == "tieba"):
          self.handletieba(item)
        if (spider.name=="movieinfo"):
          self.handlemovieinfo(item)
        if (spider.name == "allpiaofang"):
          self.handleallpiao(item)
        if (spider.name == "tencent"):
            self.handleCentent(item)
        if (spider.name == "suning"):
          with open("suningDetailPage.txt","a") as f:
                f.write(item.get("src"))
                f.write("\r")
        if (spider.name == 'tianmao'):
            self.handlesuning(item)
        if (spider.name == 'insurance'):
            self.handleinsurance(item)
        if (spider.name == "piaofang"):
          self.handlepiaofang(item)
        return item
    def handlejd(self,item):
      with open("./ama/originalData/dangdangtushu.txt","a",encoding="utf-8") as f:
        res = item.values()
        newres = []
        for i in res:
          newres.append(str(i).replace(",",""))
        f.write(",".join(newres))
        f.write("\r")
      print(
        "根分类：%s\t\t" %item["root"],
        "父分类%s\t\t"%item["parent"],
        "分类%s\t\t"%item["classify"],
        "店铺：%s\t\t"%item["storename"],
      )
    def handletieba(self,item):
      obj = {}
      obj["idport"] = item.get("id")
      obj["authorid"] = item.get("author").get("id")
      obj["authorSysname"] = item.get("author").get("name")
      obj["authorShowname"] = item.get("author").get("name_show")
      obj["createTime"] = item.get("create_time")
      obj["last_time"] = item.get("last_time")
      allimg = item.get("media")
      if type(allimg) is not list:
        obj["imgurl"] = "none"
      else:
        allurl = []
        for it in allimg:
          if type(it.get("big_pic")) is not str:
            allurl.append(it.get("none"))
          allurl.append(it.get("big_pic"))
        obj["imgurl"] = " | ".join(allurl)
      obj["replynum"] = item.get("reply_num")
      obj["tid"] = item.get("tid")
      obj["title"] = item.get("title")
      obj["tokens"] = "frs_replay_icon=%s,frs_zan=%s" %(item.get("tokens").get("frs_replay_icon"),item.get("tokens").get("frs_zan"))
      contenttext = []
      for itt in item.get("abstract"):
        contenttext.append(itt.get("text"))
      obj["text"] = " | ".join(contenttext)
      print(
        "作者名称：%s\t\t" % item.get("author").get("name"),
        "id：%s\t" % item.get("author").get("id"),
        "标题：%s\t\t" % item.get("title")
      )
      with open("./ama/originalData/tieba.txt","a",encoding="utf-8") as f:
        f.write(json.dumps(item))
        f.write("\r")
    def handlemovieinfo(self,item):
        print(item)
    def handleallpiao(self,item):
        print(
          "电影名称：%s\t\t\t" % item["name"],
          "上映年份：%s\t\t" % item["madeinyear"],
          "历史排名：%s\t\t" % item["listpai"]
        )
        with open("./ama/csvhandle/allpiaoxinfo.csv","a",encoding="utf-8") as f:
          content = ",".join(tuple(item.values()))
          f.write(content)
          f.write("\r")
        pass
    def handlepiaofang(self,item):
      print(
          "名次：%s\t" % item["order"],
          "日期：%s\t" % item['date'],
          "票房：%s\t" % item["piaojia"],
          "电影名称：%s\t"% item["name"]
      )
      with open("./ama/csvhandle/piaofang.csv","a",encoding="utf-8") as f:
        cont = ",".join(tuple(item.values()))
        f.write(cont)
        f.write("\r")
    def handleinsurance(self,item):
        print(item.get("docFileUrl"))
    def handlesuning(self,item):
      with open('./ama/csvhandle/resultSuning.csv',"a",encoding="utf8") as f:
        reuslt = (
          item["author"],
          item["title"],
          item["publish"],
          item["publishDate"],
          item["imgDesc"],
          item["oldPrice"],
          item["newPrice"],
          item["shopname"],
          item["jypwCatenIds"],
          item["brandName"],
          item["seoBreadCrumbName"],
          item["jubaoUrl"],
          item["cpmDatasGroupCode"],
          item["addCartNumLimit"],
        )
        f.write("%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,%s" % reuslt)
        f.write("\r")
    def handleCentent(self,item):
        # 数据入库，ISDOWNLOAD是控制是否入库的
        if ISDOWNLOAD_TENCENT == True:
          connect_d = connect(
            host=DATACONNECT.get("host"),
            port=DATACONNECT.get("port"),
            user=DATACONNECT.get("user"),
            password=DATACONNECT.get("password"),
            database=DATACONNECT.get("database"),
            charset=DATACONNECT.get("charset")
          )
          cursor = connect_d.cursor()
          value = (
            str(item.get("Id")),
            str(item.get("PostId")),
            str(item.get("RecruitPostId")),
            str(item.get("RecruitPostName")),
            str(item.get("CountryName")),
            str(item.get("LocationName")),
            str(item.get("BGName")),
            str(item.get("ProductName")),
            str(item.get("CategoryName")),
            str(item.get("Responsibility")),
            str(item.get("LastUpdateTime")),
            str(item.get("PostURL")),
            str(item.get("SourceID")),
            str(item.get("IsCollect")),
            str(item.get("IsValid")),
          )
          sql = "insert into cententhr values('%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s','%s');" % value
          cursor.execute(sql)
          connect_d.commit()
          cursor.close()
          connect_d.close()
```

# 中间件配置selenium等
```
#####
#####   spider实例对象是可以访问到我们配置文件的
#####   中间件配置：用来对我们的请求和响应做出一些控制
#####   介于调度器的请求对象队列和下载器之间
#####   中间件一般情况下可不写
#####   下载中间件介引擎engine、和下载器downloader之间
#####   下载中间件分为请求下载中间件和响应下载中间件，对应要实现的方法：process_request,process_response
#####   下载中间件通常作用于防爬，使用场景有添加用户代理，添加ip端口代理
#####   说明：此处自定义process_response请求中间件中将响应数据临时存储在项目根目录下tem.html下
#####




from scrapy import signals
from itemadapter import is_item, ItemAdapter
import random
from selenium import webdriver
from  scrapy.http import HtmlResponse
from selenium.common.exceptions import TimeoutException
import time

class RandomUserAgentmiddleware:

    def process_request(self,request,spider):
        pass

    


class JdongMiddleware:
    def process_request(self,request,spider):
        
        if spider.name == 'baidu' or spider.name=="jd":
            try:
                time.sleep(2)
                spider.browser.get(request.url)
                spider.browser.execute_script('window.scrollTo(0, document.body.scrollHeight)')
            except TimeoutException as e:
                print('超时')
                spider.browser.execute_script('window.stop()')
            time.sleep(2)
            return HtmlResponse(url=spider.browser.current_url, body=spider.browser.page_source,
                                encoding="utf-8", request=request)

    def process_response(self,request,response,spider):
        # print("xiangying中间件")
        with open("tem.html","w",encoding="utf-8") as f:
            f.write(response.text)
        return response 

class AmaSpiderMiddleware:

    @classmethod
    def from_crawler(cls, crawler):
        s = cls()
        crawler.signals.connect(s.spider_opened, signal=signals.spider_opened)
        return s

    def process_spider_input(self, response, spider):
        return None

    def process_spider_output(self, response, result, spider):
        for i in result:
            yield i

    def process_spider_exception(self, response, exception, spider):
        pass

    def process_start_requests(self, start_requests, spider):
        for r in start_requests:
            yield r

    def spider_opened(self, spider):
        spider.logger.info('Spider opened: %s' % spider.name)

class AmaDownloaderMiddleware:

    @classmethod
    def from_crawler(cls, crawler):
        s = cls()
        crawler.signals.connect(s.spider_opened, signal=signals.spider_opened)
        return s

    def process_request(self, request, spider):
        return None

    def process_response(self, request, response, spider):
        return response

    def process_exception(self, request, exception, spider):
        pass

    def spider_opened(self, spider):
        spider.logger.info('Spider opened: %s' % spider.name)

```

# 爬虫部分字段

```
#####
#####  用来预定义我们spider需要爬取的字段
#####  明确爬取数据结构，方便数据入库
#####  如果给item实例赋值为不存在的字段会报错
#####  item不是一个字典实例，其只是实现了对应的getitem和setitem魔法方法
#####  可以利用强制类型装换dict(item)将其装换为一个字典，方便数据入库
#####
#####

import scrapy

    # Tencent爬虫对应的item
class TecentItem(scrapy.Item):
    Id = scrapy.Field()
    PostId = scrapy.Field()
    RecruitPostId = scrapy.Field()
    RecruitPostName = scrapy.Field()
    CountryName = scrapy.Field()
    LocationName = scrapy.Field()
    BGName = scrapy.Field()
    ProductName = scrapy.Field()
    CategoryName = scrapy.Field()
    Responsibility = scrapy.Field()
    LastUpdateTime = scrapy.Field()
    PostURL = scrapy.Field()
    SourceID = scrapy.Field()
    IsCollect = scrapy.Field()
    IsValid = scrapy.Field()
    
class Suning(scrapy.Item):
    classify = scrapy.Field()
    server = scrapy.Field()
    imgSrc = scrapy.Field()
    price = scrapy.Field()
    desc = scrapy.Field()
    commentCount = scrapy.Field()
    shopName = scrapy.Field()
    discount = scrapy.Field()
    src = scrapy.Field()

class Tianmao(scrapy.Item):
    author = scrapy.Field()
    title = scrapy.Field()
    publish = scrapy.Field()
    publishDate = scrapy.Field()
    imgDesc = scrapy.Field()
    newPrice = scrapy.Field()
    shopname = scrapy.Field()
    jypwCatenIds = scrapy.Field()
    brandName = scrapy.Field()
    seoBreadCrumbName = scrapy.Field()
    jubaoUrl = scrapy.Field()
    cpmDatasGroupCode = scrapy.Field()
    addCartNumLimit = scrapy.Field()

class Insurance(scrapy.Item):
    docid = scrapy.Field()
    docSubtitle = scrapy.Field()
    publishDate = scrapy.Field()
    docSummary = scrapy.Field()
    docFileUrl = scrapy.Field()
    generaltype = scrapy.Field()
    pdfFileUrl = scrapy.Field()
    itemName = scrapy.Field()
    solicitFlag = scrapy.Field()
    docTitle = scrapy.Field()
    datafrom = scrapy.Field()
    docUuid = scrapy.Field()
    builddate = scrapy.Field()
    isTitleLink = scrapy.Field()
    titleLink = scrapy.Field()
    itemId = scrapy.Field()
    itemUuid = scrapy.Field()
    
class Piaofang(scrapy.Item):
    order = scrapy.Field()
    name = scrapy.Field()
    date = scrapy.Field()
    piaofang = scrapy.Field()
    changci = scrapy.Field()
    number = scrapy.Field()
    feichang = scrapy.Field()
    shangzuolv = scrapy.Field()
    piaojia = scrapy.Field()

class Piaofang(scrapy.Item):
    title = scrapy.Field()
    allpiaofang = scrapy.Field()
    daoyan = scrapy.Field()
    zhuyan = scrapy.Field()
    timeshangying = scrapy.Field()
    bofangtime = scrapy.Field()
    shangyingcounty = scrapy.Field()
    leixing = scrapy.Field()
    language = scrapy.Field()
    jianjie = scrapy.Field()


```

# 独立配置文件

```
#####
#####
#####  这是本地配置文件
#####
#####
#####

DATACONNECT = {
  "host": "localhost",
  "port": 3306,
  "user": "root",
  "password": "961948438",
  "database": 'spiderinfo',
  "charset": "utf8"
}

# tencent爬取数据是否
ISDOWNLOAD_TENCENT = False
```

# matplotlib和condas可视化
## 一个
```
# 电影时长直方图
# 直方图使用场景：用户的年轮分布状态，点击次数分布状态、用户活跃时间分布状态

import matplotlib.pyplot as plt
from matplotlib import font_manager
myfont =  font_manager.FontProperties(fname="C:/Windows/Fonts/方正粗黑宋简体.ttf")

atitle =[] 
btime = []
start = 1
count = 500

with open('../csvhandle/allmovie.csv','r',encoding="utf-8") as f:
  if start != 0:
    f.readlines(start)
  for inum in range(count):
    content = f.readline()
    if len(content.split(",")) <=6:
      continue
    if content.split(",")[5] =="none":
      continue
    if not content.split(",")[5].split("分钟")[0].isdigit():
      continue
    title = str(content.split(",")[0])
    time = int(content.split(",")[5].split("分钟")[0])
    
    atitle.append(title)
    btime.append(time)
bin_width = 3

num_bins = int((max(btime) - min(btime))/2)
plt.figure(figsize=(15,10))
plt.hist(btime,num_bins)
plt.xlabel("间隔",fontproperties=myfont)
plt.ylabel("数量",fontproperties=myfont)

plt.title("随机%d部电影时长分布" % count,fontproperties=myfont)
plt.savefig("./csvImg/dianying.png")
plt.show()





# 电影时长直方图
# 直方图使用场景：用户的年轮分布状态，点击次数分布状态、用户活跃时间分布状态

import matplotlib.pyplot as plt
from matplotlib import font_manager
myfont =  font_manager.FontProperties(fname="C:/Windows/Fonts/方正粗黑宋简体.ttf")

atitle =[] 
btime = []
start = 1
count = 500

with open('../csvhandle/allmovie.csv','r',encoding="utf-8") as f:
  if start != 0:
    f.readlines(start)
  for inum in range(count):
    content = f.readline()
    if len(content.split(",")) <=6:
      continue
    if content.split(",")[5] =="none":
      continue
    if not content.split(",")[5].split("分钟")[0].isdigit():
      continue
    title = str(content.split(",")[0])
    time = int(content.split(",")[5].split("分钟")[0])
    
    atitle.append(title)
    btime.append(time)
bin_width = 3

num_bins = int((max(btime) - min(btime))/2)
plt.figure(figsize=(15,10))
plt.hist(btime,num_bins)
plt.xlabel("间隔",fontproperties=myfont)
plt.ylabel("数量",fontproperties=myfont)

plt.title("随机%d部电影时长分布" % count,fontproperties=myfont)
plt.savefig("./csvImg/dianying.png")
plt.show()






```

## 两个

```
# 对应票房场次图片：
# 在当前文件夹下运行，不要再ama项目文件夹下运行
import matplotlib.pyplot as plt
from matplotlib import font_manager
myfont =  font_manager.FontProperties(fname="C:/Windows/Fonts/方正粗黑宋简体.ttf")
a = []
b = []
start = 5
lens = 30
with open("../csvhandle/piaofang.csv","r",encoding="utf-8") as f:
  for i in range(lens):
    if start !=0:
      for ssss in range(start):
        f.readline()
    lineContent = f.readline()
    print(lineContent)
    piaofangchang = int(int(lineContent.split(",")[4])/1000)
    name = str(lineContent.split(",")[1])
    a.append(piaofangchang)
    b.append(name)

plt.figure(figsize=(16,10),dpi=80)
plt.barh(range(lens),a,height=0.3,color="orange")
plt.yticks(range(lens),b,fontproperties=myfont,rotation=0)
plt.ylabel("电影",fontproperties=myfont)
plt.xlabel("播放场次 (千) ",fontproperties=myfont)
plt.title("电影场次表",fontproperties=myfont)
plt.grid(alpha=0.2)

plt.savefig("./csvImg/piao.png")
plt.show()
```

# 所建爬虫
## allpiaofang
```
import scrapy


class AllpiaofangSpider(scrapy.Spider):
    currentPage = 0
    shouCrawlPage = 0
    name = 'allpiaofang'
    allowed_domains = ['58921.com']
    start_urls = ['http://58921.com/alltime?page=0']

    
    def start_requests(self):
        cookies = "Hm_lvt_e71d0b417f75981e161a94970becbb1b=1621148214; DIDA642a4585eb3d6e32fdaa37b44468fb6c=mpfr2tgt8rta5ssvh806c842i3; remember=MTEzNTI2LjIxNjM0Mi4xMDI4MTYuMTA3MTAwLjExMTM4NC4yMDc3NzQuMTE5OTUyLjExMTM4NC4xMDI4MTYuMA%3D%3D; time=MTEzNTI2LjIxNjM0Mi4xMDI4MTYuMTA3MTAwLjExMTM4NC4yMDc3NzQuMTE5OTUyLjExMTM4NC4xMDQ5NTguMTE1NjY4LjEwNzEwMC4xMDQ5NTguMTA0OTU4LjExMzUyNi4xMDI4MTYuMTIyMDk0LjEwNDk1OC4xMDkyNDIuMA%3D%3D; Hm_lpvt_e71d0b417f75981e161a94970becbb1b=1621150914"
        yield scrapy.Request(
            self.start_urls[0],
            callback=self.parse,
            cookies={
                i.split("=")[0]:i.split("=")[1]
                for i in cookies.split(";")
            }
        )

    def parse(self, response):
        AllpiaofangSpider.shouCrawlPage = int(response.xpath('//*[@id="content"]/div[4]/ul/li[2]/span/span/text()').get().split("/")[1])
        #  这样做貌似不能保证顺序，meta使用在请求和响应之间传参的
        while AllpiaofangSpider.currentPage<=AllpiaofangSpider.shouCrawlPage:
            AllpiaofangSpider.currentPage +=1
            yield scrapy.Request(
                "http://58921.com/alltime?page=%d" % AllpiaofangSpider.currentPage,
                callback=self.handleparse,
                meta={"meta":AllpiaofangSpider.currentPage}
            )
    def handleparse(self,response):
        pageindex= response.meta.get("meta")
        alllist = response.xpath('//*[@id="content"]/div[3]/table/tbody/tr')
        for i in alllist:
            item = {}
            item["yearpai"] = i.xpath("./td[1]/text()").get()
            item["listpai"] = i.xpath("./td[2]/text()").get()
            item["name"] = i.xpath("./td[3]/a/text()").get()
            item["allpiaof"] = i.xpath("./td[4]/img/@src").get()
            item["allnum"] = i.xpath("./td[5]/text()").get()
            item["allchan"] = i.xpath("./td[6]/text()").get()
            item["madeinyear"] = i.xpath("./td[7]/text()").get()
            yield item

```

## baidu
```
#####
#####
#####  爬虫响应对象处理
#####  构造
#####
#####
#####
#####

import scrapy
from selenium import webdriver


class BaiduSpider(scrapy.Spider):

    name = 'baidu'
    allowed_domains = ['jd.com']
    start_urls = ['https://search.jd.com/search?keyword=%E7%AC%94%E8%AE%B0%E6%9C%AC&ev=933_40615%5E']
    #处理响应的
    def parse(self, response):
        print(response.status)

    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        print("init---------------")
        self.chrome_driver =  'C:\\Users\\dd\Desktop\\chromedriver.exe'
        self.browser = webdriver.Chrome(self.chrome_driver)
        self.browser.set_page_load_timeout(30)
    def closed(self,spider):
        print("closeed------------")
        # self.browser.close()
```

## dangdang
```
import scrapy
from scrapy.http import request
import re

from scrapy.utils.trackref import NoneType

class DangdangSpider(scrapy.Spider):
    currentPage = 1
    name = 'dangdang'
    allowed_domains = ['dangdang.com']
    start_urls = ['http://category.dangdang.com/?ref=www-0-C']

    def parse(self, response):
        try:
            alldata1 = response.xpath(".//div[@ddt-pit]")
            for alldata1Item in alldata1:  #alldataItem是所有大分类的div@class=ddt-pit
                result = alldata1Item.xpath("./div")
                rootclass = alldata1Item.xpath("./div[@class='classify_books_detail']//a/text()").get()
                for adddatainner in result: #adddatainner是小分类div@class=classify_kind
                    if (result.index(adddatainner) == 0):
                        continue
                    parentclass = adddatainner.xpath("./div[1]/a/text()").get()
                    allsdata = adddatainner.xpath(".//ul/li")
                    for itemdatas in allsdata:
                        nameclass = itemdatas.xpath("./a/text()").get()
                        urls = itemdatas.xpath("./a/@href").get()
                        meta = {
                            "root":rootclass,
                            "parent":parentclass,
                            "classify":nameclass,
                            "current":1
                        }
                        print(
                            "根类：%s\t\t" % rootclass,
                            "父类：%s\t\t" % parentclass,
                            "分类：%s\t\t" % nameclass,
                            # urls
                        )
                        yield scrapy.Request(
                            urls,
                            callback=self.getshop,
                            meta={"meta":meta}
                        )
        except Exception as e:
            print('-e-' * 20)
    def getshop(self,response):
        try:
            meta = response.meta.get("meta")
            alllist = response.xpath(".//div[@id='search_nature_rg']/ul/li")
            for li in alllist:
                item = {}
                item["root"] = meta.get("root")
                item["parent"] = meta.get("parent")
                item["classify"] = meta.get("classify")
                item["storename"] = li.xpath("./p[@class='search_shangjia']/a[1]/text()").get("none")
                item["title"] = li.xpath("./a[@name='itemlist-picture']/@title").get("none")
                item["shopurl"] = li.xpath("./a[@name='itemlist-picture']/@href").get("none")
                item["price"] = li.xpath("./p[@class='price']/span[@class='search_now_price']/text()").get("none")
                item["prePrice"] = li.xpath("./p[@class='price']/span[@class='search_pre_price']/text()").get("none")
                item["sellNum"] = li.xpath("./p[@class='search_shangjia']/a[2]/text()").get("none")
                spanName = li.xpath(".//span[@class='new_lable']/span")
                spanlist = []
                for ispanname in spanName:
                    spanlist.append(ispanname.xpath("./text()").get("none"))
                item["span"] = " | ".join(spanlist)
                style = li.xpath("./p[@class='search_star_line']/span[@class='search_star_black']/span/@style").get()
                item["startlevel"] = int(re.search("(\d+)",style).group(1)) / 10
                item["commentNum"] =li.xpath("./p[@class='search_star_line']/a/text()").get("none")
                item["author"] = li.xpath("./p[@class='search_book_author']/span[1]/a/text()").get("none")
                item["time"] = li.xpath("./p[@class='search_book_author']/span[2]/text()").get("none")
                item["titlemade"] = li.xpath("./p[@class='search_book_author']/span[3]/a/text()").get("none")
                item['detail'] = li.xpath("./p[@class='detail']/text()").get("none")
                yield item
                nexturl = response.xpath('//div[@class="paging"]/ul//li[@class="next"]/a/@href').get("none")
                if (nexturl !="none"):
                    yield scrapy.Request(
                        "http://category.dangdang.com"+nexturl,
                        callback=self.getshop,
                        meta={"meta":meta}
                    )
        except Exception as e:
            print("-err-"*40)



```

## goverment
```
#####
#####  crawl系列的爬虫可以高效的获取到url
#####  rules就是我们爬虫规则指定的地方，用于提取url地址
#####  CrawlSpider内部调用了parse函数，此处我们要重写必须要调用父类对应的方法在内部，不建议重写
#####  通过提取到的url交付给我们的callback去处理
#####  follow用来控制当前url地址的响应是否还会进过rules来提取url地址
#####  crawlspider会自动帮助我们把爬虫补充完整
#####





import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule


class GovementyjSpider(CrawlSpider):
    name = 'govementyj'
    allowed_domains = ['cbirc.gov.cn']
    start_urls = ['http://www.cbirc.gov.cn/cn/view/pages/ItemList.html?itemPId=923&itemId=4113&itemUrl=ItemListRightList.html&itemName=%E9%93%B6%E4%BF%9D%E7%9B%91%E4%BC%9A%E6%9C%BA%E5%85%B3&itemsubPId=931&itemsubPName=%E8%A1%8C%E6%94%BF%E5%A4%84%E7%BD%9A#1']

    rules = (
        Rule(LinkExtractor(allow=r'ItemDetail\.html\?docId=\d+\&itemId=4113\&generaltype=9'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        print("11111111111")
        print(response.text)
        item = {}
        #item['domain_id'] = response.xpath('//input[@id="sid"]/@value').get()
        #item['name'] = response.xpath('//div[@id="name"]').get()
        #item['description'] = response.xpath('//div[@id="description"]').get()
        return item


```

## insurance
```
#### 
####  爬取地址：http://www.cbirc.gov.cn/cn/view/pages/ItemList.html?itemPId=923&itemId=4113&itemUrl=ItemListRightList.html&itemName=%E9%93%B6%E4%BF%9D%E7%9B%91%E4%BC%9A%E6%9C%BA%E5%85%B3&itemsubPId=931&itemsubPName=%E8%A1%8C%E6%94%BF%E5%A4%84%E7%BD%9A#1
####  爬取顺序：先爬取当前页面的数据遍历返回数据轨道之后再次爬取其他页面的数据
####
####



import scrapy
import json
from  ama.items import Insurance

class InsuranceSpider(scrapy.Spider):
    allShoultScrapyCount = 0
    currentPage = 1
    name = 'insurance'
    allowed_domains = ['cbirc.gov.cn']
    start_urls = ['http://www.cbirc.gov.cn/cbircweb/DocInfo/SelectDocByItemIdAndChild?itemId=4113&pageSize=18&pageIndex=1']

    def parse(self, response):
        print("\r\n正在第%d此爬取数据\r\n" % InsuranceSpider.currentPage)
        InsuranceSpider.currentPage+=1
        result = json.loads(response.text)
        if InsuranceSpider.currentPage<=2:
            InsuranceSpider.allShoultScrapyCount = result.get("data").get("total")
        for i in result.get("data").get("rows"):
            it ={}
            it["docid"] = i.get("docid")
            it["docSubtitle"] = i.get("docSubtitle")
            it["publishDate"] = i.get("publishDate")
            it["docSummary"] = i.get("docSummary")
            it["docFileUrl"] = "http://www.cbirc.gov.cn" + str(i.get("docFileUrl"))
            it["generaltype"] = i.get("docid")
            it["pdfFileUrl"] = i.get("docid")
            it["itemName"] = i.get("docid")
            it["solicitFlag"] = i.get("docid")
            it["docTitle"] = i.get("docid")
            it["datafrom"] = i.get("docid")
            it["docUuid"] = i.get("docUuid")
            it["builddate"] = i.get("builddate")
            it["isTitleLink"] = i.get("isTitleLink")
            it["titleLink"] = i.get("titleLink")
            it["itemId"] = i.get("itemId")
            it["itemUuid"] = i.get("itemUuid")
            yield it
        if InsuranceSpider.currentPage < int((InsuranceSpider.allShoultScrapyCount / 18)):
            yield scrapy.Request(
                "http://www.cbirc.gov.cn/cbircweb/DocInfo/SelectDocByItemIdAndChild?itemId=4113&pageSize=18&pageIndex=%d" % InsuranceSpider.currentPage,
                self.parse
            )

```

## jd
```
import scrapy
import re
from scrapy.utils.trackref import NoneType
from selenium import webdriver

class JdSpider(scrapy.Spider):
    name = 'jd'
    allowed_domains = ['jd.com']
    start_urls = ['https://search.jd.com/Search?keyword=笔记本&enc=utf-8']

    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        print("init---------------")
        self.chrome_driver =  'C:\\Users\\dd\Desktop\\chromedriver.exe'
        self.browser = webdriver.Chrome(self.chrome_driver)
        self.browser.set_page_load_timeout(30)
        
    def closed(self,spider):
        print("closeed------------")
        self.browser.close()
    def parse(self, response):
        allClassifyPig = response.xpath('//*[@id="J_selector"]/div')
        for (i) in allClassifyPig:
            if (allClassifyPig.index(i) == 0):
                allBrand = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-logos']/ul//a")
                for inuum in allBrand:
                    obj1 = {}
                    obj1["url"] ="https://search.jd.com/" +  inuum.xpath("./@href").get()
                    obj1["brand"] = inuum.xpath("./@title").get()
            if (allClassifyPig.index(i) == 1):
                alldata2 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata2 in alldata2:
                    obj2 = {}
                    obj2["classify"] = idata2.xpath("./@title").get()
                    obj2["url"] = "https://search.jd.com/" +  idata2.xpath("./@href").get()
            if (allClassifyPig.index(i) == 2):
                alldata3 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata3 in alldata3:
                    obj3 = {}
                    obj3["classify"] = idata3.xpath("./@title").get()
                    obj3["url"] = "https://search.jd.com/" +  idata3.xpath("./@href").get()
            if (allClassifyPig.index(i) == 3):
                alldata4 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata4 in alldata4:
                    obj4 = {}
                    obj4["classify"] = idata4.xpath("./@title").get()
                    obj4["url"] = "https://search.jd.com/" +  idata4.xpath("./@href").get()
            if (allClassifyPig.index(i) == 4):
                alldata5 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata5 in alldata5:
                    obj5 = {}
                    obj5["classify"] = idata5.xpath("./@title").get()
                    obj5["url"] = "https://search.jd.com/" +  idata5.xpath("./@href").get()
            if (allClassifyPig.index(i) == 5):
                superclassify = i.xpath("./div/div[2]/div//span")
                innerclassify = i.xpath("./div/div[3]/div")
                for idata5 in superclassify:
                    supers = idata5.xpath("./text()").get()
                    supersinnerclassfy = innerclassify[superclassify.index(idata5)].xpath("./div[@class='sl-v-list']/ul//a")
                    for iinner5 in supersinnerclassfy:
                        idata5inner = {}
                        idata5inner["root"] = "高级选项"
                        idata5inner["superclassify"] = supers
                        idata5inner["title"] = iinner5.xpath("./text()").get()
                        idata5inner["url"] = "https://search.jd.com/" + iinner5.xpath("./@href").get()
                        print("父类分类：%s \t\t\t子类分类：%s\t\t\t" % (supers,idata5inner["title"]))
                        yield scrapy.Request(
                            idata5inner["url"],
                            callback=self.getdatail,
                            meta={"meta":idata5inner}
                        )
    def getdatail(self,response):
        meta = response.meta.get("meta")
        content  = re.findall('data-sku="(\d+)"',response.text)
        data = list(set(content))
        print(data)
        with open("./ama/csvhandle/jd.csv","a+",encoding="utf-8") as f:
            for isdata in data:
                f.write("https://item.jd.com/%d.html" % int(isdata))
                f.write(",%s"%meta.get("root"))
                f.write(",%s"%meta.get("superclassify"))
                f.write(",%s"%meta.get("title"))
                f.write("\r")
        # for idata in data:
        #     url = "https://item.jd.com/%d.html" % int(idata)
        #     yield scrapy.Request(
        #         url,
        #         callback=self.parsedetail,
        #         meta={"meta":response.meta.get("meta")},
        #         dont_filter=True
        #     )
        srcresult = response.xpath(".//a[@class='pn-next']/@onclick").get()
        if (type(srcresult) == NoneType):
            return
        else:
            numpage = int(re.search("(\d+)",srcresult).group(1))
            yield scrapy.Request(
                response.url.split("&")[0]+"&page="+str(numpage),
                callback=self.getdatail,
                meta={"meta":meta}
            ) 


    def parsedetail(self,response):
        # print(response.meta.get("meta"))
        print(response.rul)
                    


```

## movieinfo

```
import scrapy
import re
from scrapy.utils.trackref import NoneType
from selenium import webdriver

class JdSpider(scrapy.Spider):
    name = 'jd'
    allowed_domains = ['jd.com']
    start_urls = ['https://search.jd.com/Search?keyword=笔记本&enc=utf-8']

    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        print("init---------------")
        self.chrome_driver =  'C:\\Users\\dd\Desktop\\chromedriver.exe'
        self.browser = webdriver.Chrome(self.chrome_driver)
        self.browser.set_page_load_timeout(30)
        
    def closed(self,spider):
        print("closeed------------")
        self.browser.close()
    def parse(self, response):
        allClassifyPig = response.xpath('//*[@id="J_selector"]/div')
        for (i) in allClassifyPig:
            if (allClassifyPig.index(i) == 0):
                allBrand = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-logos']/ul//a")
                for inuum in allBrand:
                    obj1 = {}
                    obj1["url"] ="https://search.jd.com/" +  inuum.xpath("./@href").get()
                    obj1["brand"] = inuum.xpath("./@title").get()
            if (allClassifyPig.index(i) == 1):
                alldata2 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata2 in alldata2:
                    obj2 = {}
                    obj2["classify"] = idata2.xpath("./@title").get()
                    obj2["url"] = "https://search.jd.com/" +  idata2.xpath("./@href").get()
            if (allClassifyPig.index(i) == 2):
                alldata3 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata3 in alldata3:
                    obj3 = {}
                    obj3["classify"] = idata3.xpath("./@title").get()
                    obj3["url"] = "https://search.jd.com/" +  idata3.xpath("./@href").get()
            if (allClassifyPig.index(i) == 3):
                alldata4 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata4 in alldata4:
                    obj4 = {}
                    obj4["classify"] = idata4.xpath("./@title").get()
                    obj4["url"] = "https://search.jd.com/" +  idata4.xpath("./@href").get()
            if (allClassifyPig.index(i) == 4):
                alldata5 = i.xpath(".//div[@class='sl-value']/div[@class='sl-v-list']/ul//a")
                for idata5 in alldata5:
                    obj5 = {}
                    obj5["classify"] = idata5.xpath("./@title").get()
                    obj5["url"] = "https://search.jd.com/" +  idata5.xpath("./@href").get()
            if (allClassifyPig.index(i) == 5):
                superclassify = i.xpath("./div/div[2]/div//span")
                innerclassify = i.xpath("./div/div[3]/div")
                for idata5 in superclassify:
                    supers = idata5.xpath("./text()").get()
                    supersinnerclassfy = innerclassify[superclassify.index(idata5)].xpath("./div[@class='sl-v-list']/ul//a")
                    for iinner5 in supersinnerclassfy:
                        idata5inner = {}
                        idata5inner["root"] = "高级选项"
                        idata5inner["superclassify"] = supers
                        idata5inner["title"] = iinner5.xpath("./text()").get()
                        idata5inner["url"] = "https://search.jd.com/" + iinner5.xpath("./@href").get()
                        print("父类分类：%s \t\t\t子类分类：%s\t\t\t" % (supers,idata5inner["title"]))
                        yield scrapy.Request(
                            idata5inner["url"],
                            callback=self.getdatail,
                            meta={"meta":idata5inner}
                        )
    def getdatail(self,response):
        meta = response.meta.get("meta")
        content  = re.findall('data-sku="(\d+)"',response.text)
        data = list(set(content))
        print(data)
        with open("./ama/csvhandle/jd.csv","a+",encoding="utf-8") as f:
            for isdata in data:
                f.write("https://item.jd.com/%d.html" % int(isdata))
                f.write(",%s"%meta.get("root"))
                f.write(",%s"%meta.get("superclassify"))
                f.write(",%s"%meta.get("title"))
                f.write("\r")
        # for idata in data:
        #     url = "https://item.jd.com/%d.html" % int(idata)
        #     yield scrapy.Request(
        #         url,
        #         callback=self.parsedetail,
        #         meta={"meta":response.meta.get("meta")},
        #         dont_filter=True
        #     )
        srcresult = response.xpath(".//a[@class='pn-next']/@onclick").get()
        if (type(srcresult) == NoneType):
            return
        else:
            numpage = int(re.search("(\d+)",srcresult).group(1))
            yield scrapy.Request(
                response.url.split("&")[0]+"&page="+str(numpage),
                callback=self.getdatail,
                meta={"meta":meta}
            ) 


    def parsedetail(self,response):
        # print(response.meta.get("meta"))
        print(response.rul)
                    


```

## piaofang
```
import scrapy


class PiaofangSpider(scrapy.Spider):
    totalPage = 10
    name = 'piaofang'
    currentPage = 0
    allowed_domains = ['58921.com/']
    start_urls = ['http://58921.com/daily/wangpiao?page=2']

    def start_requests(self):
        cookies= "Hm_lvt_e71d0b417f75981e161a94970becbb1b=1621145612; DIDA642a4585eb3d6e32fdaa37b44468fb6c=nhdohdmunbho9nhpg9t2im0fl3; remember=0; time=MTEzNTI2LjIxNjM0Mi4xMDI4MTYuMTA3MTAwLjExMTM4NC4yMDc3NzQuMTE5OTUyLjExMTM4NC4xMDQ5NTguMTE1NjY4LjEwNzEwMC4xMDQ5NTguMTA0OTU4LjExMTM4NC4xMTM1MjYuMTE5OTUyLjExNTY2OC4xMTU2NjguMA%3D%3D; Hm_lpvt_e71d0b417f75981e161a94970becbb1b=1621145868"
        for i in range(10):
            yield scrapy.Request(
            "http://58921.com/daily/wangpiao?page=%d"% i,
            callback=self.parse,
            cookies={
                i.split("=")[0]:i.split("=")[1]
                for i in cookies.split(";")
            }
            )
    def parse(self, response):
        PiaofangSpider.currentPage+=1
        print("-"*20 + "正在爬取第%d页的数据" % (self.currentPage)+"-"*20)
        print(response.status)
        allList = response.xpath('//*[@id="content"]/div[2]/table/tbody/tr')
        for i in allList:
            item = {}
            item["order"] = i.xpath("./td[1]/text()").get()
            item["name"] = i.xpath("./td[2]/a/text()").get()
            item["date"] = i.xpath("./td[3]/text()").get()
            item["piaofang"] = i.xpath("./td[4]/a/img/@src").get()
            item["changci"] = i.xpath("./td[5]/text()").get()
            item["feichang"] = i.xpath("./td[6]/text()").get()
            item["renci"] = i.xpath("./td[7]/text()").get()
            item["shangzuolv"] = i.xpath("./td[8]/text()").get()
            item["piaojia"] = i.xpath("./td[9]/text()").get()
            yield item
        

```

## suning
```
#####
#####  爬取网站 ：https://list.suning.com/1-502319-0.html
#####  防爬太猛：部分数据js动态生成，请求地址随机
#####



import scrapy
from scrapy import item


class SuningSpider(scrapy.Spider):
    name = 'suning'
    allowed_domains = ['list.suning.com']
    start_urls = ['https://list.suning.com/1-502319-0.html?safp=d488778a.10038.searchPathbox.3&safc=cate.0.0&safpn=10006.502319#search-path-box']

    def parse(self, response):
        allLink = response.xpath('//*[@id="search-path"]/dl/dd/a')
        for i in allLink:
            item = {}
            item["href"] = "https:" + i.xpath(".//@href").get()
            item["title"] = i.xpath(".//@title").get()
            yield scrapy.Request(
                item["href"],
                callback=self.parse_booklist,
                meta={"item":item}
            )

    def parse_booklist(self,response):
        item = response.meta["item"]
        allbook = response.xpath('//*[@id="filter-results"]//ul/li')
        for i in allbook:
            info = {}
            info["classify"] = item.get("title")
            info["server"] = i.xpath(".//div[@class='res-img']/div/a/@title").get("暂无数据")
            info["imgSrc"] = "https:" + i.xpath(".//div[@class='res-img']/div/a/@href").get("暂无数据")
            info["price"]  = i.xpath('//*[@id="filter-results"]/ul/li[1]/div/div/div/div[2]/p[1]/em/text()').get("暂无数据")
            info["desc"]  = i.xpath(".//div[@class='res-info']/p[@class='sell-point']/a/text()").get("暂无数据")
            info["commentCount"] = i.xpath(".//div[@class='res-info']/p[@class='com-cnt']/a").get("暂无数据")
            info["shopName"] = i.xpath(".//div[@class='res-info']/p[@salesname]/@salesname").get("暂无数据")
            info["discount"] = i.xpath(".//div[@class='res-info']/div[@class='sales-label']/span/em").get("暂无数据")
            info["src"] = "http:" + i.xpath('.//p[@class="sell-point"]/a/@href').get("暂无数据")
            yield info
        
        
```

## tencent

```
#####  
#####  爬取命令：scrapy crawl tencent
#####  爬取对象：腾讯招聘所有岗位信息
#####  爬取网址：https://careers.tencent.com/search.html
#####  实现翻页：scrapy.Request(url,callback,meta,dont_filter=False)
#####  meta传参：scrapy.Request(nexturl,meta={"item":item})
#####  通过yield scrapy.Request(下一个url,处理函数)
#####



import scrapy
import pprint
import json
from ama.items import TecentItem
class TencentSpider(scrapy.Spider):
    ShouldRequestCount = 0
    CurrentRequestNumber = 210
    name = 'tencent'
    allowed_domains = ['careers.tencent.com']
    start_urls = ['https://careers.tencent.com/tencentcareer/api/post/Query?timestamp=1621002193841&countryId=&cityId=&bgIds=&productId=&categoryId=&parentCategoryId=&attrId=&keyword=&pageSize=10&language=zh-cn&area=cn&pageIndex=1']

    def parse(self, response):
        
        print("\r\n\r\n正在爬取第" + str(TencentSpider.CurrentRequestNumber) + "页的数据\r\n\r\n")
        job = json.loads(response.text)
        alljobCount = job.get("Data").get("Count")
        TencentSpider.ShouldRequestCount = int(alljobCount /10)
        alljobDsc = job.get("Data").get("Posts")
        for i in alljobDsc:
            item = TecentItem()
            for iproperty in i:
                item[iproperty] = i[iproperty]
            yield i
        TencentSpider.CurrentRequestNumber += 1
        nexturl = TencentSpider.start_urls[0][0:-1] + str(TencentSpider.CurrentRequestNumber)
        if TencentSpider.CurrentRequestNumber <= TencentSpider.ShouldRequestCount:
            yield scrapy.Request(nexturl,callback=self.parse)
        

```

## tianmao

```
#####
#####  此爬虫为suning自爬虫，处理suning爬取到的页面进行二次爬取
#####  抓取到了动态生成的价格、销量等数据
#####  
#####





import scrapy
import re
import os
class TianmaoSpider(scrapy.Spider):
    name = 'tianmao'
    allowed_domains = ['suning.com']
    start_urls = ['https://product.suning.com/0070926447/131403436.html']

    def parse(self, response):
        data = {}
        strCont = response.xpath('//*[@id="proinfoMain"]/div[2]/h1').get("访问出错")
        arr = []
        for i in strCont:
            if i!=" " and i!='\r' and i !="\n":
                arr.append(i)
        arrs = "".join(arr)
        s =  re.search(r'<\/span>(.*)<\/h1>',arrs).group(1)
        data["author"] = response.xpath('//*[@id="proinfoMain"]/ul/li[1]/text()').get("访问出错").strip()
        data["title"] = s
        data["publish"] = response.xpath('//*[@id="proinfoMain"]/ul/li[2]/text()').get("访问出错").strip()
        data["publishDate"] = response.xpath('//*[@id="proinfoMain"]/ul/li[3]/span[2]/text()').get("访问出错").strip()
        data["imgDesc"] = "https:" + response.xpath('//*[@id="bigImg"]/img/@src').get("访问出错")
        data["oldPrice"] = re.search(r'"itemPrice":"(.*)",',response.text).group(1)
        data["newPrice"] = re.search(r'<em id="qg_promotionPrice">(.*)</em>',response.text).group(1)
        data["shopname"] = re.search(r'"shopName":"(.*)",',response.text).group(1)
        data["jypwCatenIds"] = re.search(r'"jypwCatenIds":"(.*)",',response.text).group(1)
        data["brandName"] = re.search(r'"brandName":"(.*)",',response.text).group(1)
        data["seoBreadCrumbName"] = re.search(r'"seoBreadCrumbName":"(.*)",',response.text).group(1)
        data["jubaoUrl"] = re.search(r'"jubaoUrl":"(.*)",',response.text).group(1)
        # data["jubaoID"] = re.search(r'"jubaoID":"(.*)",',response.text).group(1)
        data["cpmDatasGroupCode"] = re.search(r'"cpmDatasGroupCode":"(.*)",',response.text).group(1)
        data["addCartNumLimit"] = re.search(r'"addCartNumLimit":"(.*)",',response.text).group(1)
        yield data
        print()
        with open("./ama/originalData/suningDetailPage.txt","r") as f:
            a = f.readlines()
            for i in a:
                nexturl = i.strip()
                yield scrapy.Request(
                    nexturl,
                    callback=self.parse
                )


```

## tieba

```
import json
import re
import scrapy


class TiebaSpider(scrapy.Spider):
    name = 'tieba'
    allowed_domains = ['tieba.baidu.com']
    start_urls = ['https://tieba.baidu.com/mg/f/getFrsData?kw=%E5%AD%A6%E6%A0%A1&rn=10&pn=1&is_good=0&cid=0&sort_type=0&fr=&default_pro=1&only_thread_list=1&eqid=']

    # def start_requests(self):
    #     # geturl = input("请输入需要爬取的贴吧名称:")
    #     defaulturl = "学校"
    #     url = "http://tieba.baidu.com/f?ie=utf-8&kw=%s&tab=main" % defaulturl
    #     cookies="BIDUPSID=C12E52F9BCA32D90207A2F5CB6A5E3B8; PSTM=1607242831; BAIDUID=C12E52F9BCA32D90E0879C0CD74E683D:FG=1; H_WISE_SIDS=107314_110085_114551_127969_131423_144966_154212_156286_162371_162898_164164_165133_165136_165194_165328_165331_165517_165617_165628_165737_166148_166184_166253_166571_166830_167069_167108_167295_167423_167537_167672_168034_168202_168389_168402_168459_168501_168542_168564_168616_168735_168908_168913_169055_169112_169164_169307_8000000; BDUSS=DBsSWJnRDRReXdhTk5aVG9zNFYwWWp-OXFPbEdzZ1lhQn5DNDhqWH5xeEFmR2xnSVFBQUFBJCQAAAAAAAAAAAEAAAACTVReuLbPvLDrAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEDvQWBA70FgN; BDUSS_BFESS=DBsSWJnRDRReXdhTk5aVG9zNFYwWWp-OXFPbEdzZ1lhQn5DNDhqWH5xeEFmR2xnSVFBQUFBJCQAAAAAAAAAAAEAAAACTVReuLbPvLDrAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEDvQWBA70FgN; BAIDUID_BFESS=41C981A28A3F65E6B0846538CFE6128D:FG=1; BDRCVFR[4r8LXJfwh-6]=I67x6TjHwwYf0; delPer=0; PSINO=7; H_PS_PSSID=26350; __yjs_duid=1_dca5b55dfd1ddfe07212e109220b398e1621223154681; ab_sr=1.0.0_NWE4NWU2ZjgzOWJlYzlhNzBhYjkwYTZlNjllZGExNzU3MGVmZDNjOTg4MGU3NTI2ODA0OWMzY2JhZmQxMmQwZmEzMjZmMGVkNDRlZDYzODQyYzM2MjE3NWQ3MWQ4NmY3YWU2NTUwYmRiZTU5ODQ3ZDU1NzA5YzFiMmNkNTNjZDU="
    #     yield scrapy.Request(
    #         url,
    #         cookies={
    #             i.split("=")[0]:i.split("=")[1]
    #             for i in cookies.split(";")
    #         },
    #         callback=self.parse
    #     )
    def parse(self, response):

        nextcurrentpage= int(json.loads(response.text).get("data").get("page").get("current_page"))+1
        totalpage = json.loads(response.text).get("data").get("page").get("total_page")
        threadlist=json.loads(response.text).get("data").get("thread_list")
        print("\r\n正在爬虫第%d页数据\r\n" % (nextcurrentpage-1)) 
        for i in threadlist:
            yield i
        if nextcurrentpage<=totalpage:
            yield scrapy.Request(
                "https://tieba.baidu.com/mg/f/getFrsData?kw=学校&rn=10&pn=%d&is_good=0&cid=0&sort_type=0&fr=&default_pro=1&only_thread_list=1&eqid=" % nextcurrentpage,
                callback=self.parse
            )

```

## txshipin

```
####
####  实验地址（人人网）：http://www.renren.com/976844768/profile
####  手动登录获取到cookies字符串，然后发起请求
####  当我们知道了登录post请求所需要的各种信息之后，我们就可以发起post请求
####  步骤就是在初始化请求登录页面后，拿到请求页面里的数据发起post请求
####  scrapy.formRequest(post请求url，formdata数据，callback回调)方法发起请求
####


import re
import scrapy
class TxshipinSpider(scrapy.Spider):
    name = 'txshipin'
    allowed_domains = ['v.qq.com']
    start_urls = ['http://www.renren.com/976844768/profile']

    def start_requests(self):
        cookies= "anonymid=kopte89awk5uey; depovince=GW; _r01_=1; JSESSIONID=abckPL4ZISOdH-i3EuWLx; ick_login=c87a2609-23cc-40f5-a071-b6992e8a70bd; taihe_bi_sdk_uid=93d159e983a8837e3ab38923127f2b7d; taihe_bi_sdk_session=e124a09b18723cd7a58d614c8759a8a3; ick=cfb912db-accf-4fa5-acf2-3638ba9a8be1; t=207a1e76fde7f74296b43d080f8daab98; societyguester=207a1e76fde7f74296b43d080f8daab98; id=976844768; xnsid=8fad00b0; ver=7.0; loginfrom=null; XNESSESSIONID=bf4169024c67; jebecookies=a16a8ce4-5ebe-4bf8-9740-c36cb32431bd|||||; wp_fold=0"
        yield scrapy.Request(
            self.start_urls[0],
            callback=self.parse,
            cookies={
                i.split("=")[0]:i.split("=")[1]
                for i in cookies.split(";")
            }
        )
    def parse(self, response):
        content =   re.findall("付祥友",response.text)
        if(len(content) == 0):
            print(response.text)
        print(content)

```