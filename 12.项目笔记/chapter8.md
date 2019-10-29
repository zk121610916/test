# 八、文章详情（下）

<img src="./assets/1566972493322.png" width="300">



## 准备文章评论组件

<img src="assets/1569481517218.png" width="300">

1、创建 `views/article/components/comment.vue` 并写入以下内容：

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="item in list"
        :key="item"
        :title="item"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          src="https://img.yzcdn.cn/vant/cat.jpeg"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">我出去跟别人说我的是。。。</p>
          <p>
            <span style="margin-right: 10px;">3天前</span>
            <van-button size="mini" type="default">回复</van-button>
          </p>
        </div>
        <van-icon slot="right-icon" name="like-o" />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        clearable
        placeholder="请输入评论内容"
      >
        <van-button slot="button" size="mini" type="info">发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->
  </div>
</template>

<script>
export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false // 是否加载结束
    }
  },

  methods: {
    onLoad () {
      // 异步更新数据
      setTimeout(() => {
        for (let i = 0; i < 10; i++) {
          this.list.push(this.list.length + 1)
        }
        // 加载状态结束
        this.loading = false

        // 数据全部加载完成
        if (this.list.length >= 40) {
          this.finished = true
        }
      }, 500)
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

2、在文章详情页面中加载注册文章评论子组件：

```js
import ArticleComment from './components/comment'

export default {
  ...
  components: {
    ArticleComment
  }
}
```

3、在文章详情页面中使用文章评论子组件

```html
<div class="article-container">
	...  	
  <!-- 文章评论 -->
  <article-comment />
  <!-- /文章评论 -->
</div>
```

## 展示文章评论列表

> 提示：有评论数据的文章id：138765。

步骤：

- 封装接口
- 请求获取数据
- 处理模板

实现：

1、创建 `api/comment.js` 中添加封装获取评论列表的请求方法

```js
/**
 * 获取文章评论/评论回复
 */
export const getComments = ({
  type,
  source,
  offset,
  limit
}) => {
  return request({
    method: 'GET',
    url: '/app/v1_0/comments',
    params: {
      type, // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
      source, // 源id，文章id或评论id
      offset, // 页码（获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据）
      limit // 每页大小（获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量）
    }
  })
}
```

2、将文章详情组件中的文章id通过 Props 传递给文章评论子组件

在文章详情组件中传递：

```html
<!-- 文章评论 -->
<article-comment :article-id="$route.params.articleId" />
<!-- /文章评论 -->
```



在文章评论组件中声明接收：

```js
export default {
  ...
  props: ['articleId']
}
```



3、然后在评论列表中请求获取文章评论列表

```js
import { getComments } from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: ['articleId'],
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
+      offset: null, // 获取下一页数据的页码，第1页就是 null
+      limit: 10 // 每页大小
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取评论数据
      const { data } = await getComments({
        type: 'a',
        source: this.articleId,
        offset: this.offset,
        limit: this.limit
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 将 loading 设置为 false
      this.loading = false

      // 4. 判断数据是否已加载结束
      if (data.data.last_id) {
        // 4.1 如果后面还有数据，则更新获取下一页数据的页码
        this.offset = data.data.last_id
      } else {
        // 4.2 如果后面没数据了，则将 finished 设置为 true
        this.finished = true
      }
    }
  }
}
```

4、最后，模板绑定

```html
  <!-- 评论列表 -->
  <van-list
    v-model="loading"
    :finished="finished"
    finished-text="没有更多了"
    @load="onLoad"
  >
    <van-cell
+      v-for="comment in list"
+      :key="comment.com_id.toString()"
    >
      <van-image
        slot="icon"
        round
        width="30"
        height="30"
        style="margin-right: 10px;"
+        :src="comment.aut_photo"
      />
+      <span style="color: #466b9d;" slot="title">{{ comment.aut_name }}</span>
      <div slot="label">
+        <p style="color: #363636;">{{ comment.content }}</p>
        <p>
+          <span style="margin-right: 10px;">{{ comment.pubdate | relativeTime }}</span>
          <van-button size="mini" type="default">回复</van-button>
        </p>
      </div>
      <van-icon slot="right-icon" name="like-o" />
    </van-cell>
  </van-list>
  <!-- 评论列表 -->
```





## 发布文章评论

步骤：

- 注册发布处理函数
- 请求提交表单
- 根据响应结果进行后续处理

实现：

1、封装接口

```js
/**
 * 添加文章评论/评论回复
 */
export const addComment = ({
  target,
  content,
  artId
}) => {
  return request({
    method: 'POST',
    url: '/app/v1_0/comments',
    data: {
      target, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
      content, // 评论内容
      art_id: artId // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
    }
  })
}
```

2、绑定获取用户输入的评论内容

```html
<!-- 发布评论 -->
<van-cell-group class="publish-wrap">
  <van-field
    clearable
    placeholder="请输入评论内容"
+    v-model="commentText"
  >
    <van-button
      slot="button"
      size="mini"
      type="info"
    >发布</van-button>
  </van-field>
</van-cell-group>
<!-- /发布评论 -->
```

然后在 data 中添加：

```js
data () {
  return {
    ...
    commentText: ''
  }
}
```

3、注册发布评论按钮的点击事件处理函数

```html
<!-- 发布评论 -->
<van-cell-group class="publish-wrap">
  <van-field
    clearable
    placeholder="请输入评论内容"
+    v-model="commentText"
  >
    <van-button
      slot="button"
      size="mini"
      type="info"
+      @click="onPublishComment"
    >发布</van-button>
  </van-field>
</van-cell-group>
<!-- /发布评论 -->
```

4、发布评论事件处理函数如下：

```js
...
import { getComments, addComment } from '@/api/comment'

async onPublishComment () {
  // 非空校验
  const commentText = this.commentText.trim()
  if (!commentText.length) {
    return
  }

  // 请求添加评论
  const { data } = await addComment({
    target: this.articleId, // 文章id
    content: this.commentText // 评论内容
  })

  // 将最新的评论数据添加到列表顶部
  this.list.unshift(data.data.new_obj)

  // 清空用户输入的文本框
  this.commentText = ''
}
```

## 展示评论回复列表

### 弹层

1、首先在 data 中初始化一个数据成员用来控制弹层的显示和隐藏：

```js
data () {
  return {
    ...
    isReplyShow: false
  }
}
```

2、在文章评论组件中添加弹层组件用来展示评论回复列表：

```html
<!-- 回复列表 -->
<van-popup
  v-model="isReplyShow"
  position="bottom"
  :style="{ height: '95%' }"
  round
>
</van-popup>
<!-- 回复列表 -->
```

3、当点击文章评论组件中的回复按钮的时候，展示弹层

```html
<van-button
  size="mini"
  type="default"
  @click="isReplyShow = true"
>回复</van-button>
```

### 回复列表组件

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="comment in list"
        :key="comment.com_id.toString()"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="comment.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">{{ comment.aut_name }}</span>
        <div slot="label">
          <p style="color: #363636;">{{ comment.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ comment.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
              @click="onReplyShow(comment)"
            >回复</van-button>
          </p>
        </div>
        <van-icon slot="right-icon" name="like-o" />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        clearable
        placeholder="请输入评论内容"
        v-model="commentText"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onPublishComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 回复列表 -->
    <van-popup
      v-model="isReplyShow"
      position="bottom"
      :style="{ height: '95%' }"
      round
    >
      <reply-list :comment="currentComment" />
    </van-popup>
    <!-- 回复列表 -->
  </div>
</template>

<script>
import { getComments, addComment } from '@/api/comment'
import ReplyList from './reply-list'

export default {
  name: 'ArticleComment',
  props: ['articleId'],
  components: {
    ReplyList
  },
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      limit: 10,
      commentText: '', // 用户输入的评论内容
      isReplyShow: false,
      totalReplyCount: 0,
      currentComment: {} // 当前点击回复的评论
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取评论数据
      const { data } = await getComments({
        type: 'a',
        source: this.articleId,
        offset: this.offset,
        limit: this.limit
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 将 loading 设置为 false
      this.loading = false

      // 4. 判断数据是否已加载结束
      if (data.data.last_id) {
        // 4.1 如果后面还有数据，则更新获取下一页数据的页码
        this.offset = data.data.last_id
      } else {
        // 4.2 如果后面没数据了，则将 finished 设置为 true
        this.finished = true
      }
    },

    async onPublishComment () {
      const commentText = this.commentText.trim()
      if (!commentText.length) {
        return
      }

      // 请求添加评论
      const { data } = await addComment({
        target: this.articleId,
        content: this.commentText
      })

      // 将最新的评论数据添加到列表顶部
      this.list.unshift(data.data.new_obj)

      // 清空用户输入的文本框
      this.commentText = ''
    },

    onReplyShow (comment) {
      // 将点击回复所在的评论存储起来，通过 Props 传递给子组件
      this.currentComment = comment

      // 显示弹层
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

2、在文章评论列表组件中注册使用这个组件

```html
<template>
  <div class="article-comments">
    ...

    <!-- 回复列表 -->
    <van-popup
      v-model="isReplyShow"
      position="bottom"
      :style="{ height: '95%' }"
      round
    >
+      <reply-list :comment="currentComment" />
    </van-popup>
    <!-- 回复列表 -->
  </div>
</template>

<script>
...
+ import ReplyList from './reply-list'

export default {
  ...
+  components: {
+    ReplyList
+  },
  ...
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

3、点击回复按钮展示弹层组件

### 展示评论回复

一、将查看回复列表的评论对象通过 Props 传递给回复列表组件

1、为文章评论列表组件中的回复按钮注册点击事件处理函数

```html
<van-button
  size="mini"
  type="default"
+  @click="onReplyShow(comment)"
>回复</van-button>
```

2、在事件处理函数中：

- 记录点击回复的评论
- 显示弹层

```js
data () {
  return {
    ...
    currentComment: {} // 存储当前点击回复的评论对象
  }
}

onReplyShow (comment) {
  // 将点击回复所在的评论存储起来，通过 Props 传递给子组件
  this.currentComment = comment

  // 显示弹层
  this.isReplyShow = true
}
```

3、使用 Props 将当前点击回复的评论对象传递给评论回复列表组件：

传递：

```html
<!-- 回复列表 -->
<van-popup
  v-model="isReplyShow"
  position="bottom"
  :style="{ height: '95%' }"
  round
>
+  <reply-list :comment="currentComment" />
</van-popup>
<!-- 回复列表 -->
```



接收：

```js
props: ['comment']
```

完了，去调试工具中，点击回复，查看回复列表组件中是否可以正常接收到 Props 数据。



二、请求获取评论回复列表

只需要将组件中的 `onLoad` 方法稍加修改：

```js
async onLoad () {
  // 1. 请求获取评论数据
  const { data } = await getComments({
+    type: 'c',
+    source: this.comment.com_id.toString(),
    offset: this.offset,
    limit: this.limit
  })

  // 2. 将数据添加到数组中
  this.list.push(...data.data.results)

  // 3. 将 loading 设置为 false
  this.loading = false

  // 4. 判断数据是否已加载结束
  if (data.data.last_id) {
    // 4.1 如果后面还有数据，则更新获取下一页数据的页码
    this.offset = data.data.last_id
  } else {
    // 4.2 如果后面没数据了，则将 finished 设置为 true
    this.finished = true
  }
},
```

改完之后测试结果。



四、模板绑定展示

还是原来的列表内容，不需要做任何修改。

> 重点：
>
> - 组件传值在这里的应用

## 发布评论回复

1、将文章评论列表组件中的文章id通过 Props 传递给评论回复列表组件

```html
<!-- 回复列表 -->
<van-popup
  v-model="isReplyShow"
  position="bottom"
  :style="{ height: '95%' }"
  round
>
  <reply-list
    :comment="currentComment"
+    :article-id="articleId"
  />
</van-popup>
<!-- 回复列表 -->
```

然后在子组件中声明接收：

```js
export default {
+	props: ['articleId', 'comment']  
}
```

2、将发布评论的 `onPublishComment` 函数稍加修改：

```js
async onPublishComment () {
  const commentText = this.commentText.trim()
  if (!commentText.length) {
    return
  }

  // 请求添加评论
  const { data } = await addComment({
+    target: this.comment.com_id.toString(), // 评论id
    content: this.commentText, // 评论内容
+    artId: this.articleId // 文章id
  })

  // 将最新的评论数据添加到列表顶部
  this.list.unshift(data.data.new_obj)

  // 清空用户输入的文本框
  this.commentText = ''
}
```

3、最后测试。

> 重点：
>
> - 组件通信传值

## 处理回复列表的头部

这里主要涉及到两个功能点：

- 展示总回复数量
- 关闭弹窗

一、展示总回复数量

```html
<template>
  <div class="article-comments">
    <!-- 关闭按钮 -->
    <van-nav-bar
+      :title="totalReplyCount + '条回复'"
    >
      <van-icon slot="left" name="cross" />
    </van-nav-bar>
    <!-- /关闭按钮 -->
    ...
</template>

<script>

export default {
  ...
  data () {
    return {
      ...
+      totalReplyCount: 0 // 总回复数量
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取评论数据
      const { data } = await getComments({
        type: 'c', // 获取评论回复传 c
        source: this.comment.com_id.toString(), // 评论id
        offset: this.offset,
        limit: this.limit
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 更新总回复数量
+      this.totalReplyCount = data.data.total_count

      // 3. 将 loading 设置为 false
      this.loading = false

      // 4. 判断数据是否已加载结束
      if (data.data.last_id) {
        // 4.1 如果后面还有数据，则更新获取下一页数据的页码
        this.offset = data.data.last_id
      } else {
        // 4.2 如果后面没数据了，则将 finished 设置为 true
        this.finished = true
      }
    },

    async onPublishComment () {
      const commentText = this.commentText.trim()
      if (!commentText.length) {
        return
      }

      // 请求添加评论
      const { data } = await addComment({
        target: this.comment.com_id.toString(), // 评论id
        content: this.commentText, // 评论内容
        artId: this.articleId // 文章id
      })

      // 将最新的评论数据添加到列表顶部
      this.list.unshift(data.data.new_obj)

      // 清空用户输入的文本框
      this.commentText = ''

      // 更新总回复数量
+      this.totalReplyCount++
    }
  }
}
</script>

```

二、点击关闭按钮关闭弹层

1、在子组件（回复列表组件）发布自定义事件

```html
<template>
  <div class="article-comments">
    <!-- 关闭按钮 -->
    <van-nav-bar
      :title="totalReplyCount + '条回复'"
    >
+      <van-icon slot="left" name="cross" @click="$emit('close')" />
    </van-nav-bar>
    <!-- /关闭按钮 -->
    ...
</template>
```



2、在父组件（评论列表组件）注册绑定处理

```html
<!-- 回复列表 -->
<van-popup
  v-model="isReplyShow"
  position="bottom"
  :style="{ height: '95%' }"
  round
>
  <reply-list
    :comment="currentComment"
    :article-id="articleId"
+    @close="isReplyShow = false"
  />
</van-popup>
<!-- 回复列表 -->
```

