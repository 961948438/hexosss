---
title: element ui 的理解
date: 2020-11-03 23:39:46
tags:
  - vue ui
categories:
  - js
---
---


好处：对深入vue底层源码和ui组件库的开发有一定的帮助

1.通过自定事件总栈深入理解vue事件总栈
{% codeblock %}
class Buf {
  constructor () {
    this.callbacks = {}
  }
  $on(name,fn) {
    this.callbacks[name] = this.callbacks[name] || []
    this.callbacks[name].push(fn)
  }
  $emit(name,args){
    if(this.callbacks[name]) {
      this.callbacks[name].forEach(ele => {
        ele(args)
      });
    }
  }
}
Vue.prototype.$buf = new Buf() //将buff的实例化对象挂载到vue的原型上
{% endcodeblock %}

2.通过$parents和$children组件访问属性完成组件间交互
{% codeblock %}
this.$parent.$emit('parentto-child2','我通过父元素找到了child2') 
//在当前组件内通过组件访问属性$parent获取父元素组件实例对象，分发一个prentto-child2事件并且传参
this.$children[0].todo() 
//通过$children组件访问属性获取到子组件，即可使用子组件下定义的方法
this.$children[0].$emit('link-child1','父元素给child1发的一些信息')
//通过$children拿到的数组不保证顺序的，其不是按template模板结构的顺序排列的
{% endcodeblock %}
3.$attrs和$listeners实现组件间交互
//父组件通过属性绑定的方式添加给子组件，但是子组件并未通过props的方式接受数据被放置于$attrs对象下
{% codeblock %}
<div>{ {$attrs.msg} }</div>
<h2 v-on="$listeners">123</h2>
//通过$listeners可以监听给该组件绑定并触发事件的组件对象
{% endcodeblock %}
4.通过provide/reject对象实现任意辈分组件间访问
{% codeblock %}
//通过provide可以向外界组件提供数据接口，通过inject注入数据
//该方法也可实现任意组件之间的数据访问，在组件开发时经常使用
provide:function () {
    return {
      fo: '0000ss',
      comp: this
    }
  },
inject:['fo','comp'],
{% endcodeblock %}
5.插槽的用法
{% codeblock %}
//template是slot组件的模版
<slot-com>
            <template v-slot:header>sdafdsa</template>
            <template>sdafdsafdsafdsafdsa</template>
            <template v-slot:footer="fc">{ {fc.msg} }</template>
</slot-com>

<template>
    <div>
        <div class="header">
            <slot name="header"></slot>
        </div>
        <div class="content">
            <slot ></slot>
        </div>
        <div class="footer">
            <!-- 通过作用域插槽，父元素可以拿到子元素的值 -->
            <slot name="footer" v-bind:msg='msg'></slot>
        </div>
    </div>
</template>
<script>
    export default {
        data: function () {
            return {
                msg: '我是子组件想传递给父组件的数据'
            }
        }
    }
</script>
{% endcodeblock %}
6熟悉以上用法就可以直接手撸element ui组件库了，深入element ui组件库源码；
1）从简单的kinput组件开始封装
{% codeblock %}
<template>
    <div>
        <input :type="type" v-bind:value="value" @input="oninput">
        <p>
            { {value} }
        </p>
    </div>
</template>

<script>
    export default {
        data: function () {
            return {
            }
        },
        props: {
            value: {
                type: String,
                default: ''
            },
            type: {
                type: String,
                default: 'text'
            }
        },
        methods: {
            oninput: function (e) {
                this.$emit('input',e.target.value)
            }
        }
    }
</script>

<style lang="scss" scoped>

</style>
{% endcodeblock %}
2）封装kinput组件的父组件kformitem
{% codeblock %}
<template>
    <div>
        <slot name="default"></slot>
        <label v-if="label">{ {label} }</label>
        <p v-if="error">{ {error} }</p>
             <p>{ {form.rules} }</p>
    </div>
</template>

<script>
    export default {
        inject: ['form'],
        data:function () {
            return {
                error: ''
            }
        },
        props:{
            label: {
                type: String,
                default: ''
            }
        }
    }
</script>

<style lang="less" scoped>
</style>
{% endcodeblock %}
3）封装kformitem的父元素表单元素
{% codeblock %}
<template>
    <div>
        <slot name="default"></slot>
    </div>
</template>

<script>
    export default {
        provide: function () {
            return {
                form: this
            }
        },
        props: {
           model: {
               type: Object,
               required: true
           },
           rules: {
               type: Object
           }
        } 
    }
</script>

<style lang="less" scoped>

</style>
{% endcodeblock %}
4）这样一个简单的表单元素功能就封装好了；可以在我们组件中使用了
{% codeblock %}
<template>
    <div class="content">
       <kform v-bind:model='userInfo' v-bind:rules='rules'>
           <kinputitem>
                <kinput v-model="userInfo.username"></kinput>
            </kinputitem>
       </kform>
    </div>
</template>

<script>
    import kinput from '../components/kinput'
    import kinputitem from '../components/kformitem'
    import kform from '../components/kform'
    export default {
        components: {
            kinput,kinputitem,kform
        },
        data: function () {
            return {
                userInfo: {
                    username: 'todo',
                    password: '',
                },
                rules: {
                    username: [{requried: true, message: '请输入用户名称'}],
                    password: [{requried: true, message: '请输入密码'}]
                }
            }
        }
    }
</script>

<style lang="less" scoped>
.content{
    padding: 30px 50px;
}
</style>
{% endcodeblock %}