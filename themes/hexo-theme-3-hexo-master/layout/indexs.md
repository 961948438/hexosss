---
title: spider爬取所有股票
date: 2021-03-28 18:09:05
tags:
  - python 爬虫
categories:
  - spider
---

#### 爬取所有股票

  1. 工具selenium

```

from logging import exception
from selenium import webdriver
import time
import io,sys
sys.stdout = io.TextIOWrapper(sys.stdout.buffer,encoding='gb18030')

class Domain(object):

  def __init__(self):
    self.chrome_driver =  'C:\\Users\\dd\Desktop\\chromedriver.exe'
    self.driver =  webdriver.Chrome(self.chrome_driver)

  @staticmethod
  def sleep_time(num):
    time.sleep(num)

  def login(self):
    self.driver.set_window_size(1920,1680)
    self.driver.get('http://vip.stock.finance.sina.com.cn/mkt/#stock_hs_up')
    time.sleep(0.5)
    self.totalpate  = self.driver.find_element_by_xpath('//div[@id="list_pages_top2"]/a[last()-1]')
    headtr =self.driver.find_element_by_xpath('//*[@id="tbl_wrap"]//thead//tr')
    headcontent = []
    headcontent.append(headtr.find_element_by_xpath('.//th[1]/a').text)
    headcontent.append(headtr.find_element_by_xpath('.//th[2]').text)
    tdalist = headtr.find_elements_by_xpath('.//td/a')
    for tda in tdalist:
      headcontent.append(tda.text)
    headcontent.append('\n')
    with open("ggupiao.csv",mode='a',encoding='utf-8') as f:
      f.write(",".join(headcontent))

    while True:      
      alltrlist =self.driver.find_elements_by_xpath('//*[@id="tbl_wrap"]//tbody//tr')
      for tr in alltrlist:
        print(f'to scrapy {alltrlist.index(tr)}')
        listcontent = []
        try:
          listcontent.append(tr.find_element_by_xpath('.//th[1]/a').text)
        except Exception:
          listcontent.append('暂未获取到代码')
        tdalist = tr.find_elements_by_xpath('.//td')
        try:
          listcontent.append(tr.find_element_by_xpath('.//th[2]/a/a').text)
        except Exception:
          listcontent.append('暂未获取到名称')
        tdalist = tr.find_elements_by_xpath('.//td')

        for tda in tdalist:

          listcontent.append(tda.text)
        listcontent.append('\n')
        with open("ggupiao.csv",mode='a',encoding='utf-8') as f:
          f.write(",".join(listcontent))
      try:
        self.driver.find_element_by_xpath('//div[@id="list_pages_top2"]/a[text()="下一页"]').click()
      except Exception:
        print('找不到元素')
        return
        
  def run(self):
    self.login()

if __name__  == "__main__":
  browser =  Domain()
  browser.run()
```