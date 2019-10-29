# 四、首页（上）

<img src="./assets/1566539328996.png" width="300">



## 创建页面组件并配置路由

### tabBar 视图组件

这里主要使用到的 Vant 组件：

- [Tabbar 标签栏](https://youzan.github.io/vant/#/zh-CN/tabbar)

创建 `views/tabbar/index.vue` 并写入以下代码：

```html
<template>
  <div>
    <!-- 路由出口 -->
    <router-view />
    <!-- /路由出口 -->

    <!-- 底部导航栏 -->
    <van-tabbar>
      <van-tabbar-item icon="home-o">首页</van-tabbar-item>
      <van-tabbar-item icon="search">问答</van-tabbar-item>
      <van-tabbar-item icon="friends-o">视频</van-tabbar-item>
      <van-tabbar-item icon="setting-o">我的</van-tabbar-item>
    </van-tabbar>
    <!-- /底部导航栏 -->
  </div>
</template>

<script>
export default {
  name: 'TabbarIndex'
}

</script>

<style>

</style>

```

然后在 `router/index.js` 中配置路由表：

```js
import Vue from 'vue'
import Router from 'vue-router'
import Login from '@/views/login'
+ import Tabbar from '@/views/tabbar'

Vue.use(Router)

export default new Router({
+   routes: [
+    {
+      path: '/',
+      component: Tabbar,
+      children: []
+    },
    {
      name: 'login',
      path: '/login',
      component: Login
    }
  ]
})

```

访问 `/` 进行测试。

### 首页视图组件

创建 `views/home/index.vue` 组件文件并写入以下代码：

```html
<template>
  <div class="home">首页</div>
</template>

<script>
export default {
  name: 'HomeIndex'
}
</script>

<style lang="less" scoped>
</style>

```

然后在 `router/index.js` 中配置路由表：

```js
import Vue from 'vue'
import Router from 'vue-router'
import login from '@/views/login'
import tabbar from '@/views/tabbar'
+ import Home from '@/views/home'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      component: tabbar,
      children: [
+        {
+          name: 'home',
+          path: '', // 默认子路由
+          component: Home
+        }
      ]
    },
    {
      name: 'login',
      path: '/login',
      component: Login
    }
  ]
})

```



最后访问 `/` 测试。

## 展示频道列表

### 布局

```html
<template>
  <div class="home">
    <!-- 导航栏 -->
    <van-nav-bar title="首页" />
    <!-- /导航栏 -->

    <!-- 频道列表 -->
    <van-tabs v-model="active">
      <van-tab title="标签 1">内容 1</van-tab>
      <van-tab title="标签 2">内容 2</van-tab>
      <van-tab title="标签 3">内容 3</van-tab>
      <van-tab title="标签 4">内容 4</van-tab>
    </van-tabs>
    <!-- /频道列表 -->

    <!-- 文章列表 -->
    <!-- /文章列表 -->
  </div>
</template>

<script>
export default {
  name: 'HomeIndex',
  data () {
    return {
      active: 2 // 控制当前激活的标签页
    }
  }
}
</script>

<style>

</style>

```

### 功能实现

1、封装请求接口

```js
/**
 * 获取所有频道列表
 */
export const getAllChannels = () => {
  return request({
    method: 'GET',
    url: '/app/v1_0/channels'
  })
}

```

2、在首页中请求调用

```js
+ import { getAllChannels } from '@/api/channel'

export default {
  name: 'HomeIndex',
  data () {
    return {
      active: 2, // 控制当前激活的标签页
+      channels: [] // 频道列表
    }
  },

  created () {
+    this.loadAllChannels()
  },

  methods: {
+    async loadAllChannels () {
+      const { data } = await getAllChannels()
+      this.channels = data.data.channels
+    }
  }
}
```

> 提示：代码写完以后在浏览器的调试工具中查看是否能够加载获取到 channels 数据，确保能正常获取到数据以后，再进行模板绑定。

3、拿到数据，渲染模板

```html
...
<!-- 频道列表 -->
<van-tabs v-model="active">
  <van-tab
    :title="channel.name"
    v-for="channel in channels"
    :key="channel.id"
  >{{ channel.name }}</van-tab>
</van-tabs>
<!-- /频道列表 -->
...
```

## 展示文章列表

### 布局

这里主要使用以下几个 Vant 组件：

- [List 列表](https://youzan.github.io/vant/#/zh-CN/list)
- [PullRefresh 下拉刷新](https://youzan.github.io/vant/#/zh-CN/pull-refresh)
- [Cell 单元格](https://youzan.github.io/vant/#/zh-CN/cell)

```html
<template>
  <div class="home">
    <!-- 导航栏 -->
    <van-nav-bar title="首页" />
    <!-- /导航栏 -->

    <!-- 频道列表 -->
    <van-tabs v-model="active">
      <van-tab
        :title="channel.name"
        v-for="channel in channels"
        :key="channel.id"
      >
        <!--
          标签页的内容：频道的文章列表
         -->
        <!-- 文章列表 -->
+        <van-list
+          v-model="loading"
+          :finished="finished"
+          finished-text="没有更多了"
+          @load="onLoad"
+        >
+          <van-cell
+            v-for="item in list"
+            :key="item"
+            :title="item"
+          />
+        </van-list>
        <!-- /文章列表 -->
      </van-tab>
    </van-tabs>
    <!-- /频道列表 -->
  </div>
</template>

<script>
import { getAllChannels } from '@/api/channel'

export default {
  name: 'HomeIndex',
  data () {
    return {
      active: 0, // 控制当前激活的标签页
      channels: [], // 频道列表
+      list: [],
+      loading: false,
+      finished: false
    }
  },

  created () {
    this.loadAllChannels()
  },

  methods: {
    async loadAllChannels () {
      const { data } = await getAllChannels()
      this.channels = data.data.channels
    },

+    onLoad () {
+      // 异步更新数据
+      setTimeout(() => {
+        for (let i = 0; i < 10; i++) {
+          this.list.push(this.list.length + 1)
+        }
+        // 加载状态结束
+        this.loading = false

+        // 数据全部加载完成
+        if (this.list.length >= 40) {
+          this.finished = true
+        }
+      }, 500)
+    }
  }
}
</script>

<style lang="less">
+ .home {
+   .van-tabs {
+     .van-tabs__content {
+       margin-bottom: 50px;
+     }
+   }
+ }
</style>
```



### 使用模拟数据

频道是遍历出来的，频道中的文章列表当然也是遍历出来的。所以要搞清楚的几点是：

- 每个频道都有一个文章列表组件（遍历出来的）
- 不同的频道应该拥有不同的数据状态
  - 频道的文章列表
  - 频道是否加载结束
  - 频道是否正在 loading 加载中

```
每个频道都有一份儿自己的文章列表数据：

[
  频道a: {
    id: xx,
    name: 'xxxx',
    文章列表: [],
    finished: false,
    loading: false
  },
  频道b: {
    id: xx,
    name: 'xxxx',
    文章列表: [],
    finished: false,
    loading: false
  }
]

查看频道a：
  - 请求获取频道a 的文章列表
  - 将数据添加到 频道a.文章列表中

查看频道b:
  - 请求获取频道b 的文章列表
  - 将数据添加到 频道a.文章列表中
```



所以我们首先要将数据进行改造，为每个频道添加自定义数据：文章列表、loading状态、finished 结束状态：

```js
loadAllChannels () {
  const { data } = await getAllChannels()

  // 定制频道的内容数据
  data.data.channels.forEach(channel => {
    channel.articles = [] // 频道的文章列表
    channel.loading = false // 频道的上拉加载更多的 loading 状态
    channel.finished = false // 频道的加载结束的状态
  })

  this.channels = data.data.channels
},
```

然后在让文章列表绑定每个频道对应的数据：

```html
<van-list
  v-model="channel.loading" // 该频道的 loading 加载状态
  :finished="channel.finished" // 该频道的 finished 结束状态
  finished-text="没有更多了"
  @load="onLoad"
>
  <!-- 具体的内容 -->
  <van-cell
    v-for="item in channel.articles" // 该频道的文章列表
    :key="item"
    :title="item"
  />
</van-list>
```

最后将 `onLoad` 函数修改为：

```js
computed: {
  currentChannel () {
    // active 是动态的，active 改变也就意味着 currentChannel 也改变了
    return this.channels[this.active]
  }
},

methods: {
  ...
  onLoad () {
    // 异步更新数据
    setTimeout(() => {
      // 1. 请求加载文章列表
      for (let i = 0; i < 10; i++) {
        // this.channels[this.active].articles
        const articles = this.currentChannel.articles
        // 2. 将得到的文章列表添加到当前频道.articles 中
        articles.push(articles.length + 1)
      }

      // 3. 本次 onLoad 数据加载完毕，将 loading 设置为 false
      this.currentChannel.loading = false

      // 4. 判断数据是否已全部加载完毕
      //   如果没有数据了，则将 **当前频道** 的 finished 设置为 true，列表组件就不再加载更多
      // 数据全部加载完成，将 finished 设置为 true，列表就不再去 onLoad 了
      if (this.currentChannel.articles.length >= 40) {
        this.currentChannel.finished = true
      }
    }, 2000)
  }
}
```



### 使用真实数据

1、封装接口：创建 `api/article.js` 模块并写入以下代码：

```js
/**
 * 获取文章列表
 */
export const getArticles = ({
  // JavaScript Standard Style 代码风格不允许有非驼峰名命名法的变量
  // 但是对象的属性名是不检查的
  channelId,
  timestamp,
  withTop
}) => {
  return request({
    method: 'GET',
    url: '/app/v1_1/articles',
    params: {
      channel_id: channelId, // 频道id
      timestamp, // 时间戳，请求新的推荐数据传当前的时间戳，请求历史推荐传指定的时间戳
      with_top: withTop // 是否包含置顶，进入页面第一次请求时要包含置顶文章，1-包含置顶，0-不包含
    }
  })
}

```



2、然后在首页中列表组件 `onload` 的时候请求加载文章列表：

```js
import { getAllChannels } from '@/api/channel'
+ import { getArticles } from '@/api/article'

export default {
  name: 'HomeIndex',
  data () {
    return {
      active: 0, // 控制当前激活的标签页
      channels: [] // 频道列表
    }
  },

  computed: {
    currentChannel () {
      // active 是动态的
      return this.channels[this.active]
    }
  },

  created () {
    this.loadAllChannels()
  },

  methods: {
    async loadAllChannels () {
      const { data } = await getAllChannels()

      // 为每一个频道初始化一个成员 articles 用来存储该频道的文章列表
      data.data.channels.forEach(channel => {
        channel.articles = [] // 频道的文章列表
        channel.loading = false // 频道的上拉加载更多的 loading 状态
        channel.finished = false // 频道的加载结束的状态
+        channel.timestamp = null // 用于获取下一页数据的时间戳（页码）
      })

      this.channels = data.data.channels
    },

    async onLoad () {
      // 1. 请求加载文章列表
      const currentChannel = this.currentChannel
      const { data } = await getArticles({
        channelId: currentChannel.id,
        // 第1页数据传递当前最新时间戳
        // 下一页数据传递上一页返回数据结果中的 pre_timestamp
        timestamp: currentChannel.timestamp || Date.now(),
        withTop: 1
      })

      // 2. 将得到的文章列表添加到当前频道.articles 中
      const { pre_timestamp: preTimestamp, results } = data.data
      // currentChannel.articles.concat(results) // 之前合并数组的方式
      currentChannel.articles.push(...results) // es6 也可以这么玩儿

      // 3. 本次 onLoad 数据加载完毕，将 loading 设置为 false
      currentChannel.loading = false

      // 4. 判断是否还有下一页数据
      if (!preTimestamp) {
        currentChannel.finished = true
      } else {
        // 还有数据，将本次得到的 preTimestamp 存储到当前频道，用于加载下一页数据
        currentChannel.timestamp = preTimestamp
      }
    }
  }
}
```

3、将数据绑定到模板中展示

```html
<van-list
  v-model="channel.loading"
  :finished="channel.finished"
  finished-text="没有更多了"
  @load="onLoad"
  >
  <!-- 具体的内容 -->
  <van-cell
    v-for="article in channel.articles"
    :key="article.art_id.toString()" // 注意：article.art_id 超出js安全整数范围被 json-bigint 转换成对象了，但是 key 只能绑定字符串或是数字，所以这里要把它转为字符串来绑定给这个 key
    :title="article.title"
  />
</van-list>
```

最终完成，测试结果。

### 下拉刷新

需求：用户在文章列表中下拉页面，刷新文章列表数据。

- 注册下拉刷新事件（组件）的处理函数
- 发送请求获取文章列表数据
- 把获取到的数据添加到当前频道的文章列表的顶部
- 提示用户刷新成功！

#### 组件

```html
<van-pull-refresh v-model="channel.pullDownLoading" @refresh="onRefresh">
  <van-list
            v-model="channel.loading"
            :finished="channel.finished"
            finished-text="没有更多了"
            @load="onLoad"
            >
    <!-- 具体的内容 -->
    <van-cell
              v-for="article in channel.articles"
              :key="article.art_id.toString()"
              :title="article.title"
              />
  </van-list>
</van-pull-refresh>
```

然后在将添加绑定数据：

```js
async loadAllChannels () {
  const { data } = await getAllChannels()

  // 为每一个频道初始化一个成员 articles 用来存储该频道的文章列表
  data.data.channels.forEach(channel => {
    channel.articles = [] // 频道的文章列表
    channel.loading = false // 频道的上拉加载更多的 loading 状态
    channel.finished = false // 频道的加载结束的状态
    channel.timestamp = null // 用于获取下一页数据的时间戳（页码）
+    channel.pullDownLoading = false // 频道的下拉刷新 loading 状态
  })

  this.channels = data.data.channels
},
  
/**
 * 下拉刷新处理函数
 */
onRefresh () {
	console.log('下拉刷新触发了。。。')
}
```

#### 功能实现

```js
/**
 * 下拉刷新处理函数
 */
async onRefresh () {
  // 1. 请求获取文章列表
  const currentChannel = this.currentChannel
  const { data } = await getArticles({
    channelId: currentChannel.id,
    timestamp: Date.now(),
    withTop: 1
  })

  // 2. 将数据添加到当前频道.articles数据中（顶部）
  currentChannel.articles.unshift(...data.data.results)

  // 3. 关闭当前频道下拉刷新的 loading 状态
  currentChannel.pullDownLoading = false

  // 4. 提示用户刷新成功
  this.$toast('刷新成功')
}
```



最后回到浏览器进行测试。

### 展示详细的列表项信息

在模板中新增：

```html
<van-cell
  v-for="article in channel.articles"
  :key="article.art_id"
  :title="article.title"
  >
+  <div slot="label">
+    <van-grid :border="false" :column-num="3">
+      <van-grid-item v-for="(img, index) in article.cover.images" :key="index">
+        <van-image height="80" :src="img" />
+      </van-grid-item>
+    </van-grid>
+    <div class="article-info">
+      <div class="meta">
+        <span>{{ article.aut_name }}</span>
+        <span>{{ article.comm_count }}评论</span>
+        <span>{{ article.pubdate }}</span>
+      </div>
+      <van-icon name="close" />
+    </div>
+  </div>
</van-cell>
```

然后在 style 中设置一下样式：

```css
...

.article-info {
  display: flex;
  align-items: center;
  justify-content: space-between;
  .meta span {
    margin-right: 10px;
  }
}
```

### 图片懒加载

Vant 提供了[Lazyload](https://youzan.github.io/vant/#/zh-CN/lazyload)组件，可以很方便的实现图片懒加载的功能。这里我们可以将文章列表中的文章图片配置为图片懒加载的方式，增强用户体验。

1、注册组件

```js
import Vue from 'vue';
import { Lazyload } from 'vant';

// options 为可选参数，无则不传
Vue.use(Lazyload, options);
```

2、使用

如果是普通的 img 标签，则将原来的 `src` 配置为 `v-lazy` 即可：

```html
<img v-for="img in imageList" v-lazy="img" >
export default {
  data() {
    return {
      imageList: [
        'https://img.yzcdn.cn/vant/apple-1.jpg',
        'https://img.yzcdn.cn/vant/apple-2.jpg'
      ]
    };
  }
}
```

如果使用的是 Vant 提供的图片组件，则直接配置 `lazy-load` 属性即可：

```html
<van-image
  width="100"
  height="100"
  lazy-load
  src="https://img.yzcdn.cn/vant/cat.jpeg"
/>
```



### 相对时间处理

[moment](https://github.com/moment/moment) 和 [dayjs](https://github.com/iamkun/dayjs) 都可以处理相对时间，这里我们以使用 dayjs 为例。

- moment 功能更强大一些
- dayjs 的设计是参照的 moment，所以无论你使用哪个一个，上手另一个都一样
- dayjs 好处是比 moment 小很多

1、安装依赖

```bash
npm i dayjs
```

2、创建 `utils/date.js` 并写入以下代码

```js
/**
 * 相对时间模块
 */
import dayjs from 'dayjs'
import 'dayjs/locale/zh-cn' // 按需加载
import rTime from 'dayjs/plugin/relativeTime' // 相对时间插件
dayjs.locale('zh-cn') // 全局使用中文语言
dayjs.extend(rTime) // 配置使用相对时间插件

// 处理相对时间的函数
export const relativeTime = time => {
  return dayjs().from(dayjs(time))
}

// 其它处理时间的函数
// export const relativeTime =
// export const relativeTime =
// export const relativeTime =

```

> 这样做的好处就是可以非常灵活：
>
> - 可以通过 JavaScript 方式来使用该函数
> - 也可以将其注册为过滤器在模板中使用



3、为了方便的在模板中能够使用到该函数，所以我们在 `main.js` 将封装好的 `relativeTime` 注册为过滤器

```js
...
import { relativeTime } from './utils/date'

Vue.filter('relativeTime', relativeTime)
```

4、然后在模板中使用

```html
...
<!--
	过滤器的本质作用：就是为模板提供一个函数进行调用
	过滤器是函数，使用过滤器就是在调用函数
	| 前面的会作为参数传递给过滤器函数
	过滤器函数的返回值将渲染到这里
-->
<span>{{ article.pubdate | relativeTime }}</span>
```

最后在浏览器中查看结果。



## 样式调整

频道列表固定定位：

1、让头部导航栏固定定位，只需要为导航栏组件添加一个 fixed 属性即可：

```html
<van-nav-bar title="首页" fixed />
```

2、让频道列表固定定位

```css
<style lang="less" scoped>
.home {
	.van-tabs /deep/ .van-tabs__wrap--scrollable {
    position: fixed;
    top: 46px;
    left: 0;
    right: 16px;
    z-index: 2;
  }

  .van-tabs /deep/ .van-tabs__content {
    margin-top: 90px;
  }
}
</style>
```

[关于作用域样式的说明](https://vue-loader.vuejs.org/zh/guide/scoped-css.html)：

- 当 `<style>` 标签有 `scoped` 属性时，它的 CSS 只作用于当前组件中的元素

- 使用 `scoped` 后，父组件的样式将不会渗透到子组件中。不过一个子组件的根节点会同时受其父组件的 scoped CSS 和子组件的 scoped CSS 的影响。这样设计是为了让父组件可以从布局的角度出发，调整其子组件根元素的样式。

- 如果你希望 `scoped` 样式中的一个选择器能够作用得“更深”，例如影响子组件，你可以使用 `>>>` 操作符：

  ```html
  <style scoped>
  .a >>> .b { /* ... */ }
  </style>
  ```

  上述代码将会编译成：

  ```css
  .a[data-v-f3f3eg9] .b { /* ... */ }
  ```

  有些像 Sass 之类的预处理器无法正确解析 `>>>`。这种情况下你可以使用 `/deep/` 或 `::v-deep` 操作符取而代之——两者都是 `>>>` 的别名，同样可以正常工作。



示例：

组件a：

```html
<template>
  <div id="app">
    <p>roo hello</p>
    <p>root world</p>
    <!-- <router-view /> -->
    <Foo />
    <div class="box">
      <p>root hello</p>
      <p>root world</p>
    </div>
  </div>
</template>

<script>
import Foo from '@/components/Foo'

export default {
  name: 'App',
  components: {
    Foo
  }
}
</script>

<!--
  关于组件样式的作用域问题
  在具有作用域样式的组件中，依然可以设置子组件根节点的样式
  如果需要在带有作用域样式的组件中想让样式作用的更深，则使用 /deep/ 这个语法。
  /deep/ 是在 .vue 文件中特定的语法，非 CSS 标准语法
  参考文档：
-->
<style scoped>
/* p {
  color: red;
} */

.box {
  color: red;
}

/* .p2 {
  color: blue;
} */

.box /deep/ .p2 {
  color: blue;
}
</style>

```

组件b：

```html
<template>
  <div class="box">
    <p>foo hello</p>
    <p class="p2">foo world</p>
  </div>

  <!-- <div>
    <div class="box">
      <p>foo hello</p>
      <p>foo world</p>
    </div>
  </div> -->
</template>

<script>
export default {

}
</script>

<style>

</style>

```

