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
