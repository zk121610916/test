#### 小程序项目day08

#### 01- 实现tabbar徽章

##### 为tabbar中的购物车添加徽章

- 在onLaunch中设置购物车的徽章

  ```
  wepy.setTabBarBadge({
    index: 3,
    text: 1+ ''
  })
  ```

##### 动态计算徽章的值

- 在onLaunch中动态计算徽章的数量

  ```js
  onLaunch() {
      this.globalData.cart = wepy.getStorageSync('cart') || []

      var total = 0;
      this.globalData.cart.forEach(item => {
        if(item.isCheck) {
          total+=item.count
        }
      });
      wepy.setTabBarBadge({
        index:3,
        text:total+''
      })
    }
  ```

- 将计算徽章的数量和设置徽章抽离出来，封装到函数里

  ```js
  // 渲染tabbarBadage的方法
    renderTabbarBadage () {
      var total = 0;
      this.globalData.cart.forEach(item => {
        if(item.isCheck) {
          total+=item.count
        }
      });
      wepy.setTabBarBadge({
        index:3,
        text:total+''
      })
    } 
  ```

- 调用该方法

  ```
  this.renderTabbarBadage()
  ```

##### 处理几个小bug

- 当没有tabbar的页面设置tabbar徽章会报错，需要捕获一下

  ```
  wepy.setTabBarBadge({
    index: 3,
    text: total+ ''
  }).catch(err=>err)
  ```

- 当购物车数据发生变化重新设置tabbar的徽章

  ```
  saveCart () {
      wepy.setStorageSync('carts',this.globalData.cart)
      this.renderTabbarBadage()
    }
  ```

- 设置每次进入购物车页面都要设置tabbar的徽章

  如果该页面想重新走onLoad生命周期，只能将该页面卸载（销毁），当使用wx.navigateBack()关闭当前页返回上一页，就会销毁当前页面。这样的话下次在进入该页面就会重新渲染该页面

  ```
  onShow () {
    this.$parent.renderTabbarBadage()
  }
  ```

##### 为详情页的购物车添加商品数量徽章

- 定义计算属性

  ```
  goods_count () {
    var total = 0
    this.$parent.globalData.cart.forEach(item=>{
      if(item.isCheck){
      	total+=item.count
      }
    })
    return total
  }
  ```

- 给info使用计算属性

  ```html
  <van-goods-action-icon
          icon="cart-o"
          text="购物车"
          info="{{goods_count}}"
          link-type="switchTab"
          url="/pages/tabs/cart"
        />
  ```

##### 提交订单跳转到确认订单页面

- 定义提交按钮的事件函数

  ```js
  onSubmitOrder () {
    if(this.totalPrice<=0) {
      return wepy.baseToast('请选择商品')
    }
    wepy.navigateTo({
      url: '/pages/order/index'
    })
  }
  ```

- 新建确认订单页面

- 将订单页面的路劲配置到app.wpy中

#### 02 - 实现货地址

##### 实现添加收货地址的结构

- 设置编译模式


- 抽离订单页面js逻辑

  ```
  import wepy from 'wepy'

  export default class extends wepy.mixin {
    data = {
      test: 1
    }

    methods = {

    }
  }
  ```

- 在订单页面中引入对应的js，通过config设置页面标题

  ```
  import mix from '@/mixins/order'
  export default class Home extends wepy.page {
    config = {
      navigationBarTitleText: '确认订单'
    }

    mixins = [mix]
  }
  ```

- 定义收货地址的结构

  - 全局注册button组件

    ```
    "van-button": "assets/vant/button/index"
    ```

  - 完成收货地址结构

    ```
    <view class="order">
        <view class="chooseAddress">
          <van-button type="info" size="small">+ 选择收货地址</van-button>
        </view>
        <image class="step_line" src="http://destiny001.gitee.io/image/cart_border.png"></image>
      </view>
    ```

  - 样式

    ```
    .order{
      .chooseAddress{
        width: 750rpx;
        height: 150rpx;
        display: flex;
        justify-content: center;
        align-items: center;
      }
      .step_line {
        height:20rpx;
        width: 750rpx;
      }
    }
    ```

##### 完成收货地址信息结构

- 结构

  ```html
  <view class="addressInfo">
    <view class="box1">
      <view class="left">收货人：张三</view>
      <view class="right">
        联系电话：13388889999
        <van-icon name="arrow" />
      </view>
    </view>
    <view class="box2">收货地址：广东省广州市海珠区新港中路397号</view>
  </view>
  ```

- 样式

  ```css
  .addressInfo {
    font-size: 28rpx;
    padding: 10rpx 20rpx;
    .box1{
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .box2{
      padding-top: 20rpx;
    }
  }
  ```

##### 完成添加收货地址和收货地址信息的动态展示

- 定义计算属性计算是否展示添加收货地址

  - 首先获取本地的收货地址

    ```
    onLoad () {
      // data中需要定义addressInfo
      this.addressInfo = wepy.getStorageSync('address') || null
    }
    ```

  - 定义计算属性

    ```js
    isShowAddress () {
      if(this.addressInfo === null) {
        return true
      }
      return false
    }
    ```

  - 使用计算属性动态展示结构

    ```html
    <view class="chooseAddress" wx:if="{{isShowAddress}}>
      <van-button type="info" size="small">+ 选择收货地址</van-button>
    </view> 
    <view class="addressInfo" wx:else>
    ```

#### 03 - 实现添加收货地址和登录功能

##### 完成添加收货地址

- 给添加收货地址注册点击事件

  ```html
  <van-button @tap="chooseAddress" type="info" size="small">+ 选择收货地址</van-button>
  ```

- 定义选择收货地址的事件函数

  ```js
  async chooseAddress () {
    const res = await wepy.chooseAddress()
    if(res.errMsg !== 'chooseAddress:ok') {
      return wepy.baseToast('选择收货地址失败')
    }
    this.addressInfo = res
    wepy.setStorageSync('address',res)
    this.$apply()
  }
  ```

- 将收货信息展示到页面

  ```html
  <view class="box1">
    <view class="left">收货人：{{ addressInfo.userName }}</view>
    <view class="right">
      联系电话：{{addressInfo.telNumber}}
      <van-icon name="arrow" />
    </view>
  </view>
  // addressStr为计算属性
  <view class="box2">收货地址：{{ addressStr }}</view>
  ```

- 定义addressStr计算属性

  ```js
  addressStr () {
    if(this.addressInfo === null) {
      return null
    }
    const {provinceName,cityName,countyName,detailInfo} = this.addressInfo
    return provinceName+cityName+countyName+detailInfo
  }
  ```

- 为联系电话添加事件

  ```
  <view class="right" @tap="chooseAddress">
  ```

##### 渲染订单列表和登陆按钮

- 实现订单列表的基本结构

  ```html
  <van-card
        num="2"
        price="2.00"
        title="商品标题"
        thumb="{{ imageURL }}"
      />
  ```

- 动态渲染商品列表

  - 将本地的购物列表数据获取出来存到data上

    ```js
    // data中要定义cart
    this.cart = wepy.getStorageSync('carts') || []
    ```

  - 循环cart

    ```html
    <van-card
          wx:for="{{cart}}"
          num="{{item.count}}"
          price="{{item.price}}"
          title="{{item.name}}"
          thumb="{{ item.pic || '/assets/none.png' }}"
        />
    ```

- 渲染登录按钮

- 解决商品列表遮挡问题

##### 实现登录功能

- 介绍登陆实现方式

  - 实现登陆功能需要调用提供的api接口（/users/wxlogin），该接口需要提供code、encryptedData、iv、rawData、signature这5个参数
  - 其中code可以调用wx.login()这个方法进行获取
  - 剩余的四个参数需要设置open-type="getUserInfo"小程序提供的方式来获取用户信息

- 实现登录功能

  - 给登陆按钮设置open-type和对应的回调函数

    ```html
    <van-button ... open-type="getUserInfo" bindgetuserinfo="getUserInfo">登录</van-button>
    ```

  - 定义获取用户信息的回调函数函数，并通过事件函数获取对应的四个参数

    ```js
    getUserInfo (even) {
      const { encryptedData,iv,rawData,signature } =  even.detail
    },
    ```

  - 获取code参数

    ```js
    async getUserInfo (even) {
      const { encryptedData,iv,rawData,signature } =  even.detail
      const {code} = await wepy.login()
    }
    ```

  - 请求登录接口，将需要的参数传递给后台，登录成功之后，将token保存到本地和data中

    ```js
    async getUserInfo (even) {
      const { encryptedData,iv,rawData,signature } =  even.detail
      const {code} = await wepy.login()
      // 请求登录接口
      const res = await wepy.post('/users/wxlogin',{
        code,encryptedData,iv,rawData,signature
      })
      console.log(res)
      if(res.data.meta.status !== 200) {
        return wepy.baseToast('登录失败')
      }
      wepy.baseToast('登录成功')
      this.token = res.data.message.token
      wepy.setStorageSync('token',res.data.message.token)
      this.$apply()
    }
    ```

  - 在onLoad中获取token保存到data中

    ```js
    onLoad () {
        this.addressInfo = wepy.getStorageSync('addressInfo') || null
        this.cart = this.$parent.globalData.cart
        this.token = wepy.getStorageSync('token') || null
      }
    ```

#### 04 - 实现支付功能--订单创建和预支付

##### 渲染支付区域并生成预支付订单

+ 按需渲染支付和登录区域

  + 实现支付订单结构

    ```html
    <van-submit-bar
          price="{{ 3050 }}"
          button-text="支付订单"
          bind:submit="onSubmitPay"
        />
    ```

    + 定义onSubmitPay事件函数

  + 定义是否登陆的计算属性

    ```js
    isLogin () {
          return this.token == '' ? false:true
        }
    ```

  - 动态渲染支付和登陆区域

    ```html
    <van-button ...省略其他属性 wx:if="{{!isLogin}}">登录</van-button>
    <van-submit-bar ...省略其他属性 wx:else/>
    ```

  - 定义总价的计算属性

    ```js
    total () {
          var total = 0
          this.cart.forEach(item=>{
            total+=item.price*item.count
          })

          return total
        }
    ```

##### 梳理支付流程

+ 根据订单信息创建订单，创建完订单之后就会有订单号
+ 根据订单号生成预支付订单，生成预支付订单之后就会得到支付所需要的一些参数
+ 根据预支付返回的参数调用微信提供的微信支付进行支付，调用微信支付后就会弹出支付，此时你可能会支付，也可能会取消支付
+ 如果取消支付，提示已取消支付，如果支付成功进行跳转即可

##### 实现订单创建和预支付

+ 创建订单
  + 访问此系列请求必须在头信息中添加token

    ```js
    config (p) {
      wepy.showLoading({
        title: '正在加载中...'
      })
      // 添加token
      if(wepy.getStorageSync('token')) {
        p.header = {
          Authorization:wepy.getStorageSync('token')
        }
      }
      // 必须返回OBJECT参数对象，否则无法发送请求到服务端
      return p;
    }
    ```

  + 在支付订单对应的事件函数中准备创建订单所需的参数

    ```js
    onSubmitPay () {
      if(this.addressInfo === null) {
        return wepy.baseToast('请选择收货地址')
      }
      const payOrderData = {
        order_price: '0.01',
        consignee_addr: this.addressStr,
        order_detail: JSON.stringify(this.cart),
        goods: this.cart.map(x=>({
          goods_id:x.id,
          goods_number:x.count,
          goods_price: x.price/100
        }))
      }
      console.log('支付订单',payOrderData)
    }
    ```

  + 调用接口创建订单

    ```js
    async onSubmitPay () {
      if(this.addressInfo === null) {
        return wepy.baseToast('请选择收货地址')
      }
      const payOrderData = {
        order_price: '0.01',
        consignee_addr: this.addressStr,
        order_detail: JSON.stringify(this.cart),
        goods: this.cart.map(x=>({
          goods_id:x.id,
          goods_number:x.count,
          goods_price: x.price/100
        }))
      }
      // 创建订单
      const {data: createOrder} = await wepy.post('my/orders/create',payOrderData)
      console.log(createOrder)
      if(createOrder.meta.status !== 200) {
        return wepy.baseToast('创建订单失败')
      }
    }
    ```

+ 根据订单号生成预支付订单

  ```js
  // 创建订单成功，发起生成预支付订单请求
  const {data: prePayData} = await wepy.post('my/orders/req_unifiedorder',{
    order_number: createOrder.message.order_number
  })

  if(prePayData.meta.status !== 200) {
    return wepy.baseToast('网络异常，重新支付')
  }
  ```

#### 05 - 实现支付和tabs结构

##### 实现微信支付

+ 生成预支付成功之后，调用微信api唤起微信支付

  ```js
  const {errMsg} = await wepy.requestPayment(prePayData.message.pay).catch(err=>err)
  // 判断支付状态
  if(errMsg === 'requestPayment:fail cancel') {
    return wepy.baseToast('您已取消支付')
  }

  wepy.baseToast('支付成功')
  wepy.navigateTo({
    url: '/pages/orderList/index'
  })
  ```


##### 订单列表页的tabs基本结构

+ 创建订单列表页基本结构

  ```html
  <view class="orderList">
    <van-tabs active="{{ active }}" bind:change="onChange">
      <van-tab title="全部">全部</van-tab>
      <van-tab title="待付款">待付款</van-tab>
      <van-tab title="已付款">已付款</van-tab>
    </van-tabs>
  </view>
  ```

+ 抽离订单列表页逻辑

  ```js
  // orderList.js
  import wepy from 'wepy'

  export default class extends wepy.mixin {
    data = {
      
    }

    methods = {

    }
  }
  ```

+ 在订单列表.wpy中引入orderList.js

  ```
  import mix from '@/mixins/orderList.js'
  export default class extends wepy.page {
    mixins = [mix]
  }
  ```


#### 06 - 完成订单列表页

##### 获取订单列表数据

+ 定义获取订单列表数据的方法

  ```js
  async getOrderList (type) {
      const {data:res} = await wepy.get('my/orders/all',{
        type
      })
      if(res.meta.status !== 200){
        return wepy.baseToast('获取订单列表失败')
      }
      this.list = res.message.orders
      this.$apply()
    }
  ```

+ 在onLoad中调用getOrderList获取数据

  ```
  onLoad () {
      this.getOrderList(1)
    }
  ```

+ 点击tabs栏发生变化的时候也获取对应数据

  ```
  tabsOnChange (even) {
    this.getOrderList(even.detail.name+1)
  }
  ```

##### 实现全部的基本结构

+ 结构

  ```html
  <van-tab title="全部">
    <view class="order_item">
      <text class="order_number">订单号：1221312313221</text>
      <van-card
                num="2"
                price="2.00"
                title="商品标题"
                thumb="{{ imageURL }}"
                />
      <text class="totalPrice">共2件商品，订单金额￥0.01</text>
    </view>
  </van-tab>
  ```

+ 样式

  ```css
  .order_item {
    border-top:10rpx solid #eee;
    .order_number{
      padding: 20rpx;
      font-size: 30rpx;
    }
    .totalPrice {
      float: right;
      line-height: 70rpx;
      font-size: 30rpx;
      color: #ccc;
    }
  }
  ```

+ 渲染数据

  ```js
  <view class="order_item" wx:for="{{list}}">
            <view class="order_number">订单号：{{item.order_number}}</view>
            <van-card
              wx:for="{{item.goods}}"
              num="{{item.goods_number}}"
              price="{{item.goods_number}}"
              title="{{item.goods_name}}"
              thumb="{{item.goods_small_logo }}"
            />
            <view class="totalPrice">共{{item.total_count}}件商品，总价{{item.total_price}}元</view>
          </view>
  ```

​