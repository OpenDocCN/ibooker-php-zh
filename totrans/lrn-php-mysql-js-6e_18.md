# 第十五章\. JavaScript 中的表达式和控制流

在前一章中，我介绍了 JavaScript 和 DOM 的基础知识。现在是时候看看如何在 JavaScript 中构建复杂的表达式，并通过使用条件语句来控制脚本的程序流了。

# 表达式

JavaScript 表达式与 PHP 中的表达式非常相似。正如您在第四章中学到的那样，表达式是值、变量、运算符和函数的组合，其结果是一个值；结果可以是数字、字符串或布尔值（评估为`true`或`false`）。

示例 15-1 展示了一些简单的表达式。对于每一行，它打印出`a`到`d`之间的字母，后跟一个冒号和表达式的结果。`<br>`标签用于创建换行并将输出分隔成四行（请记住，在 HTML5 中`<br>`和`<br />`都是可接受的，因此我选择使用前一种形式以简洁为主）。

##### 示例 15-1\. 四个简单的布尔表达式

```php
<script>
  document.write("a: " + (42 > 3) + "<br>")
  document.write("b: " + (91 < 4) + "<br>")
  document.write("c: " + (8 == 2) + "<br>")
  document.write("d: " + (4 < 17) + "<br>")
</script>
```

此代码的输出如下：

```php
a: true
b: false
c: false
d: true
```

注意，`a:`和`d:`两个表达式都评估为`true`，但`b:`和`c:`评估为`false`。与 PHP 不同（它将分别打印数字`1`和空白），实际的字符串`true`和`false`将被显示。

在 JavaScript 中，当您检查一个值是否为`true`或`false`时，除了以下值评估为`false`：字符串`false`本身，`0`，`-0`，空字符串，`null`，`undefined`和`NaN`（不是一个数字，计算机工程中用于非法浮点操作的结果，例如除以零）。

请注意，我在 JavaScript 中引用的`true`和`false`都是小写。这是因为，与 PHP 不同，这些值*必须*是小写的。因此，仅以下两个语句的第一个将会显示，打印小写单词`true`，因为第二个将导致`'TRUE'未定义`错误：

```php
if (1 == true) document.write('true') // True
if (1 == TRUE) document.write('TRUE') // Will cause an error
```

###### 注意

请记住，任何您希望在 HTML 文件中键入并尝试的代码片段都需要包含在`<script>`和`</script>`标签中。

# 文字和变量

表达式的最简单形式是*文字*，这意味着它评估为自身，例如数字`22`或字符串`Press Enter`。表达式也可以是一个变量，它评估为已分配给它的值。它们都是表达式的类型，因为它们返回一个值。

示例 15-2 展示了三种不同的文字和两个变量，所有这些都返回值，尽管类型不同。

##### 示例 15-2\. 五种文字类型

```php
<script>
  myname = "Peter"
  myage  = 24
  document.write("a: " + 42     + "<br>") // Numeric literal
  document.write("b: " + "Hi"   + "<br>") // String literal
  document.write("c: " + true   + "<br>") // Constant literal
  document.write("d: " + myname + "<br>") // String variable
  document.write("e: " + myage  + "<br>") // Numeric variable
</script>
```

而且，正如您所期望的那样，您将在以下输出中看到所有这些的返回值：

```php
a: 42
b: Hi
c: true
d: Peter
e: 24
```

运算符允许您创建更复杂的表达式，这些表达式评估为有用的结果。当您将赋值或控制流构造与表达式结合时，结果是一个*语句*。

示例 15-3 展示了其中的一个例子。第一个将表达式 `366 - day_number` 的结果赋给变量 `days_to_new_year`，第二个仅在表达式 `days_to_new_year < 30` 评估为 `true` 时输出友好消息。

##### 示例 15-3\. 两个简单的 JavaScript 语句

```php
<script>
  day_number       = 127  // For example
  days_to_new_year = 366 - day_number
  if (days_to_new_year < 30) document.write("It's nearly New Year")
  else                       document.write("It's a long time to go")
</script>
```

# 运算符

JavaScript 提供了许多强大的运算符，从算术、字符串和逻辑运算符到赋值、比较等（参见表 15-1）。

表 15-1\. JavaScript 运算符类型

| 运算符 | 描述 | 示例 |
| --- | --- | --- |
| 算术 | 基本数学 | `a + b` |
| 数组 | 数组操作 | `a + b` |
| 赋值 | 赋值 | `a = b + 23` |
| 位运算 | 在字节内操作位 | `12 ^ 9` |
| 比较 | 比较两个值 | `a < b` |
| 递增/递减 | 加或减一 | `a++` |
| 逻辑 | 布尔 | `a && b` |
| 字符串 | 连接 | `a + 'string'` |

每个运算符接受不同数量的操作数：

+   *一元* 运算符，如递增（`a++`）或否定（`-a`），只接受一个操作数。

+   *二元* 运算符占据了 JavaScript 运算符的大部分，包括加法、减法、乘法和除法，需要两个操作数。

+   *三元* 运算符，形式为 `? x : y`，需要三个操作数。它是一个简洁的单行 `if` 语句，根据第三个表达式选择两个表达式中的一个。

## 运算符优先级

像 PHP 一样，JavaScript 使用运算符优先级，其中表达式中的某些运算符先处理，因此首先进行评估。表 15-2 列出了 JavaScript 的运算符及其优先级。

表 15-2\. JavaScript 运算符优先级（从高到低）

| 运算符 | 类型 |
| --- | --- |
| `() [] .` | 括号、调用和成员 |
| `++ --` | 递增/递减 |
| `+ - ~ !` | 一元、位和逻辑 |
| `* / %` | 算术 |
| `+ -` | 算术和字符串 |
| `<< >> >>>` | 位运算 |
| `< > <= >=` | 比较 |
| `== != === !==` | 比较 |
| `& ^ &#124;` | 位运算 |
| `&&` | 逻辑 |
| `&#124;&#124;` | 逻辑 |
| `? :` | 三元 |
| `= += -= *= /= %=` | 赋值 |
| `<<= >>= >>>= &= ^= &#124;=` | 赋值 |
| `,` | 分隔符 |

## 结合性

大多数 JavaScript 运算符在等式中按照从左到右的顺序处理。但有些运算符需要从右到左进行处理。处理方向称为运算符的*结合性*。

当你没有明确强制运算优先级时，这种结合性变得很重要（顺便说一句，你应该总是这样做，因为它使代码更易读，减少错误）。例如，看看以下赋值运算符，这三个变量都被设置为值`0`：

```php
level = score = time = 0
```

仅当表达式的最右部分首先进行评估，然后以从右到左的顺序继续处理时，才能进行多重赋值。表 15-3](#operators_and_associativity)列出了 JavaScript 运算符及其结合性。

表 15-3\. 运算符及其结合性

| 运算符 | 描述 | 结合性 |
| --- | --- | --- |
| `++ --` | 增减 | 无 |
| `new` | 创建一个新对象 | 右 |
| `+ - ~ !` | 一元和位运算 | 右 |
| `?:` | 三元 | 右 |
| `= *= /= %= += -=` | 赋值 | 右 |
| `<<= >>= >>>= &= ^= &#124;=` | 赋值 | 右 |
| `,` | 分隔符 | 左 |
| `+ - * / %` | 算术 | 左 |
| `<< >> >>>` | 位运算 | 左 |
| `< <= > >= == != === !==` | 算术 | 左 |

## 关系运算符

*关系运算符*测试两个操作数并返回布尔结果`true`或`false`。有三种类型的关系运算符：*等式*、*比较*和*逻辑*。

### 相等运算符

等号运算符是`==`（不要与赋值运算符`=`混淆）。在示例 15-4 中，第一条语句赋值，第二条语句测试其是否相等。目前不会输出任何内容，因为`month`被赋予了字符串值`July`，因此检查其是否为`October`的条件将失败。

##### 示例 15-4\. 赋值和相等测试

```php
<script>
  month = "July"
  if (month == "October") document.write("It's the Fall")
</script>
```

如果相等表达式的两个操作数类型不同，JavaScript 将它们转换为最合理的类型。例如，任何完全由数字组成的字符串与数字比较时将被转换为数字。在示例 15-5 中，`a` 和 `b` 是两个不同的值（一个是数字，另一个是字符串），因此我们通常不希望任何`if`语句输出结果。

##### 示例 15-5\. 相等和恒等运算符

```php
<script>
  a = 3.1415927
  b = "3.1415927"
  if (a == b)  document.write("1")
  if (a === b) document.write("2")
</script>
```

然而，如果运行此示例，您将看到它输出数字`1`，这意味着第一个`if`语句评估为`true`。这是因为`b`的字符串值临时转换为数字，因此等式两侧都有数值`3.1415927`。

相比之下，第二个`if`语句使用*恒等*运算符，即连续三个等号，这会阻止 JavaScript 自动转换类型。因此，发现`a`和`b`是不同的，因此不会输出任何内容。

与强制运算符优先级类似，每当您对 JavaScript 如何转换操作数类型感到困惑时，可以使用恒等运算符关闭此行为。

### 比较运算符

使用比较运算符，你不仅可以测试相等和不等，JavaScript 还为你提供了`>`（大于）、`<`（小于）、`>=`（大于或等于）和`<=`（小于或等于）进行使用。示例 15-6 展示了这些运算符的使用。

##### 示例 15-6\. 四个比较运算符

```php
<script>
  a = 7; b = 11
  if (a > b)  document.write("a is greater than b<br>")
  if (a < b)  document.write("a is less than b<br>")
  if (a >= b) document.write("a is greater than or equal to b<br>")
  if (a <= b) document.write("a is less than or equal to b<br>")
</script>
```

在这个例子中，当`a`为`7`且`b`为`11`时，输出如下（因为 7 小于 11 且也小于等于 11）：

```php
a is less than b
a is less than or equal to b
```

### 逻辑运算符

逻辑运算符会产生`true`或`false`结果，也被称为*布尔*运算符。JavaScript 中有三种逻辑运算符（见表 15-4）。

表 15-4\. JavaScript 的逻辑运算符

| 逻辑运算符 | 描述 |
| --- | --- |
| `&&` *(and)* | 如果两个操作数都为`true`，则返回`true`。 |
| `&#124;&#124;` *(or)* | 如果任一操作数为`true`，则返回`true`。 |
| `!` *(not)* | 如果操作数为`false`，则返回`true`；如果操作数为`true`，则返回`false`。 |

你可以看到这些如何在示例 15-7 中使用，该示例输出`0`、`1`和`true`。

##### 示例 15-7\. 逻辑运算符的使用

```php
<script>
  a = 1; b = 0
  document.write((a && b) + "<br>")
  document.write((a || b) + "<br>")
  document.write((  !b  ) + "<br>")
</script>
```

`&&`语句要求两个操作数都为`true`才会返回`true`，`||`语句在任一值为`true`时会返回`true`，第三个语句对`b`的值执行`NOT`操作，将其从`0`转换为`true`。

`||`运算符可能会引起意外问题，因为如果第一个操作数被评估为`true`，则第二个操作数将不会被评估。在示例 15-8 中，如果`finished`的值为`1`，则`getnext`函数将永远不会被调用（这些仅为示例，`getnext`的具体行为与此解释无关——只需将其视为在调用时执行某些操作的函数）。

##### 示例 15-8\. 使用`||`运算符的语句

```php
<script>
  if (finished == 1 || getnext() == 1) done = 1
</script>
```

如果你需要在每个`if`语句中调用`getnext`，你应该按照示例 15-9 重新编写代码。

##### 示例 15-9\. 修改后的`if...or`语句以确保调用`getnext`

```php
<script>
  gn = getnext()
  if (finished == 1 OR gn == 1) done = 1;
</script>
```

在这种情况下，在`if`语句之前，将执行`getnext`函数的代码，并将其返回值存储在`gn`中。

表 15-5 展示了使用逻辑运算符的所有可能变体。你还应该注意，`!true`等于`false`，`!false`等于`true`。

表 15-5\. 所有可能的逻辑表达式

| 输入 | 运算符及结果 |
| --- | --- |
| `a` | `b` | `&&` | `&#124;&#124;` |
| --- | --- | --- | --- |
| `true` | `true` | `true` | `true` |
| `true` | `false` | `false` | `true` |
| `false` | `true` | `false` | `true` |
| `false` | `false` | `false` | `false` |

# `with`语句

`with`语句不是您在早期关于 PHP 的章节中看到的语句，因为它是 JavaScript 专用的，并且是您需要了解但不应使用的语句（见???）。使用它，您可以通过减少对一个对象的多次引用来简化某些类型的 JavaScript 语句，将`with`块内的属性和方法引用视为适用于该对象。

例如，看看示例 15-10 中的代码，其中`document.write`函数从未直接引用`string`变量名。

##### 示例 15-10\. 使用`with`语句

```php
<script>
  string = "The quick brown fox jumps over the lazy dog"

  with (string)
  {
    document.write("The string is " + length + " characters<br>")
    document.write("In upper case it's: " + toUpperCase())
  }
</script>
```

即使`string`从未被`document.write`直接引用，此代码仍然成功输出以下内容：

```php
The string is 43 characters
In upper case it's: THE QUICK BROWN FOX JUMPS OVER THE LAZY DOG
```

代码工作方式如下：JavaScript 解释器识别出`length`属性和`toUpperCase`方法必须应用于某个对象。因为它们单独存在，解释器假定它们适用于您在`with`语句中指定的`string`对象。

###### 注意

在 ECMAScript 5 严格模式中，不再推荐使用`with`，并且现已禁止。推荐的替代方法是将要访问其属性的对象分配给临时变量。请注意这一点，以便在看到其他人的代码中使用时进行更新（如果需要），但不要自己使用。

# 使用`onerror`

使用`onerror`事件或`try`和`catch`关键字的组合，可以捕获 JavaScript 错误并自行处理。

*事件*是 JavaScript 可以检测到的操作。网页上的每个元素都有特定的事件，可以触发 JavaScript 函数。例如，按钮元素的`onclick`事件可以设置为调用一个函数，并使其在用户点击按钮时运行。

示例 15-11 说明了如何使用`onerror`事件。

##### 示例 15-11\. 使用`onerror`事件的脚本

```php
<script>
  onerror = errorHandler
  document.writ("Welcome to this website") // Deliberate error

  function errorHandler(message, url, line)
  {
    out  = "Sorry, an error was encountered.\n\n";
    out += "Error: " + message + "\n";
    out += "URL: "   + url     + "\n";
    out += "Line: "  + line    + "\n\n";
    out += "Click OK to continue.\n\n";
    alert(out);
    return true;
  }
</script>
```

此脚本的第一行告诉错误事件从现在开始使用新的`errorHandler`函数。此函数接受三个参数——`message`、`url`和`line`编号——因此可以简单地在警报弹出窗口中显示所有这些内容。

然后，为了测试新函数，我们故意在代码中引入一个语法错误，调用`document.writ`而不是`document.write`（最后一个`e`丢失）。图 15-1 展示了在浏览器中运行此脚本的结果。在调试过程中，使用`onerror`这种方式也非常有用。

![使用`onerror`事件与警报方法弹出窗口](img/pmj6_1501.png)

###### 图 15-1\. 使用带有警报方法弹出窗口的`onerror`事件

# 使用 try...catch

`try`和`catch`关键字比前一节中展示的`onerror`技术更加标准和灵活。这些关键字允许您捕获代码中特定部分的错误，而不是文档中所有脚本的错误。然而，它们无法捕获语法错误，这种错误需要使用`onerror`。

`try...catch`结构被所有主要浏览器支持，当您希望捕获特定部分代码中可能发生的某个条件时，它非常方便。

例如，在第十八章中，我们将探讨利用`XMLHttpRequest`对象的 Ajax 技术。因此，我们可以使用`try`和`catch`来捕捉这种情况，并在函数不可用时执行其他操作。示例 15-12 展示了如何操作。

##### 示例 15-12\. 使用`try`和`catch`捕获错误

```php
<script>
  try
  {
    request = new XMLHTTPRequest()
  }
  catch(err)
  {
    // Use a different method to create an XMLHTTPRequest object
  }
</script>
```

`try`和`catch`之外，还有一个与之关联的关键字叫做`finally`，无论在`try`子句中是否发生错误，它都会始终被执行。要使用它，只需在`catch`语句后添加如下语句：

```php
finally
{
  alert("The 'try' clause was encountered")
}
```

# 条件语句

条件语句可以改变程序流程。它们使您能够针对某些事物提出问题，并根据所得到的答案以不同方式做出响应。非循环条件语句有三种类型：`if`语句，`switch`语句和`?`运算符。

## `if`语句

本章中的几个示例已经使用了`if`语句。只有在给定表达式评估为`true`时，才会执行此类语句中的代码。多行`if`语句需要用大括号括起来，但与 PHP 一样，对于单个语句，您可以省略大括号，尽管在编写代码时，特别是在`if`语句内部的操作数量可能会随着开发的进行而改变时，使用大括号通常是一个好习惯。因此，以下语句是有效的：

```php
if (a > 100)
{
  b=2
  document.write("a is greater than 100")
}

if (b == 10) document.write("b is equal to 10")
```

## `else`语句

当条件未被满足时，您可以使用`else`语句执行替代操作，如下所示：

```php
if (a > 100)
{
  document.write("a is greater than 100")
}
else
{
  document.write("a is less than or equal to 100")
}
```

不像 PHP，JavaScript 没有`elseif`语句，但这并不是问题，因为您可以使用`else`后面再跟一个`if`来实现类似于`elseif`语句的效果，如下所示：

```php
if (a > 100)
{
  document.write("a is greater than 100")
}
else if(a < 100)
{
  document.write("a is less than 100")
}
else
{
  document.write("a is equal to 100")
}
```

正如您所见，您可以在新的`if`后面再使用一个`else`，同样可以跟随另一个`if`语句，以此类推。虽然我在这些语句中使用了大括号，因为每个语句都是单行的，前面的示例也可以写成如下形式：

```php
if     (a > 100) document.write("a is greater than 100")
else if(a < 100) document.write("a is less than 100")
else             document.write("a is equal to 100")
```

## `switch`语句

当一个变量或表达式的结果可以有多个值时，`switch`语句非常有用，您可以针对每个值执行不同的函数。

例如，下面的代码将我们在第四章中组合的 PHP 菜单系统转换为 JavaScript。它通过根据用户的输入传递一个字符串给主菜单代码来工作，并将变量`page`设置为其中之一：Home、About、News、Login 和 Links。

使用`if...else if...`编写的代码看起来可能类似于示例 15-13。

##### 示例 15-13\. 多行的`if...else if...`语句

```php
<script>
  if      (page == "Home")  document.write("You selected Home")
  else if (page == "About") document.write("You selected About")
  else if (page == "News")  document.write("You selected News")
  else if (page == "Login") document.write("You selected Login")
  else if (page == "Links") document.write("You selected Links")
</script>
```

使用`switch`结构，代码可能看起来像示例 15-14。

##### 示例 15-14\. 一个`switch`结构

```php
<script>
  switch (page)
  {
    case "Home":
      document.write("You selected Home")
      break
    case "About":
      document.write("You selected About")
      break
    case "News":
      document.write("You selected News")
      break
    case "Login":
      document.write("You selected Login")
      break
    case "Links":
      document.write("You selected Links")
      break
  }
</script>
```

变量`page`仅在`switch`语句的开头提到一次。此后，`case`命令检查匹配项。当匹配发生时，执行相应的条件语句。当然，一个真实的程序将在这里有代码来显示或跳转到一个页面，而不仅仅是告诉用户选择了什么。

###### 注意

你也可以为单个操作提供多个情况。例如：

```php
switch (heroName)
{
  case "Superman":
  case "Batman":
  case "Wonder Woman":
    document.write("Justice League")
    break
  case "Iron Man":
  case "Captain America":
  case "Spiderman":
    document.write("The Avengers")
    break
}
```

### 跳出

正如你在示例 15-14 中所看到的，与 PHP 一样，`break`命令允许你的代码在满足条件后跳出`switch`语句。请记住包含`break`，除非你希望继续执行下一个`case`下的语句。

### 默认操作

当没有任何条件被满足时，你可以使用`default`关键字为`switch`语句指定默认操作。示例 15-15 展示了一个可以插入到示例 15-14 中的代码片段。

##### 示例 15-15\. 添加到示例 15-14 的默认语句

```php
default:
  document.write("Unrecognized selection")
  break
```

## ？操作符

`ternary`操作符（`?`），结合`:`字符，提供了一种快速进行`if...else`测试的方式。你可以编写一个表达式进行评估，然后跟随一个`?`符号和在表达式为`true`时要执行的代码。之后，放置一个`:`和在表达式评估为`false`时要执行的代码。

示例 15-16 展示了使用三元操作符来打印变量`a`是否小于或等于 5，并在任何情况下打印出一些内容。

##### 示例 15-16\. 使用三元操作符的例子

```php
<script>
  document.write(
    a <= 5 ?
    "a is less than or equal to 5" :
    "a is greater than 5"
  )
</script>
```

为了清晰起见，该语句已被分成多行，但你更可能在单行中使用这样的语句，如下所示：

```php
size = a <= 5 ? "short" : "long"
```

# 循环

当涉及到循环时，JavaScript 和 PHP 之间有许多相似之处。两种语言都支持`while`、`do...while`和`for`循环。

## while 循环

JavaScript 的`while`循环首先检查表达式的值，并仅在该表达式为`true`时开始执行循环内的语句。如果为`false`，执行将跳过到下一个 JavaScript 语句（如果有）。

在完成循环的迭代后，表达式再次被测试以查看它是否为`true`，并且进程将继续直到表达式评估为`false`或执行被另行停止。示例 15-17 展示了这样的循环。

##### 示例 15-17\. `while`循环

```php
<script>
  counter=0

  `while` `(``counter` `<` `5``)`
  `{`
    document.write("Counter: " + counter + "<br>")
    ++counter
  `}`
</script>
```

此脚本输出以下内容：

```php
Counter: 0
Counter: 1
Counter: 2
Counter: 3
Counter: 4
```

###### 警告

如果在循环内部不增加变量`counter`，某些浏览器可能会因为无限循环而变得无响应，甚至可能无法通过 Esc 键或停止按钮轻松终止页面。所以，请小心处理你的 JavaScript 循环。

## `do...while`循环

当你需要在进行任何测试之前至少执行一次循环时，请使用`do...while`循环，它类似于`while`循环，只是测试表达式仅在每次循环迭代后检查。因此，要输出 7 的乘法表的前七个结果，你可以使用类似于示例 15-18 中的代码。

##### 示例 15-18\. `do...while`循环

```php
<script>
  count = 1

  `do`
  `{`
    document.write(count + " times 7 is " + count * 7 + "<br>")
  `}` `while` `(``++``count` `<=` `7``)`
</script>
```

正如你所预料的，此循环输出以下内容：

```php
1 times 7 is 7
2 times 7 is 14
3 times 7 is 21
4 times 7 is 28
5 times 7 is 35
6 times 7 is 42
7 times 7 is 49
```

## `for`循环

`for`循环将所有优点结合到单个循环结构中，允许你为每个语句传递三个参数：

+   一个初始化表达式

+   一个条件表达式

+   一个修改表达式

这些由分号分隔，如下所示：`for (`*`expr1`* `;` *`expr2`* `;` *`expr3`*`)`。初始化表达式在循环的第一次迭代开始时执行。对于用于输出 7 的乘法表的代码，`count`将被初始化为值`1`。然后，每次循环时，条件表达式（在本例中为`count <= 7`）被测试，只有当条件为`true`时才进入循环。最后，在每次迭代结束时，修改表达式被执行。对于用于输出 7 的乘法表的代码，变量`count`被递增。示例 15-19 展示了代码的样子。

##### 示例 15-19\. 使用`for`循环

```php
<script>
  `for` `(``count` `=` `1` `;` `count` `<=` `7` `;` `++``count``)`
  `{`
    document.write(count + "times 7 is " + count * 7 + "<br>");
  `}`
</script>
```

就像在 PHP 中一样，你可以在`for`循环的第一个参数中使用逗号分隔多个变量，如下所示：

```php
for (i = 1, j = 1 ; i < 10 ; i++)
```

同样，你可以在最后一个参数中执行多个修改，如下所示：

```php
for (i = 1 ; i < 10 ; i++, --j)
```

或者你可以同时执行两者：

```php
for (i = 1, j = 1 ; i < 10 ; i++, --j)
```

## 退出循环

`break`命令，你可能还记得在`switch`语句内部很重要，在`for`循环中也是可用的。例如，当搜索某种匹配时可能需要使用此功能。一旦找到匹配，你就知道继续搜索只会浪费时间并让访问者等待。示例 15-20 展示了如何使用`break`命令。

##### Example 15-20\. 在`for`循环中使用`break`命令

```php
<script>
  haystack     = new Array()
  haystack[17] = "Needle"

  for (j = 0 ; j < 20 ; ++j)
  {
    if (haystack[j] == "Needle")
    {
      document.write("<br>- Found at location " + j)
      `break`
    }
    else document.write(j + ", ")
  }
</script>
```

此脚本输出以下内容：

```php
0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,
- Found at location 17
```

## continue 语句

有时，你不想完全退出循环，而是希望跳过本次循环中的其余语句。在这种情况下，你可以使用 `continue` 命令。示例 15-21 展示了其使用方法。

##### 示例 15-21\. 在 `for` 循环中使用 `continue` 命令

```php
<script>
  haystack     = new Array()
  haystack[4]  = "Needle"
  haystack[11] = "Needle"
  haystack[17] = "Needle"

  for (j = 0 ; j < 20 ; ++j)
  {
    if (haystack[j] == "Needle")
    {
      document.write("<br>- Found at location " + j + "<br>")
      `continue`
    }

    document.write(j + ", ")
  }
</script>
```

请注意第二个 `document.write` 调用不必像以前那样被包含在 `else` 语句中，因为 `continue` 命令将在找到匹配项时跳过它。该脚本的输出如下：

```php
0, 1, 2, 3,
- Found at location 4
5, 6, 7, 8, 9, 10,
- Found at location 11
12, 13, 14, 15, 16,
- Found at location 17
18, 19,
```

# 显式类型转换

不像 PHP，JavaScript 没有显式类型转换，比如 `(int)` 或 `(float)`。相反，当你需要一个值是特定类型时，使用 JavaScript 内置的函数之一，如 表 15-6 中所示。

表 15-6\. JavaScript 类型转换函数

| 类型更改 | 使用的函数 |
| --- | --- |
| `Int`, `Integer` | `parseInt()` |
| `Bool`, `Boolean` | `Boolean()` |
| `Float`, `Double`, `Real` | `parseFloat()` |
| `String` | `String()` |
| `Array` | `split()` |

因此，例如，要将浮点数转换为整数，可以使用以下代码（显示值为 `3`）：

```php
n = 3.1415927
i = parseInt(n)
document.write(i)
```

或者你可以使用复合形式：

```php
document.write(parseInt(3.1415927))
```

控制流和表达式就是这些内容。下一章将重点讲解 JavaScript 中函数、对象和数组的使用。

# 问题

1.  PHP 和 JavaScript 如何处理布尔值？

1.  JavaScript 变量名的定义要用到哪些字符？

1.  一元、二元和三元操作符有什么区别？

1.  如何强制自己的运算符优先级？

1.  何时会使用 `===`（恒等）运算符？

1.  什么是最简单的两种表达式形式？

1.  列举三种条件语句类型的名称。

1.  `if` 和 `while` 语句如何解释不同数据类型的条件表达式？

1.  为什么 `for` 循环比 `while` 循环更强大？

1.  `with` 语句的目的是什么？

参见 “第十五章答案” ，在 附录 A 中找到这些问题的答案。
