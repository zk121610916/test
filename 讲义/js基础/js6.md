# 00-反馈

* 用户添加笑话：

```js
    // 4：输入笑话
    else if (info == "4") {
      // 把实际的过程写出来；
      // 4.1 要求用户可以输入
      var str = prompt("请输入笑话，可以为单个笑话，也可以为格式如下的笑话：笑话1|笑话2|笑话3");

      // 4.2 需要把字符串转成数组；
      var new_jokes = str.split("|");

      // 4.3 把新的数组添加到旧数组中；
      xiao_na.add_XH(new_jokes);

      // 4.4 告诉用户添加成功
      xiao_na.tan_chuang("您已经添加成功！");

    }
```









# 01-Math-随机-向下取整

* 随机色案例

```js
function getColor() {
    var res = Math.random() * 256;
    res = Math.floor(res);

    return res;
  }

  var color = `rgba(${getColor()},${getColor()},${getColor()},${Math.random()})`;

  // console.log(color);

  // 字符串拼接：'rgb(120,120,110)'; 
  // document.write()

  document.write(`<div style="background:${color}"></div>`);
```



# 02-Math-其他

```js
  // 四舍五入
  // var res = Math.round(3.5000001);
  // console.log(res);

  // 
  // var res = Math.round(-3.9);
  // console.log(res);


  // 绝对值  
  // var res = Math.abs(40);
  // console.log(res);
  // var res = Math.abs(-40.4562);
  // console.log(res);



  // 最大值；
  var res = Math.max(10, 50, 999);
  console.log(res);
```



# 03-Date-常用方法

* 语法：

```js
  // 特定的格式 输入一个字符串
  // var date = new Date("2019-08-23");

  // 0-11
  // var month = date.getMonth();
  // console.log(month);


  // 这另外的格式输入时间
  // var date = new Date(2017, 2, 23, 5, 23, 51);

  // // 0-11
  // var month = date.getMonth();
  // console.log(month);


  // 
  var date = new Date();

  // 
  console.log(date.valueOf());
  console.log(date.getTime());
  console.log(1 * date);

  // 
  console.log(Date.now());
```



# 04-Array-对元素操作-01-push-pop-unshift-shift

```js
  // 
  var arr = [1, 2, 3, 4, 5, 6];


  // push：从数组后面添加数据，参数为单个或多个（逗号隔开)
  // 返回值：新数组的长度；
  // arr.push("abc");
  // console.log(arr, res);


  // pop:从后年删除数据。
  // 返回:被删除的数据；
  // var res = arr.pop();
  // console.log(arr, res);


  // unshift:从前面添加单个数据，或多个数据（逗号隔开）
  // 返回值：新数组的长度
  // var res = arr.unshift("abc", "ghj");
  // console.log(arr, res);


  // shift:从数组的头部去除数据；
  // 返回值：被删除的数据
  // var res = arr.shift();
  // console.log(arr, res);


```



# 04-Array-对元素操作-02-splice

```js
  // splice:删除
  // 删除一个元素
  // var res = arr.splice(3, 1);
  // console.log(arr, res);

  // 作用：第一个参数是下标；被删除数据的个数；
  // 返回值：被删除数据的数组；
  // var res = arr.splice(3, 2);
  // console.log(arr, res);


  // 添加数据
  // 下标3：代表第四个位置，
  // 第二个参数：删除0个；
  // 从下标3的位置，添加后面的数据（第三个和第三个参数）
  // var res = arr.splice(3, 0, "abc", "def");
  // 返回的是空数组；
  // console.log(arr, res);


  // 修改，替换数据
  // 逻辑：删除的逻辑。
  // 把删除的位置的数据，替换为其他数据；
  // var res = arr.splice(3, 1, "abc");
  // console.log(arr, res);
```









# 05-Array-与字符串互转

```js
  // 
  var arr = [1, 2, 3];
  console.log(arr);
  // 转为字符串

  // 不指定分隔符，默认用,  
  var str = arr.join("-");
  console.log(str);


  // 字符串 转为 数据
  var new_arr = str.split("-");
  console.log(new_arr);
```



# 06-Array-查找元素

```js
  // 用于：判断这个数据在不在当前的数组；

  var arr = [1, 10, 20, 30];

  // 在数组中 返回下标
  // 不在：返回-1；
  // var index = arr.indexOf(10);
  // console.log(index);


  // 返回按照条件的第一个数据的下标（索引）
  var res = arr.findIndex(function(item) {
    // 判断的条件：条件这可以根据业务自己设置；
    return item > 50;
  });

  // 没有满足条件的时候，返回-1；
```



# 07-Array-遍历

```js
  // item:当前遍历的一个数据
  // index：当前数据的下标
  // arr:当前的循环的数组
  // var sum = 0;
  // arr.forEach(function(item, index, arr) {
  //   // console.log(index, item, arr);
  //   sum += item;
  // });.

  // 把数组中 下标为偶数 的数据挑出来；
  var arr = [0, 10, 15, 20, 60];
  // var new_arr = [];
  // for (var index = 0; index < arr.length; index++) {
  //   // 找 下标为偶数
  //   if (index % 2 == 0) {
  //     // 从后添加数据
  //     new_arr.push(arr[index])
  //   }
  // }
  // console.log(new_arr);


  // 筛选出 满足条件的数组
  var res = arr.filter(function(item, index, arr) {
    // return 后面要跟一个筛选的条件；
    // 条件成立时，返回item到新数组里；
    return index % 2 == 0;
  });
```



# 08-Array-拼接与截取

```js
  // var arr = [1, 2, 1, 3];

  // concat:不操作原数组，创建新数组返回；
  // var res_1 = arr.concat("abc", "45");
  // console.log(res, arr);

  // 
  // var res_2 = arr.concat(["a", "b"]);
  // console.log(res_2, arr);

  // 截取：不对原数组操作，返回新数组
  // 
  var arr = ["a", "b", "c", "d", "f"];

  // 截取：包左不包右；
  // 第一个参数：截取的左下标；（包括）
  // 第二个参数：截取的右下标；(不包括)
  var res_1 = arr.slice(1, 4);
  console.log(res_1, arr);


  // 第二个参数省略，默认为数字的长度；
  var res_2 = arr.slice(1);
  console.log(res_2, arr);


  // 两个参数全部省略，第一个默认为0，第二个默认为length
  var res_3 = arr.slice();
  console.log(res_3, arr);
```



# 09-Array-复制

```js
  // 数组的复制；
  var arr = [1, 2, 4, 5, 6];

  // 复制？
  // var new_arr = arr;


  // 数组，类型是对象；
  // 
  // new_arr[0] = "abc";
  // console.log(new_arr, arr);

  // 如何复制一个数组；
  var new_arr_1 = [];
  for (let index = 0; index < arr.length; index++) {
    new_arr_1[index] = arr[index];
  }

  // forEach : 变量
  var new_arr_2 = [];
  arr.forEach(function(item, index) {
    new_arr_2.push(item);
  });

  // filter:数组过滤：满足条件返出来，返每个元素，返到新数组；
  var new_arr_3 = arr.filter(function(item, index) {
    return true;
  });

  // concat: 不对原数组操作，返回是一个新的数组；
  var new_arr_4 = arr.concat();
  // console.log(new_arr_4);

  // slice :截取 不对原数组操作，返回是一个新的数组；
  var new_arr_5 = arr.slice();
```



# 10-Object-复习

```js
  // 对象如何复制？
  // 
  var obj = {
    name: "abc",
    age: 19,
    sayHi: function() {
      console.log(1);
    }
  };

  // JS 浅拷贝；
  var new_obj = {};
  for (var key in obj) {
    // console.log(key, obj[key]);
    new_obj[key] = obj[key];
  }
```



# 11-String-为什么会有方法及不可变

* 字符串不可变：
  * 旧的字符串赋值在一个变量上，给变量重新赋值新的字符串（完全新的字符串，或者拼接后字符串），旧的字符串不是被覆盖；**在内存的游离状态；**
  * **所以尽量避免大量使用字符串的拼接；这个算性能优化的一点；**

# 12-String-查找字符

```js
  // 
  var str = "我爱北京天安门";

  // 有：返回字符串在当前字符串的下标；
  // 没有：-1；
  var index = str.indexOf("通州");
  console.log(index);


  // 查询：字符串的下标位置的字符；
  var char = str.charAt(2);
  console.log(char);


  // var a = "a";
  // a.charCodeAt(); 返回ASCII 码
  var res = str.charCodeAt(0);
  console.log(res);
```



# 13-String-split-案例

```js
  // 地址上有参数的 字符串
  var str = 'b.com?name="张三"&age=16';
  // 1.先用 ? 进行字符串分隔，把后面的参数分隔；
  var arr_1 = str.split("?");
  // 2.这个地方分隔出来是长度为2的数组，取到最后一个数据
  // name="张三"&age=16
  var str_1 = arr_1[1];
  // 3.对 取到的数据，也就是这个字符串进行 新 的分隔；得到新的数组；
  // 数组为 ['name="张三"','age=16']
  var arr_2 = str_1.split("&");
  // 4.数组的循环;
  var obj = {};
  arr_2.forEach(function(item, index) {
    // 5.在循环体内部，进行每个数据的再次分隔；
    // 比如：'name="张三"'  进行 = 分隔；
    // 分隔后得到数组  为 ["name","张三"]
    var res = item.split("=");
    // 数组 第一个位置 键key, 第二个位置 值value；
    var key = res[0];
    var value = res[1];
    // 给对象的属性进行赋值；
    obj[key] = value;
  });
```





# 14-String-拼接与截取

```js
  var str = "abc";

  // 返回新的字符串；
  var res = str.concat("dfg", "ghj");

  var str_1 = "我爱北京天安门的红旗";

  // substring:两个参数都为下标，包左不包右的截取，
  // 不对原字符串进行操作，返回新的字符串；
  var res_1 = str_1.substring(2, 5);
  // console.log(res_1, str_1);

  // 参数为负数的时候，要和字符串的长度进行相加；
  // 不对原字符串进行操作， 返回新的字符串；
  var res_2 = str_1.slice(-6, 9);
  // console.log(res_2, str_1);

  // 参数：第一个为下标，第二个截取的长度；
  //  不对原字符串进行操作，返回新的字符串；
  var res_3 = str_1.substr(2, 3);
  console.log(res_3);
```

