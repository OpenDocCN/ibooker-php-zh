# 第五章. 数字

PHP 数据的另一个基本构建块是数字。在我们周围的世界中很容易找到不同类型的数字。书中的页码通常打印在页脚上。你的智能手表显示当前时间，也许还有你今天走的步数。一些数字可能非常大，另一些可能非常小。数字可以是整数、分数，或者是像 π 这样的无理数。

在 PHP 中，数字可以以两种格式之一本地表示：作为整数（int 类型）或浮点数（float 类型）。两种数值类型都非常灵活，但你可以使用的值的范围取决于你系统的处理器架构 — 32 位系统比 64 位系统有更严格的边界。

PHP 定义了几个常量，帮助程序理解系统中可用数字的范围。考虑到 PHP 的能力因编译方式不同（32 位或 64 位）而有显著差异，最好使用在 表 5-1 中定义的常量，而不是尝试在程序中确定这些值将会是什么。最安全的做法是遵循操作系统和语言的默认设置。

表 5-1. PHP 中的常量数值

| 常量 | 描述 |
| --- | --- |
| `PHP_INT_MAX` | PHP 支持的最大整数值。在 32 位系统上，这个值是 `2147483647`。在 64 位系统上，这个值是 `9223372036854775807`。 |
| `PHP_INT_MIN` | PHP 支持的最小整数值。在 32 位系统上，这个值是 `-2147483648`。在 64 位系统上，这个值是 `-9223372036854775808`。 |
| `PHP_INT_SIZE` | 此 PHP 构建中整数的字节大小。 |
| `PHP_FLOAT_DIG` | 可以在 `float` 中往返舍入的位数，而不会丢失精度。 |
| `PHP_FLOAT_​EPSI⁠LON` | 最小可表示的正数 `*x*`，使得 `*x* + 1.0 !== 1.0`。 |
| `PHP_FLOAT_MIN` | 可以表示的最小正浮点数。 |
| `PHP_FLOAT_MAX` | 可以表示的最大浮点数。 |
| `-PHP_FLOAT_MAX` | 不是单独的常量，但是表示最小负浮点数的方式。 |

PHP 数字系统的不幸限制在于非常大或非常小的数字无法本地表示。相反，你需要利用像 [BCMath](https://oreil.ly/qFeO3) 或 [GNU 多精度算术库 (GMP)](https://oreil.ly/u9Mbf) 这样的扩展，两者都包装了操作系统原生的数字操作。我将在 Recipe 5.10 中具体介绍 GMP。

接下来的配方涵盖了 PHP 中开发者需要解决的许多与数字相关的问题。

# 5.1 在变量中验证一个数字

## 问题

你想要检查一个变量是否包含一个数字，即使该变量明确被声明为字符串类型。

## 解决方案

使用 `is_numeric()` 来检查一个变量是否可以成功转换为数值，例如：

```php
$candidates = [
    22,
    '15',
    '12.7',
    0.662,
    'infinity',
    INF,
    0xDEADBEEF,
    '10e10',
    '15 apples',
    '2,500'
];

foreach ($candidates as $candidate) {
    $numeric = is_numeric($candidate) ? 'is' : 'is NOT';

    echo "The value '{$candidate}' {$numeric} numeric." . PHP_EOL;
}
```

在控制台中运行时，上述示例将打印以下内容：

```php
The value '22' is numeric.
The value '15' is numeric.
The value '12.7' is numeric.
The value '0.662' is numeric.
The value 'infinity' is NOT numeric.
The value 'INF' is numeric.
The value '3735928559' is numeric.
The value '10e10' is numeric.
The value '15 apples' is NOT numeric.
The value '2,500' is NOT numeric.
```

## 讨论

在其核心，PHP 是一种动态类型语言。您可以轻松地将字符串与整数（反之亦然）进行交换，并且 PHP 将尝试推断您的意图，根据需要动态地将值从一种类型转换为另一种类型。虽然您可以（并且可能应该）像配方 3.4 中讨论的那样强制执行严格类型，但通常您需要显式地将数字编码为字符串。

在这些情况下，您将失去利用 PHP 类型系统识别数值字符串的能力。将一个作为`string`传递到函数中的变量，在没有显式强制转换为数值类型（`int`或`float`）的情况下，将无法进行数学运算。不幸的是，并非每个包含数字的字符串都是数值的。

字符串`15 apples`包含一个数字但不是数值。字符串`10e10`包含非数值字符但是有效的数值表示。

数字字符串和真正的数值字符串之间的差异可以通过 PHP 的本地`is_numeric()`函数的用户空间实现来最好地说明，如示例 5-1 所定义。

##### 示例 5-1\. 用户空间`is_numeric()`实现

```php
function string_is_numeric(string $test): bool
{
    return $test === (string) floatval($test);
}
```

应用于与解决方案示例中相同的`$candidates`数组，示例 5-1 将准确验证几乎所有的数值字符串，除了字面上的`INF`常量和`10e10`指数缩写。这是因为在将其转换为浮点数之前，`floatval()` 将完全去除字符串中的任何非数值字符，同时进行`(string)`强制类型转换。

用户空间实现并不适用于每种情况，因此您应该使用本机实现以确保安全。`is_numeric()`的目标是指示给定的字符串是否可以安全地转换为数值类型而不会丢失信息。

## 参见

PHP [`is_numeric()`](https://oreil.ly/jTGcF)的文档。

# 5.2 比较浮点数

## 问题

您想要测试两个浮点数的相等性。

## 解决方案

定义一个合适的误差界限（称为`epsilon`），表示两个数之间的最大可接受差异，并按以下方式评估它们的差异：

```php
$first = 22.19348234;
$second = 22.19348230;

$epsilon = 0.000001;

$equal = abs($first - $second) < $epsilon; // true
```

## 讨论

现代计算机的浮点运算由于机器内部表示数字的方式而不那么精确。您可能手工计算并假设是精确的不同操作可能会让您依赖的机器出现问题。

例如，数学运算`1 - 0.83`显然是`0.17`。这足够简单，在头脑中计算甚至在纸上工作出来。但是让计算机计算这个将产生一个奇怪的结果，正如在示例 5-2 中演示的那样。

##### 示例 5-2\. 浮点数减法

```php
$a = 0.17;
$b = 1 - 0.83;

var_dump($a == $b); ![1](img/1.png)
var_dump($a); ![2](img/2.png)
var_dump($b); ![3](img/3.png)
```

![1](img/#co_numbers_CO1-1)

`bool(false)`

![2](img/#co_numbers_CO1-2)

`float(0.17)`

![3](img/#co_numbers_CO1-3)

`float(0.17000000000000004)`

在涉及浮点运算时，计算机能够做到的最好结果是在可接受误差范围内的近似结果。因此，将此结果与预期值进行比较需要明确定义该误差（`epsilon`）并与该误差进行比较，而不是与精确值进行比较。

与其利用 PHP 的任一等式运算符（双或三个等号），不如定义一个函数来检查两个浮点数的*相对*相等性，如示例 5-3 所示。

##### 示例 5-3\. 比较浮点数的相等性

```php
function float_equality(float $epsilon): callable
{
    return function(float $a, float $b) use ($epsilon): bool
    {
        return abs($a - $b) < $epsilon;
    };
}

$tight_equality = float_equality(0.0000001);
$loose_equality = float_equality(0.01);

var_dump($tight_equality(1.152, 1.152001)); ![1](img/1.png)
var_dump($tight_equality(0.234, 0.2345)); ![2](img/2.png)
var_dump($tight_equality(0.234, 0.244)); ![3](img/3.png)
var_dump($loose_equality(1.152, 1.152001)); ![4](img/4.png)
var_dump($loose_equality(0.234, 0.2345)); ![5](img/5.png)
var_dump($loose_equality(0.234, 0.244)); ![6](img/6.png)
```

![1](img/#co_numbers_CO2-1)

`bool(false)`

![2](img/#co_numbers_CO2-2)

`bool(false)`

![3](img/#co_numbers_CO2-3)

`bool(false)`

![4](img/#co_numbers_CO2-4)

`bool(true)`

![5](img/#co_numbers_CO2-5)

`bool(true)`

![6](img/#co_numbers_CO2-6)

`bool(true)`

## 参见

PHP 文档中关于[浮点数](https://oreil.ly/-311_)的介绍。

# 5.3 浮点数四舍五入

## 问题

您希望将浮点数要么舍入到指定的小数位数，要么舍入到整数。

## 解决方案

要将浮点数舍入到指定小数位数，使用 `round()` 并指定小数位数：

```php
$number = round(15.31415, 1);
// 15.3
```

要明确地向上舍入到最接近的整数，使用 `ceil()`：

```php
$number = ceil(15.3);
// 16
```

要明确地向下舍入到最接近的整数，使用 `floor()`：

```php
$number = floor(15.3);
// 15
```

## 讨论

解决方案示例中提到的三个函数——`round()`、`ceil()` 和 `floor()`——旨在对任何数值进行操作，但在操作后将返回一个浮点数。默认情况下，`round()` 将四舍五入到小数点后零位，但仍将返回一个浮点数。

若要将 `float` 转换为 `int` 以便应用这些函数中的任何一个，请将函数本身包装在 `intval()` 中以转换为整数类型。

在 PHP 中进行四舍五入比仅仅向上或向下舍入更为灵活。默认情况下，`round()` 函数在数字正好处于中间值时，会向远离 0 的方向进行舍入。这意味着像 1.4 这样的数字会向下舍入，而 1.5 则会向上舍入。负数同样适用：−1.4 会向 0 舍入到 −1，而 −1.5 则会远离 0 舍入到 −2。

通过传递可选的第三个参数（或像 Recipe 3.3 中显示的使用命名参数），可以更改 `round()` 的行为以指定舍入模式。该参数接受 PHP 定义的四个默认常量之一，如表 5-2 所列。

表 5-2\. 舍入模式常量

| 常量 | 描述 |
| --- | --- |
| `PHP_ROUND_HALF_UP` | 当数字正好处于中间值时，远离 0 进行舍入，使得 1.5 变成 2，−1.5 变成 −2 |
| `PHP_ROUND_HALF_DOWN` | 当数字正好处于中间值时，向 0 舍入，使得 1.5 变成 1，−1.5 变成 −1 |
| `PHP_ROUND_HALF_EVEN` | 当值处于中间时，将其向最接近的偶数值舍入，使 1.5 和 2.5 都变为 2 |
| `PHP_ROUND_HALF_ODD` | 当值处于中间时，将其向最接近的奇数值舍入，使 1.5 变为 1，2.5 变为 3 |

Example 5-4 阐明了在应用于相同数字时每个舍入模式常量的效果。

##### Example 5-4\. 使用不同模式在 PHP 中对浮点数进行四舍五入

```php
echo 'Rounding on 1.5' . PHP_EOL;
var_dump(round(1.5, mode: PHP_ROUND_HALF_UP));
var_dump(round(1.5, mode: PHP_ROUND_HALF_DOWN));
var_dump(round(1.5, mode: PHP_ROUND_HALF_EVEN));
var_dump(round(1.5, mode: PHP_ROUND_HALF_ODD));

echo 'Rounding on 2.5' . PHP_EOL;
var_dump(round(2.5, mode: PHP_ROUND_HALF_UP));
var_dump(round(2.5, mode: PHP_ROUND_HALF_DOWN));
var_dump(round(2.5, mode: PHP_ROUND_HALF_EVEN));
var_dump(round(2.5, mode: PHP_ROUND_HALF_ODD));
```

上述示例将以下内容打印到控制台：

```php
Rounding on 1.5
float(2)
float(1)
float(2)
float(1)
Rounding on 2.5
float(3)
float(2)
float(2)
float(3)
```

## 参见

PHP 文档关于 [浮点数](https://oreil.ly/ONHjD)，[`round()`](https://oreil.ly/010CB) 函数，[`ceil()`](https://oreil.ly/i5Rpy) 函数和 [`floor()`](https://oreil.ly/VAZ6t) 函数。

# 5.4 生成真正随机数

## 问题

您希望在特定边界内生成随机整数。

## 解决方案

使用 `random_int()` 如下所示：

```php
// Random integer between 10 and 225, inclusive
$random_number = random_int(10, 225);
```

## 讨论

当您需要随机性时，通常需要显式真正完全不可预测的随机性。在这些情况下，您可以依赖于内置于机器本身的加密安全伪随机数生成器。PHP 的 `random_int()` 函数依赖于这些操作系统级数生成器，而不是实现其自己的算法。

在 Windows 上，PHP 将根据使用的语言版本利用 [`CryptGenRandom()`](https://oreil.ly/0kVO9) 或 [`Cryptography API: Next Generation (CNG)`](https://oreil.ly/otHP9)。在 Linux 上，PHP 利用系统调用 [`getrandom(2)`](https://oreil.ly/07DIE)。在任何其他平台上，PHP 将回退到系统级 */dev/urandom* 接口。所有这些 API 都经过充分测试，被证明是密码学安全的，意味着它们生成具有足够随机性的数字，几乎与噪声不可区分。

###### 注意

在罕见情况下，您可能希望随机数生成器生成可预测的伪随机值序列。在这些情况下，您可以依赖于像 Mersenne Twister 这样的算法生成器，如 Recipe 5.5 进一步讨论的那样。

PHP 并不原生支持创建随机浮点数的方法（即在 0 和 1 之间选择随机小数）。相反，您可以使用 `ran⁠dom_​int()` 和您在 PHP 中整数的知识来创建自己的函数来实现这一点，如 Example 5-5 所示。

##### Example 5-5\. 生成随机浮点数的用户空间函数

```php
function random_float(): float
{
    return random_int(0, PHP_INT_MAX) / PHP_INT_MAX;
}
```

由于此 `random_float()` 实现缺乏边界，因此它将始终生成一个介于 0 和 1 之间的数字。这可能对创建随机百分比或随机选择数组的样本大小非常有用。更复杂的实现可能会像 Example 5-6 中展示的那样包含边界，但通常选择在 0 和 1 之间进行选择就足够实用了。

##### Example 5-6\. 生成处于边界内的随机 `float` 的用户空间函数

```php
function random_float(int $min = 0, int $max = 1): float
{
    $rand = random_int(0, PHP_INT_MAX) / PHP_INT_MAX;

    return ($max - $min) * $rand + $min;
}
```

这个新版本的`random_float()`函数仅仅是将原始定义按照新的边界进行缩放。如果你保持默认边界不变，函数就会退化成原始定义。

## 参见

PHP 文档关于[`random_int()`](https://oreil.ly/kLoas)。

# 5.5 生成可预测的随机数

## 问题

想要预测随机数，使得每次生成的数列都相同。

## 解决方案

在调用`mt_srand()`并传入预定义种子后，可以使用`mt_rand()`函数，例如：

```php
function generate_sequence(int $count = 10): array
{
    $array = [];

    for ($i = 0; $i < $count; $i++) {
        $array[] = mt_rand(0, 100);
    }

    return $array;
}

mt_srand(42);
$first = generate_sequence();

mt_srand(42);
$second = generate_sequence();

print_r($first);
print_r($second);
```

在前面示例中的两个数组都将具有以下内容：

```php
Array
(
    [0] => 38
    [1] => 32
    [2] => 94
    [3] => 55
    [4] => 2
    [5] => 21
    [6] => 10
    [7] => 12
    [8] => 47
    [9] => 30
)
```

## 讨论

当讨论真正的随机数示例时，任何人都只能展示输出的*可能*样子。但是当涉及到`mt_rand()`的输出时，只要使用相同的种子，输出在任何计算机上都将相同。

###### 注意

PHP 默认情况下会随机种子`mt_rand()`。除非目标是从函数中获取确定性输出，否则不需要指定自己的种子。

输出相同是因为`mt_rand()`使用了名为*Mersenne Twister*的算法伪随机数生成器。这是一个广为人知和广泛使用的算法，首次引入于 1997 年；它还被用在 Ruby 和 Python 等语言中。

给定一个初始种子值，算法创建一个初始状态，然后通过在该状态上执行“扭转”操作生成看似随机的数字序列。这种方法的优势在于它是确定性的——给定相同的种子，算法每次都会生成相同的“随机”数序列。

###### 警告

随机数的可预测性对于某些计算操作可能具有危害，特别是在密码学中。需要确定性伪随机数序列的用例并不常见，因此应尽量避免使用`mt_rand()`。如果需要生成随机数，应该利用像`random_int()`和`random_bytes()`这样的真实随机源。

创建一个伪随机但可预测的数列可能在为数据库创建对象 ID 时很有用。通过多次运行代码并验证输出，可以轻松测试代码的正确运行。缺点是像 Mersenne Twister 这样的算法可能会受到外部方的操纵。

如果知道算法并给定足够长的看似随机数序列，反向操作识别原始种子将变得非常容易。一旦攻击者知道种子值，他们就能生成系统未来将使用的每一个“随机”数。

## 参见

PHP 文档关于[`mt_rand()`](https://oreil.ly/niU_q)和[`mt_srand()`](https://oreil.ly/xSa53)。

# 5.6 生成加权随机数

## 问题

您希望生成随机数以随机选择集合中的特定项目，但希望某些项目被选中的机会更高。例如，您希望在活动中选择特定挑战的获胜者，但有些参与者赚取的积分比其他人多，因此需要更大的被选中机会。

## 解决方案

将选择和权重的映射传递给`weighted_​ran⁠dom_choice()`的实现，就像在示例 5-7 中演示的那样。

##### 示例 5-7\. 加权随机选择的实现

```php
$choices = [
    'Tony'  => 10,
    'Steve' => 2,
    'Peter' => 1,
    'Wanda' => 4,
    'Carol' => 6
];

function weighted_random_choice(array $choices): string
{
    arsort($choices);

    $total_weight = array_sum(array_values($choices));
    $selection = random_int(1, $total_weight);

    $count = 0;
    foreach ($choices as $choice => $weight) {
        $count += $weight;
        if ($count >= $selection) {
            return $choice;
        }
    }

    throw new Exception('Unable to make a choice!');
}

print weighted_random_choice($choices);
```

## 讨论

在解决方案示例中，每个可能的选择都被分配了一个权重。为了选择最终选项，您可以按权重*排序*每个选项，权重最高的选项在列表中排在第一位。然后，您在总可能权重范围内确定一个随机数。该随机数选择您选择的选项之一。

这在数轴上最容易进行可视化。在解决方案示例中，Tony 的权重为 10，Peter 的权重为 1。这意味着 Tony 比 Peter 有可能赢得的机会多 10 倍，但仍然有可能*两者都不会*被选择。图 5-1 说明了如果按权重排序并在数轴上打印可能的选择，每个选择的相对权重。

![在连续线上可视化的加权随机选择](img/phpc_0501.png)

###### 图 5-1\. 按权重排序和可视化的潜在选择

在`weighted_random_choice()`中定义的算法将检查所选随机数是否在每个可能选择的范围内，如果不在，则继续下一个候选项。如果由于任何原因无法进行选择，函数将抛出异常。²

可以通过执行一千次随机选择并绘制每个选择被选中的相对次数来验证这种选择的加权性质。示例 5-8 展示了如何对这样的重复选择进行制表，而图 5-2 说明了结果。两者都清楚地展示了 Tony 被选中的可能性比候选数组中的任何其他选项更高。

##### 示例 5-8\. 重复选择加权随机选择

```php
$output = fopen('output.csv', 'w');
fputcsv($output, ['selected']);

foreach (range(0, 1000) as $i) {
    $selection = weighted_random_choice($choices);
    fputcsv($output, [$selection]);
}
fclose($output);
```

![1000 次加权随机选择的结果。](img/phpc_0502.png)

###### 图 5-2\. 饼图说明每个选择被选中的相对次数

这个在 1,000 次迭代后的结果清楚地表明，Tony 被选择的频率大约是 Peter 的 10 倍。这与他的权重为 10，Peter 的权重为 1 完美契合。同样，Wanda 的权重为 4 可靠地表明她被选择的频率是 Steve 的两倍，后者的权重为 2。

鉴于这里的选择是随机的，再次运行相同的实验将导致每个候选人的百分比略有不同。然而，每个候选人的整数权重始终会转化为大致相同的选择分布。

## 参见

PHP 文档关于[`random_int()`](https://oreil.ly/Pq16w)和[`arsort()`](https://oreil.ly/VZ-Vz)，以及在实践中进一步使用`random_int()`的 Recipe 5.4 的例子。

# 5.7 计算对数

## 问题

您想要计算一个数的对数。

## 解决方案

对于自然对数（使用基数`e`），使用`log()`如下：

```php
$log = log(5);
// 1.6094379124341
```

对于任意基数的对数，将基数作为第二个可选参数指定：

```php
$log2 = log(16, 2);
// 4.0
```

## 讨论

PHP 通过其本机 Math 扩展支持对数计算。当您调用`log()`而不指定基数时，PHP 将回退到默认的`M_E`常量，其编码为`e`的值，约为 2.718281828459。

如果尝试对负值取对数，PHP 将始终返回`NAN`，一个表示*不是一个数字*的常量（类型为`float`）。如果尝试传递一个负的底数，PHP 将返回一个`ValueError`并触发警告。

`log()`支持任何正的非零基数。许多应用程序如此频繁地使用基数 10，以至于 PHP 支持一个单独的`log10()`函数专门用于此基数。这在功能上等同于将整数`10`作为基数传递给`log()`。

## 参见

PHP 文档对[Math 扩展](https://oreil.ly/nLOM7)支持的各种功能的说明，包括[`log()`](https://oreil.ly/r-WYo)和[`log10()`](https://oreil.ly/7Tn4t)。

# 5.8 计算指数

## 问题

您想将一个数提升到任意幂。

## 解决方案

使用 PHP 的`pow()`函数如下：

```php
// 2⁵
$power = pow(2, 5); // 32

// 3⁰.5
$power = pow(3, 0.5); // 1.7320508075689

// e²
$power = pow(M_E, 2); // 7.3890560989306
```

## 讨论

PHP 的`pow()`函数是将任意数提升到任意幂并返回整数或浮点结果的有效方法。除了函数形式外，PHP 还提供了一个特殊的运算符来将一个数提升到幂：`**`。

下面的代码等效于解决方案示例中使用`pow()`的使用：

```php
// 2⁵
$power = 2 ** 5; // 32

// 3⁰.5
$power = 3 ** 0.5; // 1.7320508075689

// e²
$power = M_E ** 2; // 7.3890560989306
```

###### 警告

虽然数学上指数运算的简写通常是插入符（`^`），但在 PHP 中，此字符保留为异或运算符。请查看第二章以获取更多关于此及其他运算符的详细信息。

当对常数`e`进行任意幂运算时，可以通过`pow()`实现，PHP 还内置了专门用于此用途的函数：`exp()`。表达式`pow(M_E, 2)`和`exp(2)`在功能上是等效的。它们通过不同的代码实现，并且由于 PHP 内部浮点数的表示方式，它们返回的结果略有不同。³

## 参见

PHP 文档关于[`pow()`](https://oreil.ly/JEsKM)和[`exp()`](https://oreil.ly/AsgKw)。

# 5.9 将数字格式化为字符串

## 问题

您希望打印一个带有千位分隔符的数字，以使您的应用程序的最终用户更容易阅读。

## 解决方案

使用`number_format()`在将数字转换为字符串时自动添加千位分隔符。例如：

```php
$number = 25519;
print number_format($number);
// 25,519

$number = 64923.12
print number_format($number, 2);
// 64,923.12
```

## 讨论

PHP 的本机`number_format()`函数将自动将千位分组并将小数位四舍五入到指定的精度。您还可以选择*更改*小数和千位分隔符，以匹配给定的语言环境或格式。

例如，假设您希望使用句点分隔千位组，并使用逗号分隔小数位（如丹麦数值格式中常见）。要实现这一点，您可以利用`number_format()`如下所示：

```php
$number = 525600.23;

print number_format($number, 2, ',', '.');
// 525.600,23
```

PHP 的本机`NumberFormatter`类提供类似的实用功能，但允许您显式定义语言环境，而无需记住特定的区域格式。⁴您可以重写前面的例子，使用`NumberFormatter`特定于`da_DK`语言环境来识别丹麦格式，如下所示：

```php
$number = 525600.23;

$fmt = new NumberFormatter('da_DK', NumberFormatter::DEFAULT_STYLE);
print $fmt->format($number);
// 525.600,23
```

## 参见

PHP 关于[`number_format()`](https://oreil.ly/3_L6J)和[`NumberFormatter`类](https://oreil.ly/IC3a9)的文档。

# 5.10 处理非常大或非常小的数字

## 问题

当你需要使用超出 PHP 本机整数和浮点类型处理能力的数字时。

## 解决方案

使用 GMP 库：

```php
$sum = gmp_pow(4096, 100);
print gmp_strval($sum);
// 17218479456385750618067377696052635483579924745448689921733236816400
// 74069124174561939748453723604617328637091903196158778858492729081666
// 10249916098827287173446595034716559908808846798965200551239064670644
// 19056526231345685268240569209892573766037966584735183775739433978714
// 57858778270138079724077247764787455598671274627136289222751620531891
// 4435913511141036261376
```

## 讨论

PHP 支持两个扩展来处理超出本机类型范围的数字。[BCMath 扩展](https://oreil.ly/XhhdH)是一个接口，连接到一个支持任意精度数学的系统级*基本计算器*实用程序。与本机 PHP 类型不同，BCMath 支持使用高达 2,147,483,647 位小数，只要系统内存足够。

不幸的是，BCMath 在 PHP 中将所有数字编码为常规字符串，这使得在目标为严格类型强制的现代应用程序中使用它有些困难。⁵

GMP 扩展也是 PHP 的一种有效替代方案，它没有这个缺点。在内部，数字被存储为字符串。但是，当与 PHP 的其他部分接触时，它们被包装为`GMP`对象。这种区别有助于澄清一个函数是在一个被编码为字符串的小数字上操作，还是在需要使用扩展的大数字上操作。

###### 注意

BCMath 和 GMP 操作和返回整数值，而不是浮点数。如果需要对浮点数进行操作，您可能需要将数字大小增加一个数量级（即乘以 10），然后在计算完成后再次减少，以考虑小数或分数。

PHP 默认情况下不包含 GMP，尽管许多发行版都可以很容易地提供它。如果您从源代码编译 PHP，可以使用`--with-gmp`选项来自动添加支持。如果您使用软件包管理器安装 PHP（例如在 Linux 机器上），您可能可以直接安装`php-gmp`软件包以添加这种支持。⁶

一旦可用，GMP 将使您能够在无限大小的数字上执行任何数学操作。但问题是，您不能再使用 PHP 的原生运算符，必须使用扩展本身定义的函数格式。Example 5-9 展示了从原生运算符到 GMP 函数调用的一些转换。请注意，每个函数调用的返回类型都是一个`GMP`对象，因此您必须使用`gmp_intval()`或`gmp_strval()`将其转换回数字或字符串。

##### 示例 5-9。各种数学操作及其 GMP 函数等效项

```php
$add = 2 + 5;
$add = gmp_add(2, 5);

$sub = 23 - 2;
$sub = gmp_sub(23, 2);

$div = 15 / 4;
$div = gmp_div(15, 4);

$mul = 3 * 9;
$mul = gmp_mul(3, 9);

$pow = 4 ** 7;
$pow = gmp_pow(4, 7);

$mod = 93 % 4;
$mod = gmp_mod(93, 4);

$eq = 42 == (21 * 2);
$eq = gmp_cmp(42, gmp_mul(21, 2));
```

Example 5-9 中的最后一个示例介绍了`gmp_cmp()`函数，它允许您比较两个 GMP 封装的值。如果第一个参数大于第二个参数，则此函数将返回一个正值，如果它们相等，则返回 0，如果第二个参数大于第一个参数，则返回一个负值。这与 PHP 的太空船操作符（在 Recipe 2.4 中引入）实际上是相同的，而不是一个相等比较，这可能提供了更多的实用性。

## 参见

PHP 文档上的[GMP](https://oreil.ly/rtfm3)部分。

# 5.11 数字之间的进制转换

## 问题

您希望将一个数字从一种基数转换为另一种基数。

## 解决方案

使用`base_convert()`函数如下：

```php
// Base 10 (decimal) number
$decimal = '240';

// Convert from base 10 to base 16
// $hex = 'f0'
$hex = base_convert($decimal, 10, 16);
```

## 讨论

`base_convert()`函数尝试将一个数字从一种基数转换为另一种基数，这在处理十六进制或二进制数据字符串时特别有用。PHP 仅支持 2 到 36 之间的基数。高于 10 的基数将使用字母字符表示额外的数字——`a`等于`10`，`b`等于`11`，一直到`z`等于`35`。

请注意，解决方案示例将一个*字符串*传递给`base_convert()`而不是一个整数或浮点数值。这是因为 PHP 将尝试将输入字符串转换为具有适当基数的数字，然后将其转换为另一种基数并返回一个字符串。在 PHP 中，字符串是表示十六进制或八进制数的最佳方式，但它们足够通用，可以表示*任何*基数的数字。

除了更通用的`base_convert()`外，PHP 还支持几种基数特定的转换函数。这些额外的函数在 Table 5-3 中列出。

###### 警告

PHP 支持两个函数用于二进制数据和其十六进制表示之间的相互转换：`bin2hex()`和`hex2bin()`。这些函数不用于将二进制的字符串表示（例如`11111001`）转换为十六进制，而是操作该字符串的二进制*字节*。

表 5-3\. 特定的基数转换函数

| 函数名称 | 从基数 | 到基数 |
| --- | --- | --- |
| `bindec()` | 二进制（以`string`编码） | 十进制（以`int`或由于大小原因而是`float`） |
| `decbin()` | 十进制（以`int`编码） | 二进制（以`string`编码） |
| `hexdec()` | 十六进制（以`string`编码） | 十进制（以`int`或由于大小原因而是`float`） |
| `dechex()` | 十进制（以`int`编码） | 十六进制（以`string`编码） |
| `octdec()` | 八进制（以`string`编码） | 十进制（以`int`或由于大小原因而是`float`） |
| `decoct()` | 十进制（以`int`编码） | 八进制（以`string`编码） |

请注意，与`base_convert()`不同，专用的基数转换函数通常直接与数值类型一起工作。如果使用严格的类型检查，这将避免在改变基数之前需要从数值类型显式转换为`string`，这在使用`base_convert()`时是必需的。

## 参见

PHP 关于[`base_convert()`](https://oreil.ly/NVsk_)的文档。

¹ 更多关于类型转换的信息，请查阅“类型转换”。

² 异常和错误处理在第十二章中有详细讨论。

³ 关于浮点数之间可接受差异的更多信息，请参阅 Recipe 5.2。

⁴ `NumberFormatter`类本身是 PHP 的[intl](https://oreil.ly/B-85H)扩展的一部分。这个模块不是默认内置的，可能需要安装或启用该扩展才能使用该类。

⁵ 更多关于 PHP 严格类型的信息，请参阅“类型转换”。

⁶ 本地扩展在第十五章中有详细介绍。
