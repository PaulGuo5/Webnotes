# CSS 学习笔记
>    本文来源于网络，可能存在错漏之处，仅供参考。

## Flex布局
>    [参考](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

1. 简介
- Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。Flex 布局，可以简便、完整、响应式地实现各种页面布局。
- 布局的传统解决方案，基于盒状模型，依赖 display 属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。

2. 基本使用
- 任何容器
```bash
.box{
  display: flex;
}
```
- 行内元素
```bash
.box{
  display: inline-flex;
}
```
 *设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。*  
 
3. Flex容器  
![flex-container](https://raw.githubusercontent.com/PaulGuo5/Webnotes/master/img/flex-item.png)  

4.容器的属性  
4.1 **flex-direction**属性决定主轴的方向（即项目的排列方向）。  
```bash
.box {
  flex-direction: row(default) | row-reverse | column | column-reverse;
}
```
![flex-direction](https://raw.githubusercontent.com/PaulGuo5/Webnotes/master/img/flex-direction.png)  

4.2 **flex-wrap**属性:如果一条轴线排不下，如何换行。  
```bash
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```  
![flex-wrap](https://raw.githubusercontent.com/PaulGuo5/Webnotes/master/img/flex-wrap.png)  

4.3 **flex-flow**属性:flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
```bash
.box {
  flex-flow: <flex-direction> || <flex-wrap>;
}
```
4.4 **justify-content**属性：项目在主轴上的对齐方式。
```bash
.box {
  justify-content: flex-start | flex-end | center | space-between | space-around;
}
```
+ flex-start（默认值）：左对齐
+ flex-end：右对齐
+ center： 居中
+ space-between：两端对齐，项目之间的间隔都相等。
+ space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

4.5 **align-items**:定义了项目在主轴上的对齐方式
```bash
.box {
  align-items: flex-start | flex-end | center | baseline | stretch;
}
```
+ flex-start：交叉轴的起点对齐。
+ flex-end：交叉轴的终点对齐。
+ center：交叉轴的中点对齐。
+ baseline: 项目的第一行文字的基线对齐。
+ stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

4.6 **align-content**：多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。  
```bash
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```
+ flex-start：与交叉轴的起点对齐。
+ flex-end：与交叉轴的终点对齐。
+ center：与交叉轴的中点对齐。
+ space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
+ space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
+ stretch（默认值）：轴线占满整个交叉轴。

5. 项目的属性
5.1 **order**属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
```bash
.item {
  order: <integer>;
}
```
5.2 **flex-grow**属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。  
```bash
.item {
  flex-grow: <number>; /* default 0 */
}
```
5.3 **flex-shrink**属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
```bash
.item {
  flex-shrink: <number>; /* default 1 */
}
```
5.4 **flex-basis**属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。与height和width相似。
```bash
.item {
  flex-basis: <length> | auto; /* default auto */
}
```
5.5 **flex**属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
```bash
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```
5.6 **align-self**属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch.该属性可能取6个值，除了auto，其他都与align-items属性完全一致。
```bash
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```




## em
>  [参考](https://www.w3cplus.com/css/px-to-em)

- 浏览器的默认字体大小是16px
- 如果元素自身没有设置字体大小，那么元素自身上的所有属性值如“border、width、height、padding、margin、line-height”等值，我们都可以按下面的公式来计算
```bash
1 ÷ 父元素的font-size × 需要转换的像素值 = em值
```
- 这一种千万要慢慢理解，不然很容易与第二点混了。如果元素设置了字体大小，那么字体大小的转换依旧按第二条公式计算，也就是下面的：
```bash
1 ÷ 父元素的font-size × 需要转换的像素值 = em值
```
- 那么元素设置了字体大小，此元素的其他属性，如“border、width、height、padding、margin、line-height”计算就需要按照下面的公式来计算：
```bash
1 ÷ 元素自身的font-size × 需要转换的像素值 = em值
```

## rem
> [参考1](https://www.w3cplus.com/css3/define-font-size-with-css3-rem)
> [参考2](https://zhuanlan.zhihu.com/p/28915418)
- Rem是相对于根元素<html>，这样就意味着，我们只需要在根元素确定一个参考值，在根元素中设置多大的字体，这完全可以根据您自己的需要。
- 为了简化font-size的换算，我们在根元素html中加入font-size: 62.5%;
```bash
 html {font-size: 62.5%;  } /*  公式16px*62.5%=10px  */ 
```




## CSS盒子模型
> [参考](https://www.jianshu.com/p/d6365d449745)
1. Margin(外边距) - 清除边框外的区域，外边距是透明的。
2. Border(边框) - 围绕在内边距和内容外的边框。
3. Padding(内边距) - 清除内容周围的区域，内边距是透明的。
4. Content(内容) - 盒子的内容，显示文本和图像





## position的几种值，相对谁定位，百分比以谁为参照
- absolute:绝对定位，相对于static定位以外的第一个父元素进行定位
- fixed：绝对定位，相对于浏览器窗口进行定位
- relative：相对定位，按照元素的原始位置对该元素进行定位
- static：默认值。元素出现在文档流中。
- inherit：从父元素继承position属性的值。
+ 百分比以父容器为参照




## CSS reset
- 因为不同核心的浏览器对CSS的解析效果呈现各异，导致所期望的效果跟浏览器的“理解”效果有偏差，css reset就是用来重置（复位）元素在不同核心浏览器下的默认值，尽量保证元素在不同浏览器下的同一“起跑线”。
- 有必要重置的元素才写，不要照搬全抄。




## CSS放在底部和顶部的区别（？）
- css放在顶部；如果放在底部，浏览器构建完DOM树，然后才开始渲染，当渲染树构建完成，又要重新渲染整个页面，造成资源的浪费。
- 重要的CSS和JS放在顶部，次要的放在底部
*JS放在body和header中的区别*
- 在HTML的body部分中的JavaScripts会在页面加载的时候被执行。
- 在HTML的head部分中的JavaScripts会在被调用的时候才执行。




## CSS选择器的优先级
- CSS的选择器类型：标签选择器、类选择器、ID选择器、全局选择器、组合选择器、后代选择器、群组选择器、继承选择器、伪类选择器、字符串匹配的属性选择器、子选择器、CSS相邻兄弟选择器
- !important属性会覆盖任何样式，权重最高
> !important>行内样式>ID选择器>类选择器>标签>通配符>继承>浏览器默认属性
*后写的样式会覆盖先写的样式*
- ID选择器是唯一的，不要再在前面写其他选择器了



## CSS link和import的区别
- <link rel="stylesheets" href="..." />，link是html标签，只能放在html源码中。link引入的css文件在页面加载之前完成。
- @import url(...) ,import在html和css中都可以使用，相当于一种css样式。import引入的css会在页面加载完成后再加载。




## 左侧固定，右侧自适应两栏布局
- 左边左浮动，右边加个overflow:hidden;
```bash
lt{ float: left;width:200px; background: #ff0;}
rt{ overflow: hidden; background: #f0f;}
```
- 左边左浮动，右边加个margin-left;
```bash
lt{ float: left; width:200px; background: #ff0;}
rt{ margin-left: 200px; background: #f0f;}
```
- 左边绝对定位，右边加个margin-left;
```bash
lt{ position: absolute; top:0; left:0; width:200px; background: #ff0;}
rt{ margin-left: 200px; background: #f0f;}
```
- 左右两边绝对定位，右边加个width,top,left,right
```bash
lt{ position: absolute; top:0 ; left:0 ;width:200px; background: #ff0;}
rt{ position: absolute; top:0 ; left:200px; width: 100% ; rigth:0;background: #f0f;}
```




## 左右两边固定，中间自适应，且中间内容优先显示布局的几种方法
- 左右两边绝对定位法,中间用margin-left、margin-right；
```bash
main{ margin: 0 200px; overflow: hidden; background: #ccc;}
left{ position: absolute; top: 0; left: 0;width: 200px; background: #ff0;}
right{ position: absolute;top: 0; right: 0; width: 200px; background: #0ff;}
```
- 中间的盒子绝对定位，左右两边的盒子左右浮动
```bash
main{ position: absolute; top: 0; left: 200px; right: 200px; background: #ccc;}
left{ float: left;width: 200px;background: #ff0;}
right{ float: right;width: 200px; background: #0ff;}
```





## 清除浮动的方式 
- clear:both 元素两侧都不允许出现浮动元素



## 响应式
- 如果浏览器窗口小于 500px, 背景将变为浅蓝色
```bash
@media only screen and (max-width: 500px) {
    body {
        background-color: lightblue;
    }
}
```
- 当屏幕 (浏览器窗口) 小于 768px, 每一列的宽度是 100%:
```bash
/* For desktop: */
.col-1 {width: 8.33%;}
.col-2 {width: 16.66%;}
.col-3 {width: 25%;}
.col-4 {width: 33.33%;}
.col-5 {width: 41.66%;}
.col-6 {width: 50%;}
.col-7 {width: 58.33%;}
.col-8 {width: 66.66%;}
.col-9 {width: 75%;}
.col-10 {width: 83.33%;}
.col-11 {width: 91.66%;}
.col-12 {width: 100%;}

@media only screen and (max-width: 768px) {
    /* For mobile phones: */
    [class*="col-"] {
        width: 100%;
    }
}
```



## margin
```bash
margin: style                  /*单值语法  所有边缘 */  举例： margin: 1em; 
margin: vertical horizontal    /*二值语法  纵向 横向 */  举例： margin: 5% auto; 
margin: top horizontal bottom  /*三值语法 上 横向 下*/  举例： margin: 1em auto 2em; 
margin: top right bottom left  /*四值语法 上 右 下 左*/  举例： margin: 2px 1em 0 auto;
```


## overflow
```bash
/* 默认值。内容不会被修剪，会呈现在元素框之外 */
overflow: visible;

/* 内容会被修剪，并且其余内容不可见 */
overflow: hidden;

/* 内容会被修剪，浏览器会显示滚动条以便查看其余内容 */
overflow: scroll;

/* 由浏览器定夺，如果内容被修剪，就会显示滚动条 */
overflow: auto;

/* 规定从父元素继承overflow属性的值 */
overflow: inherit;
```




## display visibility opacity 的区别
```bash
{ display: none; /* 不占据空间，无法点击 */ } 
{ visibility: hidden; /* 占据空间，无法点击 */ } 
{ opacity: 0; filter:Alpha(opacity=0); /* 占据空间，可以点击 */ } 
```



## IE6下的双边距、内联和块级元素等  
- 双边距问题：当元素的浮动方向与它的margin方向一致时，IE6会把margin值解析为原来的2倍
- 如何解决:
1. 给float元素添加display:inline
2. 给ie6写一个hack，值为正常值的一半，例如:
```bash
margin-left:20px; 
IE^: _margin-left:10px;
```



## font-face
> [参考][https://developer.mozilla.org/zh-CN/docs/Web/CSS/@font-face]
- 可以自定义字体
- 这是一个叫做@font-face 的CSS @规则 ，它允许网页开发者为其网页指定在线字体。 通过这种作者自备字体的方式，@font-face 可以消除对用户电脑字体的依赖。 @font-face 不仅可以放在在CSS的最顶层, 也可以放在 @规则 的 条件规则组 中。





## 实现一个圆环进度条，如何用css画圆环里的进度条
- 两个div，一个左边一个右边，都只有一半的border，然后分别设置overflow:hidden，通过旋转来得到想要的样式

```bash
<style>
    .circle_process{
        position: relative;
        width: 199px;
        height : 200px;
    }
    .circle_process .wrapper{
        width: 100px;
        height: 200px;
        position: absolute;
        top:0;
        overflow: hidden;
    }
    .circle_process .right{
        right:0;
    }
    .circle_process .left{
        left:0;
    }
    .circle_process .circle{
        width: 160px;
        height: 160px;
        border:20px solid transparent;
        border-radius: 50%;
        position: absolute;
        top:0;
        transform : rotate(-135deg);
    }
    .circle_process .rightcircle{
        border-top:20px solid green;
        border-right:20px solid green;
        right:0;
        -webkit-animation: circle_right 5s linear infinite;
    }
    .circle_process .leftcircle{
        border-bottom:20px solid green;
        border-left:20px solid green;
        left:0;
        -webkit-animation: circle_left 5s linear infinite;
    }
    @-webkit-keyframes circle_right{
        0%{
            -webkit-transform: rotate(-135deg);
        }
        50%,100%{
            -webkit-transform: rotate(45deg);
        }
    }
    @-webkit-keyframes circle_left{
        0%,50%{
            -webkit-transform: rotate(-135deg);
        }
        100%{
            -webkit-transform: rotate(45deg);
        }
    }
</style>
<div class="circle_process">
    <div class="wrapper right">
        <div class="circle rightcircle"></div>
    </div>
    <div class="wrapper left">
        <div class="circle leftcircle" id="leftcircle"></div>
    </div>
</div>
```




## 页面效果切换，当鼠标划过时当前div消失，换成另一个div显示，用css怎么实现
- hover属性，设置z-index

> [鼠标经过div改变背景图片](https://blog.csdn.net/qq_24309787/article/details/82774743)
```bash
 .biankuang {
    height: 126px;
    width: 15%;
    margin-left: 14px;
    margin-top: 20px;
    background-image: url(bj1.png);
    background-size: 100% 100%;
    background-repead: no-repead;
  }
 
  .biankuang:hover,.biankuang:active {
    background-image: url(bj2.png);
    background-size: 100% 100%;
    background-repead: no-repead;
  }
```




## z-index 
> [参考](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index)
- z-index属性指定了一个具有定位属性的元素及其子代元素的 z-order。 
- 当元素之间重叠的时候，z-order 决定哪一个元素覆盖在其余元素的上方显示。 
- 通常来说 z-index 较大的元素会覆盖较小的一个。
- 对于一个已经定位的元素（即position属性值是非static的元素），z-index 属性指定：
+ 元素在当前堆叠上下文中的堆叠层级。
+ 元素是否创建一个新的本地堆叠上下文。
```bash
.dashed-box { 
  position: relative;
  z-index: 1;
  border: dashed;
  height: 8em;
  margin-bottom: 1em;
  margin-top: 2em;
}
.gold-box { 
  position: absolute;
  z-index: 3; /* put .gold-box above .green-box and .dashed-box */
  background: gold;
  width: 80%;
  left: 60px;
  top: 3em;
}
.green-box { 
  position: absolute;
  z-index: 2; /* put .green-box above .dashed-box */
  background: lightgreen;
  width: 20%;
  left: 65%;
  top: -25px;
  height: 7em;
  opacity: 0.9;
}
```





