# 十、编辑个人信息

<img src="./assets/1566431661910.png" width="300">





## 准备

1、创建 `user/index.vue` 并写入以下内容：

```html
<template>
  <div>个人信息</div>
</template>

<script>
export default {

}
</script>

<style>

</style>

```

2、配置路由

```js
...
import User from '@/views/user'

const router = new VueRouter({
  // 配置路由表
  routes: [
    ...
    {
      name: 'user',
      path: '/user',
      component: User
    }
  ]
})

export default router

```

3、在我的页面中点击用户信息跳转到个人信息页面

```html
<!-- 用户信息 -->
<van-cell-group class="user-info" v-else>
  <van-cell
    class="base-info"
    is-link :border="false"
+    @click="$router.push('/user')"
  >
    ...
  </van-cell>
  ...
</van-cell-group>
<!-- /用户信息 -->
```

最后点击跳转测试。

## 布局

```html
<template>
  <div>
    <van-nav-bar
      title="个人信息"
      left-arrow
      right-text="保存"
    />
    <van-cell-group>
      <van-cell title="头像" is-link>
        <van-image
          round
          width="30"
          height="30"
          src="http://toutiao.meiduo.site/FgSTA3msGyxp5-Oufnm5c0kjVgW7"
        />
      </van-cell>
      <van-cell title="昵称" value="abc" is-link />
      <van-cell title="性别" value="男" is-link />
      <van-cell title="生日" value="2019-9-27" is-link />
    </van-cell-group>
  </div>
</template>

<script>
export default {
  name: 'UserIndex'
}
</script>

```



## 展示个人信息

步骤：

- 封装接口
- 请求获取数据
- 处理模板



下面是具体的实现方式：

1、封装接口

```js
/**
 * 获取用户个人资料
 */
export const getProfile = () => {
  return request({
    method: 'GET',
    url: '/app/v1_0/user/profile'
  })
}

```



2、请求获取数据

```js
+ import { getProfile } from '@/api/user'

export default {
  name: 'UserIndex',
  data () {
    return {
+      user: {} // 用户个人资料
    }
  },

  created () {
+    this.loadUserProfile()
  },

  methods: {
+    async loadUserProfile () {
      const { data } = await getProfile()
      this.user = data.data
    }
  }
}
```



3、模板绑定

```html
<!-- 用户信息 -->
<van-cell-group>
  <van-cell title="头像" is-link>
    <van-image
      round
      width="30"
      height="30"
+      :src="user.photo"
    />
  </van-cell>
+  <van-cell title="昵称" :value="user.name" is-link />
+  <van-cell title="性别" :value="user.gender === 1 ? '女' : '男'" is-link />
+  <van-cell title="生日" :value="user.birthday" is-link />
</van-cell-group>
<!-- /用户信息 -->
```

最后测试。

## 头像上传

需求：

- 头像上传预览
- 保存头像上传

### 图片上传预览

方式一：结合服务器的图片上传预览

![1567067894388](./assets/1567067894388.png)

方式二：纯客户端实现上传图片预览

```js
// 获取文文件对象
const file = this.files[0]

// 设置图片的 src
图片.src = window.URL.createObjectURL(file)
```

客户端上传预览示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <input type="file">
  <img src="" alt="">
  <script>
    const file = document.querySelector('input')
    const img = document.querySelector('img')

    file.onchange = function () {
      // 获取文件对象
      const data = window.URL.createObjectURL(file.files[0])
      img.src = data
    }
  </script>
</body>
</html>

```

接下来就是在项目中使用纯客户端的方式处理用户头像上传预览。

```html
<template>
  <div>
    <!-- 导航栏 -->
    <van-nav-bar
      title="个人信息"
      left-arrow
      right-text="保存"
    />
    <!-- /导航栏 -->

    <!-- 用户信息 -->
    <van-cell-group>
      <!-- 《3》注册头像的点击事件，绑定处理函数 -->
      <van-cell title="头像" is-link @click="onShowFile">
        <van-image
          round
          width="30"
          height="30"
          :src="user.photo"
        />
      </van-cell>
      <van-cell title="昵称" :value="user.name" is-link />
      <van-cell title="性别" :value="user.gender === 1 ? '女' : '男'" is-link />
      <van-cell title="生日" :value="user.birthday" is-link />
    </van-cell-group>
    <!-- /用户信息 -->

    <!-- 《1》添加一个 file 类型的 input，设置 hidden 隐藏 -->
    <!-- 头像上传触发的 file 类型的 input -->
    <input hidden type="file" ref="file">
    <!-- /头像上传触发的 file 类型的 input -->
  </div>
</template>

<script>
import { getProfile } from '@/api/user'

export default {
  name: 'UserIndex',
  data () {
    return {
      user: {} // 用户个人资料
    }
  },

  // 《2》为了方便，封装一个计算属性获取 ref 引用的 file
  computed: {
    file () {
      return this.$refs.file
    }
  },

  created () {
    this.loadUserProfile()
  },

  // 《5》在 mounted 中给 file 注册 change 事件，处理预览图片
  // 初始化的时候操作 DOM
  mounted () {
    // 注册 file 的 change 事件，预览图片
    this.file.onchange = () => {
      // 设置给 img 的 src
      this.user.photo = window.URL.createObjectURL(this.file.files[0])
    }
  },

  methods: {
    async loadUserProfile () {
      const { data } = await getProfile()
      this.user = data.data
    },

    // 《4》在处理函数中，手动触发 file 的点击事件
    onShowFile () {
      // 手动触发 input 的点击事件
      this.file.click()
    }
  }
}
</script>

```



### 保存提交

步骤：

- 封装接口
- 请求提交
- 更新视图

1、封装接口

```js
/**
 * 更新用户头像
 */
export const updateUserPhoto = file => {
  const formData = new FormData()
  formData.append('photo', file)
  // 请求头中的 Content-Type
  //    application/json  axios会自动设置，传递普通 JavaScript 对象即可
  //    multipart/form-data 传递 FormData，axios 也会自动设置
  return request({
    method: 'PATCH',
    url: '/app/v1_0/user/photo',
    // application/json
    // data: {}

    // multipart/form-data
    // 注意：一般上传文件的接口都会要求请求头的 Content-Type 是 multipart/form-data
    data: formData
  })
}
```

2、请求提交

```html
<!-- 导航栏 -->
<van-nav-bar
  title="个人信息"
  left-arrow
  right-text="保存"
+  @click-right="onSaveProfile"
/>
<!-- /导航栏 -->
```

```js
import { getProfile, updateUserPhoto } from '@/api/user'

export default {
  ...
  methods: {
    ...
    async onSaveProfile () {
      // loading
      const toast = this.$toast.loading({
        duration: 0,       // 持续展示 toast
        forbidClick: true, // 禁用背景点击
        loadingType: 'spinner',
        message: '更新中'
      })

      try {
        // 请求提交
        await updateUserPhoto(this.file.files[0])

        // 关闭弹窗 loading
        toast.clear()

        // 提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        console.log(err)
        // 关闭弹窗 loading
        toast.clear()
        // 提示失败
        this.$toast.fail('更新失败')
      }
    }
  }
}
```



## 其它字段修改

### 编辑昵称

```html
<template>
  <div>
    <!-- 导航栏 -->
    <van-nav-bar
      title="个人信息"
      left-arrow
      right-text="保存"
      @click-right="onSaveProfile"
    />
    <!-- /导航栏 -->

    <!-- 用户信息 -->
    <van-cell-group>
      <van-cell title="头像" is-link @click="onShowFile">
        <van-image
          round
          width="30"
          height="30"
          :src="user.photo"
        />
      </van-cell>
+      <van-cell title="昵称" :value="user.name" is-link @click="isEditNameShow = true" />
      <van-cell title="性别" :value="user.gender === 1 ? '女' : '男'" is-link />
      <van-cell title="生日" :value="user.birthday" is-link />
    </van-cell-group>
    <!-- /用户信息 -->

    <!-- 头像上传触发的 file 类型的 input -->
    <input hidden type="file" ref="file">
    <!-- /头像上传触发的 file 类型的 input -->

    <!-- 编辑用户昵称 -->
+    <van-dialog
+      v-model="isEditNameShow"
+      title="用户昵称"
+      show-cancel-button
+      @confirm="user.name = inputUserName"
+    >
+      <van-field
+        :value="user.name"
+        placeholder="请输入用户名"
+        @input="onUserNameInput"
+      />
+    </van-dialog>
    <!-- 编辑用户昵称 -->

    <!-- 编辑用户性别 -->
    <!-- /编辑用户性别 -->

    <!-- 编辑用户生日 -->
    <!-- /编辑用户生日 -->
  </div>
</template>

<script>
import { getProfile, updateUserPhoto } from '@/api/user'

export default {
  name: 'UserIndex',
  data () {
    return {
      user: {}, // 用户个人资料
+      isEditNameShow: false, // 是否显示编辑昵称
+      inputUserName: '' // 存储编辑用户输入的内容
    }
  },

  computed: {
    file () {
      return this.$refs.file
    }
  },

  created () {
    this.loadUserProfile()
  },

  // 初始化的时候操作 DOM
  mounted () {
    // 注册 file 的 change 事件，预览图片
    this.file.onchange = () => {
      // 设置给 img 的 src
      this.user.photo = window.URL.createObjectURL(this.file.files[0])
    }
  },

  methods: {
    async loadUserProfile () {
      const { data } = await getProfile()
      this.user = data.data
    },

    onShowFile () {
      // 手动触发 input 的点击事件
      this.file.click()
    },

    async onSaveProfile () {
      // loading
      const toast = this.$toast.loading({
        duration: 0, // 持续展示 toast
        forbidClick: true, // 禁用背景点击
        loadingType: 'spinner',
        message: '更新中'
      })

      try {
        // 请求提交
        await updateUserPhoto(this.file.files[0])

        // 关闭弹窗 loading
        toast.clear()

        // 提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        console.log(err)
        // 关闭弹窗 loading
        toast.clear()
        // 提示失败
        this.$toast.fail('更新失败')
      }
    },

+    onUserNameInput (value) {
+      this.inputUserName = value
+    }
  }
}
</script>

```



### 编辑性别

```html
<template>
  <div>
    <!-- 导航栏 -->
    <van-nav-bar
      title="个人信息"
      left-arrow
      right-text="保存"
      @click-right="onSaveProfile"
    />
    <!-- /导航栏 -->

    <!-- 用户信息 -->
    <van-cell-group>
      <van-cell title="头像" is-link @click="onShowFile">
        <van-image
          round
          width="30"
          height="30"
          :src="user.photo"
        />
      </van-cell>
      <van-cell
        title="昵称"
        :value="user.name"
        is-link
        @click="isEditNameShow = true"
      />
      <van-cell
        title="性别"
        :value="user.gender === 1 ? '女' : '男'"
        is-link
        @click="isEditGenderShow = true"
      />
      <van-cell title="生日" :value="user.birthday" is-link />
    </van-cell-group>
    <!-- /用户信息 -->

    <!-- 头像上传触发的 file 类型的 input -->
    <input hidden type="file" ref="file">
    <!-- /头像上传触发的 file 类型的 input -->

    <!-- 编辑用户昵称 -->
    <van-dialog
      v-model="isEditNameShow"
      title="用户昵称"
      show-cancel-button
      @confirm="user.name = inputUserName"
    >
      <van-field
        :value="user.name"
        placeholder="请输入用户名"
        @input="onUserNameInput"
      />
    </van-dialog>
    <!-- 编辑用户昵称 -->

    <!-- 编辑用户性别 -->
+    <van-action-sheet
+      v-model="isEditGenderShow"
+      :actions="[
+        { name: '男', value: 0 },
+        { name: '女', value: 1 }
+      ]"
+      cancel-text="取消"
+      @select="onGenderSelect"
+    />
    <!-- /编辑用户性别 -->

    <!-- 编辑用户生日 -->
    <!-- /编辑用户生日 -->
  </div>
</template>

<script>
import { getProfile, updateUserPhoto } from '@/api/user'

export default {
  name: 'UserIndex',
  data () {
    return {
      user: {}, // 用户个人资料
      isEditNameShow: false, // 是否显示编辑昵称
      inputUserName: '', // 存储编辑用户输入的内容
+      isEditGenderShow: false // 是否显示编辑性别
    }
  },

  computed: {
    file () {
      return this.$refs.file
    }
  },

  created () {
    this.loadUserProfile()
  },

  // 初始化的时候操作 DOM
  mounted () {
    // 注册 file 的 change 事件，预览图片
    this.file.onchange = () => {
      // 设置给 img 的 src
      this.user.photo = window.URL.createObjectURL(this.file.files[0])
    }
  },

  methods: {
    async loadUserProfile () {
      const { data } = await getProfile()
      this.user = data.data
    },

    onShowFile () {
      // 手动触发 input 的点击事件
      this.file.click()
    },

    async onSaveProfile () {
      // loading
      const toast = this.$toast.loading({
        duration: 0, // 持续展示 toast
        forbidClick: true, // 禁用背景点击
        loadingType: 'spinner',
        message: '更新中'
      })

      try {
        // 请求提交
        await updateUserPhoto(this.file.files[0])

        // 关闭弹窗 loading
        toast.clear()

        // 提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        console.log(err)
        // 关闭弹窗 loading
        toast.clear()
        // 提示失败
        this.$toast.fail('更新失败')
      }
    },

    onUserNameInput (value) {
      this.inputUserName = value
    },

+    onGenderSelect (item) {
+      // 修改数据
+      this.user.gender = item.value
+
+      // 关闭弹窗
+      this.isEditGenderShow = false
+    }
  }
}
</script>

```



### 编辑生日

```html
<template>
  <div>
    <!-- 导航栏 -->
    <van-nav-bar
      title="个人信息"
      left-arrow
      right-text="保存"
      @click-right="onSaveProfile"
    />
    <!-- /导航栏 -->

    <!-- 用户信息 -->
    <van-cell-group>
      <van-cell title="头像" is-link @click="onShowFile">
        <van-image
          round
          width="30"
          height="30"
          :src="user.photo"
        />
      </van-cell>
      <van-cell
        title="昵称"
        :value="user.name"
        is-link
        @click="isEditNameShow = true"
      />
      <van-cell
        title="性别"
        :value="user.gender === 1 ? '女' : '男'"
        is-link
        @click="isEditGenderShow = true"
      />
      <van-cell
        title="生日"
        :value="user.birthday"
        is-link
+        @click="isEditBirthdayShow = true"
      />
    </van-cell-group>
    <!-- /用户信息 -->

    <!-- 头像上传触发的 file 类型的 input -->
    <input hidden type="file" ref="file">
    <!-- /头像上传触发的 file 类型的 input -->

    <!-- 编辑用户昵称 -->
    <van-dialog
      v-model="isEditNameShow"
      title="用户昵称"
      show-cancel-button
      @confirm="user.name = inputUserName"
    >
      <van-field
        :value="user.name"
        placeholder="请输入用户名"
        @input="onUserNameInput"
      />
    </van-dialog>
    <!-- 编辑用户昵称 -->

    <!-- 编辑用户性别 -->
    <van-action-sheet
      v-model="isEditGenderShow"
      :actions="[
        { name: '男', value: 0 },
        { name: '女', value: 1 }
      ]"
      cancel-text="取消"
      @select="onGenderSelect"
    />
    <!-- /编辑用户性别 -->

    <!-- 编辑用户生日 -->
+    <van-popup
+      v-model="isEditBirthdayShow"
+      position="bottom"
+      :style="{ height: '30%' }"
+    >
+      <van-datetime-picker
+        v-model="currentDate"
+        type="date"
+        :min-date="minDate"
+        @confirm="onDatetimeConfirm"
+        @cancel="isEditBirthdayShow = false"
+      />
    </van-popup>
    <!-- /编辑用户生日 -->
  </div>
</template>

<script>
import { getProfile, updateUserPhoto } from '@/api/user'
import { formatDate } from '@/utils/date'

export default {
  name: 'UserIndex',
  data () {
    return {
      user: {}, // 用户个人资料
      isEditNameShow: false, // 是否显示编辑昵称
      inputUserName: '', // 存储编辑用户输入的内容
      isEditGenderShow: false, // 是否显示编辑性别
+      isEditBirthdayShow: false, // 是否显示编辑生日
+      minDate: new Date(1870, 1, 1), // 可选的最小时间(一个小技巧，生成指定日期的日期对象)
+      currentDate: new Date() // 默认被选中的时间
    }
  },

  computed: {
    file () {
      return this.$refs.file
    }
  },

  created () {
    this.loadUserProfile()
  },

  // 初始化的时候操作 DOM
  mounted () {
    // 注册 file 的 change 事件，预览图片
    this.file.onchange = () => {
      // 设置给 img 的 src
      this.user.photo = window.URL.createObjectURL(this.file.files[0])
    }
  },

  methods: {
    async loadUserProfile () {
      const { data } = await getProfile()
      this.user = data.data
    },

    onShowFile () {
      // 手动触发 input 的点击事件
      this.file.click()
    },

    async onSaveProfile () {
      // loading
      const toast = this.$toast.loading({
        duration: 0, // 持续展示 toast
        forbidClick: true, // 禁用背景点击
        loadingType: 'spinner',
        message: '更新中'
      })

      try {
        // 请求提交
        await updateUserPhoto(this.file.files[0])

        // 关闭弹窗 loading
        toast.clear()

        // 提示成功
        this.$toast.success('更新成功')
      } catch (err) {
        console.log(err)
        // 关闭弹窗 loading
        toast.clear()
        // 提示失败
        this.$toast.fail('更新失败')
      }
    },

    onUserNameInput (value) {
      this.inputUserName = value
    },

    onGenderSelect (item) {
      // 修改数据
      this.user.gender = item.value

      // 关闭弹窗
      this.isEditGenderShow = false
    },

+    onDatetimeConfirm (value) {
+      // 修改数据
+      this.user.birthday = formatDate(value)

+      // 关闭弹层
+      this.isEditBirthdayShow = false
+    }
  }
}
</script>

```

别忘了在 `utils/date.js` 添加一个专门处理格式化日期的函数：

```js
export const formatDate = date => {
  return dayjs(date).format('YYYY-MM-DD')
}
```



## 保存修改

步骤：

- 封装接口
- 请求提交
- 后续处理



下面是具体的实现流程：

1、封装接口

```js
/**
 * 更新用户基本信息
 */
export const updateUserProfile = ({
  name,
  gender,
  birthday
}) => {
  return request({
    method: 'PATCH',
    url: '/app/v1_0/user/profile',
    data: {
      name, // 昵称
      gender, // 性别
      birthday // 生日
    }
  })
}
```

2、请求调用

```js
async onSaveProfile () {
  // loading
  const toast = this.$toast.loading({
    duration: 0, // 持续展示 toast
    forbidClick: true, // 禁用背景点击
    loadingType: 'spinner',
    message: '更新中'
  })

  try {
+    // 如果用户上传了头像才请求提交更新用户头像
+    const photo = this.file.files[0]
+    if (photo) {
+      await updateUserPhoto(this.file.files[0])
+    }

+    // 更新普通数据信息（昵称、性别、生日）
+    await updateUserProfile(this.user)

    // 关闭弹窗 loading
    toast.clear()

    // 提示成功
    this.$toast.success('更新成功')
  } catch (err) {
    // if (err.response && err.response.status)
    // 关闭弹窗 loading
    toast.clear()

    if (err.response && err.response.status === 409) {
      this.$toast.fail('昵称已存在')
    } else {
      // 提示失败
      this.$toast.fail('更新失败')
    }
  }
},
```

> 提示：性别无法修改，因为接口有问题，实际工作需要好好的和后台小哥哥商量。





