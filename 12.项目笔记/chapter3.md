# 三、Token 处理

- 什么是 Token？
- 为什么要存储 Token？

- 如何存储 Token？
  - 本地存储，方便其他地方获取
  - 最关键是为了防止刷新丢失。
- 如何给接口添加 token？
  - 要嘛在每个请求的地方添加 token
  - 要嘛为了方便，写到请求拦截器中统一添加



```js
var arr = []

点击登录
	arr = 请求数据()
```



如何存储：

- 为了方便获取：我们建议放到 Vuex 容器中
  - 组件中无论是直接通过 js 来获取还是直接在模板中获取都非常方便
  - $store
  - 如果只是纯粹的放到本地存储，那我在使用的时候，首先读取出来，然后放到 data 中，然后才能在模板中使用
- 为了防止刷新丢失：我们会把数据存储到本地存储

![1568949544263](assets/1568949544263.png)

## 使用 Vuex 容器存储 token

在 `store/index.js` 中：

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    user: null
  },
  mutations: {
    setUser (state, user) {
      state.user = user
    }
  },
  actions: {
  }
})

```

然后，在登录成功以后：

```js
...
import { mapMutations } from 'vuex'

async onLogin () {
  ...
  this.setUser(data.data)
}
```



## 持久化 token

Vuex 容器中的数据只是为了方便在其他任何地方能方便的获取登录状态数据，但是页面刷新还是会丢失数据状态，所以我们还要把数据进行持久化中以防止页面刷新丢失状态的问题。

前端持久化常见的方式就是：

- 本地存储
- Cookie



这里我们以使用本地存储持久化用户状态为例：

为了方便，这里先封装一个用于操作本地存储的存储模块。

创建 `src/utils/storage.js` 并写入以下内容：

```js
export const getItem = name => {
  return JSON.parse(window.localStorage.getItem(name))
}

export const setItem = (name, data) => {
  return window.localStorage.setItem(name, JSON.stringify(data))
}

export const removeItem = name => {
  window.localStorage.removeItem(name)
}

```



接下来就可以使用我们创建好的存储模块来完成对 token 数据的本地持久化存储了。

首先，在 `store/index.js` 容器中：

```js
import Vue from 'vue'
import Vuex from 'vuex'
import { getItem, setItem } from '@/utils/storage'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    // 初始化的时候从本地存储获取数据，没有就是 null
    user: getItem('user')
  },
  mutations: {
    setUser (state, user) {
      state.user = user
      // 存储数据的时候同时把数据也放到本地存储中
      setItem('user', state.user)
    }
  },
  actions: {

  }
})

```



## 使用请求拦截器统一添加 token

方式一：在每次请求的时候手动添加：

```js
axios({
  method: '',
  url: '',
  headers: {
  	token数据
  }
})
```

方式二：使用拦截器统一添加（推荐，更方便）

在 `utils/request.js` 中的拦截器中统一添加 token：

```js
...
// 非组件模块访问容器直接加载即可
// 这里得到的 store 和组件中访问的 this.$store 是一个东西
import store from '@/store'

// Add a request interceptor
// 注意：使用谁发送请求就给谁添加拦截器
// 这里我们使用使用的是 axios.create 出来的 request 对象来发送请求的，所以这里要给 request 设置拦截器（给 axios 设置无效）
request.interceptors.request.use(function (config) {
  // Do something before request is sent
  const { user } = store.state
  if (user) {
    // 配置 token 请求头
    // 注意 Authorization 是请求头的名字，不能乱写，由后端规定的，包括数据格式也不能乱写， 也是后端规定的
    config.headers.Authorization = `Bearer ${user.token}`
  }
  return config
}, function (error) {
  // Do something with request error
  return Promise.reject(error)
})

```



## 处理 token 过期（后面讲）

