---
title: reacc中通过context传递数据
date: 2021-01-22 14:13:49
tags:
  - react_context
categories:
  - js
---
####  Context 传递数据
Context提供了一种在组件之间共享值的方式，而不必显式地通过组件树的逐层传递 props。
##### 1. 创建一个Context对象
const ThemeContext = React.createContext();
##### 2.Provider（订阅者）：Provider 接收一个 value 属性，传递给消费者组件。
注意：这个value名字是固定的，不能改变，否则后面的消费者组件中获取不到属性值
{% codeblock %}
<ThemeContext.Provider value={this.state}>
{% endcodeblock %}
##### 3.Consumer（消费者）：这里，React 组件也可以订阅到 context 变更
{% codeblock %}
<ThemeContext.Consumer>
   {
     value => <div>{value.name}</div>
   }
</ThemeContext.Consumer>
{% endcodeblock %}
##### 4.Context 完整使用代码：
{% codeblock %}
import React, { Component } from 'react';

// 创建一个Context对象
const ThemeContext = React.createContext();
function ThemeBtn(props) {
  return (
    <ThemeContext.Consumer>
      {
        value => <div>{value.name}</div>
      }
    </ThemeContext.Consumer>
  )
}

function ToolBar(props) {
  return <ThemeBtn></ThemeBtn>
}

class ContextSimple extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: '按钮'
    };
  }
  render() {
    return (
      <ThemeContext.Provider value={this.state}>
        <ToolBar></ToolBar>
      </ThemeContext.Provider>
    );
  }
}

export default ContextSimple;
{% endcodeblock %}