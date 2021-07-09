---
title: vue中组件开发中大量使用的dispatch和broadcast
date: 2021-01-04 20:43:37
tags:
  - vue
categories:
  - js
---

1.在使用 Element 过程中组件通信大量使用 dispatch 和 broadcast 两个方法，之vue2 组件通信 也提到过 vue2 中
取消了 $dispatch 和 $broadcast 两个重要的事件，而 Element 重新实现了这两个函数，通过下方可简单认识一下这两个方法

2.
{% codeblock %}
"use strict";
exports.__esModule = true;
function _broadcast(componentName, eventName, params) {
  this.$children.forEach(function (child) {
    var name = child.$options.componentName;
    if (name === componentName) {
      child.$emit.apply(child, [eventName].concat(params));
    } else {
      _broadcast.apply(child, [componentName, eventName].concat([params]));
    }
  });
}
exports.default = {
  methods: {
    dispatch: function dispatch(componentName, eventName, params) {
      var parent = this.$parent || this.$root;
      var name = parent.$options.componentName;
      while (parent && (!name || name !== componentName)) {
        parent = parent.$parent;
        if (parent) {
          name = parent.$options.componentName;
        }
      }
      if (parent) {
        parent.$emit.apply(parent, [eventName].concat(params));
      }
    },
    broadcast: function broadcast(componentName, eventName, params) {
      _broadcast.call(this, componentName, eventName, params);
    }
  }
};
{% endcodeblock %}
3分析
	1）dispatch 和 broadcast 方法都需要 3 个参数，componentName 组件名称， eventName 事件名称， params 传递的参数。
	2）dispatch 方法会寻找所有的父组件，直到找到名称为 componentName 的组件，调用其 $emit()事件。broadcast 方法则是遍历当前组件的所有子组件，找到名称为 componentName 的子组件，然后调用其 $emit() 事件。
4.vue 组件通信方式总结
父组件向子组件传递信息使用 props down
子组件向父组件传递信息使用 event up
其它关系类型组件通信使用 global event bus
大型 SPA 组件之间通信使用 Vuex 管理组件状态
使用 Element 下 emitter.js 中的 dispatch 和 broadcast 做事件定向传播


