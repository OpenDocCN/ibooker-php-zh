# 第四章：字符串

字符串是 PHP 数据的基本构建块之一。每个字符串表示一个有序的字节序列。字符串可以是人类可读的文本部分（例如`To be or not to be`），也可以是编码为整数的原始字节序列（例如`\110\145\154\154\157\40\127\157\162\154\144\41`）。¹ PHP 应用程序读取或写入的每个数据元素都表示为字符串。

在 PHP 中，字符串通常编码为[ASCII 值](https://oreil.ly/Tjsyx)，尽管您可以根据需要在 ASCII 和其他格式（如 UTF-8）之间转换。字符串可以包含`null`字节，并且在 PHP 进程具有足够的内存可用的情况下，其存储几乎没有限制。

在 PHP 中创建字符串的最基本方法是使用单引号。单引号字符串被视为字面状态——没有特殊字符或任何类型的*插值*变量。要在单引号字符串中包含字面单引号，必须通过在其前面加上反斜杠来*转义*该引号，例如，`\'`。实际上，唯一需要转义的两个字符是单引号本身或反斜杠。例子 4-1 展示了单引号字符串及其相应的打印输出。

###### 注意

*变量插值*是直接按名称引用变量的实践，在字符串中让解释器在运行时将变量替换为其值。插值允许更灵活的字符串，因为您可以编写一个字符串，但动态替换其中一些内容以适应代码中的位置上下文。

##### 示例 4-1. 单引号字符串

```php
print 'Hello, world!';
// Hello, world!

print 'You\'ve only got to escape single quotes and the \\ character.';
// You've only got to escape single quotes and the \ character.

print 'Variables like $blue are printed as literals.';
// Variables like $blue are printed as literals.

print '\110\145\154\154\157\40\127\157\162\154\144\41';
// \110\145\154\154\157\40\127\157\162\154\144\41
```

更复杂的字符串可能需要插入变量或引用特殊字符，比如换行符或制表符。对于这些更复杂的用例，PHP 需要使用双引号，并允许使用各种转义序列，如表 4-1 所示。

表 4-1. 双引号字符串转义序列

| 转义序列 | 字符 | 示例 |
| --- | --- | --- |
| `\n` | 换行符 | `"这个字符串以换行符结尾。\n"` |
| `\r` | 回车符 | `"这个字符串以回车符结尾。\r"` |
| `\t` | 制表符 | `"很多\t 空格"` |
| `\\` | 反斜杠 | `"必须转义\\字符。"` |
| `\$` | 美元符号 | `电影票价为\$10。` |
| `\"` | 双引号 | `"一些引号是 \"恐怖引号\"."` |
| `\0` 到 `\777` | 八进制字符值 | `"\120\110\120"` |
| `\x0` 到 `\xFF` | 十六进制字符值 | `"\x50\x48\x50"` |

除了用前导反斜杠明确转义的特殊字符外，PHP 将自动替换任何传递到双引号字符串中的变量的值。此外，如果将整个表达式用大括号（`{}`）包裹起来并将其视为变量，PHP 将在双引号字符串中插值整个表达式。示例 4-2 展示了如何处理双引号字符串中的变量，无论是复杂还是简单的。

##### Example 4-2\. 双引号字符串中的变量插值

```php
print "The value of \$var is $var"; ![1](img/1.png)
print "Properties of objects can be interpolated, too. {$obj->value}"; ![2](img/2.png)
print "Prints the value of the variable returned by getVar(): {${getVar()}}"; ![3](img/3.png)
```

![1](img/#co_strings_CO1-1)

`$var` 的第一个引用是转义的，但第二个将被其实际值替换。如果 `$var = 'apple'`，则该字符串将打印 `The value of $var is apple`。

![2](img/#co_strings_CO1-2)

在双引号字符串中，使用大括号可以直接引用对象属性，就好像这些属性是本地定义的变量一样。

![3](img/#co_strings_CO1-3)

假设 `getVar()` 返回一个已定义变量的名称，这行代码将执行该函数并打印分配给该变量的值。

单引号和双引号字符串都表示为单行。但通常，程序需要将多行文本（或多行编码的二进制）表示为一个字符串时，开发人员最好的工具是 Heredoc。

*Heredoc* 是一个以三个尖括号（`<<<` 运算符）开始的文本块，接着是一个命名标识符，然后是一个换行符。每个后续的文本行（包括换行符）都是字符串的一部分，直到一个仅包含 Heredoc 命名标识符和一个分号的独立行。示例 4-3 展示了代码中 Heredoc 的外观。

###### 注意

用于 Heredoc 的标识符不需要大写。然而，在 PHP 中，通常习惯于始终将这些标识符大写，以帮助区分它们与字符串定义的文本。

##### Example 4-3\. 使用 Heredoc 语法定义字符串

```php
$poem = <<<POEM
To be or not to be,
That is the question
POEM;
```

Heredoc 函数与双引号字符串完全相同，并允许在其中进行变量插值（或者类似转义十六进制的特殊字符）。在应用程序中编码 HTML 块时，这特别有用，因为可以使用变量使字符串变得动态。

但在某些情况下，您可能希望使用字符串字面量而不是开放变量插值的形式。在这种情况下，PHP 的 Nowdoc 语法提供了单引号风格的 Heredoc 双引号字符串的替代方案。Nowdoc 看起来几乎与 Heredoc 相同，只是标识符本身用单引号括起来，如 示例 4-4 所示。

##### Example 4-4\. 使用 Nowdoc 语法定义字符串

```php
$poem = <<<'POEM'
To be or not to be,
That is the question
POEM;
```

在 Heredoc 和 Nowdoc 块中可以使用单引号和双引号，而无需额外的转义。但是，Nowdocs 不会插值或动态替换任何值，无论它们是否被转义或其他情况。

接下来的示例可以帮助进一步说明 PHP 中如何使用字符串及其解决的各种问题。

# 4.1 在更大的字符串中访问子字符串

## 问题

您希望确定一个字符串是否包含特定的子字符串。例如，您想知道一个 URL 是否包含文本 `/secret/`。

## 解决方案

使用 `strpos()`：

```php
if (strpos($url, '/secret/') !== false) {
    // A secret fragment was detected, run additional logic
    // ...
}
```

## 讨论

PHP 的 `strpos()` 函数将扫描给定的字符串，并确定给定子字符串的第一次出现的起始位置。这个函数像是在干草堆中找针一样，因为函数的参数分别被命名为 `$haystack` 和 `$needle`。如果未找到子字符串（`$needle`），则函数返回布尔值 `false`。

在这种情况下使用*严格的相等比较*很重要，因为 `strpos()` 如果子字符串出现在要搜索的字符串的开头，则返回 `0`。从 Recipe 2.3 中可以了解到，使用只有两个等号的比较将尝试重新转换类型，将整数 `0` 转换为布尔值 `false`；始终使用严格比较运算符（`===` 表示相等或 `!==` 表示不相等）以避免混淆。

如果 `$needle` 在字符串中出现多次，`strpos()` 只会返回第一次出现的位置。您可以通过将第三个参数作为函数调用的可选位置偏移量来搜索其他出现，如在 示例 4-5 中。定义偏移量还允许您搜索字符串的后半部分，以查找您已知已在字符串中较早出现的子字符串。

##### 示例 4-5\. 计算子字符串的所有出现次数

```php
function count_occurrences($haystack, $needle)
{
    $occurrences = 0;
    $offset = 0;
    $pos = 0; ![1](img/1.png)

    do {
        $pos = strpos($haystack, $needle, $offset);

        if ($pos !== false) { ![2](img/2.png)
            $occurrences += 1;
            $offset = $pos + 1; ![3](img/3.png)
        }
    } while ($pos !== false); ![4](img/4.png)

    return $occurrences;
}

$str = 'How much wood would a woodchuck chuck if a woodchuck could chuck wood?';

print count_occurrences($str, 'wood'); // 4 print count_occurrences($str, 'nutria'); // 0
```

![1](img/#co_strings_CO2-1)

所有变量最初都设置为 `0`，以便跟踪新字符串的出现次数。

![2](img/#co_strings_CO2-2)

只有在找到字符串时才应计算一个出现次数。

![3](img/#co_strings_CO2-3)

如果找到了字符串，则更新偏移量，但也要增加 `1`，以免重复计算已经找到的出现次数。

![4](img/#co_strings_CO2-4)

一旦达到目标子字符串的最后一个出现，退出循环并返回总计数。

## 参见

PHP 文档中关于 [`strpos()`](https://oreil.ly/w9Od4) 的说明。

# 4.2 从另一个字符串中提取一个字符串

## 问题

您想从一个更大的字符串中提取一个小字符串，例如从电子邮件地址中提取域名。

## 解决方案

使用 `substr()` 来选择您想要提取的字符串的部分：

```php
$string = 'eric.mann@cookbook.php';
$start = strpos($string, '@');

$domain = substr($string, $start + 1);
```

## 讨论

PHP 的 `substr()` 函数基于初始偏移量（第二个参数）返回给定字符串的部分，直到可选长度。完整的函数签名如下：

```php
function substr(string $string, int $offset, ?int $length = null): string
```

如果省略了 `$length` 参数，`substr()` 将返回字符串的剩余部分。如果 `$offset` 参数大于输入字符串的长度，则返回一个空字符串。

你也可以指定一个 *负* 偏移量，从字符串末尾而不是开头开始返回子集，就像 示例 4-6 中展示的那样。

##### 示例 4-6\. 带有负偏移量的子字符串

```php
$substring = substr('phpcookbook', -3); ![1](img/1.png)
$substring = substr('phpcookbook', -2); ![2](img/2.png)
$substring = substr('phpcookbook', -8, 4); ![3](img/3.png)
```

![1](img/#co_strings_CO3-1)

返回 `ook`（最后三个字符）

![2](img/#co_strings_CO3-2)

返回 `ok`（最后两个字符）

![3](img/#co_strings_CO3-3)

返回 `cook`（中间四个字符）

你应该注意一些关于 `substr()` 中偏移量和字符串长度的其他边界情况。偏移量可以合法地从字符串内部开始，但 `$length` 可能会超出字符串的末尾。PHP 会捕捉到这种不一致，并且即使最终返回的字符串长度 *小于* 指定的长度，也仅返回原始字符串的剩余部分。示例 4-7 细节化了基于不同指定长度的 `substr()` 可能的输出。

##### 示例 4-7\. 不同的子字符串长度

```php
$substring = substr('Four score and twenty', 11, 3); ![1](img/1.png)
$substring = substr('Four score and twenty', 99, 3); ![2](img/2.png)
$substring = substr('Four score and twenty', 20, 3); ![3](img/3.png)
```

![1](img/#co_strings_CO4-1)

返回 `and`

![2](img/#co_strings_CO4-2)

返回一个空字符串

![3](img/#co_strings_CO4-3)

返回 `y`

另一个边界情况是函数中提供的负 `$length`。当请求一个具有负长度的子字符串时，PHP 将从返回的子字符串中删除相应数量的字符，正如 示例 4-8 中所示。

##### 示例 4-8\. 带有负长度的子字符串

```php
$substring = substr('Four score and twenty', 5); ![1](img/1.png)
$substring = substr('Four score and twenty', 5, -11); ![2](img/2.png)
```

![1](img/#co_strings_CO5-1)

返回 `score and twenty`

![2](img/#co_strings_CO5-2)

返回 `score`

## 参见

PHP 文档中的 [`substr()`](https://oreil.ly/z_w10) 和 [`strpos()`](https://oreil.ly/NWcWJ) 函数。

# 4.3 替换字符串的一部分

## 问题

你想要仅用另一个字符串替换字符串的一部分。例如，在将电话号码打印到屏幕之前，你想要模糊显示除最后四位数字以外的所有内容。

## 解决方案

使用 `substr_replace()` 基于其位置替换现有字符串的组成部分：

```php
$string = '555-123-4567';
$replace = 'xxx-xxx'

$obfuscated = substr_replace($string, $replace, 0, strlen($replace));
// xxx-xxx-4567
```

## 讨论

PHP 的 `substr_replace()` 函数操作字符串的一部分，类似于 `substr()`，通过整数偏移量和特定长度定义。示例 4-9 展示了完整的函数签名。

##### 示例 4-9\. `substr_replace()` 的完整函数签名

```php
function substr_replace(
    array|string $string,
    array|string $replace,
    array|int $offset,
    array|int|null $length = null
): string
```

不像它的 `substr()` 模拟函数，`substr_replace()` 可以操作单个字符串或字符串集合。如果将包含标量值的字符串数组传递给 `$replace` 和 `$offset`，函数将对每个字符串执行替换，就像 示例 4-10 中展示的那样。

##### 示例 4-10\. 同时替换多个子字符串

```php
$phones = [
    '555-555-5555',
    '555-123-1234',
    '555-991-9955'
];

$obfuscated = substr_replace($phones, 'xxx-xxx', 0, 7);

// xxx-xxx-5555
// xxx-xxx-1234
// xxx-xxx-9955
```

总的来说，开发人员在这个函数的参数上有很大的灵活性。类似于`substr()`，以下内容是正确的：

+   如果`$offset`为负数，则从字符串的末尾开始进行替换。

+   `$length`可以为负数，表示从字符串末尾开始停止替换的字符数。

+   如果`$length`为`null`，它将内部变为输入字符串本身的长度。

+   如果`length`为`0`，`$replace`将被插入到给定的`$offset`处的字符串中，并且根本不会进行替换。

最后，如果`$string`被提供为数组，那么所有其他参数也可以作为数组提供。每个元素将代表在`$string`中相同位置的字符串的设置，如示例 4-11 所示。

##### Example 4-11\. 使用数组参数替换多个子字符串

```php
$phones = [
    '555-555-5555',
    '555-123-1234',
    '555-991-9955'
];

$offsets = [0, 0, 4];

$replace = [
    'xxx-xxx',
    'xxx-xxx',
    'xxx-xxxx'
];

$lengths = [7, 7, 8];

$obfuscated = substr_replace($phones, $replace, $offsets, $lengths);

// xxx-xxx-5555
// xxx-xxx-1234
// 555-xxx-xxxx
```

###### 注意

不要求传递给`$string`、`$replace`、`$offset`和`$length`的数组具有相同的大小。如果您传递具有不同维度的数组，PHP 不会抛出错误或警告。但这将导致在替换操作期间产生意外的输出，例如截断字符串而不是替换其内容。验证这四个数组的每个维度是否匹配是个好主意。

如果您确切地知道在字符串中需要替换字符的位置，`substr_replace()`函数是很方便的。在某些情况下，您可能不知道需要替换的子字符串的位置，但您希望替换特定子字符串的出现。在这种情况下，您会希望使用`str_replace()`或`str_ireplace()`。

这两个函数将搜索指定的字符串以查找指定子字符串的出现（或多个出现），并将其替换为其他内容。这两个函数在调用模式上是相同的，但`str_ireplace()`中的额外的`i`表示它以*不区分大小写*的方式搜索模式。示例 4-12 展示了这两个函数的使用。

##### 示例 4-12\. 在字符串内搜索和替换

```php
$string = 'How much wood could a Woodchuck chuck if a woodchuck could chuck wood?';

$beaver = str_replace('woodchuck', 'beaver', $string); ![1](img/1.png)
$ibeaver = str_ireplace('woodchuck', 'beaver', $string); ![2](img/2.png)
```

![1](img/#co_strings_CO6-1)

*如果一只海狸可以扔木头，它能扔多少木头？*

![2](img/#co_strings_CO6-2)

*如果一只海狸可以扔木头，它能扔多少木头？*

`str_replace()`和`str_ireplace()`都接受一个可选的`$count`参数，通过引用传递。如果指定了此变量，函数执行的替换次数将更新到此变量中。在示例 4-12 中，由于`Woodchuck`的大写，返回值分别为`1`和`2`。

## 参见

PHP 文档：[`substr_replace()`](https://oreil.ly/-BSkA)、[`str_replace()`](https://oreil.ly/Vm7KH)和[`str_ireplace()`](https://oreil.ly/8P46w)。

# 4.4 逐字节处理字符串

## 问题

你需要从头到尾处理一个由单字节字符组成的字符串，逐个字符处理。

## 解决方案

像循环遍历数组一样遍历字符串的每个字符。示例 4-13 将计算字符串中大写字母的数量。

##### 示例 4-13\. 计算字符串中的大写字母数量

```php
$capitals = 0;

$string = 'The Carriage held but just Ourselves - And Immortality';
for ($i = 0; $i < strlen($string); $i++) {
    if (ctype_upper($string[$i])) {
        $capitals += 1;
    }
}

// $capitals = 5
```

## 讨论

在 PHP 中，字符串不是数组，因此不能直接对它们进行循环。但是，它们提供了类似数组的访问方式，可以根据它们在字符串中的位置访问单个字符。你可以通过它们的整数偏移量（从 0 开始）引用单个字符，甚至可以通过*负*偏移量从字符串的末尾开始。

类似数组的访问并非只读。你也可以根据其位置轻松*替换*字符串中的单个字符，正如示例 4-14 所示。

##### 示例 4-14\. 替换字符串中的单个字符

```php
$string = 'A new recipe made my coffee stronger this morning';
$string[31] = 'a';

// A new recipe made my coffee stranger this morning
```

也可以通过使用[`str_split()`](https://oreil.ly/eNxaF)将字符串*直接*转换为数组，然后迭代结果数组中的所有项目来实现。这将作为更新到解决方案示例，如示例 4-15 中所示。

##### 示例 4-15\. 将字符串直接转换为数组

```php
$capitals = 0;

$string = 'The Carriage held but just Ourselves - And Immortality';
$stringArray = str_split($string);
foreach ($stringArray as $char) {
    if (ctype_upper($char)) {
        $capitals += 1;
    }
}

// $capitals = 5
```

示例 4-15 的缺点在于 PHP 现在必须维护*两份*数据副本：原始字符串和结果数组。在处理像示例中那样的小字符串时，这不是问题；但如果你的字符串代表磁盘上的整个文件，你将迅速耗尽 PHP 可用的内存。

PHP 使得在不改变数据类型的情况下相对容易地访问字符串中的单个字节（字符）。将字符串拆分为数组是可行的，但除非你确实*需要*一个字符数组，否则可能是不必要的。示例 4-16 重新构想了示例 4-15，使用了数组缩减技术，而不是直接计算字符串中的大写字母数量。

##### 示例 4-16\. 使用数组缩减技术计算字符串中的大写字母数量

```php
$str = 'The Carriage held but just Ourselves - And Immortality';

$caps = array_reduce(str_split($str), fn($c, $i) => ctype_upper($i) ? $c+1: $c, 0);
```

###### 注意

虽然示例 4-16 在功能上等效于示例 4-15，但它更为简洁，因此更难理解。虽然将复杂逻辑重新构想为单行函数很诱人，但为了简洁而不必要地重构代码可能是危险的。代码可能看起来优雅，但随着时间的推移，会变得更难维护。

在示例 4-16 中引入的简化缩减是功能上准确的，但仍然需要将字符串拆分为数组。它在程序中节省了代码行数，但仍然会导致创建数据的第二份副本。如前所述，如果你迭代的字符串很大（例如大型二进制文件），这将迅速消耗 PHP 可用的内存。

## 参见

PHP 关于[string 访问和修改](https://oreil.ly/8MOWh)的文档，以及关于[`ctype_upper()`](https://oreil.ly/bQctH)的文档。

# 4.5 生成随机字符串

## 问题

您希望生成一串随机字符。

## 解决方案

使用 PHP 的本地`random_int()`函数：

```php
function random_string($length = 16)
{
    $characters = '0123456789abcdefghijklmnopqrstuvwxyz';

    $string = '';
    while (strlen($string) < $length) {
        $string .= $characters[random_int(0, strlen($characters) - 1)];
    }
    return $string;
}
```

## 讨论

PHP 拥有强大的、*密码学安全*的伪随机生成函数，适用于整数和字节。它没有原生函数生成随机的可读文本，但可以通过利用人类可读字符列表来创建这样的随机文本串，示例见解决方案部分。

###### 注意

*密码学安全的伪随机数生成器*是一种函数，它返回没有可区分或可预测模式的数字。即使是法庭鉴定也无法区分随机噪声和密码学安全生成器的输出。

生成随机字符串的一个有效且可能更简单的方法是利用 PHP 的`random_bytes()`函数，并将二进制输出编码为 ASCII 文本。Example 4-17 展示了两种可能的使用随机字节作为字符串的方法。

##### 示例 4-17\. 创建一串随机字节

```php
$string = random_bytes(16); ![1](img/1.png)

$hex = bin2hex($string); ![2](img/2.png)
$base64 = base64_encode($string); ![3](img/3.png)
```

![1](img/#co_strings_CO7-1)

因为二进制字节串将以不同的格式进行编码，所以产生的字节数量将*不会*与最终字符串的长度匹配。

![2](img/#co_strings_CO7-2)

将随机字符串编码为十六进制格式。请注意，这种格式会使字符串长度加倍——16 字节相当于 32 个十六进制字符。

![3](img/#co_strings_CO7-3)

利用[Base64 编码](https://oreil.ly/NsyVs)将原始字节转换为可读字符。请注意，这种格式会使字符串长度增加 33%至 36%。

## 参见

PHP 关于[`random_int()`](https://oreil.ly/g3gAR)和[`random_bytes()`](https://oreil.ly/2Zbio)的文档。还有关于生成随机数的 Recipe 5.4。

# 4.6 在字符串中插入变量

## 问题

您希望在静态字符串中包含动态内容。

## 解决方案

使用双引号包裹字符串并直接在字符串中插入变量、对象属性甚至函数/方法调用：

```php
echo "There are {$_POST['cats']} cats and {$_POST['dogs']} outside.";
echo "Your username is {strlen($username)} characters long.";
echo "The car is painted {$car->color}.";
```

## 讨论

与单引号字符串不同，双引号字符串允许作为字面量使用复杂的动态值。任何以`$`字符开头的单词都会被解释为变量名，除非该前导字符被正确转义。²

虽然解决方案示例中用花括号包裹动态内容，但在 PHP 中这不是必需的。简单变量可以直接在双引号字符串中写入并正确解析。然而，对于更复杂的序列，如果没有花括号，将会使读取变得困难。强烈推荐的最佳实践是始终用花括号括起要插入的任何值，以使字符串更易读。

不幸的是，字符串插值也有其局限性。解决方案示例展示了从超全局`$_POST`数组中提取数据并直接插入字符串的操作。这是潜在危险的，因为该内容由用户直接生成，并且该字符串可能在敏感环境中被利用。事实上，这种类似插值的字符串操作是应用程序中最大的注入攻击向量之一。

###### 注意

在注入攻击中，第三方可以传递（或注入）可执行或恶意输入到您的应用程序中，并导致其行为异常。更复杂的防护方法可以在第九章中找到。

为了保护您的字符串免受潜在恶意用户生成的输入的影响，最好使用 PHP 的`sprintf()`函数通过格式字符串来过滤内容。示例 4-18 重写了解决方案示例的部分，以防止恶意的`$_POST`数据。

##### 示例 4-18\. 使用格式化字符串生成插值字符串

```php
echo sprintf('There are %d cats and %d dogs.', $_POST['cats'], $_POST['dogs']);
```

格式化字符串在 PHP 中是一种基本的输入清理形式。在示例 4-18 中，您明确假定提供的`$_POST`数据是数字。格式字符串中的`%d`标记将被用户提供的数据替换，但 PHP 在替换期间将显式地将这些数据强制转换为整数。

例如，如果此字符串正在插入数据库，则格式化将保护免受针对 SQL 接口的注入攻击的威胁。更完整的用户输入过滤和清理方法在第九章中讨论。

## 参见

PHP 关于[双引号内变量解析](https://oreil.ly/CAj-J)和 Heredoc 以及[`sprintf()`函数](https://oreil.ly/DMAg6)的文档。

# 4.7 合并多个字符串

## 问题

您需要从两个较小的字符串创建一个新的字符串。

## 解决方案

使用 PHP 的字符串连接运算符：

```php
$first = 'To be or not to be';
$second = 'That is the question';

$line = $first . ' ' . $second;
```

## 讨论

PHP 使用单个`.`字符来连接两个字符串。此运算符还会利用类型强制转换，确保操作中的两个值在串联之前都是字符串，如示例 4-19 所示。

##### 示例 4-19\. 字符串连接

```php
print 'String ' . 2; ![1](img/1.png)
print 2 . ' number'; ![2](img/2.png)
print 'Boolean ' . true; ![3](img/3.png)
print 2 . 3; ![4](img/4.png)
```

![1](img/#co_strings_CO8-1)

打印`String 2`

![2](img/#co_strings_CO8-2)

打印`2 number`

![3](img/#co_strings_CO8-3)

打印`Boolean 1`因为布尔值被转换为整数然后转换为字符串

![4](img/#co_strings_CO8-4)

打印`23`

字符串连接运算符是将简单字符串快速组合的一种方式，但如果用它来组合多个带有空格的字符串，可能会显得有些冗长。考虑示例 4-20，在这个示例中，您试图将一组单词组合成一个字符串，每个单词之间用空格分隔。

##### 示例 4-20\. 连接大量字符串时的冗长性

```php
$words = [
    'Whose',
    'woods',
    'these',
    'are',
    'I',
    'think',
    'I',
    'know'
];

$option1 = $words[0] . ' ' . $words[1] . ' ' . $words[2] . ' ' . $words[3] .
         ' ' . $words[4] . ' ' . $words[5] . ' ' . $words[6] .
         ' ' . $words[7]; ![1](img/1.png)

$option2 = '';
foreach ($words as $word) {
    $option2 .= ' ' . $word; ![2](img/2.png)
}
$option2 = ltrim($option2); ![3](img/3.png)
```

![1](img/#co_strings_CO9-1)

一种选择是逐个将集合中的每个单词与空格分隔符连接起来。随着单词列表的增长，这很快变得难以管理。

![2](img/#co_strings_CO9-2)

相反，您可以循环遍历集合并构建一个连接的字符串，而无需逐个访问集合中的每个项。

![3](img/#co_strings_CO9-3)

在使用循环时，可能会出现不必要的空白。您需要记住从字符串开头修剪多余的空格。

大型重复的连接例程可以被本机 PHP 函数如`implode()`所取代。特别是此函数接受要连接的数据数组以及要在数据元素之间使用的字符（或字符）的定义。它返回最终的连接字符串。

###### 注意

有些开发者更喜欢使用`join()`而不是`implode()`，因为它被认为是操作的更描述性名称。事实上，`join()`是`implode()`的别名，PHP 编译器不关心您使用哪个。

重新编写示例 4-20 以使用`implode()`使整个操作变得简单得多，正如示例 4-21 所示。

##### 示例 4-21\. 字符串连接的简洁方法

```php
$words = [
    'Whose',
    'woods',
    'these',
    'are',
    'I',
    'think',
    'I',
    'know'
];

$string = implode(' ', $words);
```

注意记住`implode()`的参数顺序。字符串分隔符首先出现，然后是您想要迭代的数组。PHP 的早期版本（PHP 8.0 之前）允许参数以相反的顺序指定。在 PHP 7.4 中，此行为（先指定数组，然后是分隔符）已弃用。从 PHP 8.0 开始，这将引发`TypeError`。

如果您在使用 PHP 8.0 之前编写的库，请确保在将项目部署到生产环境之前测试它没有错误使用`implode()`或`join()`。

## 参见

PHP 文档关于[`implode()`](https://oreil.ly/bGYt0)。

# 4.8 管理存储在字符串中的二进制数据

## 问题

您希望直接将数据编码为二进制，而不是作为 ASCII 格式的表示，或者您希望读取作为二进制数据明确编码的数据到您的应用程序中。

## 解决方案

使用`unpack()`从字符串中提取二进制数据：

```php
$unpacked = unpack('S1', 'Hi'); // [1 => 26952]
```

使用`pack()`将二进制数据写入字符串：

```php
$packed = pack('S13', 72, 101, 108, 108, 111, 44, 32, 119, 111,
               114, 108, 100, 33); // 'Hello, world!'
```

## 讨论

`pack()`和`unpack()`都使您能够操作原始二进制字符串，假设您知道您正在使用的二进制字符串的格式。每个函数的第一个参数是格式规范。此规范由特定的格式代码确定，如表 4-2 中定义的。

表 4-2\. 二进制格式字符串代码

| 代码 | 描述 |
| --- | --- |
| `a` | 以空字符填充的字符串 |
| `A` | 以空格填充的字符串 |
| `h` | 十六进制字符串，低半字节优先 |
| `H` | 十六进制字符串，高半字节优先 |
| `c` | 有符号字符 |
| `C` | 无符号字符 |
| `s` | 有符号短整型（始终为 16 位，机器字节顺序） |
| `S` | 无符号短整型（始终为 16 位，机器字节顺序） |
| `n` | 无符号短整型（始终为 16 位，大端字节顺序） |
| `v` | 无符号短整型（始终为 16 位，小端字节顺序） |
| `i` | 有符号整型（机器相关大小和字节顺序） |
| `I` | 无符号整型（机器相关大小和字节顺序） |
| `l` | 有符号长整型（始终为 32 位，机器字节顺序） |
| `L` | 无符号长整型（始终为 32 位，机器字节顺序） |
| `N` | 无符号长整型（始终为 32 位，大端字节顺序） |
| `V` | 无符号长整型（始终为 32 位，小端字节顺序） |
| `q` | 有符号长长整型（始终为 64 位，机器字节顺序） |
| `Q` | 无符号长长整型（始终为 64 位，机器字节顺序） |
| `J` | 无符号长长整型（始终为 64 位，大端字节顺序） |
| `P` | 无符号长长整型（始终为 64 位，小端字节顺序） |
| `f` | 浮点数（机器相关大小和表示） |
| `g` | 浮点数（机器相关大小，小端字节顺序） |
| `G` | 浮点数（机器相关大小，大端字节顺序） |
| `d` | 双精度浮点数（机器相关大小和表示） |
| `e` | 双精度浮点数（机器相关大小，小端字节顺序） |
| `E` | 双精度浮点数（机器相关大小，大端字节顺序） |
| `x` | 空字节 |
| `X` | 向后移动一个字节 |
| `Z` | 空字节填充字符串 |
| `@` | 到绝对位置的空字节填充 |

在定义格式字符串时，可以单独指定每个字节类型，或者利用可选的重复字符。在解决方案示例中，通过整数明确指定字节数。您也可以轻松地使用星号（`*`）来指定字节类型重复到字符串末尾，如下所示：

```php
$unpacked = unpack('S*', 'Hi'); // [1 => 26952]
$packed = pack('S*', 72, 101, 108, 108, 111, 44, 32, 119, 111,
               114, 108, 100, 33); // 'Hello, world!'
```

PHP 通过`unpack()`能够简单地在不同的字节编码类型之间进行转换，也提供了一种将 ASCII 字符与它们的二进制等效物进行转换的简单方法。`ord()`函数将返回特定字符的值，但如果要依次解包每个字符，则需要循环遍历字符串，正如在示例 4-22 中演示的那样。

##### 示例 4-22\. 使用`ord()`检索字符值

```php
$ascii = 'PHP Cookbook';

$chars = [];
for ($i = 0; $i < strlen($ascii); $i++) {
    $chars[] = ord($ascii[$i]);
}

var_dump($chars);
```

由于`unpack()`的帮助，您不需要显式地迭代字符串中的每个字符。`c`格式字符引用有符号字符，`C`引用无符号字符。您可以直接利用`unpack()`来获得等效结果，而无需构建循环，如下所示：

```php
$ascii = 'PHP Cookbook';
$chars = unpack('C*', $ascii);

var_dump($chars);
```

先前的`unpack()`示例以及示例 4-22 中的原始循环实现都产生以下数组：

```php
array(12) {
  [1]=>
  int(80)
  [2]=>
  int(72)
  [3]=>
  int(80)
  [4]=>
  int(32)
  [5]=>
  int(67)
  [6]=>
  int(111)
  [7]=>
  int(111)
  [8]=>
  int(107)
  [9]=>
  int(98)
  [10]=>
  int(111)
  [11]=>
  int(111)
  [12]=>
  int(107)
}
```

## 参见

PHP 文档关于[`pack()`](https://oreil.ly/0iieT)和[`unpack()`](https://oreil.ly/Un_aD)。

¹ 此字符串是“Hello World!”的八进制表示形式的字节表示。

² 详见表 4-1 了解更多双字符转义序列。
