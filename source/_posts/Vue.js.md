---
title: Vue组件传值
tags:
  - vuejs
originContent: ''
categories:
  - Vue.js
toc: true
date: 2019-01-29 16:47:52
---

# 说明

最近需要制作这么一个业务：

使用element-ui的dialog，封装为一个登录面板组件，在`Navbar.vue`组件中调用，当点击`Navbar`中的`登录`按钮时，弹出登录面板，点击面板中的`取消`按钮，或者点击背景、点击自带关闭按钮时，关闭登录面板。

但直接调用`props`内的属性作为`visible.sync`的值，不管如何在点击自带关闭按钮或背景关闭时，就会报错：

::: danger Console错误提示
[Vue warn]: Avoid mutating a prop directly since the value will be overwritten whenever the parent component re-renders. Instead, use a data or computed property based on the prop's value. Prop being mutated: "visible"
:::

先上一篇我查到的类似错误的文章：[https://www.cnblogs.com/yeqrblog/p/9141701.html](https://www.cnblogs.com/yeqrblog/p/9141701.html)

**先感谢大佬！**

和大佬第二个方法有点区别，我用到了`watch`，现在我的做法是：
1. 用子组件自定义一个data作为`visible.sync`值，并监听`props`的值
2. 父组件只管传入值，并在组件调用上`v-on`一个事件，暂定为`close`
3. 子组件中，给dialog自带的`close`方法绑定一个关闭方法，关闭方法使用`this.$emit('close',false)`触发父组件上绑定在调用子组件上的`v-on:close`事件


## 知识点
1. vue components
2. props:[]
3. this.$emit()

# 父组件
```html
<template>

<div>
 <SignPanel :is-show="signPanelActive" @close="close"></SignPanel>
</div>

</template>
<script>
import SignPanel from './SignPanel'

export default {
    data() {
            return {
                signPanelActive: false,
            }
        },
        components: {
            SignPanel
        },

        methods: {
            close() {
                this.signPanelActive = false
            },
        }
}
</script>
```

# 子组件

```html
<template>
    <!--登录注册面板-->
    <el-dialog 
        class="bg-third cm-sign-panel" 
        title="" 
        :visible.sync="active"  
        @close="close">

        <p>这里是登录面板</p>

        <div slot="footer" class="dialog-footer">
            <el-button @click="close">关闭</el-button>
        </div>
    </el-dialog>
</template>
<script lang="ts">
    export default {
        props: {
            isShow: Boolean
        },
        data() {
            return {
                active: false
            }
        },
        watch: {
            isShow: function () {
                this.active = this.isShow
            },
        },
        methods: {
            close() {
                this.$emit('close', false)
            },
        }
    }
</script>
```

# 总结

该做法是子组件只负责给父组件丢回绑定事件和返回值，其他属性由自身`watch`， 打完收工！