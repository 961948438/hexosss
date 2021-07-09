---
title: vuex的使用
date: 2020-12-31 22:24:30
tags:
  - vue
categories:
  - js
---

（两个指令的区别）
	vue add vuex:该方法添加插件会改变原有项目结构，即对新添加的插件进行初始化操作；
	npm install vuex 该方法只下载第三方插件或者模块，不做初始化资源的配置；

使用场景/好处：vuex集中管理共享的数据，易于开发和后期维护
	        能够高效的实现组件之间的数据共享，提高开发效率
	        储存再vuex中的数据都是响应式的，能够实时保持数据和页面的同步
	        组件之间需要共享的数据储存再我们的vuex中，组件私有数据储存在我们的data中即可
	坏处：条条框框特别多，当页面应用程序组件树不大时还不如通过（组件树/事件总栈）进行数据访问
	

1.vuex是一个专门为vue.js应用程序开发的状态管理模式，它应用集中式储存管理应用的所有组件的状态，并以对应的
规则保证状态以可预测的方式发生变化； 通过vuex可以很方便的实现组件之间的数据共享；

2.vue add vuex 下载我们的插件,默认会生成store文件夹；

3.import store from ‘./store/index.js’引入模块，该添加插件方法会默认配置

4.访问store仓库下state数据源的方法有以下两种：
	1==》
	{% codeblock %}
	this.$store.state[数据名]
	{% endcodeblock %}
	2==》
	{% codeblock %}
	import {mapstate} from "vuex"  //按需导入mapstate方法
	        computed: {
	        	...mapstate([数据名1，数据名2])  //（...）是展开运算符
	        }//组件中即可直接使用数据名
	{% endcodeblock %}
5.通过mutations属性下定义的方法修改，组件中不可直接修改共享状态state（可以但没必要，不方便管理数据），
	1==》
	{% codeblock %}
		mutations: {
	          login (state) {
	            state.isLogin = true
	             },
 	            add: function (state) {
  	             state.count++
	            },
  	           sub: function (state,step) {
  	             state.count -= step
  	           }
  	         },`
	         在组件中通过this.$store.commit(方法名，参数)通知mutations去修改数据
	{% endcodeblock %} 
	2==》
	{% codeblock %}
		import {mapMutation} from 'vuex'
	          methods: {
	              ...mapMutation(['subN'，'方法2'])  //简单理解就是将vuex中的mutation里方法映射过来成为本身组件
		subcountt: function (msg) {
          		      this.subN(msg)//映射为组件本身的方法之后就可以向调用自有方法一样调用mutations里的方法
       		 }`
        }
		{% endcodeblock %} 
6.mutation中不支持直接进行异步操作，在store里执行异步操作依赖于action（虽然action还是通过触发mutation间接变更数据）
	1==》在store对象的actions属性下定义我们的异步任务：
	          {% codeblock %}
			  addAsync(context,接受携带的数据) {			//context暂时理解为仓库store实例对象，具体不也不知
	          	setTimeout(() => {
	                       context.commit('mutations里的方法') //此处不能直接访问state，通过提交mutation才行，vuex官方说的
	                },1000)
	           }`
	       在我们的组件的mothods里定义好的方法中发起actions
	          `methods： {
	              handle: function(){
	                  this.$store.dispatch('addAsync',携带的数据)//addAsync为action里的异步任务
	              }
	          }
			  {% endcodeblock %}  
	2==》从vuex按需导入我们的mapactions函数，再将指定的actions函数映射为组件自有的methods函数
	{% codeblock %}
	         import {mapactions} from ‘vuex’
	         methods： {
	        	...mapActions(['actions中异步任务1','actions中的异步任务2'])//将全局中的某些函数映射成为自身方法
	        	subAsyncto: function () {
	        	    this.subAsync()
	                    //<button @click="subAsyncto">async-1</button>
	                    //差点调用函数自身进入死循环了，此处应该和actions里的异步任务函数区别开来
	        	}`
    {% endcodeblock %}            
7.store实例化对象中的getter方法用于对state中的数据进行加工输出，但是该方法并不会改变数据源，使用getter的方法有
	{% codeblock %}
	getters: {
	    teststring: function (state) {
	        return '当前的数据('+state.msg+')已经加工过s了'
 	   }
 	 }
	this.$store.getters.teststring
	import {mapGetters} from 'vuex'
	computed: {
	    ...mapGetters(['testing'])
    }
	{% endcodeblock %}
	
	
