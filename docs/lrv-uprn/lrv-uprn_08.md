# 第八章：Artisan 和 Tinker

从安装开始，现代 PHP 框架期望在命令行上进行许多交互。Laravel 提供了三种主要的命令行交互工具：Artisan，一组内置命令行操作，具有添加更多功能的能力；Tinker，用于应用程序的 REPL 或交互式 shell；以及安装程序，在第二章中已经介绍过。

# 一个关于 Artisan 的介绍

如果您已经逐章阅读本书，已经学会如何使用 Artisan 命令。它们看起来像这样：

```php
php artisan make:controller PostController
```

如果您查看应用程序的根文件夹，您会看到*artisan*实际上只是一个 PHP 文件。这就是为什么您要以`php artisan`开头进行调用；您将该文件传递给 PHP 进行解析。之后的所有内容只是作为参数传递给 Artisan。

# Symfony Console 语法

实际上，Artisan 是建立在[Symfony Console 组件](https://oreil.ly/7Cb3Y)之上的一层；因此，如果您熟悉编写 Symfony Console 命令，那么您应该会感觉如同在家一样。

由于应用程序的 Artisan 命令列表可能会被包或特定应用程序代码更改，因此值得检查您遇到的每个新应用程序以查看可用的命令。

要获取所有可用 Artisan 命令的列表，可以从项目根目录运行`php artisan list`（尽管如果只运行`php artisan`而不带参数，它将执行相同的操作）。

# 基本的 Artisan 命令

这里没有足够的空间来涵盖所有的 Artisan 命令，但我们将涵盖其中的许多命令。让我们从基本命令开始：

`clear-compiled`

移除 Laravel 的编译类文件，这类似于内部 Laravel 缓存；当事情出现问题并且您不知道原因时，首先尝试运行此命令。

`down`，`up`

将您的应用程序置于“维护模式”中，以便您可以修复错误，运行迁移或其他操作，并将应用程序从维护模式恢复。

`dump-server`

启动转储服务器（参见“Laravel Dump Server”）以收集和输出转储的变量。

`env`

显示 Laravel 当前运行的环境；这相当于在应用中回显`app()->environment()`。

`help`

为命令提供帮助；例如，`php artisan help *commandName*`。

`migrate`

运行所有数据库迁移。

`optimize`

清除并刷新配置和路由文件。

`serve`

在`localhost:8000`上启动 PHP 服务器。（您可以使用`--host`和`--port`自定义主机和/或端口。）

`tinker`

启动 Tinker REPL，我们将在本章后面介绍它。

`stub:publish`

发布所有可用于自定义的存根。

`docs`

为您提供快速访问 Laravel 文档的途径；传递一个参数，您将被提示打开这些文档的 URL，或者不传递参数，您将能够浏览文档主题列表以选择。

`about`

显示项目环境、通用配置、包等的概述。

# Artisan 命令列表随时间变化

Laravel 生命周期内 Artisan 命令及其名称略有变化。在撰写本书时，此列表尽可能是最新的。但是，了解可用内容的最佳方法是从您的应用程序中运行 `php artisan`。

## 选项

在我们介绍其余命令之前，让我们看一下您在运行 Artisan 命令时可以随时传递的一些显著选项：

`-q`

抑制所有输出

`-v`、`-vv` 和 `-vvv`

指定输出详细程度（正常、详细和调试）

`--no-interaction`

抑制交互式问题，因此命令不会中断正在运行它的自动化过程

`--env`

允许您定义 Artisan 命令应在哪个环境中运行（`local`、`production` 等）。

`--version`

显示您的应用程序正在运行的 Laravel 版本

您可能已经从这些选项中猜到，Artisan 命令的使用方式类似于基本的 shell 命令：您可以手动运行它们，但它们也可以作为某些自动化过程的一部分运行。

例如，许多自动化部署流程可能会从某些 Artisan 命令中受益。每次部署应用程序时，您可能希望运行 `php artisan config:cache`。像 `-q` 和 `--no-interaction` 这样的标志确保您的部署脚本可以顺利运行，而无需人类干预。

## 分组命令

提供了默认情况下可用的其余命令，这些命令根据上下文进行分组。我们不会在此处详细介绍所有命令，但我们将广泛涵盖每个上下文：

`auth`

这里仅有 `auth:clear-resets`，该命令从数据库中清除所有过期的密码重置令牌。

`cache`

`cache:clear` 清除缓存，`cache:forget` 从缓存中删除单个项，并且 `cache:table` 如果您计划使用 `database` 缓存驱动程序，则创建数据库迁移。

`config`

`config:cache` 缓存您的配置设置以加快查找速度；要清除缓存，请使用 `config:clear`。

`db`

`db:seed` 如果已配置数据库填充器，则向数据库中填充数据。

`event`

`event:list` 列出应用程序中的所有事件和监听器，`event:cache` 缓存该列表，`event:clear` 清除该缓存，`event:generate` 根据 `EventServiceProvider` 中的定义构建缺失的事件和事件监听器文件。您将在 第十六章 中了解更多关于事件的信息。

`key`

`key:generate` 在您的 *.env* 文件中创建一个随机的应用程序加密密钥。

# 重新生成 artisan key:generate 意味着丢失某些加密数据

如果在你的应用程序上多次运行`php artisan key:generate`，每个当前已登录的用户都将被注销。此外，任何您手动加密的数据将无法解密。要了解更多信息，请查看由同事 Tightenite Jake Bathman 撰写的文章[“APP_KEY and You”](https://oreil.ly/T_l1h)。

`make`

每个`make:`操作都从一个存根创建一个单独的项目，并具有相应变化的参数。要了解有关任何单个命令参数的更多信息，请使用`help`来阅读其文档。

例如，您可以运行`php artisan help make:migration`，了解到可以传递`--create=*tableNameHere*`来创建一个已经包含创建表语法的迁移文件，如下所示：`php artisan make:migration create_posts_table --create=posts`。

`migrate`

之前提到的用于运行所有迁移的`migrate`命令，请参阅“Running Migrations”以获取有关所有与迁移相关的命令的详细信息。

`notifications`

`notifications:table` 生成一个创建数据库通知表的迁移。

`package`

Laravel 通过其“autodiscover”功能生成的清单。这在您首次安装第三方包时为您注册服务提供程序。`package:discover` 重新构建 Laravel 的“已发现”清单，其中包含来自外部包的服务提供程序。

`queue`

我们将在第十六章介绍 Laravel 的队列，但基本思想是您可以将作业推送到远程队列，由工作进程依次执行。此命令组提供了与队列交互所需的所有工具，如`queue:listen`用于开始监听队列，`queue:table`用于创建支持数据库的队列的迁移，`queue:flush`用于刷新所有失败的队列作业。还有更多命令，您将在第十六章中了解到。

`route`

如果运行`route:list`，您将看到应用程序中定义的每个路由的定义，包括每个路由的动词、路径、名称、控制器/闭包动作和中间件。您可以使用`route:cache`缓存路由定义以加快查找速度，并使用`route:clear`清除缓存。

`schedule`

我们将在第十六章介绍 Laravel 的类似于 cron 的调度器，但为了使其工作，您需要设置系统 cron 每分钟运行`schedule:run`一次：

```php
* * * * * php /home/myapp.com/artisan schedule:run >> /dev/null 2>&1
```

正如您所看到的，此 Artisan 命令旨在定期运行，以支持 Laravel 核心服务。

`session`

`session:table` 为使用数据库支持会话的应用程序创建迁移。

`storage`

`storage:link` 创建一个符号链接，将*public/storage*链接到*storage/app/public*。这是 Laravel 应用程序中的常见约定，可以轻松地将用户上传的文件（或其他通常保存在*storage/app*中的文件）放在可以通过公共 URL 访问的地方。

`vendor`

一些特定于 Laravel 的包需要“发布”它们的一些资源，这样它们可以从你的 *public* 目录提供或者你可以修改它们。无论哪种方式，这些包都会向 Laravel 注册这些“可发布的资源”，当你运行 `vendor:publish` 时，它们就会发布到指定的位置。

`view`

Laravel 的视图渲染引擎会自动缓存你的视图。通常它处理自己的缓存失效工作做得不错，但如果你注意到有时候卡住了，可以运行 `view:clear` 来清除缓存。

# 编写自定义 Artisan 命令

现在我们已经讨论了 Laravel 开箱即用的 Artisan 命令，让我们来谈谈如何编写你自己的命令。

首先，你应该知道：有一个专门的 Artisan 命令来处理这个！运行 `php artisan make:command *YourCommandName*` 会在 *app/Console/Commands/{YourCommandName}.php* 中生成一个新的 Artisan 命令。

你的第一个参数应该是命令的类名，你还可以选择性地传递一个 `--command` 参数来定义终端命令将是什么（例如 `appname:action`）。所以，让我们来做吧：

```php
php artisan make:command WelcomeNewUsers --command=email:newusers
```

查看 示例 8-1 以查看你将得到什么。

##### 示例 8-1\. Artisan 命令的默认骨架

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class WelcomeNewUsers extends Command
{
    /**
 * The name and signature of the console command
 *
 * @var string
 */
    protected $signature = 'email:newusers';

    /**
 * The console command description
 *
 * @var string
 */
    protected $description = 'Command description';

    /**
 * Execute the console command.
 */
    public function handle(): void
    {
        //
    }
}
```

正如你所看到的，定义命令签名、命令列表中显示的帮助文本以及命令执行时的行为 (`handle()`) 非常简单。

## 一个示例命令

在本章中我们还没有涵盖邮件或 Eloquent（查看 第十五章 获取邮件和 第五章 获取 Eloquent），但 示例 8-2 中的示例 `handle()` 方法应该读起来很清晰。

##### 示例 8-2\. 一个样例 Artisan 命令 `handle()` 方法

```php
// ...

class WelcomeNewUsers extends Command
{
    public function handle(): void
    {
        User::signedUpThisWeek()->each(function ($user) {
            Mail::to($user)->send(new WelcomeEmail);
        });
    }
```

现在每当你运行 `php artisan email:newusers` 命令时，该命令将获取本周注册的每个用户，并发送给他们欢迎邮件。

如果你更喜欢注入你的邮件和用户依赖项而不是使用门面模式，你可以在命令构造函数中使用类型提示，当命令实例化时，Laravel 的容器会自动注入它们给你。

查看 示例 8-3 以查看使用依赖注入和将其行为提取到服务类中的 示例 8-2 是什么样子的。

##### 示例 8-3\. 同一个命令，重构后

```php
...
class WelcomeNewUsers extends Command
{
    public function __construct(UserMailer $userMailer)
    {
        parent::__construct();

        $this->userMailer = $userMailer
    }

    public function handle(): void
    {
        $this->userMailer->welcomeNewUsers();
    }
```

## 参数和选项

新命令的 `$signature` 属性看起来可能只包含命令名称。但这个属性也是你定义命令的参数和选项的地方。你可以使用特定而简单的语法来向你的 Artisan 命令添加参数和选项。

在我们深入研究语法之前，先看一个例子来获得一些上下文：

```php
protected $signature = 'password:reset {userId} {--sendEmail}';
```

### 参数 — 必填、可选、或者带有默认值

要定义一个必填参数，用大括号括起来：

```php
password:reset {userId}
```

要使参数变成可选的，添加一个问号：

```php
password:reset {userId?}
```

要使它可选并提供默认值，使用：

```php
password:reset {userId=1}
```

### 选项 — 必填值、默认值和快捷方式

选项类似于参数，但它们以 `--` 作为前缀，并且可以不带值使用。要添加基本选项，请用大括号括起来：

```php
password:reset {userId} {--sendEmail}
```

如果您的选项需要一个值，请在其签名中添加一个 `=`：

```php
password:reset {userId} {--password=}
```

如果要传递默认值，请在 `=` 之后添加它：

```php
password:reset {userId} {--queue=default}
```

### 数组参数和数组选项

无论是对于参数还是选项，如果要接受数组作为输入，请使用 `*` 字符：

```php
password:reset {userIds*}

password:reset {--ids=*}
```

使用数组参数和参数看起来有点像 8-4 示例。

##### 示例 8-4\. 在 Artisan 命令中使用数组语法

```php
// Argument
php artisan password:reset 1 2 3

// Option
php artisan password:reset --ids=1 --ids=2 --ids=3
```

# 数组参数必须是最后一个参数。

由于数组参数捕获其定义后的每个参数，并将它们作为数组项添加，因此数组参数必须是 Artisan 命令签名中的最后一个参数。

### 输入描述

还记得内置的 Artisan 命令如何在使用 `artisan help` 时能为我们提供有关其参数的更多信息吗？我们可以为我们的自定义命令提供相同的信息。只需在大括号中加上冒号和描述文本，就像 8-5 示例中一样。

##### 示例 8-5\. 为 Artisan 参数和选项定义描述文本

```php
protected $signature = 'password:reset
 {userId : The ID of the user}
 {--sendEmail : Whether to send user an email}';
```

## 使用输入

现在我们已经提示了这个输入，那么我们如何在命令的 `handle()` 方法中使用它呢？我们有两套方法来检索参数和选项的值。

### argument() 和 arguments()

`$this->arguments()` 返回所有参数的数组（第一个数组项将是命令名称）。没有参数调用的 `$this->argument()` 返回相同的响应；我更喜欢复数形式的方法，仅仅是为了更好的可读性。

要仅获取单个参数的值，请将参数名称作为参数传递给 `$this->argument()`，如示例 8-6 所示。

##### 示例 8-6\. 在 Artisan 命令中使用 `$this->arguments()`

```php
// With definition "password:reset {userId}"
php artisan password:reset 5

// $this->arguments() returns this array
[
    "command": "password:reset",
    "userId": "5",
]

// $this->argument('userId') returns this string
"5"
```

### option() 和 options()

`$this->options()` 返回一个包含所有选项的数组，其中一些默认为 `false` 或 `null`。没有参数调用的 `$this->option()` 返回相同的响应；我更喜欢复数形式的方法，仅仅是为了更好的可读性。

要仅获取单个选项的值，请将参数名称作为参数传递给 `$this->option()`，如示例 8-7 所示。

##### 示例 8-7\. 在 Artisan 命令中使用 `$this->options()`

```php
// With definition "password:reset {--userId=}"
php artisan password:reset --userId=5

// $this->options() returns this array
[
    "userId" => "5",
    "help" => false,
    "quiet" => false,
    "verbose" => false,
    "version" => false,
    "ansi" => false,
    "no-ansi" => false,
    "no-interaction" => false,
    "env" => null,
]

// $this->option('userId') returns this string
"5"
```

8-8 示例展示了在其 `handle()` 方法中使用 `argument()` 和 `option()` 的 Artisan 命令。

##### 示例 8-8\. 从 Artisan 命令获取输入

```php
public function handle(): void
{
    // All arguments, including the command name
    $arguments = $this->arguments();

    // Just the 'userId' argument
    $userid = $this->argument('userId');

    // All options, including some defaults like 'no-interaction' and 'env'
    $options = $this->options();

    // Just the 'sendEmail' option
    $sendEmail = $this->option('sendEmail');
}
```

## 提示

在执行命令期间，有几种方法可以从 `handle()` 代码中获取用户输入：

`ask()`

提示用户输入自由格式文本：

```php
$email = $this->ask('What is your email address?');
```

`secret()`

提示用户输入自由格式文本，但用星号隐藏输入：

```php
$password = $this->secret('What is the DB password?');
```

`confirm()`

提示用户回答是或否，并返回布尔值：

```php
if ($this->confirm('Do you want to truncate the tables?')) {
    //
}
```

除了 `y` 或 `Y` 之外的所有答案都将被视为“否”。

`anticipate()`

提示用户输入自由格式文本，并提供自动完成建议。仍然允许用户输入他们想要的任何内容：

```php
$album = $this->anticipate('What is the best album ever?', [
    "The Joshua Tree", "Pet Sounds", "What's Going On"
]);
```

`choice()`

提示用户从提供的选项中选择一个。如果用户没有选择，则使用最后一个参数作为默认值：

```php
$winner = $this->choice(
    'Who is the best football team?',
    ['Gators', 'Wolverines'],
    0
);
```

请注意，最后一个参数，默认应为数组键。由于我们传递了非关联数组，因此 `Gators` 的键是 `0`。如果您愿意，也可以对数组进行键分配：

```php
$winner = $this->choice(
    'Who is the best football team?',
    ['gators' => 'Gators', 'wolverines' => 'Wolverines'],
    'gators'
);
```

## 输出

在执行命令期间，您可能希望向用户写入消息。实现这一最基本的方法是使用 `$this->info()` 输出基本的绿色文本：

```php
$this->info('Your command has run successfully.');
```

您还可以使用 `comment()`（橙色）、`question()`（高亮青色）、`error()`（高亮红色）、`line()`（未着色）和 `newLine()`（未着色）方法在命令行输出。

请注意，确切的颜色可能因机器而异，但它们试图符合本地机器与最终用户之间的标准沟通。

### 表格输出

`table()` 方法使得创建包含数据的 ASCII 表格变得简单。查看 示例 8-9。

##### 示例 8-9\. 使用 Artisan 命令输出表格

```php
$headers = ['Name', 'Email'];

$data = [
    ['Dhriti', 'dhriti@amrit.com'],
    ['Moses', 'moses@gutierez.com'],
];

// Or, you could get similar data from the database:
$data = App\User::all(['name', 'email'])->toArray();

$this->table($headers, $data);
```

注意 示例 8-9 包含两组数据：标题和数据本身。每行都包含两个“单元格”；每行的第一个单元格是名称，第二个单元格是电子邮件。这样，来自 Eloquent 调用的数据（限制为仅提取名称和电子邮件）与标题相匹配。

查看 示例 8-10 以查看表格输出的样子。

##### 示例 8-10\. Artisan 表格的示例输出

```php
+---------+--------------------+
| Name    | Email              |
+---------+--------------------+
| Dhriti  | dhriti@amrit.com   |
| Moses   | moses@gutierez.com |
+---------+--------------------+
```

### 进度条

如果您曾经运行过 `npm install`，您之前见过命令行进度条。让我们在 示例 8-11 中构建一个。

##### 示例 8-11\. Artisan 进度条示例

```php
$totalUnits = 350;
$this->output->progressStart($totalUnits);

for ($i = 0; $i < $totalUnits; $i++) {
    sleep(1);

    $this->output->progressAdvance();
}

$this->output->progressFinish();
```

我们在这里做了什么？首先，我们告诉系统需要处理多少“单位”。也许一个单位是一个用户，您有 350 个用户。进度条然后将屏幕上可用的整个宽度除以 350，并且每次运行 `progressAdvance()` 时递增 1/350。完成后，请运行 `progressFinish()` 以通知它已完成显示进度条。

## 编写基于闭包的命令

如果您更喜欢保持命令定义过程简单，可以将命令编写为闭包而不是类，方法是在 *routes/console.php* 中定义和注册它们。本章中讨论的所有内容都将同样适用，但您将在该文件中的单个步骤中定义和注册命令，如 示例 8-12 所示。

##### 示例 8-12\. 使用闭包定义 Artisan 命令

```php
// routes/console.php
Artisan::command(
    'password:reset {userId} {--sendEmail}',
    function ($userId, $sendEmail) {
        $userId = $this->argument('userId');
        // Do something...
    }
);
```

# 在普通代码中调用 Artisan 命令

虽然 Artisan 命令设计用于从命令行运行，但您也可以从其他代码中调用它们。

最简单的方法是使用 `Artisan` 门面。您可以使用 `Artisan::call()` 调用命令（这将返回命令的退出代码），或者使用 `Artisan::queue()` 将命令排队。

两者都接受两个参数：第一个是终端命令（`password:reset`）；第二个是要传递给它的参数数组。查看示例 8-13 以了解如何使用参数和选项。

##### 示例 8-13\. 从其他代码调用 Artisan 命令

```php
Route::get('test-artisan', function () {
    $exitCode = Artisan::call('password:reset', [
        'userId' => 15,
        '--sendEmail' => true,
    ]);
});
```

如您所见，参数通过键名传递给参数名，没有值的选项可以传递`true`或`false`。

# 使用字符串语法调用 Artisan 命令

您还可以通过将与命令行中相同的字符串传递到`Artisan::call()`中，更自然地从您的代码中调用 Artisan 命令：

```php
Artisan::call('password:reset 15 --sendEmail')
```

您还可以从其他命令中使用`$this->call()`调用 Artisan 命令（与`Artisan::call()`相同），或者使用`$this->callSilent()`，它们的作用相同，但抑制了所有输出。参见示例 8-14 作为示例。

##### 示例 8-14\. 从其他 Artisan 命令调用 Artisan 命令

```php
public function handle(): void
{
    $this->callSilent('password:reset', [
        'userId' => 15,
    ]);
}
```

最后，您可以注入`Illuminate\Contracts\Console\Kernel`合同的一个实例，并使用它的`call()`方法。

# Tinker

Tinker 是一个 REPL（交互式环境），或者读取-求值-打印循环。REPL 会给您一个提示符，类似于命令行提示符，模仿应用程序的“等待”状态。您在 REPL 中键入命令，按回车键，然后期待您键入的内容进行评估并打印响应。

示例 8-15 提供了一个快速示例，让您了解它的工作方式及其可能的用处。我们使用`php artisan tinker`启动 REPL，然后看到一个空白提示符（`>>>`）；每个命令的响应都打印在以`=>`为前缀的行上。

##### 示例 8-15\. 使用 Tinker

```php
$ php artisan tinker

>>> $user = new App\User;
=> App\User: {}
>>> $user->email = 'matt@mattstauffer.com';
=> "matt@mattstauffer.com"
>>> $user->password = bcrypt('superSecret');
=> "$2y$10$TWPGBC7e8d1bvJ1q5kv.VDUGfYDnE9gANl4mleuB3htIY2dxcQfQ5"
>>> $user->save();
=> true

```

如您所见，我们创建了一个新用户，设置了一些数据（使用`bcrypt()`对密码进行了哈希处理以确保安全），并将其保存到数据库中。这是真实的情况。如果这是一个生产应用程序，我们会在系统中创建一个全新的用户。

这使得 Tinker 成为一个用于简单数据库交互、尝试新想法以及在应用程序源文件中找不到放置位置时运行代码片段的绝佳工具。

Tinker 由[Psy Shell](http://psysh.org)提供支持，因此请查看它，看看您还可以使用 Tinker 做什么。

# Laravel 转储服务器

在开发过程中，调试数据状态的一种常见方法是使用 Laravel 的`dump()`助手函数，它对任何您传递给它的内容运行装饰过的`var_dump()`。这很好用，但它经常会遇到视图问题。

您可以启用 Laravel 转储服务器，它会捕获那些`dump()`语句，并在控制台中显示它们，而不是将它们渲染到页面中。

要在本地控制台中运行转储服务器，请导航至项目的根目录并运行`php artisan dump-server`：

```php
$ php artisan dump-server

Laravel Var Dump Server
=======================

 [OK] Server listening on tcp://127.0.0.1:9912

 // Quit the server with CONTROL-C.
```

现在，请尝试在您的代码中某个地方使用`dump()`助手函数。要测试它，请在您的*routes/web.php*文件中尝试以下代码：

```php
Route::get('/', function () {
    dump('Dumped Value');

    return 'Hello World';
});
```

没有 dump 服务器，你会同时看到 dump 和你的“Hello World”。但是有 dump 服务器运行时，你只会在浏览器中看到“Hello World”。在你的控制台中，你会看到 dump 服务器捕捉到了 `dump()`，你可以在那里检查它：

```php
GET http://myapp.test/
--------------------

 ------------ ---------------------------------
  date         Tue, 18 Sep 2018 22:43:10 +0000
  controller   "Closure"
  source       web.php on line 20
  file         routes/web.php
 ------------ ---------------------------------

"Dumped Value"
```

# 自定义生成器存根

任何生成文件的 Artisan 命令（例如 `make:model` 和 `make:controller`）都使用“存根”文件，命令会复制并修改这些文件以创建新生成的文件。你可以在你的应用程序中自定义这些存根。

要在你的应用程序中自定义存根，请运行 `php artisan stub:publish`，它将把存根文件导出到一个 `stub/` 目录中，你可以在那里自定义它们。

# 测试

由于你知道如何从代码中调用 Artisan 命令，因此在测试中执行这些操作并确保你期望的行为已经正确执行很容易，就像 示例 8-16 中展示的那样。在我们的测试中，我们使用 `$this->artisan()` 而不是 `Artisan::call()`，因为它具有相同的语法但添加了一些与测试相关的断言。

##### 示例 8-16\. 在测试中调用 Artisan 命令

```php
public function test_empty_log_command_empties_logs_table()
{
    DB::table('logs')->insert(['message' => 'Did something']);
    $this->assertCount(1, DB::table('logs')->get());

    $this->artisan('logs:empty'); // Same as Artisan::call('logs:empty');
    $this->assertCount(0, DB::table('logs')->get());
}

```

你可以链式调用一些新的断言到你的 `$this->artisan()` 调用中，这使得测试 Artisan 命令变得更加容易——不仅仅是它们对你的应用程序的影响，还有它们的实际操作。看看 示例 8-17 来看看这种语法的一个示例。

##### 示例 8-17\. 对 Artisan 命令的输入和输出进行断言

```php
public function testItCreatesANewUser()
{
    $this->artisan('myapp:create-user')
        ->expectsQuestion("What's the name of the new user?", "Wilbur Powery")
        ->expectsQuestion("What's the email of the new user?", "wilbur@thisbook.co")
        ->expectsQuestion("What's the password of the new user?", "secret")
        ->expectsOutput("User Wilbur Powery created!");

    $this->assertDatabaseHas('users', [
        'email' => 'wilbur@thisbook.co'
    ]);
}
```

# TL;DR

Artisan 命令是 Laravel 的命令行工具。Laravel 自带了一些命令，但也很容易创建你自己的 Artisan 命令并从命令行或你自己的代码中调用它们。

Tinker 是一个 REPL，使得进入你的应用程序环境并与真实代码和真实数据交互变得简单，而 dump 服务器允许你在不停止代码执行的情况下调试你的代码。
