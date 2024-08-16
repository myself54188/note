# **Fonts相关属性**

##### **一.  基本属性**

1. font-family："宋体";

   ==设置字体==，后面可以接多个字体（逗号隔开），当前一个字体电脑上有时，就显示为这个字体。没有就看下一个。

   

2. font-size:  10px;

   ==设置字体大小==，单位像素。

   

3. font-weight：normal / bold / bolder / lighter /......

   ==设置文字粗细==。可以用数字，400是初始的。

   

4. font-style: normal / italic

   normal：默认值，浏览器会显示标准的字体样式。

   italic：浏览器会显示==斜体==的字体样式。

   

##### **二.  复合属性**

如果想改变一段话很多属性，就要写很多条font语句，可以用下面写法：

```html
font: font-style  font-weight  font-size/line-height  font-family;
```

分别写上倾斜，粗细，大小/行高，字体。

<font color = "red">**不能更换顺序**</font>