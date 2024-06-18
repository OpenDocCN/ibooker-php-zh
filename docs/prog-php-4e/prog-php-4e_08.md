# 第七章：日期和时间

典型的 PHP 开发人员可能需要了解可用的日期和时间函数，比如向数据库记录条目添加日期戳或计算两个日期之间的差异。PHP 提供了 `DateTime` 类，可以同时处理日期和时间信息，以及与之配合使用的 `DateTimeZone` 类。

最近几年来，随着网页门户和社交网络社区（如 Facebook 和 Twitter）的出现，时区管理变得更加突出。能够将信息发布到网站，并让其根据用户所在的世界位置识别你在同一网站上的位置，确实是当今的一个要求。但请记住，像 `date()` 这样的函数会从运行脚本的服务器获取默认信息，因此，除非人类客户告诉你他们在世界上的位置，否则自动确定时区位置可能会非常困难。不过，一旦获取了信息，操作数据就变得很容易（本章稍后将详细讨论时区）。

###### 注意

原始的日期（及相关）函数在 Windows 和某些 Unix 安装中存在时间缺陷。由于底层使用的 32 位有符号整数的特性来管理日期和时间数据，它们无法处理 1901 年 12 月 13 日之前或 2038 年 1 月 19 日之后的日期。因此，建议使用更新的 `DateTime` 类系列以提高准确性。

处理日期和时间的有四个相互关联的类。`DateTime` 类处理日期本身；`DateTimeZone` 类处理时区；`DateInterval` 类处理两个 `DateTime` 实例之间的时间跨度；最后，`DatePeriod` 类处理日期和时间的常规间隔遍历。还有两个不常用的支持类 `DateTimeImmutable` 和 `DateTimeInterface` 属于整个 `DateTime` “家族”，但本章不涵盖这些。

`DateTime` 类的构造函数自然是一切的起点。此方法接受两个参数，时间戳和时区。例如：

```php
$dt = new DateTime("2019-06-27 16:42:33", new DateTimeZone("America/Halifax"));
```

我们创建 `$dt` 对象，为其分配日期和时间字符串作为第一个参数，并使用第二个参数设置时区。在此示例中，我们内联实例化了 `DateTimeZone` 实例，但您也可以将 `DateTimeZone` 对象实例化为其自己的变量，然后在构造函数中使用，如下所示：

```php
$dtz = new DateTimeZone("America/Halifax");
$dt = new DateTime("2019-06-27 16:42:33", $dtz);
```

显然，我们正在为这些类分配硬编码的值，而这种信息类型可能并非始终可供您的代码使用，或者可能不是您想要的。或者，我们可以从服务器获取时区值，并在 `DateTimeZone` 类中使用。要获取当前服务器值，请使用类似以下代码的代码：

```php
$tz = ini_get('date.timezone');
$dtz = new DateTimeZone($tz);
$dt = new DateTime("2019-06-27 16:42:33", $dtz);
```

这些代码示例为两个类 `DateTime` 和 `DateTimeZone` 设定了一组值。最终，你将在脚本的其他地方以某种方式使用这些信息。`DateTime` 类的一个方法是 `format()`，它使用与 `date_format()` 函数相同的格式输出代码。这些日期格式代码都列在附录中，位于 `date_format()` 函数的部分。这里是 `format()` 方法作为输出发送到浏览器的示例：

```php
echo "date: " . $dt->format("Y-m-d h:i:s");
`date``:` `2019``-``06``-``27` `04``:``42``:``33`
```

到目前为止，我们已经提供了日期和时间给构造函数，但有时您还希望从服务器获取日期和时间值。要做到这一点，只需将字符串 `"now"` 作为第一个参数提供。

下面的代码与其他示例相同，除了这里我们从服务器获取日期和时间类的值。实际上，由于我们从服务器获取信息，类的属性更加充分（请注意，某些 PHP 实例可能未设置此参数，因此将返回错误，且服务器的时区可能与您的时区不匹配）：

```php
$tz = ini_get('date.timezone');
$dtz = new DateTimeZone($tz);
$dt = new DateTime("now", $dtz);

echo "date: " . $dt->format("Y-m-d h:i:s");
`date``:` `2019``-``06``-``27` `04``:``02``:``54`
```

`DateTime` 的 `diff()` 方法做你可能期望的事情 —— 它返回两个日期之间的差异。方法的返回值是 `DateInterval` 类的一个实例。

要获取两个 `DateTime` 实例之间的差异，请使用：

```php
$tz = ini_get('date.timezone');
$dtz = new DateTimeZone($tz);

$past = new DateTime("2019-02-12 16:42:33", $dtz);
$current = new DateTime("now", $dtz);

// creates a new instance of DateInterval $diff = $past->diff($current);

$pastString = $past->format("Y-m-d");
$currentString = $current->format("Y-m-d");
$diffString = $diff->format("%yy %mm, %dd");

echo "Difference between {$pastString} and {$currentString} is {$diffString}";
`Difference` `between` `2019``-``02``-``12` `and` `2019``-``06``-``27` `is` `0``y` `4``m``,` `14``d`
```

`diff()` 方法是在一个 `DateTime` 对象上调用的，参数是另一个 `DateTime` 对象。然后我们使用 `format()` 方法准备浏览器输出。

注意，`DateInterval` 类也有一个 `format()` 方法。由于它处理两个日期之间的差异，格式字符代码与 `DateTime` 类略有不同。每个字符代码之前都要加上百分号 `%`。可用的字符代码在表 7-1 中提供。

表 7-1\. DateInterval 格式控制字符

| 字符 | 格式化效果 |
| --- | --- |
| `a` | 天数（例如，23） |
| `d` | 天数（不包括已包含在月数中的天数） |
| `D` | 天数，如果小于 10 天则包括前导零（例如，02 和 125） |
| `f` | 数字微秒（例如，6602 或 41569） |
| `F` | 数字微秒，前导零，至少六位数长度（例如，006602 或 041569） |
| `h` | 小时数 |
| `H` | 小时数，如果小于 10 小时则包括前导零（例如，12 和 04） |
| `i` | 分钟数 |
| `I` | 分钟数，如果小于 10 分钟则包括前导零（例如，05 和 33） |
| `m` | 月数 |
| `M` | 月数，如果小于 10 个月则包括前导零（例如，05 和 1533） |
| `r` | 如果差异为负数，则为 `–`；如果差异为正数，则为空 |
| `R` | 如果差异为负数，则为 `–`；如果差异为正数，则为 `+` |
| `s` | 秒数 |
| `S` | 秒数的数量，如果少于 10 秒，则包括前导零（例如，05 和 15） |
| `y` | 年份的数量 |
| `Y` | 年份的数量，如果少于 10 年，则包括前导零（例如，00 和 12） |
| `%` | 百分号 `%` |

现在让我们更仔细地看看`DateTimeZone`类。可以使用`get_ini()`方法从*php.ini*文件中获取时区设置。使用`getLocation()`方法可以从时区对象获取更多信息。它提供时区的原始国家、经度和纬度，以及一些注释。通过这几行代码，您可以开始开发基于网络的 GPS 系统：

```php
$tz = ini_get('date.timezone');
$dtz = new DateTimeZone($tz);

echo "Server's Time Zone: {$tz}<br/>";

foreach ($dtz->getLocation() as $key => $value) {
 echo "{$key} {$value}<br/>";
}
`Server``'``s` `Time` `Zone``:` `America``/``Halifax`
`country_code` `CA`
`latitude` `44.65`
`longitude` `-``63.6`
`comments` `Atlantic` `-` `NS` `(``most` `areas``);` `PE`
```

如果您想设置除服务器时区外的时区，必须将该值传递给`DateTimeZone`对象的构造函数。以下是一个示例，将时区设置为意大利罗马，并使用`getLocation()`方法显示信息：

```php
$dtz = new DateTimeZone("Europe/Rome");

echo "Time Zone: " . $dtz->getName() . "<br/>";

foreach ($dtz->getLocation() as $key => $value) {
 echo "{$key} {$value}<br/>";
}

`Time` `Zone``:` `Europe``/``Rome`
`country_code` `IT`
`latitude` `41.9`
`longitude` `12.48333`
`comments`
```

可以在[PHP 在线手册](https://oreil.ly/EDpf6)中找到按全球地区分类的有效时区名称列表。

使用相同的技术，您可以通过为访问者提供一个支持的时区列表，并通过`ini_set()`函数临时调整您的*php.ini*设置，使网站对访问者“本地化”。

尽管我们在本章讨论的类提供了相当多的日期和时间处理能力，但这只是冰山一角。请务必在 PHP 官网上进一步了解这些类及其功能。

# 接下来是什么

在设计 PHP 网站时，除了日期管理之外，还有很多需要理解的内容，因此可能会遇到许多问题，增加烦恼和 PITA（让人头疼的事）因素。下一章提供了多种技巧、窍门以及一些需要注意的问题，帮助减少这些痛点。涵盖的主题包括处理变量、管理表单数据以及使用 SSL（安全套接层）保护网络数据。做好准备吧！
