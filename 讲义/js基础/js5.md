# js基础-day_05

# 对象

## 介绍

* 核心概念：**万物皆对象**，我们在编程中，使用对象来描述万事万物。
* 对象：使用`属性`描述事物的`特征`，使用`方法`来描述`行为`， 就是对象这种语法。
* 对象**就是属性和方法的集合**；
* 对象描述扳手：
  - 属性(特征)：金属、银白色
  - 方法：能拧螺丝、防身等；

## 为什么

* 之前学习过的对象：Math、new Date()；我们发现，只要学习对象的一些属性和方法，直接使用，就可以得到自己想要的效果。例如-得到随机数： Math.random() ；**我们不需要关心随机数到底是怎么产生的，只要结果**
* 这就要涉及到一个在编程界内的著名思想：**面向对象思想、万物皆对象**

* 面向对象思想：找一个**对象（工具）**，看它身上有什么方法和特点，能帮我干什么事，然后我就可以用这个工具，按照我们的思维安排这个工具做事件就可以。

```js
// 新找到一个日期对象
// Date 构造函数，构造出一个我们需要的工具，就是对象；
var date = new Date();

// 调用日期对象的获取年份的方法
date.getFullYear();

// Math对象
Math是 内置对象；
Math.floor();
```

* 特点：对于开发人员更友好的开发；
  * **实现高效开发**：我们只要知道对象（工具）有什么属性和方法，不需要知道对象里面是如何实现的。**在别人已经提供好的方法的基础上，再次实现我们想要的效果，开发过程将大大缩短。**
  * **便于维护：**因为你是单个对象（工具），我可以随时对你进行改造，修改；满足我的需要；

## 语法

### 创建

* 构造函数：

```javascript
var obj = new Object(); // 这是一个没有属性和方法的对象
console.log(obj);
```

* 字面量：

```javascript
var obj = {}; // 这也是一个没有属性和方法对象，其本质和构造函数创建的对象是一样的
console.log(obj,typeof obj);
```



### 添加

* 如： 使用对象描述一个叫`狗蛋`的人，先字面量声明一个对象，再给对象上属性和方法赋值；

```javascript
var obj = {};


// 对象.属性 = 值;
obj.name = '狗蛋';// 名字是一个人的特征

// 对象.方法名 = function(){}
// 注意：该方法后面的的赋值为一个函数，函数在什么时候执行？调用的时候才会执行；
obj.sayName = function(){  // 人会说出自己的名字，也就是人有自己的行为
	console.log('你好，我叫' + obj.name);
}
```

* 通过字面量初始化对象时，初始化属性

```js
// 描述一个学生
var student = {
  name : '狗蛋',
  age : 12,
  gender : '男',
  sayName : function(){
    console.log(student.name);
  }
}
// 
```

* 一个属性和一个值叫**键值对**。多个键值之间使用逗号分隔，键的方式添加属性：


```js
// 对象[属性名]  属性名必须是String类型，里面可以写字符串；
var obj = {};
obj['name'] = '狗蛋';
obj['sayName'] = function(){
  console.log('你好，我叫' + obj['name']);
}
```



### 获取

* 点属性访问

```javascript
// 得到对象的名字,属性可以当成变量使用
console.log(obj.name);
// 调用对象的方法，方法的本质是函数
obj.sayName();
```

* 以键的方式访问属性

```js
console.log(obj['name']);
obj['sayName']();
```



### 遍历

* 数组可以遍历；对象也可以遍历；

```javascript
// key 就是obj这个对象中的每个键，obj就是要遍历的对象。
for(var key in obj){
}

var obj = {
  name : '狗蛋',
  age : 12,
  gender : '男'
};
for(var key in obj){
  console.log(key);
  // 只能通过键的方式获取值
  console.log(obj[key]);
}
```



# 小娜v3.0

## 封装对象

* 把小娜看成一个对象，
* 对象是：属性和方法集合体。
* 作用：我们以后需要到小娜功能的地方直接调用，为了更高效的开发；

```js
// 面向对象的做法实现小娜
  // 1 把小娜看成是一个对象，使用对象的语法把小娜描述出来
  var xiaona = {};
  // 2 给小娜添加退出的功能
  // 2.1 给小娜添加了一个可以退出的功能
  xiaona.exit = function () {
    // 退出的时候，仅仅是弹出了一个提示框，询问是否已经不爱小娜了
    alert('你已经不爱小娜了吗？');
  }
  // 2.2 给小娜添加一个求和方法
  xiaona.getSum = function () {
    var nums = prompt('请输入要求和的数字，以逗号隔开，例如： 1,2,3,4');
    var arr = nums.split(',');
    var sum = 0;
    for (var i = 0; i < arr.length; i++) {
      sum += parseFloat(arr[i]);
    }
    alert('求出的和为' + sum);
  }
  // 2.3 给小娜添加一个可以给数字补全0的功能
  xiaona.patchZero = function (number) {
    if (number < 10) {
      number = '0' + number
    }
    return number;
  }
  //2.4 给小娜添加一个获取当前时间的功能
  xiaona.getTime = function () {
    var date = new Date();
    var year = date.getFullYear();

    var month = date.getMonth() + 1;
    // 调用小娜的补全0的功能
    month = xiaona.patchZero(month);

    var day = date.getDate();
    day = xiaona.patchZero(day);

    var hour = date.getHours();
    hour = xiaona.patchZero(hour);

    var minute = date.getMinutes();
    minute = xiaona.patchZero(minute);

    var second = date.getSeconds();
    second = xiaona.patchZero(second);

    var time = year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':' + second;
    alert(time);
  }
  // 2.5 给小娜添加一个讲笑话的功能
  xiaona.makeJoke = function () {
    var list = [
    "为什么结婚都喜欢选好日子，因为结婚后都没有好日子。",
    "为什么超人都喜欢穿紧身衣，因为救人要紧"
  	];
    var index = Math.random(); // [0,1)
    index *= list.length; // [0,5)
    index = Math.floor(index); // [0,4]
    alert(list[index]);
  }

  while (true) {
    var result = prompt('你好，我是小娜，请输入下面的数字，决定你要干什么。\n1-计算综合，\n2-获取时间,\n3-讲个笑话');
    switch (result) {
      case '1':
        // 3 调用小娜的求和的方法
        xiaona.getSum();
        break;
      case '2':
        // 4 调用小娜的获取时间的方法
        xiaona.getTime();
        break;
        // 讲笑话
      case '3':
        xiaona.makeJoke();
        break;
    }
    if (result === 'q') {
      xiaona.exit();
      break;
    }
  }
```

## 拓展性

* 为什么要有拓展性？因为我们写代码不可能一次性把所有功能写全，需要我们不断的完善我们的功能；

```js
// 给Math对象添加一个可以求指定范围随机数的功能
// Math内置对象；
// Math.floor(Math.random() * (最大值-最小值 + 1)) + 最小值

// 形成函数
function getRandom(n,m){
  return Math.floor(Math.random() * (m-n + 1)) + n;
}

// 使用面向对象的方式实现求随机整数 - 把这个功能交给Math对象管理
Math.getRandom = function(n,m){
  return Math.floor(Math.random() * (m-n + 1)) + n;
}
```

* 拓展在小娜对象上，新增一个增加笑话的功能；

```js
// 基础知识：数组的拼接
var arr = [1, 2];
arr = arr.concat('a');
arr = arr.concat(5, 6);
arr = arr.concat([4, 8], [9, 0]);

// 快速的复制一个数组,绝对不是 
// var arr_1 = arr;
var arr_1 = arr.concat();




// 5 把笑话的数组作为小娜的一个属性，这样的话这个属性就成为我们全局对象小娜上的一个属性，任何地方都可以访问的到；
  xiaona.jokeList = [
    "为什么结婚都喜欢选好日子，因为结婚后都没有好日子。",
    "为什么超人都喜欢穿紧身衣，因为救人要紧"
  ];


  // 给小娜添加一个讲笑话的功能
  xiaona.makeJoke = function () {
    var index = Math.random(); // [0,1)
    index *= xiaona.jokeList.length; // [0,5)
    index = Math.floor(index); // [0,4]
    alert(xiaona.jokeList[index]);
  }


  // 6 提供一个方法，可以为小娜的笑话数组添加新的新的笑话，这个方法在调用的时候可以访问到对象上的额属性；
  xiaona.addJoke = function(newJoke){
    // 如何往一个数组中加新的数据
    // 数组.concat(新的数据);
    xiaona.jokeList = xiaona.jokeList.concat(newJoke);
  }


  // 7 调用小娜的新增笑话的方法添加新的笑话
  xiaona.addJoke('火柴有个问题想不懂，然后就挠头，自己燃烧了自己');
  xiaona.addJoke('包子跑步，为什么在路上消失了，因为太饿自己把自己吃了');
  xiaona.addJoke(['joke1','joke2',"自己的事情自己做，函数也能调用自己"]);
```

## 方法小结

* 字符串分为数组：

```js
var arr = nums.split(',');
```

* 数组的拼接：把多个数组合并成一个新的数组，而不会改变原来的数组；

```js
var arr = [1, 2];
arr = arr.concat('a');
arr = arr.concat(5, 6);
arr = arr.concat([4, 8], [9, 0]);

// 快速的复制一个数组,绝对不是 
// var arr_1 = arr;
var arr_1 = arr.concat();
```







# 简单类型和复杂类型

## 简单类型的储存

* 简单类型

```javascript
var a = 10;
var b = a;
b = 20;
console.log(a,b); //输出 10，20 也就是说，a的值不会受到b的值的改变而影响
```

* **简单类型数据存储在内存的栈空间中，复杂类型的数据存储在内存的堆空间中**

![](./assets/001.png)

* 把一个变量赋值给另一个变量的时候，其实是把**栈空间**的数据(格子内的值复制了)复制了一份

![](./assets/002.png)

* 当另一个数据发生变化，会根据变量找到对应的**栈内存**上盒子的内容，进行修改；
* **此时，简单类型的的变量赋值给另一个变量，当另一个变量改变了，不会影响原来的变量；**

![](./assets/003.png)

## 复杂类型的储存

```js
var obj1 = {
  name : '狗蛋'
};
var obj2 = obj1;
obj2.name = '翠花';

// 输出 两个翠花 ， 也就是说，obj1的name属性受到了obj2的name属性影响
console.log(obj1.name,obj2.name); 
```

* **复杂类型在内存的储存，赋值给其他变量，也是把格子内的内容复制了一份，**
* **格子里是地址；相当于两个对象内容存的是同一份地址；**

![](./assets/004.png)

* 当另一个变量发生变化时，
* **修改的是同一个堆内存地址上的数据，所以obj1和obj2修改的其实是同一个对象**

![](./assets/005.png)

```js
  var obj_a = {};
  var obj_b = obj_a;

 // true 同类型，比值：同一个地址
  console.log(obj_b == obj_a);


  // false 同类型，比值：不同的地址；
  var obj_a = {};
  var obj_b = {};
  console.log({} == {});


 // 形参与实参
 // 简单数据类型
 function f1(b) {
    b = 2;
 }
 var a = 1;
 console.log(a);


  // 复制数据类型
  function f2(o) {
    o.name = '翠花';
  }
  var a = {
    name: '狗蛋'
  }
  f2(a);

  // 最终a的name属性是多少？翠花
  console.log(a.name);
```