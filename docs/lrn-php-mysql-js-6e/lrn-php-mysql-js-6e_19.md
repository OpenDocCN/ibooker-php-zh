# 第十六章：JavaScript 函数、对象和数组

就像 PHP 一样，JavaScript 提供了访问函数和对象的方法。事实上，JavaScript 实际上是基于对象的，因为——正如你已经看到的——它必须访问 DOM，这使得 HTML 文档的每个元素都可以作为一个对象进行操作。

使用和语法也与 PHP 非常相似，因此当我带你深入了解 JavaScript 中的函数和对象使用以及数组处理时，你应该会感到很自在。

# JavaScript 函数

除了访问数十个内置函数（或方法），如 `write`，你已经在 `document.write` 中看到它被使用，你还可以轻松创建自己的函数。每当你有一个可能会被重复使用的相对复杂的代码片段时，你就可以考虑创建一个函数。

## 定义函数

函数的一般语法如下所示：

```php
function *`function_name`*([*`parameter`* [, ...]])
{
  *`statements`*
}
```

语法的第一行指示以下内容：

+   定义从 `function` 这个词开始。

+   名称必须以字母或下划线开头，后跟任意数量的字母、数字、美元符号或下划线。

+   括号是必需的。

+   可选一个或多个用逗号分隔的参数（由方括号表示，不是函数语法的一部分）。

函数名区分大小写，因此以下所有字符串都指代不同的函数：`getInput`、`GETINPUT` 和 `getinput`。

在 JavaScript 中，函数有一个通用的命名约定：名称中每个单词的第一个字母大写，除了第一个字母外，其他字母都小写。因此，在前面的例子中，大多数程序员会选择使用 `getInput` 作为首选名称。这种约定通常被称为 *bumpyCaps*、*bumpyCase* 或（最常见的）*camelCase*。

大括号 `{` 开始定义函数调用时执行的语句；必须有相匹配的大括号 `}` 结束它。这些语句可能包括一个或多个 `return` 语句，它们强制函数停止执行并返回给调用代码。如果 `return` 语句附带一个值，调用代码可以检索它。

### `arguments` 数组

`arguments` 数组是每个函数的成员。借助它，你可以确定传递给函数的变量数量及其内容。以函数 `displayItems` 为例。例子 16-1 展示了一种编写它的方式。

##### 例子 16-1\. 定义函数

```php
<script>
  displayItems("Dog", "Cat", "Pony", "Hamster", "Tortoise")

  function displayItems(v1, v2, v3, v4, v5)
  {
    document.write(v1 + "<br>")
    document.write(v2 + "<br>")
    document.write(v3 + "<br>")
    document.write(v4 + "<br>")
    document.write(v5 + "<br>")
  }
</script>
```

当你在浏览器中调用这个脚本时，它将显示以下内容：

```php
Dog
Cat
Pony
Hamster
Tortoise
```

这些都很好，但如果你想向函数传递超过五个项目会怎样？此外，多次重复使用`document.write`调用而不使用循环是一种浪费。幸运的是，`arguments`数组提供了灵活性，可以处理可变数量的参数。示例 16-2 展示了如何以更高效的方式重写先前的示例。

##### 示例 16-2\. 修改使用`arguments`数组的函数

```php
<script>
  let c = "Car"

  displayItems("Bananas", 32.3, c)

  function displayItems()
  {
    for (j = 0 ; j < displayItems.arguments.length ; ++j)
      document.write(displayItems.arguments[j] + "<br>")
  }
</script>

```

注意使用了`length`属性，你已经在上一章中遇到过它，并且我使用变量`j`作为偏移量引用数组`displayItems.arguments`。我还选择通过不用花括号将`for`循环的内容包围起来来保持函数简短而简洁，因为它只包含一个语句。请记住，循环必须在`j`等于`length`之前停止，而不是等于`length`。

使用这种技术，你现在有一个函数，可以接受任意多（或少）的参数，并按照你的意愿对每个参数进行操作。

## 返回值

函数不仅仅用于显示事物。事实上，它们主要用于进行计算或数据操作，然后返回结果。示例 16-3 中的函数`fixNames`使用了`arguments`数组（在前一节中讨论过），接收传递给它的一系列字符串，并将它们返回为一个单一的字符串。它执行的“修复”操作是将每个参数中的每个字符转换为小写，除了每个参数的第一个字符，它设置为大写字母。

##### 示例 16-3\. 清理完整名称

```php
<script>
  document.write(fixNames("the", "DALLAS", "CowBoys"))

  function fixNames()
  {
    var s = ""

    for (j = 0 ; j < fixNames.arguments.length ; ++j)
      s += fixNames.arguments[j].charAt(0).toUpperCase() +
           fixNames.arguments[j].substr(1).toLowerCase() + " "

    return s.substr(0, s.length-1)
  }
</script>
```

例如，当使用参数`the`、`DALLAS`和`CowBoys`调用时，该函数返回字符串`The Dallas Cowboys`。让我们来看看这个函数。

首先将临时（和局部）变量`s`初始化为空字符串。然后，`for`循环遍历传递的每个参数，使用`charAt`方法隔离参数的第一个字符，并使用`toUpperCase`方法将其转换为大写。此示例中显示的各种方法都内置于 JavaScript 中，并默认可用。

然后使用`substr`方法获取每个字符串的其余部分，通过`toLowerCase`方法将其转换为小写。这里`substr`方法的更完整版本将指定作为第二个参数的子串的长度：

```php
substr(1, (arguments[j].length) - 1 )
```

换句话说，这个`substr`方法说，“从位置 1（第二个字符）开始，并返回剩余的字符串（长度减一）。”尽管如此，`substr`方法会假定，如果你省略第二个参数，你希望返回剩余的字符串。

在整个参数转换为所需大小写之后，向末尾添加空格字符，并将结果追加到临时变量`s`。

最后，再次使用 `substr` 方法返回变量 `s` 的内容，除了最后一个不需要的空格。我们通过使用 `substr` 返回字符串直到最后一个字符之前来移除它。

这个例子特别有趣，因为它展示了在单个表达式中使用多个属性和方法，例如：

```php
fixNames.arguments[j].substr(1).toLowerCase()
```

您必须在脑海中将该语句分成几个部分来解释。JavaScript 从左到右评估语句的这些元素如下：

1.  从函数名称本身开始：`fixNames`。

1.  从代表 `fixNames` 参数的数组 `arguments` 中提取元素 `j`。

1.  调用 `substr` 并使用参数 `1` 提取元素。这将除第一个字符外的所有内容传递给表达式的下一部分。

1.  对已传递的字符串应用 `toLowerCase` 方法。

这种做法通常被称为 *方法链*。例如，如果将字符串 `mixedCASE` 传递给示例表达式，它将经历以下转换：

```php
mixedCASE
ixedCASE
ixedcase
```

换句话说，`fixNames.arguments[j]` 生成 “mixedCASE”，然后 `substr(1)` 获取 “mixedCASE” 并生成 “ixedCASE”，最后 `toLowerCase()` 获取 “ixedCASE” 并生成 “ixedcase”。

最后提醒一下：函数内部创建的 `s` 变量是局部的，因此无法在函数外部访问。通过在 `return` 语句中返回 `s`，我们使其值可以被调用者使用或存储。但是 `s` 本身在函数结束时会消失。虽然我们可以让函数操作全局变量（有时是必要的），但最好的方法是只返回想要保留的值，并让 JavaScript 清理函数使用的所有其他变量。

## 返回一个数组

在 示例 16-3 中，该函数仅返回一个参数——但如果需要返回多个参数怎么办？您可以通过返回一个数组来实现，就像 示例 16-4 中那样。

##### 示例 16-4\. 返回一个值数组

```php
<script>
  words = fixNames("the", "DALLAS", "CowBoys")

  for (j = 0 ; j < words.length ; ++j)
    document.write(words[j] + "<br>")

  function fixNames()
  {
    var s = new Array()

    for (j = 0 ; j < fixNames.arguments.length ; ++j)
      s[j] = fixNames.arguments[j].charAt(0).toUpperCase() +
             fixNames.arguments[j].substr(1).toLowerCase()

  return s
}
</script>
```

这里变量 `words` 自动定义为数组，并用函数 `fixNames` 的返回结果填充。然后 `for` 循环迭代数组并显示每个成员。

至于 `fixNames` 函数，它与 示例 16-3 几乎完全相同，只是现在变量 `s` 是一个数组；在处理每个单词后，它作为数组的一个元素存储，并通过 `return` 语句返回。

此函数允许从其返回值中提取单个参数，例如下面的示例（其输出仅为 `The Cowboys`）：

```php
words = fixNames("the", "DALLAS", "CowBoys")
document.write(words[0] + " " + words[2])
```

# JavaScript 对象

JavaScript 对象比变量更高级，变量一次只能包含一个值。相比之下，对象可以包含多个值，甚至函数。对象将数据与操作数据所需的函数组合在一起。

## 声明一个类

在创建用于使用对象的脚本时，您需要设计称为*类*的数据和代码组合。基于此类的每个新对象称为该类的*实例*（或*发生*）。正如您已经看到的那样，与对象关联的数据称为其*属性*，而其使用的函数称为*方法*。

让我们看看如何为称为`User`的对象声明类，该对象将包含有关当前用户的详细信息。要创建类，只需编写一个以类名命名的函数。此函数可以接受参数（稍后将展示如何调用它），并且可以为该类中的对象创建属性和方法。这个函数称为*构造函数*。

示例 16-5 显示了用于`User`类的构造函数，具有三个属性：`forename`、`username`和`password`。该类还定义了`showUser`方法。

##### 示例 16-5. 声明`User`类及其方法

```php
<script>
  function User(forename, username, password)
  {
    this.forename = forename
    this.username = username
    this.password = password

    this.showUser = function()
    {
      document.write("Forename: " + this.forename + "<br>")
      document.write("Username: " + this.username + "<br>")
      document.write("Password: " + this.password + "<br>")
    }
  }
</script>
```

该函数在几个方面与我们迄今看到的其他函数不同：

+   每次调用函数时，它都会创建一个新对象。因此，您可以反复调用相同的函数并使用不同的参数来创建具有不同名字的用户，例如。

+   函数引用一个名为`this`的对象，该对象引用正在创建的实例。如示例所示，对象使用名称`this`来设置自己的属性，这些属性将与另一个`User`不同。

+   在函数内创建了一个名为`showUser`的新函数。此处显示的语法是新的且相当复杂的，但其目的是将`showUser`与`User`类关联起来。因此，`showUser`作为`User`类的方法而存在。

我使用的命名约定是将所有属性保持为小写，并在方法名称中至少使用一个大写字符，遵循本章前面提到的驼峰命名约定。

示例 16-5 遵循编写类构造函数的推荐方式，即在构造函数中包含方法。但是，您还可以参考在构造函数外定义的函数，如示例 16-6。

##### 示例 16-6. 分别定义类和方法

```php
<script>
  function User(forename, username, password)
  {
    this.forename = forename
    this.username = username
    this.password = password
    this.showUser = showUser
  }

  function showUser()
  {
    document.write("Forename: " + this.forename + "<br>")
    document.write("Username: " + this.username + "<br>")
    document.write("Password: " + this.password + "<br>")
  }
</script>
```

我向您展示这种形式，因为您在查看其他程序员的代码时一定会遇到它。

## 创建对象

要创建`User`类的实例，可以使用如下语句：

```php
details = new User("Wolfgang", "w.a.mozart", "composer")
```

或者您可以创建一个空对象，如下所示：

```php
details = new User()
```

然后稍后再填充它，如下所示：

```php
details.forename = "Wolfgang"
details.username = "w.a.mozart"
details.password = "composer"
```

您还可以像这样向对象添加新属性：

```php
details.greeting = "Hello"
```

您可以验证添加这种新属性是否有效，使用以下语句：

```php
document.write(details.greeting)
```

## 访问对象

要访问对象，你可以引用其属性，如以下两个不相关的示例语句：

```php
name = details.forename
if (details.username == "Admin") loginAsAdmin()
```

因此，要访问类`User`的对象的`showUser`方法，你将使用以下语法，其中对象`details`已经被创建并填充了数据：

```php
details.showUser()
```

假设之前提供的数据，这段代码将显示如下内容：

```php
Forename: Wolfgang
Username: w.a.mozart
Password: composer
```

## `prototype`关键字

使用`prototype`关键字可以节省大量内存。在`User`类中，每个实例都包含三个属性和一个方法。因此，如果内存中有一千个这些对象，方法`showUser`也会被复制一千次。然而，因为方法在每种情况下都是相同的，你可以指定新对象应该引用方法的单个实例，而不是创建它的副本。因此，不要在类构造函数中使用以下内容：

```php
this.showUser = function()
```

你可以用这个替换它：

```php
User.prototype.showUser = function()
```

示例 16-7 显示了新构造函数的样子。

##### 示例 16-7. 使用`prototype`关键字声明类的方法

```php
<script>
  function User(forename, username, password)
  {
    this.forename = forename
    this.username = username
    this.password = password

    User.prototype.showUser = function()
    {
      document.write("Forename: " + this.forename + "<br>")
      document.write("Username: " + this.username + "<br>")
      document.write("Password: " + this.password + "<br>")
    }
  }
</script>
```

这是因为所有函数都有一个`prototype`属性，用于保存类中任何对象创建时不会复制的属性和方法。相反，它们通过引用传递给其对象。

这意味着你可以随时添加`prototype`属性或方法，并且所有对象（即使已经创建）都会继承它，如下面的语句所示：

```php
User.prototype.greeting = "Hello"
document.write(details.greeting)
```

第一个语句将值为`Hello`的`greeting`属性添加到类`User`的`prototype`属性中。在第二行中，已经创建的对象`details`正确显示了这个新属性。

你还可以添加或修改类中的方法，如下面的语句所示：

```php
User.prototype.showUser = function()
{
  document.write("Name "  + this.forename +
                 " User " + this.username +
                 " Pass " + this.password)
}

details.showUser()
```

你可以在脚本中的条件语句（如`if`）中添加这些行，这样它们将在用户活动导致你需要不同的`showUser`方法时运行。这些行运行后，即使已经创建了对象`details`，对`details.showUser`的进一步调用也会运行新函数。旧的`showUser`定义已被擦除。

### 静态方法和属性

在阅读关于 PHP 对象时，你学到了类不仅可以具有与类的特定实例相关联的静态属性和方法，还可以具有静态属性和方法。JavaScript 也支持静态属性和方法，你可以方便地从类的`prototype`中存储和检索它们。因此，以下语句设置并从`User`中读取静态字符串：

```php
User.prototype.greeting = "Hello"
document.write(User.prototype.greeting)
```

### 扩展 JavaScript 对象

`prototype`关键字甚至让你能够向内置对象添加功能。例如，假设你想要添加一个功能，将字符串中的所有空格替换为不换行空格以防止其换行。你可以通过向 JavaScript 默认的`String`对象定义添加一个原型方法来实现这一点，就像这样：

```php
String.prototype.nbsp = function()
{
  return this.replace(/ /g, '&nbsp;')
}
```

这里使用了`replace`方法和正则表达式来查找并替换所有单个空格为字符串`&nbsp;`。

###### 注意

如果您还不熟悉正则表达式，它们是一种从字符串中提取信息或操作字符串的方便方法，并且在 第十七章 中有详细解释。总之，目前您可以复制并粘贴上述示例，并按描述的方式运行它们，展示了扩展 JavaScript `String` 对象的强大功能。

如果您输入以下命令：

```php
document.write("The quick brown fox".nbsp())
```

它将输出字符串`The&nbsp;quick&nbsp;brown&nbsp;fox`。或者你可以添加一个方法，用来从字符串中删除前导和尾随空格（再次使用正则表达式）：

```php
String.prototype.trim = function()
{
  return this.replace(/^\s+|\s+$/g, '')
}
```

如果您发出以下语句，输出将是字符串`Please trim me`（删除了前导和尾随空格）：

```php
document.write("  Please trim me    ".trim())
```

如果我们将表达式分解为其组成部分，两个`/`字符标记了表达式的开始和结束，最后的`g`指定了全局搜索。在表达式内部，`^\s+`部分搜索出现在搜索字符串开头的一个或多个空白字符，而`\s+$`部分搜索出现在搜索字符串结尾的一个或多个空白字符。中间的`|`字符用于分隔替代项。

结果是，当任何一个表达式匹配时，匹配项将被替换为空字符串，从而返回一个没有任何前导或尾随空白的修剪过的字符串版本。

###### 警告

关于是否扩展对象是一种好还是坏的做法存在争议。一些程序员认为，如果稍后将对象扩展为正式提供您已添加的功能，可以以另一种方式实现，或者执行与您的扩展完全不同的操作，这可能会导致冲突。然而，其他程序员，如 JavaScript 的发明者 Brendan Eich，认为这是完全可以接受的做法。我倾向于同意后者的观点，但在生产代码中选择最不可能被正式使用的扩展名称。例如，`trim` 扩展可以重命名为 `mytrim`，支持代码可能更安全地编写如下：

```php
String.prototype.mytrim = function()
{
  return this.replace(/^\s+|\s+$/g, '')
}
```

# JavaScript 数组

JavaScript 中的数组处理与 PHP 非常相似，尽管语法有些不同。尽管如此，鉴于您已经学到的关于数组的所有知识，这一部分对您来说应该相对简单。

## 数字数组

要创建一个新数组，请使用以下语法：

```php
arrayname = new Array()
```

或者您可以使用简写形式，如下所示：

```php
arrayname = []
```

### 分配元素值

在 PHP 中，您可以通过简单地分配而不指定元素偏移来向数组添加新元素，如下所示：

```php
$arrayname[] = "Element 1";
$arrayname[] = "Element 2";
```

但是在 JavaScript 中，您使用`push`方法来实现相同的操作，就像这样：

```php
arrayname.push("Element 1")
arrayname.push("Element 2")
```

这允许您在不必跟踪项目数量的情况下继续向数组中添加项目。当您需要知道数组中有多少元素时，可以使用 `length` 属性，如下所示：

```php
document.write(arrayname.length)
```

或者，如果你希望自己跟踪元素的位置并将它们放在特定位置，可以使用以下语法：

```php
arrayname[0] = "Element 1"
arrayname[1] = "Element 2"
```

示例 16-8 展示了创建数组、加载值和显示值的简单脚本。

##### 示例 16-8\. 创建、构建和打印一个数组

```php
<script>
  numbers = []
  numbers.push("One")
  numbers.push("Two")
  numbers.push("Three")

  for (j = 0 ; j < numbers.length ; ++j)
    document.write("Element " + j + " = " + numbers[j] + "<br>")
</script>
```

此脚本的输出如下：

```php
Element 0 = One
Element 1 = Two
Element 2 = Three
```

### 使用 `Array` 关键字赋值

您还可以使用 `Array` 关键字一起创建一个包含一些初始元素的数组，如下所示：

```php
numbers = Array("One", "Two", "Three")
```

没有什么可以阻止您之后添加更多元素。

您现在已经看到了可以向数组中添加项目的几种方式，以及引用它们的一种方式。JavaScript 提供了更多选项，我很快会介绍——但首先，我们将看看另一种类型的数组。

## 关联数组

*关联数组* 是一种通过名称而不是整数偏移引用元素的数组。然而，JavaScript 不支持这样的事情。相反，我们可以通过创建具有相同功能的属性对象来实现相同的结果。

因此，要创建一个“关联数组”，请在花括号内定义一个元素块。对于每个元素，将键放在冒号 (`:`) 的左侧，内容放在右侧。示例 16-9 展示了您可能创建一个关联数组来保存在线体育用品零售商“球”部分的内容。

##### 示例 16-9\. 创建和显示一个关联数组

```php
<script>
  balls = {"golf":    "Golf balls, 6",
           "tennis":  "Tennis balls, 3",
           "soccer":  "Soccer ball, 1",
           "ping":    "Ping Pong balls, 1 doz"}

  for (ball in balls)
    document.write(ball + " = " + balls[ball] + "<br>")
</script>
```

为了验证数组已经正确创建并填充，我使用了另一种使用 `for` 循环和 `in` 关键字的方式。这会创建一个新变量，仅在数组内部使用（在本例中是 `ball`），并迭代通过 `in` 关键字右侧的数组所有元素（在本例中是 `balls`）。循环作用于 `balls` 的每个元素，将键值放入 `ball` 中。

使用存储在 `ball` 中的键值，你还可以获取 `balls` 当前元素的值。在浏览器中调用示例脚本的结果如下：

```php
golf = Golf balls, 6
tennis = Tennis balls, 3
soccer = Soccer ball, 1
ping = Ping Pong balls, 1 doz
```

要获取关联数组的特定元素，可以明确指定键，如下所示（在这种情况下，输出值为 `Soccer ball, 1`）：

```php
document.write(balls['soccer'])
```

## 多维数组

要在 JavaScript 中创建一个多维数组，只需将数组放在其他数组中。例如，要创建一个包含二维棋盘（8 × 8 方格）详细信息的数组，您可以使用 示例 16-10 中的代码。

##### 示例 16-10\. 创建一个多维数字数组

```php
<script>
  checkerboard = Array(
    Array(' ', 'o', ' ', 'o', ' ', 'o', ' ', 'o'),
    Array('o', ' ', 'o', ' ', 'o', ' ', 'o', ' '),
    Array(' ', 'o', ' ', 'o', ' ', 'o', ' ', 'o'),
    Array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    Array(' ', ' ', ' ', ' ', ' ', ' ', ' ', ' '),
    Array('O', ' ', 'O', ' ', 'O', ' ', 'O', ' '),
    Array(' ', 'O', ' ', 'O', ' ', 'O', ' ', 'O'),
    Array('O', ' ', 'O', ' ', 'O', ' ', 'O', ' '))

  document.write("<pre>")

  for (j = 0 ; j < 8 ; ++j)
  {
    for (k = 0 ; k < 8 ; ++k)
      document.write(checkerboard[j][k] + " ")

    document.write("<br>")
  }

  document.write("</pre>")
</script>
```

在这个例子中，小写字母代表黑色棋子，大写字母代表白色。一对嵌套的 `for` 循环遍历数组并显示其内容。

外部循环包含两个语句，因此使用大括号将它们括起来。然后内部循环处理每一行中的每个方块，输出位置`[j][k]`处的字符，后跟一个空格（使输出对齐）。由于此循环只包含一个语句，因此不需要使用大括号将其括起来。使用`<pre>`和`</pre>`标签确保输出正确显示，如下所示：

```php
  o   o   o   o
o   o   o   o
  o   o   o   o

O   O   O   O
  O   O   O   O
O   O   O   O
```

你也可以直接使用方括号访问数组中的任何元素：

```php
document.write(checkerboard[7][2])
```

此语句输出大写字母`O`，从第八个元素开始，沿着第三个元素的位置—请记住数组索引从 0 开始，而不是 1。

## 使用数组方法

鉴于数组的强大功能，JavaScript 内置了多种用于操作数组及其数据的方法。以下是其中几个最有用的方法。

### some

当需要知道是否至少有一个数组元素与特定条件匹配时，可以使用`some`函数。该函数会测试所有元素，并在找到匹配的元素后自动停止并返回所需的值。这样可以避免编写自己的代码来执行此类搜索，如下所示：

```php
function isBiggerThan10(element, index, array)
{
  return element > 10
}

result = [2, 5, 8, 1, 4].some(isBiggerThan10); // result will be false
result = [12, 5, 8, 1, 4].some(isBiggerThan10); // result will be true
```

### indexOf

要找出数组中元素的位置，可以在数组上调用`indexOf`函数，该函数将返回所定位元素的偏移量（从 0 开始），如果未找到则返回`-1`。例如，以下代码将`offset`赋值为`2`：

```php
animals = ['cat', 'dog', 'cow', 'horse', 'elephant']
offset = animals.indexOf('cow')

```

### concat

`concat`方法用于连接两个数组或数组内的一系列值。例如，以下代码输出`Banana,Grape,Carrot,Cabbage`：

```php
fruit = ["Banana", "Grape"]
veg   = ["Carrot", "Cabbage"]

document.write(fruit.concat(veg))
```

你可以将多个数组指定为参数，此时`concat`方法会按照数组指定的顺序添加所有元素。

下面是另一种使用`concat`的方法。此时，普通值与数组`pets`进行了连接，输出为`Cat,Dog,Fish,Rabbit,Hamster`：

```php
pets      = ["Cat", "Dog", "Fish"]
more_pets = pets.concat("Rabbit", "Hamster")

document.write(more_pets)
```

### forEach

JavaScript 中的`forEach`方法是实现类似于 PHP `foreach`关键字功能的另一种方式。要使用它，只需传递一个函数名，该函数将针对数组中的每个元素进行调用。示例 16-11 展示了如何使用它。

##### 示例 16-11\. 使用`forEach`方法

```php
<script>
  pets = ["Cat", "Dog", "Rabbit", "Hamster"]
  pets.forEach(output)

  function output(element, index, array)
  {
    document.write("Element at index " + index + " has the value " +
      element + "<br>")
  }
</script>
```

在这种情况下，传递给`forEach`的函数称为`output`。它接受三个参数：`element`，其`index`，以及`array`。这些参数可以根据函数的需要使用。以下示例仅使用`document.write`函数显示`element`和`index`的值。

一旦数组被填充，就可以像这样调用方法：

```php
pets.forEach(output)
```

这是输出结果：

```php
Element at index 0 has the value Cat
Element at index 1 has the value Dog
Element at index 2 has the value Rabbit
Element at index 3 has the value Hamster
```

### join

使用`join`方法，你可以将数组中的所有值转换为字符串，然后将它们连接成一个大字符串，在它们之间可以放置一个可选的分隔符。示例 16-12 展示了三种使用此方法的方式。

##### 示例 16-12\. 使用`join`方法

```php
<script>
  pets = ["Cat", "Dog", "Rabbit", "Hamster"]

  document.write(pets.join()      + "<br>")
  document.write(pets.join(' ')   + "<br>")
  document.write(pets.join(' : ') + "<br>")
</script>
```

如果没有参数，`join`将使用逗号来分隔元素；否则，将插入到`join`中的字符串在每个元素之间插入。示例 16-12 的输出如下所示：

```php
Cat,Dog,Rabbit,Hamster
Cat Dog Rabbit Hamster
Cat : Dog : Rabbit : Hamster
```

### push 和 pop

您已经看到`push`方法如何用于向数组中插入值。其逆方法是`pop`。它从数组中删除最近插入的元素并返回它。示例 16-13 展示了其使用示例。

##### 示例 16-13\. 使用`push`和`pop`方法

```php
<script>
  `sports` `=` `[``"Football"``,` `"Tennis"``,` `"Baseball"``]`
  document.write("Start = "      + sports +  "<br>")

  `sports``.``push``(``"Hockey"``)`
  document.write("After Push = " + sports +  "<br>")

  `removed` `=` `sports``.``pop``(``)`
  document.write("After Pop = "  + sports +  "<br>")
  document.write("Removed = "    + removed + "<br>")
</script>
```

此脚本的三个主要语句显示为粗体。首先，脚本创建了一个名为`sports`的数组，其中包含三个元素，然后向数组中`push`了第四个元素。随后，它将该元素`pop`出来。在此过程中，通过`document.write`显示了各种当前值。脚本输出如下：

```php
Start = Football,Tennis,Baseball
After Push = Football,Tennis,Baseball,Hockey
After Pop = Football,Tennis,Baseball
Removed = Hockey
```

`push`和`pop`函数在需要中断某些活动进行其他活动然后返回的情况下非常有用。例如，假设您想要将一些活动推迟到稍后，同时现在进行更重要的事情。这在我们处理“待办”清单时经常发生，所以让我们在代码中模拟这种情况，将六个项目列表中的第 2 和第 5 项赋予优先状态，如示例 16-14 所示。

##### 示例 16-14\. 在循环内外使用`push`和`pop`

```php
<script>
  numbers = []

  for (j=1 ; j<6 ; ++j)
  {
    if (j == 2 || j == 5)
    {
      document.write("Processing 'todo' #" + j + "<br>")
    }
    else
    {
      document.write("Putting off 'todo' #" + j + " until later<br>")
      `numbers``.``push``(``j``)`
    }
  }

  document.write("<br>Finished processing the priority tasks.")
  document.write("<br>Commencing stored tasks, most recent first.<br><br>")

  document.write("Now processing 'todo' #" + `numbers``.``pop``(``)` + "<br>")
  document.write("Now processing 'todo' #" + `numbers``.``pop``(``)` + "<br>")
  document.write("Now processing 'todo' #" + `numbers``.``pop``(``)` + "<br>")
</script>

```

当然，这里实际上没有进行任何处理，只是将文本输出到浏览器，但您可以理解这个想法。此示例的输出如下：

```php
Putting off 'todo' #1 until later
Processing 'todo' #2
Putting off 'todo' #3 until later
Putting off 'todo' #4 until later
Processing 'todo' #5

Finished processing the priority tasks.
Commencing stored tasks, most recent first.

Now processing 'todo' #4
Now processing 'todo' #3
Now processing 'todo' #1
```

### 使用 reverse

`reverse`方法简单地颠倒数组中所有元素的顺序。示例 16-15 展示了其工作原理。

##### 示例 16-15\. 使用`reverse`方法

```php
<script>
  sports = ["Football", "Tennis", "Baseball", "Hockey"]
  sports.reverse()
  document.write(sports)
</script>
```

原始数组被修改，此脚本的输出如下：

```php
Hockey,Baseball,Tennis,Football
```

### 排序

使用`sort`方法，您可以根据使用的参数将数组的所有元素按字母顺序排列。示例 16-16 展示了四种排序类型。

##### 示例 16-16\. 使用`sort`方法

```php
<script>
  // Alphabetical sort
  sports = ["Football", "Tennis", "Baseball", "Hockey"]
  sports.sort()
  document.write(sports + "<br>")

  // Reverse alphabetical sort
  sports = ["Football", "Tennis", "Baseball", "Hockey"]
  sports.sort().reverse()
  document.write(sports + "<br>")

  // Ascending numeric sort
  numbers = [7, 23, 6, 74]
  numbers.sort(function(a,b){return a - b})
  document.write(numbers + "<br>")

  // Descending numeric sort
  numbers = [7, 23, 6, 74]
  numbers.sort(function(a,b){return b - a})
  document.write(numbers + "<br>")
</script>
```

四个示例部分中的第一个使用默认的`sort`方法执行*字母排序*，而第二个则使用默认的`sort`，然后应用`reverse`方法来获得*反向字母排序*。

第三和第四部分稍微复杂一些；它们使用一个函数来比较`a`和`b`之间的关系。该函数没有名称，因为它仅在排序中使用。您已经看到了名为`function`的函数用于创建匿名函数；我们用它来定义类中的方法（`showUser`方法）。

这里，`function` 创建了一个满足 `sort` 方法需求的匿名函数。如果函数返回大于零的值，则排序假定 `b` 在 `a` 之前。如果函数返回小于零的值，则排序假定 `a` 在 `b` 之前。排序通过在数组的所有值上运行此函数来确定它们的顺序。（当然，如果 `a` 和 `b` 的值相同，函数返回零，先后顺序无关紧要。）

通过操作返回的值（`a - b` 与 `b - a` 相比），示例 16-16 的第三和第四部分选择 *升序数值排序* 和 *降序数值排序*。

信不信由你，这标志着你对 JavaScript 的介绍结束了。现在你应该掌握本书涵盖的三种主要技术的核心知识。下一章将讨论一些跨这些技术使用的高级技术，如模式匹配和输入验证。

# 问题

1.  JavaScript 函数和变量名区分大小写吗？

1.  如何编写一个接受和处理无限数量参数的函数？

1.  给出一个从函数返回多个值的方法。

1.  在定义类时，用于引用当前对象的关键字是什么？

1.  类的所有方法是否都必须在类定义内部定义？

1.  用于创建对象的关键字是什么？

1.  如何使类中的所有对象都可以访问一个属性或方法，而无需在对象内部复制该属性或方法？

1.  如何创建一个多维数组？

1.  用于创建关联数组的语法是什么？

1.  编写一个语句以按降序对数字数组进行排序。

查看“第十六章答案” 中的 附录 A 获取这些问题的答案。
