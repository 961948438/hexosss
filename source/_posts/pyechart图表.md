---
title: pyechart图表
date: 2021-07-08 21:41:42
tags:
  - pyechart可视化
categories:
  - python
---
#  导入相关包(新旧版pyechart使用差别很大)

```
import pprint
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from pyecharts import *
from pyecharts.charts import Bar,Pie,WordCloud
from pyecharts import options as opts
```

# 创建一个柱状图

![t1](https://dd-3gw9huij2204eb79-1304020385.tcloudbaseapp.com/images/tb/t1.png)

```
def handlet1():
    data = pd.read_csv('../csvhandle/zhilian.csv',usecols=[4])\
        .dropna()
    arrayData = np.array(data)
    initarr = []
    for ite in arrayData:
        initarr.append(ite[0].split("|")[0])
    initDict = {}
    for initarritem in initarr:
        if initDict.get(initarritem) == None:
            initDict[initarritem] = 1
        else:
            initDict[initarritem] +=1
    findata =     sorted(initDict.items(),key=lambda x:x[1],reverse=True)[:20]
    simpleBar = (
        Bar()
            .add_xaxis([i[0] for i in findata])
            .add_yaxis("行业/职位", [i[1] for i in findata])

            .set_global_opts(
                tooltip_opts=opts.TooltipOpts(is_show=False),
                yaxis_opts=opts.AxisOpts(
                    splitline_opts=opts.SplitLineOpts(is_show=True),
                ),
                xaxis_opts=opts.AxisOpts(
                    name_rotate=60, axislabel_opts={"rotate": 45}
                )

        )

    )
    simpleBar.width = "1300px"
    simpleBar.height = "800px"
    simpleBar.render('t1.html')
```

# 创建一个饼图

![t2](https://dd-3gw9huij2204eb79-1304020385.tcloudbaseapp.com/images/tb/t2.png)
```
def handlet1():
    data = pd.read_csv('../csvhandle/zhilian.csv',usecols=[4])\
        .dropna()
    arrayData = np.array(data)
    initarr = []
    for ite in arrayData:
        initarr.append(ite[0].split("|")[0])
    initDict = {}
    for initarritem in initarr:
        if initDict.get(initarritem) == None:
            initDict[initarritem] = 1
        else:
            initDict[initarritem] +=1
    findata =     sorted(initDict.items(),key=lambda x:x[1],reverse=True)[:20]
    Spimgle = (
        Pie()
            .add("interest", findata)
            .set_global_opts(title_opts=opts.TitleOpts(
                title="行业/职位",
                pos_left="center"),
                legend_opts=opts.LegendOpts(
                    pos_left="left",
                    orient="vertical"
                )
            )

    )
    Spimgle.width='1000px'
    Spimgle.height='600px'
    Spimgle.render('t2.html')

```

# 创建一个柱状图

  1. 配置xaxis_opts让x轴坐标选装一定角度

![](https://dd-3gw9huij2204eb79-1304020385.tcloudbaseapp.com/images/tb/t3.png)

```
def handlet2():
    data = pd.read_csv('../csvhandle/zhilian.csv', usecols=[4]) \
        .dropna()
    arrayData = np.array(data)
    renData = [
        i[0].split("|")[-1].replace("以上", "").replace("人", "")
        for i in arrayData
    ]
    print(renData)
    itemD = {
        "少于15":0,
        "15-50": 0,
        "50-150": 0,
        "150-500": 0,
        "500-2000": 0,
        "2000以上": 0,
    }
    for idata in renData:
        if idata=="少于15":
            itemD["少于15"] +=1
            continue
        if idata=="15-50":
            itemD["15-50"] +=1
            continue
        if idata=="50-150":
            itemD["50-150"] +=1
            continue
        if idata=="150-500":
            itemD["150-500"] +=1
            continue
        if idata=="500-2000":
            itemD["500-2000"] +=1
            continue
        else:
            itemD["2000以上"] += 1

    findata = list(
        itemD.items()
    )
    simpleBar = (
        Bar()
            .add_xaxis([i[0] for i in findata])
            .add_yaxis("公司规模/数量", [i[1] for i in findata])

            .set_global_opts(
            tooltip_opts=opts.TooltipOpts(is_show=False),
            yaxis_opts=opts.AxisOpts(
                splitline_opts=opts.SplitLineOpts(is_show=True),
            ),
            xaxis_opts=opts.AxisOpts(
                name_rotate=60, axislabel_opts={"rotate": 45}
            )

        )

    )
    simpleBar.width = "1300px"
    simpleBar.height = "800px"
    simpleBar.render('t3.html')
```

# 创建一个词云图

![](https://dd-3gw9huij2204eb79-1304020385.tcloudbaseapp.com/images/tb/t4.png)

```
def handlet3():
    data = pd.read_csv('../csvhandle/zhilian.csv', usecols=[2]) \
        .dropna()
    arrayData = np.array(data)
    initarr = []
    for ite in arrayData:
        initarr.append(ite[0])
    initDict = {}
    for initarritem in initarr:
        if initDict.get(initarritem) == None:
            initDict[initarritem] = 1
        else:
            initDict[initarritem] += 1
    print(initDict)
    # finData = sorted(
    #     initDict.values(),
    #     key=lambda x:x[1]
    # )
    # items=[
    #     (key,str(value))
    #     for key,value in initDict.values()
    # ]
    c = (
        WordCloud(init_opts=opts.InitOpts(  ## 初始化画布的大小
            width="1920px",
            height="2000px",
        ))
            .add("", initDict.items(),
                 word_size_range=[30, 100],
                 width=900 * 1.2,
                 height=903 * 1.2,
                 pos_left=0,
                 pos_top=0,
                 pos_right=0,
                 pos_bottom=0,
                 is_draw_out_of_bound=False,
                 )
    ).render("t4.html")

```

# 横向柱状图

  1. 通过.reversal_axis()配置横向
  2. index_col=False,配置第一列非索引以便读取

![](https://dd-3gw9huij2204eb79-1304020385.tcloudbaseapp.com/images/tb/t7.png)

```
def handlet6():
    data = pd.read_csv('../csvhandle/zhilian.csv', index_col=False,usecols=[0,5,6])
    arrayData = np.array(data)
    # initarr = [
    #     (i[0],
    #      int(str(i[1]).replace("k", "")),
    #      int(str(i[2]).replace("k", "")),
    #      )
    #     for i in arrayData
    # ]
    orinin = []
    for idata in arrayData:
        try:
            orinin.append(
                [
                    idata[0],
                    round(
                        (int(idata[1].replace("k", "")) +
                         int(idata[2].replace("k", ""))) / 2
                        ,
                        2
                    )
                ]
            )
        except Exception as e:
            pass
    initDict = {}
    for initarritem, value in orinin:
        if initDict.get(initarritem) == None:
            initDict[initarritem] = [value]
        else:
            initDict[initarritem].append(value)
    findataARR = [
        (key, round(sum(value) / len(value), 1))
        for key, value in initDict.items() if len(value) > 0
    ]
    print(findataARR)
    simpleBar = (
        Bar()
            .add_xaxis([i[0] for i in findataARR])
            .add_yaxis('平均价位',[i[1] for i in findataARR])
            .reversal_axis()
            .set_series_opts(label_opts=opts.LabelOpts(position="right"))
            .set_global_opts(title_opts=opts.TitleOpts(title="语言/平均薪资"))
    )
    simpleBar.width = "900px"
    simpleBar.height = "600px"
    simpleBar.render('t7.html')
``