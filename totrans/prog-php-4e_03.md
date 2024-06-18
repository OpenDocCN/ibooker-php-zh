# 第二章。语言基础

本章快速浏览了核心 PHP 语言，涵盖了数据类型、变量、运算符和流控制语句等基本主题。PHP 受其他编程语言（如 Perl 和 C）的强烈影响，因此，如果您有这些语言的经验，学习 PHP 应该很容易。如果 PHP 是您的第一种编程语言之一，不要惊慌。我们从 PHP 程序的基本单元开始，并逐步构建您的知识。

# 词法结构

编程语言的词法结构是指控制您在该语言中编写程序的基本规则集。它是语言的最低级语法，指定变量名的外观、用于注释的字符以及如何将程序语句彼此分隔等内容。

## 大小写敏感性

用户定义的类和函数的名称，以及内置结构和关键字（如`echo`、`while`、`class`等）不区分大小写。因此，以下这三行是等效的：

```php
echo("hello, world");
ECHO("hello, world");
EcHo("hello, world");
```

另一方面，变量是区分大小写的。也就是说，`$name`、`$NAME` 和 `$NaME` 是三个不同的变量。

## 语句和分号

语句是执行某些操作的 PHP 代码集合。它可以简单到变量赋值，也可以复杂到有多个退出点的循环。这里是 PHP 语句的一个小示例，包括函数调用、一些变量数据分配以及一个 `if` 语句：

```php
echo "Hello, world";
myFunction(42, "O'Reilly");
$a = 1;
$name = "Elphaba";
$b = $a / 25.0;
if ($a == $b) {
 echo "Rhyme? And Reason?";
}
```

PHP 使用分号分隔简单语句。使用花括号标记代码块的复合语句，例如条件测试或循环，不需要在闭括号后加分号。与其他语言不同，在 PHP 中，在闭括号前的分号是不可选的：

```php
if ($needed) {
 echo "We must have it!"; // semicolon required here
} // no semicolon required here after the brace
```

然而，在闭合 PHP 标签前分号是可选的：

```php
<?php
if ($a == $b) {
 echo "Rhyme? And Reason?";
}
echo "Hello, world" // no semicolon required before closing tag
?>
```

最好在可选的地方包含分号，因为这样可以更轻松地添加代码。

## 空白和换行

一般情况下，PHP 程序中的空白不重要。您可以将语句扩展到任意行数，或将一堆语句放在一行上。例如，这条语句：

```php
raisePrices($inventory, $inflation, $costOfLiving, $greed);
```

同样可以写得更多空白：

```php
raisePrices (
 $inventory ,
 $inflation ,
 $costOfLiving ,
 $greed
) ;
```

或者更少的空白：

```php
raisePrices($inventory,$inflation,$costOfLiving,$greed);
```

您可以利用这种灵活的格式使您的代码更易读（通过对齐赋值、缩进等）。有些懒惰的程序员利用这种自由格式创建完全不可读的代码——这是不推荐的。

## 注释

注释提供给阅读您代码的人的信息，但在 PHP 执行时会被忽略。即使您认为只有您会阅读您的代码，也最好在代码中包含注释——回顾几个月前编写的代码可能会觉得好像是陌生人写的。

一个良好的实践是让您的注释尽量少，不要妨碍代码本身，但足够丰富，以便您可以使用注释告诉发生了什么。不要注释显而易见的事情，以免淹没描述棘手事物的注释。例如，这是毫无价值的：

```php
$x = 17; // store 17 into the variable $x
```

而在这个复杂的正则表达式上的注释将帮助维护代码的人：

```php
// convert &#nnn; entities into characters

$text = preg_replace('/&#([0-9])+;/', "chr('\\1')", $text);
```

PHP 提供了几种在代码中包含注释的方法，所有这些方法都借鉴自现有的语言，如 C、C++和 Unix shell。一般来说，使用 C 风格的注释来注释*掉*代码，使用 C++风格的注释来注释*解*代码。

### Shell 风格的注释

当 PHP 在代码中遇到井号字符（`#`）时，从井号到行尾或 PHP 代码段的末尾（以先到者为准）的所有内容都被视为注释。这种注释方法在 Unix shell 脚本语言中很常见，可用于注释单行代码或做简短的备注。

由于井号在页面上是可见的，有时会使用 shell 风格的注释来标记代码块：

```php
#######################
## Cookie functions
#######################
```

有时它们用于代码行之前，以标识该代码的功能，此时它们通常缩进到与所用于目标代码相同的级别：

```php
if ($doubleCheck) {
 # create an HTML form requesting that the user confirm the action
 echo confirmationForm();
}
```

经常将单行代码的简短注释放在同一行上：

```php
$value = $p * exp($r * $t); # calculate compounded interest
```

当您紧密混合 HTML 和 PHP 代码时，让闭合的 PHP 标记终止注释会很有用：

```php
<?php $d = 4; # Set $d to 4\. ?> Then another <?php echo $d; ?>
Then another 4
```

### C++注释

当 PHP 在代码中遇到两个斜杠（`//`）时，从斜杠到行尾或代码段末尾（以先到者为准），都将被视为注释。这种注释方法源于 C++。其结果与 shell 注释样式相同。

这里是使用 C++注释重写的 shell 风格注释示例：

```php
////////////////////////
// Cookie functions
////////////////////////

if ($doubleCheck) {
 // create an HTML form requesting that the user confirm the action
 echo confirmationForm();
}

$value = $p * exp($r * $t); // calculate compounded interest

<?php $d = 4; // Set $d to 4\. ?> Then another <?php echo $d; ?>
Then another 4
```

### C 注释

虽然 shell 风格和 C++风格的注释可用于注释代码或做简短的备注，但较长的注释需要不同的风格。因此，PHP 支持块注释，其语法源自 C 编程语言。当 PHP 遇到斜杠后跟一个星号（`/*`）时，从那之后直到遇到一个星号后跟一个斜杠（`*/`）之前的所有内容都被视为注释。这种类型的注释与前面展示的不同，可以跨多行。

这是 C 风格多行注释的示例：

```php
/* In this section, we take a bunch of variables and
 assign numbers to them. There is no real reason to
 do this, we're just having fun.
*/
$a = 1;
$b = 2;
$c = 3;
$d = 4;
```

由于 C 风格的注释具有特定的起始和结束标记，您可以将它们与代码紧密集成。这倾向于使您的代码更难阅读，因此不建议使用：

```php
/* These comments can be mixed with code too,
see? */ $e = 5; /* This works just fine. */
```

与其他类型不同，C 风格的注释可以延续到 PHP 结束标记后面。例如：

```php
<?php
$l = 12;
$m = 13;
/* A comment begins here
?>
<p>Some stuff you want to be HTML.</p>
<?= $n = 14; ?>
*/
echo("l=$l m=$m n=$n\n");
?><p>Now <b>this</b> is regular HTML...</p>
`l=12 m=13 n=`
`<p``>``Now` `<b``>``this``</b>` `is regular HTML...``</p>`
```

您可以根据需要缩进注释：

```php
/* There are no
 special indenting or spacing
 rules that have to be followed, either.

 */
```

C 风格的注释可用于禁用代码的部分。在以下示例中，我们通过将它们包含在块注释中禁用了第二和第三个语句，以及内联注释。要启用代码，我们只需删除注释标记：

```php
$f = 6;
/*
$g = 7; # This is a different style of comment
$h = 8;
*/
```

但是，您必须小心不要尝试嵌套块注释：

```php
$i = 9;
/*
$j = 10; /* This is a comment */
$k = 11;
Here is some comment text.
*/
```

在这种情况下，PHP 尝试（但失败）执行（非）语句 `Here is some` `comment` `text` 并返回错误。

## 字面量

字面量是程序中直接出现的数据值。以下是 PHP 中的所有字面量：

```php
2001
0xFE
1.4142
"Hello World"
'Hi'
true
null
```

## 标识符

标识符简单来说就是一个名称。在 PHP 中，标识符用于命名变量、函数、常量和类。标识符的第一个字符必须是 ASCII 字母（大写或小写）、下划线字符 (`_`) 或者在 ASCII 0x7F 到 ASCII 0xFF 之间的任何字符。初始字符之后，这些字符和数字 0–9 都是有效的。

### 变量名

变量名总是以美元符号（`$`）开头且区分大小写。以下是一些有效的变量名：

```php
$bill
$head_count
$MaximumForce
$I_HEART_PHP
$_underscore
$_int
```

以下是一些非法的变量名：

```php
$not valid
$|
$3wa
```

由于区分大小写，以下变量是不同的：

```php
$hot_stuff $Hot_stuff $hot_Stuff $HOT_STUFF
```

### 函数名

函数名不区分大小写（函数详细讨论请参见 第三章）。以下是一些有效的函数名：

```php
tally
list_all_users
deleteTclFiles
LOWERCASE_IS_FOR_WIMPS
_hide
```

这些函数名均指向同一个函数：

```php
howdy HoWdY HOWDY HOWdy howdy
```

### 类名

类名遵循 PHP 标识符的标准规则，也不区分大小写。以下是一些有效的类名：

```php
Person
account
```

类名 `stdClass` 是保留类名。

### 常量

常量是一个值的标识符，其值不会改变；标量值（布尔值、整数、浮点数和字符串）和数组可以是常量。一旦设置，常量的值就不能更改。通过 `define()` 函数设置常量：

```php
define('PUBLISHER', "O'Reilly Media");
echo PUBLISHER;
```

## 关键字

关键字（或保留字）是语言为其核心功能保留的单词，您不能给函数、类或常量赋予与关键字相同的名称。表 2-1 列出了 PHP 中的关键字，这些关键字不区分大小写。

表 2-1\. PHP 核心语言关键字

| `__CLASS__` `__DIR__`

`__FILE__`

`__FUNCTION__`

`__LINE__`

`__METHOD__`

`__NAMESPACE__`

`__TRAIT__`

`__halt_compiler()`

`abstract`

`and`

`array()`

`as`

`break`

`callable`

`case`

`catch`

`class`

`clone`

`const`

`continue`

`declare`

`default`

`die()`

`do` | `echo` `else`

`elseif`

`empty()`

`enddeclare`

`endfor`

`endforeach`

`endif`

`endswitch`

`endwhile`

`eval()`

`exit()`

`extends`

`final`

`finally`

`for`

`foreach`

`function`

`global`

`goto`

`if`

`implements`

`include`

`include_once`

`instanceof` | `insteadof` `interface`

`isset()`

`list()`

`namespace`

`new`

`or`

`print`

`private`

`protected`

`public`

`require`

`require_once`

`return`

`static`

`switch`

`throw`

`trait`

`try`

`unset()`

`use`

`var`

`while`

`xor`

`yield`

`yield from` |

此外，您不能使用与内置 PHP 函数同名的标识符。完整列表请参见 附录。

# 数据类型

PHP 提供八种值或数据类型。其中四种是标量（单值）类型：整数、浮点数、字符串和布尔值。两种是复合（集合）类型：数组和对象。剩下的两种是特殊类型：资源和 NULL。数字、布尔值、资源和 NULL 在此详细讨论，而字符串、数组和对象是如此庞大的主题，它们有自己的章节（第 4、5 和 6 章）。

## 整数

整数是整数，如 1、12 和 256。可接受值的范围根据您平台的详细信息而异，但通常从 −2,147,483,648 到 +2,147,483,647。具体来说，范围相当于您的 C 编译器的长整型数据类型的范围。不幸的是，C 标准没有指定长整型应具有的范围，因此在某些系统上，整数范围可能会有所不同。

整数字面量可以用十进制、八进制、二进制或十六进制表示。十进制值由一系列数字组成，没有前导零。序列可以以加号（`+`）或减号（`−`）开头。如果没有符号，则假定为正数。十进制整数的示例包括以下内容：

```php
1998
−641
+33
```

八进制数由前导 `0` 和由 0 到 7 的一系列数字组成。与十进制数类似，八进制数可以以加号或减号为前缀。以下是一些八进制值及其相应的十进制值示例：

```php
0755 // decimal 493
+010 // decimal 8
```

十六进制值以 `0x` 开头，后跟一系列数字（0–9）或字母（A–F）。字母可以是大写或小写，但通常用大写字母表示。与十进制和八进制值一样，十六进制数可以包含符号：

```php
0xFF // decimal 255
0x10 // decimal 16
–0xDAD1 // decimal −56017
```

二进制数以 `0b` 开头，后跟一系列数字（0 和 1）。与其他值一样，二进制数可以包含符号：

```php
0b01100000 // decimal 96
0b00000010 // decimal 2
-0b10 // decimal -2
```

如果您试图存储一个太大无法存储为整数或不是整数的变量，它将自动转换为浮点数。

使用 `is_int()` 函数（或其 `is_integer()` 别名）来测试一个值是否为整数：

```php
if (is_int($x)) {
 // $x is an integer
}
```

## 浮点数

浮点数（通常称为“实数”）用十进制数字表示数值。与整数类似，其限制取决于您计算机的详细信息。PHP 浮点数相当于您的 C 编译器的双精度数据类型的范围。通常，这允许在 1.7E−308 到 1.7E+308 之间的数值，具有 15 位精度。如果您需要更高的精度或更广范围的整数值，可以使用 BC 或 GMP 扩展。

PHP 可识别两种不同格式的浮点数。有一种是我们日常使用的：

```php
3.14
0.017
-7.1
```

但是 PHP 也能识别科学计数法表示的数字：

```php
0.314E1 // 0.314*10¹, or 3.14
17.0E-3 // 17.0*10^(-3), or 0.017
```

浮点数值只是数字的近似表示。例如，在许多系统上，3.5 实际上表示为 3.4999999999\. 这意味着您必须小心避免编写假定浮点数完全准确表示的代码，例如直接使用`==`比较两个浮点数值。通常的方法是比较几个小数位数：

```php
if (intval($a * 1000) == intval($b * 1000)) {
 // numbers equal to three decimal places
}
```

使用`is_float()`函数（或其`is_real()`别名）来测试一个值是否为浮点数：

```php
if (is_float($x)) {
 // $x is a floating-point number
}
```

## 字符串

由于字符串在 Web 应用程序中非常常见，PHP 包含了在核心级别支持创建和操作字符串的功能。字符串是任意长度的字符序列。字符串字面量由单引号或双引号括起来：

```php
'big dog'
"fat hog"
```

变量在双引号内扩展（插值），而在单引号内不会：

```php
$name = "Guido";
echo "Hi, $name <br/>";
echo 'Hi, $name';
`Hi``,` `Guido`
`Hi``,` `$name`
```

双引号还支持多种字符串转义，如表 2-2 中列出的：

表 2-2\. 双引号字符串中的转义序列

| 转义序列 | 表示的字符 |
| --- | --- |
| `\"` | 双引号 |
| `\n` | 换行符 |
| `\r` | 回车符 |
| `\t` | 制表符 |
| `\\` | 反斜杠 |
| `\$` | 美元符号 |
| `\{` | 左大括号 |
| `\}` | 右大括号 |
| `\[` | 左括号 |
| `\]` | 右括号 |
| `\0` 到 `\777` | 由八进制值表示的 ASCII 字符 |
| `\x0` 到 `\xFF` | 由十六进制值表示的 ASCII 字符 |

单引号字符串识别`\\`以获取文字反斜杠和`\'`以获取文字单引号：

```php
$dosPath = 'C:\\WINDOWS\\SYSTEM';
$publisher = 'Tim O\'Reilly';
echo "$dosPath $publisher";
C:\WINDOWS\SYSTEM Tim O'Reilly
```

要测试两个字符串是否相等，请使用`==`（双等号）比较运算符：

```php
if ($a == $b) {
 echo "a and b are equal";
}
```

使用`is_string()`函数来测试一个值是否为字符串：

```php
if (is_string($x)) {
 // $x is a string
}
```

PHP 提供了运算符和函数来比较、拆解、组装、搜索、替换和修剪字符串，以及一系列专门用于处理 HTTP、HTML 和 SQL 编码的字符串函数。由于有许多字符串操作函数，我们已经专门撰写了一个完整的章节（第四章）来详细介绍所有细节。

## 布尔值

布尔值表示一个*真值*——它表示某事是真的还是不真的。像大多数编程语言一样，PHP 定义了一些值为真，其他为假。真实性和虚假性决定了条件代码的结果，如：

```php
if ($alive) { ... }
```

在 PHP 中，以下值都被评估为`false`：

+   关键字`false`

+   整数`0`

+   浮点值`0.0`

+   空字符串（`""`）和字符串`"0"`

+   零元素的数组

+   值`NULL`

一个不为假的值就是真，包括所有资源值（稍后在“资源”部分描述）。

PHP 提供了`true`和`false`关键字以确保清晰：

```php
$x = 5; // $x has a true value
$x = true; // clearer way to write it
$y = ""; // $y has a false value
$y = false; // clearer way to write it
```

使用`is_bool()`函数来测试一个值是否为布尔值：

```php
if (is_bool($x)) {
 // $x is a Boolean
}
```

## 数组

数组保存一组值，可以通过位置（数字，零为第一个位置）或某些标识名称（字符串），称为*关联索引*来标识：

```php
$person[0] = "Edison";
$person[1] = "Wankel";
$person[2] = "Crapper";

$creator['Light bulb'] = "Edison";
$creator['Rotary Engine'] = "Wankel";
$creator['Toilet'] = "Crapper";
```

`array()`结构创建一个数组。以下是两个例子：

```php
$person = array("Edison", "Wankel", "Crapper");
$creator = array('Light bulb' => "Edison",
 'Rotary Engine' => "Wankel",
 'Toilet' => "Crapper");
```

有几种方法可以遍历数组，但最常见的是`foreach`循环：

```php
foreach ($person as $name) {
 echo "Hello, {$name}<br/>";
}

foreach ($creator as $invention => $inventor) {
 echo "{$inventor} invented the {$invention}<br/>";
}
`Hello``,` `Edison`
`Hello``,` `Wankel`
`Hello``,` `Crapper`
`Edison` `created` `the` `Light` `bulb`
`Wankel` `created` `the` `Rotary` `Engine`
`Crapper` `created` `the` `Toilet`
```

可以使用不同的排序函数对数组元素进行排序：

```php
 sort($person);
// $person is now array("Crapper", "Edison", "Wankel") 
 asort($creator);
// $creator is now array('Toilet' => "Crapper", // 'Light bulb' => "Edison", // 'Rotary Engine' => "Wankel");
```

使用`is_array()`函数来测试一个值是否为数组：

```php
if (is_array($x)) {
 // $x is an array
}
```

有返回数组中项数的函数、获取数组中每个值的函数等。数组在第五章中有详细介绍。

## 对象

PHP 也支持*面向对象编程*（OOP）。OOP 促进了清晰的模块化设计；简化了调试和维护；并有助于代码重用。类是面向对象设计的构建块。类是一个包含属性（变量）和方法（函数）定义的结构。类用`class`关键字定义：

```php
class Person
{
 public $name = '';

 function name ($newname = NULL)
 {
 if (!is_null($newname)) {
 $this->name = $newname;
 }

 return $this->name;
 }
}
```

一旦类被定义，可以用`new`关键字从中创建任意数量的对象，并且可以通过`->`构造访问对象的属性和方法：

```php
$ed = new Person;
$ed->name('Edison');
echo "Hello, {$ed->name} <br/>";
$tc = new Person;
$tc->name('Crapper');
echo "Look out below {$tc->name} <br/>";
`Hello``,` `Edison`
`Look` `out` `below` `Crapper`
```

使用`is_object()`函数来测试一个值是否为对象：

```php
if (is_object($x)) {
 // $x is an object
}
```

第六章详细描述了类和对象，包括继承、封装和内省。

## 资源

许多模块提供多个处理外部世界的函数。例如，每个数据库扩展至少有一个连接数据库的函数、一个查询数据库的函数和一个关闭数据库连接的函数。因为你可以同时打开多个数据库连接，连接函数在你调用查询和关闭函数时提供了一个用于标识唯一连接的东西：一个资源（或一个*句柄*）。

每个活动资源都有一个唯一的标识符。每个标识符都是一个数值索引，指向内部 PHP 查找表中所有活动资源的信息。PHP 在这个表中维护关于每个资源的信息，包括代码中对该资源的引用次数。当对资源值的最后一个引用消失时，调用创建资源的扩展来执行释放内存或关闭连接等任务：

```php
$res = database_connect(); // fictitious database connect function
database_query($res);

$res = "boo";
// database connection automatically closed because $res is redefined
```

这种自动清理的好处在于函数内部最为明显，当资源被分配给一个局部变量时。当函数结束时，变量的值会被 PHP 回收：

```php
function search() {
 $res = database_connect();
 database_query($res);
}
```

当不再有指向资源的引用时，它会自动关闭。

尽管如此，大多数扩展提供了特定的关闭或清理函数，明确调用该函数被认为是良好的风格，而不是依赖变量作用域触发资源清理。

使用`is_resource()`函数来测试一个值是否为资源：

```php
if (is_resource($x)) {
 // $x is a resource
}
```

## 回调

回调是一些函数或对象方法，由某些函数（如`call_user_func()`）使用。回调也可以通过`create_function()`方法和闭包（在第三章中描述）创建：

```php
$callback = function()
{
 echo "callback achieved";
};

call_user_func($callback);
```

## NULL

NULL 数据类型只有一个值。通过大小写不敏感的关键字`NULL`可以访问这个值。`NULL`值表示一个没有值的变量（类似于 Perl 的`undef`或 Python 的`None`）：

```php
$aleph = "beta";
$aleph = null; // variable's value is gone
$aleph = Null; // same
$aleph = NULL; // same
```

使用`is_null()`函数来测试一个值是否为`NULL`，例如检查一个变量是否有值：

```php
if (is_null($x)) {
 // $x is NULL
}
```

# 变量

PHP 中的变量是以美元符号（`$`）为前缀的标识符。例如：

```php
$name
$Age
$_debugging
$MAXIMUM_IMPACT
```

变量可以保存任何类型的值。在变量上没有编译时或运行时类型检查。可以用不同类型的值替换变量的值：

```php
$what = "Fred";
$what = 35;
$what = array("Fred", 35, "Wilma");
```

PHP 中没有明确的语法用于声明变量。第一次设置变量的值时，变量就会在内存中创建。换句话说，给变量赋值也是声明变量的方式。例如，这是一个有效的完整 PHP 程序：

```php
$day = 60 * 60 * 24;
echo "There are {$day} seconds in a day.";
`There` `are` `86400` `seconds` `in` `a` `day``.`
```

如果变量的值尚未设置，则变量的行为类似于`NULL`值：

```php
if ($uninitializedVariable === NULL) {
 echo "Yes!";
}
`Yes``!`
```

## 变量变量

可以通过在变量引用前面加一个额外的美元符号（`$`）来引用存储在另一个变量中的变量的值。例如：

```php
$foo = "bar";
$$foo = "baz";
```

第二条语句执行后，变量`$bar`的值为`"baz"`。

## 变量引用

在 PHP 中，引用是创建变量别名或指针的方式。要将`$black`设为变量`$white`的别名，请使用：

```php
$black =& $white;
```

`$black`的旧值（如果有）会丢失。现在，`$black`是存储在`$white`中的值的另一个名称：

```php
$bigLongVariableName = "PHP";
$short =& $bigLongVariableName;
$bigLongVariableName .= " rocks!";
print "\$short is $short <br/>";
print "Long is $bigLongVariableName";
`$short` `is` `PHP` `rocks``!`
`Long` `is` `PHP` `rocks``!`

$short = "Programming $short";
print "\$short is $short <br/>";
print "Long is $bigLongVariableName";
`$short` `is` `Programming` `PHP` `rocks``!`
`Long` `is` `Programming` `PHP` `rocks``!`
```

赋值后，两个变量是同一个值的替代名称。对别名变量取消设置不会影响该变量值的其他名称：

```php
$white = "snow";
$black =& $white;
unset($white);
print $black;
`snow`
```

函数可以通过引用返回值（例如，避免复制大字符串或数组，如第三章中所述）：

```php
function &retRef() // note the &
{
 $var = "PHP";

 return $var;
}

$v =& retRef(); // note the &
```

## 变量作用域

变量的*作用域*由变量声明的位置控制，决定程序中可以访问它的部分。在 PHP 中，变量的作用域有四种类型：局部、全局、静态和函数参数。

### 局部作用域

在函数中声明的变量只在该函数内部可见。也就是说，它只能被函数内的代码访问（除了嵌套函数定义）；它不能在函数外部访问。此外，默认情况下，在函数外部定义的变量（称为*全局*变量）在函数内部不可访问。例如，下面是一个更新局部变量而不是全局变量的函数示例：

```php
function updateCounter()
{
 $counter++;
}

$counter = 10;
updateCounter();

echo $counter;
`10`
```

函数内部的`$counter`是局部变量，因为我们没有明确指定。函数递增其私有的`$counter`变量，在子程序结束时销毁。全局的`$counter`保持为 10。

只有函数能提供局部作用域。与其他语言不同，在 PHP 中，你不能创建其作用域为循环、条件分支或其他类型块的变量。

### 全局作用域

在函数外声明的变量是全局的。也就是说，它们可以从程序的任何部分访问。但是，默认情况下，它们在函数内部不可用。为了允许函数访问全局变量，你可以在函数内部使用 `global` 关键字来声明函数内部的变量。下面是我们如何重写 `updateCounter()` 函数，以允许它访问全局 `$counter` 变量的示例：

```php
function updateCounter()
{
 global $counter;
 $counter++;
}

$counter = 10;
updateCounter();
echo $counter;
`11`
```

更新全局变量的一种更麻烦的方法是使用 PHP 的 `$GLOBALS` 数组，而不是直接访问变量：

```php
function updateCounter()
{
 $GLOBALS[‘counter’]++;
}

$counter = 10;
updateCounter();
echo $counter;
`11`
```

### 静态变量

静态变量在函数调用之间保持其值，但仅在该函数内部可见。你可以使用 `static` 关键字声明变量为静态变量。例如：

```php
function updateCounter()
{
 static $counter = 0;
 $counter++;

 echo "Static counter is now {$counter}<br/>";
}

$counter = 10;
updateCounter();
updateCounter();

echo "Global counter is {$counter}";
`Static` `counter` `is` `now` `1`
`Static` `counter` `is` `now` `2`
`Global` `counter` `is` `10`
```

### 函数参数

正如我们将在第三章中详细讨论的那样，函数定义可以有命名参数：

```php
function greet($name)
{
 echo "Hello, {$name}";
}

greet("Janet");
`Hello``,` `Janet`
```

函数参数是局部的，意味着它们仅在其函数内部可用。在这种情况下，`$name` 无法从 `greet()` 外部访问。

## 垃圾回收

PHP 使用*引用计数*和*写时复制*来管理内存。写时复制确保在变量之间复制值时不会浪费内存，并且引用计数确保在不再需要内存时将内存返回给操作系统。

要理解 PHP 中的内存管理，你必须首先理解*符号表*的概念。变量有两部分组成——它的名称（例如 `$name`）和它的值（例如 `"Fred"`）。符号表是一个数组，将变量名映射到它们值在内存中位置的数组。

当你将一个值从一个变量复制到另一个变量时，PHP 不会为该值创建一个新的内存空间。相反，它会更新符号表，指示“这两个变量都是指向同一块内存的名称”。所以下面的代码实际上并不创建一个新的数组：

```php
$worker = array("Fred", 35, "Wilma");
$other = $worker; // array isn't duplicated in memory
```

如果随后修改任一副本，PHP 会分配所需的内存并进行复制：

```php
$worker[1] = 36; // array is copied in memory, value changed
```

通过延迟分配和复制，PHP 在许多情况下节省时间和内存。这就是写时复制。

符号表指向的每个值都有一个*引用计数*，这个数字表示到达该内存块的方式数量。在将数组分配给 `$worker` 和将 `$worker` 分配给 `$other` 后，符号表条目所指向的数组的引用计数为 2。¹ 换句话说，这块内存可以通过 `$worker` 或 `$other` 两种方式访问。但是在修改 `$worker[1]` 后，PHP 会为 `$worker` 创建一个新的数组，并且每个数组的引用计数仅为 1。

当变量在函数结束时超出范围时，如函数参数和局部变量，其值的引用计数将减少一次。当变量在内存的不同区域被赋予一个新值时，旧值的引用计数将减少一次。当值的引用计数达到 0 时，其内存将被释放。这就是引用计数。

引用计数是管理内存的首选方法。保持变量局限于函数内部，传递函数需要处理的值，并让引用计数处理内存管理。如果您坚持想要获取有关释放变量值的更多信息或控制，请使用 `isset()` 和 `unset()` 函数。

若要查看变量是否设置为某些内容（甚至是空字符串），请使用 `isset()`：

```php
$s1 = isset($name); // $s1 is false
$name = "Fred";
$s2 = isset($name); // $s2 is true
```

使用 `unset()` 来移除变量的值：

```php
$name = "Fred";
unset($name); // $name is NULL
```

# 表达式和操作符

*表达式* 是一段 PHP 代码，可以求值为一个值。最简单的表达式是文字值和变量。文字值求值为它自身，而变量求值为存储在变量中的值。更复杂的表达式可以使用简单表达式和操作符组成。

*操作符* 接受一些值（操作数）并执行某些操作（例如，将它们加在一起）。操作符有时用标点符号表示，例如我们在数学中熟悉的 `+` 和 `-` 。某些操作符会修改它们的操作数，而大多数则不会。

表格 2-3 总结了 PHP 中的操作符，其中许多操作符来自 C 和 Perl。标有“P”的列显示了操作符的优先级；操作符按照从高到低的优先级顺序列出。标有“A”的列显示了操作符的结合性，可以是 L（从左到右）、R（从右到左）或 N（非结合性）。

表格 2-3\. PHP 操作符

| P | A | 操作符 | 操作 |
| --- | --- | --- | --- |
| 24 | N | `clone`, `new` | 创建新对象 |
| 23 | L | `` | 数组下标 |
| 22 | R | `**` | 指数运算 |
| 21 | R | `~` | 按位非 |
|   | R | `++` | 自增 |
|   | R | `−−` | 自减 |
|   | R | `(int)`, `(bool)`, `(float)`, `(string)`, `(array)`, `(object)`, `(unset)` | 强制类型转换 |
|   | R | `@` | 抑制错误 |
| 20 | N | `instanceof` | 类型检测 |
| 19 | R | `!` | 逻辑非 |
| 18 | L | `*` | 乘法 |
|   | L | `/` | 除法 |
|   | L | `%` | 取模 |
| 17 | L | `+` | 加法 |
|   | L | `−` | 减法 |
|   | L | `.` | 字符串连接 |
| 16 | L | `<<` | 左移 |
|   | L | `>>` | 右移 |
| 15 | N | `<`, `<=` | 小于，小于等于 |
|   | N | `>`, `>=` | 大于，大于等于 |
| 14 | N | `==` | 值相等 |
|   | N | `!=`, `<>` | 不等于 |
|   | N | `===` | 类型和值相等 |
|   | N | `!==` | 类型和值不等 |
|   | N | `<=>` | 基于两个操作数的比较返回一个整数：当左右相等时为 `0`，当左小于右时为 `-1`，当左大于右时为 `1`。 |
| 13 | L | `&` | 按位与 |
| 12 | L | `^` | 按位异或 |
| 11 | L | `&#124;` | 按位或 |
| 10 | L | `&&` | 逻辑与 |
| 9 | L | `&#124;&#124;` | 逻辑或 |
| 8 | R | `??` | 比较 |
| 7 | L | `?:` | 条件运算符 |
| 6 | R | `=` | 赋值 |
|   | R | `+=`, `−=`, `*=`, `/=`, `.=`, `%=`, `&=`, `&#124;=`, `^=`, `~=`, `<<=`, `>>=` | 带操作的赋值 |
| 5 |   | `yield from` | 从产出 |
| 4 |   | `yield` | 产出 |
| 3 | L | `and` | 逻辑与 |
| 2 | L | `xor` | 逻辑异或 |
| 1 | L | `or` | 逻辑或 |

## 操作数的数量

大多数 PHP 中的操作符是二元操作符；它们将两个操作数（或表达式）组合成一个更复杂的表达式。PHP 也支持几个一元操作符，将单个表达式转换为更复杂的表达式。最后，PHP 支持一些三元操作符，将多个表达式组合成单个表达式。

## 操作符优先级

表达式中操作符的求值顺序取决于它们的相对优先级。例如，您可能会写：

```php
2 + 4 * 3
```

如您在[表 2-3 中所见，加法和乘法运算符具有不同的优先级，其中乘法高于加法。因此，乘法先于加法运算，得出 `2 + 12`，即 `14`。如果加法和乘法的优先级被反转，`6 * 3`，即 `18`，将成为答案。

要强制执行特定顺序，可以使用括号将操作数分组。在我们之前的例子中，要获取值 `18`，可以使用以下表达式：

```php
(2 + 4) * 3
```

可以通过将操作数和操作符按照正确的顺序放置，使它们的相对优先级产生您想要的答案，来编写所有复杂的表达式（包含多个操作符的表达式）。然而，大多数程序员按照他们认为最合理的顺序编写操作符，并添加括号以确保 PHP 也能理解。如果优先级弄错了，会导致类似于以下的代码：

```php
$x + 2 / $y >= 4 ? $z : $x << $z
```

这段代码很难阅读，几乎肯定不会达到程序员的预期。

许多程序员处理编程语言中复杂的优先级规则的一种方式是将优先级简化为两个规则：

+   乘法和除法的优先级高于加法和减法。

+   对于其他情况，请使用括号。

## 操作符的结合性

结合性定义了在具有相同优先级顺序的操作符中的求值顺序。例如，看一下：

```php
2 / 2 * 2
```

除法和乘法运算符具有相同的优先级，但表达式的结果取决于我们首先执行哪个操作：

```php
2 / (2 * 2) // 0.5
(2 / 2) * 2 // 2
```

除法和乘法运算符是*左结合*的；这意味着在有歧义的情况下，运算符从左到右进行计算。在这个例子中，正确的结果是 2。

## 隐式类型转换

许多运算符对其操作数有特定的要求，例如，二进制数学运算符通常要求两个操作数是相同的类型。PHP 的变量可以存储整数、浮点数、字符串等，为了尽可能地将类型细节远离程序员，PHP 会根据需要将值从一种类型转换为另一种类型。

将一个值从一种类型转换为另一种类型称为*类型转换*。这种隐式转换在 PHP 中称为*类型强制转换*。算术运算符进行的类型强制转换的规则见表 2-4。

表 2-4\. 二元算术操作的隐式转换规则

| 第一个操作数的类型 | 第二个操作数的类型 | 执行的转换 |
| --- | --- | --- |
| 整数 | 浮点数 | 将整数转换为浮点数。 |
| 整数 | 字符串 | 将字符串转换为数字；如果转换后的值为浮点数，则整数转换为浮点数。 |
| 浮点数 | 字符串 | 将字符串转换为浮点数。 |

其他一些运算符对其操作数有不同的期望，因此有不同的规则。例如，字符串连接运算符在连接之前将两个操作数转换为字符串：

```php
3 . 2.74 // gives the string 32.74
```

在 PHP 期望数字的任何地方都可以使用字符串。假定字符串以整数或浮点数开头。如果在字符串开头找不到数字，则该字符串的数值为 0\. 如果字符串包含句点（`.`）或大写或小写 `e`，则将其数值化后产生一个浮点数。例如：

```php
"9 Lives" - 1; // 8 (int)
"3.14 Pies" * 2; // 6.28 (float)
"9\. Lives" - 1; // 8 (float / double)
"1E3 Points of Light" + 1; // 1001 (float)
```

## 算术运算符

算术运算符是你在日常使用中会认识到的运算符。大多数算术运算符都是二元的；然而，算术否定和算术断言运算符是一元的。这些运算符需要数值，非数值将按照“强制转换运算符”部分描述的规则转换为数值。算术运算符包括：

加法 (`+`)

加法运算符的结果是两个操作数的和。

减法 (`−`)

减法运算符的结果是两个操作数之间的差值，即第二个操作数从第一个操作数中减去的值。

乘法 (`*`)

乘法运算符的结果是两个操作数的乘积。例如，`3 * 4` 是 `12`。

除法 (`/`)

除法运算的结果是两个操作数的商。两个整数相除可以得到一个整数（例如，`4 / 2`）或者浮点数结果（例如，`1 / 2`）。

取模 (`%`)

模运算符将两个操作数转换为整数，并返回第一个操作数除以第二个操作数的余数。例如，`10 % 6` 的余数为 `4`。

算术取反 (`−`)

算术取反运算符返回操作数乘以−1，有效地改变其符号。例如，`−(3 − 4)` 的计算结果为 `1`。算术取反与减法运算符不同，尽管它们都写成减号。算术取反始终是一元的，并置于操作数之前。减法是二元的，并置于其操作数之间。

算术断言 (`+`)

算术断言运算符返回操作数乘以+1，这没有任何效果。它仅用作视觉提示，指示值的符号。例如，`+(3 − 4)` 的计算结果为 `-1`，就像 `(3 − 4)` 一样。

指数运算 (`**`)

指数运算符返回将 `$var1` 的值提高到 `$var2` 次幂的结果。

```php
$var1 = 5;
$var2 = 3;
echo $var1 ** $var2; // outputs 125
```

## 字符串连接运算符

在 PHP 应用程序中，操作字符串是非常核心的部分，因此 PHP 单独有一个字符串连接运算符 (`.`)。连接运算符将右操作数附加到左操作数并返回结果字符串。必要时，操作数首先会被转换为字符串。例如：

```php
$n = 5;
$s = 'There were ' . $n . ' ducks.';
// $s is 'There were 5 ducks'
```

字符串连接运算符非常高效，因为 PHP 大部分操作都可以归结为字符串连接。

## 自增和自减运算符

在编程中，增加或减少变量值是最常见的操作之一。一元自增 (`++`) 和自减 (`−−`) 运算符为这些常见操作提供了快捷方式。这些运算符独特之处在于它们仅适用于变量；运算符会改变它们的操作数的值并返回一个值。

在表达式中使用自增或自减有两种方法。如果将运算符放在操作数前面，它将返回操作数的新值（增加或减少后）。如果将运算符放在操作数后面，它将返回操作数的原始值（增加或减少前）。表 2-5 列出了不同的操作。

表 2-5\. 自增和自减操作

| 运算符 | 名称 | 返回的值 | 对 `$var` 的影响 |
| --- | --- | --- | --- |
| `$var++` | 后自增 | `$var` | 自增后的值 |
| `++$var` | 前自增 | `$var + 1` | 自增后的值 |
| `$var−−` | 后自减 | `$var` | 自减后的值 |
| `−−$var` | 前自减 | `$var − 1` | 自减后的值 |

这些运算符可以应用于字符串和数字。对字母进行自增操作会将其转换为字母表中的下一个字母。正如在 表 2-6 中所示，对 `"z"` 或 `"Z"` 进行自增操作会将其回环到 `"a"` 或 `"A"`，并使前一个字符自增一次（或者在字符串的第一个字符处插入新的 `"a"` 或 `"A"`），就像字符处于一个 26 进制数系统中一样。

表 2-6\. 字母的自增

| 递增此值 | 得到此值 |
| --- | --- |
| `"a"` | `"b"` |
| `"z"` | `"aa"` |
| `"spaz"` | `"spba"` |
| `"K9"` | `"L0"` |
| `"42"` | `"43"` |

## 比较运算符

正如它们的名称所示，比较运算符用于比较操作数。结果要么为`true`（如果比较为真），要么为`false`。

比较运算符的操作数可以是全数字、全字符串或一个数字和一个字符串。根据操作数的类型和值，这些运算符稍有不同地检查真实性，可以使用严格的数值比较或按字典序（文本）比较。表 2-7 概述了每种检查何时使用。

表 2-7\. 比较运算符执行的比较类型

| 第一个操作数 | 第二个操作数 | 比较 |
| --- | --- | --- |
| Number | Number | Numeric |
| 完全是数字的字符串 | 完全是数字的字符串 | 数字型 |
| 完全是数字的字符串 | 数字 | 数字型 |
| 完全是数字的字符串 | 不完全是数字的字符串 | 字典序 |
| 不完全是数字的字符串 | 数字 | 数字型 |
| 不完全是数字的字符串 | 不完全是数字的字符串 | 字典序 |

一个重要的注意事项是，两个数字字符串会被比较为数值。如果你有两个完全由数字字符组成的字符串，并且需要按字典顺序比较它们，请使用`strcmp()`函数。

比较运算符包括：

等于（`==`）

如果两个操作数相等，则该运算符返回`true`；否则返回`false`。

相同（`===`）

如果两个操作数相等且类型相同，则该运算符返回`true`；否则返回`false`。请注意，该运算符不会进行隐式类型转换。当你不确定所比较的值是否为相同类型时，此运算符非常有用。简单比较可能涉及值转换。例如，字符串 `"0.0"` 和 `"0"` 不相等。`==` 运算符说它们相等，但 `===` 运算符说它们不相等。

不等于（`!=` 或 `<>`）

如果操作数不相等，则该运算符返回`true`；否则返回`false`。

不相同（`!==`）

如果操作数不相等或它们不是相同类型，则该运算符返回`true`；否则返回`false`。

大于 (`>`)

如果左操作数大于右操作数，则该运算符返回`true`；否则返回`false`。

大于或等于 (`>=`)

如果左操作数大于或等于右操作数，则该运算符返回`true`；否则返回`false`。

小于 (`<`)

如果左操作数小于右操作数，则该运算符返回`true`；否则返回`false`。

小于或等于 (`<=`)

如果左操作数小于或等于右操作数，则该运算符返回 `true`；否则，返回 `false`。

太空船运算符（`<=>`），也称为“达斯·维达的 TIE 战斗机”

当左操作数等于右操作数时，该运算符返回 `0`；当左操作数小于右操作数时，返回 `-1`；当左操作数大于右操作数时，返回 `1`。

```php
$var1 = 5;
$var2 = 65;

`echo` $var1 <=> $var2 ; // outputs -1 `echo` $var2 <=> $var1 ; // outputs 1
```

空值合并运算符（`??`）

如果左操作数为 `NULL`，则该运算符求值为右操作数；否则，求值为左操作数。

```php
$var1 = null;
$var2 = 31;

echo $var1 ?? $var2 ; //outputs 31
```

## 位运算符

位运算符作用于其操作数的二进制表示。首先将每个操作数转换为其数值的二进制表示，如下面列表中位取反运算符条目所述。所有位运算符都可以作用于数字以及字符串，但它们在处理长度不同的字符串操作数时有所不同。位运算符包括：

位取反（`~`）

位取反运算符将操作数的二进制表示中的 1 变为 0，0 变为 1。在执行操作之前，浮点数值会转换为整数。如果操作数是字符串，则结果值是与原始字符串长度相同的字符串，其中字符串中的每个字符都被取反。

位与（`&`）

位与运算符比较操作数的二进制表示中的每个对应位。如果两个位都是 1，则结果中的对应位为 1；否则，对应位为 0。例如，`0755 & 0671` 是 `0651`。如果我们查看二进制表示，会更容易理解。八进制 `0755` 的二进制是 `111101101`，八进制 `0671` 的二进制是 `110111001`。然后我们可以轻松看出这两个数字中哪些位是相同的，并直观地得出答案：

```php
 111101101
& 110111001
 ---------
 110101001
```

二进制数 `110101001` 是八进制 `0651`。² 在尝试理解二进制算术时，可以使用 PHP 函数 `bindec()`、`decbin()`、`octdec()` 和 `decoct()` 进行数字的双向转换。

如果两个操作数都是字符串，则该运算符返回一个字符串，其中每个字符都是两个操作数中对应字符进行位与操作的结果。结果字符串的长度是两个操作数中较短的那个；在较长字符串的末尾多余字符将被忽略。例如，`"wolf" & "cat"` 是 `"cad"`。

位或（`|`）

位或运算符比较操作数的二进制表示中的每个对应位。如果两个位都是 0，则结果位为 0；否则，结果位为 1。例如，`0755 | 020` 是 `0775`。

如果两个操作数都是字符串，则运算符返回一个字符串，其中每个字符是操作数中对应字符进行位或操作的结果。结果字符串的长度为两个操作数中较长的那个，并且较短的字符串在末尾填充二进制 0。例如，`"pussy" | "cat"` 是 `"suwsy"`。

位异或 (`^`)

位异或运算符比较操作数的二进制表示中的每个对应位。如果一对中的任一位（但不是两者都是）为 1，则结果位为 1；否则，结果位为 0。例如，`0755 ^ 023` 是 `776`。

如果两个操作数都是字符串，则此运算符返回一个字符串，其中每个字符是操作数中对应字符进行位异或操作的结果。如果两个字符串长度不同，则结果字符串的长度为较短操作数的长度，并且忽略较长字符串中多余的尾部字符。例如，`"big drink" ^ "AA"` 是 `"#("`。

左移位 (`<<`)

左移位运算符将左操作数的二进制表示中的位向左移动右操作数指定的位数。如果它们尚未是整数，则两个操作数将被转换为整数。向左移动二进制数会在数字的最右边插入一个 0，并将所有其他位向左移动一个位置。例如，`3 << 1`（或二进制 11 向左移动一位）的结果是 `6`（二进制 110）。

注意，每次向左移动数字的位置都会导致数字加倍。左移的结果是将左操作数乘以右操作数的 2 的幂。

右移位 (`>>`)

右移位运算符将左操作数的二进制表示中的位向右移动右操作数指定的位数。如果它们尚未是整数，则两个操作数将被转换为整数。将正数二进制数向右移动会在数字的最左边插入一个 0，并将所有其他位向右移动一个位置。将负数二进制数向右移动会在数字的最左边插入一个 1，并将所有其他位向右移动一个位置。最右边的位被丢弃。例如，`13 >> 1`（或二进制 1101 向右移动一位）的结果是 `6`（二进制 110）。

## 逻辑运算符

逻辑运算符提供了构建复杂逻辑表达式的方法。逻辑运算符将其操作数视为布尔值并返回布尔值。运算符有标点和英文版本（`||` 和 `or` 是相同的运算符）。逻辑运算符包括：

逻辑与 (`&&`, `and`)

逻辑与操作的结果如果且仅如果两个操作数都为 `true`，则结果为 `true`；否则为 `false`。如果第一个操作数的值为 `false`，逻辑与操作符知道结果值必须也为 `false`，因此右操作数不会被评估。这个过程称为 *短路*，一个常见的 PHP 习惯用法是确保只有在某些条件为真时才评估代码。例如，你可能只有在某些标志不为 `false` 时才连接到数据库：

```php
$result = $flag and mysql_connect();
```

`&&` 和 `and` 操作符在它们的优先级上是有所不同的：`&&` 在 `and` 之前。

逻辑或 (`||`, `or`)

逻辑或操作的结果如果任一操作数为 `true`，则结果为 `true`；否则为 `false`。与逻辑与操作符类似，逻辑或操作符也是短路的。如果左操作数为 `true`，则操作符的结果必须为 `true`，因此右操作数永远不会被评估。一个常见的 PHP 习惯用法是在发生错误时触发错误条件。例如：

```php
$result = fopen($filename) or exit();
```

`||` 和 `or` 操作符在它们的优先级上是有所不同的。

逻辑异或 (`xor`)

逻辑异或操作的结果如果任一操作数为 `true` 但不是两个操作数都为 `true`，则结果为 `true`；否则为 `false`。

逻辑非 (`!`)

逻辑非操作符如果操作数评估为 `false`，则返回布尔值 `true`，如果操作数评估为 `true`，则返回布尔值 `false`。

## 强制转换操作符

虽然 PHP 是一种弱类型语言，但在某些情况下，将值视为特定类型是很有用的。强制转换操作符 `(int)`, `(float)`, `(string)`, `(bool)`, `(array)`, `(object)`, 和 `(unset)` 允许你将一个值强制转换为特定类型。要使用强制转换操作符，将操作符放在操作数的左侧。表 2-8 列出了强制转换操作符、同义操作符以及操作符将值转换为的类型。

表 2-8\. PHP 强制转换操作符

| 运算符 | 同义操作符 | 转换为 |
| --- | --- | --- |
| `(int)` | `(integer)` | 整数 |
| `(bool)` | `(boolean)` | 布尔值 |
| `(float)` | `(double)`, `(real)` | 浮点数 |
| `(string)` |   | String |
| `(array)` |   | Array |
| `(object)` |   | Object |
| `(unset)` |   | NULL |

强制转换影响其他操作符解释值的方式，而不是改变变量中的值。例如，代码：

```php
$a = "5";
$b = (int) $a;
```

将 `$b` 赋予 `$a` 的整数值；`$a` 保持字符串 `"5"`。要将变量本身的值强制转换，必须将强制转换的结果再赋回变量：

```php
$a = "5";
$a = (int) $a; // now $a holds an integer
```

并非每种强制转换都是有用的。将数组强制转换为数值类型会得到 `1`（如果数组为空，则为 `0`），将数组强制转换为字符串会得到 `"Array"`（在输出中看到这个，表明你已经打印了一个包含数组的变量）。

将对象强制转换为数组会构建一个属性数组，从而将属性名映射到值：

```php
class Person
{
 var $name = "Fred";
 var $age = 35;
}

$o = new Person;
$a = (array) $o;

print_r($a);
`Array` `(` `[``name``]` `=>` `Fred` `[``age``]` `=>` `35``)`
```

您可以将数组转换为对象，以构建一个对象，其属性对应于数组的键和值。例如：

```php
$a = array('name' => "Fred", 'age' => 35, 'wife' => "Wilma");
$o = (object) $a;
echo $o->name;
`Fred`
```

不是有效标识符的键是无效的属性名称，在将数组转换为对象时不可访问，但在将对象转换回数组时恢复。

## 赋值运算符

赋值运算符用于存储或更新变量中的值。我们之前看到的自增和自减运算符是高度专业化的赋值运算符—在这里我们看到更一般的形式。基本赋值运算符是`=`，但我们还会看到赋值和二进制运算的组合，如`+=`和`&=`。

### 赋值

基本赋值运算符 (`=`) 将一个值分配给一个变量。左操作数始终是一个变量。右操作数可以是任何表达式—任何简单的字面量、变量或复杂的表达式。右操作数的值存储在由左操作数命名的变量中。

因为所有运算符都要返回一个值，所以赋值运算符返回分配给变量的值。例如，表达式`$a = 5`不仅将`5`赋给了`$a`，而且在较大的表达式中使用时，也会表现为值`5`。考虑以下表达式：

```php
$a = 5;
$b = 10;
$c = ($a = $b);
```

表达式`$a = $b`首先被评估，因为有括号。现在，`$a`和`$b`都有相同的值`10`。最后，`$c`被赋值为表达式`$a = $b`的结果，即分配给左操作数的值（在本例中为`$a`）。当完整表达式评估完成时，所有三个变量都包含相同的值`10`。

### 带操作的赋值

除了基本赋值运算符外，还有几个方便的简写赋值运算符。这些运算符由一个二元运算符直接跟随一个等号组成，它们的效果等同于对完整操作数执行操作，然后将结果值分配给左操作数。这些赋值运算符包括：

加等号 (`+=`)

将右操作数加到左操作数的值上，然后将结果分配给左操作数。`$a += 5`与`$a = $a + 5`相同。

减等号 (`−=`)

从左操作数的值减去右操作数，然后将结果分配给左操作数。

除等号 (`/=`)

将左操作数的值除以右操作数，然后将结果分配给左操作数。

乘等号 (`*=`)

将右操作数乘以左操作数的值，然后将结果分配给左操作数。

取模等号 (`%=`)

对左操作数和右操作数的值执行模运算，然后将结果分配给左操作数。

按位异或等号 (`^=`)

对左操作数和右操作数执行按位异或，然后将结果分配给左操作数。

按位与等号 (`&=`)

对左操作数的值和右操作数执行按位与操作，然后将结果分配给左操作数。

按位或等于（`|=`）

对左操作数的值和右操作数执行按位或操作，然后将结果分配给左操作数。

连接等于（`.=`）

将右操作数连接到左操作数的值，然后将结果分配给左操作数。

## 杂项运算符

剩余的 PHP 运算符用于错误抑制、执行外部命令和选择值：

错误抑制（`@`）

一些运算符或函数可能会生成错误消息。完整讨论错误抑制运算符，请参阅第十七章。

执行（`` `...` ``）

反引号运算符执行包含在反引号之间的字符串作为 shell 命令并返回输出。例如：

```php
$listing = `ls -ls /tmp`;
echo $listing;
```

条件（`? :`）

条件运算符是根据您查看的代码而定，可能是最常用或最不常用的运算符之一。它是唯一的三元（三操作数）运算符，因此有时只被称为三元运算符。

条件运算符在`?`之前评估表达式。如果表达式为`true`，则运算符返回`?`和`:`之间的表达式的值；否则，运算符返回`:`之后的表达式的值。例如：

```php
<a href="<? echo $url; ?>"><? echo $linktext ? $linktext : $url; ?></a>
```

如果变量`$linktext`中存在链接`$url`的文本，则将其用作链接的文本；否则，显示 URL 本身。

类型（`instanceof`）

`instanceof`运算符测试变量是否是给定类的实例化对象或实现接口（有关对象和接口的更多信息，请参阅第六章）：

```php
$a = new Foo;
$isAFoo = $a instanceof Foo; // true
$isABar = $a instanceof Bar; // false
```

# 流程控制语句

PHP 支持许多传统的编程构造来控制程序的执行流程。

条件语句，例如`if`/`else`和`switch`，允许程序根据某些条件执行不同的代码片段，或者根本不执行。循环，例如`while`和`for`，支持对代码段的重复执行。

## if

`if`语句检查表达式的真实性，如果表达式为真，则评估一个语句。一个`if`语句看起来像：

```php
if (*`expression`*)*`statement`*
```

若要指定在表达式为假时执行的替代语句，请使用`else`关键字：

```php
if (*`expression`*)
 *`statement`*
else *`statement`*
```

例如：

```php
if ($user_validated)
 echo "Welcome!";
else
 echo "Access Forbidden!";
```

若要在`if`语句内包含多个语句，请使用*块*—由花括号括起来的一组语句：

```php
if ($user_validated) {
 echo "Welcome!";
 $greeted = 1;
}
else {
 echo "Access Forbidden!";
 exit;
}
```

PHP 为测试和循环中的块提供了另一种语法。不是用花括号将语句块括起来，而是在`if`行末尾使用冒号（`:`）并使用特定关键字来结束块（在这种情况下为`endif`）。例如：

```php
if ($user_validated):
 echo "Welcome!";
 $greeted = 1;
else:
 echo "Access Forbidden!";
 exit;
endif;
```

本章中描述的其他语句也有类似的备选语法样式（和结束关键字）；如果您的语句内部有大量的 HTML 块，它们可能非常有用。例如：

```php
<?php if ($user_validated) : ?>
 <table>
 <tr>
 <td>First Name:</td><td>Sophia</td>
 </tr>
 <tr>
 <td>Last Name:</td><td>Lee</td>
 </tr>
 </table>
<?php else: ?>
 Please log in.
<?php endif ?>
```

因为`if`是一个语句，您可以链接（嵌套）多个。这也是如何使用块来帮助保持组织的一个很好的例子：

```php
if ($good) {
 print("Dandy!");
}
else {
 if ($error) {
 print("Oh, no!");
 }
 else {
 print("I'm ambivalent...");
 }
}
```

PHP 提供了一个更简单的语法来处理这样的`if`语句链：`elseif`语句。例如，前面的代码可以重写为：

```php
if ($good) {
 print("Dandy!");
}
elseif ($error) {
 print("Oh, no!");
}
else {
 print("I'm ambivalent...");
}
```

三元条件运算符（`? :`）可用于缩短简单的真/假测试。考虑一个常见的情况，例如检查给定变量是否为 true 并在其为 true 时打印某些内容。使用普通的`if`/`else`语句，看起来像这样：

```php
<td><?php if($active) { echo "yes"; } else { echo "no"; } ?></td>
```

使用三元条件运算符时，看起来像这样：

```php
<td><?php echo $active ? "yes" : "no"; ?></td>
```

比较这两者的语法：

```php
if (*`expression`*) { *`true_statement`* } else { *`false_statement`* }
 (*`expression`*) ? *`true_expression`* : *`false_expression`*
```

这里的主要区别在于条件运算符根本不是一个语句。这意味着它用于表达式，完整的三元表达式的结果本身就是一个表达式。在上面的例子中，`echo`语句位于`if`条件内部，而使用三元运算符时，它位于表达式之前。

## 开关

单个变量的值可能决定多个不同的选择（例如，变量保存用户名，您希望针对每个用户执行不同的操作）。`switch`语句正是为这种情况设计的。

`switch`语句给定一个表达式并将其值与 switch 中的所有 case 进行比较；匹配 case 中的所有语句都会执行，直到找到第一个`break`关键字。如果没有匹配，且有`default`，则执行`default`关键字后的所有语句，直到遇到第一个`break`关键字。

例如，假设您有以下内容：

```php
if ($name == 'ktatroe') {
 // do something
}
else if ($name == 'dawn') {
 // do something
}
else if ($name == 'petermac') {
 // do something
}
else if ($name == 'bobk') {
 // do something
}
```

您可以用以下`switch`语句替换该语句：

```php
switch($name) {
 case 'ktatroe':
 // do something
 break;
 case 'dawn':
 // do something
 break;
 case 'petermac':
 // do something
 break;
 case 'bobk':
 // do something
 break;
}
```

这种的替代语法是：

```php
switch($name):
 case 'ktatroe':
 // do something
 break;
 case 'dawn':
 // do something
 break;
 case 'petermac':
 // do something
 break;
 case 'bobk':
 // do something
 break;
endswitch;
```

因为语句从匹配的 case 标签到下一个`break`关键字被执行，您可以将多个 case 结合在一起进行*穿透*。在下面的例子中，当`$name`等于`sylvie`或`bruno`时，会打印出"yes"：

```php
switch ($name) {
 case 'sylvie': // fall-through
 case 'bruno':
 print("yes");
 break;
 default:
 print("no");
 break;
}
```

在`switch`中注释您使用了穿透 case 是个好主意，这样别人就不会以为您忘记了加上`break`。

您可以为`break`关键字指定可选的中断级别数。这样，`break`语句可以跳出多层嵌套的`switch`语句。下一节中展示了使用`break`的示例。

## 当

最简单的循环形式是`while`语句：

```php
while (*`expression`*)*`statement`*
```

如果*表达式*计算结果为`true`，则执行*语句*，然后重新评估*表达式*（如果仍为`true`，则再次执行循环体，依此类推）。当*表达式*不再为真时（即计算结果为`false`时），循环退出。

作为示例，这里是一些将整数从 1 加到 10 的代码：

```php
$total = 0;
$i = 1;

while ($i <= 10) {
 $total += $i;
 $i++;
}
```

`while`的替代语法具有以下结构：

```php
while (*`expr`*):
 *`statement``;`*
 *`more` `statements`* ;
endwhile;
```

例如：

```php
$total = 0;
$i = 1;

while ($i <= 10):
 $total += $i;
 $i++;
endwhile;
```

您可以使用`break`关键字提前退出循环。在以下代码中，一旦`$i`达到`5`，循环就会停止，因此`$i`永远不会达到`6`：

```php
$total = 0;
$i = 1;

while ($i <= 10) {
 if ($i == 5) {
 break; // breaks out of the loop
 }

 $total += $i;
 $i++;
}
```

可选地，在`break`关键字后面可以放一个数字，指示要跳出的循环结构级别数。通过这种方式，嵌套循环中深埋的语句可以跳出最外层循环。例如：

```php
$i = 0;
$j = 0;

while ($i < 10) {
 while ($j < 10) {
 if ($j == 5) {
 break 2; // breaks out of two while loops
 }

 $j++;
 }

 $i++;
}

echo "{$i}, {$j}";
`0``,` `5`
```

`continue`语句跳到下一个循环条件测试。与`break`关键字一样，您可以跨可选的循环结构级别继续：

```php
while ($i < 10) {
 $i++;

 while ($j < 10) {
 if ($j == 5) {
 continue 2; // continues through two levels
 }

 $j++;
 }
}
```

在此代码中，`$j`永远不会超过`5`，但`$i`会经历从`0`到`9`的所有值。

PHP 还支持`do`/`while`循环，其形式如下：

```php
do
 *`statement`*
while (*`expression`*)
```

使用`do`/`while`循环确保至少执行一次循环体（第一次）：

```php
$total = 0;
$i = 1;

do {
 $total += $i++;
} while ($i <= 10);
```

您可以在`do`/`while`语句中像在普通`while`语句中一样使用`break`和`continue`语句。

当发生错误条件时，有时会使用`do`/`while`语句来跳出代码块。例如：

```php
do {
 // do some stuff

 if ($errorCondition) {
 break;
 }

 // do some other stuff
} while (false);
```

因为循环的条件为`false`，所以无论循环内部发生什么，循环只执行一次。但是，如果发生错误，则`break`后面的代码不会被评估。

## for

`for`语句类似于`while`语句，但它添加了计数器初始化和计数器操作表达式，通常比等效的`while`循环更短且更易读。

这里是一个`while`循环，从 0 到 9 进行计数，并打印每个数字：

```php
$counter = 0;

while ($counter < 10) {
 echo "Counter is {$counter} <br/>";
 $counter++;
}
```

这是相应的更简洁的`for`循环：

```php
for ($counter = 0; $counter < 10; $counter++) {
 echo "Counter is $counter <br/>";
}
```

`for`语句的结构是：

```php
for (*`start`*; *`condition`*; *`increment`*) { *`statement``(``s``);`* }
```

表达式*start*在`for`语句开始时评估一次。每次循环时，表达式*condition*被测试。如果为`true`，则执行循环体；如果为`false`，则循环结束。表达式*increment*在运行循环体后评估。

`for`语句的替代语法是：

```php
for (*`expr1`*; *`expr2`*; *`expr3`*):
 *`statement``;`*
 *`...``;`*
endfor;
```

此程序使用`for`循环将数字从 1 加到 10：

```php
$total = 0;

for ($i= 1; $i <= 10; $i++) {
 $total += $i;
}
```

使用替代语法的相同循环如下所示：

```php
$total = 0;

for ($i = 1; $i <= 10; $i++):
 $total += $i;
endfor;
```

您可以通过用逗号分隔表达式来为`for`语句中的任何表达式指定多个表达式。例如：

```php
$total = 0;

for ($i = 0, $j = 1; $i <= 10; $i++, $j *= 2) {
 $total += $j;
}
```

您还可以将表达式留空，表示该阶段不执行任何操作。在最简单的情况下，`for`语句变成一个无限循环。您可能不希望运行此示例，因为它永远不会停止打印：

```php
for (;;) {
 echo "Can't stop me!<br />";
}
```

在`for`循环中，与`while`循环一样，您可以使用`break`和`continue`关键字来结束循环或当前迭代。

## foreach

`foreach`语句允许您遍历数组中的元素。在第五章中进一步讨论了`foreach`语句的两种形式，更深入地讨论了数组。要循环访问数组并访问每个键处的值，请使用：

```php
foreach (*`$array`* as *`$current`*) {
 // ... }
```

替代语法是：

```php
foreach (*`$array`* as *`$current`*):
 // ... endforeach;
```

要循环访问数组并访问键和值，请使用：

```php
foreach (*`$array`* as *`$key` `=>` `$value`*) {
 // ... }
```

替代语法是：

```php
foreach (*`$array`* as *`$key` `=>` `$value`*):
 // ... endforeach;
```

## try...catch

`try...catch`结构不仅仅是一个流程控制结构，它更像是一种更优雅的处理系统错误的方式。例如，如果要确保您的 Web 应用在继续之前有一个有效的连接到数据库，您可以编写如下代码：

```php
try {
 $dbhandle = new PDO('mysql:host=localhost; dbname=library', $username, $pwd);
 doDB_Work($dbhandle); // call function on gaining a connection
 $dbhandle = null; // release handle when done
}
catch (PDOException $error) {
 print "Error!: " . $error->getMessage() . "<br/>";
 die();
}
```

在这里，连接尝试使用构造的`try`部分，并且如果有任何与之相关的错误，则代码流会自动跳转到`catch`部分，其中`PDOException`类被实例化到`$error`变量中。然后可以在屏幕上显示它，并且代码可以“优雅”地失败，而不是突然结束。甚至可以尝试连接到备用数据库，或者在`catch`部分内以其他任何方式响应错误。

###### 注意

参见第九章，了解与 PDO（PHP 数据对象）和事务处理相关的更多`try...catch`示例。

## declare

`declare`语句允许您为代码块指定执行指令。`declare`语句的结构如下：

```php
declare (*`directive`*)*`statement`*
```

目前只有三种`declare`形式：`ticks`、`encoding`和`strict_types`指令。您可以使用`ticks`指令指定在调用`register_tick_function()`时注册 tick 函数的频率（大致以代码语句数量计算）。例如：

```php
register_tick_function("someFunction");

declare(ticks = 3) {
 for($i = 0; $i < 10; $i++) {
 // do something
 }
}
```

在此代码中，在执行块内每三条语句后都调用`someFunction()`。

您可以使用`encoding`指令指定 PHP 脚本的输出编码。例如：

```php
declare(encoding = "UTF-8");
```

除非使用`--enable-zend-multibyte`选项编译 PHP，否则会忽略此形式的`declare`语句。

最后，您可以使用`strict_types`指令在定义和使用变量时强制使用严格数据类型。

## 退出和返回

一旦到达，`exit`语句将结束脚本的执行。`return`语句从函数中返回，或者在程序顶层时从脚本返回。

`exit`语句接受一个可选值。如果是数字，则为进程的退出状态。如果是字符串，则在进程终止之前打印该值。函数`die()`是此形式`exit`语句的别名：

```php
$db = mysql_connect("localhost", $USERNAME, $PASSWORD);

if (!$db) {
 die("Could not connect to database");
}
```

这通常被写成：

```php
$db = mysql_connect("localhost", $USERNAME, $PASSWORD)
 or die("Could not connect to database");
```

参见第三章，了解在函数中使用`return`语句的更多信息。

## 转到

`goto` 语句允许执行“跳转”到程序的另一个位置。您通过添加标签来指定执行点，标签是由标识符后跟冒号（`:`）组成。然后，您可以通过 `goto` 语句从脚本的另一个位置跳转到该标签：

```php
for ($i = 0; $i < $count; $i++) {
 // oops, found an error
 if ($error) {
 goto cleanup;
 }
}

cleanup:
// do some cleanup
```

您只能在与 `goto` 语句本身相同的作用域内跳转到标签，并且不能跳转到循环或开关中。通常，您可以重写代码以更清晰地处理 `goto`（或多级 `break` 语句）的任何地方。

# 包括代码

PHP 提供了两种结构来加载来自另一个模块的代码和 HTML：`require` 和 `include`。这两者在 PHP 脚本运行时加载文件，在条件和循环中工作，并在找不到要加载的文件时报错。文件可以通过使用函数中的包含文件路径作为指令的一部分来定位，或者基于 *php.ini* 文件中 `include_path` 的设置。`include_path` 可以通过 `set_include_path()` 函数进行覆盖。如果所有这些途径都失败了，PHP 的最后尝试是在调用脚本的同一目录中查找文件。主要区别在于尝试 `require` 一个不存在的文件会导致致命错误，而尝试 `include` 这样一个文件会产生警告但不会停止脚本执行。

`include` 的常见用途是将特定于页面的内容与通用站点设计分离。常见元素（如标题和页脚）放在单独的 HTML 文件中，然后每个页面看起来像：

```php
<?php include "header.html"; ?>
*`content`*
<?php include "footer.html"; ?>
```

我们使用 `include` 是因为它允许 PHP 继续处理页面，即使在站点设计文件中存在错误。`require` 结构则不太宽容，更适合加载代码库，如果库未加载，则无法显示页面。例如：

```php
require "codelib.php";
mysub(); // defined in codelib.php
```

处理标题和页脚的稍微更有效的方法是加载单个文件，然后调用函数生成标准化的站点元素：

```php
<?php require "design.php";
header(); ?>
*`content`*
<?php footer();
```

如果 PHP 无法解析通过 `include` 或 `require` 添加的文件的某些部分，则会打印警告并继续执行。您可以在调用前加上静默操作符（`@`）来消除警告 - 例如，`@include`。

如果通过 PHP 的配置文件 *php.ini* 启用了 `allow_url_fopen` 选项，则可以通过提供 URL 而不是简单的本地路径来包含来自远程站点的文件：

```php
include "http://www.example.com/codelib.php";
```

如果文件名以 *http://*、*https://* 或 *ftp://* 开头，则从远程站点检索并加载文件。

使用 `include` 和 `require` 包含的文件可以任意命名。常见的扩展名包括 *.php*、*.php5* 和 *.html*。

###### 注意

注意，从启用了 PHP 的 Web 服务器获取以 *.php* 结尾的文件将获取该 PHP 脚本的输出 - 它执行该文件中的 PHP 代码。

如果程序使用`include`或`require`两次包含同一文件（例如在循环中错误地执行），则加载文件并运行代码，或者 HTML 打印两次。这可能导致关于函数重定义的错误，或者发送多个标题或 HTML 副本。为防止这些错误发生，使用`include_once`和`require_once`结构。它们在第一次加载文件时行为与`include`和`require`相同，但会静默地忽略后续尝试加载同一文件。例如，许多页面元素，每个存储在单独文件中，需要知道当前用户的偏好设置。元素库应使用`require_once`加载用户偏好设置库。然后页面设计人员可以包含一个页面元素，而不必担心用户偏好代码是否已加载。

在包含文件中的代码被导入到`include`语句所在位置的作用域中，因此包含的代码可以查看和更改您的代码的变量。这可能是有用的——例如，用户跟踪库可能会将当前用户的名称存储在全局`$user`变量中：

```php
// main page
include "userprefs.php";
echo "Hello, {$user}.";
```

库查看和更改您的变量的能力也可能是一个问题。您必须知道库使用的每个全局变量，以确保不会意外尝试将其中一个用于自己的目的，从而覆盖库的值并扰乱其工作方式。

如果`include`或`require`结构位于函数中，则包含文件中的变量成为该函数的函数作用域变量。

因为`include`和`require`是关键字，而不是真正的语句，所以在条件和循环语句中，您必须始终将它们括在花括号中：

```php
for ($i = 0; $i < 10; $i++) {
 include "repeated_element.html";
}
```

使用`get_included_files()`函数来了解您的脚本包含或需要的文件。它返回一个包含每个已包含或需要的文件的完整系统路径文件名的数组。未解析的文件不包括在此数组中。

# 在 Web 页面中嵌入 PHP

虽然可以编写并运行独立的 PHP 程序，但大多数 PHP 代码都嵌入在 HTML 或 XML 文件中。毕竟，这正是它首次创建的原因。处理这些文档涉及将每个 PHP 源代码块替换为执行时产生的输出。

因为单个文件通常包含 PHP 和非 PHP 源代码，我们需要一种方法来识别要执行的 PHP 代码区域。PHP 提供了四种不同的方法来实现这一点。

正如您将看到的，第一种和首选的方法看起来像 XML。第二种方法看起来像 SGML。第三种方法基于 ASP 标签。第四种方法使用标准的 HTML `<script>` 标签；这使得可以使用常规 HTML 编辑器轻松编辑启用 PHP 的页面。

## 标准（XML）样式

由于可扩展标记语言（XML）的出现以及 HTML 向 XML 语言（XHTML）的迁移，目前首选的嵌入 PHP 的技术使用符合 XML 的标记来表示 PHP 指令。

在 XML 中定义 PHP 命令标签很容易，因为 XML 允许定义新标签。要使用此样式，请用`<?php`和`?>`包围你的 PHP 代码。这些标记之间的所有内容都被解释为 PHP，标记外的内容则不是。虽然不必在标记和封闭文本之间包含空格，但这样做会增加可读性。例如，要让 PHP 打印“Hello, world”，可以在网页中插入以下行：

```php
<?php echo "Hello, world"; ?>
```

语句的结尾分号是可选的，因为块的结尾也强制结束表达式。嵌入在完整的 HTML 文件中时，看起来像这样：

```php
<!doctype html>
<html>
<head>
 <title>This is my first PHP program!</title>
</head>

<body>
<p>
 Look, ma! It's my first PHP program:<br />
 <?php echo "Hello, world"; ?><br />
 How cool is that?
</p>
</body>

</html>
```

当然，这并不是很令人兴奋——我们可以在没有 PHP 的情况下完成它。PHP 真正的价值在于我们将来会将来自数据库和表单值等来源的动态信息放入网页中。不过，这是后面的章节内容了。让我们回到我们的“Hello, world”示例。当用户访问此页面并查看其源代码时，看起来像这样：

```php
<!doctype html>
<html>
<head>
 <title>This is my first PHP program!</title>
</head>

<body>
<p>
 Look, ma! It's my first PHP program:<br />
 Hello, world!<br />
 How cool is that?
</p>
</body>

</html>
```

请注意，原始文件中的 PHP 源代码已经消失了。用户只看到其输出。

还要注意，我们在一行内在 PHP 和非 PHP 之间切换。PHP 指令可以放在文件中的任何位置，甚至在有效的 HTML 标记内。例如：

```php
<input type="text" name="first_name" value="<?php echo "Peter"; ?>" />
```

当 PHP 完成此文本时，将读取：

```php
<input type="text" name="first_name" value="Peter" />
```

开始和结束标记内的 PHP 代码不必在同一行上。如果 PHP 指令的结束标记是最后一行的内容，那么跟在结束标记后的换行符也会被移除。因此，我们可以用以下方式替换“Hello, world”示例中的 PHP 指令：

```php
<?php
echo "Hello, world"; ?>
<br />
```

结果 HTML 没有任何变化。

## SGML 风格

另一种嵌入 PHP 的样式来自 SGML 指令处理标签。要使用此方法，只需在`<?`和`?>`中包围 PHP 即可。以下是“Hello, world”示例：

```php
<? echo "Hello, world"; ?>
```

这种风格称为*短标签*，默认情况下是关闭的。可以通过使用`--enable-short-tags`选项构建 PHP 或在 PHP 配置文件中启用`short_open_tag`来支持短标签。这是不鼓励的，因为它依赖于此设置的状态；如果将代码导出到另一个平台，可能会工作也可能不会。

内联代码，`<?= ... ?>`，即使短标签不可用也可以使用。

## 直接回显内容

在 PHP 应用程序中最常见的操作之一可能是向用户显示数据。在 Web 应用程序的上下文中，这意味着将将在用户查看时变成 HTML 的信息插入到 HTML 文档中。

为了简化这个操作，PHP 提供了一种特殊版本的 SGML 标记，它自动获取标记内部的值并将其插入到 HTML 页面中。要使用此功能，请在开放标记中添加等号（`=`）。使用此技术，我们可以将我们的表单示例重写为：

```php
<input type="text" name="first_name" value="<?= "Dawn"; ?>">
```

# 下一步

现在您已经掌握了语言的基础——变量的基本理解及其命名方式，数据类型的概念，以及代码流控制的工作原理——我们将继续讨论 PHP 语言的一些细节。接下来，我们将涵盖三个对 PHP 非常重要的主题，它们各自都有专门的章节：如何定义函数（第三章），操作字符串（第四章），以及管理数组（第五章）。

¹ 如果您从 C API 的角度看引用计数，实际上是 3，但为了本说明以及从用户空间的角度来看，将其视为 2 更容易理解。

² 这里有个小提示：将二进制数分成三组——6 是二进制 110，5 是二进制 101，1 是二进制 001；因此，0651 的二进制表示为 110101001。
