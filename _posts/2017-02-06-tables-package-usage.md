---
layout: post
title:  生产复杂表格的R软件包：tables
date: "2017-02-06 17:16:02"
published: true
tags: [R package, Data manipulation]
---



数据的输入输出一直是数据处理比较重要的问题。我们常见的数据表格的表头设计一般都比较复杂，存在着表头套表头的现象，而这种表格的结构对于一个数据分析软件来说是没法理解的。数据软件所能理解的表格是那种标准化的表格，也就是第一行是字段名（也就是变量名），从第二行到表格结束就是具体的内容（也就是变量的取值，或者叫实例）。这样除去第一行后，每一行表示一个实例（统计上叫一个观测，或者某个对象的属性），每一列表示一个变量的取值。如我们经常用到的`iris`数据表（为了简便，随机从每个Species类别中选取了1个数据）：


| Sepal.Length| Sepal.Width| Petal.Length| Petal.Width|Species    |
|------------:|-----------:|------------:|-----------:|:----------|
|          5.8|         4.0|          1.2|         0.2|setosa     |
|          5.5|         2.5|          4.0|         1.3|versicolor |
|          6.9|         3.2|          5.7|         2.3|virginica  |

实际上，在R中我们一般以`data.frame`的形式储存数据，实际上所面对的就是上面这种数据表格。这种数据表格对于编程人员来说很实用很方便，但对于一般人而言，就会觉得很不直观，比如我们将上面表格中的`iris`数据等价地变换一下，就会发现这样的问题：


|Species    |Type  |Shape  | Value|
|:----------|:-----|:------|-----:|
|setosa     |Sepal |Length |   5.8|
|versicolor |Sepal |Length |   5.5|
|virginica  |Sepal |Length |   6.9|
|setosa     |Sepal |Width  |   4.0|
|versicolor |Sepal |Width  |   2.5|
|virginica  |Sepal |Width  |   3.2|
|setosa     |Petal |Length |   1.2|
|versicolor |Petal |Length |   4.0|
|virginica  |Petal |Length |   5.7|
|setosa     |Petal |Width  |   0.2|
|versicolor |Petal |Width  |   1.3|
|virginica  |Petal |Width  |   2.3|

<!-- more -->

1. 表格很长；
1. 表格中重复的信息很多。

因此我们很难对数据有个直观的理解和认识，也不便于数据的比较。相反，如果我们把上面的数据变成下面这个样子，可能就容易理解很多了:

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>&nbsp;</th>
  <th>&nbsp;</th>
  <th colspan="3">Species</th>
</tr>
 <tr class="center">
  <th>Type</th>
  <th>Shape</th>
  <th>setosa</th>
  <th>versicolor</th>
  <th>virginica</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">Petal</th>
  <th class="left">Length</th>
  <td>1.2</td>
  <td>4.0</td>
  <td>5.7</td>
</tr>
 <tr class="center">
  <th class="left">&nbsp;</th>
  <th class="left">Width</th>
  <td>0.2</td>
  <td>1.3</td>
  <td>2.3</td>
</tr>
 <tr class="center">
  <th class="left">Sepal</th>
  <th class="left">Length</th>
  <td>5.8</td>
  <td>5.5</td>
  <td>6.9</td>
</tr>
 <tr class="center">
  <th class="left">&nbsp;</th>
  <th class="left">Width</th>
  <td>4.0</td>
  <td>2.5</td>
  <td>3.2</td>
</tr>
</tbody>
</table>

上面这种表格的表头是复合式的表头，生成的表格也是“交叉式”的表格，而非前面“清单式”的表格。在R中，将“清单式”的表格转换成“交叉式”的表格，所用到的工具是Duncan Murdoch开发的软件包`tables`。在这个软件包的描述中，他写到

> Computes and displays complex tables of summary statistics.
  Output may be in LaTeX, HTML, plain text, or an R
  matrix for further processing.

也就是说，`tables`这个软件包的作用有两个：

1. 汇总数据；
1. 输出复杂“交叉式”表格。

那么，上面的复杂“交叉式”表格是如何通过代码实现的呢？其实只用到了一个函数，那就是`tabular`这个函数：


{% highlight r %}
tabular(Factor(Type) * Factor(Shape) ~
          Species * Heading() * Value * Heading() * mean,
        data = d1)
{% endhighlight %}

从上面的代码中可以看出，`tabular`这个函数最主要的操作手段是一个`formula`，这个`formula`用`~`将两个部分连接起来，其中`~`左边表示的是“交叉式”表格的“行表头”（即所生成的表格左边标蓝绿底白字的那2列），其右边表示的是“列表头”（即所生成的表格上边标蓝绿底白字的那2行）。下面用另外一个例子来说明`formula`如何编写：


{% highlight r %}
tabular((Species + 1) ~ (n = 1) + Format(digits = 2) *
          (Sepal.Length + Sepal.Width) * (mean + sd),
          data = iris)
{% endhighlight %}

这个代码生成的表格为：

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>&nbsp;</th>
  <th>&nbsp;</th>
  <th colspan="2">Sepal.Length</th>
  <th colspan="2">Sepal.Width</th>
</tr>
 <tr class="center">
  <th>Species</th>
  <th>n</th>
  <th>mean</th>
  <th>sd</th>
  <th>mean</th>
  <th>sd</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">setosa</th>
  <td> 50</td>
  <td>5.01</td>
  <td>0.35</td>
  <td>3.43</td>
  <td>0.38</td>
</tr>
 <tr class="center">
  <th class="left">versicolor</th>
  <td> 50</td>
  <td>5.94</td>
  <td>0.52</td>
  <td>2.77</td>
  <td>0.31</td>
</tr>
 <tr class="center">
  <th class="left">virginica</th>
  <td> 50</td>
  <td>6.59</td>
  <td>0.64</td>
  <td>2.97</td>
  <td>0.32</td>
</tr>
 <tr class="center">
  <th class="left">All</th>
  <td>150</td>
  <td>5.84</td>
  <td>0.83</td>
  <td>3.06</td>
  <td>0.44</td>
</tr>
</tbody>
</table>

在`formula`中，除`~`外还有3个重要的符号：`+`、`*`和`=`。简单地说，`+`所起的作用就是连接作用：在`~`左边，变量用`+`相连就表示增加这些变量所代表的行，“+1”就表示增加一行汇总“All”；在`~`右边，变量用`+`相连就表示增加这些变量所代表的列，“+1”就表示增加一行汇总“All”。`~`左右两边不能同时为1（即“1~1”），因为这个表是空表无意义。

用下面的例子来说明`+`的用法。


{% highlight r %}
set.seed(123)
X <- runif(10)
A <- sample(letters[1:2], 10, rep=TRUE)
B <- factor(A)
{% endhighlight %}

- `+`在`~`左边


{% highlight r %}
tabular(A + B ~ 1)
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th></th>
  <th></th>
  <th>All</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left"></th>
  <th class="left">A</th>
  <td>10</td>
</tr>
 <tr class="center">
  <th class="left">B</th>
  <th class="left">a</th>
  <td> 5</td>
</tr>
 <tr class="center">
  <th class="left"></th>
  <th class="left">b</th>
  <td> 5</td>
</tr>
</tbody>
</table>

- `+`在`~`右边


{% highlight r %}
tabular(1 ~ A + B)
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>&nbsp;</th>
  <th>&nbsp;</th>
  <th colspan="2">B</th>
</tr>
 <tr class="center">
  <th></th>
  <th>A</th>
  <th>a</th>
  <th>b</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">All</th>
  <td>10</td>
  <td>5</td>
  <td>5</td>
</tr>
</tbody>
</table>

这里`A`和`B`其实数值上是一样的，只是数据类型不一样：`A`表示的是字符型（`character`）而`B`表示的是因子型（`factor`）。在进行数据汇总的时候，字符型被看成一个整体不被展开，而因子型则会按照因子进行展开统计，如果需要对字符型的数据进行展开统计，则需要用`Factor`函数将其转化为因子，如前面例子中的`Factor(Type)`和`Factor(Shape)`。

`*`所起的作用就是交叉作用，相当于几个条件同时满足。这里也分两种情况：`*`连接两个因子型变量，那么生成的就是这两个因子型变量所有因子组合的情况；`*`连接一个因子型变量和一个数值型变量，那么生成的就是将数值型变量按照因子型变量所有因子进行展开统计，所以另外还需要增加一个统计方法的函数，比如`mean`和`sd`。

- `*`连接两个因子型变量


{% highlight r %}
tabular(Factor(A) * B ~ 1)
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>A</th>
  <th>B</th>
  <th>All</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">a</th>
  <th class="left">a</th>
  <td>5</td>
</tr>
 <tr class="center">
  <th class="left">&nbsp;</th>
  <th class="left">b</th>
  <td>0</td>
</tr>
 <tr class="center">
  <th class="left">b</th>
  <th class="left">a</th>
  <td>0</td>
</tr>
 <tr class="center">
  <th class="left">&nbsp;</th>
  <th class="left">b</th>
  <td>5</td>
</tr>
</tbody>
</table>

- `*`连接一个因子型变量和一个数值型变量


{% highlight r %}
tabular(1 ~ B * X * mean)
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>&nbsp;</th>
  <th colspan="2">B</th>
</tr>
 <tr class="center">
  <th>&nbsp;</th>
  <th>a</th>
  <th>b</th>
</tr>
 <tr class="center">
  <th>&nbsp;</th>
  <th>X</th>
  <th>X</th>
</tr>
 <tr class="center">
  <th></th>
  <th>mean</th>
  <th>mean</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">All</th>
  <td>0.7401</td>
  <td>0.4163</td>
</tr>
</tbody>
</table>

这里需要说明的是`+`和`*`都满足交换律，也就是说在一个表达式里，这两个连接符连接的变量的顺序发生变化时不会导致报错，但是会改变变量在表格中出现的顺序。另外`*`优先级高于`+`且还满足分配律，即
`A * (B + C)`等效于`A * B + A * C`。

`=`就比较简单了，用来指定某一列的名称，需要与`()`连用。例如：


{% highlight r %}
tabular((n = 1) ~ B)
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>&nbsp;</th>
  <th colspan="2">B</th>
</tr>
 <tr class="center">
  <th></th>
  <th>a</th>
  <th>b</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">n</th>
  <td>5</td>
  <td>5</td>
</tr>
</tbody>
</table>

就将“All”变成了“n”。

另外，如果要去掉对表格和数据进行修饰，可以在相关变量前面加上起修饰作用的函数，比如`Heading`和`Format`等。`Heading`可以去掉某个不需要的名称，而`Format`可以对数据进行小数保留有效数字设置。比如：


{% highlight r %}
tabular(B + 1 ~
          Format(digit = 2) * Heading() *
          X * (X_mean = mean))
{% endhighlight %}

<style>
.Rtable thead, .Rtable .even {
  background-color: inherit;
}
.left   { text-align:left; }
.center { text-align:center; }
.right  { text-align:right; }
.Rtable, .Rtable thead { 
  border-collapse: collapse;
  border-style: solid;
  border-width: medium 0;
  border-color: inherit;
}
.Rtable th, .Rtable td {
  padding-left: 0.5em;
  padding-right: 0.5em;
  border-width: 0;
}
</style>
<table class="Rtable">
<thead>
<tr class="center">
  <th>B</th>
  <th>X_mean</th>
</tr>
</thead>
<tbody>
<tr class="center">
  <th class="left">a</th>
  <td>0.74</td>
</tr>
 <tr class="center">
  <th class="left">b</th>
  <td>0.42</td>
</tr>
 <tr class="center">
  <th class="left">All</th>
  <td>0.58</td>
</tr>
</tbody>
</table>

这样，计算的结果只保留了2为有效数字，并且`X`被隐藏了，`mean`重命名为`X_mean`。

掌握了以上用法基本就能够处理常规的复杂“交叉式”表格的生成问题，更多函数和用法可以查看这个软件包的帮助文件或者“Vignettes”。




