#### 设置字体阴影使用text-shadow

```css
		h1{
            font-size:68px;
            color:white;
            /*此处填写代码*/
            text-shadow:-1px -3px #ff00de ,0 0 20px #ff00de ;
        }
```

text-shadow: 水平偏移量   垂直偏移量 模糊半径  颜色 ; 

模糊半径如果没有指定数值，则默认为0，值越大，模糊半径越大，阴影也就越大越淡



####设置div水平居中

```css
	div {
        width: 500px;
		margin-left: auto;
		margin-right: auto;
	}
```



####文本属性

```css
 h1{
     text-align:center; /* 设置文字水平居中 */
     text-decoration: underline; /* underline下划线，overline 上划线；
     								none去掉文本修饰线；line-trought(删除线) */
     
     height: 50px;
	 line-height: 50px; /* 行高的值与高度一样 ， 文字垂直居中 */
     
     font-family: "楷体"; /* 设置文本字体 */
 }

```

####列表样式属性

list-style-type: none; 	none-将列表前面的项目符合去掉；disc-实心圆；square-实心小方块；circle-空心圆

list-style-position: inside； inside-项目符号位于里面；outside-项目符号位于外面

list-style-image: url(图像路径)

```css
		ul li{
			/* 列表前面的项目符号设置为图片 */
			list-style-type: square;
			list-style-image: url(./111.jpg);
        }
```



#### 伪类选择器

:link  -  给未被访问过的链接添加样式

:visited -  给已被访问过的链接添加样式

:hover - 当鼠标悬浮在元素上方时，元素的样式

:active -  鼠标放在元素上点击的一瞬间的样式

```css
		/* 正常状态与访问过的状态 样式相同 */
	    a:link,a:visited{
	    	text-decoration: none;
	    	color: #444;
	    }
	    /* 鼠标放上的状态 , 有下划线*/
		a:hover{
			text-decoration: underline;
			color: green;
		}
```

####属性选择器

[attr]   -  匹配所有具有属性名为 attr 的元素

[attr=val]  - 匹配所有 attr属性 的值为 val  的元素

[attr^=val]  -  匹配所有  attr属性值以val 开头的元素

[attr$=val]  -  匹配 属性值以指定值 结尾的元素

[attr*=val]  -  匹配 属性值   包含   指定值  的元素

```css
	[align]{
        color: green;
    }	
	li[align]{
        color: green;
    }
	[align=center]{
    
	}
	[color^="#FF"]{
    
	}
```

####  !important

属性:值 !important ; 

加了 !important  的属性优先级最高，提升了属性的权重

 !important 不能提升继承过来的权重

、

####背景样式属性

`background-repeat: no-repeat` 

​		repeat 默认 平铺 ; repeat-x 水平方向平铺 ;repeat-y 垂直方向平铺 ;no-repeat 不平铺*/

`background-position`: 水平位置  垂直位置;  值可以为：英文；固定值；百分百

```css
	.div1{
		width: 1000px;
		height: 500px;

		background-image: url(./111.jpg);
		background-repeat: no-repeat;  
        
        background-position: right top; /* 右上角 */
        background-position: 100px 100px;*/
		background-position: 100% 100%;
        
        background-attachment: fixed;/* 背景图片固定在浏览器上 */
	}

```

#### 浮动 float

让元素脱离标准文档流，可以让多个元素排在同一行，并设置宽高

float  值 right  left

浮动元素不占用空间，浮动元素的层级比标准文档的层级高

浮动元素浮动以后，其父元素不会再包裹着浮动元素

 将行内元素进行浮动以后，行内元素会变成块级元素



##### 清除浮动

clear: left ; 清除左浮动

clear: right ; 清除右浮动

clear：both； 两者都清除

在最后一个浮动元素的下面新建一个空白div，这个div 什么内容都没有，他只做一件事清除浮动



overflow:hidden ; 将溢出的部分隐藏。

使用 浮动元素的父元素设置属性 ` overflow:hidden ; ` 清除浮动常用于ul



#### margin

标准文档流中，**竖直方向的margin值不会叠加**，会取较大的值

水平方向会叠加

浮动元素竖直方向的margin值会叠加



####border

border： 宽度  线型 颜色；

dotted 点线

dashed  点划线

solid 实线

#### display

行内元素与块级元素之间的转换

元素的显示与隐藏

display: none ; 将元素隐藏

display: block;  行内元素转换成块级元素，将隐藏的元素显示出来

display: inline; 块级元素转换为行内元素



#### position 位置

position: fixed  固定定位- 相对浏览器窗口定位，脱离了标准文档流，层级高

position: relative  相对定位- 没有脱离标准文档流，相对于原来的自己，层级高，占用原来位置的空间

position: absolute  绝对定位- 相对于祖先(有定位属性)定位，脱离标准文档流



#### 结构伪类

E:nth-child(2n)  匹配偶数的孩子

E:nth-child(even)  匹配偶数的孩子

E:nth-child(2n +1)  匹配奇数的孩子

E:nth-child(odd)  匹配奇数的孩子



#### 合并表格边框线

border-collapse:collapse;

#### 阴影

#####文本阴影

text-shadow: 水平阴影(必选)   垂直阴影(必选)  模糊距离  阴影颜色；

##### 盒子阴影

box-shadow:水平阴影(必选)   垂直阴影(必选)  阴影清晰度  阴影大小  阴影颜色  阴影位置；

#### 圆角矩形

border-radius: 左上角 右上角  右下角 左下角;

四个值都一样时可以只写一个。