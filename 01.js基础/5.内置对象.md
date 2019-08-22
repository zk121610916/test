# js基础-day-06



# 内置对象

* 对象：属性和方法的集合体；**我们关注如何使用就可以；**
* 内置：JS语法给我封装好了一些对象，里面提供了很多常用、实用的属性和方法；

## Math

* Math.random(x)；这个方法的作用是生成一个 [0,1) 之间的随机浮点数

```js
var r = Math.random();
// 每次执行等到的数字都不一样
console.log(r);
```

* Math.floor(x)   把一个浮点数进行向下取整

```js
console.log(Math.floor(3.1));  // 3
console.log(Math.floor(3.9));  // 3
console.log(Math.floor(-3.1)); // -4
console.log(Math.floor(-3.9)); // -4
```

* 案例：刷新页面，页面改变颜色；

```js
  function getRandom() {
    return Math.floor(Math.random() * (255 + 1));
  }

  function getRandomColor() {
    var r = getRandom();
    var g = getRandom();
    var b = getRandom();
    var color = 'rgb(' + r + ',' + g + ',' + b + ')';
    return color;
  }

document.write(`<div style='background:${getRandomColor()}'></div>`)
```

* Math.round(x)  把一个浮点数进行四舍五入取整

```js
console.log(Math.floor(3.1));  // 3
console.log(Math.floor(3.9));  // 4
console.log(Math.floor(-3.1)); // -3
console.log(Math.floor(-3.9)); // -4
```

* Math.ceil(x)  把一个浮点数进行向上取整

```js
console.log(Math.ceil(3.1));  // 4
console.log(Math.ceil(3.9));  // 4
console.log(Math.ceil(-3.1)); // -3
console.log(Math.ceil(-3.9)); // -3
```

* Math.abs(x)  求一个数的绝对值 正数

```js
console.log(Math.abs(3));  // 3
console.log(Math.abs(-3)); // 3
```

* Math.max(x,y...)  求多个数字中的最大值，同理 Math.min(x,y...);

```js
console.log(Math.max(10,20));  // 20
console.log(Math.max(8,4,5,7,3)); // 8

console.log(Math.min(10,20));  // 10
console.log(Math.min(8,4,5,7,3)); // 3
```





## Date 

* 语法：

```js
var date = new Date(); // 得到的是当前时间的日期对象
```

* 获取年月日时分秒

```js
console.log(date.getFullYear());// 年份
console.log(date.getMonth()); // 月份，从0开始

// 当前日 为什么不是getDay() 英语：日期date，day某天；
console.log(date.getDate()); 

console.log(date.getHours()); // 小时，0-23
console.log(date.getMinutes()); // 分钟 ， 0-59
console.log(date.getSeconds()); // 秒数 ， 0-59
console.log(date.getMilliseconds()); // 毫秒 0-999 ， 1秒 = 1000毫秒
```

* 创建一个**指定日期**的对象

```js
// 给一个日期格式字符串
var date = new Date('2019-01-01');

// 分别传入年月日时分秒。注意传入的月份是从0开始算的
var date = new Date(2019,0,1,12,33,12);
```

* 获取从1970年1月1日到现在的总毫秒数，**常说的时间戳**

```js
var date = new Date();

console.log(date.valueOf());
console.log(date.getTime());
console.log(1*date);

console.log(Date.now());
```



## Array

### 对元素操作

* push 从数组后面推入一个元素或多个元素

```js
var arr = [1,2,3];

// 返回：修改后数组的长度
arr.push(4,5,6);
```

* pop 删除数组最后一个元素

```js
// 数组的pop方法用于将数组的最后一个元素移除
var arr = [1,2,3];

// 返回 被删除的元素；
arr.pop();
```

* unshift 从数组前面添加一个或多个元素

```js
var arr = [1,2,3];

// 返回：修改后数组的长度
arr.unshift(4,5,6);
```

* shift 用于将数组的第一个元素移除

```js
// 数组的shift方法用于将数组的第一个元素移除
var arr = [1,2,3];

// 返回 被删除的元素；
arr.shift(); 
```

* splice：可进行数组**任何位置**的增删改

```js
// 数组的splice方法用于从数组的指定位置移除、添加、替换元素
var arr = ['a','b','c','d','e'];

// 对原数组操作
// 作用：从索引3开始移除，总共移除1个元素 ，
// 返回 被移除数据的数组
arr.splice(3,1); 
console.log(arr);


// 在c的后面添加7和8两个元素
// 作用：从索引3开始添加，移除0个元素，把7，8加入；
// 
// 返回：一个空数组
// 操作原数组；
arr.splice(3,0,7,8); 



// 作用：从索引1开始替换，总共替换1个，用0替换 ；
// 返回：被替换元素的数组
arr.splice(1,1,0);
console.log(arr); 
```

### 与字符串互转

* join 用于将数组中的多元素以指定分隔符连接成一个字符串

```js
var arr = ['刘备','关羽','张飞'];
var str = arr.join('|'); 
console.log(str);  // 刘备|关羽|张飞
```

* split 字符串的方法：转数字，后面为分隔的字符

```js
// 这个方法用于将一个字符串以指定的符号分割成数组
var str = '刘备|关羽|张飞';
var arr = str.split('|');
console.log(arr);
```

### 查找元素

* indexOf：根据元素查找索引，如果这个元素在数组中，返回索引，否则返回-1，找元素在不在数组内部

```js
var arr = [10,20,30]
console.log(arr.indexOf(30));  // 2
console.log(arr.indexOf(40));  // -1
```

* findIndex方法用于查找满足条件的第一个元素的索引，如果没有，则返回-1

```js
var arr = [10, 20, 30];
var res1 = arr.findIndex(function (item) {
  return item >= 20;
});
// 返回 满足条件的第一个元素的的索引
console.log(res1);  


var res2 = arr.findIndex(function (item) {
  return item >= 50;
});
// -1
console.log(res2);
```

### 遍历数组

* for循环：JS基础语法；
* forEach：遍历数组；

```js
// 数组的 forEach 方法用于遍历数组
var arr_1 = [10,20,30,40,50];

// forEach方法需要一个参数是一个函数，这个函数也接收3个参数，
// item是数组中的每个元素，index是item的索引,arr表示当前数组；
// 对本数组操作，没有返回值；
arr_1.forEach(function(item,index,arr){
  console.log(item,index);
});
```

* filter 筛选出数组中满足条件的数组，**返回是一个新的数组；**

```js
// 数组的filter方法用于将数组中满足条件的元素筛选出来
// 筛选出数组中 小于 2000 的数据
var arr_1 = [1500,1800,2200,300,2600,800];
var res = arr_1.filter(function(item,index,arr){
  return item < 2000;
});
// fitler方法的的参数要求是一个函数，这个函数接收2个参数：item是数组中的每个元素，index是item对应的索引,arr代表当前的数组

// 复制一个数组
var arr_1 = arr_2.filter(function(item,index,arr){
  return item;
});
```

### 拼接与截取

* concat  拼接数组，**不改变原数组，创建新数组返回**

```js
// 数组的concat方法的作用是把多个数组合并成一个新的数组
var arr1 = [1,2,3];
var arr2 = [4,5,6];
var arr3 = [7,8,9];
var res = arr1.concat(arr2,arr3);
console.log(res);

// 复制一个数组
var arr_1 = arr.concat();
```

* slice 截取数组：**不对原数组操作，返回的是新的数组；**

```js
var arr = ['a','b','c','d','e'];
// 表示 从下标1（包括），截取到下标为4(不包括),
var res = arr.slice(1, 4);

// 如果不给第二个参数，默认就是把从start开始，到length结束的所有的元素截取
var arr_1 = arr_2.slice(1);

// 复制数组：如果省略两个参数，start默认是0，end默认是length
var arr_1 = arr_2.slice();
```

### 复制

```js
var arr = [10,20,30,40,50];

// 直接给，可以么？
var new_arr = arr;

var new_arr = [];
arr.forEach(function(item,index,arr){
  new_arr.push(item)
});

var new_arr = arr.filter(function(item,index,arr){
  return item;
});

var new_arr = arr.concat();
var new_arr = arr.slice();
```



## Object 复习

* 语法

```js
var obj = {}; // 字面写的这些数据，知道它的数据类型；
var obj_1 = new Object();

// 对象.属性 = 值;
obj.name = '狗蛋';
// 键值对
obj['age'] = 10;

obj.name
obj['age']

// 
for(var name in obj){
  console.log(name);
  // 只能通过键的方式获取值
  console.log(obj[name]);
}
```

* 复制

```js
var obj_1 = {
    a:1,
    b:2
};
// 可以这样么？
var obj_2 = obj_1;

var obj_2 = {}
for(var key in obj_1){
  obj_2[key] = obj_1[key]
}
```



## String

### 为什么字符串会方法（了解）

* 当访问字符串的属性时，访问过程处于一种读取模式，也就是要从内存中读取这个字符串的值。而在读取模式中访问字符串是，后台都会自动完成以下处理： 
  * 创建String类型的一个实例； 
  * 在实例上调用指定的方法； 
  * 销毁这个实例。 
* 临时对象：被称为“包装对象”，对象和包装对象的 主要区别在于对象的生存周期，可以看到自动创建的包装对象的生存周期只是一行代码的执行瞬间，然后立即销毁。而使用new操作符创建的对象的生存周期在执行流离开当前作用域之前都一直保存在内存中。 
* 可以通过String（），Number（），Boolean（）构造函数来显示地创建**包装对象**。但是通常情况下不建议这么做。 对包装对象的实例调用typeof会返回“object”，而且所有包装对象都会转换为布尔值true。 
* 拓展：<https://blog.csdn.net/IT_10/article/details/80913947>

### 不可变

* 旧的字符串赋值在一个变量上，给变量重新赋值新的字符串（完全新的字符串，或者拼接后字符串），旧的字符串不是被覆盖；**在内存的游离状态；**
* **所以尽量避免大量使用字符串的拼接；这个算性能优化的一点；**

### 查找

* indexOf  字符串中是否存在指定字符 存在就返回找到的下标，没有就-1；

```
// 这个方法用于查找某个字符串是否包含于另一个字符串
var str = '我爱中华人民共和国';
console.log(str.indexOf('中华'));
```

* lastIndexOf  用法和indexOf一样，只不过是从后面开始找
* charAt：

```js
// 这个方法用于获取字符串中位于指定位置的字符
var str = '我爱中华人民共和国';
console.log(str.charAt(2));
```

* charCodeAt

```js
// 这个方法用于获取字符串中位于指定位置的字符的ASCII码
var str = 'abcdef'
console.log(str.charCodeAt(0));
```

### 转为数组

* split 字符串转数字，后面的分隔的字符

```javascript
// 这个方法用于将一个字符串以指定的符号分割成数组
var str = '刘备|关羽|张飞';
var arr = str.split('|');
console.log(arr);
```

* 案例：解析地址`b.com?id=1&name=2`中的参数；

### 拼接与截取

* +
* concat 拼接

```js
// 这个方法用于连接多个字符串，其作用相当于 + 操作符
var res = "abc".concat('def','ghi');
console.log(res);
```

* substring 截取字符串，不操作原字符串；返回截取出来的字符串；

```javascript
// 这个方法用于获取字符串中的部分字符
var str = '我爱中华人民共和国';

// 从索引2开始，到索引4结束，得到之间的字符，不包含索引4的字符
var res = str.substring(2,4);
console.log(res);
```

* slice 

```js
// 这个方法用于获取字符串中的部分字符
var str = '我爱中华人民共和国';
var res = str.slice(2,4);// 从索引2开始，到索引4结束，得到之间的字符，不包含索引4的字符
console.log(res);

// 看起来和substring 没有啥区别，
// slice()可以设置参数为负数，slice()方法在遇到负的参数的时候，会将这个负值与字符串的长度相加。
console.log(str.slice(-6,7));
console.log(str.slice(2,-5));
console.log(str.slice(-9,-7));
```

* substr 

```javascript
// 这个方法用于获取字符串中的部分字符
var str = '我爱中华人民共和国';
var res = str.substr(2,2);// 索引2开始，总共获取2个字符，第二个参数为个数
```



## 文档

* MDN - <https://developer.mozilla.org/zh-CN/> - 这个东西跟百度一样使用即可 - 最推荐 - 官方比较权威的，新且全;

* 百度

* 菜鸟 - <https://www.runoob.com/> 也是跟普通的论坛一样使用即可

* 如何学习一个方法（对象）

  * 1.方法的功能是什么

  - 2.方法的参数有哪些

  - 3.方法的返回值是什么























 