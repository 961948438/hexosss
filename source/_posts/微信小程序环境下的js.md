---
title: 微信小程序环境下的js
date: 2021-01-01 23:41:10
tags:
  - wx api
categories:
  - js
---


1.json文件里不能注释，非主页面json文件可以覆盖app.json文件
                     页面里的配置内容只可以设置window下的一些配置
	    tabbar标签条中只能有2-5个标签，
2.微信小程序的开发语言是js但是不同于浏览器运行环境的javascript
	==》首先没有bom和dom对象模型，所以没有对象window和document对象，
	==》小程序运行环境的javascript拥有的额外的全局成员（方法）有：
	        App()    方法用于定义应用程序对象；
	        Page()   方法用于定义页面对象；
	        getApp()   方法用于获取全局应用程序对象
	        getcurrentPages()   方法用于获取当前页面的调用栈
	==》微信小程序中js的顶级对象是wx，微信小程序端还提供了一些核心的对象；
	        微信小程序环境的js是支持commonjs规范的（和nodejs的模块开发相似），但是不支持直接export导出对象
	        在0.10版本之后默认使用 babel 将开发者 ES6 语法代码转换为三端都能很好支持的 ES5 的代
3.语法知识：
	==》支持插值表达式，且插值表达式可以放在我们的类中动态管理类class = " into  
	{ {addclass} }",可以用在任意值的位置
	==》在小程序运行环境下的复选框checked =“false”（不会被当成布尔类型，使得复选框被勾选，），可以以插值表达式
	         表示一个false，checked = "{ {false} }"
	==》小程序中的循环语句为：
	{% codeblock %}
            todos: [
                { name: 'java', completed: false },
                { name: 'php', completed: true },
                { name: 'vue', completed: false },
                { name: 'javascript', completed: true ,
                { name: 'phpstudy', completed: false },
                { name: 'vuex', completed: false }
             ]
	        <view wx:for="{ { todos } }" wx:key="item">
    	        <checkbox checked="{ { item.completed } }" ></checkbox>
  	            <text>-------{ {item.name} }</text>
    	    </view>
	{% endcodeblock %}
	注意：在当前循环作用域内如有全局对象index和item会冲突访问不到，当前作用域内的index和item
	         会被当做循环的当前对象和当前索引；
	解决方法：  通过wx：for-item =”aaa“即为当前作用域遍历的对象起个别名为aaa，index也可以这样子起别名
6.小程序里提供的事件和window对象下的事件的区别；（小程序端没有鼠标概念，只有tap触摸概念）
	给元素添加触摸事件的方法：bindtap = "todo"//行内事件也不需要添加小括号，区别于pc端js
	默认情况下，小程序的开发也会冒泡事件,但是阻止冒泡的方式是通过catchtap事件，
	即bindtap会有事件冒泡机制，但是无法阻止，catchtap不会产生冒泡，
7.事件传参的方法也显著区别于浏览器运行环境下的js 
	不能通过函数传参的方法传递参数，语法不允许，实际上bindtap="type()"中的type()会被当做一个整体函数名
	有点莫名奇妙，但是不得不接受官方，
	同时也不可通过事件处理函数中的this指向调用该方法的对象即元素，因为在小程序的运行环境中，this会直接指向当前页面的pages对象，
	可以通过dataset属性获取到我们自定义的属性值（dataset相似于浏览器运行环境中的data-开头的自定义数据的集合）
	{% codeblock %}
    <button bindtap="type" data-name="张三" data-id="zhansan">456</button>
	console.log(e.target.dataset)
	{id: "zhansan", name: "张三"}
	id: "zhansan"
	name: "张三"
	__proto__: Object
    {% endcodeblock %}
	小程序没有双向数据绑定，所有的数据都是单向的，当通过插值表达式从逻辑层拿到数据到界面时，不再监听数据的变化，（不会像vue那样监听数据）
	只能再通知小程序这个框架去渲染数据
    `this.setData({ testmsg: e.target.value})`








