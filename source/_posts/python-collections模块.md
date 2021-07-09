---
title: python_collections模块
date: 2019-04-12 12:09:32
tags:
  - python_collections模块
categories:
  - python
---
###  collctions模块给我们提供了一些python的特殊的数据类型
#### 1.namedtuple: 命令元组生成可以使用名字来访问元素内容的tuple
#### 2.deque: 双端队列，可以快速的从另外一侧追加和推出对象
#### 3.Counter: 计数器，主要用来计数
#### 4.OrderedDict: 有序字典
#### 5.defaultdict: 带有默认值的字典

{% codeblock %}
from collections import namedtuple,deque
Point = namedtuple('tu',["a","b","c"]) # 第一个参数是元组的名字,第二参数是元组中元素的[名字,名字]
p = Point({"keu":(1,2,3,4)}, 20,10)
print(p)
tu = (1,2,32,34,4)
print(tu)

lst = deque([1,2,3,4,5,6,7])
lst.append(8)
lst.appendleft(0)
lst.pop()
lst.popleft()
print(lst[4])

队列:先进先出
栈:先进后出  --  栈顶

lst = [1,2,3,4]
lst.append(5)
lst.pop(0)

'''5.defaultdict: 带有默认值的字典'''
from collections import defaultdict
dic = defaultdict(list)
dic["k1"].append(12)
print(dic)

li = [11,22,33,44,55,77,88,99,90]
result = {"key1":[],"key2":[]}
result = {}
for row in li:
    if row > 66:
        if 'key1' not in result:
            result['key1'] = []
        result['key1'].append(row)
    else:
        if 'key2' not in result:
            result['key2'] = []
        result['key2'].append(row)
print(result)

from collections import defaultdict
dic = defaultdict(set)
li = [11,22,33,44,55,77,88,99,90]
for i in li:
    if i > 66:
        dic["k1"].add(i)
    else:
        dic["k2"].add(i)
print(dic)

from collections import Counter
s = "1112233344aaa"
s = [1,1,2,2,3,3]
s = (1,2,3,3,4,5,6,7,78)
print(dict(Counter(s)))  # ***
'''统计元素出现的次数'''
{% endcodeblock %}