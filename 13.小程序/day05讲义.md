#### 小程序day-05

#### 课程目标

+ 能够知道如何实现分类页
+ 能够知道如何使用vant
+ 能够知道如何实现loading效果
+ 能够知道实现搜索页面的功能和逻辑

#### 01- 点击楼层图片跳转到商品列表页面

- 给图片注册点击事件

  ```
  <!-- 内容图片 -->
        <view class="content_img">
          <image @tap="goGoodsDetail({{item.navigator_url}})" style="width:{{item.image_width}}rpx" wx:for="{{item.product_list}}" src="{{item.image_src}}"></image>
        </view>
  ```

- 定义事件函数

  ```
  goGoodsDetail (url) {
    wepy.navigateTo({
      url
    })
  }
  ```

- 创建商品列表页

- 将商品列表路劲存到app.wpy里面的pages里

- 在商品列表里通过onLoad的形参获取传递过来的参数

#### 02 - 分类页的实现（上）

##### 获取分类数据列表并使用vant组件实现基本结构

+ 实现自定义编辑模式

  + 点击工具栏中，编译模式的下拉菜单，选择新建编译模式
  + 填写编译模式的名称
  + 选择启动页面的路径
  + 确认添加

+ 抽离cates.js文件

  + 在mixins》tabs里面新建cate.js

  + 创建cate.js基本结构

    ```
    import wepy from 'wepy'

    export default class extends wepy.mixin {
      data = {
        
      }

      config = {
      }

      methods = {

      }
    }
    ```

  + 在cate.wpy中引入cate.js

    ```
    import mixinCates from '@/mixins/tabs/cates.js'
    export default class Cates extends wepy.page {
      mixins = [mixinCates]
    }
    ```

+ 获取分类数据列表

  + 在cates.js中定义获取分类数据的方法

    ```javascript
    // 获取分类数据
      async getCatesList () {
        var { data: res } = await wepy.get('/categories')
        if(res.meta.status !== 200){
          return wepy.baseToast('获取分类数据失败')
        }

        this.cates = res.message
        this.$apply()
      }
    ```

  + 在onLoad中调用该方法获取数据

    ```javascript
    this.getCatesList()
    ```

+ 下载安装vant小程序ui组件库

  [vant官网](https://youzan.github.io/vant-weapp/#/intro)

  + 在github中下载vant-weapp源码文件

  + 将源码解压之后，将里面的src改名为vant，并复制到自己项目中components中一份

  + 上面两个步骤可以忽略，直接将资料中的vant复制到自己项目components中

  + 在app.wpy中注册使用的组件为全局组件

    ```
    "usingComponents": {
      "van-sidebar": "./components/vant/sidebar/index",
      "van-sidebar-item": "./components/vant/sidebar-item/index"
    }
    ```

  + 在cates中使用sidebar组件

    ```
    <van-sidebar>
      <van-sidebar-item title="标签名称" />
      <van-sidebar-item title="标签名称" />
      <van-sidebar-item title="标签名称" />
    </van-sidebar>
    ```

##### 渲染一级分类并优化滚动效果

+ 渲染左侧一级分类的结构

  ```html
  <view class="cate_box">
      <van-sidebar>
        <van-sidebar-item wx:for="{{cates}}" title="{{item.cat_name}}" wx:key="index" />
      </van-sidebar>
  </view>
  ```

+ 分类-使用scroll-view优化左侧分类的滚动效果

  ```html
  <view class="cate_box">
    <scroll-view class="left" scroll-y>
      <van-sidebar>
        <van-sidebar-item wx:for="{{cates}}" title="{{item.cat_name}}" wx:key="index" />
      </van-sidebar>
    </scroll-view>
  </view>
  ```

  ```css
  page{
    height:100%;
  }
  .cate_box{
    height:100%;
    .left{
      height:100%;
      width: 85px;
    }
  }
  ```


#### 03- 分类页的实现 （下）

##### 准备二级分类数据和基本结构

+ 分类-根据一级分类的变化动态切换二级分类数据

  + 给一级分类注册change事件

    ```html
    <van-sidebar bind:change="sidebarChange" active="0">
      <van-sidebar-item wx:for="{{cates}}" title="{{item.cat_name}}" wx:key="index" />
    </van-sidebar>
    ```

  + 定义对应的sidebarChange事件函数

    ```js
    sidebarChange (e) {
      console.log(e.detail)
      this.secondCates = this.cates[e.detail].children
    }
    ```

  + 在获取一级分类的时候默认直接给二级分类赋值

    ```js
    this.secondCates = res.message[0].children
    ```

+ 分类-右侧完成结构

  + 右侧结构实现

    ```html
    <scroll-view scroll-y class="right">
          <view class="secondItem">
            <text class="tit">/ 圣诞狂欢 /</text>
            <!-- 三级分类 -->
            <van-grid column-num="3" border="{{ false }}">
              <van-grid-item use-slot wx:for="{{ 3 }}" wx:for-item="index">
                <image
                  style="height: 80px; width:80px;"
                  src="https://api.zbztb.cn/full/37e2413fad20445374180542fce4d06d0cb38545.jpg"
                />
                <text>图片</text>
              </van-grid-item>
            </van-grid>
          </view>
        </scroll-view>
    ```

  + 右侧样式美化

    ```css
    .right {
      height: 100%;
        .second_item {
          text-align: center;
          .tit {
            line-height: 80rpx;
            font-size: 28rpx;
          }
          image {
            width: 120rpx !important;
            height: 120rpx !important;
          }
          .name{
            font-size: 30rpx;
            line-height: 70rpx;
          }
          
        }
      }
    ```

##### 渲染二级分类和三级分类

+ 分类-渲染二级分类

  + 根据数据渲染二级分类，因为要渲染多个结构所以用block包裹

    ```html
    <block wx:for="{{secondCates}}" wx:key="index">
      <!-- 二级分类标题 -->  
      <view class="second_tit">/  {{item.cat_name}}  /</view>
      <!-- 三级分类 -->
      <van-grid column-num="3" border="{{ false }}">
        <van-grid-item use-slot wx:for="{{ 3 }}" wx:for-item="index">
          <image
                 style="width: 100%; height: 90px;"
                 src="https://img.yzcdn.cn/vant/apple-{{ index + 1 }}.jpg"
                 />
          <view class="img_tit">曲面电视</view>
        </van-grid-item>
      </van-grid>
    </block>
    ```

+ 分类-渲染三级分类

  + 根据数据渲染三级分类

    ```html
    <!-- 三级分类 -->
    <van-grid column-num="3" border="{{ false }}">
      <van-grid-item use-slot wx:for="{{ item.children }}">
        <image
               src="{{item.cat_icon}}"
               />
        <view class="img_tit">{{item.cat_name}}</view>
      </van-grid-item>
    </van-grid>
    ```

  + 处理三级级分类间距

    ```
    .van-grid-item__content{
      padding: 5px 8px !important;
    }
    ```

+ 分类-点击三级分类跳转到商品列表页面

  ```html
  <van-grid-item 
   use-slot wx:for="{{ item.children }}" 
   link-type="navigateTo" 
   url="/pages/goods_list/index?id={{item.cat_id}}" wx:key="index">
    <image src="{{item.cat_icon}}"/>
    <view class="img_tit">{{item.cat_name}}</view>
  </van-grid-item>
  ```


#### 04 - loading效果和搜索功能

##### 利用拦截器实现loading加载

+ 拦截器-介绍wepy框架中的拦截器

+ 拦截器-实现数据加载期间的loading效果

  ```javascript
  // 拦截request请求
  this.intercept('request', {
    // 发出请求时的回调函数
    config (p) {
      wepy.showLoading({
        title: '正在加载中...'
      })
      // 必须返回OBJECT参数对象，否则无法发送请求到服务端
      return p;
    },

    // 请求成功后的回调函数
    success (p) {
      // 必须返回响应数据对象，否则后续无法对响应数据进行处理
      return p;
    },

    //请求失败后的回调函数
    fail (p) {
      // 必须返回响应数据对象，否则后续无法对响应数据进行处理
      return p;
    },

    // 请求完成时的回调函数(请求成功或失败都会被执行)
    complete (p) {
      wepy.hideLoading();
    }
  });
  ```

##### 实现搜索的基本结构和逻辑抽离

+ 搜索-演示搜索功能并自定义编译模式

+ 搜索-全局注册搜索组件并渲染到页面中

  + 利用vant的search组件完成顶部结构

  + 注册search组件

    ```
    "van-search": "assets/vant/search/index"
    ```

  + 使用search组件

    ```html
    <van-search
                value="{{ value }}"
                placeholder="请输入搜索关键词"
                use-action-slot
                bind:search="onSearch"
                >
      <view slot="action" bind:tap="onSearch">取消</view>
    </van-search>
    ```

  + 创建search.js，并定义value数据

    ```js
    import wepy from 'wepy'

    export default class extends wepy.mixin {
      data = {
        value: '11'
      }

      config = {
      }

      methods = {

      }
    }
    ```

  + 在search.wpy中引入search.js

    ```js
    import wepy from 'wepy'
    import searchMix from '@/mixins/tabs/search.js'
    export default class Search extends wepy.page {
      mixins = [searchMix]
    }
    ```

##### 实现搜索功能

+ 搜索-根据关键字的变化动态获取搜索建议列表数据

  + 给输入框注册change事件

  + 定义change事件函数

    ```js
    methods = {
      onchange (e) {
        this.value = e.detail
      }
    }
    ```

  + 在事件函数内部完成搜索逻辑

    ```js
    const {data} = await wepy.get('/goods/qsearch',{
      query:e.detail
    })

    if(data.meta.status !== 200){
      return wepy.baseToast('获取数据失败')
    }
    this.suggestList = data.message
    this.$apply()
    ```

  + 如果在输入框发生变化的时候为空，不进行搜索，并且把suggestList=[]

    ```js
    if(this.value.trim() == '') return this.suggestList = []
    ```


#### 05 - 搜索功能（下）

##### 实现搜索列表的结构和渲染

+ 利用cell单元格实现搜索的建议列表结构和渲染

  + 全局注册cell组件

    ```js
    "van-cell": "./components/vant/cell/index",
    "van-cell-group": "./components/vant/cell-group/index"
    ```

  + 使用cell组件

    ```html
    <van-cell-group>
        <van-cell title="单元格" />
      </van-cell-group>
    ```

  + 根据数据循环生成

    ```html
    <van-cell-group>
      <van-cell wx:for="{{suggestList}}" wx:key="index" title="{{item.goods_name}}" />
    </van-cell-group>
    ```

  + 给搜索结果列表添加url属性进行跳转

    ```html
    <van-cell-group>
        <van-cell url="/pages/goods_detail/index?goods_id={{item.goods_id}}" wx:for="{{suggestList}}" wx:key="index" title="{{item.goods_name}}" />
      </van-cell-group>
    ```

  + 敲回车（手机搜索按钮）搜索的时候进入商品列表页面

    + 给输入框注册search事件

    + 定义search事件函数进行跳转

      ```js
      // 点击搜索按钮
      onSearch () {
        if(this.value.trim() == '') return this.suggestList = []
        wepy.navigateTo({
          url: '/pages/goods_list/index?query='+this.value
        })
      }
      ```

##### 实现搜索历史数据缓存

+ 介绍搜索历史功能和小程序的数据缓存

  + wx.setStorageSync：向本地保存数据
  + wx.getStorageSync：获取本地数据
  + wx.removeStorageSync：移除本地数据

+ 点击搜索的时候将搜索记录存到本地

  + 在data中定义historyList数组，用来存储搜索历史的

  + 当触发搜索按钮的时候将数据存储到本地

    ```js
    // 点击搜索按钮
    onSearch () {
      if(this.value.trim() == '') return this.suggestList = []
      // 将数据存储到本地
      this.historyList.unshift(this.value)
      wepy.setStorageSync('historyList',this.historyList)
      wepy.navigateTo({
        url: '/pages/goods_list/index?query='+this.value
      })
    }
    ```

  + 优化-如果数据没有存储过在进行存储，并且之将数据的前十条进行存储

    ```js
    // 点击搜索按钮
    onSearch () {
      if(this.value.trim() == '') return this.suggestList = []

      if(this.historyList.indexOf(this.value) === -1){
        this.historyList.unshift(this.value)
      }
      this.historyList = this.historyList.slice(0,10)
      wepy.setStorageSync('historyList',this.historyList)
      wepy.navigateTo({
        url: '/pages/goods_list/index?query='+this.value
      })
    }
    ```

  + 每次重新进入页面的时候要获取本地存储的数据

    ```js
    onLoad () {
        this.historyList = wepy.getStorageSync('historyList') || []
      }
    }
    ```


#### 06 - 搜索历史功能

##### 实现搜索历史基本结构按需展示

+ 完成商品历史结构

  + 搜索历史要使用tag和icon组件，所以将tag注册为全局组件

    ```js
    "van-tag": "./components/vant//tag/index",
    "van-icon": "./components/vant/icon/index"
    ```

  + 搜索历史结构

    ```html
    <!-- 搜索历史 -->
    <view class="history">
      <view class="tit">
        <text class="left">搜索历史</text>
        <van-icon name="delete" />
      </view>
      <view class="tag_box">
        <van-tag size="medium">标签</van-tag>
      </view>
    </view>
    ```

  + 搜索历史样式

    ```
    .history{
      padding: 10rpx 20rpx;
      .tit{
        display: flex;
        justify-content: space-between;
        .left{
          font-size: 30rpx;
        }
      }
      .tag_box {
        .van-tag{
          margin-right:0 20rpx 10rpx 0;
        }
      }
    }
    ```

  + 搜索历史和搜索结果不能同时出现

    ```
    <van-cell-group hidden="{{value === ''}}">
    <view class="history"  hidden="{{value !== ''}}">
    ```

##### 动态渲染tag和删除历史功能

+ 动态渲染tag标签

  ```
  <van-tag wx:for="{{historyList}}" wx:key="index" class="tag" size="medium">{{item}}</van-tag>
  ```

+ 删除历史功能

  + 给删除按钮注册tap事件

  + 在事件函数中删除本地历史

    ```js
    // 删除本地历史
    deleteHistory () {
      this.historyList = []
      wepy.setStorageSync('historyList',this.historyList)
    }
    ```

##### 实现商品列表逻辑抽离

+ 定义商品列表编译模式添加query参数，设置页面标题

+ 抽离商品列表js

  + 在mixins下面新建goods_list.js

    ```js
    import wepy from 'wepy'

    export default class extends wepy.mixin {
      data = {
      }

      methods = {

      }

      onLoad () {
        
      }
    }
    ```
    ​



