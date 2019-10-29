# 十二、功能优化

## 处理 token 过期

- 为什么 token 过期时间这么短？
  - 为了安全
- 过期了怎么办？
  - 通过登录页面重新登录获取 token
  - 使用 refresh_token 重新获取新的 token



![1567481874811](./assets/1567481874811.png)

```js
/**
 * 请求模块：封装了 axios
 */
import axios from 'axios'
import JSONbig from 'json-bigint'
// 《使用拦截器统一添加 token》2. 加载容器
// 在非组件模块中访问 Vuex 容器，那就谁用谁就直接加载
// 这里加载得到的 store 和组件中的 $store 是一个东西
import store from '@/store'
import router from '@/router'

// 创建 axios 实例
// axios.create 的作用是克隆一个 axios 实例，它的作用和 axios 是一样的
// 假如一个应用中有多个不同的后台接口路径
//      http://api.a.com
//      http://api.b.com
// 当然，不仅仅是这个 baseURL，还有例如拦截器等都可以独立
const request = axios.create({
  baseURL: 'http://ttapi.research.itcast.cn/'
})

const refreshTokenReq = axios.create({
  baseURL: 'http://ttapi.research.itcast.cn/'
})

// 配置处理后端返回的数据中包含超出 JavaScript 安全整数范围问题
request.defaults.transformResponse = [function (data) {
  // 假如 data 不是标准的 JSON 格式的字符串
  try {
    // data 数据可能不是标准的 JSON 格式字符串，否则会导致 JSONbig.parse(data) 转换失败报错
    return JSONbig.parse(data)
  } catch (err) {
    // 无法转换的数据直接原样返回
    return data
  }
}]

// 配置 axios
// 请求拦截器
// 响应拦截器
// 。。。。

/**
 * 请求拦截器
 * 使用拦截器统一添加 token
 */
// 《使用拦截器统一添加 token》1. 添加拦截器
// 注意：为 request 添加拦截器
request.interceptors.request.use(function (config) {
  // Do something before request is sent

  // 统一在请求头中添加 token，名字，数据
  // // 《使用拦截器统一添加 token》3. 添加 token
  const { user } = store.state
  if (user) {
    // 注意：Bearer 后面有一个空格，这是后端要求的数据格式
    config.headers.Authorization = `Bearer ${user.token}`
  }
  return config
}, function (error) {
  // Do something with request error
  return Promise.reject(error)
})

/**
 * 响应拦截器
 */
request.interceptors.response.use(function (response) {
  // Any status code that lie within the range of 2xx cause this function to trigger
  // Do something with response data
  return response
}, async error => {
+  const { user } = store.state
  // 如果 401
+  if (error.response && error.response.status === 401) {
    // 判断是否有 refresh_token

    // 如果没有 refresh_token，直接跳转登录页
+    if (!user || !user.refresh_token) {
      // 跳转到登录页
+      router.push({
+        name: 'login'
+      })
+      return
+    }

    // 如果有，就请求获取新的 token
    //    把新的 token 更新到容器中
    //    原来过期的请求重新发出去
+    try {
      // 这里务必单独创建一个请求对象来请求刷新 token
      // 不要使用 request 请求对象，因为拦截器里面的代码不一样
+      const { data } = await refreshTokenReq({
+        method: 'PUT',
+        url: '/app/v1_0/authorizations',
+        headers: {
+          Authorization: `Bearer ${user.refresh_token}`
+        }
+      })

      // 刷新 token 成功，将 token 重新存储
+      store.commit('setUser', {
+        token: data.data.token, // 重新获取的最新 token
+        refresh_token: user.refresh_token // 还是原来的
+      })

      // 把之前失败的请求重新发出去
+      return request(error.config)
    } catch (err) {
      // 刷新 token 都失败了，甭想了 ，直接跳转到登录页
+      router.push({
+        name: 'login'
+      })
    }
  }
  return Promise.reject(error)
})

// 导出请求对象
export default request

```



## 登录成功跳转回原来页面

首先在响应拦截器中：

```js
/**
 * 响应拦截器
 */
request.interceptors.response.use(function (response) {
  // Any status code that lie within the range of 2xx cause this function to trigger
  // Do something with response data
  return response
}, async error => {
  const { user } = store.state
  console.log(router.currentRoute) // 这个对象和组件中的 $route 是一个东西
  // 如果 401
  if (error.response && error.response.status === 401) {
    // 判断是否有 refresh_token

    // 如果没有 refresh_token，直接跳转登录页
    if (!user || !user.refresh_token) {
      // 跳转到登录页
      router.push({
        name: 'login',
        // 固定的使用动态
        // 例如 /user/1 /user/2 /user/3 /user/xxx ，不允许出现 /user
        // 动态路由：
        // 路由路径：/xxx/:xxx
        // 传参：params: { 名字: 值 }
        // 获取：$route.params.xxx

        // 可选的，就使用 query
        // /login /login?foo=bar
        // 这个参数不用修改路由之前的路径，仅此而已
        // 传递这样穿，获取的时候使用 $route.query.xxx 来获取
+        query: {
+          redirect: router.currentRoute.fullPath
+        }
      })
      return
    }

    // 如果有，就请求获取新的 token
    //    把新的 token 更新到容器中
    //    原来过期的请求重新发出去
    try {
      // 这里务必单独创建一个请求对象来请求刷新 token
      // 不要使用 request 请求对象，因为拦截器里面的代码不一样
      const { data } = await refreshTokenReq({
        method: 'PUT',
        url: '/app/v1_0/authorizations',
        headers: {
          Authorization: `Bearer ${user.refresh_token}`
        }
      })

      // 刷新 token 成功，将 token 重新存储
      store.commit('setUser', {
        token: data.data.token, // 重新获取的最新 token
        refresh_token: user.refresh_token // 还是原来的
      })

      // 把之前失败的请求重新发出去
      return request(error.config)
    } catch (err) {
      // 刷新 token 都失败了，甭想了 ，直接跳转到登录页
      router.push({
        name: 'login',
+        query: {
+          redirect: router.currentRoute.fullPath
+        }
      })
    }
  }
  return Promise.reject(error)
})

// 导出请求对象
export default request

```

然后在登录成功以后：

```js
...

// 登录成功，跳转到首页
const { redirect } = this.$route.query
this.$router.push(redirect || '/')
```



## 解决移动端点击 300ms 延迟

有些移动端浏览器的点击事件有 300ms 延迟问题，为了保证都没事儿，我们建议把 fastclick 配置到你的项目中。

安装 FastClick：

```bash
npm i fastclick
```

在 `main.js` 中：

```js
...
import fastClick from 'fastclick'

fastClick.attach(document.body)

```

## 移动端 REM 适配

详见 `一、项目初始化`。

## 组件缓存

> 参考文档：
>
> - [在动态组件上使用 `keep-alive`](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#在动态组件上使用-keep-alive)

```html
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  动态组件
</keep-alive>
```

> 注意：`<keep-alive>` 要求被切换到的组件都有自己的名字，不论是通过组件的 `name` 选项还是局部/全局注册。

### 禁用组件缓存

> 参考文档：
>
> - https://cn.vuejs.org/v2/api/#keep-alive

手动销毁的方式需要修改组件内部的代码，这里介绍一种更灵活的方式来配置哪些组件缓存，哪些组件不缓存。

`include` 和 `exclude` 属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：

```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```

匹配首先检查组件自身的 `name` 选项，如果 `name` 选项不可用，则匹配它的局部注册名称 (父组件 `components` 选项的键值)。匿名组件不能被匹配。



