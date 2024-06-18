# 第三章：函数

*函数*是执行特定任务的命名代码块，可能作用于给定的一组值，即*参数*，并可能返回单个值或数组中的一组值。函数节省编译时间——无论调用多少次，函数仅在页面加载时编译一次。它们还通过允许您在一个地方修复任何错误来提高可靠性，而不是在每个执行任务的地方修复错误，并通过隔离执行特定任务的代码来提高可读性。

本章介绍了函数调用和函数定义的语法，并讨论了如何在函数中管理变量和向函数传递值（包括传值调用和引用调用）。它还涵盖了可变函数和匿名函数。

# 调用函数

PHP 程序中的函数可以是内置的（或者通过扩展实际上是内置的）或用户定义的。无论其来源如何，所有函数都以相同的方式进行评估：

```php
$someValue = *`function_name`*( [ *`parameter``,` `...`* ] );
```

函数需要的参数数量因函数而异（后面我们会看到，同一函数的参数甚至可能不同）。提供给函数的参数可以是任何有效表达式，并且必须按照函数期望的特定顺序提供。如果参数顺序给出错，函数可能仍会运行，但基本上是“垃圾进，垃圾出”的情况。函数的文档会告诉您函数期望的参数以及您可以期望返回的值（们）。

下面是一些函数示例：

```php
// strlen() is a PHP built-in function that returns the length of a string
$length = strlen("PHP"); // $length is now 3
// sin() and asin() are the sine and arcsine math functions
$result = sin(asin(1)); // $result is the sine of arcsin(1), or 1.0

// unlink() deletes a file
$result = unlink("functions.txt");
// $result = true or false depending on success or failure
```

在第一个示例中，我们将一个参数`"PHP"`传递给`strlen()`函数，它返回所提供字符串的字符数。在这种情况下，它返回`3`，并赋值给变量`$length`。这是使用函数的最简单和最常见的方式。

第二个示例将`asin(1)`的结果传递给`sin()`函数。由于正弦和反正弦函数是互为反函数，对任何值的反正弦的正弦总是返回该相同的值。在这里，我们看到一个函数可以在另一个函数内调用。内部调用的返回值随后被发送到外部函数，然后将总体结果返回并存储在`$result`变量中。

在最后一个示例中，我们向`unlink()`函数提供一个文件名，它尝试删除该文件。像许多函数一样，当它失败时返回`false`。这使您可以使用另一个内置函数`die()`和逻辑运算符的短路特性。因此，这个示例可以重写为：

```php
$result = unlink("functions.txt") or die("Operation failed!");
```

`unlink()`函数，与其他两个示例不同，会影响给定参数之外的内容。在这种情况下，它会从文件系统中删除文件。应仔细记录和考虑函数的所有此类副作用。

PHP 拥有大量已定义的函数可供在程序中使用。这些扩展执行任务包括访问数据库、创建图形、读写 XML 文件、从远程系统抓取文件等。PHP 的内置函数在附录中有详细描述。

###### 注意

并非所有函数都返回一个值。它们可以执行像发送电子邮件这样的动作，然后只需将控制行为返回给调用代码；完成了它们的任务后，它们没有任何“话要说”。

# 定义一个函数

要定义一个函数，使用以下语法：

```php
function [&] *`function_name`*([*`parameter`*[, ...]])
{
 *`statement` `list`*
}
```

语句列表可以包含 HTML。你可以声明一个不包含任何 PHP 代码的 PHP 函数。例如，`column()`函数只是为页面中可能需要多次使用的 HTML 代码提供了一个方便的简短名称：

```php
<?php function column()
{ ?>
 </td><td>
<?php }
```

函数名可以是以字母或下划线开头，后跟零个或多个字母、下划线和数字的任何字符串。函数名不区分大小写；也就是说，你可以将`sin()`函数称为`sin(1)`、`SIN(1)`、`SiN(1)`等等，因为所有这些名称都指代同一个函数。按照惯例，内置的 PHP 函数都使用全小写来调用。

通常，函数会返回某个值。要从函数中返回值，请使用`return`语句：将`return` *expr*放在函数内部。当执行到`return`语句时，控制流返回到调用语句，并将*expr*的计算结果作为函数的返回值返回。你可以在函数中包含任意数量的`return`语句（例如，如果有`switch`语句确定返回哪个值）。

让我们看一个简单的函数。示例 3-1 接受两个字符串，将它们连接起来，然后返回结果（在这种情况下，我们创建了一个稍慢的等效于连接运算符的示例，但请谅解，这只是为了举例）。

##### 示例 3-1\. 字符串连接

```php
function strcat($left, $right)
{
 $combinedString = $left . $right;

 return $combinedString;
}
```

该函数接受两个参数`$left`和`$right`。使用连接运算符，函数在变量`$combinedString`中创建一个组合字符串。最后，为了使函数在用我们的参数进行评估时具有一个值，我们返回值`$combinedString`。

因为`return`语句可以接受任何表达式，甚至是复杂的表达式，我们可以像这样简化程序：

```php
function strcat($left, $right)
{
 return $left . $right;
}
```

如果我们将这个函数放在一个 PHP 页面上，可以在页面的任何地方调用它。看看示例 3-2。

##### 示例 3-2\. 使用我们的连接函数

```php
<?php
function strcat($left, $right)
{
 return $left . $right;
}
$first = "This is a ";
$second = " complete sentence!";

echo strcat($first, $second);
```

当显示这个页面时，将显示完整的句子。

在下一个示例中，一个函数接受一个整数，通过对原始值进行位移来使其加倍，并返回结果：

```php
function doubler($value)
{
 return $value << 1;
}
```

一旦函数被定义，你可以在页面的任何地方使用它。例如：

```php
<?php echo "A pair of 13s is " . doubler(13); ?>
```

你可以嵌套函数声明，但效果有限。嵌套声明不会限制内部定义函数的可见性，该函数可以从程序中的任何地方调用。内部函数不会自动获取外部函数的参数。最后，内部函数在外部函数被调用之前不能被调用，也不能在解析外部函数之后的代码中被调用：

```php
function outer ($a)
{
 function inner ($b)
 {
 echo "there $b";
 }

 echo "$a, hello ";
}

// outputs "well, hello there reader"
outer("well");
inner("reader");
```

# 变量作用域

如果不使用函数，则创建的任何变量可以在页面的任何地方使用。但在函数中，情况并非总是如此。函数保持其自己的变量集，这些变量与页面和其他函数的变量不同。

函数中定义的变量（包括其参数）在函数外部不可访问，默认情况下，在函数外部定义的变量在函数内部也不可访问。以下示例说明了这一点：

```php
$a = 3;

function foo()
{
 $a += 2;
}

foo();
echo $a;
```

函数`foo()`内的变量`$a`是一个不同的变量，而不是函数外部的变量`$a`；即使`foo()`使用了加法赋值运算符，页面的外部`$a`的值仍然是`3`。在函数内部，`$a`的值是`2`。

正如我们在第二章中讨论的那样，变量在程序中可见的程度被称为变量的*作用域*。在函数内创建的变量处于函数的作用域内（即具有*函数级作用域*）。在函数和对象外创建的变量具有*全局作用域*，可以在这些函数和对象之外的任何地方存在。PHP 提供的少数变量既有函数级作用域也有全局作用域（通常称为*超全局变量*）。

乍一看，即使是经验丰富的程序员可能会认为在前面的例子中，到达`echo`语句时`$a`将会是`5`，因此在为变量选择名称时，请牢记这一点。

## 全局变量

如果你想让全局作用域中的变量在函数内部可访问，可以使用`global`关键字。其语法如下：

```php
global *`var1`*, *`var2`*, ... ;
```

将上述例子更改以包括`global`关键字，我们得到：

```php
$a = 3;

function foo()
{
 global $a;

 $a += 2;
}

foo();
echo $a;
```

PHP 不会创建具有函数级作用域的新变量`$a`，而是在函数内部使用全局变量`$a`。现在，当显示`$a`的值时，它将是`5`。

在函数体之前必须使用`global`关键字声明函数中要访问的全局变量或变量。因为它们在函数体之前声明，所以函数参数永远不能是全局变量。

使用`global`等同于在`$GLOBALS`变量中创建变量的引用。也就是说，以下两个声明都会在函数作用域内创建一个变量，该变量是全局作用域变量`$var`的引用：

```php
global $var;
$var = & $GLOBALS['var'];
```

## 静态变量

与 C 语言类似，PHP 支持声明函数变量为*静态*。静态变量在所有对函数的调用之间保持其值，并且仅在脚本执行期间的第一次调用函数时初始化。使用`static`关键字在函数变量的第一次使用时将其声明为静态。通常，静态变量的第一次使用分配一个初始值：

```php
static *`var`* [= *`value`*][, ... ];
```

在示例 3-3 中，变量`$count`每次调用函数时递增 1。

##### 示例 3-3\. 静态变量计数器

```php
<?php
function counter()
{
 static $count = 0;

 return $count++;
}

for ($i = 1; $i <= 5; $i++) {
 print counter();
}
```

当第一次调用函数时，静态变量`$count`被赋值为`0`。返回该值并递增`$count`。当函数结束时，`$count`不像非静态变量那样被销毁，其值保持不变，直到下次调用`counter()`。`for`循环显示从 0 到 4 的数字。

# 函数参数

函数可以期望由函数定义声明的任意数量的参数。有两种不同的方法可以将参数传递给函数。第一种，也是更常见的，是通过值传递。第二种是通过引用。

## 通过值传递参数

在大多数情况下，您通过值传递参数。参数是任何有效表达式。该表达式被评估，并且结果值被分配给函数中适当的变量。到目前为止，我们在所有示例中都是通过值传递参数的。

## 通过引用传递参数

通过引用传递允许您覆盖正常的作用域规则，并直接访问变量。要通过引用传递，参数必须是一个变量；您通过在参数列表中的变量名称前面加上一个`&`符号来指示函数的特定参数将通过引用传递。示例 3-4 重新访问我们的`doubler()`函数，稍作修改。

##### 示例 3-4\. doubler() redux

```php
<?php
function doubler(&$value)
{
 $value = $value << 1;
}

$a = 3;
doubler($a);

echo $a;
```

因为函数的`$value`参数是通过引用传递的，所以`$a`的实际值被修改，而不是该值的副本。之前，我们必须`return`双倍值，但现在我们将调用者的变量更改为双倍值。

这是另一个函数具有副作用的地方：因为我们通过引用将变量`$a`传递给`doubler()`，所以`$a`的值受函数的影响。在这种情况下，`doubler()`为其分配了一个新值。

仅变量——而不是常量——可以提供给声明为按引用传递的参数。因此，如果我们在前面的示例中包含语句 `<?php echo doubler(7); ?>`，它会发出一个错误。但是，您可以为通过引用传递的参数分配默认值（与为通过值传递的参数提供默认值的方式相同）。

即使在您的函数不影响给定值的情况下，您可能希望通过引用传递参数。通过值传递时，PHP 必须复制该值。特别是对于大字符串和对象，这可能是一个昂贵的操作。通过引用传递可以避免复制值的需要。

## 默认参数

有时，函数可能需要接受特定参数。例如，当您调用函数以获取站点的偏好设置时，函数可能需要一个带有要检索的偏好名称的参数。而不是使用某些特殊关键字来指定要检索所有偏好设置，您可以简单地不提供任何参数。此行为通过使用默认参数工作。

要指定默认参数，请在函数声明中分配参数值。分配给参数的默认值不能是复杂表达式，只能是标量值：

```php
function getPreferences($whichPreference = 'all')
{
 // if $whichPreference is "all", return all prefs;
 // otherwise, get the specific preference requested...
}
```

当您调用`getPreferences()`时，您可以选择提供参数。如果这样做，它将返回与您提供的字符串匹配的偏好设置；如果不提供，则返回所有偏好设置。

###### 注意

函数可以具有任意数量的具有默认值的参数。但是，这些具有默认值的参数必须在所有没有默认值的参数之后列出。

## 变量参数

函数可能需要变量数量的参数。例如，在前一节中的`getPreferences()`示例中，可能返回任意数量的名称的偏好设置，而不仅仅是一个。要声明带有变量数量参数的函数，请完全省略参数块：

```php
function getPreferences()
{
 // some code
}
```

PHP 提供了三个函数供您在函数中使用以检索传递给它的参数。`func_get_args()`返回提供给函数的所有参数的数组；`func_num_args()`返回提供给函数的参数数量；`func_get_arg()`从参数中返回特定参数。例如：

```php
$array = func_get_args();
$count = func_num_args();
$value = func_get_arg(*`argument_number`*);
```

在例子 3-5 中，`count_list()`函数接受任意数量的参数。它遍历这些参数并返回所有值的总和。如果未提供参数，则返回`false`。

##### 示例 3-5\. 参数计数器

```php
<?php
function countList()
{
 if (func_num_args() == 0) {
 return false;
 }
 else {
 $count = 0;

 for ($i = 0; $i < func_num_args(); $i++) {
 $count += func_get_arg($i);
 }

 return $count;
 }
}

echo countList(1, 5, 9); // outputs "15"
```

这些函数的任何结果都不能直接用作另一个函数的参数。相反，您必须首先将变量设置为函数的结果，然后在函数调用中使用它。以下表达式将不起作用：

```php
foo(func_num_args());
```

取而代之，使用：

```php
$count = func_num_args();
foo($count);
```

## 缺失的参数

PHP 允许您随意——当您调用函数时，可以向函数传递任意数量的参数。函数期望但未传递给它的任何参数保持未设置，并为每个参数发出警告：

```php
function takesTwo($a, $b)
{
 if (isset($a)) {
 echo " a is set\n";
 }

 if (isset($b)) {
 echo " b is set\n";
 }
}

 echo "With two arguments:\n";
takesTwo(1, 2);

echo "With one argument:\n";
takesTwo(1);
`With` `two` `arguments``:`
 `a` `is` `set`
 `b` `is` `set`
`With` `one` `argument``:`
`Warning``:` `Missing` `argument` `2` `for` `takes_two``()`
 `in` `/``path``/``to``/``script``.``php` `on` `line` `6`
`a` `is` `set`
```

## 类型提示

在定义函数时，可以添加类型提示，即可以要求参数是特定类的实例（包括扩展该类的类的实例）、实现特定接口的类的实例、数组或可调用的。要为参数添加类型提示，请在函数的参数列表中变量名**之前**包括类名、`array`或`callable`。例如：

```php
class Entertainment {}

class Clown extends Entertainment {}

class Job {}

function handleEntertainment(Entertainment $a, callable $callback = NULL)
{
 echo "Handling " . get_class($a) . " fun\n";

 if ($callback !== NULL) {
 $callback();
 }
}

$callback = function()
{
 // do something
};

handleEntertainment(new Clown); // works
handleEntertainment(new Job, $callback); // runtime error
```

类型提示的参数必须为`NULL`、给定类的实例或该类的子类、数组或可调用的指定参数。否则，会发生运行时错误。

您可以在类中为属性定义数据类型。

# 返回值

PHP 函数只能使用`return`关键字返回单个值：

```php
function returnOne()
{
 return 42;
}
```

要返回多个值，请返回一个数组：

```php
function returnTwo()
{
 return array("Fred", 35);
}
```

如果函数没有提供返回值，则函数会返回`NULL`。您可以通过在函数定义中声明来设置返回数据类型。例如，当执行以下代码时，将返回整数 50：

```php
`function` *`someMath`*($var1, $var2): *`int`*
{
 `return` $var1 * $var2;
}

`echo` *`someMath`*(10, 5);
```

默认情况下，值是从函数中复制出来的。要通过引用返回值，请在声明函数和将返回值分配给变量时都在函数名前加上`&`：

```php
$names = array("Fred", "Barney", "Wilma", "Betty");

function &findOne($n) {
 global $names;

 return $names[$n];
}

$person =& findOne(1); // Barney
$person = "Barnetta"; // changes $names[1]
```

在此代码中，`findOne()`函数返回`$names[1]`的别名而不是其值的副本。因为我们通过引用赋值，所以`$person`是`$names[1]`的别名，第二次赋值会改变`$names[1]`中的值。

有时，这种技术用于高效地从函数中返回大字符串或数组值。但是，PHP 对变量值实施写时复制（copy-on-write），意味着通常情况下从函数返回引用是不必要的。返回值的引用比返回值本身要慢。

# 可变函数

与可变变量类似，其中表达式引用的是外观变量（`$$`结构）的值的变量的值的变量（`$variable()`），可以在变量后添加括号来调用函数。考虑以下情况，其中一个变量用于确定要调用的三个函数之一：

```php
switch ($which) {
 case 'first':
 first();
 break;

 case 'second':
 second();
 break;

 case 'third':
 third();
 break;
}
```

在这种情况下，我们可以使用变量函数调用来调用适当的函数。要进行变量函数调用，请在变量后的括号中包含函数的参数。重新编写前面的示例：

```php
$which(); // if $which is "first", the function first() is called, etc...
```

如果变量没有对应的函数存在，则在评估代码时会发生运行时错误。为了防止这种情况，在调用函数之前，可以使用内置的`function_exists()`函数来确定变量的值是否存在函数：

```php
$yesOrNo = function_exists(*`function_name`*);
```

例如：

```php
if (function_exists($which)) {
 $which(); // if $which is "first", the function first() is called, etc...
}
```

语言结构（如`echo()`和`isset()`）不能通过可变函数调用：

```php
$which = "echo";
$which("hello, world"); // does not work
```

# 匿名函数

一些 PHP 函数通过使用您提供的函数来完成部分工作。例如，`usort()` 函数使用您创建并作为参数传递给它的函数来确定数组中项目的排序顺序。

尽管您可以为此类目的定义一个函数，如前所示，但这些函数往往是局部化且临时的。为了反映回调的临时性质，请创建并使用 *匿名函数*（也称为 *闭包*）。

您可以使用正常的函数定义语法创建匿名函数，但将其分配给变量或直接传递。

示例 3-6 展示了使用 `usort()` 的示例。

##### 示例 3-6\. 匿名函数

```php
$array = array("really long string here, boy", "this", "middling length", "larger");

usort($array, function($a, $b) {
 return strlen($a) – strlen($b);
});

print_r($array);
```

数组通过使用匿名函数 `usort()` 按字符串长度顺序排序。

匿名函数可以使用使用 `use` 语法在其封闭作用域中定义的变量。例如：

```php
$array = array("really long string here, boy", "this", "middling length", 
"larger");
$sortOption = 'random';

usort($array, function($a, $b) use ($sortOption)
{
 if ($sortOption == 'random') {
 // sort randomly by returning (-1, 0, 1) at random
 return rand(0, 2) - 1;
 }
 else {
 return strlen($a) - strlen($b);
 }
});

print_r($array);
```

注意，将封闭作用域中的变量包含到闭包中并不等同于使用全局变量——全局变量始终位于全局作用域，而将变量包含允许闭包使用封闭作用域中定义的变量。还要注意，这不一定与调用闭包的作用域相同。例如：

```php
$array = array("really long string here, boy", "this", "middling length", 
"larger");
$sortOption = "random";

function sortNonrandom($array)
{
 $sortOption = false;

 usort($array, function($a, $b) use ($sortOption)
 {
 if ($sortOption == "random") {
 // sort randomly by returning (-1, 0, 1) at random
 return rand(0, 2) - 1;
 }
 else {
 return strlen($a) - strlen($b);
 }
 });

 print_r($array);
}

print_r(sortNonrandom($array));
```

在这个示例中，`$array` 是按正常顺序排序的，而不是随机排序——闭包内的 `$sortOption` 的值是在 `sortNonrandom()` 作用域内的 `$sortOption` 的值，而不是全局作用域内的 `$sortOption` 的值。

# 接下来是什么

用户定义的函数编写起来可能令人困惑且难以调试，因此务必对其进行充分测试，并尽量限制其执行单一任务。在下一章中，我们将讨论字符串及其相关内容，这是另一个复杂且潜在令人困惑的主题。不要灰心：请记住，我们正在为编写良好、稳固、简洁的 PHP 代码打下坚实的基础。一旦掌握了函数、字符串、数组和对象的关键概念，您就将成为一名优秀的 PHP 开发者。
