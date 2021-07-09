---
title: react-virtualized高性能长列表组件使用
date: 2021-03-28 17:52:33
tags:
  - react-virtualized_component
categories:
  - js
---
### react长列表优化方案: react-virtualized

#### 常见问题：

  ![常见列表问题]('http://www.mk2048.com/web_upload/blog_imgs/7/https___user-gold-cdn-xitu-io_2018_5_7_1633aaa0934caa53_w-1052_h-980_f-png_s-51636.gif')

  当一个学生组件为如下时：
  ```
  function Student({student}) {
    return <div>{student.name}</div>
  }
  ```
  当有五千个学生时会生成五千个组件，消耗很大性能，特别是在dom进行重绘重排的时候；
  DOM结构如果过大, 网页就会出现用户操作体验上的问题, 比如滚动, 点击等常用操作. 同时, 对react的虚拟DOM计算以及虚拟DOM反映到真实DOM的压力也会很大. 当用户点击切换教室时, 就会出现秒级的卡顿

#### 使用react-virtualized优化

  + 在react生态中, react-virtualized作为长列表优化的存在已久, 社区一直在更新维护, 讨论不断, 同时也意味着这是一个长期存在的棘手问题!？
  + 解决以上问题的核心思想就是: 只加载可见区域的组件
  + react-virtualized将我们的滚动场景区分为了viewport内的局部滚动, 和基于viewport的滚动, 前者相当于在页面中开辟了一个独立的滚动区域，属于内部滚动, 这跟和iscroll的滚动很类似, 而后者则把滚动作为了window滚动的一部分(对于移动端而言，这种更为常见). 基于此计算出当前所需要显示的组件.
  + 通过该第三方组件我们将进行如下实现：

  ```
  function Student({student, style, ...rest}) {
    return (
        <div style={style}>
            ...
                <div>{student.name} ....</div>
            ...
        </div>
    )
  }
  import React from 'react'
import { AutoSizer } from 'react-virtualized/dist/commonjs/AutoSizer'
import { List as VList } from 'react-virtualized/dist/commonjs/List'

class StudentList extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            list: []
        }
    }
    getList = () => {
        api.getList.then(res => {
            this.setState({
                list: res
            })
        })
    }
    componentDidMount() {
        this.getList()
    }
    render() {
        const { list } = this.state  
        const renderItem = ({ index, key, style }) => {
            return <Student key={key} student={list[index]} style{style} />
        }
        return (
            <div style={{height: 1000}}>
                <AutoSizer>
                    {({ width, height }) => (
                        <VList
                            width={width}
                            height={height}
                            overscanRowCount={10}
                            rowCount={list.length}
                            rowHeight={100}
                            rowRenderer={renderItem}
                        />
                    )}
                </AutoSizer>
            </div>
        )
    }
  }

  ```

  外层div样式中的高度不是必须的, 比如你的网页是flex布局, 你可以用flex: 1来让react-virtualized计算出这个高度)这个时候, 如果每个Student的高度相同的话, 问题基本上就解决啦!
  可是, 问题又来了, 有时候我们的Student会是不确定高度的, 可以有两种方法解决问题, 推荐react-virtualized的CellMeasurer组件解决方案

  解决方法一：
  ```
  import React from 'react'
  import { AutoSizer } from 'react-virtualized/dist/commonjs/AutoSizer'
  import { List as VList } from 'react-virtualized/dist/commonjs/List'
  import { CellMeasurerCache, CellMeasurer } from 'react-virtualized/dist/commonjs/CellMeasurer'

  class StudentList extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            list: []
        }
    }
    measureCache = new CellMeasurerCache({
        fixedWidth: true,
        minHeight: 58
    })
    getList = () => {
        api.getList.then(res => {
            this.setState({
                list: res
            })
        })
    }
    componentDidMount() {
        this.getList()
    }
    render() {
        const { list } = this.state  
        const renderItem = ({ index, key, parent, style }) => {
            return (
                <CellMeasurer cache={this.measureCache} columnIndex={0} key={key} parent={parent} rowIndex={index}>
                    <Student key={key} student={list[index]} />
                </CellMeasurer>
            )
        }
        return (
            <div style={{height: 1000}}>
                <AutoSizer>
                    {({ width, height }) => (
                        <VList
                            ref={ref => this.VList = ref}
                            width={width}
                            height={height}
                            overscanRowCount={10}
                            rowCount={list.length}
                            rowHeight={this.getRowHeight}
                            rowRenderer={renderItem}
                            deferredMeasurementCache={this.measureCache}
                            rowHeight={this.measureCache.rowHeight}
                        />
                    )}
                </AutoSizer>
            </div>
        )
    }
  }

  ```

  解决方法二：过react-height或者issue中提到的通过计算回调的方法解决, 以使用react-height为例:

  ```
  import React from 'react'
import { AutoSizer } from 'react-virtualized/dist/commonjs/AutoSizer'
import { List as VList } from 'react-virtualized/dist/commonjs/List'
import ReactHeight from 'react-height'

class StudentList extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            list: []
            heights = []
        }
    }
    getList = () => {
        api.getList.then(res => {
            this.setState({
                list: res
            })
        })
    }
    componentDidMount() {
        this.getList()
    }
    handleHeightReady = (height, index) => {
        const heights = [...this.state.heights]
        heights.push({
            index,
            height
        })
        this.setState({
            heights
        }, this.vList.recomputeRowHeights(index))
    }
    getRowHeight = ({ index }) => {
        const row = this.heights.find(item => item.index === index)
        return row ? row.height : 100
    }
    render() {
        const { list } = this.state  
        const renderItem = ({ index, key, style }) => {
            if (this.heights.find(item => item.index === index)) {
                return <Student key={key} student={list[index]} style{style} />
            }
            return (
                <div key={key} style={style}>
                    <ReactHeight
                        onHeightReady={height => {
                            this.handleHeightReady(height, index)
                        }}
                    >
                        <Student key={key} student={list[index]} />
                    </ReactHeight>
                </div>
            )
        }
        return (
            <div style={{height: 1000}}>
                <AutoSizer>
                    {({ width, height }) => (
                        <VList
                            ref={ref => this.VList = ref}
                            width={width}
                            height={height}
                            overscanRowCount={10}
                            rowCount={list.length}
                            rowHeight={this.getRowHeight}
                            rowRenderer={renderItem}
                        />
                    )}
                </AutoSizer>
            </div>
        )
    }
}


  ```