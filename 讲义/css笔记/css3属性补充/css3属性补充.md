## 1. css3盒模型

- box-sizing:content-box;

  padding和border不被包含在定义的width和height之内。对象的实际宽度等于设置的width值和border、padding之和，即 ( Element width = width + border + padding ) 

  此属性表现为标准模式下的盒模型。 

- box-sizing:border-box; 

  padding和border被包含在定义的width和height之内。对象的实际宽度就等于设置的width值，即使定义有border和padding也不会改变对象的实际宽度，即 ( Element width = width ) 

  此属性表现为怪异模式下的盒模型。 

## 2. 背景属性

### 2.1背景尺寸:backgroud-size:

检索或设置对象的背景图像的尺寸大小。

- 该属性提供2个参数值(特性值cover和contain除外)。 

  - cover： 

    将背景图像等比缩放到完全覆盖容器，背景图像有可能超出容器。 

  - contain： 

    将背景图像等比缩放到宽度或高度与容器的宽度或高度相等，背景图像始终被包含在容器内。 

- 如果提供两个具体的值(百分比或者像素值)，第一个用于定义背景图像的宽度，第二个用于定义背景图像的高度。 

- 如果只提供一个值，该值将用于定义背景图像的宽度，第2个值默认为auto，即高度为auto，此时背景图以提供的宽度作为参照来进行等比缩放。

  代码示例

  ```
  background-size: cover;
  background-size: contain;
  background-size: 50% 50%;
  background-size: 200px 300px;
  background-size: 200px;
  ```

### 2.2背景线型渐变

```
自左向右:background-image: linear-gradient(to right,white, gray);
自右向左:background-image: linear-gradient(to left,white, gray);
自上向下:background-image: linear-gradient(to bottom,white, gray);
自下向上:background-image: linear-gradient(to top,white, gray);
注意:多组颜色之间逗号隔开
```

### 2.3多背景图片

```
background:url(test1.jpg) no-repeat  10px 20px/50px 60px,
	   url(test2.jpg) no-repeat  10px 20px/70px 90px,
	   url(test3.jpg) no-repeat  10px 20px/110px 130px #ccc;
	   多张背景图片之间用逗号隔开
注意， 背景颜色只能设置一次，且由于写在前面的背景会叠在之后的背景之上，所以背景色通常都定义在最后一组上，避免背景色将图像盖住。
```