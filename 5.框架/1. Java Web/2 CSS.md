## CSS

### 1. CSS与HTML结合的三种方式

1. 内部样式表

   内部样式表（内嵌样式表）是写到html页面内部，是将所有的css代码抽取出来，单独放在一个<style>标签中。

   

2. 行内样式表

   行内样式表(内联样式表)是在元素标签内部的style属性中设置CSS样式，用于修改简单样式。

   ```css
   <div style = "color: red">行内样式表</div>
   ```

   style就是标签的属性

   在双引号中，写法要符合CSS规范

   可以控制当前的标签设置样式

   

3. <font color = "red">外部样式表（用的最多的）</font>

   实际开发都是外部样式表，适用于比较多的情况，核心是：将样式单独写入css文件中，之后在将css文件引入HTML页面中使用。

   引入分为两步：

   1. 新建一个后缀名为.css的样式文件，把所有的css代码吗都放在此文件内。

   2. 在HTML文件中，使用<link>标签引入这个文件。

      ```html
      <link rel="stylesheet" href="css文件路径">
      ```



### 2. 选择器

选择器分为 ***基础选择器*** 和 ***复合选择器*** 两大类。

基础选择器由单个选择器组合成的。

基础选择器又包括：<font color = "red">标签选择器</font>，<font color = "red">类选择器</font>，<font color = "red">id选择器</font> 和 <font color = "red">通配符选择器</font>。

##### 1.1.  标签选择器

  标签选择器是指用HTML标签名称作为选择器，按标签名称分类，为页面中某一类标签指定统一的CSS样式。

```html
格式：
标签名{
	属性1：属性值1;
	属性2：属性值2;
	属性3：属性值3;
	属性4：属性值4;
}
```



##### 1.2.  类选择器

  ```html
语法：
.类名{
	属性1：属性值1;
	属性2：属性值2;
	属性3：属性值3;
	属性4：属性值4;
}
  ```

多类名：一个标签可以写多个类名。

使用方式为把多个类名写在一个class类里面，之间用空格间隔



##### **1.3.  id选择器**

```html
语法：
#id名{
	属性1：属性值1;
	属性2：属性值2;
	属性3：属性值3;
	属性4：属性值4;
}
```

id和类的区别，id 是 唯一的，只能被调用一次，类可以多次调用。

类选择器相当于人的名字，可以重复。id相当于身份证号，不可重复。



##### **1.4.  通配符选择器**

通配符选择器使用“ * ” 定义，它表示选取页面中所有元素（标签）。

```html
语法：
*{
	属性1：属性值1;
	属性2：属性值2;
	属性3：属性值3;
	属性4：属性值4;
}
```



##### 2.1. 后代选择器

又称为包含选择器，可以选择父元素里的子元素。其写法就是把外层标签写在前面，内层标签写在后面。中间用==空格==分隔。当标签发生嵌套时，内层标签就称为外层标签的后代。

```css
语法:
元素1 元素2{
    样式声明
}

例：
找出ul标签中的li标签改变：
ul li {
    样式声明
}
```

> 元素1和元素2可以用任意基础选择器



##### 2.2.  子元素选择器

语法：

```css
元素1>元素2{
    样式声明
}
```

表示元素1里面的所有直接后代（子元素）元素2。



##### 2.3.  并集选择器

并集选择器可以选择多组标签，同时为他们定义相同的样式，通常用于集体声明。

并集选择器是个选择器通过英文逗号链接而成，<font color="red">任何形式的选择器都可以作为并集选择器的一部分。</font>

```css
语法：
元素1, 元素2{样式申明};
```
