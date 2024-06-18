# 第十章：PHP 8 和 MySQL 8 的新特性

到 21 世纪第二个十年结束时，PHP 和 MySQL 都已经成熟到了它们的第八个版本，并且按照软件技术标准可以被认为是高度成熟的产品。

根据[w3techs.com](https://tinyurl.com/w3tpl)网站的说法，到 2021 年初，PHP 在各种网站中以某种方式被使用，超过了 79%，远远超过最接近的竞争对手 ASP.NET 的 70%。[explore-group.com](https://tinyurl.com/egctmpd) 报告称，2019 年 MySQL 仍然是网页上使用最广泛的数据库，安装在 52%的网站上。尽管 MySQL 的市场份额在近年来有所下降，但它仍然比最接近的竞争对手 PostgreSQL 多 16%，后者在 36%的网站上使用。似乎这种情况在可预见的未来将继续存在，特别是由于几乎相同的 MariaDB 也在市场中占有一定比例。

随着这两种技术的最新 8 版本，以及 JavaScript，这些现代 Web 开发的支柱看起来将在未来多年内继续保持重要性。因此，让我们来看看最新版本的 PHP 和 MySQL 中有什么新功能。

# 关于本章

本书版本准备时，这两种技术的最新版本已经发布，因此本章更多地是一个总结或更新。它不仅包含对初学者和中级 PHP 和 MySQL 开发人员有用的信息，还利用机会详细介绍了这两种技术中所做的一些更高级的改进。

如果以下任何内容是您在本书或其他地方尚未涉及的话题，请不要担心；您还不需要（甚至不需要）在您的开发工具包中使用它。但对于那些您以前见过的，您将理解新增或更改的内容，并有一些如何利用它的提示。

本书的未来版本将在教程的主体中包含对新开发者最有用的部分。

# PHP 8

PHP 8 的第 8 版标志着一个重大更新和一个重要的里程碑，为以下内容带来了许多新功能：类型，系统，语法错误处理，字符串，面向对象编程等等。

新功能使得 PHP 比以往更加强大和易于使用，同时最大限度地减少可能会破坏或修改现有安装的更改。

例如，命名参数，即时（JIT）编译，属性和构造函数属性带来了重大的改进和语法变化，改进的错误处理，以及操作符的变化和改进，以帮助减少忽视错误的机会。

# PHP8 和 AMPPS 堆栈

在撰写本文时，建议您在第二章中安装的 AMPPS 堆栈版本包含 MySQL 8.0.18，但其 PHP 版本仍为 7.3.11。在本书出版过程中，预计 AMPPS 堆栈将包含 PHP 8 的版本，并建议您让 AMPPS 为您处理安装，特别是如果您是初学者。但如果 AMPPS 尚未提供 PHP 8，而您希望立即开始使用 PHP 8，您可以直接从[php.net](http://php.net)网站下载更新版本，并按照提供的说明安装和使用它。

## 命名参数

除了传统的位置参数外，在函数调用中还可以使用命名参数，就像这样：

```php
str_contains(needle: 'ian', haystack: 'Antidisestablishmentarianism');
```

这使得函数（或方法）参数名成为公共 API 的一部分，并允许您根据它们的参数名而不是参数顺序将输入数据传递到函数中，大大提高了代码清晰度。因此，由于参数是按名称传递的，它们的顺序不再重要，这应该会减少难以找到的错误。顺便说一句，`str_contains`（稍后解释）也是 PHP 8 的新功能。

## 属性

在 PHP 8 中，属性（如属性和变量）映射到 PHP 类名，允许在类、方法和变量中包含元数据。以前，您必须使用 DocBloc 注释来推断它们。

属性是一种将一个系统或程序的细节提供给另一个系统（如插件或事件系统）的方法，是简单的带有`#[Attribute]`注释的类，可以附加到类、属性、方法、函数、类常量或函数/方法参数上。

在运行时，属性不起任何作用，对代码没有影响。但是，它们可以通过反射 API 使用，允许其他代码检查属性并采取额外的操作。

您可以在[PHP.net 概述](https://tinyurl.com/php8attr)中找到有关属性（有些超出本书范围）的很好解释。

只要它们在一行上，属性在较旧的 PHP 版本中被解释为注释，因此安全地被忽略。

## 构造函数属性

使用 PHP 8，您现在可以直接从类构造函数中声明类属性，从而节省大量重复代码。例如，看看这段代码：

```php
class newRecord
{
`    public` `string` `$username``;`
`    public` `string` `$email``;`

     public function __construct(
        string $username,
        string $email,
    ) {
        $this->username = $username;
        $this->email    = $email;
    }
}

```

使用构造函数属性，您现在可以将所有这些减少到以下内容：

```php
class newRecord
{
     public function __construct(
`        public` `string` `$username``,`
`        public` `string` `$email``,`
    ){}
}
```

这是一个不兼容的特性，所以只有在确保已安装 PHP 8 的情况下才使用它。

## 即时编译

启用时，即时（JIT）编译并缓存本机指令（而不是所谓的 OPcache，它保存文件解析时间），以提供性能提升给 CPU 密集型应用程序。

您可以像这样在*php.ini*文件中启用 JIT： 

```php
opcache.enable          = 1
opcache.jit_buffer_size = 100M
opcache.jit             = tracing

```

请记住，JIT 对 PHP 来说是相对较新的，它目前使得调试和性能分析更加困难。此外，在初始发布前的一天，JIT 还存在问题，因此要注意可能在系统中存在一些未发现的 bug，在此期间最好禁用 JIT 编译，并且在调试时应该保持禁用。

## 联合类型

在 PHP 8 中，类型声明可以扩展为联合类型，以声明多个类型（也支持`false`作为布尔值`false`的特殊类型），例如：

```php
function parse_value(`string``|``int``|``float`): string|null {}

```

## 空安全操作符

在以下内容中，`?->` 空安全操作符会在遇到`null`值时立即返回`null`，而不会导致错误，并且会短路后续的代码块：

```php
return $user->getAddress()?->getCountry()?->isoCode;
```

以前，你可能需要对每个部分使用多个连续的`isset()`调用进行测试，以检查它们是否为`null`值。

## match 表达式

`match`表达式类似于`switch`语句块，但提供了类型安全的比较，支持返回值，不需要`break`语句跳出，同时支持多个匹配值。因此，这个相对繁琐的`switch`块：

```php
`switch` ($country)
{
  case "UK":
  case "USA":
  case "Australia":
  default:
      $lang = "English";
      break;
  case "Spain":
      $lang = "Spanish";
      break;
  case "Germany":
  case "Austria":
      $lang = "German";
      break;
}

```

可以用以下简单得多的`match`表达式来替代：

```php
$lang = `match`($country)
{
  "UK", "USA", "Australia", default => "English",
  "Spain"                           => "Spanish",
  "Germany", "Austria"              => "German",
};
```

关于类型安全比较，`switch`语句对以下代码会感到困惑，因为它将`'0e0'`视为零的指数值，并输出`'null'`，而实际上应该输出`'a'`。然而，`match`不会出现这个问题。

```php
$a = '0e0';

switch ($a)
{
  case    0 : echo 'null'; break;
  case '0e0': echo 'a';    break;
}
```

## 新函数

PHP 8 提供了许多新函数，提供了更多功能和语言改进，涵盖字符串处理、调试和错误处理等领域。

### str_contains

`str`函数将返回一个字符串是否包含在另一个字符串中。它比`strpos`函数更好，因为`strpos`返回一个字符串在另一个字符串中的位置，如果未找到则返回`false`。但是存在一个潜在的问题，如果字符串在位置零被找到并且返回值为 0，则使用`==`进行比较时会被解释为`false`，除非使用严格比较运算符（`===`）。

因此，使用`strpos`时，你需要编写以下不太清晰的代码：

```php
if (`strpos`('Once upon a time', 'Once') !== false)
  echo 'Found';
```

而使用`str_contains`的代码如下所示，在快速扫描（和编写）代码时更加清晰，不太可能导致隐蔽的 bug：

```php
if (`str_contains`('Once upon a time', 'Once'))
  echo 'Found';
```

`str_contains`函数是区分大小写的，所以如果需要进行不区分大小写的检查，应该先将`$needle`和`$haystack`都通过函数转换为小写，例如`strtolower`，像这样：

```php
if (str_contains(`strtolower`('Once upon a time'),
                 `strtolower`('`o`nce')))
  echo 'Found';
```

如果您希望使用`str_contains`并确保您的代码与旧版本的 PHP 兼容，您可以使用兼容性解决方案（提供您的代码期望的功能的代码），创建自己的`str_contains`函数版本（如果尚不存在）。

# 帮助使用兼容性解决方案

而不是编写自己的兼容性解决方案，可能会无意中引入错误或与其他人的兼容性解决方案不兼容，您可以使用在[GitHub](https://github.com/symfony/polyfill-php80)上免费提供的 PHP 8 Symfony 兼容性解决方案包。

在以下 PHP 7+的兼容性解决方案函数中，对于`$needle`为空字符串的检查是必需的，因为 PHP 8 认为空字符串存在于每个字符串的每个位置（甚至包括在空字符串中），因此此行为必须与替换函数匹配：

```php
if (!function_exists('str_contains'))
{
  function str_contains(string $haystack, string $needle): bool
  {
    return $needle === '' || strpos($haystack, $needle) !== false;
  }
}
```

### str_starts_with

`str_starts_with`函数提供了一种更清晰的方法来检查一个字符串是否以另一个字符串开头。以前，您可能会使用`strpos`函数并检查它是否返回零值，但是由于我们已经看到在某些情况下 0 和`false`可能会混淆，`str_starts_with`显著减少了这种可能性。您可以像这样使用它：

```php
if (`str_starts_with`('In the beginning', 'In'))
  echo 'Found';
```

就像`str_contains`一样，测试是区分大小写的，因此在两个字符串上使用类似`strtolower`的函数执行不区分大小写的测试。PHP 7+的此函数的兼容性解决方案可能如下所示：

```php
if (!function_exists('str_starts_with'))
{
  function str_starts_with(string $haystack, string $needle): bool
  {
    return $needle === '' || strpos($haystack, $needle) === 0;
  }
}
```

由于 PHP 8 认为空字符串存在于字符串的每个位置，因此如果`$needle`为空字符串，此兼容性解决方案始终返回`true`。

### str_ends_with

此函数提供了一种更清晰和更简单的方法来检查一个字符串是否以另一个字符串结尾。以前，您可能会使用`substr`函数，并传递`$needle`的负长度，但是`str_ends_with`使得这个任务变得简单得多。您可以像这样使用它：

```php
if (`str_ends_with`('In the end', 'end'))
  echo 'Found';
```

就像其他新字符串函数一样，测试是区分大小写的，因此在两个字符串上使用类似`strtolower`的函数执行不区分大小写的测试。PHP 7+的此函数的兼容性解决方案可能如下所示：

```php
if (!function_exists('str_ends_with'))
{
  function str_ends_with(string $haystack, string $needle): bool
  {
    return $needle === '' ||
           $needle === substr($haystack, `-`strlen($needle));
  }
}
```

如果传递给`substr`的第二个参数为负数（如本例），则从其末尾向后工作匹配该数字的字符数。同样，如果`$needle`是空字符串，则此兼容性解决方案始终返回`true`。还要注意使用`===`严格比较运算符确保在两个字符串之间进行精确比较。

### fdiv

新的`fdiv`函数类似于现有的`fmod`（在除法后返回浮点余数）和`intdiv`（返回除法的整数商）函数，但允许除以 0 而不发出除以零错误，而是返回其中之一的值：`INF`，`-INF`或`NAN`，具体取决于情况。

例如，`intdiv(1, 0)` 将会发出除零错误，`1 % 0` 或者简单地 `1 / 0` 也一样。但是你可以安全地使用 `fdiv(1, 0)`，结果将是一个带有值 `INF` 的浮点数，而 `fdiv(-1, 0)` 返回 `-INF`。

这是一个 PHP 7+ 的兼容解决方案，您可以使用它使您的代码向后兼容：

```php
if (!function_exists('fdiv'))
{
  function fdiv(float $dividend, float $divisor): float
  {
    return @($dividend / $divisor);
  }
}
```

### get_resource_id

PHP 8 添加了新的 `get_resource_id` 函数，类似于将 `(int) $resource` 转换以更轻松地获取资源 ID，但是返回类型被检查为资源，返回值为整数，因此它们是类型安全的。

### get_debug_type

`get_debug_type` 函数提供的值比现有的 `gettype` 函数更一致，并且最适合用于在异常消息或日志中获取意外变量的详细信息，因为它更冗长并提供额外信息。有关更多信息，请参阅[Wiki](https://wiki.php.net/rfc/get_debug_type)。

### preg_last_error_msg

PHP 的 `preg_` 函数不会抛出异常，因此如果有错误，你必须使用 `preg_last_error` 来获取错误代码。但是如果你想要一个友好的错误消息而不仅仅是一个未解释的代码，在 PHP 8 中现在可以调用 `preg_last_error_msg`。如果没有错误，则返回“没有错误”。

由于本书面向初学者到中级水平，我只是真正触及了 PHP 8 中所有伟大新功能的皮毛，让您品尝可以立即开始使用的主要功能。然而，如果您渴望学习关于这个里程碑更新的所有内容，您可以在[官方网页](https://www.php.net/releases/8.0)上获取完整详情。

# MySQL 8

MySQL 8 首次发布于 2018 年，在本书之前的版本中无法覆盖更新中包含的功能。因此，现在，随着最新更新（至 8.0.22）在 2020 年底的发布，是一个很好的机会，了解 MySQL 8 相比早期版本提供的所有功能，如更好的 Unicode 支持、更好的 JSON 和文档处理、地理支持以及窗口函数。

在此摘要中，您将获得最新版本 8 中已经改进、升级或添加的八个领域的概述。

###### 注意

MySQL 8 是版本 5.7 的继任者，因为版本 6 从未真正被开发社区接受，而当 Sun Microsystems 收购 MySQL 时，版本 6 的开发被中止。更重要的是，在版本 8 之前，最大的变化是从 5.6 到 5.7，因此根据 Sun 的说法，“由于我们在这个 MySQL 版本中引入了许多新的重要功能，我们决定启动一个全新的系列。由于 MySQL 之前实际上已经使用了系列号 6 和 7，所以我们选择了 8.0。”

## SQL 更新

MySQL 8 现在引入了窗口函数（也称为*分析函数*）。它们类似于分组聚合函数，将行集的计算折叠为单行。然而，窗口或分析函数对结果集中的每一行执行聚合操作，是非聚合的。因为它们不是 MySQL 使用的核心部分，并且应被视为高级扩展，所以本书不涵盖它们。

新函数包括`RANK`、`DENSE_RANK`、`PERCENT_RANK`、`CUME_DIST`、`NTILE`、`ROW_NUMBER`、`FIRST_VALUE`、`LAST_VALUE`、`NTH_VALUE`，如果需要了解更多信息，可以在 MySQL 的[官方网站](https://tinyurl.com/mysql8winfuncs)上找到完整文档。

MySQL 8 还引入了递归通用表达式（CTE）、增强的 SQL 锁定子句中的`NOWAIT`和`SKIP LOCKED`的替代方法、降序索引、`GROUPING`函数和优化器提示。

所有这些以及更多内容可以在[MySQL 网站](https://tinyurl.com/mysql8statements)上查看。

## JSON（JavaScript 对象表示法）

有多个新函数用于处理 JSON，同时 JSON 值的排序和分组已得到改进。此外，路径表达式中的范围扩展语法和排序改进，还有新的表、聚合、合并和其他函数。

鉴于 MySQL 的这些改进和 JSON 的使用，可以认为 MySQL 现在可以用来替代 NoSQL 数据库。

使用 JSON 在 MySQL 中的使用超出了本书的范围，但如果这是你感兴趣的领域，你可以参考[所有新特性的官方文档](https://tinyurl.com/mysql8json)。

## 地理信息支持

MySQL 8 还引入了 GIS（地理信息系统）支持，包括对 SRS（空间参考系统）的元数据支持、SRS 数据类型、索引和函数。这意味着 MySQL 现在可以（例如）使用任何支持的空间参考系统中的纬度和经度坐标计算地球表面上两点之间的距离。

若要详细了解如何访问 MySQL GIS，可以参考[官方网站](https://tinyurl.com/mysql8gis)。

## 可靠性

MySQL 已经非常可靠，但在版本 8 中进一步改进，通过将其元数据存储在 InnoDB 事务存储引擎中，使得*用户*、*权限*和*字典*表现在都位于`InnoDB`中。

在 MySQL 8 中，现在只有一个数据字典，而在 5.7 版本及更早版本中有两个数据字典（一个用于服务器，一个用于`InnoDB`层），这可能导致不同步问题。

从版本 8 开始，用户可以确保任何 DDL 语句（如`CREATE TABLE`）要么完全执行，要么完全不执行，以防止主副本服务器可能出现不同步的情况。

## 速度和性能

在 MySQL 中，通过在`InnoDB`中将表存储为简单视图的数据字典表上，信息模式的速度提高了多达 100 倍。此外，对性能模式表添加了超过 100 个索引，进一步提高了性能，读写速度更快，适用于 I/O 密集型工作负载和高竞争工作负载，此外，现在可以将用户线程映射到 CPU 以进一步优化性能。

MySQL 8 在处理大量写入工作负载时，与版本 5.7 相比，性能提升了多达四倍，并对读写工作负载提供了更显著的增长。

使用 MySQL，您可以充分利用每个存储设备的能力，提高在高竞争工作负载下的性能（事务排队等待锁的情况）。

总的来说，MySQL 8 的开发人员称其速度提高了最多两倍，您可以在[官方网站](https://tinyurl.com/mysql8performance)了解他们的理由和如何在应用程序中实现这一性能增加的技巧。

## 管理

使用 MySQL 8，您现在可以在可见和不可见之间切换索引。不可见索引在创建查询计划时不被优化器考虑，但仍在后台维护，因此很容易使其再次可见，让您决定是否可以删除索引。

此外，现在用户完全控制 Undo 表空间，并且现在可以持久保存在服务器重启时会丢失的全局动态服务器变量。另外还有一个 SQL `RESTART`命令，允许通过 SQL 连接远程管理 MySQL 服务器，并提供了一个改进的`RENAME COLUMN`命令，以取代先前的`ALTER TABLE...CHANGE`语法。

更多详细信息，请参阅[官方网站](https://tinyurl.com/mysql8serveradmin)。

## 安全

安全性在计划第 8 版时绝对没有被忽视，因为有许多新的改进。

首先，从`mysql_native_password`更改为`caching_sha2_password`作为默认认证插件，并且在企业版和现在的社区版中选择了 OpenSSL 作为默认的 TLS/SSL 库，并且是动态链接的。

使用 MySQL 8，现在 Undo 和 Redo 日志已加密，实现了 SQL 角色，您可以向用户授予角色和角色的权限。在创建新用户时还可以使用强制角色。现在有一个安全存储的密码历史的全局或用户级密码轮换策略。

在认证过程中，根据连续失败的登录尝试，MySQL 8 增加了延迟，以减缓暴力攻击尝试。延迟触发和最大延迟长度是可配置的。

有关 MySQL 和安全性的更多信息，请访问[官方网站](https://tinyurl.com/mysql8security)。

# 问题

1.  当声明类属性时，PHP 8 现在允许您做什么？

1.  什么是空安全操作符，它有什么作用？

1.  如何在 PHP 8 中使用`match`表达式，并且它为什么比替代方法更好？

1.  PHP 8 中现在可以使用什么简单易用的新函数来确定一个字符串是否存在于另一个字符串中？

1.  在 PHP 8 中，如何进行浮点数除法计算，而不会导致除以零错误？

1.  什么是 polyfill？

1.  PHP 8 中，查看由`preg_`函数调用生成的最新错误的简单新方法是什么？

1.  MySQL 8 默认使用什么作为其事务性存储引擎？

1.  在 MySQL 8 中，你可以使用什么命令来代替`ALTER TABLE...CHANGE TABLE`来修改列名？

1.  MySQL 8 中默认的认证插件是什么？

参见 “第十章答案”，在 附录 A 中找到这些问题的答案。
