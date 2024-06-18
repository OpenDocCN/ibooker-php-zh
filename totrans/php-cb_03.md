# 第三章。函数

每种语言中的每个计算机程序都是通过将各种业务逻辑组件绑定在一起来构建的。通常，这些组件需要有些可重用性，封装常见功能，这些功能需要在应用程序的多个地方引用。将这些组件的业务逻辑封装到*函数*中是使其模块化和可重用的最简单方式，函数是应用程序中的特定构造，可以在其他地方引用。

示例 3-1 说明了如何编写一个简单的程序来将字符串的第一个字符大写。编写不使用函数的代码被视为*命令式*编程，因为您逐行定义程序需要完成的确切任务（或代码行）。

##### 示例 3-1。命令式（无函数）字符串大写

```php
$str = "this is an example";

if (ord($str[0]) >= 97 && ord($str[0]) <= 122) {
    $str[0] = chr(ord($str[0]) - 32);
}

echo $str . PHP_EOL; // This is an example

$str = "and this is another";

if (ord($str[0]) >= 97 && ord($str[0]) <= 122) {
    $str[0] = chr(ord($str[0]) - 32);
}

echo $str . PHP_EOL; // And this is another

$str = "3 examples in total";

if (ord($str[0]) >= 97 && ord($str[0]) <= 122) {
    $str[0] = chr(ord($str[0]) - 32);
}

echo $str . PHP_EOL; // 3 examples in total
```

###### 注意

函数`ord()`和`chr()`是对 PHP 本身定义的本地函数的引用。[`ord()`](https://oreil.ly/kSI-4)函数将字符的二进制值作为整数返回。类似地，[`chr()`](https://oreil.ly/0KUmf)将二进制值（表示为整数）转换为其对应的字符。

当您编写没有定义函数的代码时，由于必须在应用程序中复制和粘贴相同的代码块，您的代码会变得非常重复。这违反了软件开发的关键原则之一：*DRY*，即*不要重复自己*。

描述此原则的*相反*方式是*WET*，或*写两次一切*。重复编写相同的代码块会导致两个问题：

+   您的代码变得相当冗长且难以维护。

+   如果重复代码块内的逻辑需要更改，则必须每次更新*多个*程序部分。

与以命令式方式重复逻辑不同，如示例 3-1，您可以定义一个包装此逻辑的函数，并直接调用该函数，如示例 3-2。定义函数是从命令式到过程式编程的演变，通过这种方式增强语言本身提供的函数与应用程序定义的函数。

##### 示例 3-2。过程式字符串大写

```php
function capitalize_string($str)
{
    if (ord($str[0]) >= 97 && ord($str[0]) <= 122) {
        $str[0] = chr(ord($str[0]) - 32);
    }

    return $str;
}

$str = "this is an example";

echo capitalize_string($str) . PHP_EOL; // This is an example

$str = "and this is another";

echo capitalize_string($str) . PHP_EOL; // And this is another

$str = "3 examples in total";

echo capitalize_string($str) . PHP_EOL; // 3 examples in total
```

用户定义的函数非常强大且非常灵活。在示例 3-2 中的`capitalize_string()`函数相对简单——它接受一个字符串参数并返回一个字符串。但在函数定义中没有指示`$str`参数必须是一个字符串——您可以很容易地传递一个数字甚至一个数组，如下所示：

```php
$out = capitalize_string(25); // 25

$out = capitalize_string(['a', 'b']); // ['A', 'b']
```

回想一下，来自第一章关于 PHP 宽松类型系统的讨论--默认情况下，当你将参数传递给`capitalize_string()`时，PHP 会尝试推断你的意图，并且在大多数情况下返回一些有用的东西。如果传递一个整数，在访问数组元素时，PHP 将触发警告，表示你正在错误地访问数组元素，但仍然会返回一个整数而不会崩溃。

更复杂的程序可以在函数参数和返回类型中添加显式类型信息，以便对这种用法进行安全检查。其他函数可以返回*多个*值而不是单个项。强类型在食谱 3.4 中明确说明。

接下来的示例涵盖了 PHP 中函数的多种使用方式，并开始探讨构建完整应用程序的基础。

# 3.1 访问函数参数

## 问题

当在程序的其他地方调用函数时，你想要访问传递给函数的值。

## 解决方案

在函数体内可以使用函数签名中定义的变量名来使用函数签名中定义的变量名如下：

```php
function multiply($first, $second)
{
    return $first * $second;
}

multiply(5, 2); // 10

$one = 7;
$two = 5;

multiply($one, $two); // 35
```

## 讨论

函数签名中定义的变量名仅在函数本身的作用域内有效，并将包含与调用函数时传入的数据匹配的值。在定义函数的花括号内部，你可以像自己定义它们一样使用这些变量。只需知道，对这些变量进行的任何更改将*仅*在函数内部可用，并且默认情况下不会影响应用程序中的其他任何内容。

示例 3-3 说明了如何在函数内部和外部同时使用特定变量名，同时引用两个完全独立的值。换句话说，更改函数内部`$number`的值只会影响函数内部的值，而不会影响父应用程序中的值。

##### 示例 3-3\. 本地函数作用域

```php
function increment($number)
{
    $number += 1;

    return $number;
}

$number = 6;

echo increment($number); // 7
echo $number; // 6
```

默认情况下，PHP 将值传递到函数中，而不是传递变量的引用。在示例 3-3 中，这意味着 PHP 将值`6`传递给函数内部的新变量`$number`，执行计算并返回结果。函数外部的`$number`变量完全不受影响。

###### 警告

PHP 默认按值传递简单值（字符串、整数、布尔值、数组）。然而，更复杂的对象*总是*按引用传递。对于对象，函数内部的变量指向与函数外部变量相同的对象，而不是它的副本。

在某些情况下，您可能希望显式地通过引用传递变量，而不仅仅传递其值。 在这种情况下，您需要修改函数签名，因为这是对其定义的更改，而不是在调用函数时可以修改的内容。 示例 3-4 说明了如何修改`increment()`函数，以通过引用而不是值传递`$number`。

##### 示例 3-4\. 通过引用传递变量

```php
function increment(&$number)
{
    $number += 1;

    return $number;
}

$number = 6;

echo increment($number); // 7
echo $number; // 7
```

实际上，在函数内外，变量名称不需要完全匹配。 我在这里两种情况下都使用`$number`来说明作用域的差异。 如果在`$a`中存储了一个整数并将该变量作为`increment($a)`传递，则结果与示例 3-4 中的结果相同。

## 参见

PHP 参考文档上的[用户定义函数](https://oreil.ly/9c1Nr)和[通过引用传递变量](https://oreil.ly/ZfOLR)。

# 3.2 设置函数的默认参数

## 问题

您希望为函数的参数设置默认值，以便调用无需传递它。

## 解决方案

在函数签名内部分配默认值。例如：

```php
function get_book_title($isbn, $error = 'Unable to query')
{
    try {
        $connection = get_database_connection();
        $book = query_isbn($connection, $isbn);

        return $book->title;
    } catch {
        return $error;
    }
}

get_book_title('978-1-098-12132-7');
```

## 讨论

解决方案中的示例尝试基于其 ISBN 查询书籍的标题。 如果由于任何原因查询失败，则函数将返回传递给`$error`参数的字符串。

为了使该参数可选，函数签名分配了一个默认值。 当使用单个参数调用`get_book_title()`时，将自动使用默认的`$error`值。 您还可以选择在调用函数时将自己的字符串传递给此变量，例如`get_book_title​(*978-1-098-12132-7*, *Oops!*);`。

在定义具有默认参数的函数时，最佳做法是在函数签名中将所有具有默认值的参数放在*最后*。 虽然可以按任何顺序定义参数，但这样做会使正确调用函数变得困难。

示例 3-5 说明了在必填参数之后放置可选参数可能会出现的问题类型。

###### 警告

可以按任意顺序定义具有特定默认值的函数参数。 但是，自 PHP 8.0 起，声明必填参数在可选参数之后已弃用。 继续这样做可能会导致未来版本的 PHP 中出现错误。

##### 示例 3-5\. 错误的默认参数顺序

```php
function brew_latte($flavor = 'unflavored', $shots)
{
    return "Brewing a {$shots}-shot, {$flavor} latte!";
}

brew_latte('vanilla', 2); ![1](img/1.png)
brew_latte(3); ![2](img/2.png)
```

![1](img/#co_functions_CO1-1)

适当执行。 返回`Brewing a 2-shot, vanilla latte!`

![2](img/#co_functions_CO1-2)

引发`ArgumentCountError`异常，因为`$shots`未定义。

在某些情况下，将参数按特定顺序放置可能更符合逻辑（例如使代码更易读）。请注意，如果需要任何参数，则它们左侧的每个参数即使尝试定义默认值也是必需的。

## 参见

在[PHP 手册](https://oreil.ly/XVoK1)中有默认参数的示例。

# 3.3 使用命名函数参数

## 问题

您希望根据参数的名称而不是位置将参数传递给函数。

## 解决方案

在调用函数时，请使用命名参数语法如下：

```php
array_fill(start_index: 0, count: 100, value: 50);
```

## 讨论

默认情况下，PHP 在函数定义中使用位置参数。解决方案示例引用了原生的[`array_fill()`](https://oreil.ly/jdZQH)函数，具有以下函数签名：

```php
array_fill(int $start_index, int $count, mixed $value): array
```

基本的 PHP 编码必须按照定义的顺序向`array_fill()`提供参数——先是`$start_index`，然后是`$count`，最后是`$value`。顺序本身并不是问题，但是在通过代码进行视觉扫描时理解每个值的含义可能会有挑战。使用基本的有序参数，解决方案示例将写成以下形式，需要对函数签名深入了解，以知道哪个整数代表哪个参数：

```php
array_fill(0, 100, 50);
```

命名函数参数可以消除对内部变量分配的歧义。当您调用函数时，命名函数参数还允许任意重新排序参数。

命名参数的另一个关键优势是在函数调用时可以完全跳过可选参数。考虑一个冗长的活动日志函数，例如示例 3-6，其中多个参数被视为可选参数，因为它们设置了默认值。

##### 示例 3-6\. 冗长的活动日志函数

```php
activity_log(
    string    $update_reason,
    string    $note           = '',
    string    $sql_statement  = '',
    string    $user_name      = 'anonymous',
    string    $ip_address     = '127.0.0.1',
    ?DateTime $time           = null
): void
```

在内部，示例 3-6 将在仅使用一个参数调用时使用其默认值；如果`$time`为`null`，则该值将被默默替换为代表“现在”的新`DateTime`实例。然而，有时您可能希望填充其中一个可选参数，而不希望显式设置*所有*可选参数。

假设您想要从静态日志文件中重新播放先前观察到的事件。用户活动是匿名的（因此`$user_name`和`$ip_address`的默认值是足够的），但您需要显式设置事件发生的日期。没有命名参数的情况下，此类调用看起来类似于示例 3-7。

##### 示例 3-7\. 调用冗长的`activity_log()`函数

```php
activity_log(
    'Testing a new system',
    '',
    '',
    'anonymous',
    '127.0.0.1',
    new DateTime('2021-12-20')
);
```

使用命名参数，您可以跳过设置参数为其默认值，并仅显式设置您需要的参数。前面的代码可以简化为以下形式：

```php
activity_log('Testing a new system', time: new DateTime('2021-12-20'));
```

除了极大地简化`activity_log()`的使用之外，命名参数还有一个额外的好处，即保持代码 DRY。参数的默认值直接存储在函数定义中，而不是在每次调用函数时都复制一遍。如果以后需要更改默认值，只需编辑函数定义即可。

## 另请参阅

最初的 RFC[提出命名参数](https://oreil.ly/UdoDP)。

# 3.4 强制函数参数和返回类型

## 问题

您希望强制程序实现类型安全性，并避免 PHP 的本机松散类型比较。

## 解决方案

为函数定义添加输入和返回类型。可选地，在每个文件的顶部添加严格的类型声明，以强制值匹配类型注解（如果不匹配则发出致命错误）。例如：

```php
declare(strict_types=1);

function add_numbers(int $left, int $right): int
{
    return $left + $right;
}

add_numbers(2, 3); ![1](img/1.png)
add_numbers(2, '3'); ![2](img/2.png)
```

![1](img/#co_functions_CO2-1)

这是一个完全有效的操作，将返回整数`5`。

![2](img/#co_functions_CO2-2)

虽然`2 + '3'`是有效的 PHP 代码，但字符串`'3'`违反了函数的类型定义，将触发致命错误。

## 讨论

PHP 本身支持多种标量类型，并允许开发者声明函数的输入参数和返回值，以确定每种值的允许性。此外，开发者还可以将自定义类和接口作为类型，或在类型系统中利用类继承。¹

在定义函数时，通过在参数名称前直接注释类型来标注参数类型。类似地，通过在函数签名后附加`:`和函数可能返回的类型来指定返回类型，如下所示：

```php
function name(type $parameter): return_type
{
    // ...
}
```

表 3-1 列举了 PHP 利用的最简单类型。

表 3-1\. PHP 中的简单单类型

| 类型 | 描述 |
| --- | --- |
| `array` | 值必须是一个数组（包含任何类型的值）。 |
| `callable` | 值必须是可调用函数。 |
| `bool` | 值必须是布尔值。 |
| `float` | 值必须是浮点数。 |
| `int` | 值必须是整数。 |
| `string` | 值必须是一个字符串。 |
| [`iterable`](https://oreil.ly/tiTI1) | 值必须是一个数组或实现了`Traversable`接口的对象。 |
| [`mixed`](https://oreil.ly/V8VOc) | 对象可以是任意值。 |
| [`void`](https://oreil.ly/Izmvp) | 表示函数不返回任何值。 |
| [`never`](https://oreil.ly/48KVB) | 表示函数不返回任何值；它要么调用`exit`，抛出异常，或故意是一个无限循环。 |

此外，无论是内置还是自定义类都可以用来定义类型，如表 3-2 所示。

表 3-2\. PHP 中的对象类型

| 类型 | 描述 |
| --- | --- |
| 类/接口名称 | 值必须是指定类的实例或接口的实现。 |
| `self` | 值必须是与声明使用的同一类的实例。 |
| `parent` | 值必须是在声明使用的类的父类的实例。 |
| `object` | 值必须是一个对象的实例。 |

PHP 还允许将简单标量类型扩展为可为空，或者将它们组合为*联合类型*。要使特定类型可为空，必须在类型注释前面加上 `?`。这将指示编译器允许值为指定类型或`null`，例如在 示例 3-8 中。

##### 示例 3-8\. 使用可空参数的函数

```php
function say_hello(?string $message): void
{
    echo 'Hello, ';

    if ($message === null) {
        echo 'world!';
    } else {
        echo $message . '!';
    }
}

say_hello('Reader'); // Hello, Reader!
say_hello(null); // Hello, world!
```

联合类型声明通过使用管道字符(`|`)将多个类型组合成单个声明。如果您在 Solution 示例中重新编写类型声明，将字符串和整数组合成联合类型，通过传递字符串进行加法操作时引发的致命错误将解决。考虑在 示例 3-9 中可能的重写，允许*整数或字符串*作为参数。

##### 示例 3-9\. 重新编写 Solution 示例以利用联合类型

```php
function add_numbers(int|string $left, int|string $right): int
{
    return $left + $right;
}

add_numbers(2, '3'); // 5
```

这种替代方案的最大问题是，使用 `+` 运算符将字符串连接在一起在 PHP 中没有意义。如果两个参数都是数字（整数或表示为字符串的整数），则函数将正常工作。如果其中一个是非数字字符串，PHP 将抛出 `TypeError`，因为它不知道如何将两个字符串“加”在一起。通过为代码添加类型声明并强制执行严格类型化，可以避免这类错误。类型声明形式化了您希望代码支持的契约，并鼓励自然防御编码错误的编程实践。

默认情况下，PHP 使用其类型系统来*提示*哪些类型允许进入函数并从函数返回。这对于防止将坏数据传递给函数很有用，但它在很大程度上依赖于开发人员的勤奋或额外的工具²来强制执行类型。与依赖于人类检查代码不同，PHP 允许在每个文件中静态声明，以便所有调用都遵循严格的类型化。

将 `declare(strict_types=1);` 放置在文件顶部告诉 PHP 编译器，您打算让该文件中的所有调用遵守参数和返回类型声明。请注意，此指令适用于文件内部的调用，而不适用于该文件中函数的定义。如果从另一个文件调用函数，PHP 也会遵守该文件中的类型声明。然而，将此指令放置在您的文件中不会强制要求引用您的函数的其他文件遵循类型系统。

## 参见

PHP 关于 [类型声明](https://oreil.ly/I9D33) 和 [`declare` 结构](https://oreil.ly/P2jM_) 的文档。

# 3.5 定义具有可变数量参数的函数

## 问题

您想要定义一个函数，该函数接受一个或多个参数，而不知道预先传入多少个值。

## 解决方案

使用 PHP 的扩展操作符(`…​`)来定义可变数量的参数：

```php
function greatest(int ...$numbers): int
{
    $greatest = 0;
    foreach ($numbers as $number) {
        if ($number > $greatest) {
            $greatest = $number;
        }
    }

    return $greatest;
}

greatest(7, 5, 12, 2, 99, 1, 415, 3, 7, 4);
// 415
```

## 讨论

*展开运算符*会自动将传递给特定位置或之后的所有参数添加到一个数组中。可以通过在展开运算符前面添加类型声明来为此数组指定类型（详见示例 3.4 了解更多关于类型的内容），因此需要确保数组的每个元素都与特定类型匹配。调用解决方案示例中定义的函数如 `greatest(2, "five");` 将抛出 `TypeError`，因为您已经明确声明了 `$numbers` 数组的每个成员为 `int` 类型。

您的函数可以接受多个位置参数，同时利用展开运算符接受无限数量的额外参数。在示例 3-10 中定义的函数将向屏幕上无限数量的个人打印问候语。

##### 示例 3-10\. 利用展开运算符

```php
function greet(string $greeting, string ...$names): void
{
    foreach($names as $name) {
        echo $greeting . ', ' . $name . PHP_EOL;
    }
}

greet('Hello', 'Tony', 'Steve', 'Wanda', 'Peter');
// Hello, Tony
// Hello, Steve
// Hello, Wanda
// Hello, Peter

greet('Welcome', 'Alice', 'Bob');
// Welcome, Alice
// Welcome, Bob
```

展开运算符不仅在函数定义时更有用。虽然它可以用于将多个参数打包到一个数组中，但也可以用于将数组解包为多个参数，以便进行更传统的函数调用。示例 3-11 通过使用展开运算符将数组传递给一个不接受数组的函数提供了展示如何工作的简单示例。

##### 示例 3-11\. 使用展开运算符解包数组

```php
function greet(string $greeting, string $name): void
{
    echo $greeting . ', ' . $name  . PHP_EOL;
}

$params = ['Hello', 'world'];
greet(...$params);
// Hello, world
```

在某些情况下，更复杂的函数可能会返回多个值（如下一篇章节所讨论的），因此将一个函数的返回值传递给另一个函数在使用展开运算符时变得非常简单。事实上，任何实现 PHP 的[可遍历](https://oreil.ly/jVUvs)接口的数组或变量都可以以这种方式解包到函数调用中。

## 参见

PHP 关于[可变长度参数列表](https://oreil.ly/9IoHh)的文档。

# 3.6 返回多个值

## 问题

您希望从单个函数调用中返回多个值。

## 解决方案

而不是返回单个值，可以通过在函数外部使用 `list()` 来返回多个值的数组：

```php
function describe(float ...$values): array
{
    $min = min($values);
    $max = max($values);
    $mean = array_sum($values) / count($values);

    $variance = 0.0;
    foreach($values as $val) {
        $variance += pow(($val - $mean), 2);
    }
    $std_dev = (float) sqrt($variance/count($values));

    return [$min, $max, $mean, $std_dev];
}

$values = [1.0, 9.2, 7.3, 12.0];
list($min, $max, $mean, $std) = describe(...$values);
```

## 讨论

PHP 只能从函数调用中返回一个值，但该值本身可以是包含多个值的数组。与 PHP 的 `list()` 结构配对时，可以轻松地将此数组解构为单独的变量，以供程序进一步使用。

尽管需要返回许多不同的值并不常见，但在需要时能够这样做确实非常方便。一个例子是在 Web 身份验证中。许多现代系统今天使用 JSON Web Tokens（JWT），这些是以 Base64 编码的数据的期限分隔字符串。JWT 的每个组件代表一个单独的离散事物：描述所使用算法的标头，令牌有效负载中的数据以及该数据上的可验证签名。

在将 JWT 作为字符串读取时，PHP 应用程序通常利用内置的`explode()`函数来在每个组件的句点上拆分字符串。简单使用`explode()`可能如下所示：

```php
$jwt_parts = explode('.', $jwt);
$header = base64_decode($jwt_parts[0]);
$payload = base64_decode($jwt_parts[1]);
$signature = base64_decode($jwt_parts[2]);
```

前述代码可以正常工作，但数组内的重复引用位置在开发期间和后期调试时可能难以跟踪。此外，开发人员必须手动分别解码 JWT 的每个部分；忘记调用`base64_decode()`可能对程序的运行造成致命影响。

另一种方法是在函数内部解包并自动解码 JWT，并以数组形式返回各个组件，如示例 3-12 所示。

##### 示例 3-12\. 解码 JWT

```php
function decode_jwt(string $jwt): array
{
    $parts = explode('.', $jwt);

    return array_map('base64_decode', $parts);
}

list($header, $payload, $signature) = decode_jwt($jwt);
```

使用函数解包 JWT 而不是直接分解每个元素的另一个优点是，你可以在其中构建自动签名验证或甚至根据头部声明的加密算法过滤 JWT。虽然这种逻辑在处理 JWT 时可以被逐步应用，但将所有内容保持在单个函数定义中会导致更清晰、更易维护的代码。

在一个函数调用中返回多个值的最大缺点在于类型。这些函数具有`array`返回类型，但 PHP 本身不允许指定数组中元素的类型。我们有潜在的解决方法来解决这个限制，比如文档化函数签名并集成静态分析工具如[Psalm](https://psalm.dev)或[PHPStan](https://phpstan.org)，但语言本身不支持数组类型。因此，如果你在使用严格类型（你*应该*使用），从单个函数调用返回多个值应该是一个少见的情况。

## 参见

食谱 3.5 关于传递可变数量的参数和食谱 1.3 更多关于 PHP 的`list()`构造的信息。还可以参考像[phpDocumentor 关于类型数组的文档](https://oreil.ly/RsXGh)，可以通过 Psalm 等工具强制执行。

# 3.7 从函数内访问全局变量

## 问题

你的函数需要引用应用程序其他地方定义的全局变量。

## 解决方案

在函数的范围内使用`global`关键字前缀来访问任何全局变量：

```php
$counter = 0;

function increment_counter()
{
    global $counter;

    $counter += 1;
}

increment_counter();

echo $counter; // 1
```

## 讨论

PHP 根据变量定义的上下文将操作分为不同的范围。对于大多数程序，单个范围覆盖所有已包含或所需的文件。定义在全局范围内的变量在*任何地方*都可用，无论当前执行的是哪个文件，如示例 3-13 中所示。

##### 示例 3-13\. 在全局范围内定义的变量可供包含的脚本使用

```php
$apple = 'honeycrisp';

include 'someotherscript.php'; ![1](img/1.png)
```

![1](img/#co_functions_CO3-1)

`$apple`变量也在此脚本中定义并可供使用。

然而，用户定义的函数定义了它们自己的作用域。在用户定义的函数外定义的变量*不可用*于函数体内。同样，函数内定义的任何变量在函数外部也是不可用的。示例 3-14 说明了程序中父作用域和函数作用域的边界。

##### 示例 3-14\. 本地与全局作用域

```php
$a = 1; ![1](img/1.png)

function example(): void
{
    echo $a . PHP_EOL; ![2](img/2.png)
    $a = 2; ![3](img/3.png)

    $b = 3; ![4](img/4.png)
}

example();

echo $a . PHP_EOL; ![5](img/5.png)
echo $b . PHP_EOL; ![6](img/6.png)
```

![1](img/#co_functions_CO4-1)

变量`$a`最初是在父作用域中定义的。

![2](img/#co_functions_CO4-2)

在函数作用域内，`$a`尚未定义。尝试`echo`其值将导致警告。

![3](img/#co_functions_CO4-3)

在函数内定义名为`$a`的变量将*不会*覆盖函数外部相同名称变量的值。

![4](img/#co_functions_CO4-4)

在函数中定义一个名为`$b`的变量，使其在函数内可用，但此值不会逃逸函数的作用域。

![5](img/#co_functions_CO4-5)

即使在调用`example()`后，在函数外部也会打印你设置的初始值的`$a`。

![6](img/#co_functions_CO4-6)

由于`$b`是在函数内定义的，因此在父应用程序的作用域中未定义。

###### 注意

如果函数定义为接受这种方式的变量，那么可以通过引用将变量传递给函数调用。然而，这是函数定义的决定，而不是在调用该函数后可用于利用该函数的程序的运行时标志。示例 3-4 展示了传递引用可能的效果。

要引用在其作用域外定义的变量，函数需要在其自身作用域内将这些变量声明为*全局*变量。要引用父作用域，可以将示例 3-14 重写为示例 3-15。

##### 示例 3-15\. 本地与全局作用域的再访问

```php
$a = 1;

function example(): void
{
    global $a, $b; ![1](img/1.png)

    echo $a . PHP_EOL; ![2](img/2.png)
    $a = 2; ![3](img/3.png)

    $b = 3; ![4](img/4.png)
}

example();

echo $a . PHP_EOL; ![5](img/5.png)
echo $b . PHP_EOL; ![6](img/6.png)
```

![1](img/#co_functions_CO5-1)

声明`$a`和`$b`为全局变量后，函数将使用父作用域的值而不是自己的作用域。

![2](img/#co_functions_CO5-2)

通过对*全局*变量`$a`的引用，你现在可以将其打印到输出中。

![3](img/#co_functions_CO5-3)

同样，在函数作用域内对`$a`的任何更改都将影响父作用域中的变量。

![4](img/#co_functions_CO5-4)

类似地，你现在定义了`$b`，但由于它是全局的，此定义也将向上冒泡到父作用域中。

![5](img/#co_functions_CO5-5)

现在，`echo $a`将反映在`example()`作用域内所做的更改，因为你将变量设置为全局的。

![6](img/#co_functions_CO5-6)

同样，`$b`现在已全局定义，并且也可以打印到输出中。

除了系统可用的内存外，PHP 可以支持的全局变量数量没有限制。此外，可以通过枚举 PHP 定义的特殊$GLOBALS 数组来列出所有全局变量。这个关联数组对于想要在全局范围内引用特定变量而不声明为全局变量的情况非常有用，例如例子 3-16。

##### 例子 3-16\. 使用关联的$GLOBALS 数组

```php
$var = 'global';

function example(): void
{
    $var = 'local';

    echo 'Local variable: ' . $var . PHP_EOL;
    echo 'Global variable: ' . $GLOBALS['var'] . PHP_EOL;
}

example();
// Local variable: local
// Global variable: global
```

###### 警告

从 PHP 8.1 开始，不再可能完全覆盖$GLOBALS 数组。在以前的版本中，你可以将其重置为空数组（例如，在代码的测试运行期间）。从现在开始，你只能编辑数组的内容，而不能再整体操作集合了。

全局变量是在应用程序中引用状态的方便方式，但如果过度使用可能会导致混乱和可维护性问题。一些大型应用程序大量使用全局变量——WordPress 是一个基于 PHP 的项目，驱动着超过 40%的互联网[³]，[在其代码库中广泛使用全局变量](https://oreil.ly/jztni)。然而，大多数应用程序开发人员都同意，应尽可能少地使用全局变量，以帮助保持系统的清洁和易维护性。

## 参见

PHP 文档中关于[变量作用域](https://oreil.ly/tN5tV)和特殊的[$GLOBALS](https://oreil.ly/z9JJS)数组。

# 3.8 在多次调用之间管理函数内部的状态

## 问题

你的函数需要随着时间的推移跟踪其状态变化。

## 解决方案

使用`static`关键字定义一个在函数调用之间保持状态的本地作用域变量：

```php
function increment()
{
    static $count = 0;

    return $count++;
}

echo increment(); // 0
echo increment(); // 1
echo increment(); // 2
```

## 讨论

静态变量仅存在于其声明的函数作用域内。然而，与普通的局部变量不同的是，它在每次返回函数作用域时保留其值。通过这种方式，函数可以变得*有状态*，并在独立调用之间跟踪某些数据（如被调用的次数）。

在典型的函数中，使用`=`运算符将值分配给变量。当应用`static`关键字时，此赋值操作仅在首次调用该函数时发生。后续调用将引用变量的先前状态，并允许程序使用或修改存储的值。

静态变量的最常见用例之一是跟踪递归函数的状态。例子 3-17 展示了一个在退出之前递归调用自身固定次数的函数。

##### 例子 3-17\. 使用静态变量限制递归深度

```php
function example(): void
{
    static $count = 0;

    if ($count >= 3) {
        $count = 0;
        return;
    }

    $count += 1;

    echo 'Running for loop number ' . $count . PHP_EOL;
    example();
}
```

`static` 关键字还可以用于跟踪可能需要多次函数调用但您可能只想要单个实例的昂贵资源。考虑一个将消息记录到数据库的函数：您可能无法将数据库连接传递给函数本身，但希望确保该函数只打开一个*单一*数据库连接。这样的记录函数可以实现如 示例 3-18 中所示。

##### 示例 3-18\. 使用静态变量保存数据库连接

```php
function logger(string $message): void
{
    static $dbh = null;
    if ($dbh === null) {
        $dbh = new PDO(DATABASE_DSN, DATABASE_USER, DATABASE_PASSWORD);
    }

    $sql = 'INSERT INTO messages (message) VALUES (:message)';
    $statement = $dbh->prepare($sql);

    $statement->execute([':message', $message]);
}

logger('This is a test'); ![1](img/1.png)
logger('This is another message');![2](img/2.png)
```

![1](img/#co_functions_CO6-1)

第一次调用 `logger()` 时，它将定义静态变量 `$dbh` 的值。在此情况下，它将通过[PHP 数据对象（PDO）](https://oreil.ly/do1eJ)接口连接到数据库。此接口是 PHP 提供的用于访问数据库的标准对象。

![2](img/#co_functions_CO6-2)

每次调用 `logger()` 都将利用存储在 `$dbh` 中的初始数据库连接。

请注意，PHP 自动管理其内存使用情况，并在变量离开作用域时从内存中自动清除变量。对于函数内的常规变量，这意味着变量在函数完成后从内存中释放。静态和全局变量*永远*不会在程序本身退出之前清除，因为它们始终在作用域中。在使用 `static` 关键字时要小心，确保不会在内存中存储不必要的大数据块。在 示例 3-18 中，您打开一个连接到数据库的连接，该连接永远不会被您创建的函数自动关闭。

虽然 `static` 关键字可以是在函数调用之间重用状态的强大方式，但应谨慎使用以确保您的应用程序不会执行意外操作。在许多情况下，明确传递表示状态的变量到函数中可能更好。更好的方法是将函数的状态封装为一个全局对象的一部分，这在第八章中有详细介绍。

## 参见

PHP 文档关于[变量作用域，包括 `static` 关键字](https://oreil.ly/-yflc)。

# 3.9 定义动态函数

## 问题

您希望定义一个匿名函数，并将其作为变量引用到应用程序中，因为您只想使用或调用该函数一次。

## 解决方案

定义一个可以分配给变量并根据需要传递到另一个函数中的闭包：

```php
$greet = function($name) {
    echo 'Hello, ' . $name . PHP_EOL;
};

$greet('World!');
// Hello, World!
```

## 讨论

虽然 PHP 中的大多数函数都有定义的名称，该语言支持创建无名（所谓的*匿名*）函数，也称为*闭包*或*lambda*。这些函数可以封装简单或复杂的逻辑，并可以直接分配给变量以供程序中其他地方引用。

在内部，匿名函数使用 PHP 的原生[`Closure`](https://oreil.ly/u5qt7)类实现。该类声明为`final`，这意味着没有类可以直接扩展它。然而，匿名函数都是这个类的实例，可以直接用作函数或作为对象使用。

默认情况下，闭包不会继承任何父应用程序的作用域，并且像普通函数一样，在其自己的作用域内定义变量。可以通过在定义函数时利用`use`指令，直接将父作用域的变量传递给闭包。示例 3-19 演示了如何动态地将一个作用域的变量传递到另一个作用域中。

##### 示例 3-19\. 使用`use()`在不同作用域之间传递变量

```php
$some_value = 42;

$foo = function() {
    echo $some_value;
};

$bar = function() use ($some_value) {
    echo $some_value;
};

$foo(); // Warning: Undefined variable

$bar(); // 42
```

匿名函数用于许多项目中，以封装应用于数据集合的逻辑片段。下一个示例正好涵盖了这种用例。

###### 注意

PHP 的旧版本使用[`create_function()`](https://oreil.ly/RRMgO)实现类似的效果。开发人员可以将匿名函数作为字符串创建，并将该代码传递给`create_function()`，将其转换为闭包实例。不幸的是，这种方法在底层使用了`eval()`来评估字符串，这种做法被认为是非常不安全的。虽然一些旧项目可能仍在使用`create_function()`，但该函数在 PHP 7.2 中已被弃用，并在版本 8.0 中从语言中完全删除。

## 参见

PHP 文档中关于[匿名函数](https://oreil.ly/W0QPL)的说明。

# 3.10 将函数作为参数传递给其他函数

## 问题

您希望定义函数实现的一部分，并将该实现作为参数传递给另一个函数。

## 解决方案

定义一个实现所需逻辑部分的闭包，并将其直接传递到另一个函数中，就像任何其他变量一样：

```php
$reducer = function(?int $carry, int $item): int {
    return $carry + $item;
};

function reduce(array $array, callable $callback, ?int $initial = null): ?int
{
    $acc = $initial;
    foreach ($array as $item) {
        $acc = $callback($acc, $item);
    }

    return $acc;
}

$list = [1, 2, 3, 4, 5];
$sum = reduce($list, $reducer); // 15
```

## 讨论

许多认为 PHP 是*功能型语言*，因为函数在语言中是一级元素，可以绑定到变量名，作为参数传递，甚至从其他函数中返回。PHP 通过语言中实现的[callable](https://oreil.ly/m7skJ)类型支持函数作为变量。许多核心函数（如`usort()`、`array_map()`和`array_reduce()`）支持传递可调用参数，内部使用它来定义函数的整体实现。

解决方案示例中定义的`reduce()`函数是 PHP 本地`array_reduce()`函数的用户自定义实现。两者行为相同，解决方案可以重写，直接将`$reducer`传递到 PHP 本地实现中，结果不变：

```php
$sum = array_reduce($list, $reducer); // 15
```

由于函数可以像任何其他变量一样传递，PHP 可以定义函数的部分实现。通过定义一个函数，然后返回另一个函数，可以在程序的其他地方使用它。

例如，您可以定义一个函数来设置一个基本的乘法器例程，该例程将任意输入乘以一个*固定*基数，就像示例 3-20 中那样。主函数每次调用都返回一个新函数，因此您可以创建加倍或三倍任意值的函数，并根据需要使用它们。

##### 示例 3-20\. 部分应用的乘法器函数

```php
function multiplier(int $base): callable
{
    return function(int $subject) use ($base): int {
        return $base * $subject;
    };
}

$double = multiplier(2);
$triple = multiplier(3);

$double(6);  // 12
$double(10); // 20
$triple(3);  // 9
$triple(12); // 36
```

将函数分解成这样的形式称为[*https://oreil.ly/-*](https://oreil.ly/-)*a4l[_currying*]。这是将具有多个输入参数的函数改为一系列每个只接受一个*单一*参数的函数，并且其中大多数参数本身也是函数的做法。为了充分说明这在 PHP 中的工作原理，让我们看看示例 3-21 并逐步重写`multiplier()`函数。

##### 示例 3-21\. PHP 中柯里化的演示

```php
function multiply(int $x, int $y): int ![1](img/1.png)
{
    return $x * $y;
}

multiply(7, 3); // 21 
function curried_multiply(int $x): callable ![2](img/2.png)
{
    return function(int $y) use ($x): int { ![3](img/3.png)
        return $x * $y; ![4](img/4.png)
    };
}

curried_multiply(7)(3); // 21 ![5](img/5.png)
```

![1](img/#co_functions_CO7-1)

这个函数的最基本形式接受两个值，将它们相乘并返回最终结果。

![2](img/#co_functions_CO7-2)

当你柯里化函数时，你希望每个组件函数只接受一个值。新的`curried_multiply()`例如，只接受一个参数，但返回一个在内部利用该参数的函数。

![3](img/#co_functions_CO7-3)

内部函数自动引用前一个函数调用传递的值（使用`use`指令）。

![4](img/#co_functions_CO7-4)

结果函数实现了与基本形式相同的业务逻辑。

![5](img/#co_functions_CO7-5)

调用柯里化函数看起来像是按顺序调用*多个*函数，但结果是相同的。

就像在示例 3-21 中的柯里化函数一样，柯里化的最大优势是部分应用的函数可以作为变量传递并在其他地方使用。类似于使用`multiplier()`函数，您可以通过以下方式*部分*应用您的柯里化乘法器来创建一个加倍或三倍的函数：

```php
$double = curried_multiply(2);
$triple = curried_multiply(3);
```

部分应用的柯里化函数本身是可调用的函数，但可以作为变量传递给其他函数，并在稍后完全调用。

## 另请参阅

有关匿名函数的详细信息，请参见 Recipe 3.9。

# 3.11 使用简洁的函数定义（箭头函数）

## 问题

您希望创建一个简单的匿名函数，它引用父作用域而不需要冗长的`use`声明。

## 解决方案

使用 PHP 的短匿名函数（箭头函数）语法自动定义一个函数，该函数自动继承其父作用域：

```php
$outer = 42;

$anon = fn($add) => $outer + $add;

$anon(5); // 47
```

## 讨论

*箭头函数*在 PHP 7.4 中作为编写更简洁匿名函数的一种方式引入，就像在 Recipe 3.9 中一样。箭头函数自动捕获任何引用的变量并（按值而非按引用）导入到函数的作用域中。

可以以更详细的方式编写解决方案示例，如示例 3-22，同时仍然实现相同级别的功能。

##### 示例 3-22\. 匿名函数的长格式

```php
$outer = 42;

$anon = function($add) use ($outer) {
    return $outer + $add;
};

$anon(5);
```

箭头函数总是返回一个值——不可能隐式或显式返回`void`。这些函数遵循非常特定的语法，并始终返回其表达式的结果：`*fn* (*arguments*) => *expression*`。这种结构使得箭头函数在各种情况下都非常有用。

一个例子是通过 PHP 的本地`array_map()`来应用于数组中所有元素的内联函数的简明定义。假设输入用户数据是表示整数值的字符串数组，并且您希望将字符串数组转换为整数数组以强制执行适当的类型安全性。这可以通过示例 3-23 轻松实现。

##### 示例 3-23\. 将数值字符串数组转换为整数数组

```php
$input = ['5', '22', '1093', '2022'];

$output = array_map(fn($x) => intval($x), $input);
// $output = [5, 22, 1093, 2022]
```

箭头函数仅允许单行表达式。如果您的逻辑复杂到需要多个表达式，请使用标准匿名函数（参见配方 3.9）或在代码中定义一个命名函数。尽管如此，箭头函数本身就是一个表达式，因此一个箭头函数实际上可以返回另一个箭头函数。

将箭头函数作为另一个箭头函数的表达式返回的能力，导致可以在*柯里化*或部分应用函数中使用箭头函数以促进代码重用。假设您希望在程序中传递一个函数，该函数使用固定的模数执行模数算术。您可以通过定义一个箭头函数来执行计算并将其包装在另一个函数中以指定模数，将最终的柯里化函数分配给可以在其他地方使用的变量，例如示例 3-24。

###### 注意

模数算术用于创建*时钟函数*，无论输入的整数是什么，它们总是返回一组特定的整数值。通过将两个整数取模，即将它们相除并返回整数余数来完成。例如，“12 模 3”写作 `12 % 3` 并返回 `12/3` 的余数，即 `0`。类似地，“15 模 6”写作 `15 % 6` 并返回 `15/6` 的余数，即 `3`。模运算的返回值永远不会大于模数本身（在前两个示例中分别为 `3` 或 `6`）。模数算术通常用于将大量输入值分组或用于支持加密操作，有关详细信息，请参阅第九章。

##### 示例 3-24\. 使用箭头函数进行函数柯里化

```php
$modulo = fn($x) => fn($y) => $y % $x;

$mod_2 = $modulo(2);
$mod_5 = $modulo(5);

$mod_2(15); // 1
$mod_2(20); // 0
$mod_5(12); // 2
$mod_5(15); // 0
```

最后，就像常规函数一样，箭头函数也可以接受多个参数。与传递单个变量（或隐式引用父作用域中定义的变量）不同，您可以轻松地定义一个具有多个参数并在表达式中自由使用它们的函数。一个简单的相等函数可以使用箭头函数如下所示：

```php
$eq = fn($x, $y) => $x == $y;

$eq(42, '42'); // true
```

## 参见

在 3.9 节匿名函数的详细信息和 PHP 手册文档中的[箭头函数](https://oreil.ly/MLURC)。

# 3.12 创建一个没有返回值的函数

## 问题

您需要定义一个函数，它在完成后不向程序的其余部分返回数据。

## 解决方案

使用显式类型声明，并引用`void`返回类型：

```php
const MAIL_SENDER = 'wizard@oz.example';
const MAIL_SUBJECT = 'Incoming from the Wonderful Wizard';

function send_email(string $to, string $message): void
{
    $headers = ['From' => MAIL_SENDER];

    $success = mail($to, MAIL_SUBJECT, $message, $headers);

    if (!$success) {
        throw new Exception('The man behind the curtain is on break.');
    }
}

send_email('dorothy@kansas.example', 'Welcome to the Emerald City!');
```

## 讨论

解决方案示例使用 PHP 的本机`mail()`函数将静态主题的简单消息发送到指定的接收者。PHP 的`mail()`在成功时返回`true`，出现错误时返回`false`。在解决方案示例中，当出现问题时您仅想抛出异常，但在其他情况下希望静默返回。

###### 注意

在许多情况下，当函数完成时，您可能希望返回一个标志 —— 布尔值、字符串或`null` —— 以指示发生了什么，这样您程序的其余部分可以适当地行事。返回*nothing*的函数相对较少，但当您的程序与外部进行通信且通信结果不影响程序的其余部分时，它们确实会出现。将消息队列发送到火并忘连接或记录到系统错误日志是返回`void`的函数的常见用例。

PHP 中的`void`返回类型在编译时强制执行，这意味着如果函数体返回*任何*内容，即使您尚未执行任何操作，您的代码也将触发致命错误。示例 3-25 展示了`void`的有效和无效使用。

##### 示例 3-25\. `void` 返回类型的有效和无效使用

```php
function returns_scalar(): void
{
    return 1; ![1](img/1.png)
}

function no_return(): void
{
    ![2](img/2.png)
}

function empty_return(): void
{
    return; ![3](img/3.png)
}

function returns_null(): void
{
    return null; ![4](img/4.png)
}
```

![1](img/#co_functions_CO8-1)

返回标量类型（如字符串、整数或布尔值）将触发致命错误。

![2](img/#co_functions_CO8-2)

在函数中省略任何返回是有效的。

![3](img/#co_functions_CO8-3)

明确地返回没有数据是有效的。

![4](img/#co_functions_CO8-4)

即使`null`是“空的”，它仍然算作返回值，并将触发致命错误。

与 PHP 中的大多数其他类型不同，`void`类型仅适用于返回。它不能用作函数定义的参数类型；尝试这样做将导致编译时致命错误。

## 参见

PHP 7.1 中引入`void`返回类型的[原始 RFC](https://oreil.ly/FvRb_)。

# 3.13 创建一个不返回值的函数

## 问题

您需要定义一个显式退出的函数，并确保应用程序的其他部分知道它永远不会返回。

## 解决方案

使用显式类型注释并引用 `never` 返回类型。例如：

```php
function redirect(string $url): never
{
    header("Location: $url");
    exit();
}
```

## 讨论

PHP 中的一些操作意图是在退出当前进程之前引擎执行的最后一个操作。调用 `header()` 定义特定响应头必须在打印响应主体或处理其他操作之前完成。具体来说，调用 `header()` 触发重定向通常是应用程序执行的最后一步 —— 在告知请求客户端重定向至其他位置后打印任何主体文本或处理任何其他操作没有意义或价值。

`never` 返回类型向 PHP 和代码的其他部分都表明函数通过 `exit()`、`die()` 或抛出异常“*保证*”停止程序执行。

如果使用 `never` 返回类型的函数仍然隐式返回，例如示例 3-26，PHP 将抛出 `TypeError` 异常。

##### 示例 3-26\. 一个本不应该返回的函数中的隐式返回

```php
function log_to_screen(string $message): never
{
    echo $message;
}
```

同样，如果 `never` 类型的函数 *显式* 返回一个值，PHP 也会抛出 `TypeError` 异常。无论是隐式还是显式返回，这个异常都是在调用时（函数被调用时）而不是在定义时强制执行的。

## 参见

[PHP 8.1 中引入 `never` 返回类型的原始 RFC](https://oreil.ly/wO3zv)。

¹ 在第八章详细讨论了自定义类和对象。

² [PHP CodeSniffer](https://oreil.ly/G4tHg) 是一款流行的开发者工具，用于自动扫描代码库并确保所有代码符合特定的编码标准。它可以轻松扩展以在所有文件中强制执行严格的类型声明。

³ 根据[W3Techs](https://oreil.ly/8Y_Zp)，截至 2023 年 3 月，WordPress 的市场覆盖率约为使用内容管理系统的网站的 63%，以及所有网站的超过 43%。
