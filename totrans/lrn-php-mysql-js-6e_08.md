# 第五章 PHP 函数和对象

任何编程语言的基本要求包括存储数据的地方，指导程序流的方法，以及一些细节，如表达式评估、文件管理和文本输出。PHP 具备所有这些功能，还有像 `else` 和 `elseif` 这样的工具，使生活更轻松。但即使在你的工具包中拥有所有这些，编程仍然可能笨拙和乏味，特别是在每次需要它们时必须重新编写非常相似的代码片段时。

函数和对象应运而生。正如你可能猜到的那样，*函数*是执行特定功能的一组语句，还可以选择性地返回一个值。当你需要使用多次的一段代码时，你可以将其放入一个函数中，并在需要时通过函数名调用该函数。

函数比连续的内联代码有很多优势。例如：

+   减少输入量

+   减少语法和其他编程错误

+   减少程序文件的加载时间

+   减少执行时间，因为每个函数只编译一次，无论调用多少次

+   接受参数，因此可以用于一般以及特定的情况

对象进一步推进了这个概念。*对象*将一个或多个函数及其使用的数据合并到一个称为*类*的单一结构中。

在本章中，你将学习如何使用函数，从定义和调用到来回传递参数。掌握这些知识后，你将开始创建函数，并在自己的对象中使用它们（在这里它们被称为*方法*）。

###### 注意

现在使用低于 PHP 5.4 的任何版本已经非常不常见（而且绝对不推荐）。因此，本章假设您将使用此版本作为最低版本。一般建议使用版本 5.6，或者新版本 7.0 或 7.1（没有版本 6）。您可以从 AMPPS 控制面板中选择其中任何一个，如第二章所述。

# PHP 函数

PHP 提供了数百个内置函数，使其成为一个非常丰富的语言。要使用函数，只需通过名称调用它。例如，你可以在这里看到 `date` 函数的运行效果：

```php
echo date("l"); // Displays the day of the week
```

括号告诉 PHP 你正在引用一个函数。否则，它会认为你在引用一个常量或变量。

函数可以接受任意数量的参数，包括零个。例如，如下所示的 `phpinfo` 显示有关当前 PHP 安装的大量信息，并且不需要参数：

```php
phpinfo();
```

调用该函数的结果可以在图 5-1 中看到。

![](img/pmj6_0501.png)

###### 图 5-1\. PHP 内置函数 `phpinfo` 的输出

###### 警告

`phpinfo` 函数非常有用，可以获取有关当前 PHP 安装的信息，但这些信息对潜在的黑客也可能非常有用。因此，在任何 Web 可用的代码中绝不要留下对此函数的调用。

一些使用一个或多个参数的内置函数出现在[示例 5-1](https://example.org/three_string_functions)中。

##### 示例 5-1\. 三个字符串函数

```php
<?php
  echo strrev(" .dlrow olleH"); // Reverse string
  echo str_repeat("Hip ", 2);   // Repeat string
  echo strtoupper("hooray!");   // String to uppercase
?>
```

此示例使用三个字符串函数输出以下文本：

**`Hello world. Hip Hip HOORAY!`**

正如您所见，`strrev`函数颠倒了字符串中字符的顺序，`str_repeat`重复了字符串`"Hip "`两次（根据第二个参数的要求），而`strtoupper`将`"hooray!"`转换为大写。

## 定义一个函数

函数的一般语法如下：

```php
function *`function_name`*([*`parameter`* [, ...]])
{
  // *`Statements`*
}
```

语法的第一行指示如下：

+   定义以`function`开头。

+   名称紧随其后，必须以字母或下划线开头，后跟任意数量的字母、数字或下划线。

+   括号是必需的。

+   一个或多个用逗号分隔的参数是可选的（如方括号所示）。

函数名称不区分大小写，所以以下所有字符串都可以引用`print`函数：`PRINT`、`Print`和`PrInT`。

开放大括号开始执行调用函数时会执行的语句；匹配的大括号必须结束它。这些语句可以包括一个或多个`return`语句，它们会强制函数停止执行并返回到调用代码。如果`return`语句附有值，调用代码可以检索它，我们将在接下来看到。

## 返回一个值

让我们看一个简单的函数，将一个人的全名转换为小写，然后将每个部分的第一个字母大写。

我们已经在[示例 5-1](https://example.org/three_string_functions)中看到了 PHP 内置的`strtoupper`函数的示例。对于我们当前的函数，我们将使用它的对应函数，`strtolower`：

```php
$lowered = strtolower("aNY # of Letters and Punctuation you WANT");
```

```php
echo $lowered;
```

此实验的输出如下：

**`any # of letters and punctuation you want`**

尽管我们不希望名字全部小写；我们希望将句子中每个部分的第一个字母大写。（对于这个示例，我们不打算处理 Mary-Ann 或 Jo-En-Lai 等细微情况。）幸运的是，PHP 还提供了一个`ucfirst`函数，可以将字符串的第一个字符设为大写：

```php
$ucfixed = ucfirst("any # of letters and punctuation you want");
```

```php
echo $ucfixed;
```

输出如下：

**`    Any # of letters and punctuation you want`**

现在我们可以进行第一步程序设计：将单词首字母大写，我们先调用`strtolower`函数将字符串转为小写，然后再调用`ucfirst`。这样做的方法是在`ucfirst`中嵌套调用`strtolower`。让我们看看为什么，因为理解代码评估顺序很重要。

假设你简单调用`print`函数：

```php
print(5-8);
```

表达式`5-8`首先被评估，输出是`–3`。（如前一章所示，PHP 将结果转换为字符串以便显示。）如果表达式包含一个函数，那么该函数也将首先被评估：

```php
print(abs(5-8));
```

PHP 在执行这个简短语句时做了几件事情：

1.  评估`5-8`以生成`–3`。

1.  使用`abs`函数将`–3`转换为`3`。

1.  将结果转换为字符串并使用`print`函数输出它。

这一切都有效是因为 PHP 从内到外评估每个元素。当我们调用以下内容时，同样的过程正在进行：

```php
ucfirst(strtolower("aNY # of Letters and Punctuation you WANT"))
```

PHP 将我们的字符串传递给`strtolower`，然后传递给`ucfirst`，生成的结果如我们分别使用这些函数时所见：

**`    任意数量的字母和标点符号`**

现在让我们定义一个函数（显示在示例 5-2 中），该函数接受三个名称并将每个名称转换为小写，并以大写字母开头。

##### 示例 5-2\. 清理完整姓名

```php
<?php
  echo fix_names("WILLIAM", "henry", "gatES");

  function fix_names($n1, $n2, $n3)
  {
    $n1 = ucfirst(strtolower($n1));
    $n2 = ucfirst(strtolower($n2));
    $n3 = ucfirst(strtolower($n3));

    return $n1 . " " . $n2 . " " . $n3;
  }
?>
```

你可能会发现自己写这种类型的代码，因为用户经常不小心将 Caps Lock 键保持打开状态，错误地插入大写字母，甚至完全忘记大写。此示例的输出如下所示：

**  `William Henry Gates`**

## 返回一个数组

我们刚刚看到一个函数返回单个值。还有从函数中获取多个值的方法。

第一种方法是在一个数组中返回它们。正如你在第 3 章中看到的那样，数组就像一排粘在一起的变量。示例 5-3 展示了如何使用数组返回函数值。

##### 示例 5-3\. 在数组中返回多个值

```php
<?php
  $names = fix_names("WILLIAM", "henry", "gatES");
  echo $names[0] . " " . $names[1] . " " . $names[2];

  function fix_names($n1, $n2, $n3)
  {
    $n1 = ucfirst(strtolower($n1));
    $n2 = ucfirst(strtolower($n2));
    $n3 = ucfirst(strtolower($n3));

    return array($n1, $n2, $n3);
  }
?>
```

这种方法的好处是保持所有三个名称分开，而不是将它们连接成一个字符串，因此你可以仅仅通过名字或姓氏来引用任何用户，而不必从返回的字符串中提取任何一个名称。

## 按引用传递参数

在 PHP 5.3 之前的版本中，你可以在调用函数时用`&`符号作为变量的前缀（例如，`increment(&$myvar);`）告诉解析器传递变量的引用，而不是变量的值。这使函数可以访问变量（允许将不同的值写回到它）。

###### 注意

在 PHP 5.3 中弃用了调用时传递引用，而在 PHP 5.4 中移除了这个特性。因此，除了在旧版网站上，你不应该使用这个特性，即使在那里也建议重新编写传递引用的代码，因为在较新版本的 PHP 上会产生致命错误。

但是，在函数定义内部，你仍然可以通过引用访问参数。这个概念可能很难理解，所以让我们回到第 3 章中的火柴盒比喻。

想象一下，不是从火柴盒里拿出一张纸，读取它，将上面的内容复制到另一张纸上，把原始的放回去，然后将复印件传递给一个函数（呼！），你可以简单地将一根线连接到原始的纸上，并将其一端传递给函数（参见图 5-2）。

![将引用想象成连接到变量的线程](img/pmj6_0502.png)

###### 图 5-2\. 想象一个参考作为附加到变量的线索

现在函数可以跟随线索找到要访问的数据。这样可以避免为函数使用的变量创建副本的所有开销。更重要的是，函数现在可以修改变量的值。

这意味着您可以重写 示例 5-3，将所有参数的引用传递，然后函数可以直接修改这些参数（参见 示例 5-4）。

##### 示例 5-4\. 通过引用将值传递给函数

```php
<?php
  $a1 = "WILLIAM";
  $a2 = "henry";
  $a3 = "gatES";

  echo $a1 . " " . $a2 . " " . $a3 . "<br>";
  fix_names($a1, $a2, $a3);
  echo $a1 . " " . $a2 . " " . $a3;

  function fix_names(&$n1, &$n2, &$n3)
  {
    $n1 = ucfirst(strtolower($n1));
    $n2 = ucfirst(strtolower($n2));
    $n3 = ucfirst(strtolower($n3));
  }
?>
```

与其直接将字符串传递给函数，不如先将它们分配给变量并打印出它们的“before”值。然后像以前一样调用函数，但在函数定义内部，您在每个参数前面放置一个 `&` 符号以按引用传递。

现在变量 `$n1`、`$n2` 和 `$n3` 都附加到通向 `$a1`、`$a2` 和 `$a3` 值的“线索”。换句话说，有一个值组，但允许两组变量名访问它们。

因此，函数 `fix_names` 只需要为 `$n1`、`$n2` 和 `$n3` 分配新值即可更新 `$a1`、`$a2` 和 `$a3` 的值。此代码的输出是：

**`    威廉·亨利·盖茨·威廉·亨利·盖茨`**

正如您所见，`echo` 语句只使用了 `$a1`、`$a2` 和 `$a3` 的值。

## 返回全局变量

给函数访问外部创建的不作为参数传递的变量更好的方法是通过在函数内部声明具有全局访问权限。在变量名后面跟着 `global` 关键字可以让代码的每个部分都完全访问它（参见 示例 5-5）。

##### 示例 5-5\. 在全局变量中返回值

```php
<?php
  $a1 = "WILLIAM";
  $a2 = "henry";
  $a3 = "gatES";

  echo $a1 . " " . $a2 . " " . $a3 . "<br>";
  fix_names();
  echo $a1 . " " . $a2 . " " . $a3;

  function fix_names()
  {
    global $a1; $a1 = ucfirst(strtolower($a1));
    global $a2; $a2 = ucfirst(strtolower($a2));
    global $a3; $a3 = ucfirst(strtolower($a3));
  }
?>
```

现在您不必将参数传递给函数，函数也不必接受它们。一旦声明，这些变量保留全局访问权限，并且可以在程序的其余部分（包括其函数）中使用。

## 变量作用域回顾

快速回顾从 第三章 中所知的内容：

+   *局部变量* 只能从定义它们的代码部分访问。如果它们在函数外部，则可以被所有函数外部的代码访问，包括类等。如果一个变量在函数内部，只有该函数可以访问该变量，并且当函数返回时其值会丢失。

+   *全局变量* 可以从代码的所有部分访问，无论是在函数内部还是外部。

+   *静态变量* 只能在声明它们的函数内部访问，但在多次调用之间保留它们的值。

# 包含和需要文件

在您使用 PHP 编程时，您可能会开始构建您认为将来会再次使用的函数库。您还可能开始使用其他程序员创建的库。

没有必要将这些函数复制粘贴到你的代码中。你可以将它们保存在单独的文件中，并使用命令将它们拉入。有两个命令可以执行此操作：`include` 和 `require`。

## include 语句

使用 `include`，你可以告诉 PHP 获取一个特定文件并加载其所有内容。就好像你把包含的文件粘贴到当前文件的插入点一样。示例 5-6 展示了如何包含一个名为 *library.php* 的文件。

##### 示例 5-6\. 包含一个 PHP 文件

```php
<?php
  include "library.php";

  // Your code goes here
?>
```

## 使用 include_once

每次使用 `include` 指令，即使已经插入过，也会再次包含请求的文件。例如，假设 *library.php* 包含许多有用的函数，你将它包含在你的文件中，但你还包含了另一个包含 *library.php* 的库。通过嵌套，你无意中包含了 *library.php* 两次。这将产生错误消息，因为你尝试多次定义相同的常量或函数。所以，你应该使用 `include_once` 替代（参见 示例 5-7）。

##### 示例 5-7\. 仅包含一次 PHP 文件

```php
<?php
  include_once "library.php";

  // Your code goes here
?>
```

然后，任何进一步尝试包含同一文件（使用 `include` 或 `include_once`）都将被忽略。要确定请求的文件是否已执行，需要在解析所有相对路径（到它们的绝对路径）并在你的 `include` 路径中找到文件后，匹配绝对文件路径。

###### 注意

一般来说，最好坚持使用 `include_once`，忽略基本的 `include` 语句。这样，你就永远不会遇到多次包含文件的问题。

## 使用 require 和 require_once

`include` 和 `include_once` 的一个潜在问题是，PHP 只会 *尝试* 包含请求的文件。即使找不到文件，程序执行也会继续。

当绝对需要包含文件时，使用 `require`。出于同样的原因，我建议你在需要 `require` 文件时通常坚持使用 `require_once`（参见 示例 5-8）。

##### 示例 5-8\. 仅需要一次引入 PHP 文件

```php
<?php
  require_once "library.php";

  // Your code goes here
?>
```

# PHP 版本兼容性

PHP 正在持续开发中，有多个版本。如果需要检查特定函数是否可用于你的代码，可以使用 `function_exists` 函数，该函数检查所有预定义和用户创建的函数。

示例 5-9 检查 `array_combine`，这是仅适用于某些 PHP 版本的特定函数。

##### 示例 5-9\. 检查函数是否存在

```php
<?php
  if (function_exists("array_combine"))
  {
    echo "Function exists";
  }
  else
  {
    echo "Function does not exist - better write our own";
  }
?>
```

使用这样的代码，你可以利用新版本 PHP 的功能，同时使你的代码在旧版本上运行，只要你复制了任何缺失的功能。你的函数可能比内置函数慢，但至少你的代码将更加可移植。

你也可以使用`phpversion`函数来确定你的代码运行在哪个版本的 PHP 上。返回的结果将类似于以下内容，具体取决于版本：

**  8.0.0**

# PHP 对象

就像函数代表着编程力量在计算机早期时代的巨大增长一样，在那些时代，有时候最好的程序导航只是一个非常基本的`GOTO`或`GOSUB`语句一样，*面向对象编程*（OOP）将函数的使用带入了不同的方向。

一旦你掌握了将可重用的代码片段压缩成函数的技巧，将这些函数及其数据打包成对象就不是那么大的飞跃了。

让我们以一个有很多部分的社交网络站点为例。其中一个部分处理所有用户功能——也就是说，用于启用新用户注册和现有用户修改其详细信息的代码。在标准的 PHP 中，你可能会创建一些函数来处理这些，并嵌入一些调用 MySQL 数据库的代码来跟踪所有用户。

要创建一个表示当前用户的对象，你可以创建一个类，也许叫做`User`，其中包含处理用户所需的所有代码以及操作类内部数据所需的所有变量。然后，每当需要操作用户数据时，你可以简单地使用`User`类创建一个新对象。

你可以将这个新对象视为实际的用户。例如，你可以向对象传递姓名、密码和电子邮件地址；询问它是否已存在这样一个用户；如果不存在，让它使用这些属性创建一个新用户。你甚至可以有一个即时消息对象，或者用于管理两个用户是否是朋友的对象。

## 术语

当创建一个使用对象的程序时，你需要设计一个称为*类*的数据和代码的复合体。基于这个类创建的每个新对象称为该类的*实例*（或*发生*）。

与对象相关联的数据称为其*属性*；它所使用的函数称为*方法*。在定义一个类时，你提供其属性的名称和方法的代码。参见图 5-3 关于一个对象的自动唱机比喻。想象一下它在旋转碟盘中保存的 CD，就像是它的属性；播放它们的方法是在前面板上按按钮。还有一个插入硬币的槽口（用于激活对象的方法），以及一个激光唱片阅读器（用于从 CD 中检索音乐或属性的方法）。

![一个自包含对象的绝佳例子：自动唱机](img/pmj6_0503.png)

###### 图 5-3\. 一个自包含对象的绝佳例子：自动唱机

当你创建对象时，最好使用*封装*，或者以这样一种方式编写类，即只有其方法才能用于操作其属性。换句话说，你拒绝外部代码直接访问其数据。你提供的方法被称为对象的*接口*。

这种方法使得调试变得容易：你只需修复类内的错误代码。 另外，当你想要升级程序时，如果使用了适当的封装并保持了相同的接口，你只需开发新的替代类，彻底调试它们，然后替换旧类。 如果它们不起作用，你可以重新替换旧类，立即修复问题，然后进一步调试新类。

一旦你创建了一个类，你可能会发现你需要另一个类，它与之类似但不完全相同。 最快最简单的方法是使用*继承*定义一个新类。 当你这样做时，你的新类拥有从它继承的所有属性。 原始类现在称为*父类*（或偶尔是*超类*），而新类则是*子类*（或*派生类*）。

在我们的点唱机示例中，如果你发明了一个新的点唱机，它可以同时播放视频和音乐，你可以继承原始点唱机超类的所有属性和方法，并添加一些新属性（视频）和新方法（电影播放器）。

这个系统的一个显著优点是，如果你提高了超类的速度或任何其他方面，其子类将获得同样的好处。 另一方面，对父/超类进行的任何更改可能会破坏子类。

## 声明类

在你可以使用对象之前，你必须用`class`关键字定义一个类。 类定义包含类名（区分大小写），其属性和其方法。 示例 5-10 定义了`User`类，具有两个属性`$name`和`$password`（由`public`关键字指示—参见“属性和方法范围”）。 它还创建了这个类的新实例（称为`$object`）。

##### 示例 5-10\. 声明一个类并检查一个对象

```php
<?php
  $object = new User;
  print_r($object);

  class User
  {
    public $name, $password;

    function save_user()
    {
      echo "Save User code goes here";
    }
  }
?>
```

在这里，我还使用了一个称为`print_r`的宝贵函数。 它要求 PHP 以人类可读的形式显示关于变量的信息。 （`_r`代表*人类可读*。） 在新对象`$object`的情况下，它显示如下内容：

```php
`User` `Object`
`(`
  `[``name``]`     `=>`
  `[``password``]` `=>`
`)`

```

然而，浏览器会压缩所有空白字符，因此在浏览器中输出稍微难以阅读：

**`    用户对象（[name] => [password] => ）`**

无论如何，输出显示`$object`是一个具有属性`name`和`password`的用户定义对象。

## 创建对象

要创建一个具有指定类的对象，请使用`new`关键字，如此：`$object = new Class`。 这里有几种我们可以这样做的方式：

```php
$object = new User;
$temp   = new User('name', 'password');
```

在第一行中，我们只是将一个对象分配给`User`类。 在第二行，我们向调用传递参数。

一个类可能需要或禁止参数； 它也可能允许不明确需要的参数。

## 访问对象

让我们添加几行到示例 5-10，并检查结果。示例 5-11 通过设置对象属性并调用方法扩展了先前的代码。

##### 示例 5-11\. 创建并与对象交互

```php
<?php
  $object = new User;
  print_r($object); echo "<br>";

  $object->name     = "Joe";
  $object->password = "mypass";
  print_r($object); echo "<br>";

  $object->save_user();

  class User
  {
    public $name, $password;

    function save_user()
    {
      echo "Save User code goes here";
    }
  }
?>
```

正如您所见，访问对象属性的语法是*`$object->property`*。同样，调用方法的方式是这样的：*`$object->method()`*。

您应该注意到，示例中的`property`和`method`并没有在它们前面加上`$`符号。如果您在它们前面加上`$`符号，代码将无法工作，因为它会尝试引用变量中的值。例如，表达式`$object->$property`会尝试查找分配给名为`$property`的变量的值（假设该值是字符串`brown`），然后尝试引用属性`$object->brown`。如果`$property`未定义，则会尝试引用`$object->NULL`并导致错误。

使用浏览器的查看源代码工具查看示例 5-11 的输出如下：

```php
`User` `Object`
`(`
  `[``name``]`     `=>`
  `[``password``]` `=>`
`)`
`User` `Object`
`(`
  `[``name``]`     `=>` `Joe`
  `[``password``]` `=>` `mypass`
`)`
`Save` `User` `code` `goes` `here`

```

再次使用`print_r`通过在属性分配之前和之后提供`$object`的内容来展示其实用性。从现在开始，我将省略`print_r`语句，但是如果您在开发服务器上跟随本书工作，可以添加一些语句以准确了解发生了什么。

您还可以看到，在方法`save_user`中的代码通过调用该方法而执行。它打印了一个提醒我们创建一些代码的字符串。

###### 注意

您可以将函数和类定义放置在代码的任何位置，无论是在使用它们的语句之前还是之后。不过，通常认为将它们放置在文件的末尾是一种良好的实践。

## 克隆对象

一旦创建了对象，在将其作为参数传递时，它将按引用传递。用火柴盒的比喻来说，这就像将几根线固定在一个放在火柴盒中的对象上，这样您就可以跟随任何附加的线来访问它。

换句话说，对象分配并不复制对象的整体内容。您将在示例 5-12 中看到这是如何工作的，我们定义了一个非常简单的`User`类，没有方法，只有属性`name`。

##### 示例 5-12\. 复制对象

```php
<?php
  $object1       = new User();
  $object1->name = "Alice";
  $object2       = $object1;
  $object2->name = "Amy";

  echo "object1 name = " . $object1->name . "<br>";
  echo "object2 name = " . $object2->name;

  class User
  {
    public $name;
  }
?>
```

在这里，我们首先创建对象`$object1`并将值`Alice`分配给`name`属性。然后，我们创建`$object2`，将其赋值为`$object1`的值，并仅将`Amy`赋值给`$object2`的`name`属性——或者我们可能会这样认为。但是，此代码输出如下：

```php
object1 name = Amy
object2 name = Amy

```

发生了什么？`$object1`和`$object2`都指向*同一个*对象，因此将`$object2`的`name`属性更改为`Amy`也会为`$object1`设置该属性。

为了避免混淆，可以使用`clone`操作符，它创建类的新实例，并从原始实例复制属性值到新实例中。示例 5-13 展示了这种用法。

##### 示例 5-13\. 克隆对象

```php
<?php
  $object1       = new User();
  $object1->name = "Alice";
  $object2       = clone $object1;
  $object2->name = "Amy";

  echo "object1 name = " . $object1->name . "<br>";
  echo "object2 name = " . $object2->name;

  class User
  {
    public $name;
  }
?>
```

瞧！这段代码的输出正是我们最初想要的：

```php
object1 name = Alice
object2 name = Amy

```

## 构造函数

当创建一个新对象时，你可以向被调用的类传递一系列参数。这些参数传递给类中的一个特殊方法，称为*构造方法*，它初始化各种属性。

要做到这一点，你使用函数名`__construct`（即，`construct`前面加上两个下划线字符），就像在示例 5-14 中一样。

##### 示例 5-14\. 创建构造方法

```php
<?php
  class User
  {
    function __construct($param1, $param2)
    {
      // Constructor statements go here
    }
  }
?>
```

## 析构函数

你还可以创建*析构*方法。当代码已经引用了一个对象或脚本达到结尾时，这个功能非常有用。示例 5-15 展示了如何创建析构方法。析构函数可以进行清理工作，例如释放数据库连接或类中保留的其他资源。因为你在类中保留了资源，所以必须在这里释放它，否则它将无限期存在。许多系统-wide 问题是由程序保留资源并忘记释放它们引起的。

##### 示例 5-15\. 创建析构方法

```php
<?php
  class User
  {
    function __destruct()
    {
      // Destructor code goes here
    }
  }
?>
```

## 编写方法

正如你所见，声明方法类似于声明函数，但有一些区别。例如，以双下划线 (`__`) 开头的方法名是保留的（例如，`__construct` 和 `__destruct`），你不应该创建这种形式的任何方法。

你还可以访问一个特殊的变量称为`$this`，它可以用来访问当前对象的属性。要了解它是如何工作的，请看示例 5-16，其中包含了`User`类定义中不同的方法`get_password`。

##### 示例 5-16\. 在方法中使用变量`$this`

```php
<?php
  class User
  {
    public $name, $password;

    function get_password()
    {
      return $this->password;
    }
  }
?>
```

`get_password`使用`$this`变量来访问当前对象，然后返回该对象的`password`属性的值。请注意，当我们使用`->`操作符时，前面的`$`符号在`$password`属性中被省略了。在首次使用此功能时，保留`$`可能是一个典型的错误。

下面是如何使用在示例 5-16 中定义的类：

```php
$object           = new User;
$object->password = "secret";

echo $object->get_password();
```

这段代码打印出密码`secret`。

## 声明属性

在类中显式声明属性并不是必需的，因为它们可以在首次使用时隐式定义。为了说明这一点，在示例 5-17 中，`User`类没有属性和方法，但是这是合法的代码。

##### 示例 5-17\. 隐式定义属性

```php
<?php
  $object1       = new User();
  $object1->name = "Alice";

  echo $object1->name;

  class User {}
?>
```

这段代码正确输出字符串`Alice`，没有问题，因为 PHP 会隐式地为你声明属性`$object1->name`。但这种编程方式可能会导致极难发现的错误，因为`name`是从类外部声明的。

为了帮助自己和任何将来维护你代码的人，我建议你养成在类内部始终显式声明属性的习惯。你会为此感到高兴的。

此外，当你在类内声明一个属性时，你可以为其分配一个默认值。你使用的值必须是一个常量，而不是函数或表达式的结果。示例 5-18 展示了一些有效和无效的赋值。

##### 示例 5-18\. 有效和无效的属性声明

```php
<?php
  class Test
  {
    public $name  = "Paul Smith"; // Valid
    public $age   = 42;           // Valid
    public $time  = time();       // Invalid - calls a function
    public $score = $level * 2;   // Invalid - uses an expression
  }
?>
```

## 声明常量

就像你可以使用`define`函数创建全局常量一样，你也可以在类内部定义常量。普遍接受的做法是使用大写字母以突出它们的重要性，如示例 5-19 所示。

##### 示例 5-19\. 在类内定义常量

```php
<?php
  Translate::lookup();

  class Translate
  {
    const ENGLISH = 0;
    const SPANISH = 1;
    const FRENCH  = 2;
    const GERMAN  = 3;
    // ...

    static function lookup()
    {
      echo self::SPANISH;
    }
  }
?>
```

你可以直接引用常量，使用`self`关键字和双冒号操作符。请注意，此代码在第 1 行直接调用类，而不是先创建实例。运行此代码时打印的值将是`1`。

请记住，一旦定义了常量，就不能更改它。

## 属性和方法的作用域

PHP 提供了三个关键字来控制属性和方法（*成员*）的作用域：

<dfn class="keep-together">`public`</dfn>

公共成员可以在任何地方引用，包括其他类和对象的实例。这是使用`var`或`public`关键字声明变量时的默认值，或者在第一次使用变量时隐式声明的默认值。`var`和`public`关键字是可互换的，因为尽管`var`已被弃用，但为了与 PHP 的旧版本兼容，它仍然保留。方法默认被假定为`public`。

<dfn class="keep-together">`protected`</dfn>

这些成员只能通过对象的类方法及其任何子类的方法引用。

<dfn class="keep-together">`private`</dfn>

这些成员只能通过同一类内的方法引用，而不能通过子类引用。

下面是如何决定使用哪个：

+   当外部代码*应该*访问此成员且扩展类也*应该*继承它时，请使用`public`。

+   当外部代码*不应该*访问此成员但扩展类*应该*继承它时，请使用`protected`。

+   当外部代码*不应该*访问此成员且扩展类也*不应该*继承它时，请使用`private`。

示例 5-20 说明了这些关键字的使用。

##### 示例 5-20\. 改变属性和方法的作用域

```php
<?php
  class Example
  {
    var $name   = "Michael"; // Same as public but deprecated
    public $age = 23;        // Public property
    protected $usercount;    // Protected property

    private function admin() // Private method
    {
      // Admin code goes here
    }
  }
?>
```

## 静态方法

您可以将方法定义为`static`，这意味着它是在类上调用而不是在对象上调用。静态方法无法访问任何对象属性，并且如在示例 5-21 中所示创建和访问。

##### 示例 5-21\. 创建和访问静态方法

```php
<?php
  User::pwd_string();

  class User
  {
    static function pwd_string()
    {
      echo "Please enter your password";
    }
  }
?>
```

注意如何使用双冒号（也称为*作用域解析*运算符）而不是`->`来调用类本身以及静态方法。静态函数用于执行与类本身相关的操作，而不是特定类的实例。您可以在示例 5-19 中看到另一个静态方法的示例。

###### 注意

如果尝试从静态函数内部访问`$this->property`或其他对象属性，将收到错误消息。

## 静态属性

大多数数据和方法适用于类的实例。例如，在`User`类中，您希望执行设置特定用户密码或检查用户注册时间等操作。这些事实和操作分别适用于每个用户，因此使用实例特定的属性和方法。

但偶尔您可能需要维护关于整个类的数据。例如，要报告注册用户的数量，您将存储一个适用于整个`User`类的变量。PHP 提供了用于此类数据的静态属性和方法。

如示例 5-21 中简要显示的那样，声明类成员为`static`使它们可以在不实例化类的情况下访问。声明为`static`的属性无法在类的实例内直接访问，但静态方法可以。

示例 5-22 定义了一个名为`Test`的类，其中包含一个静态属性和一个公共方法。

##### 示例 5-22\. 定义具有静态属性的类

```php
<?php
  $temp = new Test();

  echo "Test A: " . Test::$static_property . "<br>";
  echo "Test B: " . $temp->get_sp()        . "<br>";
  echo "Test C: " . $temp->static_property . "<br>";

  class Test
  {
    static $static_property = "I'm static";

    function get_sp()
    {
       return self::$static_property;
    }
  }
?>
```

运行此代码时，将返回以下输出：

```php
 Test A: I'm static
Test B: I'm static
Notice: Undefined property: Test::$static_property
Test C: 

```

此示例显示，通过 Test A 中的双冒号运算符，可以直接从类本身引用属性`$static_property`。另外，Test B 可以通过从类`Test`创建的对象`$temp`调用`get_sp`方法来获取其值。但是，Test C 失败了，因为对象`$temp`无法访问静态属性`$static_property`。

注意方法`get_sp`如何使用关键字`self`访问`$static_property`。这是在类内部直接访问静态属性或常量的方式。

## 继承

定义类后，可以从中派生子类。这可以节省大量费力的代码重写：您可以取一个与您需要编写的类类似的类，将其扩展为子类，并仅修改不同的部分。您可以使用`extends`关键字来实现这一点。

在示例 5-23 中，类`Subscriber`通过`extends`关键字声明为`User`的子类。

##### 示例 5-23\. 继承和扩展类

```php
<?php
  $object           = new Subscriber;
  $object->name     = "Fred";
  $object->password = "pword";
  $object->phone    = "012 345 6789";
  $object->email    = "fred@bloggs.com";
  $object->display();

  class User
  {
    public $name, $password;

    function save_user()
    {
      echo "Save User code goes here";
    }
  }

  class Subscriber extends User
  {
    public $phone, $email;

    function display()
    {
      echo "Name:  " . $this->name     . "<br>";
      echo "Pass:  " . $this->password . "<br>";
      echo "Phone: " . $this->phone    . "<br>";
      echo "Email: " . $this->email;
    }
  }
?>
```

原始`User`类具有两个属性，`$name`和`$password`，以及一个将当前用户保存到数据库的方法。 `Subscriber`通过添加另外两个属性`$phone`和`$email`来扩展此类，并包括使用变量`$this`显示当前对象属性的方法，该变量引用正在访问的对象的当前值。 此代码的输出如下：

**`    名称:  弗雷德     密码:  密码     电话: 012 345 6789     电子邮件: fred@bloggs.com`**

### 父关键字

如果您在子类中编写与父类中同名的方法，其语句将覆盖父类的语句。 有时这不是您想要的行为，您需要访问父方法。 为此，可以使用`parent`运算符，如示例 5-24。

##### 示例 5-24\. 覆盖方法并使用`parent`运算符

```php
<?php
  $object = new Son;
  $object->test();
  $object->test2();

  class Dad
  {
    function test()
    {
      echo "[Class Dad] I am your Father<br>";
    }
  }

  class Son extends Dad
  {
    function test()
    {
      echo "[Class Son] I am Luke<br>";
    }

    function test2()
    {
      parent::test();
    }
  }
?>
```

此代码创建了一个名为`Dad`的类和一个名为`Son`的子类，后者继承了其属性和方法，然后覆盖了方法`test`。 因此，当第 2 行调用方法`test`时，将执行新方法。 要执行`Dad`类中重写的`test`方法的唯一方法是使用`parent`运算符，如`Son`类的`test2`函数所示。 代码输出如下：

`**    [类儿子] 我是卢克**`

`**    [类爸爸] 我是你的父亲**`

如果希望确保您的代码调用当前类的方法，可以使用`self`关键字，如下所示：

```php
self::method();
```

### 子类构造函数

当您扩展一个类并声明自己的构造函数时，应该意识到 PHP 不会自动调用父类的构造函数。 如果希望确保执行所有初始化代码，子类应始终调用父类构造函数，如示例 5-25。

##### 示例 5-25\. 调用父类构造函数

```php
<?php
  $object = new Tiger();

  echo "Tigers have...<br>";
  echo "Fur: " . $object->fur . "<br>";
  echo "Stripes: " . $object->stripes;

  class Wildcat
  {
    public $fur; // Wildcats have fur

    function __construct()
    {
      $this->fur = "TRUE";
    }
  }

  class Tiger extends Wildcat
  {
    public $stripes; // Tigers have stripes

    function __construct()
    {
      parent::__construct(); // Call parent constructor first
      $this->stripes = "TRUE";
    }
  }
?>
```

此示例以典型方式利用继承。 `Wildcat`类创建了属性`$fur`，我们希望重用该属性，因此我们创建了`Tiger`类来继承`$fur`并额外创建了另一个属性`$stripes`。 为了验证已调用两个构造函数，程序输出如下：

**`    老虎有...     毛皮: TRUE     条纹: TRUE`**

### 最终方法

当你希望防止子类覆盖超类方法时，可以使用`final`关键字。 示例 5-26 演示了如何使用。

##### 示例 5-26\. 创建一个`final`方法

```php
<?php
  class User
  {
    final function copyright()
    {
      echo "This class was written by Joe Smith";
    }
  }
?>
```

一旦您消化了本章的内容，您应该对 PHP 能为您做什么有很强的感觉。 您应该能够轻松使用函数，并且如果希望，编写面向对象的代码。 在第六章中，我们将通过查看 PHP 数组的工作方式来完成我们对 PHP 的初步探索。

# 问题

1.  使用函数的主要好处是什么？

1.  函数可以返回多少个值？

1.  通过名称和引用访问变量之间有什么区别？

1.  *作用域*在 PHP 中的含义是什么？

1.  如何在一个 PHP 文件中引用另一个？

1.  对象与函数有何不同？

1.  如何在 PHP 中创建一个新对象？

1.  使用什么语法可以从现有类创建一个子类？

1.  如何在创建对象时使其初始化？

1.  明确在类中声明属性是个好主意的原因是什么？

参见“第五章答案”，可以找到这些问题的答案。
