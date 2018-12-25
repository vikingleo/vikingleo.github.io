---
title: Nuxt.js使用axios、自定义拦截器和代理
tags:
  - nuxt.js
  - '@nuxtjs/axios'
  - proxy
  - ssr
originContent: |-
  # ssh拷贝文件
  ```
    在nuxt.js (ssr)中使用axios，或者自定义请求/返回的拦截器，以及配置跨域代理
  ```
categories:
  - Vue.js
toc: true
date: 2018-12-25 12:22:49
---


# @nuxtjs/axios使用
## 说明
**本文基于nuxt-create-app**

> nuxt.js中使用`@nuxtjs/axios`，其已封装了一些方法，例如：
```ts
import { AxiosInstance, AxiosRequestConfig } from 'axios'
import Vue from 'vue'

interface NuxtAxiosInstance extends AxiosInstance {
  $request<T = any>(config: AxiosRequestConfig): Promise<T>
  $get<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>
  $delete<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>
  $head<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>
  $options<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>
  $post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>
  $put<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>
  $patch<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>
}

declare module 'vue/types/vue' {
  interface Vue {
    $axios: NuxtAxiosInstance
  }
}
```

在不改变任何东西的情况下，直接在页面或组件中使用是可以的：
```js
 async getHomeSwiper() {
    try {
    
      // 默认拦截器返回response中的data
      let res = await this.$axios.$get('/index')
    } catch (e) {
      throw Error(e)
    }
}
```

## 使用自定义拦截器

**文档: [https://axios.nuxtjs.org/extend](https://axios.nuxtjs.org/extend)**

### 一、创建文件

先看官方说明
![image](https://note.youdao.com/yws/api/personal/file/F374023FB44D442AA53F95C309D1DE4E?method=download&shareKey=26ab2a90ca9ab183756085ceb9a6fcfa)

**其中说，这些回调是没必要有返回的，但我们或许可以根据需要做一些自定义的返回**

创建`plugins/axios.js`，内容如下：
```js
export default function({ $axios }) {
  //请求回调
  $axios.onRequest(config => {
    console.log('Making request to ' + config.url)
  })
  //返回结果回调
  $axios.onResponse(res => {
    // 自定义返回数据
    let { status, statusText, data } = res
    return { status, statusText, data }
  })
  //错误拦截器
  $axios.onError(error => {
    const code = parseInt(error.response && error.response.status)
    if (code === 400) {
      redirect('/400')
    }
  })
}
```

### 二、修改`nuxt.config.js`

```js
plugins:[
    { src: '~/plugins/axios', ssr: false }
]
```

### 三、使用和差异

#### 1、使用默认的`$get`方法
```js
let res=await this.$axios.$get('/')
// 此方法只会返回data，自定义的拦截器返回是对它木有影响的
```

#### 2、非默认方法
```js
let res=await this.$axios({
    url:'/'
})
// 此方法返回 {status:200,statusText:"ok",data:[]}，获取了我们自定义的拦截器返回数据
```

# 代理(proxy)，解决跨域问题

> 查了很多资料说要用`@nuxtjs/proxy`，但我安装最新版的nuxt.js，选择了axios以后，只有@nuxtjs/axios的情况下，使用`proxy`还是阔以的

```js
  axios:
    {
      // See https://github.com/nuxt-community/axios-module#options
      proxy: true,
      // prefix: '/mock',   //加入此，会自动加入前缀，否则得手动/mock/xxx
      credentials: true,
      debug:true,
    },

  proxy: {
    '/mock': {
      target: 'https://easy-mock.com',
      changeOrigin: true,
      pathRewrite: {
        '^/mock': ''
      }
    }
  },
```
## 使用
```js
let res=await this.$axios({
    url:'/mock/test'
})
```
至此，使用`nuxt-create-app`安装的项目，几乎不需要另外安装其他就可使用axios代理。

另外，其实mock是不用代理的，毕竟又没有跨域问题，这里一并处理，只是希望在代理处统一管理而已。

# 总结

其实还有更好的优化，但目前为止，这样是可以用的，以后优化会放出