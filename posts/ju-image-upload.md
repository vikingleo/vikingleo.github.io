---
title: 'ju-image-upload'
date: 2021-01-06 16:32:23
tags: [uniapp,uni-ju]
published: true
hideInList: false
feature: 
isTop: false
---
## 简介

多图上传组件，实现：

1. 按行均分显示
2. 多图片上传
3. 选择模式和多选模式

## 适用端


| 端         | 支持 |
| ---------- | ---- |
| Web        | √    |
| 微信小程序 | √    |

## 依赖

1. 微信公众号网页开发请自行引入`js-sdk`，并注入`Vue.prototype.$wx`，名称可自定，但要改动组件内调用的`$wx`
2. `/static` 自带的iconfont，也可以自行替换

## 调用

```js
import ImageUpload from '@/components/ju-image-upload/ju-image-upload.vue'
export default{
	components:{
		ImageUpload
	}
}
```

```html
<image-upload 
    :src="src" 
    count="2" 
    text="点击就上传" 
    @select="onTest" 
    @cancel-select="onTest" 
    @choose="onTest" 
    @remove="onTest" 
    @preview="onTest"
    @control="onTest" 
    @cancel-control="onTest" />
```

## 属性
<!-- more -->


| 属性            | 类型                    | 必填 | 默认          | 可选     | 说明                                                                                       |
| --------------- | ----------------------- | ---- | ------------- | -------- | ------------------------------------------------------------------------------------------ |
| type            | String                  | -    | -             | 'select' | 图片列表的类型                                                                             |
| selectMore      | Boolean                 | -    | false         | -        | 是否可多选，type="select"时生效                                                            |
| src             | String/Array            | -    | -             | -        | 图片列表，可作为默认图片显示                                                               |
| rowCount        | String/Number           | -    | 0             | -        | 每列显示个数（加上添加按钮），如果设置大于0，则全部变成正方形，设置的`height`和`width`无效 |
| gutter          | String(Number) / Number | -    | 10            | -        | 两个图片框之间的间距                                                                       |
| width           | String                  | -    | 200rpx        | -        | 宽度，受`rowCount`影响                                                                     |
| height          | String                  | -    | 200rpx        | -        | 高度，受`rowCount`影响                                                                     |
| icon            | String                  | -    | -             | -        | 自定义添加按钮的icon，填写完整icon，例如 `icon="app-icon icon-home"`，也可以加额外的class  |
| text            | String                  | -    | -             | -        | 添加按钮的文本，默认只有图标                                                               |
| backgroundColor | String                  | -    | '#ffffff'     | 颜色值   | 添加按钮的背景色                                                                           |
| mode            | String                  | -    | 'scaleToFill' | -        | 和uni提供的`image`的`mode`匹配                                                             |
| param           | Object                  | -    | null          | -        | 额外参数，每个操作回调都会返回，例如form中，指明field是哪个：:param="{field:'idCard'}"}    |
| count           | String / Number         | 1    | -             | 1-9      | 选择图片总数,所选图片到达数量，隐藏添加按钮                                                |
| chooseCount     | String\|Number          | -    | 9             | 1-9      | 单次选择图片数量                                                                           |
| color           | String                  | -    | #ccc          | 颜色值   | 添加按钮的字体颜色                                                                         |
| upload          | Boolean                 | -    | false         | -        | 自动上传到服务器，需要根据实际业务修改                                                     |
| uploadUrl       | String                  | -    | -             | -        | 已包含我自己到上传地址，需要根据实际业务修改                                               |

## 事件

> 所有方法会返回Object，其中包含数据的字段。

### 可能返回值

| 名称    | 数据类型 | 说明                                                      |
| ------- | -------- | --------------------------------------------------------- |
| param   | Object   | 所有方法都返回。回传页面传过来的param字段，用于方法后操作 |
| urls    | Array    | 所有方法都返回。当前所有的图片路径                        |
| files   | Array    | 当前选择的本地图片、serverId、localIds、tempFilePaths     |
| current | String   | 当前图片地址                                              |
| index   | Number   | 当前图片在urls中都索引                                    |

### 方法说明

| 名称           | 返回字段（包含默认返回）        | 说明                                                                       |
| -------------- | ------------------------------- | -------------------------------------------------------------------------- |
| choose         | `files`                         | 选择图片后，如果用微信公众号的js-sdk，则此事件将会在`wx.uploadImage`后执行 |
| upload         | `current_upload`                | 开启自动上传时，图片上传后事件                                             |
| control        | `current`                       | 点击单张图片弹出操作面板后的事件                                           |
| cancel-control | `current`、 `index`             | 取消操作面板后                                                             |
| preview        | `current`、 `index`             | 预览打开后                                                                 |
| remove         | `current`、 `index`             | 删除单张图片后                                                             |
| select         | `current`、 `index`、`selected` | 选择单张图片后                                                             |
| cancel-select  | `current`、 `index`、`selected` | 取消选择后                                                                 |

## slot

默认slot

**注意：** 设置slot时，属性`icon`和`text`无效

```html
<image-upload icon="app-icon icon-home" text="点我上传">
    <!--这里带了slot，如果在此设置，则属性上的icon和text无效-->
    <view >slot示例</view>
</image-upload>
```

## 结尾

工作中顺带捋出来的组件，可能有些许bug，多多包涵。

## TODO

1. 图片多选
2. 选择后先展示，后上传，改动`upload`：`before` , `after` , `null`