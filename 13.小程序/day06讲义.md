#### 小程序-day06

.mixin中不能设置config,只能在.page中使用或者在全局也是可以的

在page中如果想抽离js逻辑，抽离的文件内部必须继承wepy.mixin，因为会混入的时候 mixins = []，能够将wepy.mixin的代码混入进来

#### 01 - 搜索历史功能

##### 实现搜索历史基本结构按需展示

- 完成商品历史结构

  - 搜索历史要使用tag和icon组件，所以将tag注册为全局组件

    ```js
    "van-tag": "./components/vant//tag/index",
    "van-icon": "./components/vant/icon/index"
    ```

  - 搜索历史结构

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

  - 搜索历史样式

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

  - 搜索历史和搜索结果不能同时出现

    ```
    <van-cell-group hidden="{{value === ''}}">
    <view class="history"  hidden="{{value !== ''}}">
    ```

##### 动态渲染tag和删除历史功能

- 动态渲染tag标签

  ```
  <van-tag wx:for="{{historyList}}" wx:key="index" class="tag" size="medium">{{item}}</van-tag>
  ```

- 删除历史功能

  - 给删除按钮注册tap事件

  - 在事件函数中删除本地历史

    ```js
    // 删除本地历史
    deleteHistory () {
      this.historyList = []
      wepy.setStorageSync('historyList',this.historyList)
    }
    ```

- 点击tag进入商品列表

  + 给tag注册点击事件

    ```html
    <view class="tag_box">
            <van-tag @tap="goGoodsList({{item}})" size="medium" wx:for="{{history}}" wx:key="index">{{item}}</van-tag>
          </view>
    ```

  + 定义goGoodsList事件函数

    ```js
    // 点击tag进入商品列表
        goGoodsList (query) {
          wepy.navigateTo({
            url: '/pages/goods_list/index?query='+query
          })
        }
    ```


##### 实现商品列表逻辑抽离

- 定义商品列表编译模式添加query参数

- 抽离商品列表js

  - 在mixins下面新建goods_list.js

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

  - 在商品列表页面导入goods_list.js，并利用mixins混入进来

- 通过config来设置页面标题



#### 02 - 实现商品列表数据渲染和上拉加载更多

##### 实现商品列表数据渲染

+ 参数的整理

- 获取商品列表数据

  - 定义获取商品列表数据的方法

    ```js
    // 获取商品列表数据
      async getGoodsList () {
        const queryData = {
          cid: this.cid,
          query: this.query,
          pagenum: this.pagenum,
          pagesize: this.pagesize
        }
        var {data: res} = await wepy.get('/goods/search',queryData)
        if(res.meta.status !== 200){
          return wepy.baseToast('获取数据失败')
        }
        this.goodsList = res.message.goods 
        this.total = res.message.total
        this.$apply()
      }
    ```

  - 在onLoad中调用getGoodsList方法

    ```js
    this.getGoodsList()​
    ```

- 完成商品列表的静态结构

  - 引入商品卡片组件

    ```
    "van-card": "assets/vant/card/index"
    ```


- 完成基本结构

  ```html
  <van-card
      custom-class="goods_item"
      wx:for="{{goodsList}}"
      wx:key="index"
      num="{{item.goods_number}}"
      price="{{item.goods_price}}"
      title="{{item.goods_name}}"
      thumb="{{ item.goods_small_logo || 'http://destiny001.gitee.io/image/none.png' }}"
    />
  ```

- css样式

  ```css
  .goods_item{
    border-bottom: 4rpx solid #ccc;
  }
  ```

##### 实现上拉加载更多

- 实现上拉加载更多基本功能

  - 通过onReachBottom来实现加载更多操作

    ```js
    onReachBottom () {
      console.log('触底了')
    }
    ```

  - 实现加载更多逻辑

    ```
    onReachBottom () {
      console.log('触底了')
      this.pagenum++
      this.getGoodsList()
    }
    ```

  - 修改获取数据之后复制给goodsList的方式

    ```
    this.goodsList = [...this.goodsList,...res.message.goods] 
    ```

- 实现防止重复发起请求

  - 首先定义isLoading: false

  - 在每次将要发起请求时将isLoading修改为true，也就是在getGoodsList里面的发送请求之前

  - 等数据加载成功之后将isLoading修改为false

  - 在onReachBottom中判断

    ```
    if(this.isLoading) return
    ```

- 判断数据是否加载完毕

  - 判断数据是否还有更多

    ```
    onReachBottom () {
        if(this.isLoading) return
        // 判断数据时候加载完成
        if(this.pagenum*this.pagesize>=this.total) {
          return 
        }
        this.pagenum++
        this.getGoodsList()
      }
    ```

  - 定义加载完毕的结构·

    ```
    <view class="isOver">-------客观没油了哦-------</view>
    ```

    ```css
    .isOver{
      height: 70rpx;
      border-top:3rpx solid #ccc;
      text-align: center;
      line-height: 70rpx;
      font-size: 30rpx;
      color: #ccc;
    }
    ```

  - 定义hiddenIsOver数据默认为true，控制加载完毕的结构按需显示

    ```html
    <view hidden="{{hiddenIsOver}}" class="isOver">-------客观没油了哦-------</view>
    ```

  - 当所有数据加载完毕将hiddenIsOver修改为false

    ```js
    onReachBottom () {
        if(this.isLoading) return
        if(this.pagenum*this.pagesize>=this.total) {
          // 当所有数据加载完毕将hiddenIsOver修改为false
          return this.hiddenIsOver = false
        }
        this.pagenum++
        this.getGoodsList()
      }
    ```

    ​

#### 03 - 实现商品列表下拉刷新和商品详情轮播图

##### 实现下拉刷新

+ 开启下拉刷新，并配置样式

  ```js
  enablePullDownRefresh: true,
  backgroundColor: '#ccc',
  backgroundTextStyle: 'light'
  ```

+ 通过onPullDownRefresh监听下拉行为

  ```js
  onPullDownRefresh () {
    console.log('下拉了')
  }
  ```

+ 触发下拉，重新请求数据，并在获取数据之前，将部分数据设置到初始状态

  ```js
  onPullDownRefresh () {
      console.log('下拉了')
      this.pagenum = 1
      this.isLoading = false 
      this.hiddenIsOver = true
      this.goodsList = []
      this.getGoodsList()
    }
  ```

+ 当下拉完成之后手动关闭下拉，通过回调函数的方式进行关闭

  ```js
  this.getGoodsList(function(){
    wepy.stopPullDownRefresh()
  })
  ```

  ```js
  // getGoodsList函数内部最下边
  if(cb){
    cb()
  }
  ```

##### 实现点击商品列表进入商品详情页

+ 通过thumb-link设置点击图片进行跳转，并将id带过去

  ```html
  thumb-link="/pages/goods_detail/index?goods_id={{item.goods_id}}"
  ```

+ 优化暂无图片

  ```
  thumb="{{ item.goods_small_logo || '/assets/none.png'}}"
  ```


##### 商品详情页-功能演示以及自定义编译模式

##### 获取商品详情页的数据

+ 抽离商品详情js逻辑

  ```js
  import wepy from 'wepy'

  export default class extends wepy.mixin {
    data = {
      test: 123
    }

    methods = {

    }
  }
  ```

+ 将抽离的逻辑文件在goods_detail中引入进来

  ```js
  import wepy from 'wepy'
  import mix from '@/mixins/goods_detail'
  export default class Home extends wepy.page {
    mixins = [mix]
  }
  ```

+ 在onLoad中获取到参数保存到data上

  ```js
  onLoad (options) {
      this.goods_id = options.goods_id
    }
  ```

+ 定义获取商品详情的方法获取商品详情的数据

  ```js
  // 获取商品详情的数据
    async getGoodsDetail () {
      var { data: res } = await wepy.get('/goods/detail',{
        goods_id:this.goods_id
      })
      if(res.meta.status !== 200){
        return wepy.baseToast('获取数据失败')
      }

      this.goods_detail = res.message
      this.$apply()
    }
  ```

+ 在onLoad中调用该方法

  ```
  this.getGoodsDetail()
  ```


##### 渲染轮播图区域

+ 利用swiper和swiper-item组件实现结构

  ```html
  <swiper>
    <swiper-item>
      1
    </swiper-item>
    <swiper-item>
      1
    </swiper-item>
  </swiper>
  ```

+ 根据数据渲染轮播图

  ```html
  <view class="goods_detail">
    <swiper indicator-dots circular>
      <swiper-item wx:for="{{goods_detail.pics}}" wx:key="index">
        <image src="{{item.pics_big_url}}"></image>
      </swiper-item>
    </swiper>
  </view>
  ```

+ 轮播图样式设置

  ```css
  .goods_detail{
    swiper{
      height:750rpx;
      image{
        width:100%;
        height: 100%;
      }
    }
  }
  ```

#### 04 - 实现商品详情轮播图效果和运费、促销的结构

wx.previewImage可以实现图片预览，参数是一个配置对象

urls：值是数组，['1.jpg','2.jpg']  

current: 值是图片地址，是当前预览的图片地址

##### 实现轮播图图片预览效果

+ 给图片注册点击事件

  ```html
  <image @tap="previewImage({{item.pics_big_url}})" src="{{item.pics_big_url}}"></image>
  ```

+ 在事件函数内部通过previewImage进行图片预览

  ```js
  previewImage (currentUrl) {
    const urls = this.goods_detail.pics.map(item=>{
      return item.pics_big_url
    })
    const current = currentUrl
    wepy.previewImage({urls,current})
  }
  ```


##### 绘制价格名称运费区域和数据渲染

+ 价格运费区域的结构

  ```html
  <view class="info">
    <view class="box1">
      <view class="price">￥168</view>
      <view class="goods_name">
        <view class="left">
          卡奇莱德汽车车载空气净化器负离子除甲醛PM2.5除烟异味车用氧吧双涡轮出风（红色）
        </view>
        <view class="right">
          <van-icon name="star-o" />
          <text>收藏</text>
        </view>
      </view>
      <view class="express">快递：免运费</view>
    </view>
  </view>
  ```

+ 价格运费区域样式

  ```css
  .info{
    .box1{
      padding: 10rpx 20rpx;
      .price{
        color: red;
        font-size: 35rpx;
      }
      .goods_name {
        display: flex;
        padding: 15rpx 0;
        .left{
          font-size: 28rpx;
        }
        .right {
          width: 200rpx;
          margin-left: 10rpx;
          text-align: center;
          border-left:1px solid #ccc;
          text{
            font-size: 28rpx;
            display: block;
          }
        }
      }

      .express {
        color: #333;
        font-size: 28rpx;
        font-weight: bold;
      }
    }
  }
  ```

+ 渲染数据

#####完成促销已选的结构和样式

+ 结构

  ```html
  <view class="line"></view>
  <view class="box2">
    <view>
      <text>促销</text>
      <text>满200元减400元</text>
    </view>
    <view>
      <text>已选</text>
      <text>黑色/s/1件</text>
    </view>
  </view>
  <view class="line"></view>
  ```

+ 样式

  ```css
  .box2 {
    padding: 0rpx 20rpx;
    view{
      font-size: 28rpx;
      padding: 20rpx 0;
      text:nth-child(2){
        margin-left: 10rpx;
        color: #999;
      }
    }
  }
  ```

#### 05 - 实现收货地址和tab页签

##### 绘制收货地址区域

+ 结构

  ```html
  <view class="box3">
    <view>
      <text>送至</text>
      <text>请选择收货地址</text>
    </view>
    <van-icon name="arrow" />
  </view>
  ```

+ 样式

  ```css
  .box3{
    padding: 0rpx 20rpx;
    display: flex;
    justify-content: space-between;
    align-items: center;
    view{
      font-size: 28rpx;
      padding: 20rpx 0;
      text:nth-child(2){
        margin-left: 10rpx;
        color: #999;
      }
    }
  }
  ```


##### 完成tab标签页结构

+ 全局注册vant的Tab标签页

  ```js
  "van-tab": "assets/vant/tab/index",
  "van-tabs": "assets/vant/tabs/index"
  ```

+ 使用tabs标签页

  ```html
  <van-tabs active="{{ active }}" bind:change="onChange">
    <van-tab title="图文详情">图文详情</van-tab>
    <van-tab title="规格参数">规格参数</van-tab>
  </van-tabs>
  ```

##### 完成规格参数

+ 完成规格参数结构

  ```html
  <van-tab title="规格参数">
    <view class="params_row">
      <text>名字</text>
      <text>信息</text>
    </view>
  </van-tab>
  ```

+ 样式

  ```css
  .params_row {
    display: flex;
    text{
      width: 50%;
      border-right:1px solid #eee;
      border-bottom:1px solid #eee;
      text-align: center;
      line-height: 80rpx;
      color: #333;
      font-size: 30rpx;
    }
    text:nth-child(2) {
      border-right:none;
    }
  }
  ```

+ 动态渲染规格参数

  ```html
  <van-tab title="规格参数">
    <view wx:for="{{ goods_detail.attrs }}" wx:key="index" class="params_row">
      <text>{{item.attr_name}}</text>
      <text>{{item.attr_vals}}</text>
    </view>
  </van-tab>
  ```


##### 完成图文详情

+ 利用rich-text组件解析html

  ```html
  <rich-text nodes="{{goods_info.goods_introduce}}"></rich-text>
  ```

+ 我们当前的图文详情图片在ios下面是显示不出来的，是因为后端返回来的数据图片，最终的后缀名是webp,ios不能识别


##### 点击收货地址选择收货地址

+ 给收货地址注册点击事件

  ```
  <view class="box3" @tap="chooseAddress">
  ```

+ 定义选择收货地址的事件函数，获取到地址后，存储到data和storage中

  ```js
  // 选择收货地址
  async chooseAddress () {
    // 小程序开发文档中 api》开放接口
    const res = await wepy.chooseAddress()
    console.log(res)
    if(res.errMsg !== 'chooseAddress:ok') {
      return wepy.baseToast('获取收货地址失败')
    }
    wepy.baseToast('已选择地址')
    this.addressInfo = res 
    wepy.setStorageSync('address',res)
    this.$apply()
  }
  ```

+ 每次重新进入页面的时候如果本地有直接获取本地

  ```js
  onLoad (options) {
    this.goods_id = options.goods_id
    // 获取本地收货地址
    this.addressInfo = wepy.getStorageSync('address') || null
    this.getGoodsDetail()
  }
  ```

#### 06 - 实现动态展示收货地址和客服功能

##### 动态展示收货地址信息

+ 根据判断addressInfo是否为null来动态展示，此处定义计算属性

  ```js
  computed = {
    formatAddressInfo () {
      if(this.addressInfo === null) {
        return '请选择收货地址'
      }else{
        const { provinceName, cityName, countyName,detailInfo } = this.addressInfo
        return provinceName+cityName+countyName+detailInfo
      }
    }
  }
  ```

+ 将计算属性展示到页面

  ```html
  <view>
    <text>送至</text>
    <text>{{formatAddressInfo}}</text>
  </view>
  ```

##### 商品导航和客服功能

+ 注册商品导航组件

  ```js
  "van-goods-action": "assets/vant/goods-action/index",
  "van-goods-action-icon": "assets/vant/dist/goods-action-icon/index",
  "van-goods-action-button": "assets/vant/goods-action-button/index"
  ```

+ 使用导航组件

  ```html
  <van-goods-action>
    <van-goods-action-icon
      icon="chat-o"
      text="客服"
      bind:click="onClickIcon"
    />
    <van-goods-action-icon
      icon="cart-o"
      text="购物车"
      bind:click="onClickIcon"
    />
    <van-goods-action-button
      text="加入购物车"
      type="warning"
      bind:click="onClickButton"
    />
    <van-goods-action-button
      text="立即购买"
      bind:click="onClickButton"
    />
  </van-goods-action>
  ```

+ 解决层级问题

  + 给van-goods-action添加自定义类名 custom-class="goods_nav"

  + 通过类名设置层级

    ```css
    .goods_nav{
      z-index:999999;
    }
    ```

+ 实现客服功能，介绍如何添加客服

  ```html
  <van-goods-action-icon
          icon="chat-o"
          text="客服"
          bind:click="onClickIcon"
          open-type="contact"
        />
  ```



##### 实现加入购物车数据全局共享

+ 给加入购物车按钮注册tap事件

  ```js
  <van-goods-action-button
          text="加入购物车"
          type="warning"
          bind:click="addToCart"
        />
  ```

+ 定义addToCart事件函数

  ```js
  addToCart () {
    console.log(this.goods_detail)
    wepy.baseToast('添加成功','success')
  }
  ```

+ 在app.wpy下的globalData中定义全局数据，和方法

  ```
  globalData = {
  	cart: []
  }
  test () {
  	console.log('我是全局的方法')
  }
  ```

+ 在页面中访问全局数据和全局方法

  ```
  this.$parent.globalData.cart
  this.$parent.test()
  ```







​

​



