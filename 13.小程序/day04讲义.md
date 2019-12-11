#### 小程序day04

#### 课程目标

+ 能够知道如何实现项目初始化
+ 能够知道如何实现项目的tabbar和导航栏的配置
+ 能够知道如何实现轮播图的效果
+ 能够知道如何实现分类的渲染
+ 能够知道如何抽离页面的js文件
+ 能够知道如何封装消息提示、get请求、post请求
+ 能够知道如何实现楼层结构和数据渲染

#### 01项目初始化

**项目介绍**

首页、分类、搜索、商品列表、商品详情、购物车、支付、订单页面

**初始化项目**（正常流程）

+ 运行 `wepy init standard heima_ugo` 命令，初始化小程序项目
+ 运行 `cd heima_ugo` 进入项目根目录
+ 运行 `npm install` 安装所有依赖项
+ 运行 `wepy build --watch` 命令，开启 wepy 项目的实时编译功能
+ 打开微信开发者工具，加载 wepy 项目并查看效果

**初始化项目（上课流程） **

+ 将wxapp_wepy压缩包，解压到wxapp_wepy文件里面
+ 修改project.config.json里面的appid和projectname
+ 通过cnpm i下载项目依赖包
+ 编译小程序：wepy build --watch
+ 在开发者工具中打开项目



**梳理项目结构**

+ 清理并重置 `src -> pages -> index.wpy` 首页，index.wpy全部删除，重新创建基本结构
+ 清理并重置 `src -> app.wpy` 中的代码，将 `style` 和 `script` （onLaunch）标签中，不必要的代码删除掉
+ 清空 `src -> components` 和 `src -> mixins` 目录
+ 将梳理完毕后的项目，上传至码云


#### 02-完成项目的外观配置并获取轮播图的数据

##### 导航栏和tabbar的配置 

- 初始化项目-配置导航栏相关的样式

  - 修改背景色、下拉loading样式、标题文字、导航文字颜色

    ```
    backgroundTextStyle: 'dark',
    navigationBarBackgroundColor: '#FF0000',
    navigationBarTitleText: '黑马优购',
    navigationBarTextStyle: 'white'
    ```

- 初始化项目-完成tabbar

  + 首先准备好对应的5个页面和图标

  + 将5个页面的路劲配置到app.wpy中的config下面的pages里

    ```javascript
    pages: [
      'pages/tabs/home',
      'pages/tabs/cates',
      'pages/tabs/search',
      'pages/tabs/carts',
      'pages/tabs/my'
    ]
    ```

  + 错误记录pages/tabs/home.js 出现脚本错误或者未正确调用 Page()

    再次进入对应的页面重新保存一下即可

  + 在app.wpy》config利用tabBar配置小程序的tabbar

    ```javascript
    tabBar: {
      list: [
        {
          pagePath: 'pages/tabs/home',
          text: '首页',
          iconPath: 'assets/icons/home.png',
          selectedIconPath: 'assets/icons/home-active.png'
        },
        {
          pagePath: 'pages/tabs/cates',
          text: '分类',
          iconPath: 'assets/icons/cates.png',
          selectedIconPath: 'assets/icons/cates-active.png'
        },
        {
          pagePath: 'pages/tabs/search',
          text: '搜索',
          iconPath: 'assets/icons/search.png',
          selectedIconPath: 'assets/icons/search-active.png'
        },
        {
          pagePath: 'pages/tabs/cart',
          text: '购物车',
          iconPath: 'assets/icons/cart.png',
          selectedIconPath: 'assets/icons/cart-active.png'
        },
        {
          pagePath: 'pages/tabs/my',
          text: '我的',
          iconPath: 'assets/icons/my.png',
          selectedIconPath: 'assets/icons/my-active.png'
        }
      ]
    }
    ```

##### 获取轮播图的数据

- 首页-为异步API启用Promise功能

  ```
  this.use('promisify')
  ```

- 首页-获取轮播图的数据

  + 首先定义一个方法进行获取轮播图的数据
    + 发送请求获取数据

      ```javascript
      // 获取轮播图的数据
        async getSwiperData () {
          var {data: res} = await wepy.request({
            url: 'https://uinav.com/api/public/v1/home/swiperdata'
          })
          console.log(res)
        }
      ```

    + 将获取回来的数据保存到data上

      ```javascript
      // 获取轮播图的数据
        async getSwiperData () {
          var {data: res} = await wepy.request({
            url: 'https://uinav.com/api/public/v1/home/swiperdata'
          })
          console.log(res)
          if(res.meta.status !== 200) {
            return console.log('获取数据失败')
          }
          this.swiperData = res.message
          this.$apply()
        }
      ```

  + 在小程序生命周期onLoad中去调用该方法

    ```javascript
    onLoad () {
      this.getSwiperData()
    }
    ```

#### 03 - 完成轮播图获取分类数据

##### 实现轮播图功能

- 首页-使用wepy.showToast进行弹框提示

  + 将获取数据失败的打印换成showToas方法进行提示

    ```javascript
    if(res.meta.status !== 200) {
      return wepy.showToast({
        title: '获取数据失败',
        icon: 'none', // 提示没有图标
      })
    }
    ```

- 首页-初步渲染轮播图效果

  + 循环swiperData数据配合swiper组件快速搭建结构

    ```javascript
    <swiper>
      <swiper-item wx:for="{{swiperData}}">
        <image src="{{item.image_src}}"></image>
      </swiper-item>
    </swiper>
    ```

- 首页-美化轮播图中图片的显示效果

  + 美化轮播图的样式

    ```javascript
    swiper{
      height:350rpx;
      navigator,image{
        width: 100%;
        height: 100%;
      }
    }
    ```

  + 利用api修饰轮播图

    + 给swiper添加indicator-dots，显示轮播图的点
    + 给swiper添加circular，控制轮播图衔接滚动

- 首页-点击轮播图实现页面跳转功能

  + 设置navagator组件的url进行页面跳转

    ```javascript
    <navigator url="{{ item.navigator_url }}">
      <image src="{{item.image_src}}"></image>
    </navigator>
    ```

  + 在pages文件夹里新建goods_detail文件夹在新建index页面

  + 将商品详情页的路劲配置到app.wpy中

    ```
    'pages/goods_detail/index'
    ```

##### 获取分类数据

- 首页-获取首页分类选项数据

  + 定义获取分类数据的方法

    ```javascript
    // 获取分类数据
    async getCatesData () {
      var {data: res} = await wepy.request({
        url: 'https://uinav.com/api/public/v1/home/catitems'
      })
      console.log(res)
      // 判断数据是否获取成功
      if(res.meta.status !== 200) {
        return wepy.showToast({
          title: '获取数据失败',
          icon: 'none', // 提示没有图标
        })
      }
      this.catesData = res.message 
      this.$apply()
    }
    ```

  + 在onLoad中调用该方法

    ```
    this.getCatesData()
    ```

#### 04 - 完成分类渲染和美化并且优化功能

##### 首页- 完成分类的结构和渲染

+ 完成分类的静态结构

  ```
  <!-- 分类 -->
    <view class="cate_box">
      <navigator>
        <image src="https://api.zbztb.cn/pyg/icon_index_nav_4@2x.png"></image>
      </navigator>
      <navigator>
        <image src="https://api.zbztb.cn/pyg/icon_index_nav_4@2x.png"></image>
      </navigator>
      <navigator>
        <image src="https://api.zbztb.cn/pyg/icon_index_nav_4@2x.png"></image>
      </navigator>
      <navigator>
        <image src="https://api.zbztb.cn/pyg/icon_index_nav_4@2x.png"></image>
      </navigator>
    </view>
  ```

+ 样式美化结构

  ```
  .cate_box {
      display: flex;
      navigator{
        width:25%;
        text-align: center;
        padding: 40rpx 0rpx;
        image{
          width: 128rpx;
          height: 140rpx;
        }
      }
    }
  ```

+ 根据数据渲染分类

  ```
  <!-- 分类 -->
    <view class="cate_box">
      <navigator wx:for="{{ catesData }}" wx:key="index">
        <image src="{{item.image_src}}"></image>
      </navigator>
    </view>
  ```

+ 点击分类进行跳转

  ```
  <navigator hover-class="none" url="/pages/tabs/cates" open-type="switchTab" wx:for="{{ catesData }}" wx:key="index">
    <image src="{{item.image_src}}"></image>
  </navigator>
  ```

##### 优化-抽离逻辑

+ 在mixins文件夹里创建tabs文件夹，在tabs文件夹内部在创建home.js

+ 将home.wpy的逻辑抽离到home.js里面

  ```javascript
  import wepy from 'wepy'
  export default class Home extends wepy.mixin {
    data = {
      swiperData: [],
      catesData: []
    }
      
    // 获取轮播图的数据
    async getSwiperData () {
      var {data: res} = await wepy.request({
        url: 'https://uinav.com/api/public/v1/home/swiperdata'
      })
      if(res.meta.status !== 200) {
        return wepy.showToast({
          title: '获取数据失败',
          icon: 'none', // 提示没有图标
        })
      }
      this.swiperData = res.message
      this.$apply()
    }

    // 获取分类数据
    async getCatesData () {
      var {data: res} = await wepy.request({
        url: 'https://uinav.com/api/public/v1/home/catitems'
      })
      console.log(res)
      if(res.meta.status !== 200) {
        return wepy.showToast({
          title: '获取数据失败',
          icon: 'none', // 提示没有图标
        })
      }
      this.catesData = res.message 
      this.$apply()
    }

    onLoad () {
      this.getSwiperData()
      this.getCatesData()
    }
  }
  ```

+ 在home.wpy中将home.js导入进来，并进行混入

  ```javascript
  import mixinHome from '../../mixins/tabs/home.js'
  export default class Home extends wepy.page {
    mixins = [mixinHome]
  }
  ```

#####  封装baseToast、get、post

- 优化-封装baseToast函数提示错误消息
  + 首先在src内部创建utils文件夹，在utils文件夹里面创建api.js


  + 在api.js里封装baseToast方法

    ```javascript
    /**
     * @str为提示信息
     */
    wepy.baseToast = function(str='获取数据失败'){
      wepy.showToast({
        title: str,
        icon: 'none', // 提示没有图标
      })
    }
    ```

  + 在home.wpy中导入utils/api.js

    ```javascript
    import '@/utils/api.js'
    ```

  + 修改之前使用的showToast方法

    ```javascript
    wepy.baseToast('获取数据失败')
    ```

- 优化-封装wepy.get函数发起get请求

  + 封装get方法

    ```javascript
    /**
     * 封装get请求
     * @baseUrl 提取的公告域名
     * @path 是接口地址
     * @data 请求传递的数据
     */

     wepy.get = function (path,data={}) {
       return wepy.request({
         url: baseUrl+path,
         method: 'get',
         data:data
       })
     }
    ```

  + 将之前的get请求修改成封装之后的get

    ```
    var {data} = await wepy.get('/home/swiperdata')

    var {data} = await wepy.get('/home/catitems')
    ```

- 优化-封装wepy.get函数发起post请求

#### 05 - 完成首页楼层效果

##### 获取楼层数据并渲染标题

- 首页-获取楼层相关的数据

  + 定义获取楼层数据的方法

    ```javascript
    // 获取楼层数据
    async getFloorData () {
      var {data:res} = await wepy.get('/home/floordata')
      if(res.meta.status !== 200) {
        return wepy.baseToast('获取楼层数据失败')
      }
      this.floorData = res.message
      this.$apply()
    }
    ```

  + 在onLoad中调用getFloorData方法

    ```javascript
    this.getFloorData()
    ```

- 首页-渲染楼层标题

  + 定义基本的结构

    ```javascript
    <!-- 楼层 -->
      <view class="floor_box">
        <view class="floor_item">
          <!-- 标题图片 -->
          <image></image>
          <!-- 内容图片 -->
          <view class="content_img"></view>
        </view>
      </view>
    ```

  + 循环标题数据生成结构

    ```javascript
    <!-- 楼层 -->
      <view class="floor_box">
        <view class="floor_item" wx:for="{{floorData}}">
          <!-- 标题图片 -->
          <image src="{{item.floor_title.image_src}}"></image>
          <!-- 内容图片 -->
          <view class="content_img"></view>
        </view>
      </view>
    ```

  + 美化标题

    ```
    .floor_box{
      .floor_item{
        image{
          height: 50rpx;
          width: 640rpx;
        }
      }
    }
    ```

##### 渲染图片列表

- 首页-渲染楼层图片列表

  + 根据数据动态生成图片列表

    ```javascript
    <!-- 内容图片 -->
    <view class="content_img">
      <image wx:for="{{item.product_list}}" src="{{item.image_src}}"></image>
    </view>
    ```

  + 美化图片列表样式

    ```javascript
    .floor_box{
      .floor_item{
        .tit{
          height: 50rpx;
          width: 640rpx;
        }
        .content_img{
          image{
            height: 190rpx;
            float: left;
            margin: 8rpx;
          }
          image:nth-child(1){
              height: 390rpx;
          }
        }
      }
    }
    ```

- 首页-点击楼层图片跳转到商品列表页面

  + 给图片注册点击事件

    ```
    <!-- 内容图片 -->
          <view class="content_img">
            <image @tap="goGoodsDetail({{item.navigator_url}})" style="width:{{item.image_width}}rpx" wx:for="{{item.product_list}}" src="{{item.image_src}}"></image>
          </view>
    ```

  + 定义事件函数

    ```
    goGoodsDetail (url) {
      wepy.navigateTo({
        url
      })
    }
    ```

  + 创建商品列表页

  + 将商品列表路劲存到app.wpy里面的pages里

  + 在商品列表里通过onLoad的形参获取传递过来的参数