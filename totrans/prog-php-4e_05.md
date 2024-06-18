# 第四章。字符串

编程中遇到的大多数数据都是字符序列，或*字符串*。字符串可以包含人名、密码、地址、信用卡号码、照片链接、购买历史等。因此，PHP 提供了丰富的函数来处理字符串。

本章展示了在程序中创建字符串的许多方法，包括有时棘手的*插值*主题（将变量的值放入字符串中），然后涵盖了用于更改、引用、操作和搜索字符串的函数。通过本章，您将成为处理字符串的专家。

# 引用字符串常量

在编写 PHP 代码中，有四种方法可以写字符串文字：使用单引号、双引号，从 Unix shell 派生的*here document*（heredoc）格式，以及其“堂兄弟”*now document*（nowdoc）。这些方法在是否识别特殊的*转义序列*以及插入变量方面有所不同。

## 变量插值

当您使用双引号或 heredoc 定义字符串文字时，该字符串将被*变量插值*处理。插值是将字符串中的变量名替换为它们包含的值的过程。有两种方法将变量插值到字符串中。

两种插值变量的方式中，较简单的一种是将变量名放在双引号字符串或 heredoc 中：

```php
$who = 'Kilroy';
$where = 'here';
echo "$who was $where";
`Kilroy` `was` `here`
```

另一种方式是用花括号包围要插值的变量。使用这种语法可以确保正确地插值变量。花括号的经典用法是消除变量名与周围文本的歧义：

```php
$n = 12;
echo "You are the {$n}th person";
`You` `are` `the` `12``th` `person`
```

如果没有花括号，PHP 将尝试打印 `$nth` 变量的值。

与某些 shell 环境不同，在 PHP 中，字符串不会重复处理插值。相反，在双引号字符串中首先处理任何插值，然后使用结果作为字符串的值：

```php
$bar = 'this is not printed';
$foo = '$bar'; // single quotes print("$foo");
`$bar`
```

## 单引号字符串

单引号字符串和 nowdoc 不会插值变量。因此，以下字符串中的变量名不会被扩展，因为它出现在单引号字符串文字中：

```php
$name = 'Fred';
$str = 'Hello, $name'; // single-quoted echo $str;
`Hello``,` `$name`
```

在单引号字符串中，唯一有效的转义序列是 `\'`，用于在单引号字符串中插入单引号，以及 `\\`，用于在单引号字符串中插入反斜杠。任何其他反斜杠的出现都会简单地被解释为一个反斜杠：

```php
$name = 'Tim O\'Reilly';// escaped single quote echo $name;
$path = 'C:\\WINDOWS'; // escaped backslash echo $path;
$nope = '\n'; // not an escape echo $nope;
`Tim` `O``'``Reilly`
`C``:``\WINDOWS`
`\n`
```

## 双引号字符串

双引号字符串会插入变量并扩展许多 PHP 转义序列。Table 4-1 列出了 PHP 在双引号字符串中识别的转义序列。

表 4-1。双引号字符串中的转义序列

| 转义序列 | 表示的字符 |
| --- | --- |
| `\"` | 双引号 |
| `\n` | 换行符 |
| `\r` | 回车符 |
| `\t` | 制表符 |
| `\\` | 反斜杠 |
| `\$` | 美元符号 |
| `\{` | 左花括号 |
| `\}` | 右花括号 |
| `\[` | 左方括号 |
| `\]` | 右方括号 |
| `\0` 到 `\777` | 由八进制值表示的 ASCII 字符 |
| `\x0` 到 `\xFF` | 由十六进制值表示的 ASCII 字符 |
| `\u` | UTF-8 编码 |

如果在双引号字符串字面量中发现未知的转义序列（即反斜杠后面跟一个不属于表 4-1 中的字符），则将其忽略（如果设置了警告级别`E_NOTICE`，则会生成警告以表示此类未知转义序列）：

```php
$str = "What is \c this?";// unknown escape sequence echo $str;
`What` `is` `\c` `this``?`
```

## Here Documents

你可以使用 heredoc 轻松将多行字符串放入程序中，如下所示：

```php
$clerihew = <<< EndOfQuote
Sir Humphrey Davy
Abominated gravy.
He lived in the odium
Of having discovered sodium.

EndOfQuote;
echo $clerihew;
`Sir` `Humphrey` `Davy`
`Abominated` `gravy``.`
`He` `lived` `in` `the` `odium`
`Of` `having` `discovered` `sodium``.`
```

`<<<`标识符令牌告诉 PHP 解析器你正在编写 heredoc。你可以选择标识符（在本例中为`EndOfQuote`），如果愿意，可以用双引号括起来（例如，`"EndOfQuote"`）。接下来的一行开始了 heredoc 引用的文本，直到遇到只包含标识符的行为止。为了确保引用的文本在输出区域中显示为你所编排的样子，可以通过在代码文件顶部添加此命令来打开纯文本模式：

```php
header('Content-Type: text/plain;');
```

或者，如果你可以控制服务器设置，你可以在*php.ini*文件中将`default_mimetype`设置为`plain`。

```php
default_mimetype = "text/plain"
```

然而，这并不推荐，因为它会将服务器的*所有*输出都置于纯文本模式，这会影响大多数 Web 代码的布局。

如果你没有为 heredoc 设置纯文本模式，默认通常是 HTML 模式，这会将输出显示为单行。

当使用 heredoc 表示一个简单表达式时，你可以在终止标识符后加上分号来结束语句（正如第一个例子所示）。然而，如果你在更复杂的表达式中使用 heredoc，你需要在下一行继续表达式，就像这里所示：

```php
printf(<<< Template
%s is %d years old.
Template
, "Fred", 35);
```

在 heredoc 中，单引号和双引号都是被保留的：

```php
$dialogue = <<< NoMore
"It's not going to happen!" she fumed.
He raised an eyebrow. "Want to bet?"
NoMore;
echo $dialogue;
`"``It's not going to happen!``"` `she` `fumed``.`
`He` `raised` `an` `eyebrow``.` `"``Want to bet?``"`
```

正如空格所示：

```php
$ws = <<< Enough
 boo
 hoo
Enough;
// $ws = " boo\n hoo";
```

在 PHP 7.3 中引入了 heredoc 结束标记的缩进功能。这允许在嵌入代码的情况下更易读地格式化，就像以下函数所示：

```php
function sayIt() {
 $ws = <<< "StufftoSay"
 The quick brown fox
 Jumps over the lazy dog.
 StufftoSay;
return $ws;
}

echo sayIt() ;

    `The` `quick` `brown` `fox`
 `Jumps` `over` `the` `lazy` `dog``.`
```

结束标识符之前的换行符被移除，因此这两个赋值是相同的：

```php
$s = 'Foo';
// same as
$s = <<< EndOfPointlessHeredoc
Foo
EndOfPointlessHeredoc;
```

如果你希望在 heredoc 引用的字符串末尾添加一个换行符，你需要自己添加一个：

```php
$s = <<< End
Foo

End;
```

# 打印字符串

有四种方式将输出发送到浏览器。`echo`结构允许你一次打印多个值，而`print()`只打印一个值。`printf()`函数通过将值插入模板中构建格式化字符串。`print_r()`函数对于调试很有用；它以更或少人类可读的形式打印数组、对象和其他内容。

## echo

要将字符串放入 PHP 生成页面的 HTML 中，请使用`echo`。虽然看起来——并且在大多数情况下行为也像一个函数——`echo`是一个语言结构。这意味着你可以省略括号，因此以下表达式是等效的：

```php
echo "Printy";
echo("Printy"); // also valid
```

你可以通过用逗号分隔它们来指定要打印的多个项目：

```php
echo "First", "second", "third";
`Firstsecondthird`
```

在尝试回显多个值时使用括号会导致解析错误：

```php
// this is a parse error
echo("Hello", "world");
```

因为 `echo` 不是真正的函数，所以不能将其作为较大表达式的一部分使用：

```php
// parse error
if (echo("test")) {
 echo("It worked!");
}
```

通过使用 `print()` 或 `printf()` 函数，您可以轻松纠正此类错误。

## print()

`print()` 函数将一个值（其参数）发送到浏览器：

```php
if (print("test\n")) {
 print("It worked!");
}
`test`
`It` `worked``!`
```

## printf()

`printf()` 函数通过将值替换到模板（*格式字符串*）中来输出一个字符串。它源自标准 C 库中同名函数。`printf()` 的第一个参数是格式字符串。其余参数是要替换的值。格式字符串中的 `%` 字符表示一个替换位置。

### 格式修饰符

模板中的每个替换标记均以百分号（`%`）开头，可能后跟以下列表中的修饰符，并以类型指示符结尾。（使用 `%%` 可在输出中获取单个百分号字符。）修饰符必须按照以下顺序出现：

1.  填充修饰符表示用于将结果填充到适当字符串大小的字符。指定 `0`、空格或任何以单引号前缀的字符。默认情况下使用空格进行填充。

1.  符号。对于字符串与数字的影响不同。对于字符串，此处的减号（`–`）会强制字符串左对齐（默认为右对齐）。对于数字，此处的加号（`+`）会强制正数带有前导加号（例如，`35` 将打印为 `+35`）。

1.  此元素应包含的最小字符数。如果结果少于这些字符数，则符号和填充修饰符将决定如何填充到此长度。

1.  对于浮点数，精度修饰符包含一个点和一个数字；这决定了将显示多少位小数。对于除 double 以外的类型，此修饰符将被忽略。

### 类型指示符

类型指示符告诉 `printf()` 正在替换的数据类型。这决定了先前列出的修饰符的解释。共有八种类型，如 表 4-2 所列。

表 4-2\. printf() 类型指示符

| 指示符 | 含义 |
| --- | --- |
| `%` | 显示百分号。 |
| `b` | 参数为整数，并以二进制数显示。 |
| `c` | 参数为整数，并按该值显示为字符。 |
| `d` | 参数为整数，并以十进制数显示。 |
| `e` | 参数为双精度浮点数，并以科学计数法显示。 |
| `E` | 参数为双精度浮点数，并以大写字母科学计数法显示。 |
| `f` | 参数为浮点数，并以当前区域设置的格式显示。 |
| `F` | 参数为浮点数，并以此类形式显示。 |
| `g` | 参数是双精度浮点数，显示为科学计数法（如 `%e` 类型说明符）或浮点数（如 `%f` 类型说明符），以较短的方式显示。 |
| `G` | 参数是双精度浮点数，显示为科学计数法（如 `%E` 类型说明符）或浮点数（如 `%f` 类型说明符），以较短的方式显示。 |
| `o` | 参数是整数，以八进制（基数 8）数显示。 |
| `s` | 参数是字符串，显示为该字符串。 |
| `u` | 参数是无符号整数，以十进制数显示。 |
| `x` | 参数是整数，以十六进制（基数 16）数显示；使用小写字母。 |
| `X` | 参数是整数，以十六进制（基数 16）数显示；使用大写字母。 |

对于不是 C 程序员的人来说，`printf()` 函数看起来异常复杂。不过一旦你习惯了，你会发现它是一个非常强大的格式化工具。以下是一些示例：

+   保留到小数点后两位的浮点数：

    ```php
    printf('%.2f', 27.452);
    `27.45`
    ```

+   十进制和十六进制输出：

    ```php
    printf('The hex value of %d is %x', 214, 214);
    `The` `hex` `value` `of` `214` `is` `d6`
    ```

+   整数填充到三位小数：

    ```php
    printf('Bond. James Bond. %03d.', 7);
    `Bond``.` `James` `Bond``.` `007.`
    ```

+   格式化日期：

    ```php
    printf('%02d/%02d/%04d', $month, $day, $year);
    `02``/``15``/``2005`
    ```

+   百分比：

    ```php
    printf('%.2f%% Complete', 2.1);
    `2.10``%` `Complete`
    ```

+   浮点数填充：

    ```php
    printf('You\'ve spent $%5.2f so far', 4.1);
    `You``'``ve` `spent` `$` `4.10` `so` `far`
    ```

`sprintf()` 函数与 `printf()` 接受相同的参数，但返回构建好的字符串而不是将其打印出来。这让你可以将字符串保存在变量中以供后续使用：

```php
$date = sprintf("%02d/%02d/%04d", $month, $day, $year);
// now we can interpolate $date wherever we need a date
```

## print_r() 和 var_dump()

`print_r()` 函数会智能地显示传递给它的内容，而不像 `echo` 和 `print()` 那样将一切都转换为字符串。字符串和数字会直接打印出来。数组显示为带有键和值的括号列表，前面有 `Array` 字样：

```php
$a = array('name' => 'Fred', 'age' => 35, 'wife' => 'Wilma');
print_r($a);
`Array`
`(`
 `[``name``]` `=>` `Fred`
 `[``age``]` `=>` `35`
 `[``wife``]` `=>` `Wilma``)`
```

在数组上使用 `print_r()` 会将内部迭代器移动到数组中最后一个元素的位置。参见第五章了解更多有关迭代器和数组的信息。

当你对对象使用 `print_r()` 时，你会看到单词 `Object`，然后显示作为数组的初始化属性：

```php
class P {
 var $name = 'nat';
 // ... }

$p = new P;
print_r($p);
`Object`
`(`
 `[``name``]` `=>` `nat``)`
```

布尔值和 `NULL` 对于 `print_r()` 来说没有实际意义：

```php
print_r(true); // prints "1"; `1`
print_r(false); // prints ""; 
print_r(null); // prints "";
```

因此，对于调试，推荐使用 `var_dump()` 而不是 `print_r()`。`var_dump()` 函数可以以人类可读的格式显示 PHP 中的任何值：

```php
var_dump(true);
var_dump(false);
var_dump(null);
var_dump(array('name' => "Fred", 'age' => 35));
class P {
 var $name = 'Nat';
 // ... }
$p = new P;
var_dump($p);
`bool``(``true``)`
`bool``(``false``)`
`bool``(``null``)`
`array``(``2``)` `{`
 `[``"``name``"``]``=>`
 `string``(``4``)` `"``Fred``"`
 `[``"``age``"``]``=>`
 `int``(``35``)`
`}`
`object``(``p``)(``1``)` `{`
 `[``"``name``"``]``=>`
 `string``(``3``)` `"``Nat``"`
`}`
```

谨防在递归结构（例如 `$GLOBALS`，它有一个指向自身的 `GLOBALS` 条目）上使用 `print_r()` 或 `var_dump()`。`print_r()` 函数会无限循环，而 `var_dump()` 则在访问同一元素三次后截断。

# 访问单个字符

`strlen()` 函数返回字符串中的字符数：

```php
$string = 'Hello, world';
$length = strlen($string); // $length is 12
```

你可以使用字符串偏移语法来访问字符串的各个字符：

```php
$string = 'Hello';
for ($i=0; $i < strlen($string); $i++) {
 printf("The %dth character is %s\n", $i, $string{$i});
}
`The` `0``th` `character` `is` `H`
`The` `1``th` `character` `is` `e`
`The` `2``th` `character` `is` `l`
`The` `3``th` `character` `is` `l`
`The` `4``th` `character` `is` `o`
```

# 清理字符串

经常，我们从文件或用户那里获得的字符串在使用前需要清理。原始数据常见的问题是存在多余的空白和不正确的大写（大写与小写之间的区分）。

## 移除空白

您可以使用`trim()`、`ltrim()`和`rtrim()`函数删除前导或尾随的空白字符：

```php
$trimmed = trim(*`string`* [, *`charlist`* ]);
$trimmed = ltrim(*`string`* [, *`charlist`* ]);
$trimmed = rtrim(*`string`* [, *`charlist`* ]);
```

`trim()`返回从*string*中删除开头和结尾的空白的副本。 `ltrim()`（*l*代表*左*）执行相同的操作，但仅删除字符串开头的空白。 `rtrim()`（*r*代表*右*）仅从字符串结尾删除空白。 可选的*charlist*参数是一个字符串，用于指定要删除的所有字符。 默认要删除的字符在表 4-3 中给出。

表 4-3\. 由 trim()、ltrim()和 rtrim()移除的默认字符

| 字符 | ASCII 值 | 含义 |
| --- | --- | --- |
| `" "` | 0x20 | 空格 |
| `"\t"` | 0x09 | 制表符 |
| `"\n"` | 0x0A | 换行符（换行） |
| `"\r"` | 0x0D | 回车符 |
| `"\0"` | 0x00 | NUL 字节 |
| `"\x0B"` | 0x0B | 垂直制表符 |

例如：

```php
$title = " Programming PHP \n";
$str1 = ltrim($title); // $str1 is "Programming PHP \n"
$str2 = rtrim($title); // $str2 is " Programming PHP"
$str3 = trim($title); // $str3 is "Programming PHP"
```

给定一个以制表符分隔的数据行，请使用*charlist*参数删除前导或尾随的空白而不删除制表符：

```php
$record = " Fred\tFlintstone\t35\tWilma\t \n";
$record = trim($record, " \r\n\0\x0B");
// $record is "Fred\tFlintstone\t35\tWilma"
```

## 更改大小写

PHP 有几个函数可以改变字符串的大小写：`strtolower()`和`strtoupper()`作用于整个字符串，`ucfirst()`仅作用于字符串的第一个字符，`ucwords()`作用于字符串中每个单词的第一个字符。每个函数接受一个要操作的字符串作为参数，并返回相应修改后的副本。例如：

```php
$string1 = "FRED flintstone";
$string2 = "barney rubble";
print(strtolower($string1));
print(strtoupper($string1));
print(ucfirst($string2));
print(ucwords($string2));
`fred` `flintstone`
`FRED` `FLINTSTONE`
`Barney` `rubble`
`Barney` `Rubble`
```

如果您有一个混合大小写的字符串，并且想要将其转换为“标题案例”，其中每个单词的第一个字母大写，其余字母小写（并且您不确定字符串最初的大小写），请使用`strtolower()`和`ucwords()`的组合：

```php
print(ucwords(strtolower($string1)));
`Fred` `Flintstone`
```

# 编码与转义

因为 PHP 程序通常与 HTML 页面、网址（URL）和数据库交互，所以有一些函数可以帮助您处理这些类型的数据。HTML、网址和数据库命令都是字符串，但它们每个都需要以不同的方式进行不同的字符转义。例如，网址中的空格必须写成`%20`，而 HTML 文档中的文字小于号(`<`)必须写成`&lt;`。PHP 有许多内置函数用于在这些编码之间进行转换。

## HTML

HTML 中的特殊字符由*实体*表示，如`&amp;`（`&`）和`&lt;`（`<`）。有两个 PHP 函数将字符串中的特殊字符转换为它们的实体：一个用于移除 HTML 标签，另一个用于仅提取元标签。

### 对所有特殊字符进行实体引用

`htmlentities()`函数将所有具有 HTML 实体等效项的字符转换为这些等效项（空格字符除外）。这包括小于号(`<`)、大于号(`>`)、和带重音的字符。

例如：

```php
$string = htmlentities("Einstürzende Neubauten");
echo $string;
`Einstürzende` `Neubauten`
```

实体转义版本，`&uuml;`（查看源代码可见），在渲染的网页中正确显示为ü。正如你所见，空格没有被转换成`&nbsp;`。

`htmlentities()`函数实际上可以接受最多三个参数：

```php
$output = htmlentities(*`input`*, *`flags`*, *`encoding`*);
```

*encoding*参数，如果给出，标识字符集。默认为“UTF-8”。*flags*参数控制是否将单引号和双引号转换为它们的实体形式。`ENT_COMPAT`（默认）仅转换双引号，`ENT_QUOTES`转换两种引号类型，`ENT_NOQUOTES`既不转换单引号也不转换双引号。没有选项只转换单引号。例如：

```php
$input = <<< End
"Stop pulling my hair!" Jane's eyes flashed.<p>
End;

$double = htmlentities($input);
// &quot;Stop pulling my hair!&quot; Jane's eyes flashed.&lt;p&gt;

$both = htmlentities($input, ENT_QUOTES);
// &quot;Stop pulling my hair!&quot; Jane&#039;s eyes flashed.&lt;p&gt;

$neither = htmlentities($input, ENT_NOQUOTES);
// "Stop pulling my hair!" Jane's eyes flashed.&lt;p&gt;
```

### 仅对 HTML 语法字符进行实体引用

`htmlspecialchars()`函数将尽可能少地转换实体以生成有效的 HTML。以下实体被转换：

+   Ampersands (`&`) are converted to `&amp;`

+   双引号(`"`)被转换为`&quot;`

+   单引号(`'`)如果`ENT_QUOTES`打开，则被转换为`&#039;`（如`htmlentities()`所述）

+   小于号(`<`)被转换为`&lt;`

+   大于号(`>`)被转换为`&gt;`

如果您有一个显示用户在表单中输入的数据的应用程序，则需要在显示或保存数据之前将该数据通过`htmlspecialchars()`运行。如果不这样做，用户输入像`"angle < 30"`或`"sturm & drang"`这样的字符串，浏览器将认为特殊字符是 HTML，导致页面混乱。

像`htmlentities()`一样，`htmlspecialchars()`最多可以接受三个参数：

```php
$output = htmlspecialchars(*`input`*, [*`flags`*, [*`encoding`*]]);
```

*flags*和*encoding*参数具有与`htmlentities()`相同的含义。

没有专门用于将实体转换回原始文本的函数，因为这很少需要。不过，有一种相对简单的方法可以做到这一点。使用`get_html_translation_table()`函数获取由这些函数之一在给定引用样式中使用的翻译表。例如，要获取`htmlentities()`使用的翻译表，可以这样做：

```php
$table = get_html_translation_table(HTML_ENTITIES);
```

要获取`htmlspecialchars()`中`ENT_NOQUOTES`模式下的表格，请使用：

```php
$table = get_html_translation_table(HTML_SPECIALCHARS, ENT_NOQUOTES);
```

一个不错的技巧是使用这个翻译表，用`array_flip()`来翻转它，并将其传递给`strtr()`以将其应用到字符串中，从而有效地执行`htmlentities()`的反向操作：

```php
$str = htmlentities("Einstürzende Neubauten"); // now it is encoded 
$table = get_html_translation_table(HTML_ENTITIES);
$revTrans = array_flip($table);

echo strtr($str, $revTrans); // back to normal `Einstürzende` `Neubauten`
```

当然，您也可以获取翻译表，添加您想要的任何其他翻译，并使用`strtr()`。例如，如果您想要`htmlentities()`还将每个空格编码为`&nbsp;`，可以这样做：

```php
$table = get_html_translation_table(HTML_ENTITIES);
$table[' '] = '&nbsp;';
$encoded = strtr($original, $table);
```

### 移除 HTML 标签

`strip_tags()`函数从字符串中删除 HTML 标签：

```php
$input = '<p>Howdy, &quot;Cowboy&quot;</p>';
$output = strip_tags($input);
// $output is 'Howdy, &quot;Cowboy&quot;'
```

该函数可能接受第二个参数，该参数指定要在字符串中保留的标记字符串。只列出标记的开放形式。列在第二个参数中的标记的闭合形式也会被保留：

```php
$input = 'The <b>bold</b> tags will <i>stay</i><p>';
$output = strip_tags($input, '<b>');
// $output is 'The <b>bold</b> tags will stay'
```

保留标签中的属性不会被`strip_tags()`改变。因为诸如`style`和`onmouseover`之类的属性可能会影响网页的外观和行为，所以用`strip_tags()`保留一些标签不一定会消除滥用的可能性。

### 提取元标签

`get_meta_tags()`函数返回指定为本地文件名或 URL 的 HTML 页面的 meta 标签数组。meta 标签的名称（`keywords`、`author`、`description`等）成为数组中的键，而 meta 标签的内容成为相应的值：

```php
$metaTags = get_meta_tags('http://www.example.com/');
echo "Web page made by {$metaTags['author']}";
`Web` `page` `made` `by` `John` `Doe`
```

函数的一般形式是：

```php
$array = get_meta_tags(*`filename`* [, *`use_include_path`*]);
```

传递`true`值给*use_include_path*，让 PHP 尝试使用标准包含路径打开文件。

## URL

PHP 提供了用于转换到和从 URL 编码的函数，这使您能够构建和解码 URL。实际上有两种类型的 URL 编码，它们在如何处理空格方面有所不同。第一种（由 RFC 3986 指定）将空格视为 URL 中的另一个非法字符，并将其编码为`%20`。第二种（实现`application/x-www-form-urlencoded`系统）将空格编码为`+`，用于构建查询字符串。

注意，您不应在完整 URL 上使用这些函数，例如[*http://www.example.com/hello*](http://www.example.com/hello)，因为它们将转义冒号和斜杠以生成：

```php
http%3A%2F%2Fwww.example.com%2Fhello
```

仅对部分 URL 进行编码（*http://www.example.com/*之后的部分），稍后添加协议和域名。

### RFC 3986 编码和解码

要根据 URL 约定对字符串进行编码，请使用`rawurlencode()`：

```php
$output = rawurlencode(*`input`*);
```

此函数接受一个字符串，并返回一个使用`%dd`约定编码的副本，其中包含非法的 URL 字符。

如果您正在为页面中的链接动态生成超文本引用，需要使用`rawurlencode()`进行转换：

```php
$name = "Programming PHP";
$output = rawurlencode($name);
echo "http://localhost/{$output}";
`http``://``localhost``/``Programming``%``20``PHP`
```

`rawurldecode()`函数解码 URL 编码的字符串：

```php
$encoded = 'Programming%20PHP';
echo rawurldecode($encoded);
`Programming` `PHP`
```

### 查询字符串编码

`urlencode()`和`urldecode()`函数与其原始版本唯一的区别在于它们将空格编码为加号(`+`)而不是`%20`序列。这是构建查询字符串和 cookie 值的格式。这些函数在 HTML 中提供类似表单的 URL 时非常有用。PHP 会自动解码查询字符串和 cookie 值，因此您不需要使用这些函数来处理这些值。这些函数在生成查询字符串时非常有用：

```php
$baseUrl = 'http://www.google.com/q=';
$query = 'PHP sessions -cookies';
$url = $baseUrl . urlencode($query);
echo $url;

`http``://``www``.``google``.``com``/``q``=``PHP``+``sessions``+-``cookies`
```

## SQL

大多数数据库系统要求 SQL 查询中的字符串字面值进行转义。SQL 的编码方案相当简单——单引号、双引号、NUL 字节和反斜杠需要在前面加上反斜杠。`addslashes()`函数添加这些反斜杠，而`stripslashes()`函数则将其移除：

```php
$string = <<< EOF
"It's never going to work," she cried,
as she hit the backslash (\) key.
EOF;
$string = addslashes($string);
echo $string;
echo stripslashes($string);
`\``"``It``\``'s never going to work,``\"` `she cried,`
`as she hit the backslash (``\\``) key.`
`"``It``'``s` `never` `going` `to` `work``,``"` `she cried,`
`as she hit the backslash (``\``) key.`
```

## C 字符串编码

`addcslashes()` 函数通过在特定字符前加上反斜杠来转义任意字符。除了 [Table 4-4](https://wiki.example.org/single_character_escapes_recognized_by) 中的字符外，ASCII 值小于 32 或大于 126 的字符会用它们的八进制值进行编码（例如 `"\002"`）。`addcslashes()` 和 `stripcslashes()` 函数用于非标准数据库系统，这些系统有自己的想法，即哪些字符需要转义。

Table 4-4\. `addcslashes()` 和 `stripcslashes()` 识别的单字符转义

| ASCII 值 | 编码 |
| --- | --- |
| 7 | `\a` |
| 8 | `\b` |
| 9 | `\t` |
| 10 | `\n` |
| 11 | `\v` |
| 12 | `\f` |
| 13 | `\r` |

使用两个参数调用 `addcslashes()` —— 要编码的字符串和要转义的字符：

```php
$escaped = addcslashes(*`string`*, *`charset`*);
```

使用 `".."` 结构指定要转义的字符范围：

```php
echo addcslashes("hello\tworld\n", "\x00..\x1fz..\xff");
`hello\tworld\n`
```

警告在字符集中指定 `'0'`, `'a'`, `'b'`, `'f'`, `'n'`, `'r'`, `'t'`, 或 `'v'`，因为它们会转换为 `'\0'`, `'\a'` 等。这些转义符由 C 和 PHP 识别，可能会引起混淆。

`stripcslashes()` 接受一个字符串，并返回一个带有扩展转义的副本：

```php
$string = stripcslashes(*`escaped`*);
```

例如：

```php
$string = stripcslashes('hello\tworld\n');
// $string is "hello\tworld\n"
```

# 比较字符串

PHP 有两个操作符和六个函数用于比较字符串：

## 精确比较

使用 `==` 和 `===` 操作符可以比较两个字符串是否相等。这两个操作符在处理非字符串操作数时有所不同。`==` 操作符会将字符串操作数转换为数字，因此会报告 `3` 和 `"3"` 是相等的。根据将字符串转换为数字的规则，它也会报告 `3` 和 `"3b"` 是相等的，因为只使用字符串中非数字字符之前的部分进行转换。而 `===` 操作符不进行类型转换，如果参数的数据类型不同，则返回 `false`：

```php
$o1 = 3;
$o2 = "3";

if ($o1 == $o2) {
 echo("== returns true<br>");
}
if ($o1 === $o2) {
 echo("=== returns true<br>");
}
`==` `returns` `true`
```

比较操作符 (`<`, `<=`, `>`, `>=`) 也适用于字符串：

```php
$him = "Fred";
$her = "Wilma";

if ($him < $her) {
 print "{$him} comes before {$her} in the alphabet.\n";
}
`Fred` `comes` `before` `Wilma` `in` `the` `alphabet`
```

然而，当比较字符串和数字时，比较操作符会给出意外的结果：

```php
$string = "PHP Rocks";
$number = 5;

if ($string < $number) {
 echo("{$string} < {$number}");
}
`PHP` `Rocks` `<` `5`
```

当比较操作符的一个参数是数字时，另一个参数会被转换为数字。这意味着 `"PHP Rocks"` 被转换为数字，得到 `0`（因为字符串不以数字开头）。因为 0 小于 5，PHP 输出 `"PHP Rocks < 5"`。

若要明确将两个字符串作为字符串比较，必要时将数字转换为字符串，请使用 `strcmp()` 函数：

```php
$relationship = strcmp(*`string_1`*, *`string_2`*);
```

该函数返回一个小于 0 的数，如果 *string_1* 在 *string_2* 前排序；如果 *string_2* 在 *string_1* 前排序，则返回大于 0 的数；如果它们相同，则返回 0：

```php
$n = strcmp("PHP Rocks", 5);
echo($n);
`1`
```

`strcmp()` 的变体是 `strcasecmp()`，它在比较之前将字符串转换为小写。它的参数和返回值与 `strcmp()` 相同：

```php
$n = strcasecmp("Fred", "frED"); // $n is 0
```

另一种字符串比较的变体是仅比较字符串的前几个字符。`strncmp()` 和 `strncasecmp()` 函数需要额外的参数，即用于比较的初始字符数：

```php
$relationship = strncmp(*`string_1`*, *`string_2`*, *`len`*);
$relationship = strncasecmp(*`string_1`*, *`string_2`*, *`len`*);
```

这些函数的最后一种变体是 *自然顺序* 比较，使用 `strnatcmp()` 和 `strnatcasecmp()`，它们与 `strcmp()` 使用相同的参数并返回相同类型的值。自然顺序比较识别被比较字符串中的数字部分，并将字符串部分与数字部分分别排序。

表 4-5 展示了自然顺序和 ASCII 顺序中的字符串。

表 4-5\. 自然顺序与 ASCII 顺序

| 自然顺序 | ASCII 顺序 |
| --- | --- |
| `pic1.jpg` | `pic1.jpg` |
| `pic5.jpg` | `pic10.jpg` |
| `pic10.jpg` | `pic5.jpg` |
| `pic50.jpg` | `pic50.jpg` |

## 近似相等

PHP 提供了几个函数，允许您测试两个字符串是否近似相等 — `soundex()`、`metaphone()`、`similar_text()` 和 `levenshtein()`：

```php
$soundexCode = soundex(*`$string`*);
$metaphoneCode = metaphone(*`$string`*);
$inCommon = similar_text(*`$string_1`*, *`$string_2`* [, *`$percentage`* ]);
$similarity = levenshtein(*`$string_1`*, *`$string_2`*);
$similarity = levenshtein(*`$string_1`*, *`$string_2`* [, *`$cost_ins`*, *`$cost_rep`*, 
*`$cost_del`* ]);
```

Soundex 和 Metaphone 算法各自生成一个字符串，大致表示一个英语单词的发音。要查看这些算法是否将两个字符串近似视为相等，请比较它们的发音。只能将 Soundex 值与 Soundex 值比较，将 Metaphone 值与 Metaphone 值比较。如下示例所示，Metaphone 算法通常更准确：

```php
$known = "Fred";
$query = "Phred";

if (soundex($known) == soundex($query)) {
 print "soundex: {$known} sounds like {$query}<br>";
}
else {
 print "soundex: {$known} doesn't sound like {$query}<br>";
}

if (metaphone($known) == metaphone($query)) {
 print "metaphone: {$known} sounds like {$query}<br>";
}
else {
 print "metaphone: {$known} doesn't sound like {$query}<br>";
}
`soundex``:` `Fred` `doesn``'``t` `sound` `like` `Phred`
`metaphone``:` `Fred` `sounds` `like` `Phred`
```

`similar_text()` 函数返回它的两个字符串参数共有的字符数。如果存在第三个参数，则将共性以百分比存储在其中：

```php
$string1 = "Rasmus Lerdorf";
$string2 = "Razmus Lehrdorf";
$common = similar_text($string1, $string2, $percent);
printf("They have %d chars in common (%.2f%%).", $common, $percent);
`They` `have` `13` `chars` `in` `common` `(``89.66``%``)``.`
```

Levenshtein 算法根据需要添加、替换或删除的字符数计算两个字符串的相似性。例如，`"cat"` 和 `"cot"` 的 Levenshtein 距离为 1，因为您只需更改一个字符（将 `"a"` 改为 `"o"`）使它们相同：

```php
$similarity = levenshtein("cat", "cot"); // $similarity is 1
```

此相似性度量通常比 `similar_text()` 函数使用的更快速。可选地，您可以将三个值传递给 `leven``shtein()` 函数，以分别加权插入、删除和替换 —— 例如，比较一个单词与一个缩写。

此示例在比较字符串与其可能的缩写时过度加权插入，因为缩写不应插入字符：

```php
echo levenshtein('would not', 'wouldn\'t', 500, 1, 1);
```

# 操作和搜索字符串

PHP 提供了许多用于处理字符串的函数。对于搜索和修改字符串最常用的函数是那些使用正则表达式来描述被查询字符串的函数。本节描述的函数不使用正则表达式 —— 它们比正则表达式更快，但仅适用于寻找固定字符串（例如，如果您要查找 `"12/11/01"` 而不是 "由斜杠分隔的任意数字"）。

## 子字符串

如果您知道感兴趣的数据位于较大字符串的哪个位置，可以使用 `substr()` 函数将其复制出来：

```php
$piece = substr(*`string`*, *`start`* [, *`length`* ]);
```

*start* 参数是在 *string* 中开始复制的位置，`0` 表示字符串的开头。*length* 参数是要复制的字符数（默认是复制直到字符串的末尾）。例如：

```php
$name = "Fred Flintstone";
$fluff = substr($name, 6, 4); // $fluff is "lint"
$sound = substr($name, 11); // $sound is "tone"
```

要了解较大字符串中较小字符串出现的次数，请使用 `substr_count()`：

```php
$number = substr_count(*`big_string`*, *`small_string`*);
```

例如：

```php
$sketch = <<< EndOfSketch
Well, there's egg and bacon; egg sausage and bacon; egg and spam;
egg bacon and spam; egg bacon sausage and spam; spam bacon sausage
and spam; spam egg spam spam bacon and spam; spam sausage spam spam
bacon spam tomato and spam;
EndOfSketch;
$count = substr_count($sketch, "spam");
print("The word spam occurs {$count} times.");
`The` `word` `spam` `occurs` `14` `times``.`
```

函数 `substr_replace()` 允许进行多种类型的字符串修改：

```php
$string = substr_replace(*`original`*, *`new`*, *`start`* [, *`length`* ]);
```

函数替换 *original* 指定部分，该部分由 *start*（`0` 表示字符串的开头）和 *length* 值与字符串 *new* 替换。如果未给出第四个参数，`substr_replace()` 将删除从 *start* 到字符串末尾的文本。

例如：

```php
$greeting = "good morning citizen";
$farewell = substr_replace($greeting, "bye", 5, 7);
// $farewell is "good bye citizen"
```

使用 *length* 为 `0` 进行插入而不删除：

```php
$farewell = substr_replace($farewell, "kind ", 9, 0);
// $farewell is "good bye kind citizen"
```

使用替换 `""` 进行删除而不插入：

```php
$farewell = substr_replace($farewell, "", 8);
// $farewell is "good bye"
```

下面是如何在字符串开头插入的方法：

```php
$farewell = substr_replace($farewell, "now it's time to say ", 0, 0);
// $farewell is "now it's time to say good bye"'
```

*start* 的负值表示从字符串末尾开始替换的字符数：

```php
$farewell = substr_replace($farewell, "riddance", −3);
// $farewell is "now it's time to say good riddance"
```

负数 *length* 表示从字符串末尾停止删除的字符数：

```php
$farewell = substr_replace($farewell, "", −8, −5);
// $farewell is "now it's time to say good dance"
```

## 杂项字符串函数

函数 `strrev()` 接受一个字符串并返回其反转副本：

```php
$string = strrev(*`string`*);
```

例如：

```php
echo strrev("There is no cabal");
`labac` `on` `si` `erehT`
```

函数 `str_repeat()` 接受一个字符串和一个计数，并返回一个由参数 *string* 重复 *count* 次的新字符串：

```php
$repeated = str_repeat(*`string`*, *`count`*);
```

例如，构建一个简陋的波浪形水平线：

```php
echo str_repeat('_.-.', 40);
```

函数 `str_pad()` 使用一个字符串填充另一个字符串。可选地，您可以指定用于填充的字符串，以及是在左侧、右侧还是两侧填充：

```php
$padded = str_pad(*`to_pad`*, *`length`* [, *`with`* [, *`pad_type`* ]]);
```

默认在右侧填充空格：

```php
$string = str_pad('Fred Flintstone', 30);
echo "{$string}:35:Wilma";
`Fred` `Flintstone` `:``35``:``Wilma`
```

可选的第三个参数是要填充的字符串：

```php
$string = str_pad('Fred Flintstone', 30, '. ');
echo "{$string}35";
`Fred` `Flintstone``.` `.` `.` `.` `.` `.` `.` `.``35`
```

可选的第四个参数可以是 `STR_PAD_RIGHT`（默认值）、`STR_PAD_LEFT` 或 `STR_PAD_BOTH`（居中）。例如：

```php
echo '[' . str_pad('Fred Flintstone', 30, ' ', STR_PAD_LEFT) . "]\n";
echo '[' . str_pad('Fred Flintstone', 30, ' ', STR_PAD_BOTH) . "]\n";
`[` `Fred` `Flintstone``]`
`[` `Fred` `Flintstone` `]`
```

## 分解字符串

PHP 提供了几个函数，可以让你将一个字符串拆分成较小的组件。按复杂性递增的顺序，它们是 `explode()`、`strtok()` 和 `sscanf()`。

### 分解和爆炸

数据通常以字符串形式到达，必须将其拆分为值数组。例如，您可能想要从字符串 `"Fred,25,Wilma"` 中拆分逗号分隔的字段。在这些情况下，使用 `explode()` 函数：

```php
$array = explode(*`separator`*, *`string`* [, *`limit`*]);
```

第一个参数 *separator* 是包含字段分隔符的字符串。第二个参数 *string* 是要拆分的字符串。可选的第三个参数 *limit* 是在数组中返回的最大值数。如果达到限制，数组的最后一个元素包含字符串的其余部分：

```php
$input = 'Fred,25,Wilma';
$fields = explode(',', $input);
// $fields is array('Fred', '25', 'Wilma')
$fields = explode(',', $input, 2);
// $fields is array('Fred', '25,Wilma')
```

函数 `implode()` 做与 `explode()` 正好相反的操作——它从较小字符串的数组创建一个大字符串：

```php
$string = implode(*`separator`*, *`array`*);
```

第一个参数 *separator* 是要放在第二个参数 *array* 元素之间的字符串。要重建简单的逗号分隔值字符串，只需说：

```php
$fields = array('Fred', '25', 'Wilma');
$string = implode(',', $fields); // $string is 'Fred,25,Wilma'
```

函数 `join()` 是 `implode()` 的别名。

### 标记化

函数 `strtok()` 允许您遍历字符串，每次获取一个新的块（标记）。第一次调用它时，您需要传递两个参数：要遍历的字符串和标记分隔符。例如：

```php
$firstChunk = strtok(*`string`*, *`separator`*);
```

要检索其余的标记，请反复调用 `strtok()`，只带分隔符：

```php
$nextChunk = strtok(*`separator`*);
```

例如，考虑以下调用：

```php
$string = "Fred,Flintstone,35,Wilma";
$token = strtok($string, ",");

while ($token !== false) {
 echo("{$token}<br />");
 $token = strtok(",");
}
`Fred`
`Flintstone`
`35`
`Wilma`
```

当没有更多标记可返回时，函数 `strtok()` 返回 `false`。

使用两个参数调用 `strtok()` 重新初始化迭代器。这会从字符串的开头重新启动标记化器。

### sscanf()

函数 `sscanf()` 根据类似 `printf()` 的模板分解字符串：

```php
$array = sscanf(*`string`*, *`template`*);
$count = sscanf(*`string`*, *`template`*, *`var1`*, ... );
```

如果没有使用可选变量，`sscanf()` 返回字段数组：

```php
$string = "Fred\tFlintstone (35)";
$a = sscanf($string, "%s\t%s (%d)");
print_r($a);
`Array`
`(`
 `[``0``]` `=>` `Fred`
 `[``1``]` `=>` `Flintstone`
 `[``2``]` `=>` `35``)`
```

通过引用传递变量，使字段存储在这些变量中。返回分配的字段数：

```php
$string = "Fred\tFlintstone (35)";
$n = sscanf($string, "%s\t%s (%d)", $first, $last, $age);
echo "Matched {$n} fields: {$first} {$last} is {$age} years old";
`Matched` `3` `fields``:` `Fred` `Flintstone` `is` `35` `years` `old`
```

## 字符串搜索函数

几个函数在一个较大的字符串中查找字符串或字符。它们分为三类：`strpos()` 和 `strrpos()`，它们返回位置；`strstr()`、`strchr()` 和相关函数，它们返回找到的字符串；以及 `strspn()` 和 `strcspn()`，它们返回字符串开头匹配掩码的数量。

在所有情况下，如果您指定一个数字作为要搜索的“字符串”，PHP 将该数字视为要搜索的字符的序数值。因此，这些函数调用是相同的，因为 44 是逗号的 ASCII 值：

```php
$pos = strpos($large, ","); // find first comma
$pos = strpos($large, 44); // also find first comma
```

所有的字符串搜索函数在无法找到指定的子字符串时都返回 `false`。如果子字符串出现在字符串的开头，这些函数返回 `0`。因为 `false` 转换为数字 `0`，在测试失败时始终使用 `===` 比较返回值：

```php
if ($pos === false) {
 // wasn't found
}
else {
 // was found, $pos is offset into string
}
```

### 返回位置的搜索

函数 `strpos()` 在较大字符串中查找小字符串的第一次出现：

```php
$position = strpos(*`large_string`*, *`small_string`*);
```

如果未找到小字符串，`strpos()` 返回 `false`。

函数 `strrpos()` 在字符串中查找字符的最后一个出现。它接受与 `strpos()` 相同的参数并返回相同类型的值。

例如：

```php
$record = "Fred,Flintstone,35,Wilma";
$pos = strrpos($record, ","); // find last comma echo("The last comma in the record is at position {$pos}");
`The` `last` `comma` `in` `the` `record` `is` `at` `position` `18`
```

### 返回其余字符串的搜索

函数 `strstr()` 查找较大字符串中第一次出现的小字符串，并从该小字符串开始返回。例如：

```php
$record = "Fred,Flintstone,35,Wilma";
$rest = strstr($record, ","); // $rest is ",Flintstone,35,Wilma"
```

`strstr()` 的变体有：

`stristr()`

不区分大小写的 `strstr()`

`strchr()`

`strstr()` 的别名

`strrchr()`

查找字符串中最后一个字符的出现

与 `strrpos()` 类似，`strrchr()` 向后搜索字符串，但仅针对单个字符，而不是整个字符串。

### 使用掩码进行搜索

如果您认为 `strrchr()` 很奇怪，那么您还没见过。`strspn()` 和 `strcspn()` 函数告诉您字符串开头有多少字符由特定字符组成：

```php
$length = strspn(*`string`*, *`charset`*);
```

例如，此函数测试字符串是否包含八进制数：

```php
function isOctal($str)
{
 return strspn($str, '01234567') == strlen($str);
}
```

`strcspn()`中的*c*代表*complement*，表示字符串开头不包含字符集中字符的长度。当有趣字符的数量大于无趣字符的数量时使用它。例如，此函数测试字符串是否包含任何 NUL 字节、制表符或回车符：

```php
function hasBadChars($str)
{
 return strcspn($str, "\n\t\0") != strlen($str);
}
```

### 解析 URL

`parse_url()`函数返回 URL 的各个组成部分的数组：

```php
$array = parse_url(*`url`*);
```

例如：

```php
$bits = parse_url("http://me:secret@example.com/cgi-bin/board?user=fred");
print_r($bits);

`Array`
`(`
 `[``scheme``]` `=>` `http`
 `[``host``]` `=>` `example``.``com`
 `[``user``]` `=>` `me`
 `[``pass``]` `=>` `secret`
 `[``path``]` `=>` `/``cgi``-``bin``/``board`
 `[``query``]` `=>` `user``=``fred``)`
```

哈希的可能键包括`scheme`、`host`、`port`、`user`、`pass`、`path`、`query`和`fragment`。

# 正则表达式

如果您需要比前面的方法提供更复杂的搜索功能，可以使用正则表达式——它是表示*模式*的字符串。正则表达式函数会将该模式与另一个字符串进行比较，并查看字符串中是否有任何匹配模式的部分。一些函数告诉您是否有匹配，而其他函数则对字符串进行更改。

正则表达式有三个用途：匹配，也可以用于从字符串中提取信息；用新文本替换匹配文本；将字符串分割成较小块的数组。PHP 有适用于所有这些情况的函数。例如，`preg_match()`执行正则表达式匹配。

长期以来，Perl 一直被认为是功能强大的正则表达式的基准。PHP 使用称为*pcre*的 C 库来几乎完全支持 Perl 的正则表达式功能集合。Perl 正则表达式作用于任意二进制数据，因此您可以安全地使用包含 NUL 字节（`\x00`）的模式或字符串进行匹配。

## 基础知识

正则表达式中的大多数字符都是字面字符，意味着它们仅匹配它们自己。例如，如果您在字符串`"Dave was a cowhand"`中搜索正则表达式`"/cow/"`，您会得到一个匹配，因为该字符串中包含`"cow"`。

一些字符在正则表达式中具有特殊含义。例如，正则表达式开头的插入符号（`^`）表示它必须匹配字符串的开头（或者更准确地说，将正则表达式锚定到字符串的开头）：

```php
preg_match("/^cow/", "Dave was a cowhand"); // returns false
preg_match("/^cow/", "cowabunga!"); // returns true
```

类似地，正则表达式末尾的美元符号（`$`）表示它必须匹配字符串的末尾（即将正则表达式锚定到字符串的末尾）：

```php
preg_match("/cow$/", "Dave was a cowhand"); // returns false
preg_match("/cow$/", "Don't have a cow"); // returns true
```

正则表达式中的句点（`.`）匹配任意单个字符：

```php
preg_match("/c.t/", "cat"); // returns true
preg_match("/c.t/", "cut"); // returns true
preg_match("/c.t/", "c t"); // returns true
preg_match("/c.t/", "bat"); // returns false
preg_match("/c.t/", "ct"); // returns false
```

如果您想匹配这些特殊字符中的一个（称为*元字符*），您必须使用反斜杠进行转义：

```php
preg_match("/\$5.00/", "Your bill is $5.00 exactly"); // returns true
preg_match("/$5.00/", "Your bill is $5.00 exactly"); // returns false
```

默认情况下，正则表达式区分大小写，因此正则表达式`"/cow/"`不匹配字符串`"COW"`。如果您想执行不区分大小写的匹配，可以指定一个标志（稍后在本章中将会看到）。

到目前为止，我们还没有做任何与我们已经看过的字符串函数（如 `strstr()`）无法完成的事情。正则表达式的真正威力来自于它们能够指定抽象模式，这些模式可以匹配许多不同的字符序列。您可以在正则表达式中指定三种基本类型的抽象模式：

+   可以出现在字符串中的可接受字符集（例如，字母字符、数字字符、特定标点字符）

+   一组字符串的替代项（例如 `"com"`、`"edu"`、`"net"` 或 `"org"`）

+   字符串中的重复序列（例如，至少一个但不超过五个数字字符）

这三种类型的模式可以以无数种方式结合在一起，以创建能够匹配有效电话号码和 URL 等内容的正则表达式。

## 字符类

要在模式中指定一组可接受的字符集，您可以自己构建一个字符类或使用预定义的字符类。您可以通过将可接受的字符括在方括号中来构建自己的字符类：

```php
preg_match("/c[aeiou]t/", "I cut my hand"); // returns true
preg_match("/c[aeiou]t/", "This crusty cat"); // returns true
preg_match("/c[aeiou]t/", "What cart?"); // returns false
preg_match("/c[aeiou]t/", "14ct gold"); // returns false
```

正则表达式引擎找到一个 `"c"`，然后检查下一个字符是否是 `"a"`、`"e"`、`"i"`、`"o"` 或 `"u"` 中的一个元音字母。如果不是元音字母，则匹配失败，引擎会回到寻找另一个 `"c"`。如果找到一个元音字母，则引擎检查下一个字符是否是 `"t"`。如果是，则引擎处于匹配的末尾并返回 `true`。如果下一个字符不是 `"t"`，则引擎会回到寻找另一个 `"c"`。

您可以在开头使用插入符 (`^`) 来否定一个字符类：

```php
preg_match("/c[^aeiou]t/", "I cut my hand"); // returns false
preg_match("/c[^aeiou]t/", "Reboot chthon"); // returns true
preg_match("/c[^aeiou]t/", "14ct gold"); // returns false
```

在这种情况下，正则表达式引擎正在寻找一个以 `"c"` 开头的字符，后面跟着一个不是元音字母的字符，然后是一个 `"t"`。

您可以使用连字符 (`-`) 定义字符的范围。这简化了字符类，例如“所有字母”和“所有数字”：

```php
preg_match("/[0-9]%/", "we are 25% complete"); // returns true
preg_match("/[0123456789]%/", "we are 25% complete"); // returns true
preg_match("/[a-z]t/", "11th"); // returns false
preg_match("/[a-z]t/", "cat"); // returns true
preg_match("/[a-z]t/", "PIT"); // returns false
preg_match("/[a-zA-Z]!/", "11!"); // returns false
preg_match("/[a-zA-Z]!/", "stop!"); // returns true
```

当您指定字符类时，一些特殊字符失去其含义，而其他字符则具有新的含义。特别地，`$` 锚点和句点在字符类中失去其含义，而 `^` 字符如果是开方括号后的第一个字符，则不再是锚点，而是否定字符类。例如，`[^\]]` 匹配任何非闭合括号字符，而 `[$.^]` 匹配任何美元符号、句点或插入符。

各种正则表达式库定义了字符类的快捷方式，包括数字、字母字符和空白字符。

## 替代项

您可以使用竖线 (`|`) 字符来指定正则表达式中的替代项：

```php
preg_match("/cat|dog/", "the cat rubbed my legs"); // returns true
preg_match("/cat|dog/", "the dog rubbed my legs"); // returns true
preg_match("/cat|dog/", "the rabbit rubbed my legs"); // returns false
```

替代的优先级可能会让人感到意外：`"/^cat|dog$/"` 会从 `"^cat"` 和 `"dog$"` 中选择，意味着它匹配以 `"cat"` 开头或以 `"dog"` 结尾的行。如果您希望匹配仅包含 `"cat"` 或 `"dog"` 的行，则需要使用正则表达式 `"/^(cat|dog)$/"`。

您可以结合字符类和替代项，例如检查不以大写字母开头的字符串：

```php
preg_match("/^([a-z]|[0-9])/", "The quick brown fox"); // returns false
preg_match("/^([a-z]|[0-9])/", "jumped over"); // returns true
preg_match("/^([a-z]|[0-9])/", "10 lazy dogs"); // returns true
```

## 重复序列

要指定重复模式，需要使用*量词*。量词跟在要重复的模式之后，并指定重复该模式的次数。表格 4-6 显示了 PHP 正则表达式支持的量词。

表格 4-6\. 正则表达式量词

| 量词 | 含义 |
| --- | --- |
| `?` | 0 或 1 次 |
| `*` | 0 或更多次 |
| `+` | 1 或更多次 |
| `{` *n* `}` | 恰好 *n* 次 |
| `{` *n* `,` *m* `}` | 至少 *n* 次，不超过 *m* 次 |
| `{` *n* `,}` | 至少 *n* 次 |

要重复单个字符，只需将量词放在字符后面：

```php
preg_match("/ca+t/", "caaaaaaat"); // returns true
preg_match("/ca+t/", "ct"); // returns false
preg_match("/ca?t/", "caaaaaaat"); // returns false
preg_match("/ca*t/", "ct"); // returns true
```

使用量词和字符类，我们实际上可以做一些有用的事情，比如匹配有效的美国电话号码：

```php
preg_match("/[0-9]{3}-[0-9]{3}-[0-9]{4}/", "303-555-1212"); // returns true
preg_match("/[0-9]{3}-[0-9]{3}-[0-9]{4}/", "64-9-555-1234"); // returns false
```

## 子模式

你可以使用括号将正则表达式的各部分分组，以便将其作为称为*子模式*的单个单元处理：

```php
preg_match("/a (very )+big dog/", "it was a very very big dog"); // returns true
preg_match("/^(cat|dog)$/", "cat"); // returns true
preg_match("/^(cat|dog)$/", "dog"); // returns true
```

括号还会导致匹配子模式的子字符串被捕获。如果将数组作为 match 函数的第三个参数传递，数组将填充任何捕获的子字符串：

```php
preg_match("/([0-9]+)/", "You have 42 magic beans", $captured);
// returns true and populates $captured
```

数组的零号元素设置为整个匹配的字符串。第一个元素是匹配第一个子模式（如果有的话）的子字符串，第二个元素是匹配第二个子模式的子字符串，依此类推。

## 定界符

Perl 风格的正则表达式模拟了 Perl 模式的语法，这意味着每个模式必须用一对定界符括起来。传统上，正斜杠 (`/`) 字符被使用；例如，`/`*模式*`/`。然而，除反斜杠 (`\`) 字符之外的任何非字母数字字符都可以用于分隔 Perl 风格的模式。这对于匹配包含斜杠的字符串（例如文件名）非常有用。例如，以下两者是等价的：

```php
preg_match("/\/usr\/local\//", "/usr/local/bin/perl"); // returns true
preg_match("#/usr/local/#", "/usr/local/bin/perl"); // returns true
```

括号 (`()`)、大括号 (`{}`)、方括号 (`[]`) 和尖括号 (`<>`) 可以用作模式定界符：

```php
preg_match("{/usr/local/}", "/usr/local/bin/perl"); // returns true
```

章节 “尾随选项” 讨论了你可以在结束定界符后添加的单字符修饰符，以修改正则表达式引擎的行为。一个非常有用的选项是 `x`，它使正则表达式引擎在匹配之前从正则表达式中去除空白和 `#` 标记的注释。这两个模式是相同的，但一个更易于阅读：

```php
'/([[:alpha:]]+)\s+\1/'
'/( # start capture
[[:alpha:]]+ # a word
\s+ # whitespace
\1 # the same word again
 ) # end capture
/x'
```

## 匹配行为

句点 (`.`) 匹配除换行符 (`\n`) 外的任何字符。美元符号 (`$`) 匹配字符串的末尾，或者如果字符串以换行符结尾，则在该换行符之前：

```php
preg_match("/is (.*)$/", "the key is in my pants", $captured);
// $captured[1] is 'in my pants'
```

## 字符类

如 表格 4-7 所示，Perl 兼容的正则表达式定义了一些命名字符集，你可以在字符类中使用这些字符集。表格 4-7 中的扩展是针对英文的。实际字母根据地区而有所不同。

每个 `[:` *something* `:]` 类可以用于字符类中的字符位置。例如，要查找任何数字、大写字母或“at”符号 (`@`) 的字符，请使用以下正则表达式：

```php
[@[:digit:][:upper:]]
```

然而，你不能使用字符类作为范围的结束点：

```php
preg_match("/[A-[:lower:]]/", "string");// invalid regular expression
```

某些区域设置认为某些字符序列就像它们是一个单独的字符一样 — 这些称为*整理序列*。要在字符类中匹配其中一个多字符序列，请用 `[.` 和 `.]` 括起来。例如，如果你的区域设置有整理序列 `ch`，你可以用这个字符类匹配 `s`、`t` 或 `ch`：

```php
[st[.ch.]]
```

对字符类的最终扩展是*等价类*，你可以通过将字符括在 `[=` 和 `=]` 中指定。等价类匹配具有相同排序顺序的字符，如当前区域设置中定义的那样。例如，某个区域设置可能将 `a`、`á` 和 `ä` 定义为具有相同排序优先级。要匹配它们中的任何一个，等价类是 `[=a=]`。

表 4-7\. 字符类

| 类别 | 描述 | 扩展 |
| --- | --- | --- |
| `[:alnum:]` | 字母数字字符 | `[0-9a-zA-Z]` |
| `[:alpha:]` | 字母字符（字母） | `[a-zA-Z]` |
| `[:ascii:]` | 7 位 ASCII | `[\x01-\x7F]` |
| `[:blank:]` | 水平空白（空格、制表符） | `[ \t]` |
| `[:cntrl:]` | 控制字符 | `[\x01-\x1F]` |
| `[:digit:]` | 数字 | `[0-9]` |
| `[:graph:]` | 使用墨水打印的字符（非空格、非控制字符） | `[^\x01-\x20]` |
| `[:lower:]` | 小写字母 | `[a-z]` |
| `[:print:]` | 可打印字符（图形类加空格和制表符） | `[\t\x20-\xFF]` |
| `[:punct:]` | 任何标点字符，如句点（`.`）和分号（`;`） | `[-!"#$%&'()*+,./:;<=>?@[\\\]^_'{&#124;}~]` |
| `[:space:]` | 空白字符（换行符、回车符、制表符、空格、垂直制表符） | `[\n\r\t \x0B]` |
| `[:upper:]` | 大写字母 | `[A-Z]` |
| `[:xdigit:]` | 十六进制数字 | `[0-9a-fA-F]` |
| `\s` | 空白字符 | `[\r\n \t]` |
| `\S` | 非空白 | `[^\r\n \t]` |
| `\w` | 单词（标识符）字符 | `[0-9A-Za-z_]` |
| `\W` | 非单词（标识符）字符 | `[⁰-9A-Za-z_]` |
| `\d` | 数字 | `[0-9]` |
| `\D` | 非数字 | `[⁰-9]` |

## 锚点

锚点限定了正则表达式匹配到字符串中的特定位置（锚点并不匹配目标字符串中的实际字符）。正则表达式支持的锚点列在表 4-8 中。

表 4-8\. 锚点

| 锚点 | 匹配 |
| --- | --- |
| `^` | 字符串开始 |
| `$` | 字符串结尾 |
| `[[:<:]]` | 单词开头 |
| `[[:>:]]` | 单词结尾 |
| `\b` | 单词边界（在 `\w` 和 `\W` 之间或在字符串开头或结尾） |
| `\B` | 非单词边界（在 `\w` 和 `\w` 之间，或 `\W` 和 `\W` 之间） |
| `\A` | 字符串开始 |
| `\Z` | 字符串结束或在末尾的 `\n` 之前 |
| `\z` | 字符串结尾 |
| `^` | 行开始（或在启用 `/m` 标志时的 `\n` 之后） |
| `$` | 行尾（或在启用 `/m` 标志时的 `\n` 之前） |

词边界被定义为空白字符和标识符（字母数字或下划线）字符之间的点：

```php
preg_match("/[[:<:]]gun[[:>:]]/", "the Burgundy exploded"); // returns false
preg_match("/gun/", "the Burgundy exploded"); // returns true
```

请注意，字符串的开头和结尾也被视为词边界。

## 量词和贪婪

正则表达式量词通常是*贪婪*的。也就是说，当遇到量词时，引擎会尽可能多地匹配，同时仍满足模式的其余部分。例如：

```php
preg_match("/(<.*>)/", "do <b>not</b> press the button", $match);
// $match[1] is '<b>not</b>'
```

正则表达式从第一个小于号匹配到最后一个大于号。实际上，`.*`匹配第一个小于号之后的所有内容，然后引擎回溯以使其匹配越来越少，直到最终有一个大于号需要匹配。

这种贪婪性可能会成为问题。有时您需要*最小（非贪婪）匹配*，即尽可能少地匹配以满足模式的其余部分。Perl 提供了一组与贪婪量词对应的最小匹配量词。它们很容易记住，因为它们与贪婪量词相同，只是附加了一个问号（`?`）。表 4-9 显示了 Perl 风格正则表达式支持的相应贪婪和非贪婪量词。

表 4-9\. Perl 兼容正则表达式中的贪婪和非贪婪量词

| 贪婪量词 | 非贪婪量词 |
| --- | --- |
| `?` | `??` |
| `*` | `*?` |
| `+` | `+?` |
| `{m}` | `{m}?` |
| `{m,}` | `{m,}?` |
| `{m,n}` | `{m,n}?` |

这是如何使用非贪婪量词匹配标签的方法：

```php
preg_match("/(<.*?>)/", "do <b>not</b> press the button", $match);
// $match[1] is "<b>"
```

另一种更快的方法是使用字符类来匹配下一个大于号之前的每个非大于号字符：

```php
preg_match("/(<[^>]*>)/", "do <b>not</b> press the button", $match);
// $match[1] is '<b>'
```

## 非捕获组

如果您将模式的一部分括在括号中，则匹配该子模式的文本将被捕获并可以在以后访问。但有时，您想创建一个不捕获匹配文本的子模式。在兼容 Perl 的正则表达式中，您可以使用`(?:` *子模式* `)`结构来实现这一点：

```php
preg_match("/(?:ello)(.*)/", "jello biafra", $match);
// $match[1] is " biafra"
```

## 反向引用

您可以使用*反向引用*在模式中引用先前捕获的文本：`\1`引用第一个子模式的内容，`\2`引用第二个，依此类推。如果嵌套子模式，第一个从第一个左括号开始，第二个从第二个左括号开始，依此类推。

例如，这可以识别重复的单词：

```php
preg_match("/([[:alpha:]]+)\s+\1/", "Paris in the the spring", $m);
// returns true and $m[1] is "the"
```

`preg_match()`函数最多捕获 99 个子模式；第 99 个之后的子模式将被忽略。

## 尾部选项

Perl 风格的正则表达式允许您在正则表达式模式后面放置单个字母选项（标志），以修改匹配的解释或行为。例如，要进行不区分大小写匹配，只需使用`i`标志：

```php
preg_match("/cat/i", "Stop, Catherine!"); // returns true
```

表 4-10 显示了哪些 Perl 修饰符在兼容 Perl 的正则表达式中受支持。

表 4-10\. Perl 标志

| 修饰符 | 含义 |
| --- | --- |
| `/`*regexp*`/i` | 不区分大小写匹配 |
| `/`*regexp*`/s` | 使句点（`.`）匹配任何字符，*包括*换行符（`\n`） |
| `/`*regexp*`/x` | 从模式中去除空格和注释 |
| `/`*regexp*`/m` | 使插入符号（`^`）匹配内部换行符（`\n`）后，以及美元符号（`$`）匹配内部换行符（`\n`）前 |
| `/`*regexp*`/e` | 如果替换字符串是 PHP 代码，则使用`eval()`执行它以获取实际替换字符串 |

PHP 的 Perl 兼容正则表达式函数还支持 Perl 不支持的其他修饰符，如表 4-11 所列。

表 4-11\. 其他 PHP 标志

| Modifier | Meaning |
| --- | --- |
| `/`*regexp*`/U` | 反转子模式的贪婪性；`*`和`+`现在尽可能少地匹配，而不是尽可能多地匹配 |
| `/`*regexp*`/u` | 使模式字符串被视为 UTF-8 |
| `/`*regexp*`/X` | 使反斜杠后跟一个没有特殊含义的字符时发生错误 |
| `/`*regexp*`/A` | 使字符串的开头锚定，就好像模式的第一个字符是`^` |
| `/`*regexp*`/D` | 使`$`字符只匹配行尾 |
| `/`*regexp*`/S` | 使表达式解析器更仔细地检查模式的结构，因此在下一次运行（例如在循环中）时可能运行得稍快些 |

可以在单个模式中使用多个选项，如以下示例所示：

```php
$message = <<< END
To: you@youcorp
From: me@mecorp
Subject: pay up

Pay me or else!
END;

preg_match("/^subject: (.*)/im", $message, $match);

print_r($match);

// output: Array ( [0] => Subject: pay up [1] => pay up )
```

## 内联选项

除了在闭合模式定界符后指定整体模式选项外，还可以在模式中的部分指定选项以仅应用于模式的一部分。其语法为：

```php
(?*`flags`*:*`subpattern`*)
```

例如，在此示例中只有单词“PHP”是大小写不敏感的：

```php
echo preg_match('/I like (?i:PHP)/', 'I like pHp', $match);
print_r($match) ;
// returns true (echo: 1)
// $match[0] is 'I like pHp'
```

`i`、`m`、`s`、`U`、`x`和`X`选项可以以这种方式在内部应用。你可以同时使用多个选项：

```php
preg_match('/eat (?ix:foo d)/', 'eat FoOD'); // returns true
```

使用连字符（`-`）前缀来关闭一个选项：

```php
echo preg_match('/I like (?-i:PHP)/', 'I like pHp', $match);
print_r($matche) ;
// returns false (echo: 0)
// $match[0] is ''
```

替代形式可以启用或禁用标志，直到封闭子模式或模式结束：

```php
preg_match('/I like (?i)PHP/', 'I like pHp'); // returns true
preg_match('/I (like (?i)PHP) a lot/', 'I like pHp a lot', $match);
// $match[1] is 'like pHp'
```

内联标志不启用捕获。你需要额外的捕获括号来实现这一点。

## 前瞻和后顾

在模式中，有时可以很有用地说“如果接下来是这样，则在此处匹配”。这在分割字符串时特别常见。正则表达式描述了分隔符，但分隔符本身不会被返回。你可以使用*前瞻*来确保（不进行匹配，从而防止它被返回）分隔符后还有更多数据。同样地，*后顾*检查前面的文本。

前瞻和后顾有两种形式：*正向*和*负向*。正向前瞻或后顾表示“下一个/前一个文本必须是这样”。负向前瞻或后顾表示“下一个/前一个文本不能是这样”。表 4-12 展示了在兼容 Perl 的模式中可用的四种构造。这些构造都不捕获文本。

表 4-12\. 前瞻和后顾断言

| Construct | Meaning |
| --- | --- |
| `(?=`*subpattern*`)` | 正向预查 |
| `(?!`*subpattern*`)` | 负向预查 |
| `(?<=`*subpattern*`)` | 正向回顾后断言 |
| `(?<!`*subpattern*`)` | 负向回顾后断言 |

正向预查的一个简单用法是将 Unix mbox 邮件文件拆分为单独的消息。单独起始一行的单词`"From"`指示新消息的开始，因此可以通过指定分隔符为下一文本在行首为`"From"`来将邮箱拆分为消息：

```php
$messages = preg_split('/(?=^From )/m', $mailbox);
```

使用负回顾后断言的一个简单用法是提取包含带引号定界符的引号字符串。例如，以下是提取单引号字符串的方法（注意使用`x`修饰符注释正则表达式）：

```php
$input = <<< END
name = 'Tim O\'Reilly';
END;

$pattern = <<< END
' # opening quote
( # begin capturing
 .*? # the string
 (?<! \\\\ ) # skip escaped quotes
) # end capturing
' # closing quote END;
preg_match( "($pattern)x", $input, $match);
echo $match[1];
`Tim` `O\``'``Reilly`
```

唯一棘手的部分是，为了得到一个向后查看以查看最后一个字符是否为反斜杠的模式，我们需要转义反斜杠以防止正则表达式引擎看到`\)`，这将意味着文字的右括号。换句话说，我们必须反斜杠那个反斜杠：`\\)`。但是 PHP 的字符串引用规则表示`\\`会生成一个文字上的单个反斜杠，所以我们最终需要*四个*反斜杠来传递给正则表达式！这就是为什么正则表达式以难以阅读而闻名。

Perl 限制回顾后断言为常数宽度表达式。也就是说，表达式不能包含量词，如果使用选择，所有选择必须具有相同的长度。Perl 兼容的正则表达式引擎也禁止在回顾后断言中使用量词，但允许不同长度的选择。

## Cut

很少使用的一次性子模式或*cut*通过阻止某些类型模式在正则表达式引擎中的最坏情况行为。一旦匹配，该子模式将不再回溯。

一次性子模式的常见用法是当你有一个可能本身重复的重复表达式时：

```php
/(a+|b+)*\.+/
```

此代码片段需要几秒钟来报告失败：

```php
$p = '/(a+|b+)*\.+$/';
$s = 'abababababbabbbabbaaaaaabbbbabbababababababbba..!';

if (preg_match($p, $s)) {
 echo "Y";
}
else {
 echo "N";
}
```

正则表达式引擎尝试所有不同的起始匹配位置，但必须回溯每一个，这需要时间。如果你知道一旦某些内容匹配就不应该回溯，你应该用`(?>*subpattern*)`标记它：

```php
$p = '/(?>a+|b+)*\.+$/';
```

剪切永远不会改变匹配的结果；它只是让匹配失败更快。

## 条件表达式

条件表达式就像正则表达式中的`if`语句。一般形式是：

```php
(?(*`condition`*)*`yespattern`*)
(?(*`condition`*)*`yespattern`*|*`nopattern`*)
```

如果断言成功，正则表达式引擎匹配*yespattern*。第二种形式，如果断言不成功，正则表达式引擎跳过*yespattern*，尝试匹配*nopattern*。

断言可以是两种类型之一：回溯引用，或者前瞻或后顾匹配。要引用先前匹配的子字符串，断言是从 1 到 99 的数字（可用的最多回溯引用）。如果断言是一个回溯引用，条件只有在回溯引用匹配时才使用断言中的模式。如果断言不是回溯引用，则必须是正向或负向前瞻或后顾断言。

## 函数

有五类函数与 Perl 兼容的正则表达式一起使用：匹配、替换、分割、过滤，以及用于引用文本的实用函数。

### 匹配

`preg_match()` 函数在字符串上执行 Perl 风格的模式匹配。它相当于 Perl 中的 `m//` 操作符。`preg_match_all()` 函数接受与 `preg_match()` 函数相同的参数，并返回相同的值，只是它接受 Perl 风格的模式而不是标准模式：

```php
$found = preg_match(*`pattern`*, *`string`* [, *`captured`* ]);
```

例如：

```php
preg_match('/y.*e$/', 'Sylvie'); // returns true
preg_match('/y(.*)e$/', 'Sylvie', $m); // $m is array('ylvie', 'lvi')
```

虽然有一个 `preg_match()` 函数来进行不区分大小写的匹配，但没有 `preg_matchi()` 函数。相反，可以在模式上使用 `i` 标志：

```php
preg_match('y.*e$/i', 'SyLvIe'); // returns true
```

`preg_match_all()` 函数会重复匹配从上一次匹配结束的地方开始，直到不能再进行匹配为止：

```php
$found = preg_match_all(*`pattern`*, *`string`*, *`matches`* [, *`order`* ]);
```

*order* 值，可以是 `PREG_PATTERN_ORDER` 或 `PREG_SET_ORDER`，决定了 *matches* 的布局。我们将使用这段代码作为指南查看两者：

```php
$string = <<< END
13 dogs
12 rabbits
8 cows
1 goat
END;
preg_match_all('/(\d+) (\S+)/', $string, $m1, PREG_PATTERN_ORDER);
preg_match_all('/(\d+) (\S+)/', $string, $m2, PREG_SET_ORDER);
```

使用 `PREG_PATTERN_ORDER`（默认），数组的每个元素对应于特定的捕获子模式。因此，`$m1[0]` 是所有匹配模式的子字符串数组，`$m1[1]` 是匹配第一个子模式的所有子字符串数组（即数字），`$m1[2]` 是匹配第二个子模式的所有子字符串数组（即单词）。数组 `$m1` 比子模式多一个元素。

使用 `PREG_SET_ORDER`，数组的每个元素对应于下一次尝试匹配整个模式。因此，`$m2[0]` 是第一组匹配的数组（`'13 dogs'`, `'13'`, `'dogs'`），`$m2[1]` 是第二组匹配的数组（`'12 rabbits'`, `'12'`, `'rabbits'`），依此类推。数组 `$m2` 的元素数量与整个模式的成功匹配次数相同。

示例 4-1 获取特定网址的 HTML 到一个字符串中，并从该 HTML 中提取 URLs。对于每个 URL，它生成一个链接返回到显示该地址 URLs 的程序中。

##### 示例 4-1\. 从 HTML 页面提取 URLs

```php
<?php
if (getenv('REQUEST_METHOD') == 'POST') {
 $url = $_POST['url'];
}
else {
 $url = $_GET['url'];
}
?>

<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
 <p>URL: <input type="text" name="url" value="<?php echo $url ?>" /><br />
 <input type="submit">
</form>

<?php
if ($url) {
 $remote = fopen($url, 'r'); {
 $html = fread($remote, 1048576); // read up to 1 MB of HTML
 }
 fclose($remote);

 $urls = '(http|telnet|gopher|file|wais|ftp)';
 $ltrs = '\w';
 $gunk = '/#~:.?+=&%@!\-';
 $punc = '.:?\-';
 $any = "{$ltrs}{$gunk}{$punc}";

 preg_match_all("{
 \b # start at word boundary
 {$urls}: # need resource and a colon
 [{$any}] +? # followed by one or more of any valid
 # characters—but be conservative
 # and take only what you need
 (?= # the match ends at
 [{$punc}]* # punctuation
 [^{$any}] # followed by a non-URL character
 | # or
 \$ # the end of the string
 )
 }x", $html, $matches);

 printf("I found %d URLs<P>\n", sizeof($matches[0]));

 foreach ($matches[0] as $u) {
 $link = $_SERVER['PHP_SELF'] . '?url=' . urlencode($u);
 echo "<a href=\"{$link}\">{$u}</a><br />\n";
 }
}
```

### 替换

`preg_replace()` 函数的行为类似于文本编辑器中的查找和替换操作。它找到字符串中所有模式的出现，并将这些出现更改为其他内容：

```php
$new = preg_replace(*`pattern`*, *`replacement`*, *`subject`* [, *`limit`* ]);
```

最常见的用法是所有参数字符串，除了整数 *limit*。限制是要替换的模式的最大出现次数（当传递 `-1` 时，默认情况和当一个限制为 `−1` 时的行为都是所有出现）：

```php
$better = preg_replace('/<.*?>/', '!', 'do <b>not</b> press the button');
// $better is 'do !not! press the button'
```

将一个字符串数组作为*subject*传递给`preg_replace()`以对所有字符串进行替换。新字符串从中返回：

```php
$names = array('Fred Flintstone',
 'Barney Rubble',
 'Wilma Flintstone',
 'Betty Rubble');
$tidy = preg_replace('/(\w)\w* (\w+)/', '\1 \2', $names);
// $tidy is array ('F Flintstone', 'B Rubble', 'W Flintstone', 'B Rubble')
```

要对同一个字符串或字符串数组执行多次替换，只需一次调用`preg_replace()`并传递模式和替换的数组即可：

```php
$contractions = array("/don't/i", "/won't/i", "/can't/i");
$expansions = array('do not', 'will not', 'can not');
$string = "Please don't yell - I can't jump while you won't speak";
$longer = preg_replace($contractions, $expansions, $string);
// $longer is 'Please do not yell - I can not jump while you will not speak';
```

如果你提供的替换少于模式，则匹配额外模式的文本将被删除。这是一种方便的批量删除方式：

```php
$htmlGunk = array('/<.*?>/', '/&.*?;/');
$html = '&eacute; : <b>very</b> cute';
$stripped = preg_replace($htmlGunk, array(), $html);
// $stripped is ' : very cute'
```

如果你给定了一个模式数组但只有一个字符串替换，则每个模式都将使用相同的替换：

```php
$stripped = preg_replace($htmlGunk, '', $html);
```

替换可以使用反向引用。不过，与模式中的反向引用不同，替换中首选的语法是`$1`、`$2`、`$3`等。例如：

```php
echo preg_replace('/(\w)\w+\s+(\w+)/', '$2, $1.', 'Fred Flintstone')
Flintstone, F.
```

`/e`修饰符使得`preg_replace()`将替换字符串视为 PHP 代码，返回实际用于替换的字符串。例如，这会将每个摄氏温度转换为华氏温度：

```php
$string = 'It was 5C outside, 20C inside';
echo preg_replace('/(\d+)C\b/e', '$1*9/5+32', $string);
It was 41 outside, 68 inside
```

这个更复杂的示例会展开字符串中的变量：

```php
$name = 'Fred';
$age = 35;
$string = '$name is $age';
preg_replace('/\$(\w+)/e', '$$1', $string);
```

每个匹配项都会分离变量的名称（`$name`、`$age`）。替换中的`$1`指的是这些名称，因此实际执行的 PHP 代码是`$name`和`$age`。该代码会评估变量的值，这就是替换时使用的内容。呼！

`preg_replace_callback()`是`preg_replace()`的一种变体。它调用一个函数来获取替换字符串。该函数会传递一个匹配数组（零号元素是匹配模式的全部文本，第一个是第一个捕获子模式的内容，依此类推）。例如：

```php
function titlecase($s)
{
 return ucfirst(strtolower($s[0]));
}

$string = 'goodbye cruel world';
$new = preg_replace_callback('/\w+/', 'titlecase', $string);
echo $new;

`Goodbye` `Cruel` `World`
```

### 分割

当你知道要提取字符串的部分时，可以使用`preg_match_all()`，而当你知道如何*分隔*这些部分时，则可以使用`preg_split()`：

```php
$chunks = preg_split(*`pattern`*, *`string`* [, *`limit`* [, *`flags`* ]]);
```

*模式*匹配两个部分之间的分隔符。默认情况下，分隔符不会被返回。可选的*limit*参数指定要返回的最大部分数（默认值为`−1`，表示所有部分）。*flags*参数是标志位 OR 组合，包括`PREG_SPLIT_NO_EMPTY`（不返回空部分）和`PREG_SPLIT_DELIM_CAPTURE`（在模式中捕获的字符串部分会被返回）。

例如，要从简单的数字表达式中仅提取操作数，请使用：

```php
$ops = preg_split('{[+*/−]}', '3+5*9/2');
// $ops is array('3', '5', '9', '2')
```

要提取操作数和操作符，请使用：

```php
$ops = preg_split('{([+*/−])}', '3+5*9/2', −1, PREG_SPLIT_DELIM_CAPTURE);
// $ops is array('3', '+', '5', '*', '9', '/', '2')
```

空模式在字符串中的每个字符边界以及字符串的开头和结尾匹配。这使您可以将字符串拆分为字符数组：

```php
$array = preg_split('//', $string);
```

### 使用正则表达式过滤数组

`preg_grep()`函数返回一个数组中与给定模式匹配的元素：

```php
$matching = preg_grep(*`pattern`*, *`array`*);
```

例如，要仅获取以*.txt*结尾的文件名，请使用：

```php
$textfiles = preg_grep('/\.txt$/', $filenames);
```

### 正则表达式引用

`preg_quote()`函数创建一个仅匹配给定字符串的正则表达式：

```php
$re = preg_quote(*`string`* [, *`delimiter`* ]);
```

*string*中的每个特殊含义字符（例如`*`或`$`）都会用反斜杠作为前缀：

```php
echo preg_quote('$5.00 (five bucks)');
\$5\.00 \(five bucks\)
```

可选的第二个参数是一个要转义的额外字符。通常，你会在这里传递你的正则表达式分隔符：

```php
$toFind = '/usr/local/etc/rsync.conf';
$re = preg_quote($toFind, '/');

if (preg_match("/{$re}/", $filename)) {
 // found it!
}
```

## 与 Perl 正则表达式的差异

尽管非常相似，PHP 实现的 Perl 风格正则表达式与实际 Perl 正则表达式有一些细微差异：

+   不允许在模式字符串中作为文字字符使用空字符（ASCII 0）。但是你可以通过其他方式引用它（如`\000`、`\x00`等）。

+   不支持`\E`、`\G`、`\L`、`\l`、`\Q`、`\u`和`\U`选项。

+   不支持使用`(?{` *一些 Perl 代码* `})` 结构。

+   支持`/D`、`/G`、`/U`、`/u`、`/A`和`/X`修饰符。

+   垂直制表符`\v`被视为空白字符。

+   前瞻断言和后顾断言不能使用`*`、`+`或`?`进行重复。

+   在负向断言中的括号子匹配不会被记住。

+   后顾断言中的交替分支可以有不同的长度。

# 下一步计划

现在你已经了解了关于字符串及其操作的所有内容，PHP 的下一个主要部分我们将关注的是数组。这些复合数据类型会让你挑战，但你需要对它们非常熟悉，因为 PHP 在许多领域都使用它们。学习如何添加数组元素、对数组进行排序以及处理多维数组形式对于成为一个优秀的 PHP 开发者是至关重要的。
