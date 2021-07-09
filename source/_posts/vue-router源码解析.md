---
title: vue-router源码解析
date: 2021-01-07 12:29:44
tags:
  - vue
categories:
  - js
---

### 手写vue-router插件

##### 1.作为一个插件存在，实现VueRouter类和install方法

##### 2.定义两个全局组件router-link,和router-view

##### 3.监控url变化

##### 4.创建响应式的current属性 

##### 5.路由映射表的创建

{% codeblock %}
// 1.作为一个插件存在，实现VueRouter类和install方法
let Vue;
class XXXVueRouter {
    constructor(options) {
        this.$options = options
        //4.创建响应式的current属性 （可以通过Vue.set()方法设置响应式数据，util设置也可以）
        Vue.util.defineReactive(this,'current','/')
        // Vue.set(this,'current','/')
        //3.此处可以监控url变化
        window.addEventListener('hashchange', () => {
            console.log(this)
            this.onHashChange()
        }
        )
        window.addEventListener('load', () => {
            console.log(this)
            this.onHashChange()
        }
        ) 
        //5.路由映射表的创建
        this.routeMap = {}
        options.routes.forEach(route => {
            this.routeMap[route.path] = route
        })
    }
    onHashChange() {
        console.log(window.location.hash);
        this.current = window.location.hash.slice(1)
    }
}
XXXVueRouter.install = function (_Vue) {
    Vue = _Vue;

    Vue.mixin({
        beforeCreate(){
            if(this.$options.router) {//有router即根实例
                Vue.prototype.$router = this.$options.router
            }
        } 
    })
    //2.定义两个全局组件router-link,和router-view
    Vue.component('router-link',{
        props: {
            to: {
                type: String,
                required: true
            }
        },
        render:function (h) {
            //此处不能使用我们的template去渲染元素，因为running-time-only运行时环境下没得编译器
            return h('a',{attrs: {href: '#'+this.to}},this.$slots.default)
        }
    })
    Vue.component('router-view',{
        render:function (createElement) {
            const {routeMap,current} = this.$router
            const component = routeMap[current].component || null
            return createElement(component)
        }
    })
}
export default XXXVueRouter

{% endcodeblock %}