# 第十九章\. CSS 简介

使用层叠样式表（CSS），你可以对网页应用样式，使其看起来完全符合你的期望。这是因为 CSS 与文档对象模型（DOM）相关联，我在第十四章中有所解释。

借助 CSS 及其与 DOM 的集成，你可以快速轻松地重新设计任何元素。例如，如果你不喜欢`<h1>`、`<h2>`和其他标题标签的默认外观，可以分配新的样式以覆盖用于设置字体系列和大小，以及是否应设置粗体或斜体等许多其他属性的默认设置。

你可以通过在页面头部的`<head>`和`</head>`标签之间插入所需的语句来为网页添加样式。所以，要更改`<h1>`标签的样式，你可以使用以下代码（稍后我将解释语法）：

```php
<style>
  h1 { color:red; font-size:3em; font-family:Arial; }
</style>
```

在 HTML 页面中，这可能看起来像示例 19-1（见图 19-1），与本章中的所有示例一样，使用标准的 HTML5`DOCTYPE`声明。

##### 示例 19-1\. 一个简单的 HTML 页面

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Hello World</title>
    <style>
      h1 { color:red; font-size:3em; font-family:Arial; }
    </style>
  </head>
  <body>
    <h1>Hello there</h1>
  </body>
</html>
```

![在插图中显示原始样式的标签样式](img/pmj6_1901.png)

###### 图 19-1\. 在插图中显示原始样式的标签样式

# 导入样式表

当你希望为整个站点设置样式，而不仅仅是单个页面时，更好的管理样式表的方法是将它们完全移出网页并放入单独的文件中，然后导入你需要的样式表。这样可以让你为不同的布局（如网页和打印）应用不同的样式表，而不必更改 HTML。

有几种不同的方法可以实现这一点。首先是使用 CSS 的`@import`指令，就像这样：

```php
<style>
  @import url('styles.css');
</style>
```

这个语句告诉浏览器获取一个名为*styles.css*的样式表。`@import`命令非常灵活，因为你可以将它放在一个样式表中，所以样式表可以引入其他样式表，依此类推。只要确保你的任何外部样式表中没有`<style>`或`</style>`标签，否则它们将不起作用。

## 从 HTML 中导入 CSS

你也可以使用 HTML 的`<link>`标签包含样式表，就像这样：

```php
<link rel='stylesheet' href='styles.css'>
```

这与`@import`指令具有完全相同的效果，唯一的区别是`<link>`是一个仅限于 HTML 的标签，不是有效的样式指令，因此无法从一个样式表中使用它来引入另一个样式表，也无法放置在一对`<style>...</style>`标签内。

就像你可以在 CSS 中使用多个`@import`指令来包含多个外部样式表一样，你也可以在 HTML 中使用尽可能多的`<link>`元素。

## 嵌入式样式设置

当然，你也可以在当前页面的 HTML 中直接插入样式声明来逐个设置或覆盖某些样式，例如这样（结果是标签内部的斜体蓝色文本）：

```php
<div style='font-style:italic; color:blue;'>Hello there</div>
```

但这应该仅在非常特殊的情况下使用，因为它打破了内容和表现的分离，因此维护起来非常麻烦。

# 使用 ID

在 HTML 中，更好的方法是为元素分配一个 ID，就像这样：

```php
<div id='welcome'>Hello there</div>
```

这表示具有 ID 为`welcome`的`<div>`元素的内容应用在定义为`welcome`样式设置中的样式上。这个匹配的 CSS 语句可能如下所示：

```php
#welcome { font-style:italic; color:blue; }
```

###### 注意

注意使用`#`符号，指定只有名为`welcome`的 ID 的元素才会应用此样式。

# 使用类

`id`元素的值在网页中必须是唯一的，因为这是它作为标识符的依据。如果你想将相同的样式应用于多个元素，你不必为每个元素分配不同的 ID，因为你可以指定一个类来管理它们，就像这样：

```php
<div class='welcome'>Hello</div>
```

这表示这个元素的内容（以及使用该类的任何其他元素）应用了在`welcome`类中定义的样式。一旦应用了类，你可以使用以下规则，无论是在页面头部还是在外部样式表中，来设置类的样式：

```php
.welcome { font-style:italic; color:blue; }
```

而不是`#`符号，该符号保留用于 ID，类的语句则以`.`（句点）开头。

# 使用分号

在 CSS 中，分号用于分隔同一行上的多个 CSS 语句。但是，如果规则中只有一个语句（或者在 HTML 标签内的内联样式设置中），则可以省略分号，就像在一组中的最后一个语句那样。

然而，为了避免难以发现的 CSS 错误，你可能更喜欢在每个 CSS 设置后始终使用分号。这样，你可以复制和粘贴它们，并且可以修改属性，而不必担心在不必要的地方删除分号或者在必要的地方添加分号。

# CSS 规则

CSS 规则中的每个语句都以*选择器*开始，这是将应用规则的项目。例如，在这个赋值中，`h1`是被赋予比默认字体大小大 240%的选择器：

```php
h1 { font-size:240%; }
```

`font-size`是一个*属性*。为选择器的`font-size`属性提供`240%`的值确保了所有`<h1>...</h1>`标签对的内容将以比默认大小大 240%的字体大小显示。所有规则中的更改都必须在跟随选择器的`{`和`}`符号内进行。在`font-size:240%;`中，冒号`:`前面的部分是属性，而其余部分是应用于它的值。

最后，使用一个`;`（分号）来结束语句。在这种情况下，因为`font-size`是规则中的最后一个属性，所以分号是不必要的（但是如果后面还有其他赋值，则是必需的）。

## 多重赋值

可以通过几种不同的方式创建多个样式声明。首先，可以将它们连接在同一行上，就像这样：

```php
h1 { font-size:240%; color:blue; }
```

这添加了第二个分配，将所有`<h1>`标题的颜色更改为蓝色。您还可以像下面这样每行放置一个分配：

```php
h1 { font-size:240%;
color:blue; }
```

或者您可以稍微分开分配，使它们在冒号下面彼此对齐成列，像这样，这可能是现在首选的方法：

```php
h1 {
  font-size:240%;
  color    :blue;
}
```

这样，您可以轻松地看到每个新规则集从哪里开始，因为选择器总是位于第一列，后面跟随的分配都整齐地对齐，所有属性值从相同的水平偏移开始。在上述示例中，最后的分号是不必要的，但是如果您想把这些语句组合成一行，只需将所有分号放在正确的位置即可。

您可以指定相同的选择器任意次数，CSS 将合并所有属性。因此，前面的示例也可以指定如下：

```php
h1 { font-size: 240%; }
h1 { color    : blue; }
```

###### 注意

在布局 CSS 时没有一种正确或错误的方式，但我建议您至少尝试使每个 CSS 块在视觉布局上保持一致，以便其他人能够一目了然。

如果您指定了相同的选择器和属性两次会怎么样？

```php
h1 { color : red; }
h1 { color : blue; }
```

指定相同选择器的相同属性，最后指定的值将生效——在这种情况下是蓝色。在单个文件中，对同一选择器重复相同属性是没有意义的，但在现实的网页中，当应用多个样式表时经常发生这种重复。这是 CSS 的一个宝贵特性，也是“层叠”一词的来源之一。

## 使用注释

对您的 CSS 规则进行注释是一个好主意，即使只描述主要的语句组，而不是所有或大多数语句。可以通过在`/*...*/`标签对之间放置注释来实现，如下所示：

```php
/* This is a CSS comment */
```

您可以扩展注释到多行，如下所示：

```php
/*
 A multi-
 line
 comment
*/
```

###### 注意

使用多行注释时，请注意不能在其中嵌套单行（或其他任何）注释。这样做可能会导致不可预测的错误。

# 样式类型

样式类型有多种，从浏览器设置的默认样式（以及您可能在浏览器中应用的用户样式，用于覆盖其默认样式），到内联或嵌入式样式，再到外部样式表。每种类型定义的样式具有从低到高的优先级层次。

我们将更详细地讨论层叠样式表中的“层叠”部分，详细解释见“CSS 层叠”。但在深入细节之前，先简要介绍一下会有所帮助。

## 默认样式

网页浏览器应用的样式优先级最低。这些样式是作为备用样式创建的，用于在网页没有任何样式时使用，旨在提供一组通用的样式，以在大多数情况下显示得相当不错。

在 CSS 出现之前，这些是文档中唯一的样式，并且只有少数样式可以由网页更改（如字体、颜色和一些元素大小的参数）。

## 用户样式

用户样式具有下一个最高的优先级。它们被大多数现代浏览器支持，但每个浏览器的实现方式都有所不同，因此如今为你创建自己喜欢的浏览样式最简单的方式是使用诸如 [Stylish](http://userstyles.org) 的插件。

如果你想为浏览创建自己的默认样式，Stylish 是一个方便的工具。只需搜索“stylish 扩展”来在你的浏览器中安装它，如 图 19-2 所示。

![](img/pmj6_1902.png)

###### 图 19-2\. Stylish 是一种你可以根据自己的喜好为网页添加样式的方式

如果已分配了用户样式，并且已定义为浏览器默认值，则它将覆盖浏览器的默认设置。任何未在用户样式表中定义的样式将保留它们在浏览器中设置的默认值。

## 外部样式表

下一种类型的样式是外部样式表中定义的。这些设置会覆盖用户或浏览器分配的任何样式。外部样式表是创建你的样式的推荐方式，因为你可以为不同的用途创建不同的样式表，比如通用的网页使用、在较小屏幕上的移动浏览器查看、打印等等。当你创建网页时，只需为每种媒体应用需要的样式表即可。

## 内部样式

接下来是内部样式，你可以在 `<style>...</style>` 标签内创建，并且优先于之前所有的样式类型。不过，这时候你开始打破样式与内容之间的分离，因为同时加载的任何外部样式表的优先级会更低。

## 内联样式

最后，内联样式是直接将属性分配给元素的方式。它们拥有任何样式类型中的最高优先级，并且像这样使用：

```php
<a href="http://google.com" style="color:green;">Visit Google</a>
```

在这个例子中，指定的链接将显示为绿色，而不管任何默认或其他样式表应用的颜色设置，无论是直接应用于此链接还是通用应用于所有链接。

###### 注意

当你使用这种类型的样式时，你正在打破布局与内容之间的分离；因此，建议仅在有很好的理由时这样做。

# CSS 选择器

访问一个或多个元素的方法称为*选择*，执行此操作的 CSS 规则的一部分称为*选择器*。正如你所预期的那样，选择器有多种变体。

## 类型选择器

类型选择器用于 HTML 元素类型，如 `<p>` 或 `<i>`。例如，以下规则将确保所有 `<p>...</p>` 标签内的文本完全右对齐：

```php
p { text-align:justify; }
```

## 后代选择器

后代选择器允许你将样式应用到包含在其他元素内的元素上。例如，以下规则将所有`<p>...</p>`标签中的`<b>...</b>`标签内的文本设为红色，但前提是这些标签出现在`<p>...</p>`标签内（像这样：`<p><b>Hello</b> there</p>`）：

```php
p b { color:red; }
```

后代选择器可以无限嵌套，所以下面的规则也是有效的，它将无序列表中列表元素内的粗体文本设为蓝色：

```php
ul li b { color:blue; }
```

作为一个实际的例子，假设你想要在嵌套在另一个有序列表中的有序列表中使用不同的编号系统。你可以通过以下方式实现，它将用小写字母（从`a`开始）替换默认的数字编号（从`1`开始）：

```php
<!DOCTYPE html>
<html>
  <head>
    <style>
      `ol` `ol` `{` `list-style-type``:``lower-alpha``;` `}`
    </style>
  </head>
  <body>
    <ol>
      <li>One</li>
      <li>Two</li>
      <li>Three <ol>
          <li>One</li>
          <li>Two</li>
          <li>Three</li>
        </ol>
      </li>
    </ol>
  </body>
</html>
```

将这段 HTML 加载到网络浏览器中的结果如下，你可以看到第二个元素列表显示不同：

```php
1\. One
2\. Two
3\. Three
  a. One
  b. Two
  c. Three
```

## 子选择器

子选择器类似于后代选择器，但在应用样式时更为严格，仅选择直接是另一个元素的子元素的那些元素。例如，以下代码使用后代选择器，将段落内的任何粗体文本都改为红色，即使粗体文本本身位于斜体文本部分中（像这样`<p><i><b>Hello</b> there</i></p>`）：

```php
p b { color:red; }
```

在这种情况下，单词`Hello`显示为红色。然而，当不需要这种更一般的行为时，可以使用子选择器来缩小选择器的范围。例如，以下规则插入了一个大于号（`>`）以创建子选择器，仅当元素是段落的直接子元素且不位于另一个元素内时，将粗体文本设为红色：

```php
p > b { color:red; }
```

现在`Hello`不会改变颜色，因为`<b>`不是`<p>`的直接子元素。

举个实际的例子，假设你希望仅将直接位于`<ol>`元素下的`<li>`元素加粗。你可以按照以下方法实现，其中直接位于`<ul>`元素下的`<li>`元素不会加粗：

```php
<!DOCTYPE html>
<html>
  <head>
    <style>
      `ol` `>` `li` `{` `font-weight``:``bold``;` `}`
    </style>
  </head>
  <body>
    <ol>
      <li>One</li>
      <li>Two</li>
      <li>Three</li>
    </ol>
    <ul>
      <li>One</li>
      <li>Two</li>
      <li>Three</li>
    </ul>
  </body>
</html>
```

将这段 HTML 加载到浏览器中的结果将如下所示：

```php
1\. One
2\. Two
3\. Three

 • One
 • Two
 • Three
```

## ID 选择器

如果给一个元素赋予 ID 名称（像这样`<div id='mydiv'>`），你可以通过以下方式直接从 CSS 中访问它，将元素内所有文本改为斜体：

```php
#mydiv { font-style:italic; }
```

每个 ID 在文档中只能使用一次，因此只会将第一个找到的出现位置按照 CSS 规则的新属性值进行更改。但在 CSS 中，可以直接引用具有相同名称的任何 ID，只要它们出现在不同的元素类型中，像这样：

```php
<div id='myid'>Hello</div> <span id='myid'>Hello</span>
```

因为 ID 通常仅适用于唯一的元素，所以下面的规则将仅为第一个出现的`myid`应用下划线：

```php
#myid { text-decoration:underline; }
```

然而，你可以确保 CSS 将规则应用于两者的方法如下：

```php
span#myid { text-decoration:underline; }
div#myid  { text-decoration:underline; }
```

或者更简洁地写成这样（参见“通过组选择”）：

```php
span#myid, div#myid { text-decoration:underline; }
```

###### 注意

我不建议使用这种选择形式，因为它增加了对 JavaScript 使用的障碍。任何还必须访问这些元素的 JavaScript 也不能轻松地这样做，因为常用的`getElementById`函数将只返回第一个出现的元素。要引用任何其他实例，程序必须搜索文档中所有元素的整个列表——这是一个更棘手的任务。因此，通常最好始终使用唯一的 ID 名称。

## 类选择器

当您希望在页面中的多个元素之间共享相同的样式时，可以为它们分配相同的类名（如此：`<span class='myclass'>`）。然后创建一个单一规则一次性修改所有这些元素，如以下规则，为使用该类的所有元素创建一个 10 像素的左边距偏移量：

```php
.myclass { margin-left:10px; }
```

在现代浏览器中，您可以让 HTML 元素使用多个类，类名之间用空格分隔，例如：`<span class='class1 class2 class3'>`。

您可以通过指定应用该类的元素类型来缩小类的作用范围。例如，以下规则仅适用于使用类`main`的段落：

```php
p.main { text-indent:30px; }
```

在这个示例中，只有使用类`main`（如此：`<p class="main">`）的段落会接收新的属性值。任何其他尝试使用该类的元素类型（例如`<div class="main">`）都不会受到这个规则的影响。

## 属性选择器

许多 HTML 标签支持属性，使用这种类型的选择器可以避免使用 ID 和类来引用它们。例如，您可以直接以以下方式引用属性，这样可以将所有具有`type="submit"`属性的元素设置为 100 像素的宽度：

```php
[type="submit"] { width:100px; }
```

如果您希望将选择器的范围缩小到仅限于带有该属性类型的`<form>`输入元素，则可以改用以下规则：

```php
form input[type="submit"] { width:100px; }
```

###### 注意

属性选择器也适用于 ID 和类，因此，例如，`[class~="classname"]`的工作方式与类选择器`.classname`完全相同（只是前者具有更高的优先级）。同样，`[id="idname"]`等同于使用 ID 选择器`#idname`。因此，带有`#`和`.`前缀的类和 ID 选择器可以视为带有更高优先级的属性选择器的速记。`~=`运算符匹配属性，即使它是一组以空格分隔的属性之一。

## 通用选择器

通配符`*`或通用选择器匹配任何元素，因此以下规则将通过给所有元素添加绿色边框来彻底弄乱文档：

```php
* { border:1px solid green; }
```

因此，很少会单独使用`*`，但作为复合规则的一部分，它可以非常强大。例如，以下规则将适用与前述规则相同的样式，但仅适用于作为具有 ID `boxout`的元素的子元素的所有段落，并且只要它们不是直接子元素：

```php
#boxout * p {border:1px solid green; }
```

让我们看看这里发生了什么。在`#boxout`后面的第一个选择器是一个`*`符号，因此它指的是`boxout`对象中的任何元素。然后，以下`p`选择器通过将选择器更改为仅适用于由`*`选择器返回的元素的段落（由`p`定义）来缩小选择焦点。因此，此 CSS 规则执行以下操作（其中我可以互换使用术语*对象*和*元素*）：

1.  找到具有 ID `boxout` 的对象。

1.  找到第 1 步返回的对象的所有子元素。

1.  在第 2 步返回的对象中找到所有 `p` 子元素，并且由于这是组中的最终选择器，还要找到第 2 步返回的对象的所有 `p` 子及子子元素（等等）。

1.  将 `{` 和 `}` 字符之间的样式应用于第 3 步返回的对象。

这样做的结果是，绿色边框仅应用于主元素的子子代（或曾孙代等等）中的段落。

## 通过组选择

使用 CSS，您可以通过逗号分隔选择器一次性将规则应用于多个元素、类或任何其他类型的选择器。例如，以下规则将在所有段落、具有 ID `idname` 的元素以及使用类 `classname` 的所有元素下方放置一个虚线橙色线条：

```php
p, #idname, .classname { border-bottom:1px dotted orange; }
```

图 19-3 显示了使用的各种选择器及其附带的规则。

![一些 HTML 及其使用的 CSS 规则](img/pmj6_1903.png)

###### 图 19-3. 一些 HTML 及其使用的 CSS 规则

# CSS 层叠

如前所述，CSS 属性中最基本的之一就是它们会层叠 — 因此称为层叠样式表。但这意味着什么呢？

层叠是一种用于解决浏览器支持的各种样式表之间潜在冲突并按照创建者、创建样式的方法和所选属性类型优先顺序应用它们的方法。

## 样式表创建者

所有现代浏览器都支持三种主要类型的样式表。按优先级从高到低的顺序，它们如下：

1.  由文档作者创建的样式

1.  由用户创建的样式

1.  由浏览器创建的样式

这三组样式表按相反顺序处理。首先，将默认值应用于文档中的网络浏览器。如果没有这些默认值，不使用样式表的网页将看起来很糟糕。它们包括字体、大小和颜色；元素间距；表边框和间距；以及用户期望的所有其他合理标准。

接下来，如果用户已创建任何用于替代标准样式的样式，则会应用这些样式，取代可能冲突的浏览器默认样式。

最后，将应用由当前文档作者创建的任何样式，以替换已由浏览器默认或用户创建的样式。

## 样式表方法

样式表可以通过三种不同方法创建。从高到低的优先顺序如下：

1.  作为内联样式

1.  在嵌入式样式表中

1.  在外部样式表中

同样，样式表创建的这些方法按照优先顺序的相反顺序应用。因此，首先处理所有外部样式表，并将其样式应用于文档。

其次，处理任何嵌入样式（在 `<style>...</style>` 标签内），并优先处理与外部规则冲突的样式，并将其覆盖。

最后，将直接应用于元素的任何样式（例如 `<div style="...">...</div>`）作为内联样式具有最高优先级，并覆盖所有先前分配的属性。

## 样式表选择器

有三种不同的选择元素进行样式设置的方法。按照优先顺序从高到低，它们如下：

1.  按单个 ID 或属性选择器引用

1.  按类分组引用

1.  按元素标签引用（例如 `<p>` 或 `<b>`）

根据规则影响的元素数量和类型处理选择器，这与解决冲突的前两种方法略有不同。这是因为规则不必同时仅适用于一种选择器类型，并且可能引用多种不同的选择器。

因此，我们需要一种方法来确定可以包含任意选择器组合的规则的优先顺序。CSS 通过计算每个规则的特异性来实现这一点，按照其作用范围的从宽到窄的顺序对其进行排序。

### 计算特异性

我们通过基于前述编号列表中的选择器类型创建三部分数字来计算规则的特异性。这些复合数字起始形式为`[0,0,0]`。在处理规则时，每个引用 ID 的选择器会使第一个数字增加 1，因此复合数字会变为`[1,0,0]`。

让我们看一下以下规则，它引用了七个选择器。其中三个是对 ID `#heading`、`#main` 和 `#menu` 的引用，因此复合数字变为`[3,0,0]`：

```php
#heading #main #menu .text .quote p span {
  // Rules go here;
}
```

然后，选择器中类的数量放置在复合数字的第二部分。在此示例中，有两个类 (`.text` 和 `.quote`)，因此复合数字变为`[3,2,0]`。

最后，统计所有引用元素标签的选择器，并将此数字放置在复合数字的最后部分。在此示例中，有两个选择器 (`p` 和 `span`)，因此最终复合数字为`[3,2,2]`。

这是比较此规则特异性与其他规则所需的全部内容。在像这种情况下，复合数字中每种类型都不超过九个，可以直接转换为十进制数，本例中为 322。低于此数的规则优先级较低，高于此数的规则优先级更高。如果两个规则具有相同的值，则最近应用的规则胜出。

例如，假设我们还有以下规则：

```php
#heading #main .text .quote .news p span {
  // Rules go here;
}
```

在这里，尽管还引用了七个元素，但现在只有两个 ID 引用，但三个类引用，这导致了复合数字`[2,3,2]`。由于 322 大于 232，前者的例子优先于后者。

#### 使用不同的数字基数

当一个复合数字中有超过九个以上的类型时，你必须使用更高的数字基数。例如，你无法简单地将复合数字`[11,7,19]`连接起来转换为十进制。相反，你必须将数字转换为更高的基数，如 20 进制（或更高，如果任何类型超过 19 个）。

要做到这一点，请像这样将三个部分相乘并添加结果，从最右边的数字开始，从右向左工作：

```php
         20 × 19 = 380
      20×20 ×  7 = 2800
   20×20×20 × 11 = 88000
Total in decimal = 91180
```

在左边，将`20`的值替换为你使用的基数。一旦一组规则的复合数字从此基数转换为十进制，就很容易确定每个数字的特异性和因此的优先级。

幸运的是，CSS 处理器会为你处理所有这些问题，但了解它的工作原理有助于你正确构建规则并理解它们将具有的优先级。

###### 注意

如果所有这些优先级计算听起来相当复杂，你会高兴地知道，在大多数情况下，你通常可以通过这个简单的经验法则来解决：一般来说，要修改的元素越少，它们越具体，规则的优先级就越高。

#### 有些规则比其他规则更平等

当两个或多个样式规则完全等效时，只有最近处理的规则会优先生效。然而，通过使用`!important`声明，你可以强制某个规则比其他等效规则具有更高的优先级，例如：

```php
p { color:#ff0000 !important; }
```

当你这样做时，所有先前的等效设置都将被覆盖（甚至使用`!important`的设置），并且稍后处理的任何等效规则都将被忽略。因此，例如，以下两个规则中的第二个通常会优先生效，但由于在先前分配中使用了`!important`，第二个规则被忽略了：

```php
p { color:#ff0000 !important; }
p { color:#ffff00 }
```

###### 注意

用户样式表可以用于指定默认浏览器样式，并且它们可能使用`!important`声明，此时用户的样式设置将优先于当前网页中指定的相同属性。还需要注意，非`!important`的用户样式设置将被网页中任何`!important`样式所覆盖。

# `<div>`和`<span>`元素之间的区别

`<div>`和`<span>`元素都是容器类型，但具有一些不同的特性。默认情况下，`<div>`元素具有无限的宽度（至少到浏览器边缘），你可以通过为其中一个应用边框来查看，例如：

```php
<div style="border:1px solid green;">Hello</div>
```

然而，`<span>` 元素的宽度仅限于其包含的文本。因此，以下 HTML 行仅在单词 `Hello` 周围创建边框，不会延伸到浏览器的右侧边缘：

```php
<span style="border:1px solid green;">Hello</span>
```

此外，`<span>` 元素随文本或其他对象的换行或流动而跟随，并且因此可以具有复杂的边框。例如，在 示例 19-2 中，我使用 CSS 将所有 `<div>` 元素的背景设为黄色，将所有 `<span>` 元素设为青色，并为两者添加了边框，然后创建了几个示例 `<span>` 和 `<div>` 部分。

##### 示例 19-2\. `<div>` 和 `<span>` 示例

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Div and span example</title>
    <style>
      div, span { border          :1px solid black; }
      div       { background-color:yellow;          }
      span      { background-color:cyan;            }
    </style>
  </head>
  <body>
    <div>This text is within a div tag</div>
    This isn't. <div>And this is again.</div><br>

    <span>This text is inside a span tag.</span>
    This isn't. <span>And this is again.</span><br><br>

    <div>This is a larger amount of text in a div that wraps around
    to the next line of the browser</div><br>

    <span>This is a larger amount of text in a span that wraps around
    to the next line of the browser</span>
  </body>
</html>
```

图 19-4 显示了此示例在 Web 浏览器中的样子。尽管在打印的书中只显示为灰色，但该图清楚地展示了 `<div>` 元素如何延伸到浏览器窗口的右侧边缘，并强制下面的内容出现在第一个可用位置的起始处。

![不同宽度的各种元素](img/pmj6_1904.png)

###### 图 19-4\. 不同宽度元素的各种类型

图中还显示了 `<span>` 元素如何保持独立，并且仅占用足够容纳其内容的空间，而不会强制下面的内容出现在它们下面。

此外，在图中的最后两个示例中，您可以看到当 `<div>` 元素在屏幕边缘换行时，它们保持矩形形状，而 `<span>` 元素只是随着包含的文本（或其他内容）流动（或换行）。

###### 注意

由于 `<div>` 元素只能是矩形的，它们更适合用于包含像图片、盒子、引用等对象，而 `<span>` 标签最适合用于容纳文本或其他按行内顺序排列的属性，其应从左到右（或某些语言中从右到左）流动。

# 测量

CSS 支持多种测量单位，可以精确地调整网页到特定的数值或相对尺寸。我通常使用的有像素、点、ems 和百分比，但这里是完整列表：

<dfn class="keep-together">像素</dfn>

像素的大小根据用户屏幕的尺寸和像素深度而变化。一个像素等于屏幕上单个点的宽度/高度，因此该测量单位最适合屏幕而不是印刷。例如：

```php
.classname { margin:5px; }
```

<dfn class="keep-together">点</dfn>

一点相当于一英寸的 1/72 大小。该测量单位来自于印刷设计背景，最适合于该媒介，但也常用于屏幕显示。例如：

```php
.classname { font-size:14pt; }
```

<dfn class="keep-together">英寸</dfn>

一英寸等于 72 点，是最适合印刷的测量单位。例如：

```php
.classname { width:3in; }
```

<dfn class="keep-together">厘米</dfn>

厘米是另一种最适合印刷的度量单位。一厘米略大于 28 点。例如：

```php
.classname { height:2cm; }
```

<dfn class="keep-together">毫米</dfn>

毫米是厘米的十分之一（或接近 3 点）。毫米是另一种最适合印刷的度量单位。例如：

```php
.classname { font-size:5mm; }
```

<dfn class="keep-together">Picas</dfn>

点（Pica）是另一种印刷排版测量单位，相当于 12 点。例如：

```php
.classname { font-size:1pc; }
```

<dfn class="keep-together">Ems</dfn>

一个 em 等于当前字体大小，因此在 CSS 中它是描述相对尺寸的更有用的测量单位之一。例如：

```php
.classname { font-size:2em; }
```

<dfn class="keep-together">Exs</dfn>

Ex 是与当前字体大小相关的，它相当于小写字母 *x* 的高度。这是一种较少使用的度量单位，最常用作帮助设置将包含一些文本的框的宽度的良好近似值。例如：

```php
.classname { width:20ex; }
```

<dfn class="keep-together">百分比</dfn>

这个单位与 em 相关，因为它在字体上的倍数是 100（当用于字体时）。而 1 em 等于当前字体大小，相同大小的百分比是 100。当不涉及字体时，此单位相对于正在访问的属性的容器的大小。例如：

```php
.classname { height:120%; }
```

图 19-5 显示依次使用这些测量类型来显示几乎相同大小的文本。

![几乎相同大小的不同测量](img/pmj6_1905.png)

###### 图 19-5\. 几乎相同大小的不同测量

# 字体和排版

你可以使用 CSS 更改四个主要的字体属性：`font-family`、`font-style`、`font-size` 和 `font-weight`。通过它们，你可以微调网页和/或打印时文本的显示方式。

## 字体系列

`font-family` 属性指定要使用的字体。你可以按照优先级从左到右列出各种字体，这样当用户没有安装首选字体时，样式可以优雅地回退。例如，要设置段落的默认字体，你可以使用类似这样的 CSS 规则：

```php
p { font-family:Verdana, Arial, Helvetica, sans-serif; }
```

如果一个字体名称由两个或更多单词组成，你必须用引号将名称括起来，像这样：

```php
p { font-family:"Times New Roman", Georgia, serif; }
```

###### 注

因为它们几乎可以在所有的网页浏览器和操作系统中使用，所以在网页上使用的最安全的字体系列包括 Arial、Helvetica、Times New Roman、Times、Courier New 和 Courier。Verdana、Georgia、Comic Sans MS、Trebuchet MS、Arial Black 和 Impact 字体适用于 Mac 和 PC，但可能未安装在其他操作系统（如 Linux）上。其他常见但较不安全的字体包括 Palatino、Garamond、Bookman 和 Avant Garde。如果你使用了其中一种较不安全的字体，请确保在你的 CSS 中提供一个或多个更安全的字体作为备用，以便在没有你首选字体的浏览器上，你的网页能够优雅地降级显示。

图 19-6 显示这两组 CSS 规则的应用。

![选择字体系列](img/pmj6_1906.png)

###### 图 19-6\. 选择字体系列

## 字形

使用`font-style`属性，您可以选择以普通、斜体或倾斜方式显示字体。以下规则创建了三个类（`normal`、`italic`和`oblique`），可以应用于元素以创建这些效果：

```php
.normal  { font-style:normal;  }
.italic  { font-style:italic;  }
.oblique { font-style:oblique; }
```

## 字号

正如在测量部分中所述，你可以以多种方式更改字体的大小。但这些归结为两种主要类型：固定大小和相对大小。固定设置如下规则，将默认段落字体大小设置为 14 点：

```php
p { font-size:14pt; }
```

或者，您可能希望使用当前默认的字体大小来为各种类型的文本设置样式，比如标题。在以下规则中，定义了一些标题的相对大小，例如，`<h4>`标签比默认大小大 20%，每个更大的尺寸比上一个大 40%：

```php
h1 { font-size:240%; }
h2 { font-size:200%; }
h3 { font-size:160%; }
h4 { font-size:120%; }
```

图 19-7 展示了正在使用的一些字体大小的选择。

![设置四个标题大小和默认段落大小](img/pmj6_1907.png)

###### 图 19-7\. 设置四个标题大小和默认段落大小

## 字重

使用`font-weight`属性，您可以选择以何种粗细显示字体。它支持许多值，但您可能主要使用的是`normal`和`bold`，如下所示：

```php
.bold { font-weight:bold; }
```

# 管理文本样式

无论使用哪种字体，你都可以通过修改其装饰、间距和对齐方式进一步修改文本的显示方式。文本和字体属性之间存在交叉，比如斜体或粗体文字通过`font-style`和`font-weight`属性实现，而下划线等则需要`text-decoration`属性。

## 装饰

使用`text-decoration`属性，您可以对文本应用效果，如`underline`、`line-through`、`overline`和`blink`。以下规则创建了一个名为`over`的新类，将横线应用于文本（横线、下划线和删除线的粗细将与字体相匹配）：

```php
.over { text-decoration:overline; }
```

在图 19-8 中，你可以看到一些字体样式、粗细和装饰的选择。

![可用样式和装饰规则的示例](img/pmj6_1908.png)

###### 图 19-8\. 可用样式和装饰规则的示例

## 间距

许多不同的属性允许你修改行、词和字母的间距。例如，以下规则通过修改`line-height`属性使段落行距增加 25%，将`word-spacing`属性设置为 30 像素，并将`letter-spacing`设置为 3 像素：

```php
p {
  line-height   :125%;
  word-spacing  :30px;
  letter-spacing:3px;
}
```

你可以选择使用百分比值和`word-spacing`或`letter-spacing`来减少或增加字体应用的默认空间量，使用小于或大于 100%的值，这对比例和非比例字体都适用。

## 对齐

在 CSS 中有四种文本对齐方式：`left`、`right`、`center`和`justify`。在下面的规则中，默认段落文本设置为完全对齐：

```php
p { text-align:justify; }
```

## 转换

有四种属性可用于转换文本：`none`、`capitalize`、`uppercase`和`lowercase`。以下规则创建一个名为`upper`的类，当使用时将确保所有文本以大写显示：

```php
.upper { text-transform:uppercase; }
```

## 缩进

使用`text-indent`属性，您可以按指定量缩进文本块的第一行。下面的规则将每个段落的第一行缩进 20 像素，尽管也可以应用不同的测量单位或百分比增加：

```php
p { text-indent:20px; }
```

在图 19-9 中，以下规则已应用于文本部分：

```php
p {          line-height   :150%;
             word-spacing  :10px;
             letter-spacing:1px;
}
.justify   { text-align    :justify;   }
.uppercase { text-transform:uppercase; }
.indent    { text-indent   :20px;      }
```

![应用了缩进、大写和间距规则](img/pmj6_1909.png)

###### 图 19-9\. 应用了缩进、大写和间距规则

# CSS 颜色

您可以通过使用`color`和`background-color`属性（或通过向`background`属性提供单个参数）将颜色应用于文本和对象的前景和背景。指定的颜色可以是命名颜色之一（如`red`或`blue`）、由十六进制 RGB 三元组创建的颜色（如`#ff0000`或`#0000ff`）或使用`rgb`CSS 函数创建的颜色。

根据[W3C 标准组织](http://www.w3.org)定义的标准 16 种颜色名称分别为`aqua`、`black`、`blue`、`fuchsia`、`gray`、`green`、`lime`、`maroon`、`navy`、`olive`、`purple`、`red`、`silver`、`teal`、`white`和`yellow`。下面的规则使用其中一种名称设置具有 ID`object`的对象的背景颜色：

```php
#object { background-color:silver; }
```

在此规则中，所有`<div>`元素中文本的前景色设置为黄色（因为在计算机显示器上，十六进制级别`ff`红色，加上`ff`绿色，再加上`00`蓝色创建黄色）：

```php
div { color:#ffff00; }
```

或者，如果您不想使用十六进制，可以使用`rgb`函数指定颜色三元组，如下规则，将当前文档的背景颜色更改为水绿色：

```php
body { background-color:rgb(0, 255, 255); }
```

###### 注意

如果您不喜欢使用每种颜色 256 级范围，也可以在`rgb`函数中使用百分比，范围从`0`到`100`，从最低（`0`）到最高（`100`）的主色彩量。例如：`rgb(58%, 95%, 74%)`。您还可以使用浮点值进行更精细的颜色控制，如：`rgb(23.4%, 67.6%, 15.5%)`。

## 短颜色字符串

还有一种十六进制数字串的简写形式，每个颜色只使用每个 2 字节对中的第一个。例如，不使用颜色`#fe4692`，而使用`#f49`，省略每对中的第二个十六进制数字，相当于颜色值为`#ff4499`。

这几乎产生相同的颜色，在不需要精确颜色时非常有用。6 位数和 3 位数字符串的区别在于前者支持 1600 万种不同的颜色，而后者仅支持 4000 种。

无论何时需要使用`#883366`这样的颜色，这直接等同于`#836`（因为较短版本的重复数字被隐含），你可以使用任意一种字符串来创建完全相同的颜色。

## 渐变

可选择应用渐变来替代实心背景颜色，从给定的初始颜色自动流向你选择的最终颜色。最好与简单的颜色规则结合使用，这样不支持渐变的浏览器至少会显示实心颜色。

示例 19-3 使用规则显示橙色渐变（或在不支持的浏览器上显示纯橙色），如图 19-10 的中间部分所示。

##### 示例 19-3\. 创建线性渐变

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Creating a linear gradient</title>
    <style>
      .orangegrad {
        background:orange;
        background:linear-gradient(top, #fb0, #f50);
    </style>
  </head>
  <body>
    <div class='orangegrad'>Black text<br>
    on an orange<br>linear gradient</div>
  </body>
</html>
```

![A solid background color, a linear gradient, and a radial gradient](img/pmj6_1910.png)

###### 图 19-10\. 一个实心背景颜色、线性渐变和径向渐变

要创建渐变，选择从`top`、`bottom`、`left`、`right`和`center`（或任意组合，如`top left`或`center right`）开始的位置，输入所需的起始和结束颜色，然后应用`linear-gradient`或`radial-gradient`规则，确保为所有目标浏览器提供规则。

你还可以通过提供被称为*停止*颜色的中间参数来使用不仅仅是起始和结束颜色。例如，如果提供了五个参数，则每个参数将控制区域的五分之一颜色变化，由其在参数列表中的位置确定。

除了渐变，你还可以将透明度应用到 CSS 对象中，详细说明在第 20 章。

# 定位元素

网页内的元素按其在文档中的放置位置排列，但你可以通过将元素的`position`属性从默认的`static`更改为`absolute`、`relative`、`sticky`或`fixed`之一来移动它们。

## 绝对定位

绝对定位元素从文档中移除后，其它可用的元素将流入其释放出的空间。然后，你可以使用`top`、`right`、`bottom`和`left`属性在文档中任意位置放置对象。

例如，要将 ID 为`object`的对象移动到文档开始位置下方 100 像素并距左边 200 像素的绝对位置，你可以向其应用以下规则（还可以使用 CSS 支持的任何其他测量单位）：

```php
#object {
  position:absolute;
  top     :100px;
  left    :200px;
}
```

该对象将位于其重叠的其他元素的顶部或后面，具体取决于分配给 `z-index` 属性的值（仅适用于定位元素）。元素的默认 `z-index` 值为 `auto`，浏览器会自动为您解决。或者，您可以给属性一个整数值（可能是负数），如下所示：

```php
#object {
  position  :absolute;
  top       :100px;
  left      :200px;
  z-index   :100;
}
```

然后，对象按照从最低到最高 `z-index` 级别的顺序显示，具有较高值的显示在较低值之上。`html` 元素的默认 `z-index` 值为 `0`；所有其他默认值为 `auto`。

## 相对定位

同样，您可以将对象相对于其在正常文档流中将占据的位置移动。因此，例如，要将 `object` 向下移动 10 像素并向右移动 10 像素以超出其正常位置，您将使用以下规则：

```php
#object {
  position:relative;
  top     :10px;
  left    :10px;
}
```

## 固定定位

最终的 `position` 属性设置允许您将对象移动到绝对位置，但仅限于当前浏览器视口。然后，当文档滚动时，对象仍然保持在其放置的确切位置，主文档在其下滚动 - 这是创建码头栏和其他类似设备的绝佳方式。要将对象固定到浏览器窗口的左上角，请使用以下规则：

```php
#object {
  position:fixed;
  top     :0px;
  left    :0px;
}
```

例子 19-4 展示了在页面上应用不同定位值到对象的方法。

##### 例子 19-4\. 应用不同的定位值

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Positioning</title>
    <style>
      #container {
        position  :absolute;
        top       :50px;
        left      :0px;
      }
      #object1 {
        position  :absolute;
        background:pink;
        width     :100px;
        height    :100px;
        top       :0px;
        left      :0px;
      }
      #object2 {
        position  :relative;
        background:lightgreen;
        width     :100px;
        height    :100px;
        top       :0px;
        left      :110px;
      }
      #object3 {
        position  :fixed;
        background:yellow;
        width     :100px;
        height    :100px;
        top       :50px;
        left      :220px;
      }
    </style>
  </head>
  <body>
    <br><br><br><br><br>
    <div id='container'>
      <div id='object1'>Absolute Positioning</div>
      <div id='object2'>Relative Positioning</div>
      <div id='object3'>Fixed Positioning</div>
    </div>
  </body>
</html>
```

在 图 19-11 中，例子 19-4 已加载到浏览器中，并且浏览器窗口已减小到宽度和高度，因此您必须向下滚动才能查看整个网页。

![使用不同的定位值](img/pmj6_1911.png)

###### 图 19-11\. 使用不同的定位值

当这样做时，可以立即看出具有固定定位的元素（`object3`）即使通过滚动也保持在原位。您还可以看到容器元素（名为 `container`）具有绝对定位，并且正好位于向下 50 像素，水平偏移为 0 像素，以便 `object1`（在 `container` 内具有绝对定位）出现在该位置。与此同时，`object2` 具有相对定位，因此与 `container` 的左边距相差 110 像素，与 `object1` 对齐。

在该图中，`object3`，即使出现在 HTML 的 `container` 元素中，也具有固定定位，因此实际上与其他对象完全独立，不受 `container` 边界的限制。它最初设置为与 `object1` 和 `object2` 对齐，但与其他对象一起滚动到页面上方时保持不变，现在看起来偏离了它们下方的偏移位置。

# 伪类

一些选择器和类仅在样式表中使用，并且在 HTML 中没有任何匹配的标签或属性。它们的任务是使用除了名称、属性或内容之外的特征对元素进行分类，即无法从文档树中推断出的特征。这些包括伪类，如`link`和`visited`。还有伪元素用于进行选择，这些选择可能包括部分元素，例如`first-line`或`first-letter`。

伪类和伪元素由`:`（冒号）字符分隔。例如，要创建一个名为`bigfirst`的类以强调元素的第一个字母，您可以使用如下规则：

```php
.bigfirst:first-letter {
  font-size:400%;
  float    :left;
}
```

当`bigfirst`类应用于元素时，第一个字母将显示为大号，其余文本以正常大小显示，并整齐地围绕它流动（由于`float`属性），就像第一个字母是图像或其他对象一样。伪类包括`hover`、`link`、`active`和`visited`，它们大多用于应用于锚元素，如以下规则，将所有链接的默认颜色设置为蓝色，已访问的链接颜色设置为浅蓝色：

```php
a:link    { color:blue;      }
a:visited { color:lightblue; }
```

以下规则非常有趣，因为它们使用了`hover`伪类，因此只有当鼠标指针悬停在元素上时才应用。在这个例子中，它们将链接更改为白色文本和红色背景，提供了一种通常只能通过使用 JavaScript 代码实现的动态效果：

```php
a:hover {
  color     :white;
  background:red;
}
```

在这里，我使用了`background`属性和单个参数，而不是较长的`background-color`属性。

`active`伪类也是动态的，它在鼠标按钮按下和释放之间的时间内对链接进行更改，例如下面的规则，将链接颜色更改为深蓝色：

```php
a:active { color:darkblue; }
```

另一个有趣的动态伪类是`focus`，它只有在用户通过键盘或鼠标选择元素时才应用。以下规则使用通用选择器，始终在当前聚焦对象周围放置一条中灰色的虚线 2 像素边框：

```php
*:focus { border:2px dotted #888888; }
```

###### 注意

这个讨论适用于传统的网页开发，不适用于移动/触摸设备的开发。我们将在第二十三章中更专注于这个话题，讨论 jQuery Mobile。

示例 19-5 显示了两个链接和一个输入字段，如图 19-12 所示。第一个链接因在此浏览器中已访问过而显示为灰色，但第二个链接尚未访问，并显示为蓝色。已按下 Tab 键，并且输入的焦点现在是输入字段，因此其背景已更改为黄色。单击任一链接时，它将显示为紫色，并在悬停时显示为红色。

##### 示例 19-5\. 链接和焦点伪类

```php
<!DOCTYPE html>
<html>
  <head>
    <title>Pseudoclasses</title>
    <style>
      a:link    { color:blue; }
      a:visited { color:gray; }
      a:hover   { color:red; }
      a:active  { color:purple; }
      *:focus   { background:yellow; }
    </style>
  </head>
  <body>
    <a href='http://google.com'>Link to Google'</a><br>
    <a href='nowhere'>Link to nowhere'</a><br>
    <input type='text'>
  </body>
</html>
```

![应用于一组元素的伪类](img/pmj6_1912.png)

###### 图 19-12\. 应用于一组元素的伪类

还有其他伪类可用；有关更多信息，请参阅[HTML Dog“伪类”教程](https://tinyurl.com/htmldipc)。

# 速记规则

为节省空间，相关 CSS 属性组可以合并为单个速记分配。例如，我已经多次使用了创建边框的速记，例如前一节中的`focus`规则中使用的速记：

```php
*:focus { border:2px dotted #ff8800; }
```

这实际上是以下规则集的速记连接：

```php
*:focus {
  border-width:2px;
  border-style:dotted;
  border-color:#ff8800;
}
```

使用速记规则时，只需应用到您希望更改值的点即可。因此，您可以使用以下内容仅设置边框的宽度和样式，选择不设置其颜色：

```php
*:focus { border:2px dotted; }
```

###### 注意

属性在速记规则中的放置顺序可能很重要，误放可能会导致意外结果。由于本章节无法详细说明所有属性，如果您希望使用速记 CSS，请查阅 CSS 手册或搜索引擎获取默认属性及其应用顺序的信息。

# 框模型和布局

影响页面布局的 CSS 属性围绕*框模型*展开，这是围绕元素的一组嵌套属性。几乎所有元素都具有（或可以具有）这些属性，包括文档主体，您可以（例如）通过以下规则删除其边距：

```php
body { margin:0px; }
```

对象的框模型从外部开始，从对象的边距开始。边框位于其内部，边框和内部内容之间有填充，最后是对象的内容。

一旦掌握了框模型，您将能够专业地布局页面，因为这些属性本身将构成页面样式的大部分。

## 设置边距

边距是框模型的最外层。它将元素彼此分开，使用起来非常聪明。例如，假设您给多个元素设置了默认边距为每个元素周围的 10 像素。这是您希望在下面放置两个元素之间出现的空间量，但如果它们每个都有 10 像素的边距，那么结果不是 20 像素的间隙吗？

实际上，CSS 克服了这个潜在问题：当具有定义边距的两个元素直接相邻时，仅使用两个边距中较大的一个来分隔它们。如果两个边距的宽度相同，则只使用其中一个宽度。这样，您更有可能得到想要的结果。但是请注意，绝对定位或内联元素的边距不会以这种方式折叠。

元素的外边距可以通过`margin`属性一次性更改，或者通过`margin-left`、`margin-top`、`margin-right`和`margin-bottom`单独设置。设置`margin`属性时，可以提供一个、两个、三个或四个参数，其效果如下规则所述：

```php
/* Set all margins to 1 pixel */
margin:1px;

/* Set top and bottom to 1 pixel, and left and right to 2 */
margin:1px 2px;

/* Set top to 1 pixel, left and right to 2, and bottom to 3 */
margin:1px 2px 3px;

/* Set top to 1 pixel, right to 2, bottom to 3, and left to 4 */
margin:1px 2px 3px 4px;
```

在示例 19-6 中，一个应用于放置在`table`元素内的正方形元素的`margin`属性规则（加粗显示）被应用。图 19-13 展示了这个示例加载到浏览器中的情况。表格没有给定尺寸，所以它将尽可能紧密地围绕内部`<div>`元素包裹。因此，它上方有 10 像素的外边距，右侧有 20 像素的外边距，下方有 30 像素的外边距，左侧有 40 像素的外边距。

##### 示例 19-6\. 外边距如何应用

```php
<!DOCTYPE html>
<html>
  <head>
    <title>CSS Margins</title>
    <style>
      #object1 {
        background  :lightgreen;
        border-style:solid;
        border-width:1px;
        font-family :"Courier New";
        font-size   :9px;
        width       :100px;
        height      :100px;
        padding     :5px;
        `margin`      `:``10px` `20px` `30px` `40px``;`
      }
      table {
        padding     :0;
        border      :1px solid black;
        background  :cyan;
      }
    </style>
  </head>
  <body>
    <table>
      <tr>
        <td>
          <div id='object1'>margin:<br>10px 20px 30px 40px;</div>
        </td>
      </tr>
    </table>
  </body>
</html>
```

![外部表格根据边距宽度扩展](img/pmj6_1913.png)

###### 图 19-13\. 外部表格根据边距宽度扩展

## 应用边框

盒子模型的边框级别类似于外边距，不同之处在于没有折叠效果。随着我们进入盒子模型，它是下一个级别。用于修改边框的主要属性包括`border`、`border-left`、`border-top`、`border-right`和`border-bottom`，每个属性还可以添加其他后缀子属性，如`-color`、`-style`和`-width`。

用于`margin`属性的四种访问单个属性设置的方法也适用于`border-width`属性，因此以下所有规则均有效：

```php
/* All borders */
border-width:1px;

/* Top/bottom and left/right */
border-width:1px 5px;

/* Top, left/right, and bottom */
border-width:1px 5px 10px;

/* Top, right, bottom, and left */
border-width:1px 5px 10px 15px;
```

图 19-14 展示了依次应用这些规则到一组正方形元素的情况。在第一个元素中，可以清楚地看到所有边框都有 1 像素的宽度。然而，第二个元素的顶部和底部边框宽度为 1 像素，而其侧边边框各为 5 像素。第三个元素的顶部边框宽度为 1 像素，其侧边为 5 像素宽，底部为 10 像素宽。第四个元素的顶部边框宽度为 1 像素，右侧边框宽度为 5 像素，底部边框宽度为 10 像素，左侧边框宽度为 15 像素。

![应用长格式和简写边框规则值](img/pmj6_1914.png)

###### 图 19-14\. 应用长格式和简写边框规则值

在前述元素下的最后一个元素不使用简写规则，而是分别设置每个边框宽度。正如你所见，要达到相同的效果需要输入更多内容。

## 调整填充

盒子模型的最深层（除了元素内容之外）是内边距，它应用在任何边框和/或外边距的内部。用于修改内边距的主要属性包括`padding`、`padding-left`、`padding-top`、`padding-right`和`padding-bottom`。

用于`margin`和`border`属性的四种访问单个属性设置的方法也适用于`padding`属性，因此以下所有规则均有效：

```php
/* All padding */
padding:1px;

/* Top/bottom and left/right */
padding:1px 2px;

/* Top, left/right, and bottom */
padding:1px 2px 3px;

/* Top, right, bottom, and left */
padding:1px 2px 3px 4px;
```

图 19-15 显示了对某个表格单元格内的一些文本应用了填充规则（在 示例 19-7 中以粗体显示），该单元格由规则 `display:table-cell;` 定义，使包围的 `<div>` 元素显示为表格单元格，未给定尺寸，因此它将尽可能紧密地环绕文本。因此，内部元素上方有 10 像素的填充，右侧有 20 像素，下方有 30 像素，左侧有 40 像素。

##### 示例 19-7\. 应用填充

```php
<!DOCTYPE html>
<html>
  <head>
    <title>CSS Padding</title>
    <style>
      #object1 {
        border-style:solid;
        border-width:1px;
        background  :orange;
        color       :darkred;
        font-family :Arial;
        font-size   :12px;
        text-align  :justify;
        display     :table-cell;
        width       :148px;
        `padding`     `:``10px` `20px` `30px` `40px``;` `}`
    </style>
  </head>
  <body>
    <div id='object1'>To be, or not to be that is the question:
    Whether 'tis Nobler in the mind to suffer
    The Slings and Arrows of outrageous Fortune,
    Or to take Arms against a Sea of troubles,
    And by opposing end them.</div>
  </body>
</html>
```

![对对象应用不同的填充值](img/pmj6_1915.png)

###### 图 19-15\. 对对象应用不同的填充值

## 对象内容

最后，在盒模型的深层级中心，存在一个可以在本章讨论的所有方式中进行样式设置的元素。如您所知，这个元素可以（通常会）包含进一步的子元素，反过来可能包含子-子元素，依此类推，每个都有自己的样式和盒模型设置。

# 问题

1.  用于将一个样式表导入另一个样式表（或某些 HTML 的 `<style>` 部分）的指令是哪个？

1.  您可以使用什么 HTML 标签来将样式表导入文档中？

1.  哪个 HTML 标签属性用于直接将样式嵌入元素？

1.  CSS ID 和 CSS 类之间有什么区别？

1.  CSS 规则中用于前缀 (a) ID 和 (b) 类名的字符是哪些？

1.  在 CSS 规则中，分号的作用是什么？

1.  如何向样式表添加注释？

1.  CSS 中用于表示任何元素的字符是哪个？

1.  如何在 CSS 中选择一组不同的元素和/或元素类型？

1.  给定具有相等优先级的一对 CSS 规则，如何使其中一个比另一个具有更高的优先级？

参见 “第十九章答案”，以获取这些问题的答案。
