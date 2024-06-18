# 第六章\. PHP 数组

在第三章中，我对 PHP 数组进行了非常简要的介绍——仅仅足够让你略知一二它们的强大。在本章中，我将向你展示更多可以用数组做的事情，其中一些——如果你曾经使用过像 C 这样的强类型语言——可能会因其优雅和简洁而让你感到惊讶。

数组不仅消除了编写处理复杂数据结构的代码的枯燥感，而且提供了许多访问数据的方式，同时保持惊人的快速性。

# 基本访问

我们已经看过数组，就好像它们是粘在一起的火柴盒群。另一种思考数组的方法是把它看作是一串珠子，每个珠子代表一个可以是数字、字符串或甚至其他数组的变量。它们像珠串一样，因为每个元素都有自己的位置，并且（除了第一个和最后一个）每个元素两侧都有其他元素。

有些数组通过数字索引进行引用；其他允许使用字母数字标识符。内置函数允许你对它们进行排序、添加或移除部分，并通过一种特殊的循环来遍历它们处理每个项目。通过在另一个数组内放置一个或多个数组，你可以创建二维、三维或任意维数的数组。

## 数字索引数组

假设你被委托为当地一家办公用品公司创建一个简单的网站，目前正在处理与纸张相关的部分。管理该类别库存中各种物品的一种方式是将它们放入一个数字数组中。你可以在例子 6-1 中看到最简单的做法。

##### 例子 6-1\. 向数组添加项目

```php
<?php
  $paper[] = "Copier";
  $paper[] = "Inkjet";
  $paper[] = "Laser";
  $paper[] = "Photo";

  print_r($paper);
?>
```

在这个例子中，每当你给数组`$paper`赋值时，都会使用该数组内的第一个空位置来存储值，并且 PHP 内部的指针会递增指向下一个空位置，为将来的插入做准备。熟悉的`print_r`函数（用于打印变量、数组或对象的内容）用于验证数组是否已正确填充。它打印出以下内容：

```php
Array
(
  [0] => Copier
  [1] => Inkjet
  [2] => Laser
  [3] => Photo
)
```

之前的代码也可以像在例子 6-2 中展示的那样书写，其中指定了数组中每个项目的确切位置。但是，正如你所看到的，这种方法需要额外的输入，并且如果你想要在数组中插入或删除项目，会使得你的代码更难维护。所以，除非你希望指定不同的顺序，通常最好让 PHP 处理实际的位置编号。

##### 例子 6-2\. 使用显式位置向数组添加项目

```php
<?php
  $paper[0] = "Copier";
  $paper[1] = "Inkjet";
  $paper[2] = "Laser";
  $paper[3] = "Photo";

  print_r($paper);
?>
```

这些例子的输出是相同的，但在开发网站时你不太可能使用`print_r`，所以例子 6-3 展示了如何使用`for`循环打印出网站提供的各种类型的纸张。

##### 示例 6-3\. 向数组添加项目并检索它们

```php
<?php
  $paper[] = "Copier";
  $paper[] = "Inkjet";
  $paper[] = "Laser";
  $paper[] = "Photo";

  for ($j = 0 ; $j < 4 ; ++$j)
    echo "$j: $paper[$j]<br>";
?>
```

此示例打印出以下内容：

```php
 0: Copier
  1: Inkjet
  2: Laser
  3: Photo

```

到目前为止，你已经看到了几种向数组添加项目和引用它们的方式。PHP 还提供了更多方法，稍后会详细介绍。但首先，我们将看看另一种类型的数组。

## 关联数组

通过索引跟踪数组元素可以很好地工作，但可能需要额外的工作来记住哪个数字指代哪个产品。这也可能使得其他程序员难以理解代码。

这就是关联数组发挥作用的地方。使用它们，您可以通过名称而不是数字引用数组中的项目。示例 6-4 扩展了先前的代码，为数组中的每个元素赋予了一个标识名称和一个更长、更详细的字符串值。

##### 示例 6-4\. 向关联数组添加项目并检索它们

```php
<?php
  $paper['copier'] = "Copier & Multipurpose";
  $paper['inkjet'] = "Inkjet Printer";
  $paper['laser']  = "Laser Printer";
  $paper['photo']  = "Photographic Paper";

  echo $paper['laser'];
?>
```

现在，每个项目都有一个您可以在其他地方引用的唯一名称，例如 `echo` 语句—它仅仅打印出 `激光打印机`。这些名称（如 `复印机`、`喷墨打印机` 等）称为*索引*或*键*，而分配给它们的项目（如 `激光打印机`）称为*值*。

PHP 的这一强大功能经常用于从 XML 和 HTML 中提取信息。例如，像搜索引擎使用的 HTML 解析器可以将网页的所有元素放入一个关联数组中，其名称反映了页面的结构：

```php
$html['title'] = "My web page";
$html['body']  = "... body of web page ...";
```

该程序还可能会将页面中找到的所有链接分解为另一个数组，并将所有标题和副标题分解为另一个数组。当您使用关联数组而不是数值数组时，引用所有这些项的代码变得易于编写和调试。

## 使用 `array` 关键字进行赋值

到目前为止，你已经学会了如何通过逐个添加新项目来向数组赋值。无论你指定键、指定数字标识符还是让 PHP 隐式分配数字标识符，这都是一种冗长的方法。使用 `array` 关键字，有一种更紧凑和更快速的赋值方法。示例 6-5 展示了使用这种方法分配的数字和关联数组。

##### 示例 6-5\. 使用 `array` 关键字向数组添加项目

```php
<?php
  $p1 = array("Copier", "Inkjet", "Laser", "Photo");

  echo "p1 element: " . $p1[2] . "<br>";

  $p2 = array('copier' => "Copier & Multipurpose",
              'inkjet' => "Inkjet Printer",
              'laser'  => "Laser Printer",
              'photo'  => "Photographic Paper");

  echo "p2 element: " . $p2['inkjet'] . "<br>";
?>
```

此片段的前半部分将旧的、简短的产品描述分配给数组 `$p1`。有四个项目，因此它们将占据 0 到 3 的位置。因此，`echo` 语句打印出以下内容：

**`    p1 元素：激光`**

第二部分使用格式*`key`* `=>` *`value`* 将关联标识符和伴随的较长产品描述分配给数组`$p2`。`=>`的使用类似于常规的`=`赋值运算符，不同之处在于你正在为*索引*而不是*变量*赋值。索引因此与该值紧密联系，除非它被赋予新值。因此，`echo`命令打印出以下内容：

**`    p2 元素: 墨水喷墨打印机`**

你可以验证`$p1`和`$p2`是不同类型的数组，因为将以下两个命令附加到代码后，都会导致`Undefined index`或`Undefined offset`错误，因为每个数组标识符都不正确：

```php
echo $p1['inkjet']; // Undefined index
echo $p2[3];        // Undefined offset
```

# `foreach...as`循环

PHP 的创建者为了使语言易于使用，做出了很大努力。因此，他们不满足于已经提供的循环结构，特别为数组添加了另一个循环：`foreach...as`循环。使用它，你可以逐个遍历数组中的所有项，并对它们进行操作。

这个过程从第一项开始，直到最后一项结束，因此你甚至不需要知道数组中有多少项。示例 6-6 展示了如何使用`foreach...as`重写示例 6-3。

##### 示例 6-6\. 使用`foreach...as`遍历数值数组

```php
<?php
  $paper = array("Copier", "Inkjet", "Laser", "Photo");
  $j = 0;

  foreach($paper as $item)
  {
    echo "$j: $item<br>";
    ++$j;
  }
?>
```

当 PHP 遇到`foreach`语句时，它将数组的第一个项放入跟随`as`关键字的变量中；每次控制流返回到`foreach`时，下一个数组元素将被放入`as`关键字中。在这种情况下，变量`$item`依次设置为数组`$paper`中的每个值。一旦所有值都被使用，循环的执行结束。这段代码的输出与示例 6-3 完全相同。

现在让我们看看如何通过查看示例 6-7，了解`foreach`如何与关联数组一起使用，这是示例 6-5 的后半部分重写。

##### 示例 6-7\. 使用`foreach...a`s 遍历关联数组

```php
<?php
  $paper = array('copier' => "Copier & Multipurpose",
                 'inkjet' => "Inkjet Printer",
                 'laser'  => "Laser Printer",
                 'photo'  => "Photographic Paper");

  foreach($paper as $item => $description)
    echo "$item: $description<br>";
?>
```

请记住，关联数组不需要数值索引，因此在此示例中未使用变量`$j`。相反，数组`$paper`的每个项被传递到变量`$item`和`$description`的键/值对中，从中打印出它们。该代码的显示结果如下：

**`    复印机: 复印机和多用途     墨水喷墨: 墨水喷墨打印机     激光: 激光打印机     照片: 摄影纸`**

在 PHP 7.2 版本之前，作为`foreach...as`的替代语法，您可以将`list`函数与`each`函数结合使用。然而，`each`随后被弃用，因此不建议使用，因为它可能会在未来的版本中删除。对于需要更新的 PHP 程序员来说，这是一场噩梦，尤其是`each`函数非常有用。因此，我编写了一个名为`myEach`的替代品，它的功能与`each`完全相同，并且允许您轻松更新旧代码，如示例 6-8。

##### 示例 6-8\. 使用`myEach`和`list`遍历关联数组

```php
<?php
  $paper = array('copier' => "Copier & Multipurpose",
                 'inkjet' => "Inkjet Printer",
                 'laser'  => "Laser Printer",
                 'photo'  => "Photographic Paper");

  while (list($item, $description) = myEach($paper))
    echo "$item: $description<br>";

  function myEach(&$array) // Replacement for the deprecated each function
  {
    $key    = key($array);
    $result = ($key === null) ? false :
              [$key, current($array), 'key', 'value' => current($array)];
    next($array);
    return $result;
  }
?>

```

在这个示例中，设置了一个`while`循环，并且将继续循环，直到`myEach`函数（等同于旧版 PHP 的`each`函数）返回`FALSE`为止。`myEach`函数类似于`foreach`，它返回一个包含数组`$paper`中的键/值对的数组，然后将其内置指针移动到该数组中的下一个对。当没有更多的对要返回时，`myEach`返回`FALSE`。

`list`函数接受一个数组作为其参数（在本例中，是函数`myEach`返回的键/值对），然后将数组的值分配给括号内列出的变量。

在示例 6-9 中，您可以更清楚地看到`list`函数的工作原理，其中一个数组由两个字符串`Alice`和`Bob`创建，然后传递给`list`函数，该函数将这些字符串分配为变量`$a`和`$b`的值。

##### 示例 6-9\. 使用`list`函数

```php
<?php
  list($a, $b) = array('Alice', 'Bob');
  echo "a=$a b=$b";
?>
```

这段代码的输出如下所示：

**`    a=Alice b=Bob`**

因此，在处理数组时，您可以根据需要选择。使用`foreach...as`创建一个循环，将值提取到`as`后面的变量中，或者使用`myEach`函数创建自己的循环系统。

# 多维数组

PHP 数组语法中的一个简单设计特性使得可以创建多维数组。事实上，它们可以是任意多维（尽管很少有应用程序会超过三维）。

这个特性是将整个数组作为另一个数组的一部分包含进来，并且可以继续这样做，就像那首古老的韵文：“大跳蚤背上有小跳蚤来咬它们。小跳蚤有更小的跳蚤，继续蚤类，无穷尽。”

让我们通过扩展前面示例中的关联数组来看看它是如何工作的；参见示例 6-10。

##### 示例 6-10\. 创建一个多维关联数组

```php
<?php
  $products = array(

    'paper' => array(

      'copier' => "Copier & Multipurpose",
      'inkjet' => "Inkjet Printer",
      'laser'  => "Laser Printer",
      'photo'  => "Photographic Paper"),

    'pens' => array(

      'ball'   => "Ball Point",
      'hilite' => "Highlighters",
      'marker' => "Markers"),

    'misc' => array(

      'tape'   => "Sticky Tape",
      'glue'   => "Adhesives",
      'clips'  => "Paperclips"
    )
  );

  echo "<pre>";

  foreach($products as $section => $items)
    foreach($items as $key => $value)
      echo "$section:\t$key\t($value)<br>";

  echo "</pre>";
?>
```

现在代码开始增长，为了使事情更清晰，我已经重新命名了一些元素。例如，因为前面的数组`$paper`现在只是更大数组的一个子部分，所以主数组现在称为`$products`。在这个数组内部，有三个条目——`paper`、`pens`和`misc`——每个条目都包含另一个带有键/值对的数组。

如果必要的话，这些子数组可能还包含更进一步的数组。例如，在 `ball` 下面可能有许多不同类型和颜色的圆珠笔可以在在线商店中获取。但是现在，我将代码限制在了只有两层的深度。

一旦数组数据被赋值，我使用一对嵌套的 `foreach...as` 循环来打印出各种值。外部循环从数组的顶层提取主要部分，内部循环提取每个部分内的键/值对。

只要记住每个数组级别都以相同方式工作（它是一个键/值对），你可以轻松编写代码来访问任何级别的任何元素。

`echo` 语句利用 PHP 转义字符 `\t` 输出制表符。尽管制表符通常对网页浏览器来说不重要，但我让它们被 `<pre>...</pre>` 标签使用，这告诉浏览器将文本格式化为预格式化和等宽字体，并且 *不* 忽略空白字符如制表符和换行符。从这段代码的输出看起来，如下所示：

```php
paper:  copier  (Copier & Multipurpose)
paper:  inkjet  (Inkjet Printer)
paper:  laser   (Laser Printer)
paper:  photo   (Photographic Paper)
pens:   ball    (Ball Point)
pens:   hilite  (Highlighters)
pens:   marker  (Markers)
misc:   tape    (Sticky Tape)
misc:   glue    (Adhesives)
misc:   clips   (Paperclips)
```

你可以通过使用方括号直接访问数组的特定元素。

```php
echo $products['misc']['glue'];
```

这输出值为 `胶粘剂`。

你也可以创建数值型多维数组，通过索引而不是通过字母数字标识符进行访问。示例 6-11 创建了一个象棋游戏的棋盘，其中各个棋子处于起始位置。

##### 示例 6-11\. 创建一个多维数值数组

```php
<?php
  $chessboard = array(
    array('r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'),
    array('p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'),
    array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    array('P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'),
    array('R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R')
  );

  echo "<pre>";

  foreach($chessboard as $row)
  {
    foreach ($row as $piece)
      echo "$piece ";

    echo "<br>";
  }

  echo "</pre>";
?>
```

在这个例子中，小写字母表示黑色棋子，大写字母表示白色棋子。键为 `r` = 车，`n` = 马，`b` = 象，`k` = 王，`q` = 后，`p` = 兵。同样，一对嵌套的 `foreach...as` 循环遍历数组并显示其内容。外部循环将每一行处理为变量 `$row`，它本身是一个数组，因为 `$chessboard` 数组为每一行使用一个子数组。这个循环内有两条语句，所以用大括号将它们括起来。

然后内部循环处理每行中的每个方块，输出存储在其中的字符（`$piece`），后跟一个空格（以使打印输出更加整齐）。这个循环只有一条语句，所以不需要用大括号将其括起来。`<pre>` 和 `</pre>` 标签确保输出正确显示，如下所示：

```php
r n b q k b n r
p p p p p p p p

P P P P P P P P
R N B Q K B N R
```

你可以通过使用方括号直接访问这个数组中的任何元素。

```php
echo $chessboard[7][3];
```

这个语句输出大写字母 `Q`，在第八行向下和第四列向右的位置（请记住数组索引从 0 开始，不是从 1 开始）。

# 使用数组函数

你已经看到了 `list` 和 `each` 函数，但是 PHP 还配备了许多其他处理数组的函数。你可以在[文档](https://tinyurl.com/arraysinphp)中找到完整的列表。然而，其中一些函数非常基础，值得花时间在这里仔细研究它们。

## is_array

数组和变量共享相同的命名空间。这意味着你不能同时有一个名为 `$fred` 的字符串变量和一个名为 `$fred` 的数组。如果你不确定并且你的代码需要检查一个变量是否是数组，你可以使用 `is_array` 函数，如下所示：

```php
echo (is_array($fred)) ? "Is an array" : "Is not an array";
```

注意，如果 `$fred` 还没有被赋值，将生成一个 `Undefined variable` 的消息。

## count

虽然 `each` 函数和 `foreach...as` 循环结构是遍历数组内容的优秀方式，但有时你确实需要知道数组中有多少元素，特别是如果你将直接引用它们的话。要计算顶层数组中的所有元素数，使用如下命令：

```php
echo count($fred);
```

如果你想知道多维数组中总共有多少元素，可以使用如下语句：

```php
echo count($fred, 1);
```

第二个参数是可选的，设置要使用的模式。它应该是 `0` 以限制仅计算顶层，或者 `1` 以强制递归计算所有子数组元素。

## 排序

排序是如此常见，PHP 为此提供了一个内置函数。在其最简单的形式中，你可以像这样使用它：

```php
sort($fred);
```

需要记住的是，与其他一些函数不同，`sort` 会直接对提供的数组进行操作，而不是返回已排序元素的新数组。它在成功时返回 `TRUE`，在出错时返回 `FALSE`，并且还支持一些标志——你可能希望使用的主要两个标志，强制按数字或字符串对项目进行排序，如下所示：

```php
sort($fred, SORT_NUMERIC);
sort($fred, SORT_STRING);
```

你也可以使用 `rsort` 函数按照反向顺序对数组进行排序，如下所示：

```php
rsort($fred, SORT_NUMERIC);
rsort($fred, SORT_STRING);
```

## 洗牌

有时候你可能需要将数组的元素随机排序，比如在创建一副扑克牌游戏时：

```php
shuffle($cards);
```

与 `sort` 类似，`shuffle` 直接作用于提供的数组，并在成功时返回 `TRUE`，在出错时返回 `FALSE`。

## explode

`explode` 是一个非常有用的函数，你可以用它将包含多个由单个字符（或字符串）分隔的项目的字符串转换成一个数组。一个方便的例子是将句子拆分成一个包含所有单词的数组，如 示例 6-12 所示。

##### 示例 6-12\. 使用空格将字符串分割成数组

```php
<?php
  $temp = explode(' ', "This is a sentence with seven words");
  print_r($temp);
?>
```

此示例在浏览器中打印出以下内容（单行显示）：

```php
Array
(
  [0] => This
  [1] => is
  [2] => a
  [3] => sentence
  [4] => with
  [5] => seven
  [6] => words
)
```

第一个参数，分隔符，不一定是空格，甚至不一定是单个字符。示例 6-13 展示了一个略有不同的变体。

##### 示例 6-13\. 将用 `***` 分隔的字符串分割成数组

```php
<?php
  $temp = explode('***', "A***sentence***with***asterisks");
  print_r($temp);
?>
```

示例 6-13 中的代码将打印出以下内容：

```php
Array
(
  [0] => A
  [1] => sentence
  [2] => with
  [3] => asterisks
)
```

## extract

有时候将数组中的键/值对转换为 PHP 变量可能会很方便。一个这样的时机可能是当你处理由表单发送到 PHP 脚本的 `$_GET` 或 `$_POST` 变量时。

当通过网络提交表单时，Web 服务器将变量解包到 PHP 脚本的全局数组中。如果使用 GET 方法发送变量，则会放入一个名为`$_GET`的关联数组；如果使用 POST 发送，则会放入一个名为`$_POST`的关联数组。

当然，您可以按照迄今为止的示例中所示的方式遍历这样的关联数组。但是，有时您只想将发送的值存储到变量中以供以后使用。在这种情况下，您可以让 PHP 自动执行这项工作：

```php
extract($_GET);
```

因此，如果将查询字符串参数`q`发送到 PHP 脚本以及相关联的值`Hi there`，将创建一个名为`$q`的新变量，并为其分配该值。

虽然这种方法很有用，但要小心，因为如果提取的任何变量与您已经定义的变量冲突，您现有的值将被覆盖。为了避免这种可能性，您可以使用该函数提供的许多附加参数之一，就像这样：

```php
extract($_GET, EXTR_PREFIX_ALL, 'fromget');
```

在这种情况下，所有新变量将以给定前缀字符串开始，后跟下划线，因此`$q`将变为`$fromget_q`。我强烈建议您在处理`$_GET`和`$_POST`数组或任何其他可能由用户控制键的数组时使用此函数版本，因为恶意用户可能会提交有意选择的键来覆盖常用变量名并危害您的网站。

## compact

有时您可能想要使用`compact`（`extract`的反向操作）来创建一个包含变量及其值的数组。示例 6-14 展示了您可能如何使用此函数。

##### 示例 6-14\. 使用`compact`函数

```php
<?php
  $fname         = "Doctor";
  $sname         = "Who";
  $planet        = "Gallifrey";
  $system        = "Gridlock";
  $constellation = "Kasterborous";

  $contact = compact('fname', 'sname', 'planet', 'system', 'constellation');

  print_r($contact);
?>
```

运行示例 6-14 的结果如下：

```php
Array
(
  [fname] => Doctor
  [sname] => Who
  [planet] => Gallifrey
  [system] => Gridlock
  [constellation] => Kasterborous
)
```

请注意，`compact`要求以引号提供变量名称，而不是以`$`符号开头。这是因为`compact`正在寻找变量名列表，而不是它们的值。

另一个使用该函数的情况是调试时，您希望快速查看几个变量及其值，例如示例 6-15。

##### 示例 6-15\. 使用`compact`函数进行调试

```php
<?php
  $j       = 23;
  $temp    = "Hello";
  $address = "1 Old Street";
  $age     = 61;

  print_r(compact(explode(' ', 'j temp address age')));
?>
```

这通过使用`explode`函数从字符串中提取所有单词到数组中实现，然后将其传递给`compact`函数，后者再返回一个数组给`print_r`，最终显示其内容。

如果您复制并粘贴`print_r`代码行，则只需修改那里命名的变量即可快速打印一组变量的值。在本例中，输出如下所示：

```php
Array
(
  [j] => 23
  [temp] => Hello
  [address] => 1 Old Street
  [age] => 61
)
```

## 重置

当 `foreach...as` 结构或 `each` 函数遍历数组时，它会保持一个内部的 PHP 指针，记录应该返回的数组元素。如果你的代码需要返回数组的开头，你可以使用 `reset`，它也会返回那个元素的值。以下是如何使用此函数的示例：

```php
reset($fred);         // Throw away return value
$item = reset($fred); // Keep first element of the array in $item
```

## end

与 `reset` 类似，你可以使用 `end` 函数将 PHP 内部数组指针移动到数组的最后一个元素，并返回该元素的值。以下是一些示例：

```php
end($fred);
$item = end($fred);
```

本章结束了你对 PHP 的基础介绍，现在你应该能够使用所学的技能编写相当复杂的程序。在下一章中，我们将讨论使用 PHP 处理常见实际任务的方法。

# 问题

1.  数值数组和关联数组有什么区别？

1.  `array` 关键字的主要优势是什么？

1.  `foreach` 和 `each` 有什么区别？

1.  如何创建多维数组？

1.  如何确定数组中元素的数量？

1.  `explode` 函数的目的是什么？

1.  如何将 PHP 内部指针设置回数组的第一个元素？

请参阅“第六章答案”，位于附录 A 中，以获取这些问题的答案。
