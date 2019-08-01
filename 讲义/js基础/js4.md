# js基础-day-04

# 函数

* 假设我们要输出一个故事

```javascript
console.log("从前有座山，山里有座庙");
console.log("庙里有个在给小和尚讲故事");
console.log("讲的是什么呢？");
console.log("老和尚对小和尚说：");
```

* 如果我想**随时随地的给别人讲**，代码就会很多；不友好；

```javascript
// 1次
console.log("从前有座山，山里有座庙");
console.log("庙里有个在给小和尚讲故事");
console.log("讲的是什么呢？");
console.log("老和尚对小和尚说：");


// 2次
console.log("从前有座山，山里有座庙");
// ......
```

## 介绍

* 函数：我们**把一段相对独立的具有特定功能的代码块封装起来**，形成一个**独立实体**，起个名字（函数名），在后续开发中可以**随时反复调用**。
* 作用：**封装（包起来）一段代码，将来可以随时拿来使用。**
* **封装：功能要单一**

## 语法

```js
// function 关键字 用于声明函数
// tellStroy 函数名
function tellStroy(){
  // 里面叫函数体：我们封装，我们想随时随地拿来使用的东西；
  console.log("从前有座山，山里有座庙");
  console.log("庙里有个在给小和尚讲故事");
  console.log("讲的是什么呢？");
  console.log("老和尚对小和尚说：");
}
```

* 调用：声明的函数，一段代码被包起来；需要被调用，才能执行当前的函数；

```js
tellStroy(); // 此时在控制台中就会输出一个故事

// 如果想输出多次，就可以调用多次这个函数
tellStroy(); 
tellStroy(); 
tellStroy(); 
```

* 起名字重名，会覆盖；和变量一样；



## 参数

### 配置参数

* 参数：对于函数来说，**函数内部的变量；**
* 为什么？给别人讲故事，得不同的情况套用不同的人名把；
* 语法：

```javascript
// 在小括号内的变量，对于函数来说，就是参数；
// 参数就是函数 内部的变量；
function 函数名(参数){
  // 函数体
}
// 参数：对于函数，这些参数都是形式上的参数，它就是代替位置，相当于是变量在这占了个坑；至于这个参数真实背后代表什么值，我们现在还不知道；
function tellStroy(name){
  console.log("从前有座山，山里有座庙");
  console.log("庙里有个老和尚在给小和尚讲故事");
  console.log("讲的是什么呢？");
  console.log("老和尚对"+ name +"说：");
}
```

* 既然是**变量**，可以被改变存储值；如何改变？调用时传递一个值；

```javascript
// 调用函数的时候，传入参数；
tellStroy('清风');
tellStroy('明月');
```

* 需求：如果我们想在调用函数的时候，把老和尚的名字也明确一下，就把老和尚的名字作为变量改变下；

```javascript
// 声明函数，配置参数；
function tellStroy(name1,name2){
  console.log("从前有座山，山里有座庙");
  console.log("庙里有个老和尚在给小和尚讲故事");
  console.log("讲的是什么呢？");
  console.log(name1 "对"+ name2 +"说：");
}

// 调用函数
tellStroy('圆通','清风');
```

* 如何配置参数？根据业务需求；



### 参数不赋值

* 变量：函数内部的变量，没有赋值，默认为undefined；和我们的变量一模一样；


```js
function tellStroy(name){
  console.log("从前有座山，山里有座庙");
  console.log("庙里有个老和尚在给小和尚讲故事");
  console.log("讲的是什么呢？");
  console.log("老和尚对"+ name +"说：");   // 老和尚对undefined说；
}
```

* 解决：对参数进行判断，如果是undefined，给一个默认值；

```js
function tellStroy(name){
    
  // if 条件语句
  if(name==undefined) {
      name = '小和尚'
  }
  else {
      name = name;
  }
  
  // 三元表达式；
  name = name?name:"小和尚"；
  console.log("从前有座山，山里有座庙");
  console.log("庙里有个老和尚在给小和尚讲故事");
  console.log("讲的是什么呢？");
  console.log("老和尚对"+ name +"说：");
}
```



### 形参与实参

* 参数：
  * 可配置多个；
  * **函数内部的变量**
* 名称：
  * 形参：形式上参数，函数内部声明的变量，只能在内部使用，用于参与逻辑过程；
  * 实参：调用函数时，真实参与运算的数据，可以为传入数据，也可以传变量；

### 相互不影响

* 传入简单值类型，互不影响；

```js
var a = 10;
var b = 20;

function fn(x,y){
  x = x + y;
  console.log(x,y);
}

// 传入实参a b，相当于是变量a的值，赋值给了函数内部变量x，
// x再以后的运算和a没有任何关系；
// 输出 30，20
fnm(a,b); 

// 输出 10,20 a的值并没有受到x的变化影响
console.log(a,b);
```



## 返回值

* 需求： 使用函数的方式求两个数字的和


* 提炼函数：这个过程中，a和b是两个随时要变化的数据，所以我们把它们作为两个参数；

```javascript
var a = 10;
var b = 20;
var sum = a + b;

function getSum(a,b){
  var sum = a + b;
  // 只是打印；
  console.log(sum);
}

```

* 想要：函数内部打印这个值，不是输出这个和，想得到这个和，该怎么呢？
* **函数的返回值**：函数执行完毕，会得到一个结果，这个结果就是函数的返回值；

```javascript
// 默认情况下，函数返回undefined，如果想改变这个返回值，需要使用`return`关键字；
var result = getSUm(20,30);
console.log(result);// 输出undefined
```

### 修改返回值

* **`return`作用1**：修改函数的返回值，若后面有值，则返回，若没有值；默认还是undefined；

```javascript
function getSum(a,b){
  var sum = a + b;
  return sum;
}
```

* 我们再执行这个函数

```javascript
var result = getSUm(20,30);
console.log(result);// 输出50 , return 修改了函数的返回值
```

### 终止函数执行

* **`return`作用2**：终止函数的执行；

```javascript
function fn(){
  console.log(1);
  console.log(2);
  return;
  console.log(3);
  console.log(4);
}
fn(); // 只输出了1和2，3和4没有执行，return 终止了函数的执行，即： return 后面的代码不会执行
```

## 案例

### 求1-n之间所有数的和，写函数

* 实际：1-10和;
* 抽象：把某些地方变为参数；

```js
  function getSum(m) {
    var sum = 0;
    for (var i = 1; i <= m; i++) {
      sum += i;
    }
    return sum;
  }
```

* 步骤：
  1. 写出要实现的过程
  2. 分析过程中哪些是会变化的，哪些是不会变化的
  3. 把变化的作为参数，不变的作为函数体
  4. 按照函数的语法把代码写来
  5. 考虑是否需要修改函数的返回值
* 求n-m之间所有数的和

```js
  function getSum(n，m) {
    var sum = 0;
    for (var i = n; i <= m; i++) {
      sum += i;
    }
    return sum;
  }

// 经验：以后大家会经常看别人写的函数，用法；
/**
 * 函数的作用 - 求n-m之间的整数和
 * @param {type:number} n 较小值
 * @param {type:number} m 较大值
 * @returns {type:number} 整数和
 */
```

* 圆的面积(圆的面积公式： 圆周率 * 半径的平方 )，自己练；

## arguments

* 案例：求三个数的和返回

```js
function getSum1(a, b) {
    return a + b;
}

// 求3个数字的和
function getSum2(a, b, c) {
    return a+b+c;
}
// 函数名不要重名！！！

var res = getSum2(10, 20, 30);
console.log(res);
```

* 目标：无论输入多少个参数，都可以参加运算；
* arguments：获取所有实参的对象，**函数内部的变量（不是我们声明的，也不需要我们声明）**

```javascript
function fn(){
  console.log(arguments);
}
fn(1); // 输出 [1]
fn(1,2) // 输出 [1,2]
fn(1,2,3,4,5) // 输出 [1,2,3,4,5]
```

* arguments 这个东西看起来**样子像数组**，但是其实不是一个数组，我们管它叫 `伪数组`。它具有数组的长度和顺序等特征。本质为对象，
* arguments 伪数组可以**循环遍历**；

```javascript
function getSum(){
  var sum = 0;
  for(var i = 0; i < arguments.length ; i++){
    sum += arguments[i];
  }
  return sum;
}

getSum(1,2,3);// 输出 6
getSum(1,2,3,4,5); // 输出15
```

* 应用场景：**当我们不知道我们的参数个数的时候；**



## 函数表达式

* js中声明函数的方式不只有一种，还有一种方式叫`函数表达式`；
* 声明变量，赋值为函数；

```javascript
// 
var 函数名 = function (参数){
 	//函数体
}

var getSum = function(a,b){
  return a + b;
}
getSum(10,20);
```

## 匿名函数

* 匿名函数：没有名字的函数，但是在js的语法中，是不允许匿名函数单独存在的，要配合其它语法使用：

  如：

```javascript
function (参数){
  函数体
}

var fn = function(a,b){
  return a + b;
}
```

* 自调用函数（自执行函数）：匿名函数的另外一种使用方法；很多时候，我们需要加载页面后，自动执行一个函数；

```js
// 定义之后，立刻调用，输出10
(function(){  
  console.log(10);  
})();
```



## 函数类型

```javascript
function fn(){}
console.log(typeof fn); // 输出 字符串的  function
```

* **在js中，只要是一种数据类型的，都可以作为函数的参数**，

```javascript
function f1(a,b){
  return a + b;
}
f1(10,20); // 数字作为参数
f1('abc','def') // 字符串作为参数
```

## 回调函数

* 函数也是数据类型，也可以作为别的函数的参数

```javascript
// fn 只不过在函数内部是一个形参，内部变量；
function f1(a,fn){
  console.log(a);
  // 函数的调用，在函数名的后面加括号；
  // 内部的函数对外面的函数叫回调函数；
  fn(); 
}

function f2(){
  console.log('f2函数执行了');
}
f1(10,f2);// 输出 10 和 'f2函数执行了'
```

* 像这种作为函数的参数，并在之内调用的函数，我们称为 `回调函数`；





# 作用域

* 作用域：作用范围，能生效的范围；
* 为什么要学作用域？**目前，我们要分清楚自己的声明的变量在哪个作用域下，也就是生效的范围是多大；配合下面预解析的知识，经常是面试比较常问的基础题**；
* 全局：
  * 全局作用域：能在页面的任何位置都可以访问
  * 全局变量：在全局作用哉下声明的变量；
* 局部：
  * 局部作用域：只能在局部的作用域范围进行访问；
  * 局部变量：在局部作用域下声明的变量；

```javascript
var a = 10;
function f1(){
  console.log(a);
}
f1();// 变量a在函数外定义，可以在函数内使用




function f2(){
  var b = 20;
}
// 变量b在函数内定义，在函数外无法访问，报错： b is not defined
console.log(b); 
```



# 预解析

* 在作用域内，你声明的变量在**任何地方**都可以访问。是**任何地方**；
* 预解析：提前、解析（分析）会把**初始化的声明的变量、函数**，全部提升到**当前作用域**的最顶端；
* 也叫变量提升：从概念的字面意义上说，“变量提升”意味着**变量和函数的声明**会在物理层面移动到代码的最前面，但这么说并不准确。实际上变量和函数声明在代码里的位置是不会动的，而是在编译阶段被放入内存中。
  * 变量：已经声明；函数：已经声明；
  * 而变量的赋值和函数的调用还在原来的位置；
* **找到当前作用域的顶端，提升上去**

```javascript
fn();// 正常执行
function f1(){
  console.log(1);
}
fn(); // 正常执行


f2();// 报错 ： f2 is not a function
var f2 = function(){
  console.log(2);
}
// function 关键字定义的函数，可以在定义之前使用，函数表达式的不行
```

* 上面的代码预解析后：

```javascript
function f1(){
  console.log(1);
}
var f2;


fn();
fn();


f2();
f2 = function(){
  console.log(2);
}
```

* 所以在调用f1的时候，其实函数已经声明好了，但是在调用f2的时候，f2还是undefined，就会报错

* 面试基础题：

```javascript
// 观察下面的代码，说出执行结果
var num = 10;
fun();

function fun() {
  console.log(num);
  var num = 20;
}

// ------------------------------------------------------------变量提升的演示
// 预解析：先把你声明变量、函数先全部提升到你当前的作用域的最顶端；
var num;

function fun() {
    var num;
    console.log(num);
    num = 20;
}

// 赋值；
num = 10;
// 函数调用；
fun(); // 输出 undefined；
```



# 小娜v2.0

* 把每个场景下的执行体包装为一个函数；
* 尽量：函数的功能要单一！这个涉及到后面面向对象，封装原型函数，也是函数，功能要单一
* 经验：**高内聚，低耦合！**

## 求和

```js
function calculateCount(nums) {
  var sps = nums.split(',');
  var sum = 0;
  for (var i = 0; i < sps.length; i++) {
    sum += parseFloat(sps[i]);
  }
  return sum;
}
```

## 日期时间数字补足两位的封装

```js
function patchFrontZero(num) {
  if (num < 10) {
    num = "0" + num;
  }
  return num;
}
```

## 格式化日期的封装

```js
function getFormateDate() {
  var date = new Date();
  var year = date.getFullYear();
    
  var month = date.getMonth() + 1;
  month = patchFrontZero(month);
    
  var day = date.getDate();
  day = patchFrontZero(day);
    
  var hour = date.getHours();
  hour = patchFrontZero(hour);
    
  var minutes = date.getMinutes();
  minutes = patchFrontZero(minutes);
    
  var seconds = date.getSeconds();
  seconds = patchFrontZero(seconds);
    
  var format = year + "-" + month + "-" + day + " " + hour + ":" + minutes + ":" + seconds;
  return format;
}
```

