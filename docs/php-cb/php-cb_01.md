# 第一章：变量

灵活应用的基础是*变量*——程序在不同上下文中提供多种用途的能力。*变量*是任何编程语言中构建此类灵活性的常见机制。这些命名占位符引用程序希望使用的特定值。这可以是一个数字、一个原始字符串，甚至是一个具有自己属性和方法的更复杂的对象。关键在于变量是程序（以及开发者）引用该值并将其从程序的一部分传递到另一部分的方式。

变量默认情况下不需要设置——定义一个占位符变量而不给它分配任何值是完全合理的。可以将其视为架子上的空盒子，准备好等待圣诞节礼物。您可以轻松找到这个盒子——即变量——但因为里面什么也没有，所以您不能做太多事情。

例如，假设变量称为 `$giftbox`。如果您现在尝试检查此变量的值，它将是空的，因为它尚未设置。实际上，`empty($giftbox)` 将返回 `true`，而 `isset($giftbox)` 将返回 `false`。这个盒子既是空的，也还未设置。

###### 注意

重要的是要记住，任何未显式定义（或设置）的变量在 PHP 中将被视为 `empty()`。一个实际定义（或设置）的变量可以是空的或非空的，具体取决于其值，因为任何评估为 `false` 的实际值将被视为空。

广义上讲，编程语言可以是强类型或松散类型。强类型语言要求明确标识所有变量、参数和函数返回类型，并强制确保每个值的类型完全符合预期。而松散类型语言（如 PHP）在使用时对值进行*动态*类型化。例如，开发者可以将整数（如 `42`）存储在变量中，然后在其他地方将该变量用作字符串（即 `"42"`），PHP 将在运行时将该变量从整数透明地转换为字符串。

松散类型的优势在于，开发者在定义变量时不需要确定其如何使用，因为解释器可以在运行时很好地识别。其主要缺点在于，当解释器从一种类型强制转换为另一种类型时，某些值将如何处理并不总是清楚。

PHP 以松散类型语言而闻名。这使得该语言与众不同，开发者在创建或调用特定变量时无需明确标识其类型。PHP 解释器会在变量使用时识别正确的类型，并且在许多情况下，会在运行时将变量透明地转换为不同的类型。表格 1-1 展示了各种表达式，截至 PHP 8.0 版本，无论其底层类型如何，都被评估为“空”。

表格 1-1 PHP 空表达式

| 表达式 | `empty($x)` |
| --- | --- |
| `$x = ""` | `true` |
| `$x = null` | `true` |
| `$x = []` | `true` |
| `$x = false` | `true` |
| `$x = 0` | `true` |
| `$x = "0"` | `true` |

注意，其中一些表达式虽然不是真正的空值，但在 PHP 中被视为空值。在一般对话中，它们被视为`falsey`，因为它们被视为等同于`false`，尽管它们与`false`并非完全相同。因此，在应用程序中明确检查预期值如`null`、`false`或`0`比依赖于像`empty()`这样的语言结构更为重要。在这种情况下，您可能希望检查变量是否为空，并对已知的固定值进行显式的等式检查。¹

本章的示例介绍了 PHP 中变量定义、管理和利用的基础知识。

# 1.1 定义常量

## 问题

您希望在程序中定义一个特定的变量，使其具有固定的值，不受任何其他代码的突变或更改影响。

## 解决方案

下面的代码块使用`define()`来显式定义一个全局作用域常量的值，其他代码无法更改它：

```php
if (!defined('MY_CONSTANT')) {
    define('MY_CONSTANT', 5);
}
```

作为替代方法，以下代码块使用类内的`const`指令来定义一个仅在该类内部作用域中有效的常量：²

```php
class MyClass
{
    const MY_CONSTANT = 5;
}
```

## 讨论

如果在应用程序中定义了一个常量，函数`defined()`将返回`true`，告诉您可以直接在代码中访问该常量。如果常量尚未定义，PHP 会尝试猜测您的意图，并将常量引用转换为字符串字面量。

###### 提示

将常量名称全部大写并非必需。但是，PHP 基础编码规范（PHP 标准推荐 1，或 PSR-1）中定义的这一约定被 PHP 框架互操作性组（PHP-FIG）强烈推荐。[Basic Coding Standard (PHP Standard Recommendation 1, or PSR-1)](https://oreil.ly/_rNMe)中有详细的标准。

例如，以下代码块只有在定义了常量时才会将`MY_CONSTANT`的值赋给变量`$x`。在 PHP 8.0 之前，未定义的常量会导致`$x`保存字面字符串`"MY_CONSTANT"`：

```php
$x = MY_CONSTANT;
```

如果`MY_CONSTANT`的预期值不是字符串，则 PHP 的回退行为会导致应用程序出现意外副作用。解释器不一定会崩溃，但在期望整数的位置出现`"MY_​CON⁠STANT"`将会引起问题。从 PHP 8.0 开始，引用未定义的常量会导致致命错误。

解决方案示例演示了定义常量的两种模式：`define()` 或 `const`。使用 `define()` 将创建一个全局常量，在应用程序的任何地方都可以通过常量的名称直接访问。在类定义中使用 `const` 定义常量将常量作用域限定为该类。与在第一个解决方案中引用 `MY_CONSTANT` 不同，类作用域的常量被引用为 `MyClass::MY_CONSTANT`。

###### 警告

PHP 定义了[几个默认常量](https://oreil.ly/zQ40o)，不能被用户代码覆盖。总体上，常量是固定的，不能被修改或替换，因此*始终*在尝试定义常量之前检查它是否已定义。尝试重新定义常量将导致通知。有关处理错误和通知的更多信息，请参见第 12 章。

类常量默认为公共可见，这意味着应用程序中可以引用 `MyClass` 的任何代码也可以引用其公共常量。但是，自 PHP 7.1.0 起，可以对类常量应用可见性修饰符，并将其私有化为类的实例。

## 参见

[PHP 中的常量文档](https://oreil.ly/9WBhy)，[`defined()`](https://oreil.ly/jmiau)，[`define()`](https://oreil.ly/9iON9) 和 [类常量](https://oreil.ly/ggaCv) 的文档。

# 1.2 创建变量变量

## 问题

您希望动态引用特定变量，而不知道程序将需要哪个相关变量。

## 解决方案

PHP 的变量语法以 `$` 开头，后跟要引用的变量名称。您可以使变量名称本身成为一个变量。以下程序将通过使用变量变量打印 `#f00`：

```php
$red = '#f00';
$color = 'red';

echo $$color;
```

## 讨论

当 PHP 解析您的代码时，它将看到以 `$` 字符开头的内容作为变量的标识，并且紧随其后的文本表示该变量的名称。在解决方案示例中，该文本本身就是一个变量。PHP 将从右向左评估变量变量，将一个评估的结果作为左侧评估使用的名称，然后再将任何数据打印到屏幕上。

换句话说，示例 1-1 显示了两行代码，在功能上是等效的，只是第二行使用大括号显式标识首先评估的代码。

##### Example 1-1\. 评估变量变量

```php
$$color;
${$color};
```

最右边的 `$color` 首先评估为文字 `"red"`，这反过来意味着 `$$color` 和 `$red` 最终引用相同的值。引入大括号作为显式评估分隔符，可以提供更复杂的应用程序。

示例 1-2 假设应用程序希望出于搜索引擎优化（SEO）目的对标题进行 A/B 测试。 市场团队提供了两个选项，开发人员希望针对不同的访问者返回不同的标题—但当访问者返回网站时返回*相同*的标题。 您可以通过利用访问者的 IP 地址并创建一个变量变量来实现这一目的，选择基于访问者 IP 地址的标题。

##### 示例 1-2\. A/B 测试标题

```php
$headline0 = 'Ten Tips for Writing Great Headlines';
$headline1 = 'The Step-by-Step to Writing Powerful Headlines';

echo ${'headline' . (crc32($_SERVER['REMOTE_ADDR']) % 2)};
```

在上述示例中，`crc32()` 函数是一个方便的实用工具，用于以确定性方式计算给定字符串的 32 位校验和—它将字符串转换为整数。 `%` 操作符对结果整数执行模运算，如果校验和为偶数则返回 `0`，为奇数则返回 `1`。然后将结果连接到你的动态变量 `headline` 中，以使函数能够选择其中一个标题。

###### 注意

`$_SERVER` 数组是一个系统定义的[超全局变量](https://oreil.ly/DtQV-)，包含有关运行代码的服务器以及触发 PHP 运行的传入请求的有用信息。 这个特定数组的确切内容会因服务器而异，特别是根据您在 PHP 前端使用的是 NGINX 还是 Apache HTTP 服务器（或其他 Web 服务器），但它通常包含有用的信息，如请求头、请求路径和当前执行脚本的文件名。

示例 1-3 逐行展示了 `crc32()` 的使用，进一步说明了如何利用像 IP 地址这样的用户相关值来确定性地标识用于 SEO 目的的标题。

##### 示例 1-3\. IP 地址检验和演练

```php
$_SERVER['REMOTE_ADDR'] = '127.0.0.1'; ![1](img/1.png)
crc32('127.0.0.1') = 3619153832; ![2](img/2.png)
3619153832 % 2 = 0; ![3](img/3.png)
'headline' . 0 = 'headline0' ![4](img/4.png)
${'headline0'} = 'Ten Tips for Writing Great Headlines'; ![5](img/5.png)
```

![1](img/#co_variables_CO1-1)

IP 地址从 `$_SERVER` 超全局变量中提取。 还请注意，仅在通过 Web 服务器而不是通过 CLI 使用 PHP 时，`REMOTE_ADDR` 键才会存在。

![2](img/#co_variables_CO1-2)

`crc32()` 将字符串 IP 地址转换为整数校验和。

![3](img/#co_variables_CO1-3)

模运算符 (`%`) 确定校验和是偶数还是奇数。

![4](img/#co_variables_CO1-4)

此模运算的结果附加到 `headline` 中。

![5](img/#co_variables_CO1-5)

最终字符串 `headline0` 被用作变量变量，用于标识正确的 SEO 标题值。

甚至可以嵌套多层次的可变变量。使用三个 `$` 字符，例如 `$$$name`，同样有效，就像 `$$${*some_function*()}` 一样。在变量名中限制变化水平既有助于代码审查的简化，也有助于一般维护。变量变量的使用案例本来就很少见，但如果出现问题，多层间接将使您的代码难以跟踪、理解、测试或维护。

## 另请参见

关于[可变变量](https://oreil.ly/wNBh0)的文档。

# 1.3 在原地交换变量

## 问题

你想要在不定义任何额外变量的情况下交换两个变量存储的值。

## 解决方案

下面的代码块使用 `list()` 语言结构来直接在原地重新分配变量的值：

```php
list($blue, $green) = array($green, $blue);
```

更简洁版本的前述解决方案是同时使用 PHP 7.1 提供的短列表和短数组语法，如下所示：

```php
[$blue, $green] = [$green, $blue];
```

## 讨论

PHP 中的 `list` 关键字并不是指一个函数，虽然看起来像一个。它是一个*语言结构*，用于将值分配给一组变量而不是一次只给一个变量。这使开发人员可以一次性从另一个类似列表的值集合（如数组）中设置多个变量。它还允许将数组解构为单独的变量。

现代 PHP 利用方括号（`[` 和 `]`）来简化数组语法，允许更简洁的数组字面量。写 `[1, 4, 5]` 在功能上等同于 `array(1, 4, 5)`，但在使用的上下文中更清晰一些。

###### 注意

像 `list` 一样，`array` 关键字在 PHP 中指的是一个语言结构。语言结构是硬编码到语言中的关键字，是使系统工作的关键字。像 `if`、`else` 或 `echo` 这样的关键字很容易与用户代码区分开来。像 `list`、`array` 和 `exit` 这样的语言结构看起来像函数，但像关键字样的结构内建于语言中，与典型函数的行为稍有不同。[PHP 手册的保留关键字列表](https://oreil.ly/OJD13)更好地说明了现有的结构，并交叉引用了每个关键字在实践中的使用方式。

自 PHP 7.1 起，开发人员可以使用相同的短方括号语法来替换 `list()` 的使用，创建更简洁和可读的代码。考虑到解决此问题的方案是将数组的值分配给一组变量数组，使用赋值运算符 (`=`) 两边类似的语法是合理的并且可以澄清您的意图。

解决方案示例明确交换了变量`$green`和`$blue`中存储的值。这是工程师在部署应用程序时可能会做的事情，以从一个 API 版本切换到另一个版本。滚动部署通常将当前的生产环境称为*绿色*部署，将新的、潜在的替代环境称为*蓝色*，指示负载均衡器和其他依赖应用程序从绿色/蓝色切换并验证连接和功能，然后确认部署是否健康。

在一个更详细的示例中（示例 1-4），假设应用程序消耗一个以部署日期为前缀的 API。该应用程序跟踪正在使用的 API 版本（`$green`），并尝试切换到新环境以验证连接。如果连接检查失败，应用程序将自动切换回旧环境。

##### 示例 1-4\. 蓝/绿环境切换

```php
$green = 'https://2021_11.api.application.example/v1';
$blue = 'https://2021_12.api.application.example/v1';

[$green, $blue] = [$blue, $green];

if (connection_fails(check_api($green))) {
    [$green, $blue] = [$blue, $green];
}
```

`list()`结构也可以用来从任意元素组中提取特定值。示例 1-5 说明了如何将作为数组存储的地址，在不同上下文中提取所需的特定值。

##### 示例 1-5\. 使用`list()`来提取数组元素

```php
$address = ['123 S Main St.', 'Anywhere', 'NY', '10001', 'USA'];

// Extracting each element as named variables
[$street, $city, $state, $zip, $country] = $address;

// Extracting and naming only the city
[,,$state,,] = $address;

// Extracting only the country
[,,,,$country] = $address;
```

前面示例中的每次提取都是独立的，只设置了必要的变量。³ 对于像这样的简单示例，不需要担心提取每个元素并设置一个变量，但对于操作数据规模显著较大的更复杂应用程序，设置不必要的变量可能会导致性能问题。虽然`list()`是一个用于解构类似数组的集合的强大工具，但它只适用于前面示例中讨论的简单情况。

## 参见

[`list()`](https://oreil.ly/bzO7i)、[`array()`](https://oreil.ly/tq1Z_)的文档，以及 PHP RFC 关于[short `list()` syntax](https://oreil.ly/Ou98z)。

¹ 相等运算符在 Recipe 2.3 中有所涵盖，其中提供了一个示例和对相等性检查的深入讨论。

² 在第八章中了解更多关于类和对象的内容。

³ 在本章介绍中回顾一下，变量引用如果没有明确设置将会被评估为“空”。这意味着你只能设置你需要使用的值和变量。