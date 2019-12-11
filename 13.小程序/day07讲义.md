#### 小程序day07

#### 课程目标

+ 实现添加购物车功能
+ 实现全局数据的管理
+ 实现购物车列表页面的结构
+ 实现购物车列表页面的功能

#### 01 - 实现加入购物车功能

##### 实现加入购物车数据全局共享

- 给加入购物车按钮注册tap事件

  ```js
  <van-goods-action-button
          text="加入购物车"
          type="warning"
          bind:click="addToCart"
        />
  ```

- 定义addToCart事件函数

  ```js
  addToCart () {
    console.log(this.goods_detail)
  }
  ```

- 在app.wpy下的globalData中定义全局数据，和方法

  ```
  globalData = {
  	cart: []
  }
  test () {
  	console.log('我是全局的方法')
  }
  ```

- 在页面中访问全局数据和全局方法，利用$parent就能获取到全局对象

  ```
  this.$parent.globalData.cart
  this.$parent.test()
  ```

##### 点击加入购物车将商品存储到购物车中

- 在全局定义将商品保存到购物车的方法

  ```js
  addTocar (goods) {
      const goodsObj = {
        // 商品id
        id: goods.goods_id,
        // 商品名称
        name: goods.goods_name,
        // 商品价格
        price: goods.goods_price,
        // 商品数量
        count: 1,
        // 商品图片
        pic: goods.goods_big_logo,
        // 是否购买
        isCheck: true
      }
      this.globalData.cart.push(goodsObj)
      console.log(this.globalData.cart)
    }
  ```

- 点击购物车的事件函数中调用上面的方法

  ```js
  this.$parent.addTocar(this.goods_detail)
  ```

- 防止购物车数据重复添加

  ```js
  addTocar (goods) {
    const index = this.globalData.cart.findIndex(item=>{
      return item.id == goods.goods_id
    })
    if(index!==-1){
      this.globalData.cart[index].count++
    }else{
      const goodsObj = {
        // 商品id
        id: goods.goods_id,
        // 商品名称
        name: goods.goods_name,
        // 商品价格
        price: goods.goods_price,
        // 商品数量
        count: 1,
        // 商品图片
        pic: goods.goods_big_logo,
        // 是否购买
        isCheck: true
      }
      this.globalData.cart.push(goodsObj)
    }
    console.log(this.globalData.cart)
  }
  ```

##### 将数据进行持久化存储

- 封装saveCart方法

  ```
  wepy.sesaveCart () {
      wepy.setStorageSync('carts',this.globalData.cart)
    }
  ```

- 在全局的addTocar中调用该方法

  ```
  this.saveCart()
  ```

- 在onLaunch中获取本地数据交给cart

  ```
  onLaunch() {
      this.globalData.cart = wepy.getStorageSync('carts') || []
    }
  ```

#### 02 - 实现购物车列表页面基本结构

##### 自定义编译模式并美化空白购物车的页面结构

- 在商品详情页点击购物车进入购物车页面

  ```html
  <van-goods-action-icon
          icon="cart-o"
          text="购物车"
          link-type="switchTab"
          url="/pages/tabs/cart"
        />
  ```

- 自定义编译模式

- 定义空的购物车页面

  - 结构

    ```html
    <view class="carts">
        <view class="empty">
          <image src="http://destiny001.gitee.io/image/cart_empty.png"></image>
          <view>哎呦，购物车是空的啊！</view>
        </view>
      </view>
    ```

  - 样式

    ```css
    .carts{
      .empty{
        text-align: center;
        padding-top: 50rpx;
        image{
          width: 120rpx;
          height:120rpx;
          margin: 20rpx 0;
        }
        view{
          font-size: 30rpx;
          color: #666;
        }
      }
    }
    ```

##### 实现空白页面和非空页面的显示

- 定义非空页面

  ```html
  <view class="empty">
    <image src="../../assets/cart_empty.png"></image>
    <view>哎呦，购物车是空的啊！</view>
  </view>
  <!-- 非空页面 -->
  <view class="cart_list">
    购物车列表
  </view>
  ```

- 抽离cart逻辑js

  - 新建cart.js

    ```js
    import wepy from 'wepy'

    export default class extends wepy.mixin {
      data = {
        test: '购物车'
      }

      methods = {

      }
    }
    ```

  - 在cart.wpy中导入cart.js

    ```js
    import mix from '@/mixins/tabs/cart'
    export default class Cart extends wepy.page {
      mixins = [mix]
    }
    ```

- 将全局数据转存到当前组件上

  ```
  onLoad () {
      this.cart = this.$parent.globalData.cart
    }
  ```

- 定义计算属性是否显示empty

  ```js
  computed = {
      isEmpty () {
        return this.cart.length <= 0 ? true : false
      }
    }
  ```

- 将通过isEmpty控制空和非空的显示

  ```html
  <view class="empty" wx:if="{{isEmpty}}">
    <image src="../../assets/cart_empty.png"></image>
    <view>哎呦，购物车是空的啊！</view>
  </view>
  <!-- 非空页面 -->
  <view class="cart_list" wx:else>
    购物车列表
  </view>
  ```

##### 实现购物车列表基本结构

- 实现购物车列表标题

  ```html
  <van-cell title="购物车列表" icon="cart-o" />
  ```



- 实现购物车列表结构--使用插槽实现价格和修改数量的结构

  ```html
  <view class="cart_list" wx:else>
    <van-cell title="购物车列表" icon="cart-o" />
    <van-card title="商品标题" thumb="http://destiny001.gitee.io/image/none.png">
      <!-- 利用描述信息插槽实现商品价格和数量 -->
      <view class="desc" slot="desc">
        <text class="price">￥128</text>
        <!--引入步进器组件 "van-stepper": "./components/vant/stepper/index" -->
        <van-stepper value="{{ 1 }}" bind:change="onChange" />
      </view>
    </van-card>
  </view>
  ```

- 样式

  ```css
  .cart_list {
    .desc {
      display: flex;
      justify-content:space-between;
      align-items: center;
      .price{
        color: red;
      }
    }
  }
  ```




#### 03  -  商品列表图品部分和数据渲染

##### 实现图片自定义插槽和数据动态渲染

+ 结构

  + 因为要用到复选款，所以引入复选框组件

    ```
    "van-checkbox": "./components/vant/checkbox/index",
    ```

  + 利用图片插槽实现图片区域

    ```html
    <view class="cart_list" wx:else>
      <van-cell title="购物车列表" icon="cart-o" />
      <van-card title="商品标题">
        <!-- 利用描述信息插槽实现商品价格和数量 -->
        <view class="desc" slot="desc">
          <text class="price">￥128</text>
          <van-stepper value="{{ 1 }}" bind:change="onChange" />
        </view>
        <!-- 利用图片的插槽实现图片和复选框 -->
        <view class="thumb" slot="thumb">
          <van-checkbox value="{{ checked }}" bind:change="onChange"></van-checkbox>
          <image src="http://destiny001.gitee.io/image/none.png"></image>
        </view>
      </van-card>
    </view>
    ```

+ 样式

  ```css
  .cart_list {
      .desc {
        display: flex;
        justify-content:space-between;
        align-items: center;
        .price{
          color: red;
        }
      }

      .thumb {
        display: flex;
        align-items: center;
        width: 110px;
        justify-content: space-between;
        image{
          width: 90px;
          height: 90px;
          margin-left: 5px;
        }
      }
    }

   .van-card__thumb {
  	width: 110px !important;
    }
  ```


+ 利用wx:for循环生成购物车列表

  ```html
  <van-card title="{{item.name}}" wx:for="{{cart}}" wx:key="index">
    <!-- 利用描述信息插槽实现商品价格和数量 -->
    <view class="desc" slot="desc">
      <text class="price">￥{{item.price}}</text>
      <van-stepper value="{{ item.count }}" bind:change="onChange" />
    </view>
    <!-- 利用图片的插槽实现图片和复选框 -->
    <view class="thumb" slot="thumb">
      <van-checkbox value="{{ item.isCheck }}" bind:change="onChange"></van-checkbox>
      <image src="{{item.pic}}"></image>
    </view>
  </van-card>
  ```

##### 数量发生变化将数据进行同步

+ 注册change事件并且将id传递进事件函数内部

   ```html
   <van-stepper data-id="{{item.id}}" value="{{ item.count }}" bind:change="countChange" />
   ```


+ 定义chang的事件函数

  ```js
  countChange (e) {
    console.log(e)
    console.log(e.detail)
    console.log(e.target.dataset.id)
  }
  ```


+ 实现商品数量的同步

  + 在全局定义更新数量的方法

    ```js
    // 更新商品数量
      updateCount (id,count) {
        const i = this.globalData.cart.findIndex(x=>x.id == id)
        this.globalData.cart[i].count = count
        this.saveCart()
      }
    ```

  + 调用该方法进行更新

    ```js
    countChange (e) {
      this.$parent.updateCount(e.target.dataset.id,e.detail)
    }
    ```


#### 04 - 是否购买check同步和删除功能

##### 实现是否购买的check同步

+ 定义监听checkbox发生变化的事件

  ```html
  <van-checkbox ...忽略属性 data-id="{{item.id}}" bind:change="checkChange"></van-checkbox>
  ```

+ 定义checkChange事件函数

  ```js
  checkChange (e) {
    console.log(e.detail)
    console.log(e.target.dataset.id)
  }
  ```

+ 定义同步isCheck的方法

  ```js
  // 更新isCheck
    updateIsCheck (id, status) {
      const i = this.globalData.cart.findIndex(x=>x.id == id)
      this.globalData.cart[i].isCheck = status
      this.saveCart()
    }
  ```

+ 调用该方法进行同步

  ```js
  checkChange (e) {
    this.$parent.updateIsCheck(e.target.dataset.id,e.detail)
  }
  ```


##### 实现删除的样式

+ 引入滑动单元格组件并注册

  ```html
  "van-swipe-cell": "assets/vant/swipe-cell/index"
  ```

+ 重构商品卡片结构

  ```html
  <van-swipe-cell right-width="{{ 65 }}" left-width="{{ 0.001 }}" wx:for="{{cart}}" wx:key="index">
    <van-card title="{{item.name}}">
      <!-- 利用描述信息插槽实现商品价格和数量 -->
      <view class="desc" slot="desc">
        <text class="price">￥{{item.price}}</text>
        <van-stepper value="{{ item.count }}" data-id="{{item.id}}" bind:change="countChange" />
      </view>
      <!-- 利用图片的插槽实现图片和复选框 -->
      <view class="thumb" slot="thumb">
        <van-checkbox value="{{ item.isCheck }}" data-id="{{item.id}}" bind:change="checkboxChange"></van-checkbox>
        <image src="{{item.pic}}"></image>
      </view>
    </van-card>
    <view class="rightDel" slot="right">删除</view>
  </van-swipe-cell>
  ```

+ 删除的样式

  ```css
  .cell_right {
    height: 100px;
    width: 65px;
    font-size: 30rpx;
    line-height: 100px;
    text-align: center;
    background: red;
    color: #fff;
  }
  ```


##### 实现删除的功能

+ 给删除按钮注册点击事件

  ```
  <view slot="right" class="cell_right" @tap="removeGoods({{item.id}})">删除</view>
  ```

+ 定义删除对应的事件函数

  ```js
  removeGoods (id) {
    console.log(id)
  }
  ```

+ 在app.wpy中定义删除商品的方法

  ```
  // 删除商品
    removeGoods (id) {
      const i = this.globalData.cart.findIndex(x=>x.id == id)
      this.globalData.cart.splice(i,1)
      this.saveCart()
    }
  ```

+ 在当前组件的removeGoods方法中调用全局的removeGoods并将id传入进去

  ```
  removeGoods (id) {
    this.$parent.removeGoods(id)
  }
  ```


#### 05 - 实现总价计算和全选功能

##### 利用提交订单栏实现订单区域的结构

+ 首先将提交订单栏注册为全局组件

  ```
  "van-submit-bar": "assets/vant/submit-bar/index"
  ```

+ 使用订单栏完成基本结构

  ```html
  <van-submit-bar
          price="{{ 3050 }}"
          button-text="提交订单"
          bind:submit="onClickButton"
          tip="{{ false }}"
        >
    <van-checkbox style="margin-left:10px;" value="{{ checked }}" bind:change="onChange">全选   </van-checkbox>
  </van-submit-bar>
  ```

##### 实现总价和全选的计算

+ 定义计算属性计算总价

  + 定义计算属性

    ```
    totalPrice () {
      var total = 0
      this.cart.forEach(item=>{
        if(item.isCheck){
          total+=item.count*item.price
        }
      })
      return total*100
    }
    ```

  + 使用计算属性

    ```
    <van-submit-bar ...省略其他属性 price="{{ totalPrice }}">
    ```

+ 定义全选的计算属性

  + 定义计算属性

    ```
    isFullCheck () {
      return flag = this.cart.every(item=>item.isCheck) 
    }	
    ```

  + 使用计算属性

    ```
    <van-checkbox ...省略其他属性 value="{{ isFullCheck }}">全选</van-checkbox>
    ```


##### 点击全选更新所有商品的选中状态

+ 给全选按钮注册change的事件函数

  ```
  onFullChange (e) {
    console.log(e)
  }
  ```

+ 在app.wpy中定义修改cart的方法

  ```
  // 全选
    fullChange (status) {
      this.globalData.cart.forEach(item=>{
        item.isCheck = status
      })
    }
  ```

+ cart.js中的onFullChange中调用全局组件的fullChange

  ```
  onFullChange (e) {
    this.$parent.fullChange(e.detail)
  }
  ```




​

​