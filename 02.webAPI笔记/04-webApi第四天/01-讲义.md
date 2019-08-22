# webApi day04

# DOM

## 事件：onkeydown、onkeyup

- 键盘事件：
  - 事件类型
    - 按键按下：keydown
    - 按键弹起：keyup
  - 按下哪个键？
    - 事件对象.keyCode，这个属性被称为  键盘码 ，每个按键对应的数字是不一样 ，只需要判断数字，就知道按下的按键是哪一个； keyCode==13 回车键
    - 按下了ctrl：事件对象里面有一个属性 ctrlKey ；如果是true，按下了ctrl键
- 实现：
  - 获取元素：文本域；
  - 注册事件：keydown；（组合键）
  - 按下键盘：判断e.ctrlKey && e.keyCode === 13全部成立

```js
text.onkeydown = function(e) {
    console.log(e.keyCode, e.ctrlKey);
    // 判断是否同时按下ctrl和回车
    if (e.ctrlKey && e.keyCode === 13) {
    }
}
```

## 案例：微博案例v4.0-组合键发布

* 实现：实现点下ctrl和enter 发布；

```js
text.onkeydown = function(e) {
    console.log(e.keyCode, e.ctrlKey);
    // 判断是否同时按下ctrl和回车
    if (e.ctrlKey && e.keyCode === 13) {
        // 在代码中执行；
        btn.onclick();
    }
}
```

* btn.onclick()： 为什么可以这样写？.onclick只是元素对象上的属性，赋值为方法，调用执行；

## 案例：微博案例v4.0-删除

* 思路：
  * DOM节点操作：**在点击删除的时候，把对应li从ul里面移除**；
  * 本地化数据更新：**删除对应的那条数据及整个列表数组，那我怎么知道我要删除哪个？**
    * 唯一的ID：
      * ID：identification身份证，保证该数据绝对的唯一，我们就能找到它；
      * 构成：采用 时间戳+随机数，基本可以保证不会重复；
    * 生成ID，在发布阶段生成：
      * 新增DOM节点，修改DOM节点内容同时，把新增的ID通过赋值为自定义属性的方式加进去；
      * 本地数据，把ID存进去；
    * 删除：有了发布时新增的ID，点击删除时，获取点击对象的自定义属性ID，
      * DOM去除：删除DOM，
      * 数据去除：根据唯一的ID去找当前的数组内的数据，剔除掉；完成更新本地数据；

```js
// ------------------------------------------------------发布
// 生成一个唯一的id
var id = Date.now() + '' + Math.floor(Math.random() * 10000);

// 更变发布时的ID插入，自定义属性
li.innerHTML = '<p class="content">' + content + '</p>' +
  '<span class="del" data-id="'+ id +'">删除</span>' +
  '<em class="time">' + time + '</em>';
ul.insertBefore(li, ul.children[0]);

// 保证你的存的数据顺序和展示的顺序一样；
wbData.unshift({
  id : id,
  content: content,
  time: time
});


// ----------------------------------------------------删除
// 获取ID
var delId = e.target.dataset.id;

// 删除DOM
var li = e.target.parentNode;
ul.removeChild(li);


// 删除本地数据
wbData.forEach(function(e,i){
  if(e.id === delId){
    wbData.splice(i,1);
  }
});


// 更新本地数据
var result = JSON.stringify(wbData);
localStorage.setItem('wbshuju',result);
```



## 事件：onmouseover、onmouseout

* 鼠标移入某个元素的时候，触发事件；

```js
<div class="box" index=0></div>

// 鼠标移入时触发
var box = document.querySelector('.box');

// 对象属性，
box.a = 0;

box.onmouseover = function() {
    // 获取自定义属性
    this.getAttribute("index");
    
    // 添加节点对象属性，随便添加；
    console.log(this.a);
}

// 鼠标移除时触发
dom.onmouseout = function(){ 
}
```



## 案例：tab栏

* 案例：鼠标移入一个选项（选项变为当前选择状态），下面图就会跟着发生变化；
* 实现：
  * 获取元素：选项、图片；
  * 注册事件：选项，鼠标移入；
  * 移入后的变化：
    * 按钮的状态变化：**排他思想**
      * 先把所有的清除为初始化状态；
      * 给当前DOM节点加当前选择状态；
    * 图片的变化：**排他思想**
      * 先把所有图片清除样式；
      * 给当前选项一样的下标的图片，图片的当前样式；

```js
  // 给所有的tab注册鼠标移入事件
  for (var i = 0; i < tabs.length; i++) {
	// 先通过自定义属性存起来；
    tabs[i].setAttribute("index", i);
      
    tabs[i].onmouseover = function() {
      // 排他思想： 就是遇上一部分不一样，其他都一样，就使用排他思想
      // 排他的第一步： 先把所有的都变成普通
      for (var j = 0; j < tabs.length; j++) {
        tabs[j].classList.remove('active');
      }
      // 然后把特殊的变特殊
      // 给自己添加一个class
      this.classList.add('active');

      // 3.3 根据索引获取对应的商品
      // 排他的设置商品的显示和隐藏
      for (var k = 0; k < goods.length; k++) {
        goods[k].classList.remove('selected');
      }
	  
      var index = this.getAttribute("index");
      // 把对应的商品显示
      goods[index].classList.add('selected');
    }
  }
```

* **上面是通过获取自定义属性的方式拿到对应的下标，也可以把index的值，作为每个DOM节点的对象属性存起来；**
* **这个在工作上非常常用，可以把一一对应的数据初始化在你的DOM节点上，也可以后期在JS内把相关属性设置在对象上；**

```js
  // 给所有的tab注册鼠标移入事件
  for (var i = 0; i < tabs.length; i++) {
	tabs[i].index = i;
    tabs[i].onmouseover = function() {
      // 排他思想： 就是遇上一部分不一样，其他都一样，就使用排他思想
      // 排他的第一步： 先把所有的都变成普通
      for (var j = 0; j < tabs.length; j++) {
        tabs[j].classList.remove('active');
      }
      // 然后把特殊的变特殊
      // 给自己添加一个class
      this.classList.add('active');

      // 3.3 根据索引获取对应的商品
      // console.log(this.index);
      // 排他的设置商品的显示和隐藏
      for (var k = 0; k < goods.length; k++) {
        goods[k].classList.remove('selected');
      }
	
      // 获取当前DOM节点 对应的下标；  
      var index = this.index;
      // 把对应的商品显示
      goods[index].classList.add('selected');
    }
  }
```



## BOM方法：获取DOM节点样式

```js
// Computed：计算后的样式
// 返回值： 当前作用在这个元素身上的所有样式的集合对象  BOM的方法；
// 属性：具体的属性 无论是行内的还是CSS样式设置的，都可以获取到；字符串
var res = window.getComputedStyle(元素对象)；
res.width 

// 只能操作行内属性；
var dom = document.getElementById('xx');
dom.style.color；
```



## 属性：获取元素的实际宽度和高度

* 元素的实际宽高 = border+padding+content（width和height）

```js
// 返回值：数值；

// 只能进行获取；
// 元素的实际宽度
元素.offsetWidth 
// 元素的实际高度
元素.offsetHeight

// 获取和设置
dom.style.width；
```

## 案例：轮播图需求

* 布局：ul下有很多li标签；浮动在一行；
* 原理：切换图片的时候，把ul位置修改一下，给ul的父容器，设置一个 overflow:hidden；

* 功能需求：
  * 序号轮播
  * 左右按钮的轮播
  * 自动轮播
  * 鼠标在轮播图里面的时候，停止自动轮播，离开后继续自动轮播

## 需求：序号轮播

* **其实就是tab栏；**

* 获取元素：所有的小圆点序号
* 注册事件：鼠标移入mouseover小圆点的事件
* 移入之后：
  * 小圆点：排他思想实现当前样式出现；
  * 图片切换：计算ul应该向左移动多少，`ul的位置 = 图片宽度 * 索引 * -1`;

```js
    // 1.1 获取元素 (小圆点，ul)
    var circles = document.querySelectorAll('.list>i');

    var ul = document.getElementById('imglist');

    // 定义一个变量，保存图片的宽度 // li和图片的宽度是一样的
    var imgWidth = ul.children[0].offsetWidth;

    // 1.2 给序号注册鼠标移入事件
    for (var i = 0; i < circles.length; i++) {
	  // 先把数据存起来；
      circles[i].index = i;
      circles[i].onmouseover = function() {
        var target = imgWidth * this.index * -1;


        ul.style.left = target + 'px';
        ul.style.transition = "left 300ms linear";

		// 当前样式设置；排他思想；
        circles.forEach(function(c) {
          c.classList.remove('current');
        });
        this.classList.add('current');
      }

    }
```



## 需求：左右轮播

* 获取元素：左右按钮
* 注册事件：click
* 点击之后：整体向左滑动一格；
  * 变量控制下标增1；
  * 我要一直向右滑动动么？
    * 不能滑动到无限，
    * 点击下标为5，显示为第6张；
    * 点击下标为6，显示为第1张；回归下标为0；
* 实现：**点击右侧**
  * 设置变量，为初始化图片的下标为0；
  * 点击右侧，变量加+；
    * 下标0：显示第一张；
    * 下标为5：显示第六张；
    * 下标为6，当前显示第1张，回到下标为0;

```js
    //注册点击事件
    // 下标为0；当前显示第1张
    // 下标为1；当前显示第2张
    // 下标为5，当前显示第6张
    // 下标为6，当前显示第1张，回到下标为0;
    rightBtn.onclick = function() {
      currentIndex++;

      // 下标等于长度的时候，应该恢复到第1张，下标为0；
      if (currentIndex == ul.children.length) {
        currentIndex = 0;
      }

      // 算出ul的位置
      var target = currentIndex * imgWidth * -1;
      // 设置ul的位置
      ul.style.left = target + 'px';

    };
```

* 实现：**点击左侧**
  * 设置变量，为初始化图片的下标为0；（0：已经显示为第一张图片）
  * 点击左侧，变量加-；
  * 当 变量==0 时，让它归到ul.children.length - 1；
    - 5下标：显示第六张；
    - 0下标：显示第一张；
    - 所以：点击的一瞬间：变量==-1，应该显示第六站图片，归于ul.children.length - 1；

```js
leftBtn.onclick = function() {
    // 正常递减
    currentIndex--;

    // 当下标为-1；当前显示应该为第6张，下标回归为 最后的下标= 数组长度-1；
    if (currentIndex == -1) {
        currentIndex = ul.children.length - 1;
    }

    // 算出ul的位置
    var target = currentIndex * imgWidth * -1;
    // 设置ul的位置
    ul.style.left = target + 'px';
}
```



## 需求：左右联动序号

* 点击左右按钮切换图片后，小圆点也应该跟着切换当前样式；
* 让当前下标的小圆点的样式变化：排他思想；

```js
    rightBtn.onclick = function() {
	  ...

      // 联动序号
      // 归根到底，左右切换控制的就是index，序号也是控制的index。
      // 左右切换时，应该把左右切换控制的index,影响到序号的样式上；

      // 样式联动：排他思想
      circles.forEach(function(c) {
        c.classList.remove('current');
      });
      circles[currentIndex].classList.add('current');

    };

```



## 需求：序号联动左右

* 完成上面左右切换联动序号样式；
* 当鼠标通过序号切换后，在用左右切换，会出现问题，原因：
  * 左右按钮控制一个**全局变量**，也把全局变量的值联动给**小圆点的样式**了；（左右切换联动序号）
  * **小圆点自己切换的时候，没有影响到全局变量，所以会出现乱转的情况；**
* 如何设置：把小圆点切换时控制的当前的下标，赋值给全局的变量（左右按钮控制的那个变量）即可；

```js
    // 1.2 给序号注册鼠标移入事件
    for (var i = 0; i < circles.length; i++) {
      // const element = array[index];
      circles[i].index = i;
      circles[i].onmouseover = function() {
        // 计算
        var target = imgWidth * this.index * -1;
        // 移动
        ul.style.left = target + 'px';

        // 样式设置
        circles.forEach(function(c) {
          c.classList.remove('current');
        });
        this.classList.add('current');

        // 联动
        // 要把序号切换的值，影响到全局的变量形成统一；
        currentIndex = this.index;
      }

    }
```



## 需求：自动轮播及鼠标控制轮播

* 初始化页面，页面自动向右轮播；
* 实现：
  * 需要把向右的函数提炼为一个函数
  * 定时器：执行向右点击的函数

```js
// 自动轮播
var timer = setInterval(function() {
    moveRight();
}, 2000);
```

* 鼠标不在整个盒子上操作时，图片自动向右轮播；
* 现实：
  * 获取元素：整个盒子；
  * 注册事件：鼠标移出；
  * 移出之后：定时器执行向右的函数；（需要把向右的函数提炼为一个函数）

```js
// 鼠标移出，恢复自动轮播
box.onmouseout = function() {
    timer = setInterval(function() {
        moveRight();
    }, 2000);
}
```

* 鼠标在盒子上，停止自动轮播
* 实现：
  * 获取元素：整个盒子；
  * 注册事件：鼠标移入；
  * 移入之后：清除定时器；

```js
var box = document.getElementById('box');
box.onmouseover = function() {
    clearInterval(timer);
};
```















