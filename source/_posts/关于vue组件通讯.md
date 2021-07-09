---
title: 关于vue组件通信
date: 2020-7 -4 18:14:19
tags:
  - vue
categories:
  - js
---
## vue子父兄弟组件之间实现数据传递的方式


(区分标签内容传递和data数据传递的区别)
### 兄弟组件之间实现数据传递
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}
1） val hub=new Vue()实例化一个vue对象用做事件中心管理组件；
2）  hub.$emit('tom-event', 1); 再子组件的methods的方法中，通过$emit(事件名，data)方法触发事件管理中心下的指定事件
3） hub.$on('jerry-event',fn）再mounted声明周期钩子函数中给中心管理组件绑定事件和业务处理逻辑
4） hub.$off('tom-event'); 可以再任意一个组件中销毁事件中心管理组件的绑定事件；
{% codeblock %}
  <div id="app">
      <div>父组件</div>
      <div>
      <button v-on:click='handle'>销毁事件</button>
      </div>
      <test-tom></test-tom>
      <test-jerry></test-jerry>
  </div>
  var hub = new Vue();
      Vue.component('test-tom', {
        data: function(){
          return {
            num: 0
          }
        },
        template: 
          "<div>
            <div>TOM:{ {num} }</div>
            <div>
              <button v-on:click='handle'>点击</button>
            </div>
          </div>
          "
        ,
        methods: {
          handle: function(){
            hub.$emit('jerry-event', 2);
          }
        },
        mounted: function() {
          // 监听事件
          hub.$on('tom-event', (val) => {
            this.num += val;
          });
        }
      });
      Vue.component('test-jerry', {
        data: function(){
          return {
            num: 0
          }
        },
        template: `
          <div>
            <div>JERRY:{ {num} }</div>
            <div>
              <!-- <button v-on:click='handle'>点击</button> -->
            </div>
          </div>
        `,
        methods: {
          handle: function(){
            // 触发兄弟组件的事件
            hub.$emit('tom-event', 1);
          }
        },
        mounted: function() {
          // 监听事件
          hub.$on('jerry-event', (val) => {
            this.num += val;
          });
        }
      });
      var vm = new Vue({
        el: '#app',
        data: {
        },
        methods: {
          handle: function(){
            hub.$off('tom-event');
            hub.$off('jerry-event');
          }
        }
      });
{% endcodeblock %}
### 父组件向子组件传递数据的方式；
<hello-p title="我是父元素传递过来的值" content='我是父元素值过来的' v-on:enlarge-text='hang($event)'></hello-p>
1）title="我是父元素传递过来的值" 父组件通过添加属性的方式向子组件传递一个值；
2）props: ['title', 'content'], 子组件利用配置参数对象下的props属性接受传递过来的值并存储以便使用
/*****以下以局部组件为例子，全局组件方法为vue。components（组件名，配置参数）
{% codeblock %}
  components: {
                  'hello-p': {
                      props: ['title', 'content'],
                      data: function() {
                          return {
                              msg: 'hello - p'
                          }
                      },
                      template: `<div  v-on:click="$emit('enlarge-text',5)">
                      { {msg+"-- -- -- -- --"+title} }</div>`,
                      methods: {
                          fn: function(event) {
                              console.log(event)
                          }
                      }
                  }
  }
{% endcodeblock %}
### 子组件向父组件传递数据的方式
<div id="app">
        <div :style='{fontSize: fontSize + "px"}'>{ {pmsg} }</div>
        <menu-item :parr='parr' @enlarge-text='handle($event)'></menu-item>
</div>
1）父组件给子组件绑定一个自定义事件v-on:事件名：v-on:enlarge-text="handle($event)"
1）子组件通过$emit('触发事件名'，携带data)方法触发一个自定义事件
{% codeblock %}
Vue.component('menu-item', {
            props: ['parr'],
            template: `
        <div>
          <ul>
            <li :key='index' v-for='(item,index) in parr'>{ {item} }</li>
          </ul>
          <button v-on:click='$emit("enlarge-text", 5)'>扩大父组件中字体大小</button>
          <button v-on:click='$emit("enlarge-text", 10)'>扩大父组件中字体大小</button>
        </div>
      `
        });
        var vm = new Vue({
            el: '#app',
            data: {
                pmsg: '父组件中内容',
                parr: ['apple', 'orange', 'banana'],
                fontSize: 10
            },
            methods: {
                handle: function(val) {
                    // 扩大字体大小
                    this.fontSize += val;
                }
            }
});
{% endcodeblock %}