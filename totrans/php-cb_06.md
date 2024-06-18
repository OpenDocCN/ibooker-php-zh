# 第六章 日期与时间

操纵日期和时间是任何语言中最复杂的任务之一，更不用说在 PHP 中了。这只是因为时间是相对的——*现在*对于每个用户都可能不同，并且可能会触发应用程序中不同的行为。

## 对象导向

PHP 开发人员主要通过`DateTime`对象来处理代码。这些对象通过包装特定时间实例提供广泛的功能。您可以计算两个`DateTime`对象之间的差异，转换任意时区，或者从静态对象中添加/减去时间窗口。

此外，PHP 还支持`DateTimeImmutable`对象，它在功能上与`DateTime`相同，但不能直接修改。`DateTime`对象上的大多数方法既返回同一对象又改变其内部状态。`DateTimeImmutable`上的相同方法保持内部状态不变，但返回表示更改结果的*新实例*。

###### 注意

两个日期/时间类都扩展自抽象的`DateTimeInterface`基类，使得这两个类在 PHP 的日期和时间功能中几乎可以互换使用。在本章中，无论何处看到`DateTime`，您都可以改用`DateTimeImmutable`实例，并实现类似甚至相同的功能。

## 时区

任何开发人员面临的最大挑战之一是处理时区，特别是涉及夏令时时。一方面，简化并假设应用程序中的每个时间戳引用相同的时区是很容易的。但这种情况很少发生。

幸运的是，PHP 使得处理时区变得非常容易。每个`DateTime`对象自动嵌入一个时区，通常基于 PHP 运行系统中定义的默认值。您还可以在创建`DateTime`时显式设置时区，从而使您引用的区域和时间变得完全明确。在第 6.9 节中也详细介绍了时区之间的转换，简单而强大。

## Unix 时间戳

许多计算机系统在内部使用 Unix 时间戳来表示日期和时间。这些时间戳表示从 Unix 纪元（1970 年 1 月 1 日 00:00:00 GMT）到给定时间经过的秒数。它们在内存效率上很高，并经常被数据库和编程接口使用。然而，计算自固定日期/时间以来的秒数并不完全用户友好，因此您需要一种可靠的方法在应用程序中在 Unix 时间戳和人类可读日期/时间表示之间进行转换。

PHP 的原生格式化功能使得这一切变得简单直接。类似[`time()`](https://oreil.ly/RBqxh)这样的附加函数直接生成 Unix 时间戳。

下面的示例详细介绍了这些主题，以及其他几个常见的日期/时间相关任务。

# 6.1 查找当前日期和时间

## 问题

您想要知道当前日期和时间。

## 解决方案

要按特定格式打印当前日期和时间，请使用`date()`。例如：

```php
$now = date('r');
```

`date()`的输出取决于其运行所在系统和当前实际时间。使用`r`作为格式字符串，该函数会返回类似以下的内容：

```php
Wed, 09 Nov 2022 14:15:12 -0800
```

同样，新实例化的`DateTime`对象也将表示当前日期和时间。该对象上的`::format()`方法展现出与`date()`相同的行为，这意味着以下两个语句在功能上是相同的：

```php
$now = date('r');

$now = (new DateTime())->format('r');
```

## 讨论

PHP 的`date()`函数以及不带参数实例化的`DateTime`对象将自动继承其运行系统的当前日期和时间。在这两者中额外传入的`r`是一个格式字符，定义了如何将给定的日期/时间信息转换为字符串——在本例中，特别是按照[RFC 2822](https://oreil.ly/WrB1I)格式化的日期。您可以在 Recipe 6.2 中了解更多关于日期格式化的信息。

利用 PHP 的`getdate()`函数，您可以获取当前系统日期和时间的所有部分的关联数组，这个数组将包含在表 6-1 中的键和值。

表 6-1\. `getdate()`返回的关键元素

| 键 | 值的描述 | 示例 |
| --- | --- | --- |
| `seconds` | 秒数 | `0` 到 `59` |
| `minutes` | 分钟 | `0` 到 `59` |
| `hours` | 小时 | `0` 到 `23` |
| `mday` | 月中的天数 | `1` 到 `31` |
| `wday` | 周几 | `0`（星期日）到 `6`（星期六） |
| `mon` | 月份 | `1` 到 `12` |
| `year` | 完整的四位数年份 | `2023` |
| `yday` | 年中的天数 | `0` 到 `365` |
| `weekday` | 星期几 | `Sunday` 到 `Saturday` |
| `month` | 年中的月份 | `January` 到 `December` |
| `0` | Unix 时间戳 | `0` 到 `2147483647` |

在某些应用中，您可能只需要星期几而不是完整操作的`DateTime`对象。考虑示例 6-1，它展示了如何使用`DateTime`或`getdate()`来实现这一目标。

##### 示例 6-1\. 比较`DateTime`与`getdate()`

```php
print (new DateTime())->format('l') . PHP_EOL;

print getdate()['weekday'] . PHP_EOL;
```

这两行代码在功能上是等效的。对于像“打印今天的日期”这样的简单任务，任何一种都足够完成工作。`DateTime`对象提供了转换时区或预测未来日期的功能（这些都在其他配方中进一步介绍）。而`getdate()`返回的关联数组则缺乏这种功能，但通过其简单易识别的数组键来弥补这一缺点。

## 参见

PHP 文档关于[date 和时间函数](https://oreil.ly/rJ9fn)，[`DateTime`类](https://oreil.ly/t28Zh)，以及[`getdate()`函数](https://oreil.ly/Kv7l8)。

# 6.2 日期和时间的格式化

## 问题

您想要将日期打印为特定格式的字符串。

## 解决方案

使用给定的`DateTime`对象的`::format()`方法来指定返回字符串的格式，如示例 6-2 所示。

##### 示例 6-2\. 日期和时间格式示例

```php
$birthday = new DateTime('2017-08-01');

print $birthday->format('l, F j, Y') . PHP_EOL; ![1](img/1.png)
print $birthday->format('n/j/y') . PHP_EOL; ![2](img/2.png)
print $birthday->format(DateTime::RSS) . PHP_EOL; ![3](img/3.png)
```

![1](img/#co_dates_and_times_CO1-1)

`Tuesday, August 1, 2017`

![2](img/#co_dates_and_times_CO1-2)

`8/1/17`

![3](img/#co_dates_and_times_CO1-3)

`Tue, 01 Aug 2017 00:00:00 +0000`

## 讨论

`date()`函数和`DateTime`对象的`::format()`方法都接受多种输入字符串，最终定义了 PHP 生成的字符串的结构。每个格式字符串由表示日期或时间值特定部分的单个字符组成，正如你可以在表 6-2 中看到的那样。

表格 6-2\. PHP 格式字符

| 字符 | 描述 | 示例值 |
| --- | --- | --- |
| **日期** |  |  |
| `d` | 月份中的日期，带有前导零 | `01`到`31` |
| `D` | 一周中某天的文本表示，三个字母 | `Mon`到`Sun` |
| `j` | 月份中的日期，不带前导零 | `1`到`31` |
| `l` | 一周中某天的名称 | `Sunday`到`Saturday` |
| `N` | ISO 8601 表示的星期几 | `1`（代表星期一）到`7`（代表星期日） |
| `S` | 日期的英文序数后缀，两个字符 | `st`, `nd`, `rd`, 或 `th`。与`j`结合使用 |
| `w` | 一周中的日期表示，数字形式 | `0`（代表星期日）到`6`（代表星期六） |
| `z` | 一年中的第几天（从 0 开始） | `0`到`365` |
| **月份** |  |  |
| `F` | 月份的完整名称 | `January`到`December` |
| `m` | 月份的数字表示，带有前导零 | `01`到`12` |
| `M` | 月份的文本表示，三个字母 | `Jan`到`Dec` |
| `n` | 月份的数字表示，不带前导零 | `1`到`12` |
| `t` | 给定月份的天数 | `28`到`31` |
| **年份** |  |  |
| `L` | 是否为闰年 | 如果是闰年则为`1`，否则为`0`。 |
| `o` | ISO 8601 周年份。其值与`Y`相同，但如果 ISO 周属于前一年或后一年，则使用该年份 | `1999`或`2003` |
| `Y` | 年份的完整数字表示，四位数 | `1999`或`2003` |
| `y` | 年份的两位数表示 | `99`或`03` |
| **时间** |  |  |
| `a` | 小写的上午或下午 | `am`或`pm` |
| `A` | 大写的上午或下午 | `AM`或`PM` |
| `g` | 带有前导零的 12 小时制小时数 | `1`到`12` |
| `G` | 24 小时制小时数，不带前导零 | `0`到`23` |
| `h` | 带有前导零的 12 小时制小时数 | `01`到`12` |
| `H` | 带有前导零的 24 小时制小时数 | `00`到`23` |
| `i` | 带有前导零的分钟 | `00`到`59` |
| `s` | 带有前导零的秒数 | `00`到`59` |
| `u` | 微秒 | `654321` |
| `v` | 毫秒 | `654` |
| **时区** |  |  |
| `e` | 时区标识符 | `UTC`、`GMT`、`Atlantic/Azores` |
| `I` | 日期是否处于夏令时 | 夏令时为 `1`，否则为 `0`。 |
| `O` | 与格林尼治时间（GMT）的差异，小时和分钟之间不用冒号分隔 | `+0200` |
| `P` | 与格林尼治时间（GMT）的差异，小时和分钟之间用冒号分隔 | `+02:00` |
| `p` | 与 P 相同，但返回 Z 而不是 +00:00 | `+02:00` |
| `T` | 如果已知则为时区缩写；否则为 GMT 偏移量。 | `EST`、`MDT`、`+05` |
| `Z` | 时间偏移秒数 | `-43200` 到 `50400` |
| **其他** |  |  |
| `U` | Unix 时间戳 | `0` 到 `2147483647` |

将这些字符组合成格式字符串确定了 PHP 如何将给定的日期/时间构造转换为字符串。

类似地，PHP 定义了几个预定义常量，表示众所周知且广泛使用的格式。 表 6-3 显示了一些最有用的常量。

表 6-3\. 预定义日期常量

| 常量 | 类常量 | 格式字符 | 示例 |
| --- | --- | --- | --- |
| `DATE_ATOM` | `DateTime::ATOM` | `Y-m-d\TH:i:sP` | `2023-08-01T13:22:14-08:00` |
| `DATE_COOKIE` | `DateTime::COOKIE` | `l, d-M-Y H:i:s T` | `Tuesday, 01-Aug-2023 13:22:14 GMT-0800` |
| ``DATE_ISO8601`footnote:[不幸的是，`DATE_ISO8601` 不兼容 ISO 8601 标准。如果需要该级别的兼容性，请改用 `DATE_ATOM`。] | `DateTime::ISO8601` | `Y-m-d\TH:i:sO` | `2013-08-01T21:21:14\+0000` |
| `DATE_RSS` | `DateTime::RSS` | `D, d M Y H:i:s O` | `Tue, 01 Aug 2023 13:22:14 -0800` |

## 参见

有关 [格式字符](https://oreil.ly/oQpYP) 和 [预定义 `DateTime` 常量](https://oreil.ly/XJiZy) 的完整文档。

# 6.3 转换日期和时间为 Unix 时间戳

## 问题

您想要将特定日期或时间转换为 Unix 时间戳，并将给定的 Unix 时间戳转换为本地日期或时间。

## 解决方案

要将给定的日期/时间转换为时间戳，请使用 `U` 格式字符（参见 表 6-2），使用 `DateTime::format()` 如下所示：

```php
$date = '2023-11-09T13:15:00-0700';
$dateObj = new DateTime($date);

echo $dateObj->format('U');
// 1699560900
```

要将给定的时间戳转换为 `DateTime` 对象，同样使用 `U` 格式字符，但是要用 `DateTime::createFromFormat()` 如下所示：

```php
$timestamp = '1648241792';
$dateObj = DateTime::createFromFormat('U', $timestamp);

echo $dateObj->format(DateTime::ISO8601);
// 2022-03-25T20:56:32+0000
```

## 讨论

`::createFromFormat()` 方法是 `DateTime` 的 `::format()` 方法的静态逆过程。这两个函数使用相同的格式字符串来指定使用的格式¹，但表示格式化字符串和 `DateTime` 对象的基础状态之间的相反转换。解决方案示例明确利用 `U` 格式字符告知 PHP 输入数据为 Unix 时间戳。

如果输入字符串实际上与您的格式不匹配，PHP 将返回一个字面上的 `false`，如以下示例所示：

```php
$timestamp = '2023-07-23';
$dateObj = DateTime::createFromFormat('U', $timestamp);

echo gettype($dateObj); // false
```

在解析用户输入时，明智的做法是显式检查`::createFromFormat()`的返回值，以确保日期输入是有效的。有关日期验证的更多信息，请参见食谱 6.7。

与其使用完整的`DateTime`对象，不如直接操作日期/时间的*部分*。PHP 的[`mktime()`函数](https://oreil.ly/YFKz0)始终返回 Unix 时间戳，唯一必需的参数是小时。

例如，假设您希望表示 2023 年 7 月 4 日中午在 GMT 时区（无时区偏移）的 Unix 时间戳。您可以通过两种方式实现，如示例 6-3 中所示。

##### 示例 6-3\. 直接创建时间戳

```php
$date = new DateTime('2023-07-04T12:00:00');
$timestamp = $date->format('U'); ![1](img/1.png)

$timestamp = mktime(month: 7, day: 4, year: 2023, hour: 12); ![2](img/2.png)
```

![1](img/#co_dates_and_times_CO2-1)

此输出将完全是`1688472000`。

![2](img/#co_dates_and_times_CO2-2)

此输出将*接近*于`1688472000`，但在最后三位数上会有所变化。

虽然这个简单的例子看起来优雅，避免了只为了再转换为数字而实例化对象的问题，但它有一个重要问题。未指定参数（在本例中为分钟或秒）将导致`mktime()`默认使用当前系统值作为这些参数。如果在下午 3 点 05 分运行此示例代码，则输出可能是`1688472300`。

此 Unix 时间戳在转换回`DateTime`时转换为 12:05:00，而不是应用程序期望的 12:00:00（可能是可忽略的差异）。

重要的是要记住，如果选择利用`mktime()`的函数接口，要么为日期/时间的*每个*组件提供值，要么构建应用程序，以便预期和处理轻微偏差。

## 另请参见

文档中的[`DateTime::createFromFormat()`](https://oreil.ly/otv8q)。

# 6.4 从 Unix 时间戳转换为日期和时间部分

## 问题

您希望从 Unix 时间戳中提取特定的日期或时间部分（天或小时）。

## 解决方案

将 Unix 时间戳作为参数传递给`getdate()`，并引用生成的关联数组中的所需键。例如：

```php
$date = 1688472300;
$time_parts = getdate($date);

print $time_parts['weekday'] . PHP_EOL; // Tuesday
print $time_parts['hours'] . PHP_EOL;   // 12
```

## 讨论

您可以向`getdate()`提供的唯一参数是 Unix 时间戳。如果省略此参数，PHP 将利用当前系统日期和时间。当提供时间戳时，PHP 在内部解析该时间戳，并允许提取所有预期的日期和时间元素。

或者，您可以通过两种方式将时间戳传递到`DateTime`实例的构造函数中以构建一个完整的对象：

1.  在时间戳前加上`@`字符告诉 PHP 将输入解释为 Unix 时间戳，例如，`new DateTime('@1688472300')`。

1.  当导入时间戳到`DateTime`对象时，可以使用`U`格式字符，例如，`DateTime::createFromFormat('U', '1688472300')`。

无论如何，一旦您的时间戳被正确解析并加载到 `DateTime` 对象中，您可以使用其 `::format()` 方法提取任何所需的组件。示例 6-4 是一个使用 `DateTime` 而不是 `getdate()` 的解决方案示例的替代实现。

##### 示例 6-4\. 从 Unix 时间戳中提取日期和时间部分

```php
$date = '1688472300';

$parsed = new DateTime("@{$date}");
print $parsed->format('l') . PHP_EOL;
print $parsed->format('g') . PHP_EOL;

$parsed2 = DateTime::createFromFormat('U', $date);
print $parsed2->format('l') . PHP_EOL;
print $parsed2->format('g') . PHP_EOL;
```

示例 6-4 中的任一方法都可以有效替代 `getdate()`，同时还提供了一个完全可用的 `DateTime` 实例。您可以以任何格式打印日期（或时间），直接操作底层值，或者根据需要在时区之间进行转换。`DateTime` 的这些潜在进一步用途在后续的配方中都有涵盖。

## 参见

食谱 6.1 进一步讨论 `getdate()`。请继续阅读 食谱 6.8 以了解如何操作 `DateTime` 对象，以及 食谱 6.9 中如何直接管理时区。

# 6.5 计算两个日期之间的差异

## 问题

您想知道两个日期或时间之间经过了多少时间。

## 解决方案

将每个日期/时间封装在 `DateTime` 对象中。利用其中一个对象上的 `::diff()` 方法计算它与另一个 `DateTime` 之间的相对差异。结果将是一个 `DateInterval` 对象，如下所示：

```php
$firstDate = new DateTime('2002-06-14');
$secondDate = new DateTime('2022-11-09');

$interval = $secondDate->diff($firstDate);

print $interval->format('%y years %d days %m months');
// 20 years 25 days 4 months
```

## 讨论

`DateTime` 对象的 `::diff()` 方法有效地从另一个日期/时间（作为方法参数传递的日期/时间）中减去另一个日期/时间（对象本身表示的日期/时间）。其结果是两个对象之间时间相对持续的表示。

###### 警告

`::diff()` 方法忽略夏令时。为了正确考虑到该系统固有的潜在一小时差异，首先将两个日期/时间对象转换为 UTC 是一个好主意。

还需要注意的是，尽管解决方案示例中看起来可能类似，但 `DateInterval` 对象的 `::format()` 方法使用的格式字符完全不同于 `DateTime` 使用的格式字符。每个格式字符都必须以字面 `%` 字符为前缀，但格式字符串本身可以包含非格式化字符（例如解决方案示例中的 *年* 和 *月*）。

可用的格式字符在 表 6-4 中列出。除了 `a` 和 `r` 格式字符外，使用小写格式字符将返回一个数字值，没有前导 0。列出的大写格式字符将返回至少两位数，带有前导 0。记住，每个格式字符前面必须加上字面量 `%`。

表 6-4\. `DateInterval` 格式字符

| 字符 | 描述 | 示例 |
| --- | --- | --- |
| `%` | 字面 `%` | `%` |
| `Y` | 年 | `03` |
| `M` | 月 | `02` |
| `D` | 天 | `09` |
| `H` | 小时 | `08` |
| `I` | 分钟 | `01` |
| `S` | 秒 | `04` |
| `F` | 微秒 | `007705` |
| `R` | 在负数时显示`-`，在正数时显示`+` | `-` 或 `+` |
| `r` | 在负数时显示`-`，在正数时为空 | `-` |
| `a` | 总天数 | `548` |

## 参见

完整文档请参见[`DateInterval` 类](https://oreil.ly/r0FBV)。

# 6.6 从任意字符串解析日期和时间

## 问题

你需要将任意用户定义的字符串转换为有效的 `DateTime` 对象，以便进一步使用或操作。

## 解决方案

使用 PHP 强大的 `strtotime()` 函数将文本输入转换为 Unix 时间戳，然后传递给新 `DateTime` 对象的构造函数。例如：

```php
$entry = strtotime('last Wednesday');
$parsed = new DateTime("@{$entry}");

$entry = strtotime('now + 2 days');
$parsed = new DateTime("@{$entry}");

$entry = strtotime('June 23, 2023');
$parsed = new DateTime("@{$entry}");
```

## 讨论

`strtotime()` 的强大之处来自语言支持的底层[日期和时间导入格式](https://oreil.ly/2f4o_)。这些包括你可能期望计算机使用的格式（如 YYYY-MM-DD 表示年、月、日）。但它还扩展到*相对*指定器和复杂的复合格式。

###### 注释

将 Unix 时间戳作为前缀的文本直接传递给 `DateTime` 构造函数的约定，来源于 PHP 支持的复合日期/时间格式。

相对格式是最强大的，支持像这样的人类可读字符串：

+   `yesterday`

+   `first day of`

+   `now`

+   `ago`

掌握这些格式后，你几乎可以用 PHP 解析任何想象得到的字符串。然而，也有一些限制。在解决方案示例中，我使用了 `now + 2 days` 来指定“从现在开始的 2 天后”。示例 6-5 显示，尽管在英语中读起来很好，但在 PHP 中却会导致解析错误。

##### 示例 6-5\. `strtotime()` 解析的限制

```php
$date = strtotime('2 days from now');

if ($date === false) {
    throw new InvalidArgumentException('Error parsing the string!');
}
```

应该始终注意，无论你如何使计算机聪明，你始终受到最终用户提供的输入质量的限制。没有办法预见每种指定日期或时间的可能方式；`strtotime()` 接近，但你也需要处理输入错误。

另一个解析用户提供的日期的潜在方式是 PHP 的 `date_parse()` 函数。与 `strtotime()` 不同，此函数期望一个合理格式化的输入字符串。它也不能完全像相对时间一样处理。示例 6-6 说明了几个可以由 `date_parse()` 解析的字符串。

##### 示例 6-6\. `date_parse()` 示例

```php
$first = date_parse('January 4, 2022'); ![1](img/1.png)

$second = date_parse('Feb 14'); ![2](img/2.png)

$third = date_parse('2022-11-12 5:00PM'); ![3](img/3.png)

$fourth = date_parse('1-1-2001 + 12 years'); ![4](img/4.png)
```

![1](img/#co_dates_and_times_CO3-1)

解析 2022 年 1 月 4 日

![2](img/#co_dates_and_times_CO3-2)

解析 2 月 14 日，但年份为 `null`

![3](img/#co_dates_and_times_CO3-3)

解析日期和时间，但没有时区

![4](img/#co_dates_and_times_CO3-4)

解析日期并存储额外的相对字段

`date_parse()` 不会返回时间戳，而是从输入字符串中提取相关的日期/时间部分，并将它们存储在带有以下键的关联数组中：

+   `year`

+   `month`

+   `day`

+   `hour`

+   `minute`

+   `second`

+   `fraction`

此外，将字符串中的时间相对规范（例如 Example 6-6 中的`+ 12 years`）将在数组中添加一个`relative`键，其中包含有关相对偏移的信息。

所有这些都有助于确定用户提供的日期是否作为实际日期有用。如果`date_parse()`函数遇到任何解析问题，它还将返回警告和错误，这样更容易检查日期是否有效。要了解更多关于检查日期有效性的信息，请阅读 Recipe 6.7。

重新访问 Example 6-5 并利用`date_parse()`显示了为什么 PHP 在解析`2 days from now`作为相对日期时遇到困难的更多信息。考虑以下例子：

```php
$date = date_parse('2 days from now');

if ($date['error_count'] > 0) {
    foreach ($date['errors'] as $error) {
        print $error . PHP_EOL;
    }
}
```

前面的代码将打印`The time zone could not be found in the database`，这表明 PHP 正在*尝试*解析日期，但无法确定语句`from now`中的`from`实际上意味着什么。事实上，检查`$date`数组本身将显示它返回了一个`relative`键。这个相对偏移正确表示了指定的两天，这意味着`date_parse()`（甚至`strtotime()`）能够读取相对日期偏移（`2 days`），但在最后一部分上却无法处理。

这个额外的错误提供了进一步的调试上下文，可能会为应用程序提供给最终用户的某种错误消息提供信息。无论如何，这比`strtotime()`单独返回的简单`false`更有帮助。

## 参见

[`date_parse()`](https://oreil.ly/2CECz)和[`strtotime()`](https://oreil.ly/S7qkH)的文档。

# 6.7 验证日期

## 问题

你想确保一个日期是有效的。例如，你想确保用户定义的生日是日历上的真实日期，而不是像 2022 年 11 月 31 日这样的日期。

## 解决方案

使用 PHP 的`checkdate()`函数如下：

```php
$entry = 'November 31, 2022';
$parsed = date_parse($entry);

if (!checkdate($parsed['month'], $parsed['day'], $parsed['year'])) {
    throw new InvalidArgumentException('Specified date is invalid!');
}
```

## 讨论

`date_parse()`函数已经在 Recipe 6.6 中讨论过，但与`checkdate()`一起使用是新的。这第二个函数尝试验证日期是否根据日历有效。

它检查月份（第一个参数）是否在 1 到 12 之间，年份（第三个参数）是否在 1 到 32,767 之间（PHP 中 2 字节整数的最大值），以及该给定月份和年份的天数是否有效。

`checkdate()`函数正确处理具有 28、30 或 31 天的月份。Example 6-7 显示它还考虑了闰年，验证了二月 29 日是否存在于适当的年份中。

##### 示例 6-7\. 验证闰年

```php
$valid = checkdate(2, 29, 2024); // true

$invalid = checkdate(2, 29, 2023); // false
```

## 参见

PHP 文档关于[`checkdate()`](https://oreil.ly/T2io8)。

# 6.8 添加或减去日期

## 问题

你想对一个固定日期应用特定的偏移量（无论是加法还是减法）。例如，你想通过将天数添加到今天的日期来计算未来的日期。

## 解决方案

使用给定`DateTime`对象的`::add()`或`::sub()`方法分别添加或减去`DateInterval`，如示例 6-8 所示。

##### 示例 6-8\. 简单的`DateTime`添加

```php
$date = new DateTime('December 25, 2023');

// When do the 12 days of Christmas end?
$twelve_days = new DateInterval('P12D');
$date->add($twelve_days);

print 'The holidays end on ' . $date->format('F j, Y');

// The holidays end on January 6, 2024
```

## 讨论

在`DateTime`对象上，`::add()`和`::sub()`方法会分别通过添加或减去给定的间隔来修改对象本身。间隔使用的周期标识指定了该间隔表示的时间量。表 6-5 展示了用于表示间隔的格式字符。

表 6-5\. `DateInterval`使用的周期标识

| 字符 | 描述 |
| --- | --- |
| **周期标识符** |  |
| `Y` | 年 |
| `M` | 月 |
| `D` | 天 |
| `W` | 周 |
| **时间标识符** |  |
| `H` | 小时 |
| `M` | 分钟 |
| `S` | 秒 |

每个格式化的日期间隔周期都以字母`P`开头。接着是该周期中的年/月/日/周数。持续时间中的任何时间元素都以字母`T`作为前缀。

###### 警告

月份和分钟的周期标识都是字母`M`。在时间标识中，这可能导致在识别 15 分钟与 15 个月时产生混淆。如果你打算使用分钟，请确保你的持续时间已正确使用了`T`前缀，以避免在应用程序中出现令人沮丧的错误。

例如，3 周零 2 天的期间将表示为`P3W2D`。4 个月、2 小时和 10 秒的期间将表示为`P4MT2H10S`。同样，1 个月、2 小时和 30 分钟的期间将表示为`P1MT2H30M`。

### 可变性

注意，在示例 6-8 中，调用`::add()`时会修改原始的`DateTime`对象本身。在简单的示例中，这是可以接受的。如果你尝试从相同的起始日期计算*多个*日期偏移量，则`DateTime`对象的可变性会导致问题。

取而代之，你可以利用几乎相同的`DateTimeImmutable`对象。该类实现了与`DateTime`相同的接口，但是`::add()`和`::sub()`方法会返回该类的*新实例*，而不是改变对象本身的内部状态。

在示例 6-9 中考虑两种对象类型的比较。

##### 示例 6-9\. 比较`DateTime`和`DateTimeImmutable`

```php
$date = new DateTime('December 25, 2023');
$christmas = new DateTimeImmutable('December 25, 2023');

// When do the 12 days of Christmas end? $twelve_days = new DateInterval('P12D');
$date->add($twelve_days); ![1](img/1.png)
$end = $christmas->add($twelve_days); ![2](img/2.png)

print 'The holidays end on ' . $date->format('F j, Y') . PHP_EOL;
print 'The holidays end on ' . $end->format('F j, Y') . PHP_EOL;

// When is next Christmas? $next_year = new DateInterval('P1Y');
$date->add($next_year);
$next_christmas = $christmas->add($next_year);

print 'Next Christmas is on ' . $date->format('F j, Y') . PHP_EOL;
print 'Next Christmas is on ' . $next_christmas->format('F j, Y') . PHP_EOL; ![3](img/3.png)

print 'This Christmas is on ' . $christmas->format('F j, Y') . PHP_EOL; ![4](img/4.png)
```

![1](img/#co_dates_and_times_CO4-1)

由于`$date`是一个可变对象，调用其`::add()`方法将直接修改对象。

![2](img/#co_dates_and_times_CO4-2)

由于`$christmas`是不可变的，调用`::add()`将返回一个新对象，必须将其存储在变量中。

![3](img/#co_dates_and_times_CO4-3)

将时间添加到`DateTimeImmutable`后，打印结果对象的数据将呈现正确的数据，因为这个*新*对象是用正确的日期和时间创建的。

![4](img/#co_dates_and_times_CO4-4)

即使调用`:add()`后，`DateTimeImmutable`对象始终包含相同的数据，因为它实际上是不可变的。

不可变对象的优点在于您可以将它们视为常量，并放心地知道没有人在您不注意时会重写日历。唯一的缺点是内存利用率。由于`DateTime`修改单个对象，随着您不断进行更改，内存不一定会增加。然而，每次“修改”`DateTimeImmutable`对象时，PHP 都会创建一个新对象并消耗额外的内存。

在典型的 Web 应用程序中，这里的内存开销几乎可以忽略不计。没有理由*不*使用`DateTimeImmutable`对象。

### 更简单的修改

在类似的路径中，`DateTime`和`DateTimeImmutable`都实现了一个`::modify()`方法，该方法使用人类可读的字符串而不是间隔对象。这允许您从给定对象中查找相对日期，如“上个星期五”或“下周”。

一个很好的例子是美国的感恩节，它在每年 11 月的第四个星期四。您可以使用示例 6-10 中定义的函数轻松计算给定年份中的确切日期。

##### 示例 6-10\. 使用`DateTime`找到感恩节

```php
function findThanksgiving(int $year): DateTime
{
    $november = new DateTime("November 1, {$year}");
    $november->modify('fourth Thursday');

    return $november;
}
```

可以使用不可变日期对象实现相同的功能，如示例 6-11 所示。

##### 示例 6-11\. 使用`DateTimeImmutable`找到感恩节

```php
function findThanksgiving(int $year): DateTimeImmutable
{
    $november = new DateTimeImmutable("November 1, {$year}");
    return $november->modify('fourth Thursday');
}
```

## 参见

[`DateInterval`](https://oreil.ly/KvluE)上的文档。

# 6.9 在时区之间计算时间

## 问题

您想要跨多个时区确定特定时间。

## 解决方案

使用`DateTime`类的`::setTimezone()`方法来更改时区如下：

```php
$now = new DateTime();
$now->setTimezone(new DateTimeZone('America/Los_Angeles'));

print $now->format(DATE_RSS) . PHP_EOL;

$now->setTimezone(new DateTimeZone('Europe/Paris'));

print $now->format(DATE_RSS) . PHP_EOL;
```

## 讨论

时区是应用程序开发者最令人沮丧的事情之一。幸运的是，PHP 允许相对轻松地从一个时区转换到另一个时区。解决方案示例中使用的`::setTimezone()`方法说明了如何仅通过指定所需的时区将任意`DateTime`转换为另一个时区。

###### 注意

请注意，`DateTime`和`DateTimeImmutable`都实现了`::setTimezone()`方法。它们之间的区别在于，`DateTime`会修改底层对象的状态，而`DateTimeImmutable`始终会返回一个*新*对象。

了解代码中可用的时区非常重要。列出所有可用的命名时区过长，但开发人员可以利用`DateTimeZone::listIdentifiers()`列出所有可用的命名时区。如果您的应用程序只关心特定地区，您可以进一步使用该类提供的预定义组常量来简化列表。

例如，`DateTimeZone::listIdentifiers(DateTimeZone::AMERICA)` 返回一个数组，列出了所有在美洲可用的时区。在特定的测试系统上，这个数组列出了 145 个时区，每个时区指向一个主要的本地城市，以帮助识别它们代表的时区。您可以为以下每个地区常量生成可能的时区标识符列表：

+   `DateTimeZone::AFRICA`

+   `DateTimeZone::AMERICA`

+   `DateTimeZone::ANTARCTICA`

+   `DateTimeZone::ARCTIC`

+   `DateTimeZone::ASIA`

+   `DateTimeZone::ATLANTIC`

+   `DateTimeZone::AUSTRALIA`

+   `DateTimeZone::EUROPE`

+   `DateTimeZone::INDIAN`

+   `DateTimeZone::PACIFIC`

+   `DateTimeZone::UTC`

+   `DateTimeZone::ALL`

类似地，您可以使用位运算符从这些常量中构建联合，以检索跨两个或更多地区的所有时区列表。例如，`DateTimeZone::ANTARCTICA | DateTimeZone::ARCTIC` 将表示靠近南极或北极的所有时区。

基础的 `DateTime` 类使您能够实例化一个具有特定时区的对象，而不是接受系统默认设置。只需将一个 `DateTimeZone` 实例作为可选的第二个参数传递给构造函数，新对象将自动设置为正确的时区。

例如，按照 [ISO 8601](https://oreil.ly/rip_R) 格式化的日期时间 `2022-12-15T17:35:53` 表示 2022 年 12 月 15 日下午 5:35，但不反映具体的时区。在实例化 `DateTime` 对象时，您可以轻松指定这是日本东京的时间，如下所示：

```php
$date = new DateTime('2022-12-15T17:35:53', new DateTimeZone('Asia/Tokyo'));

echo $date->format(DateTime::ATOM);
// 2022-12-15T17:35:53+09:00
```

如果要解析的日期时间字符串中缺少时区信息，则提供该时区会使事情更加明确。如果在前面的示例中*未*添加时区标识符，PHP 将假定系统配置的时区。

如果日期时间字符串中存在时区信息，PHP 将忽略第二参数中指定的显式时区，并按照提供的字符串解析。

## 另请参阅

有关 [`::setTimezone()` 方法](https://oreil.ly/dk2gQ) 和 [`DateTimeZone` 类](https://oreil.ly/MkdHB) 的文档。

¹ 格式字符串和可用的格式字符在 Recipe 6.2 中有介绍。

² 您可以使用 `date_default_timezone_get()` 检查系统当前的时区设置。
