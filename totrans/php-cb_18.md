# 第十八章 PHP 命令行

开发者们来自各种背景，具有不同水平的软件开发经验，他们选择 PHP。无论你是一名新的计算机科学毕业生，一名经验丰富的开发者，还是来自非编程领域并希望学习新技能的人，这种语言的宽容性使得入门变得容易。话虽如此，对于这些非编程初学者来说，最大的障碍可能是 PHP 的命令行界面。

非编程初学者可能习惯于使用图形用户界面，并通过鼠标和图形显示器进行导航。给这样的用户一个命令行终端，他们可能会对界面感到困惑或者感到害怕。

作为后端语言，PHP 经常在命令行中操作。这可能使得对于不习惯基于文本的界面的开发者来说，这是一种令人生畏的语言。幸运的是，基于 PHP 的命令行应用相对简单构建，使用起来非常强大。

一个应用程序可能会暴露一个类似于其默认 RESTful 接口的命令面板，从而使得从终端进行交互类似于通过浏览器或通过 API 进行交互。另一个应用程序可能会将其管理工具隐藏在 CLI 中，以防止不太技术的最终用户意外损坏应用程序。

当今市场上最流行的 PHP 应用之一是[WordPress](https://wordpress.org)，这是一个开源的博客和网络平台。大多数用户通过其图形化网络界面与平台互动，但 WordPress 社区还维护着一个丰富的命令行界面：[WP-CLI](https://wp-cli.org)。这个工具允许用户通过可脚本化的文本终端界面管理图形工具已经暴露的一切。它还提供了用于管理用户角色、系统配置、数据库状态甚至系统缓存的命令。所有这些功能都不存在于默认的网络界面中！

今天构建 PHP 应用程序的任何开发者都可以并且应该了解命令行的功能，无论是关于 PHP 本身的功能，还是关于您的应用程序如何通过相同的界面暴露其功能。一个真正丰富的 Web 应用程序最终将在可能不会暴露任何图形界面的服务器上运行，因此能够从终端控制应用程序不仅仅是一种强大的举措，而且是一种必要性。

以下示例揭示了参数解析、管理输入和输出的复杂性，甚至利用扩展构建在控制台中运行的完整应用程序。

# 18.1 解析程序参数

## 问题

当用户调用您的脚本时，您希望用户传递一个参数，以便在应用程序内部解析。

## 解决方案

使用 `$argc` 整数和 `$argv` 数组直接在脚本中检索参数的值。例如：

```php
<?php
if ($argc !== 2) {
    die('Invalid number of arguments.');
}

$name = htmlspecialchars($argv[1]);

echo "Hello, {$name}!" . PHP_EOL;
```

## 讨论

假设在示例解决方案中将脚本命名为 *script.php*，则可以通过以下命令在终端会话中调用它：

```php
% php script.php World
```

在内部，`$argc` 变量包含了执行 PHP 脚本时传递的参数数量计数。在示例解决方案中，确切地有两个参数：

+   脚本本身的名称（*script.php*）

+   无论你在脚本名称后传递了什么字符串值

###### 注意

可以通过在 *php.ini* 文件中将 [`reg⁠ister_argc_argv` 标志](https://oreil.ly/ZKulH) 设置为 `false` 来在运行时禁用 `$argc` 和 `$argv`。如果启用了这些参数，它们将包含传递给脚本的参数或者从 Web 服务器转发的 GET 请求的信息。

第一个参数将*始终*是正在执行的脚本或文件的名称。除此之外的所有参数都是通过空格分隔的。如果需要传递复合参数（例如带有空格的字符串），请用双引号括起来。例如：

```php
% php script.php "dear reader"
```

更复杂的实现可能会利用 PHP 的 `getopt()` 函数而不是直接操作参数变量。此函数将解析短选项和长选项，并将它们的内容传递给你的应用程序可以利用的数组中。

短选项是在命令行上用单个破折号表示的每个单字符选项，例如 `-v`。每个选项可以仅仅是存在（作为标志）或者后面跟着数据（作为选项）。

长选项以双破折号开头，但在其他方面与它们的短选项同样有效。你可以假设应用程序中存在任何一种或两种样式的选项，并根据需要使用它们。

###### 注意

通常，命令行应用程序将为同一功能提供长选项和单字符快捷方式。例如，`-v` 和 `--verbose` 经常用于控制脚本的输出级别。使用 `getopt()`，你可以轻松获取两者，但 PHP 不会将它们关联起来。如果你支持两种不同的方法来提供相同的选项值或标志，你需要在脚本中手动协调它们。

`getopt()` 函数接受三个参数，并返回表示 PHP 解释器已解析的选项的数组：

+   第一个参数是一个单个字符串，在其中每个字符表示一个短选项或标志。

+   第二个参数是一个字符串数组，每个字符串是一个长选项名称。

+   最后一个参数，*这个参数是通过引用传递的*，是一个整数，表示在 PHP 遇到非选项时停止解析的 `$argv` 索引。

短选项和长选项都接受修饰符。如果仅传递一个选项，PHP 将不接受该选项的值，但会将其视为标志。如果在选项后添加一个冒号，PHP 将*要求*一个值。如果添加两个冒号，PHP 将将值视为可选的。

作为例子，Table 18-1 列出了短选项和长选项如何利用这些附加元素。

表 18-1\. PHP `getopt()` 参数

| 参数 | 参数类型 | 描述 |
| --- | --- | --- |
| `a` | 短选项 | 无值的单个标志：`-a` |
| `b:` | 短选项 | 需要值的单个标志：`-b value` |
| `c::` | 短选项 | 可选值的单个标志：`-c value` 或 `-c` |
| `ab:c` | 短选项 | 组合三个标志，其中 `a` 和 `c` 没有值，但 `b` 需要一个值：`-a -b value -c` |
| `verbose` | 长选项 | 无值的选项字符串：`--verbose` |
| `name:` | 长选项 | 带有必需值的选项字符串：`--name Alice` |
| `output::` | 长选项 | 带有可选值的选项字符串：`--output file.txt` 或 `--output` |

为了说明选项解析的实用性，定义一个程序，如 Example 18-1，它暴露了短选项和长选项，但也利用了标志后的自由形式（非选项）输入。以下脚本将期望如下：

+   控制输出是否大写的标志（`-c`）

+   用户名（`--name`）

+   一些额外的、随意的文本选项后

##### 示例 18-1\. 直接演示`getopt()`与多个选项的情况

```php
<?php
$optionIndex = 0;

$options = getopt('c', ['name:'], $optionIndex); ![1](img/1.png)

$firstLine = "Hello, {$options['name']}!" . PHP_EOL; ![2](img/2.png)

$rest = implode(' ', array_slice($argv, $optionIndex)); ![3](img/3.png)

if (array_key_exists('c', $options)) { ![4](img/4.png)
    $firstLine = strtoupper($firstLine);
    $rest = strtoupper($rest);
}

echo $firstLine;
echo $rest . PHP_EOL;
```

![1](img/#co_php_command_line_CO1-1)

使用 `getopt()` 定义脚本期望的短选项和长选项。第三个可选参数通过引用传递，并将被解析器用于解析选项的索引。

![2](img/#co_php_command_line_CO1-2)

有值的选项可以轻松从结果关联数组中提取。

![3](img/#co_php_command_line_CO1-3)

`getopt()` 的结果索引可用于通过从 `$argv` 数组中提取未解析值来快速提取任何附加数据。

![4](img/#co_php_command_line_CO1-4)

无值的选项仍会在关联数组中设置一个键，但其值将是布尔值 `false`。检查键是否存在，但不要依赖其值，因为结果的直观性质可能会产生误导。

假设您将脚本命名为 Example 18-1 定义的 *getopt.php*，您可以期望看到如下结果：

```php
% php getopt.php -c --name Reader This is fun
HELLO, READER!
THIS IS FUN
%
```

## 参见

关于[`$argc`](https://oreil.ly/BXdSI)，[`$argv`](https://oreil.ly/ODRwK)，以及[`getopt()` 函数](https://oreil.ly/ZfqTP)的文档。

# 18.2 读取交互式用户输入

## 问题

您希望提示用户输入并将其响应读入变量。

## 解决方案

使用 `STDIN` 文件句柄常量从标准输入流中读取数据。例如：

```php
echo 'Enter your name: ';

$name = trim(fgets(STDIN, 1024));

echo "Welcome, {$name}!" . PHP_EOL;
```

## 讨论

标准输入流使您可以轻松地读取请求中提供的任何数据。在程序中使用 `fgets()` 直接从流中读取数据将暂停程序的执行，直到最终用户向您提供该输入为止。

解决方案示例利用了简写常量`STDIN`来引用输入流。您也可以像在 Example 18-2 中演示的那样，使用流的完全限定名称（以及显式的`fopen()`）。

##### Example 18-2\. 从`stdin`读取用户输入

```php
echo 'Enter your name: ';

$name = trim(fgets(fopen('php://stdin', 'r'), 1024));

echo "Welcome, {$name}!" . PHP_EOL;
```

###### 注意

特殊的`STDIN`和`STDOUT`简称仅在应用程序中可访问。如果像 Recipe 18.5 中使用交互式终端 REPL，这些常量将不会被定义或者无法访问。

另一种方法是在 PHP 中使用[GNU Readline 扩展](https://oreil.ly/eRhJw)，这在您的安装中可能有也可能没有。这个扩展包装了许多手动提示、检索和修剪用户输入的工作。整个解决方案示例可以重写为 Example 18-3 中所示。

##### Example 18-3\. 从 GNU Readline 扩展读取输入

```php
$name = readline('Enter your name: ');

echo "Welcome, {$name}!" . PHP_EOL;
```

Readline 扩展提供的其他函数，如[`readline_add_​history()`](https://oreil.ly/J5do3)，允许高效地操作系统命令行历史。如果该扩展可用，这是处理用户输入的强大方式。

###### 注意

PHP 的某些发行版（如 Windows 版）默认启用了 Readline 支持。在其他情况下，您可能需要显式编译 PHP 以包含此支持。有关原生 PHP 扩展的更多信息，请查看 Recipe 15.4。

## 参见

进一步讨论标准输入，请参阅 Recipe 11.2。

# 18.3 给控制台输出着色

## 问题

您希望在控制台中以不同颜色显示文本。

## 解决方案

使用正确转义的控制台颜色代码。例如，以以下方式在红色背景上以蓝色文本打印字符串`Happy Independence Day`：

```php
echo "\e0;34;41mHappy Independence Day!\e[0m" . PHP_EOL;
```

## 讨论

类 Unix 终端支持 ANSI 转义序列，这些序列允许程序对诸如光标位置和字体样式之类的细节进行精细控制。特别是，您可以使用这个转义序列为终端后续的所有文本定义颜色：

```php
\e[{foreground};{background}m
```

前景色有两种变体——普通和粗体（由颜色定义中的额外布尔标志确定）。背景色缺乏这种区分。所有颜色都由[Table 18-2 中的这些代码标识。

Table 18-2\. ANSI 颜色代码

| 颜色 | 普通前景色 | 亮前景色 | 背景色 |
| --- | --- | --- | --- |
| 黑色 | `0;30` | `1;30` | `40` |
| 红色 | `0;31` | `1;31` | `41` |
| 绿色 | `0;32` | `1;32` | `42` |
| 黄色 | `0;33` | `1;33` | `43` |
| 蓝色 | `0;34` | `1;34` | `44` |
| 洋红色 | `0;35` | `1;35` | `45` |
| 青色 | `0;36` | `1;36` | `46` |
| 白色 | `0;37`（真正的浅灰色） | `1;37` | `47` |

要将终端颜色重置为正常状态，请在任何颜色定义的位置使用简单的`0`。代码`\e[0m`将重置所有属性。

## 参见

Wikipedia 涵盖了[ANSI 转义码](https://oreil.ly/y02cf)。

# 18.4 使用 Symfony Console 创建命令行应用程序

## 问题

想要创建一个完整的命令行应用程序，而不必手动编写所有的参数解析和处理代码。

## 解决方案

使用 Symfony Console 组件定义你的应用程序及其命令。例如，示例 18-4 定义了一个 Symfony 命令，用于在控制台上向用户打招呼。然后，示例 18-5 使用该命令对象创建一个在终端中向用户打招呼的应用程序。

##### 示例 18-4\. 一个基本的 hello world 命令

```php
namespace App\Command;

use Symfony\Component\Console\Attribute\AsCommand;
use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

#[AsCommand(name: 'app:hello-world')]
class HelloWorldCommand extends Command
{
    protected static $defaultDescription = 'Greets the user.';

    // ...
    protected function configure(): void
    {
        $this
            ->setHelp('This command greets a user...')
            ->addArgument('name', InputArgument::REQUIRED, 'User name');
    }

    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        $output->writeln("Hello, {$input->getArgument('name')}");
        return Command::SUCCESS;
    }
}
```

##### 示例 18-5\. 创建实际的控制台应用程序

```php
#!/usr/bin/env php
<?php
// application.php

require __DIR__.'/vendor/autoload.php';

use Symfony\Component\Console\Application;

$application = new Application();

$application->add(new App\Command\HelloWorldCommand());

$application->run();
```

然后按如下命令运行：

```php
% ./application.php app:hello-world User
```

## 讨论

[Symfony 项目](https://symfony.com)为 PHP 提供一套强大的可重复使用组件集合。它作为一个框架简化了 Web 应用程序的开发，并显著提高了开发速度。它文档详尽、功能强大，最重要的是完全免费和开源。

###### 注意

开源的[Laravel 框架](https://laravel.com)，其数据模块被涵盖在 Recipe 16.9 中，本身是 Symfony 组件的一个元包。它自己的[Artisan 控制台工具](https://oreil.ly/uY4QL)建立在 Symfony Console 组件之上，为 Laravel 项目提供丰富的命令行控制，包括其配置和运行环境。

与任何其他 PHP 扩展一样，Symfony 组件通过 Composer 安装。¹ Console 组件本身可以如下安装：

```php
% composer require symfony/console
```

前置命令`require`将更新你项目的*composer.json*文件，包括 Console 组件，并将其（及其依赖项）安装在你项目的*vendor/*目录中。

###### 注意

如果你的项目还没有使用 Composer，安装任何包将自动为你创建一个新的*composer.json*文件。你应该花些时间更新它，以便自动加载项目所需的所有类或文件，以确保一切无缝协作。有关 Composer、扩展和自动加载的更多信息，请参阅第十五章。

安装完库之后，你可以立即开始利用它。各种命令的业务逻辑可以存在于应用程序的其他位置（例如，在 RESTful API 后面），但也可以通过命令行界面导入并公开。

默认情况下，每个继承自`Command`的类都可以处理用户提供的参数，并将内容显示回终端。选项和参数通过该类的`addArgument()`和`addOption()`方法创建，并可以直接在其`configure()`方法中进行操作。

输出非常灵活。你可以使用`ConsoleOutputInterface`类中列出的任何方法直接将内容打印到屏幕上，详见表 18-3。

表格 18-3\. Symfony 控制台输出方法

| 方法 | 描述 |
| --- | --- |
| `writeln()` | 将单行文本写入控制台。相当于在某些文本后面使用`echo`，然后显式使用`PHP_EOL`换行符。 |
| `write()` | 在控制台中写入文本，不添加换行符。 |
| `section()` | 创建一个新的输出区域，可以像独立的输出缓冲区一样进行原子控制。 |
| `overwrite()` | 仅对部分有效——使用给定内容覆盖部分中的内容。 |
| `clear()` | 仅对部分有效——清除部分的所有内容。 |

除了在表格 18-3 中介绍的文本方法之外，Symfony Console 还可以在控制台中创建动态表格。每个`Table`实例都绑定到一个输出接口，可以具有所需的任意行数、列数和分隔符。示例 18-6 演示了如何在从数组中提取内容填充之前构建一个简单的表格，并将其渲染到控制台。

##### 示例 18-6\. 使用 Symfony 在控制台中渲染表格

```php
// ...

#[AsCommand(name: 'app:book')]
class BookCommand extends Command
{
    public function execute(InputInterface $input, OutputInterface $output): int
    {
        $table = new Table($output);
        $table
            ->setHeaders(['ISBN', 'Title', 'Author'])
            ->setRows([
                [
                    '978-1-940111-61-2',
                    'Security Principles for PHP Applications',
                    'Eric Mann'
                ],
                ['978-1-098-12132-7', 'PHP Cookbook', 'Eric Mann'],
            ])
        ;
        $table->render();

        return Command::SUCCESS;
    }
}
```

Symfony Console 自动解析传递到`Table`对象的内容，并为您渲染包含网格线的表格。前面的命令在控制台中生成以下输出：

```php
+-------------------+------------------------------------------+-----------+
| ISBN              | Title                                    | Author    |
+-------------------+------------------------------------------+-----------+
| 978-1-940111-61-2 | Security Principles for PHP Applications | Eric Mann |
| 978-1-098-12132-7 | PHP Cookbook                             | Eric Mann |
+-------------------+------------------------------------------+-----------+
```

组件内进一步模块帮助控制和渲染动态的[进度条](https://oreil.ly/TszPm)和交互式的[用户提示和问题](https://oreil.ly/8i5Hx)。

控制台组件甚至可以直接帮助[着色终端输出](https://oreil.ly/arrtr)。与 18.3 节讨论的复杂 ANSI 转义序列不同，Console 允许您直接使用命名标签和样式来控制内容。

###### 警告

在撰写本文时，Console 组件默认在 Windows 系统上禁用输出着色。有各种免费的终端应用程序（如[Cmder](https://oreil.ly/gs5e6)）可供 Windows 作为标准终端的替代品，支持输出着色。

终端是您的用户非常强大的界面。Symfony Console 使得在应用程序中定位这个界面变得容易，无需手动解析参数或手工制作丰富的输出。

## 参见

Symfony Console 组件的[完整文档](https://oreil.ly/vm8Qx)。

# 18.5 使用 PHP 的本地读取-评估-打印循环

## 问题

您希望测试一些 PHP 逻辑，而不需要创建完整的应用程序来托管它。

## 解决方案

利用 PHP 的交互式 shell 如下：

```php
% php -a
```

## 讨论

PHP 交互式 shell 提供了一个读取-评估-打印循环（REPL），可以有效地测试 PHP 中的单个语句，并在可能的情况下直接打印到终端。在 shell 中，您可以定义函数和类，甚至直接执行命令式代码，而无需创建磁盘上的脚本文件。

这个 shell 是在完整应用程序的上下文之外测试特定行代码或逻辑的有效方法。

交互式 shell 还允许在 shell 会话运行时对所有 PHP 函数或变量以及您定义的任何函数或变量进行全面的 Tab 补全。只需键入名称的前几个字符，按 Tab 键，shell 将自动为您完成名称。如果存在多个可能的完成项，请按两次 Tab 键查看所有可能性的列表。

您可以在 *php.ini* 配置文件中控制 shell 的两个特定设置：`cli.pager` 允许外部程序处理输出而不是直接显示到控制台，并且 `cli.prompt` 允许您控制默认的 `php >` 提示符。

例如，您可以通过在 shell 会话中将任意字符串传递给 `#cli.prompt` 来替换提示符本身：

```php
% php -a ![1](img/1.png)

php > #cli.prompt=repl ~> ![2](img/2.png)
repl ~> ![3](img/3.png)
```

![1](img/#co_php_command_line_CO2-1)

初始调用 PHP 启动交互式 shell。

![2](img/#co_php_command_line_CO2-2)

直接设置 `cli.prompt` 配置将覆盖默认配置，直到会话关闭。

![3](img/#co_php_command_line_CO2-3)

一旦您覆盖了默认提示符，您将看到新版本直到退出。

###### 警告

使用反引号可以在提示符本身内执行任意 PHP 代码。[PHP 文档中的一些示例](https://oreil.ly/o6NU6)使用这种方法在提示符前添加当前时间。然而，在不同系统之间，这可能不会始终正常工作，并且可能在执行 PHP 代码时引入不必要的不稳定性。

您可以使用在 Table 18-2 中定义的 ANSI 转义序列为输出添加颜色。在许多情况下，这提供了更愉快的界面，并且允许您在需要时提供附加信息。CLI 提示符本身引入了四个额外的转义序列，如 Table 18-4 中定义的那样。

表 18-4\. CLI 提示符转义序列

| Sequence | Description |
| --- | --- |
| `\e` | 使用在 Recipe 18.3 中引入的 ANSI 代码为提示符添加颜色。 |
| `\v` | 打印 PHP 的版本。 |
| `\b` | 指示包含解释器的逻辑块。默认情况下，这将是 `php`，但可能是 `/*` 表示多行注释。 |
| `\>` | 表示默认情况下为 `>` 的提示字符。当解释器位于另一个未终止的块或字符串内时，此字符将更改以指示 shell 的位置。可能的字符包括 `' " { ( >`。 |

通过同时使用 ANSI 转义序列定义颜色和为提示符本身定义的特殊序列，您可以定义一个提示符，以显示 PHP 的版本和解释器的位置，并使用友好的前景色，如下所示：

```php
php > #cli.prompt=\e032m\v \e031m\b \e[34m\> \e[0m
```

上述设置导致在 [Figure 18-1 中显示。

![使用着色更新的 PHP 控制台

###### 图 18-1\. 使用着色更新的 PHP 控制台

###### 警告

并非所有控制台都支持通过 ANSI 控制序列进行着色。如果这是您打算使用的模式，请务必在要求其他人使用系统之前彻底测试您的序列。虽然正确渲染的控制台既吸引人又易于使用，但未渲染的转义序列可能使控制台几乎无法使用。

## 参见

[PHP 交互命令行](https://oreil.ly/HrCV-)的文档。

¹ 要了解更多关于 Composer 的信息，请参阅 Recipe 15.3。
