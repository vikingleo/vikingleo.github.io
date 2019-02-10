---
title: 微信小程序input监听获取值
tags:
  - 小程序
originContent: |-
  # 原理
  给dom设置属性，
  `data-model`：这是
  # js
  ```js
  /**
     * 页面的初始数据，其中form其实可以不写
     */
  Page({
      data: {
          name: null,
          age: null,
      },
      /**
       * 监听文本框
       */
      watchInput: function (e) {
          let that = this
          console.log(e)
          let model = e.currentTarget.dataset.model
          let value = e.detail.value
          that.setData({
              [model]: value
          })
      }
  })
  ```

  # wxml

  ```html
  <!-- 
  data-model:类型，对应data中的key
  -->
  <input placeholder="姓名" data-model="name" bindinput="watchInput">

  <input type="number" placeholder="年龄" data-model="age" bindinput="watchInput">
  ```
categories:
  - 小程序
toc: true
date: 2019-01-29 16:52:48
---

# 原理
给dom设置属性，
`data-model`：这是
# js
```js
/**
   * 页面的初始数据，其中form其实可以不写
   */
Page({
    data: {
        name: null,
        age: null,
    },
    /**
     * 监听文本框
     */
    watchInput: function (e) {
        let that = this
        console.log(e)
        let model = e.currentTarget.dataset.model
        let value = e.detail.value
        that.setData({
            [model]: value
        })
    }
})
```

# wxml

```html
<!-- 
data-model:类型，对应data中的key
-->
<input placeholder="姓名" data-model="name" bindinput="watchInput">

<input type="number" placeholder="年龄" data-model="age" bindinput="watchInput">
```