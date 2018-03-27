# css_study
Mainly for CSS develupment and study

## 前端布局基础概述

Reference by 蔡剑涛
https://mp.weixin.qq.com/s/-LcNZWFFty2lWuND6uuNNA

前端布局方案主要有三种:
* 传统布局方案（借助浮动、定位等手段）
* flex布局方案
* grid布局方案

传统布局方案，需要使用者熟练掌握元素的分类及布局特性、浮动原理和定位原理等众多基础知识，方能在解决各类前端布局问题时游刃有余，这不仅学习成本大，而且实现的复杂度也高，实现的CSS代码也不够精简、优雅。但由于其基础知识来源于CSS2，所以浏览器兼容性最好，对于用户是友好的。

flex布局方案，正是为了解决传统布局方案的种种不便，而提出的一种新型改进方案，它不再需要借助浮动和定位等布局手段，而是通过父元素（flex box）单方面配置相关的CSS属性来决定子元素的布局规则，且在大多情况下无需子元素（flex item）参与，就能完成子元素间的布局问题，不仅学习成本低（公司之前有几个后端工程师亦能快速上手），且大大简化了布局的实现复杂度，CSS代码也更加精炼。美中不足的是IE10才开始支持，且需要使用-ms-前缀（IE11无需）。

grid布局方案，是由微软提出，相对于传统布局方案和flex布局方案，它是一种二维布局方案，在IE10开始支持，但需要使用-ms-后缀（IE11+不再需要）。

### 1. 量化布局方案的合理性

布局变化主要分为两大类：一是**布局需求**的变化，二是**运行环境**的变化。

对于**布局需求**的变化，可以做到:
* 方便快速定位需修改的位置
* 能够不花或用最少的修改成本应对变化

对于**运行环境**的变化，可以做到:
* 在不同浏览器均有正确或良好的显示

结论: 方案在满足正确性的前提下，其实现逻辑规范、实现职责分明且拥有良好的浏览器兼容性。

### 2. 布局基础要点

2.1 CSS标准盒模型(或W3C盒模型)

一个web页面是由众多html元素拼凑而成的，而每一个html元素，都被解析为一个矩形盒，而CSS盒模型就是这种矩形盒的解构模型。CSS盒模型，它由内到外、被四条边界:
* Content edge
* Padding edge
* Border edge
* Margin edge

划分为四个区域:
* Content area
* Padding area
* Border area
* Margin area
在形状上，Content area（又称content-box）是实心矩形，其余是空心环形（空心部分是Content area）。

此外，每个区域都有其特定的作用：Content area，是当前元素用来容纳所有子孙元素；Padding area，是当前元素用来隔离自身和子孙元素；Border area是当前元素用来显示自身的轮廓；Margin area，是当前元素用来隔离自身和相邻元素。理解每个区域的作用和职责至关重要，有助于我们写出优雅、清晰的布局代码。

2.2 box-sizing（CSS3属性）

2.2.1 box-sizing的作用

box-sizing，顾名思义，其作用与设置CSS box的尺寸大小有关，而CSS box又可细分为:
* content-box(即content area)
* padding-box(=content area + padding area)
* border-box(=content area + padding area + border area)
* margin-box(=content area + padding area + border area + margin area)

简单来说，box-sizing的作用就是告诉浏览器：CSS属性width和height是用于设置哪一种box的尺寸。

在W3C标准中,box-sizing的值仅有content-box和border-box（firefox则额外支持padding-box）。
所以，当box-sizing的值为content-box（默认值）时，有：
width = content-width;
height = content-height;
当box-sizing的值为border-box时，有：
width = content-width + padding-left + padding-right + border-left-width + border-right-width;
height = content-height + padding-top + padding-bottom + border-top-height + border-bottom-height;

2.2.2 box-sizing的浏览器兼容性

box-sizing是CSS3属性，在IE8+（包含IE8）开始支持，然而在IE8，box-sizing的值为border-box时，不能与min-width, max-width, min-height或max-height的一起使用，因为IE8对min-* 和 max-* 的解析，仍是作用于content-box，不受box-sizing属性控制。

2.2.3 box-sizing的产生原因

在CSS的发展历程中，有两个版本，一个是**IE盒模型**，另外一个是**W3C盒模型**。
ie盒模型中，width代表的是border-box。而W3C盒模型则代表content-box。

在了解了CSS盒模型的发展历程，以及后来新增的box-sizing的开始支持时间，我们不难发现：
* IE5-采用IE盒模型
* IE6、7的标准模式放弃了IE盒模型，转为使用W3C盒模型
* IE8+借助box-sizing，又重新提供了对IE盒模型的支持

于是乎，为了重新在新规范中支持IE盒模型，也为了向后兼容W3C盒模型，W3C组织在CSS3中添加了box-sizing属性，用于切换这两种盒模型。

2.2.4 box-sizing的最佳实践

* 最低需要兼容IE6、7: 那么box-sizing不可使用，只能使用W3C盒模型
* 最低只需兼容IE8: content-box —— 一些弹性布局和响应式布局实现上，会稍微麻烦一点。 border-box —— 不能和IE8的min-width、min-height、max-width和max-height四个属性一同使用。
* 只需兼容IE9: 全局配置取content-box更为合适，局部配置二者均可。

总之，大部分场景二者可以互换，只是使用理念不一样。小部分场景border-box更具优势，但随着calc函数的支持，这种优势已经不再，相反content-box是默认值的优势愈加明显。

2.3 元素的分类及其布局特性

从元素的布局特性来分，主要可以分为三类元素
* block-level（块级）元素
  display属性取block、table、flex、grid和list-item等值的独占一行显示的元素。
* inline-level（行内级）元素
  display属性取inline值的可在同一行内排列显示的元素。
* inline-block-level（行内块级）
  display属性取inline-block、inline-table、inline-flex和inline-grid 等值的兼具块级元素和行内级元素布局特性的元素。
  
 2.6 基本原理
 
 2.6.1 文档流（正常流），是页面元素默认存放的“容器”。
 
 文档流具有如下特性：
 * 文档流按照页面元素书写的顺序，将页面元素按从左到右，从上至下的一般顺序进行排列，而页面元素则根据自身的布局属性(block-box or inline-box)，决定是行内显示，还是换行显示。
 * 文档流内的元素，相互尊重：有序排列，彼此识别；
 
 2.6.2 脱离文档流
 
 元素脱离了默认存放的容器，换到另外一个容器存放。一个元素脱离了文档流，这样会导致：其父元素无法识别其，其也不参与父元素高度的计算。
 方法有:
 * 将元素设置为浮动元素
 * 将元素设置为absolute、fixed元素
 
 
 
