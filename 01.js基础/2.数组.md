# js基础-day-03

# 数组

* 一个人的成绩可以用一个变量存储，500个人的成绩，我们就不用500个变量存储了，我们可以使用数组存储。

## 介绍

* 数组是一个有**顺序**、**有长度**的数据集合；
* 数组：类型Object；
* 特点：
  * 把数据放在一起；
  * 有先后位置上的顺序；
  * 有数据的长度；

## 声明

```javascript
// 5个人的成绩为： 91，88，72，45，63
// 先声明一个数组，字面量的形式
var arr = [];

// 输出 []  这是一个没有数据在里面的数组，称为空数组
console.log(arr,typeof arr); 
```

## 存值

* 数组中的数据使用**索引**管理。
* 索引：**序号、顺序、排位、位置、下标**
* **索引从0开始**

```javascript
//把成绩存储到数组中
arr[0] = 91;
arr[1] = 88;
arr[2] = 72;
arr[3] = 45;
arr[4] = 63;
console.log(arr); // 输出 [91,88,72,45,63] 就是一个数据集合
```

* 把 `数组[索引]`格式当成一个变量使用就行，

```js
// 初始化赋值完成后，也可以再次改变值，把前面的值覆盖掉；
arr[0] = 100;
```

* 如果一开始就知道数组了，可以直接使用一个简单的语法存储数据

```javascript
var arr = [91,88,72,45,63];
console.log(arr); // 输出的结果是一样的
```

* 上面每个位置上存的都是数字类型，可以为其他类型；





## 取值

* 把数据取出来，得知道你要取哪个位置上的数据把。
* 数据取值同样使用**索引**取。

```javascript
// 拿到索引为0，顺序上第一个位置上的数据；
// 把 数组[索引] 格式当成一个变量使用就行；
console.log(arr[0]);

// 数组求和：班里的成绩总和
var sum = arr[0] + arr[1] + arr[2] + arr[3] + arr[4];
console.log(sum); // 输出370
```



## 遍历

* 求成绩总和：一个一个地把数组里面的数组取出来了，从索引 0 到最后一个索引，
* 索引从0开始到结束的过程，**有重复的思想，需要用到循环；**

```javascript
// 最初的写法
var sum = arr[0] + arr[1] + arr[2] + arr[3] + arr[4];


// 循环 这个从0~最后一个索引，有重复的思想在里面，使用循环。
var sum = 0;
for(var i = 0; i <= 4; i++){
  sum += arr[i];
}
console.log(sum); // 输出 370，和我们一个一个相加是一样的
```

* 使用**循环来遍历**数组，当数组中的数据比较多的时候，会比较方便。**一般是使用for循环；**



## 数组长度

### 语法

* 存取数据：涉及到就是数组的**顺序**问题，通过索引去存取；

* 数组长度：数组中一共存放了多少个数据；

```javascript
console.log(arr.length); // 数组.length 就是数组的长度
```

* 如果数组的长度是5，最后一个元素的**索引**就是4；
* **我们发现最大索引总是比长度少 1 ，所以遍历还可以这么写**

```javascript
for(var i = 0; i <= arr.length - 1; i++){
  console.log(arr[i]);
}

// 简化一下 
for(var i = 0; i < arr.length; i++){
  console.log(arr[i]);
}
```

### 案例：求数组中所有数字的总和的平均值

* 分析：
  * 总和：循环遍历，加在一个变量上得到；
  * 均值：总和 / 个数；（arr.length）

```js
// 还可以更简单
var sum = 0;
for(var i =0; i < arr.length; i++){
  sum += arr[i];
}
console.log(sum);

// 求平均分
// 平均分= 总分 / 数组的长度
var avg = sum / arr.length;
```

### 案例：求数组中的最大值

* 分析：生活中，一堆人最高的（人很多，多到你一下看不出来）；
  * 最大值：最起码得两个数比较下，得到最大值；
  * 假设：其中随便一个是最大值MAX，每个元素和max比较，
    * 若有比MAX大的，那该元素代替MAX；
    * 若都没有MAX大，恭喜，你一开始就猜对了；

```js
var max = arr[0];
for(var i =0; i < arr.length; i++){
  if(max < arr[i]){
    max = arr[i];
  }
}
```

* 需求：求数组中的最大值的索引

```js
// 分析：找打最大值的时候，记录下最大值的下标就行了。
var max_index = 0;
var max = arr[max_index];
for(var i =0; i < arr.length; i++){
  if(max < arr[i]){
    max = arr[i];
    max_index = i;
  }
}
```

### 清空数组

```js
arr.length = 0;
```







## 数组的构造函数

* 数组在JS中还可以使用另一种方式创建，这个方式我们称为 ： 构造函数
* 构造函数：能构造一个你需要的东西（对象）；

```javascript
// 使用 构造函数 创建数组
var arr = new Array();
// 存储数据
arr[0] = 10;
arr[1] = 20;
console.log(arr);

var arr = new Array(10,20);
console.log(arr);
```

* 注意：一个数据，不要使用这个方式存储数据；它会认为你想要设置数组的长度，而不是要把数据存储在数组中。

```javascript
var arr = new Array(10);
console.log(arr); // 输出 [empty × 10]
```





# 小娜V1.0

## 需求

* 输入q：完全退出；

* 输入1：得到求和功能，我们输入"数字,数字,数字"的格式，帮我们计算出我们的输入的和；
* 输入2：获得当前时间；
* 输入3：随机给我讲一个笑话；
* 不q：一直循环 提示 可以 输入1/2/3、q这些功能点；

## 功能

### 循环与退出

* 使用wihle，设置条件为true，一直循环；

```js
while(true){
    
}
```

* 在内部使用变量接受用户输入的信息，使用固定值switch case 判断用户输入的是哪种情况

```js
while(true){
  var result = prompt('你好，我是小娜，请输入下面的数字，决定你要干什么。\n1-计算综合，\n2-获取时间,\n3-讲个笑话');  
  switch (result) {
    case 'q':
      
      break;
  }
}
```

* 退出功能：使用break退出while，

```js
while(true){
  var result = prompt('你好，我是小娜，请输入下面的数字，决定你要干什么。\n1-计算综合，\n2-获取时间,\n3-讲个笑话');  
  switch (result) {
    case 'q':
      // 如果在这里写break，会被认为是switch case 的break,所以该处不能这样使用，应该在外面使用if进行判断；
      break;
      break;
  }
  
  if(result=='q'){
  	  alert('你不爱小娜了吗');
      break;
  }
}
```

### 求和

* 需求：输入1，进入求和功能

```js
// 注意： 输入的都是字符串，所以这里要使用字符串比较
case '1':
	var nums = prompt('请输入要求和的数字，以逗号隔开，例如： 1,2,3,4');

break;
```

* 需求：我们输入"数字,数字,数字"的格式，需要转为数组，求和；

```js
var str = '12,88,72,6';
// 以逗号为分隔符，分割字符串，得到一个数组
// 字符串.split(指定分隔符)
// 后面的指定分隔符就是我们写字符串数字与字符串数字之间的间隔符号
var arr = str.split(',');

// 输出 ['12','88','72','6']
// 转为数组，但不是说里面的元素转为数字类型了；
console.log(arr); 
```

* 求和：循环、每个元素转类型；

```js
sum += parseFloat(arr[i]);
```

### 获取时间

* 需求：输入2，获取当前时间；
* 在js中，要获取系统的当前日期和时间，需要用到一个js自带的**Date对象** ，
* **现在先不用管什么是对象，先学习如何使用，对象能给我们带来什么**

```js
// 创建Date对象
var date = new Date();
console.log(data); // 系统时间不同，输出的结果也会不同，但是都是输出当前系统的时间

// 获取时间对象的各个部分，对象.方法();

// 获取年份
var year = date.getFullYear();
console.log(year);

// 获取月份 ， 得到的月份是从0开始的 ，使用 0-11 表示 1-12 月
var month = date.getMonth();
console.log(month);

// 获取天
var day = date.getDate();
console.log(day);

// 获取小时
var hour = date.getHours();
console.log(hour);

// 获取分钟
var minute = date.getMinutes();
console.log(minute);

// 获取秒数
var second = date.getSeconds();
console.log(second);
```

* 为了格式上的好看：单位数，补位成双位数；

```js
if(day < 10){
    day = '0' + day;
}
```



### 随机笑话

* 需求：输入3，随机给我讲个笑话；

* 随机值：每次给你的值，一般情况下是不一样的。
* 有给随机值的一个对象Math；

```js
// 获取随机数
var r = Math.random();

// 输出一个在 [0,1) 之间的浮点数，可以得到0，但是无法得到1
console.log(r); 
```

* 如果想要得到一个随机整数，需要把整机浮点数  乘以 一个 倍数，再取整，

```js
// 获取 [0,10) 之间的随机浮点数，随机的一个值，
var r = Math.random() * 10;
```

* 取整：向下取整，向数轴的左边获取最近的一个整数

```js
// 向下取整
var a = Math.floor(1.12);
console.log(a); // 输出1
var b = Math.floor(1.99);
console.log(b); // 输出1
var c = Math.floor(-3.2);
console.log(c); // 输出 -4
var d = Math.floor(3.9);
console.log(d); // 输出 -4
```

* 获取一个随机整数

```js
// 获取一个 [0,10] 之间的随机整数
var r = Math.random();
r = r * (10 + 1) ;// 因为 Math.random得到的是不能得到1的浮点数，我们等下要向下取整，就得不到10了， * 11 向下取整才能得到10
r = Math.floor(r);
console.log(r); // 得到一个在 [0,10] 之间的整数
```

* 分析：随机来个笑话：
  * 笑话是个数字，有很多效果，我不知道要哪个；
  * 随机给我来一个，随机的下标就可以；
  * 随机的下标：随机的整数；看整数的范围：数组的长度；

```js
var index = Math.random();  // [0,1)
index *= list.length ; // [0,5)

// 最后一条永远拿不到，让最后一个笑话为空；
index = Math.floor(index); // [0,4]
```



# git

## 介绍

* version

- git：**做版本管理的；**
- 版本管理：写代码，不是一次性把所有功能模块写完善的，或者写完的，代码和功能都是一点一点迭代出来，就是形成不同的版本；
- 特点：
  - **代码安全**：git这个工具，可以将我们写好的代码，备份到网络上面（比如：github），将来如果想要恢复，可以直接恢复；
  - **团队开发**：在现在公司里面，绝大多数情况都是团队开发，如果没有一个可以协同的软件，人工进行代码的合并、管理是非常吃力的；
  - **责任明确**：版本管理工具，是可以管理到出问题的代码是谁合并到一起的。
  - **代码回滚**：之前有一个版本，是没有问题的，现在为了加新的功能，影响了原来的版本，就可以先把当前的版本进行撤销，恢复到上一个版本先进行使用。
- **什么时候用？大家只要做个不小的项目，都要初始化一个git。**

## 安装

- 官网下载对应电脑的版本：[git下载页面](https://www.git-scm.com/download/)

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/001.png)

- 下载完成就会得到一个安装文件,直接双击安装，无脑下一步安装即可。

## 配置

- 配置个人信息：**用于追责用**；在任务位置右键，选择 `Git Bash Here`

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/002.png)

- 出现一个黑色的窗口，在窗口内输入：

```cmd
git config --global user.email "你自己的邮箱"
git config --global user.name "你自己的名字"
```

- 查看配置：

```
git config --list
```

## 使用

- 其实，使用git这个黑色窗口使用命令行管理自己的代码是可以的，我们以后会学习，目前我们还是学习界面化操作；
- 在vscode中，我们可以找到这个图标![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/004.png)
- 点击这个图标，就可以进行源代码管理，需要选择个我们要管理代码的目录初始化：

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/005.png)

- 初始化之后，会在当前要管理的文件夹下面有.git文件夹；之后当你的代码发生变量，都会在里面有提示；这个文件就是用于管理我们多个版本的文件夹；

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/006.png)

- 此时如果我们想保存一下当前这个版本的代码

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/007.png)

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/008.png)

- 必须输入备注信息，标识该次提交的简单的备注；

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/009.png)

## 查看

- 上面只是对每次的修改做一次提交，每次提交就算是一个版本；
- VSC下载**gitLens插件**查看每次提交的版本记录

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/010.png)

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/011.png)

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/012.png)

- 多次提交版本，只能看到与上次版本的对比，如果当前最新版本有问题，我们删除了当前版本的部分代码即可；

![](E:/000-all_learning/005-itcast_videos/004-js/003-BJ-83/01-js_base/day03/01-%E8%B5%84%E6%96%99/assets/013.png)





