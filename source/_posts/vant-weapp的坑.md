---
title: vant-weapp的坑
date: 2020-01-08 16:43:52
tags: 
    - 微信小程序
    - vant
    - UI框架
category: 微信小程序
---

## 描述

页面 2 使用该组件，页面 1 首次进入 2，正常，返回，再次进入页面 2，报错如下：

> Error: Expect END descriptor with depth 0 but get another

## 原因

微信调试基础库 2.6.x 后面的版本没有问题，引用网友的 issues：

> 其实从优化来说，数值比较比字符串比较的效率要高点吧
> 他并非是类型的比较，并非是只能数字比较，你调用其他你不在组件里面动态改动的字段来比较的话，他是没问题的。
> 测试后可以知道，此问题是在基础库 2.6.1 才会发现，如果是之前的基础库是没问题的，
> 从另外一个角度可以知道
> steps 组件里面 formatSteps 这个方法中 step.status = \_this.getStatus(index); 是改变对象的属性的，而该属性和页面的 wx:if 关联，形成了一个双向绑定的过程，在改动过程中页面已经进行绑定，如果把这句语句去掉，页面是不会报错的，或者在

```
this.setData({
steps: steps
});
```

改成

```js
this.setData(
  {
    steps: steps,
    _steps: steps
  }
);
```

> 然后页面渲染使用的是\_steps 这个对象去渲染（要先判断这个值是否存在 `wx:if="{{__steps}}"）`,这样的话，页面是不会出问题的。<br>与其不停的改动属性值影响到页面渲染，不如直接通过
> `index !== active`来进行判断

## 解决方案：

https://github.com/youzan/vant-weapp/issues/1336

找到组件 `steps/index.wxml`如下位置

```js
view class="van-step__circle" wx:if="{{ item.status !== 'process' }}" style="{{ item.status === 'finish' ? 'background-color: ' + activeColor : '' }}"
```

改为

```js
view class="van-step__circle" wx:if="{{ index !== active }}" style="{{ item.status === 'finish' ? 'background-color: ' + activeColor : '' }}"
```
